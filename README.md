# FamiliarizaMVC
## setup working environment
  #### App
  ###### class
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
  ###### functions
  ###### ## helper
  ###### models
  ###### routing
  #### Bootstrapp
  ###### caches
  ###### init.php
        This file help us to initialize our environment
        <?php
          
          if(!isset($_SESSION)) session_start();
          require_once __DIR__.'../app/config/_env.php;
        
        ?>
  #### Resources
  ###### assets
  ###### ##bower
  ###### ####vendor
  ###### view
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
  
     
     
     
