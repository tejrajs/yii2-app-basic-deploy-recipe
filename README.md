Yii 2 Basic Project Template with Deployer.php support.
==========================================================

Yii 2 Basic Project Template with Deployer.php support is a skeleton [Yii 2](http://www.yiiframework.com/) application for
rapidly creating projects.

The template contains the basic features including user login/logout and a contact page.
It includes all commonly used configurations that would allow you to focus on adding new
features to your application.

HOW IS THIS DIFFERENT FROM STANDARD BASIC APP?
----------------------------------------------
* This project can be deployed by Deployer
* `config/db.php` and `yii` is generated automatically
* An `.htaccess` is added to the `web` folder and *FollowSymlinks* is turned on.
* Project can be served directly from source on the development machine, but this requires manual setup - namely creating `yii` and `config/db.php`.


DIRECTORY STRUCTURE
-------------------

      assets/             contains assets definition
      commands/           contains console commands (controllers)
      config/             contains application configurations
      controllers/        contains Web controller classes
      deployer/recipe     contains Deployer recipes
      deployer/templates  contains templates configured by Deployer
      deployer/stage      contains configuration file for Deployer
      mail/               contains view files for e-mails
      migrations/         contains migrations
      models/             contains model classes
      tests/              contains various tests for the application
      vendor/             contains dependent 3rd-party packages
      views/              contains view files for the Web application
      web/                contains the entry script and Web resources


REQUIREMENTS
------------

The minimum requirement by this project template that your Web server supports PHP 5.4.0.

Getting Start for deployer https://deployer.org/
=====================================================
Step 1 : Install Deployer
-------------------------
Run the following commands in the console:
```
curl -LO https://deployer.org/deployer.phar
mv deployer.phar /usr/local/bin/dep
chmod +x /usr/local/bin/dep
```
Or you can install it with composer:
```
composer require deployer/deployer
```

Step 2 : Initalize Deployer
---------------------------
Now you can use Deployer via the dep command. Open up a terminal in your project directory and run the following command:
```
dep init
```
This command will create deploy.php file in current directory. It is called recipe and contains configuration and tasks for deployment. By default all recipes extends common recipe.

Step 3 : Remotely
-----------------
You need to set the web root of your site to use current/web:
```
/home/yourname/yoursite.com/current/web
```

How Deployer works
------------------
Deployer makes use of three directories on the server:

releases - contains a number of releases (by default 3).

current - symlink to latest release.

shared - this directory contains files/dirs that are shared between releases.

When the deploy script is run, a new release is created on the server. When the deployment has been completed successfully, the symlink current is updated to point to the latest release.

Recipes
---------
With Deployer, we create our deploy script using modular blocks of code called 'recipes'.

Deployer ships with recipes for common frameworks: deployer/tree/master/recipe
In addition, there is a repository for third-party recipes here: https://github.com/deployphp/recipes

for app advance https://github.com/tejrajs/yii2-app-advance-deploy-recipe
for app basic https://github.com/tejrajs/yii2-app-basic-deploy-recipe

Yii2 app basic recipe
-------------------------

#deploy.php

```
<?php 
require_once __DIR__ . '/deployer/recipe/yii-configure.php';
require_once __DIR__ . '/deployer/recipe/yii2-app-basic.php';
//require_once __DIR__ . '/deployer/recipe/in-place.php';
if (!file_exists (__DIR__ . '/deployer/stage/servers.yml')) {
  die('Please create "' . __DIR__ . '/deployer/stage/servers.yml" before continuing.' . "\n");
}
serverList(__DIR__ . '/deployer/stage/servers.yml');
set('repository', '{{repository}}');
set('default_stage', 'production');
set('keep_releases', 2);
set('writable_use_sudo', false); // Using sudo in writable commands?
set('shared_files', [
    'config/db.php'
]);
task('deploy:configure_composer', function () {
  $stage = env('app.stage');
  if($stage == 'dev') {
    env('composer_options', 'install --verbose --no-progress --no-interaction');
  }
})->desc('Configure composer');
// uncomment the next two lines to run migrations
//after('deploy:symlink', 'deploy:run_migrations');
//after('inplace:configure', 'inplace:run_migrations');
before('deploy:vendors', 'deploy:configure_composer');
before('deploy:vendors', 'deploy:configure_composer');
before('deploy:shared', 'deploy:configure');
```

#Servers.yml

The deploy script loads configuration values from deployer/stage/servers.yml:
```
# list servers
# -------------
prod_1:
    host: hostname
    user: username
    password: password
    stage: production
    repository: https://github.com/user/repository.git
    deploy_path: /var/www
    app:
        stage: 'prod'

        mysql:
            host: db_server
            username: dbuser
            password: dbpassword
            dbname: dbname

dev_1:
    local: true
    host: localhost
    user: username
    password: password
    stage: local
    repository: https://github.com/user/repository.git
    deploy_path: /home/user/www
    app:
        stage: 'dev'

        mysql:
            host: localhost
            username: dbuser
            password: dbpassword
            dbname: dbname

 ```
Deployment
--------------
In order to deploy our project, we need to create servers.yml in deployer/stage.

Copy the contents of stage/servers-sample.yml as a template to get you started.

Once that is done, we can finally deploy our project using the following command:
```
dep deploy production
```
If we want to see more of what the deploy script is doing, we can pass it the v option:
```
dep deploy production -v
```
If you want even more, add -vv - and for everything, add -vvv

If something goes wrong, we can roll back:
```
dep rollback production
```
We can also deploy locally, provided that one of the servers in the server list has the local flag set (see above):

dep deploy local