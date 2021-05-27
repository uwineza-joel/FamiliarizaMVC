# FamiliarizaMVC
## setup working environment
  #### App
  ###### classes
  ###### ## Database.php
          <?php
            namespace App\Classes;
            use Illuminate\Database\Capsule\Manager as Capsule;
            
            class Database
            {
              public function __construct()
              {
                $db = new Capsule;
                $db->addConnection([
                  'driver' => getenv('DB_DRIVER'),
                  'host' => getenv('DB_HOST'),
                  'database' => getenv('DB_NAME'),
                  'username' => getenv('DB_USERNAME'),
                  'password' => getenv('DB_PASSWORD'),
                  'charset' => 'utf8',
                  'collation' => 'utf8_unicode_ci',
                  'prefix' => ''
                ]);
                
                $db->setAsGlobal();
                $db->bootEloquent();
              }
            }
          ?>
  ###### ## Mail.php
          <?php
            namespace App\Classes;
            use PHPMailer;
            
            class Mail
            {
               protected $mail;
               public function __construct()
               {
                  $this->mail = new PHPMailer;
                  $this->setUp();
               }
               public function setUp()
               {
                  $this->mail->isSMTP();
                  $this->mail->mailer = 'smtp';
                  $this->mail->SMTPAuth = true;
                  $this->mail->SMTPSecure = 'tls';
                  
                  $this->mail->Host = getenv('SMTP_HOST');
                  $this->mail->Port = getenv('SMTP_PORT');
                  
                  $environment = getenv('APP_ENV');
                  if($envirnment === 'local') { $this->mail->SMTPDebug = 2; }
                  
                  //auth info
                  $this->mail->Username = getenv('EMAIL_USERNAME');
                  $this->mail->Password = getenv('EMAIL_PASSWORD');
                  
                  $this->mail->isHTML(true);
                  $this->mail->singleTo = true;
                  
                  //sender info
                  $this->mail->From = getenv('ADMIN_EMAIL');
                  $this->mail->FromName = getenv('APP_NAME');
               }
               public function send($data) {
                  $this->mail->addAddress($data['to'], $data['name']);
                  $this->mail->Subject = $data['subject'];
                  $this->mail->Body = make($data['view'], array('data' => $data['body']));
                  return $this->email->send();
               }
            }
          ?>
  ###### ## ErrorHandler.php
          <?php
              namespace App\Classes
              
              class ErrorHandler
              {
                public function handleErrors($error_number, $error_message, $error_file, $error_line) {
                  $error = "[{$error_number}] An error occured in file {$error_file} on line $error_line: $error_message";
                  
                  $environment = getenv('APP_ENV');
                  
                  if($environment) {
                    $whoops = new \Whoops\Run;
                    $whoops->pushHandler(new \Whoops\Handler\PrettyPageHandler);
                    $whoops->register();
                  }
                  else {
                    $data = [
                      'to' => getenv('ADMIN_EMAIL'),
                      'subject' => 'System error',
                      'view' => 'welcome',
                      'name' => 'Joe Doe',
                      'body' => $error
                    ];
                    ErrorHandler::emailAdmin($data)->outputFriendlyError();
                  }
                }
              
                public function outputFriendlyError() {
                  ob_end_clean();
                  view('errors/generic');
                  exit;
                }
              
                public static function emailAdmin($data) {
                  
                  //No need to require Mail class bcs it's in same namespace
                  $mail = new Mail;
                  $mail->send($data);
                  return new static;
                }
              }
          ?>
  ###### config
  ###### ## _env.php
          this is file where we load our .env file
          <?php
          
            define('BASE_PATH', realpath(__DIR__.'/../../../)); /* Now I am out of my App folder, I am in project folder */
            
            require_once __DIR__.'/../../vendor/autoload.php';
            
            $dotEnv = new \Dotenv\Dotenv(BASE_PATH);
            
            $dotEnv->load();
            
          ?>
  ###### controllers
  ###### ## BaseController.php
          <?php
              namespace App\Controller;
              
              class BaseController
              {
                
              }
          ?>
  ###### ## IndexController.php
          <?php
              namespace App\Controller;
              //import email for test
              use App\Classes\Mail;
              
              class IndexController
              {
                public function show()
                {
                  echo "Inside Homepage from controller class";
                  
                  //Testing email server
                  $mail = new Mail();
                  $data = [
                    'to' => 'user@gmail.com',
                    'subject' => 'Welcome to my store',
                    'view' => 'welcome',
                    'name' => 'Joe Doe',
                    'body' => 'Testing email template'
                  ];
                  if($mail->send($data)) {
                    echo "Email sent successfully";
                  }
                  else{
                    echo 'sent failure';
                  }
                }
              }
          ?>
  ###### functions
  ###### ## helper
          <?php
              use Philo\Blade\Blade;
              
              function view($path, array $data = []) {
                $view = __DIR__.'/../../resources/view';
                $cache = __DIR__.'/../../../bootstrapp/cache';
                
                $blade = new Blade($view, $cache);
                
                echo $blade->view()->make($path, $data)->render();
              }
              
              function make($filename, $data) {
                extract($data);
                
                //turn on output buffering
                ob_start();
                
                //include template
                include(__DIR__.'/../../resources/views/emails' . $filename . '.php');
                
                //get content of file
                $content = ob_get_contents();
                
                //erase the output and turn off output buffering
                ob_end_clean();
                
                return $content; 
              }
          ?>
  ###### models
  ###### routing
  ###### ## routes.php
          <?php
              $router = new AltoRouter;
              
              $router->map('GET', '/about', '', 'about_us');
              
              $match = $router->match();
              
              if()
              
          ?>
  ###### ## RouteDispatcher.php
          <?php
              namespace App;
              
              use AltoRouter;
              class RouteDispatcher
              {
                protected $match;
                protected $controller;
                protected $method;
                public function __construct(AltoRouter $router) 
                {
                  $this->match = $router->match();
                  
                  if($this->match) {
                    list($controller, $method) = explode('@', $this->match['target']);
                    $this->controller = $controller;
                    $this->method = $method;
                    
                    if(is_callable(array(new $this->controller, $this->method))) {
                      call_user_func_array(array(new $this->controller, $this->method), 
                        array($this->match['params']));
                    }
                    else {
                      echo "The method {$this->method} is not defined in {$this->controller}";
                    }
                  }
                  else {
                    header($_SERVER['SERVER_PROTOCOL'].'404 not found');
                    view('error/404');
                  }
                }
              }
          ?>
  #### Bootstrapp
  ###### cache
  ###### init.php
        This file help us to initialize our environment
        <?php
          
          if(!isset($_SESSION)) session_start();
          require_once __DIR__.'/../app/config/_env.php;
          
          //instantiate database class
          new \App\Classes\Database();
          
          //set custom error handler
          set_error_handler([new \App\Classes\ErrorHandler(), 'handleErrors']);
          
          require_once __DIR__.'/../app/routing/routes.php;
          
          new \App\RouteDispatcher($router);
        ?>
  #### Resources
  ###### assets
  ###### ## bower
  ###### #### vendor
  ###### ## css
  ###### ## js
  ###### ## sass
  ###### #### app.scss
              //font
              @import url(https://fonts.googleapis.com/css?font=Railway:300,400,600);
              
              @import '../bower/vender/foundation-sites/assets/foundation';
              
              @import '../bower/vender/motion-ui/src/motion-ui';
              @include motion-ui-transitions
              @include motion-ui-animations
  ###### views
  ###### ## emails
  ###### #### welcome.php
            write simple HTML to welcome new user
            <body>
              email body <?php echo $data; ?>
            </body>
  ###### #### errors.php
            <div style=''>
              <img src='' />
                <?php echo "Error: {$data}"; ?>
              <p>
                Regards <br /><br />
                <strong> StoreSaler Team </strong>
              </p>
            </div>
  ###### ## errors
  ###### #### 404.blade.php
             <h1>
                Page not found
             </h1>
  ###### #### generic.blade.php
            <div style=''>
              <h1> A system error occurred, Please try again later.</h1>
            </div>
  #### Public
  ###### images
  ###### index.php
        Let test if we can use our .env variable
        <?php
          
          require_once __DIR__.'../app/config/_env.php
          OR
          require_once __DIR__.'../app/config/init.php 
          /**
          * If you have used init.php first require_once will be moved to init.php and you keep second require_once line
          * init.php will help you also to initialize more like sessions and so on
          */
          
          $app_name = getenv(APP_NAME);
          
          echo $app_name;
          
        ?>
  ###### .htaccess
        #turn on rewrite engine
        RewriteEngine on
        
        #IF REQUESTED FILE IS NOT A REAL FILE
        RewriteCond %{REQUEST_FILENAME} !-f
        
        #redirect all request to index.php
        RwriteRule . index.php [L]
        
  #### vendor
     Here you have to require vlucas composer package to load .env variables 
  #### .env
     This is a file that hold our environment variables those variables are:
     APP_URL = http://127.0.0.1/{{project_directory}}
     APP_ENV = local /*later switch to "production" after dev is done*/
     APP_NAME = "{project_name}"
     
     #Databse
     DB_DRIVER = 'mysql'
     DB_HOST = 127.0.0.1
     DB_NAME = db_name
     DB_USERNAME = root
     DB_PASSWORD = ""
     
     #Mail Credentials
     EMAIL_USERNAME=gmail_address_yours
     EMAIL_PASSWORD=XXXXXXXXXXX
     SMTP_PORT=587
     SMTP_HOST=smtp.gmail.com
     ADMIN_EMAIL=gmail_support_address
     
  #### package.json
     This package.json is of NodeJs, you have to create it manually as follow:
     {
        "author": "Joel Uwineza",
        "description": "{Your project descriptions goes right here}",
        
        "private": true,
        
        "dependence": {
          "gulp": "~3.9",
          "laravel-elixir": "~5.0.0" 
        }
     }
     
     Note: 
        ** gulp ):- gulp is used for task automation that compile all of our css and javascript in just single minified format
        ** laravel-elixir ):- laravel-elixir is very nice wrapper over gulp package, it help us to easily specify different task that we want to do with gulp
        
        after writing and save your package.json, in your cmd go ahead run
        > npm install
        
        >npm install gulp bower --global
        **  bower ):- bower is another tool for front-end dependence management
  #### .bowerrc
     This hidden file look for specified directory and install all my front-end component inside that directory, here is sample:
     {
        "directory": "resources/asset/bower/vendor/"
     }
  #### bower.json
     {
        "name": "{project name}"
     }
  #### gulp.js
     var elixir = require('laravel-elixir');
     //make elixir not to compile all sourcemap
     elixir.config.sourcemaps = false;
     
     var gulp = require('gulp');
     
     elixir(function(mix) {
     
        //compile sass to css
        mix.sass('resources/assets/sass/app.scss', 'resources/assets/css');
        
        //combine css files
        mix.style(
          [
            'css/app.css',
            'bower/vendor/slick-carousel/slick/slick.css'
          ], 
          'public/css/all.css', //output file
          'resources/assets' //sources directory);
          
          var bowerpath = 'bower/vendor'
          mix.script(
            [
              //Jquery
              bowerpath + '/jquery/dist/jquery.min.js',
              //founation javascript
              bowerpath + '/foundation-sites/dist/js/foundation.min.js',
              //other dependences
              bowerpath + '/slick-carousel/slick/slick.min.js'
            ], 'public/js/app.js', 'resources/assets'
          )
     });
  
  ####
    
 
 ## Installing dependences needed for front-end part
  1. first required is called 'zurb foundation', this is used for 
  > bower install foundation-sites --save
  
  2. Next is 'motion UI' of zurb foundation too
  > bower install motion-ui --save

  because bower' s Jquery will cause problems you need to specify it directly in bower.json dependencies As below:
  
  {
    "name": .........,
    "dependence": {
      "foundation-sites": ........
      "jquery": "~2.2",
      "motion-ui": ........
    }
  }
  
  3. adding 'slick slider' for carousel sliders.
  > bower install --save slick-carousel
 
 ## Task automation with gulp and laravel-elixir
  After creating and writing content of gulp.js, the run command:
  > gulp
  
  To watch all the changes
  >gulp watch
  
 ## Altorouting and mod_rewrite
  This will help in create routes of our project links
  > composer require altorouter/altorouter

 ## Setup blade template engine
  the laravel blade is templating engine used for view part of MVC
  > composer require philo/laravel-blade
 
 ## set autload parameter in composer.json
  the sample of all required to be loaded are:
  "autoload": {
    "psr-4": {
      "App\\": "app"
    },
    "files": ["app/functions/helper.php"]
  },
  "config": {
    "optimize-autoloader": true
  }
  
## set Object-relational mappting (ORM)
 The ORM is technology that will help you in operationg with database more easily. it' s installed with composer as follow:
 > composer require illuminate/database

## error Handling
 > composer require filp/whoops --dev
     
     
