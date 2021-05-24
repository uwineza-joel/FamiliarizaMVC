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
     
     
     
     
