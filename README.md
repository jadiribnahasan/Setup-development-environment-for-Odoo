# Setup-development-environment-for-Odoo

In this article, we will see how can we prepare our **Ubuntu** machine to develop **Odoo**.

Lets keep this simple and try to think that we are trying to run a program which needs some other program and libraries to run and these program and libraries needs other libraries to run and so on.

So as we already know that Odoo uses **Python** for the model and controller part of the framework, we first need to install python runtime in our machine.
  * For Odoo Upto Version 10, use Python 2.7.16.
  * For Odoo version 11 and onwards, use Python version 3.5 or above.

`sudo apt-get install python3.7`

Now this python package is dependent in some other packages to run. So lets install it's dependencies and development tools.

`sudo apt install git python3-pip build-essential wget
python3-dev python3-wheel libxslt-dev
libzip-dev libldap2-dev libsasl2-dev python3-setuptools
libpng12-0 libjpeg-dev gdebi -y`

So, our python is ready to execute our backend of Odoo. But this time we need a database server running to serve and store the data of our system.
Odoo uses **Postgres** as its database. Lets install postgres in our machine and set it up. Note that there are some version compatibility isuue between *Odoo* and *Postgres*. Download the desire one of postgres for your Odoo version.

`sudo apt install postgresql -y`

By default, the only user is **postgres** but Odoo forbids connecting as postgres, so we need to create a new PostgreSQL user:

`sudo -u postgres createuser -s $User_name`

Now let's alter password of our newly created user

- Enter into postgres console

  `sudo -u postgres psql`
  for multiversion postgres -> check running versions
  `pg_lsclusters`
  then `psql --cluster <cluster_version>`
  
- Alter Password

  `ALTER USER user_name WITH PASSWORD 'new_password';`

Now that Python and Postgres is ready, let's prepare a directory where we want to reside our source code.

- create a directory named *OdooDev* or any name you choose and inside it create another directory named *source* where we will clone the source code of odoo as we are following source installation of Odoo[^1].

  `mkdir OdooDev`
  `cd OdooDev`
  `mkdir source`
  
  I assume we already have installed git on our machine. Clone the appropriate branch of the odoo from github. The parameter **single-branch** ensures that we are cloning only that branch we passed on parameter **b** and parameter **depth** equal 1 ensures that we are not downloading all the previous commit history as we do not need those.
  
  `git clone -b 14.0 --single-branch --depth 1 https://github.com/odoo/odoo.git`
  
Till now the packages, libraries and the dependencies we have installed was globally installed because they are common and essential for any application we develop, but there are some packages that should be installed for a specefic application and redundant for other. Also it may not be possible for one specefic version of package installation to meet the requirements of every application. Suppose if application A needs version 1.0 of a particular module but application B needs version 2.0, then the requirements are in conflict and installing either version 1.0 or 2.0 will leave one application unable to run.

Here comes the virtual environment as rescue. We create seperate virtual environment for seperate application/instance of Odoo. Now lets install virtual environment. There are several way to install virtual environment. Choose which seems simple to you.

  - Install pip3
  
    `sudo apt install python3-pip`
  
  - Install virtual environment by pip3
  
    `pip3 install virtualenv`
    
  - Finally create virtual environment
  
    `python3.7 -m virtualenv $your_environment_name`

Now we need to activate our environment that we created and then go to the directory where our odoo source code resides.

  `source /$your_environment_name/bin/activate`
  `cd ~/OdooDev/source/odoo/`
  
  - In this directory we will find a file named requirements.txt where all the listings of required packages are there. We need to install these packages in our environment.
  
  `pip3 install -r requirements.txt`

As the require packages for Odoo is installed, now we need to install the remaining dependencies for different use cases.
Like we need **wkhtmltopdf** that is a library to render HTML into PDF. Odoo uses it to create PDF reports. wkhtmltopdf is not installed through pip and must be installed manually in version 0.12.5 to support headers and footers.

  - `wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.5-1/wkhtmltox_0.12.5-1.focal_amd64.deb`
  - `sudo apt install ./wkhtmltox_0.12.5-1.focal_amd64.deb`
  
Install Web dependencies

  - `sudo apt install npm nodejs`
  - `sudo apt install node-less`
  - `sudo apt install rtlcss`

So, at this point we are finished with all the installation process, we just need some quick configuration to run our Odoo server. In the OdooDev/source/odoo/ directory we have a excucutable odoo-bin file. We need to execute it from the console with the necessary parameters like **addons_path**, **db_host**, **db_port**, **db_user**, **db_user** and so on. But for the sake of complexity and managability we can also create a file with .conf extension and pass the path of this file as the value of **c** parameter like bellow. In this case all other essential parameter should be written inside the .conf file.

  - `./odoo-bin -c $path_of_the_conf_file`
  - where the conf file contains 
      
      `[options]`
      
      `addons_path = $path_to_the_odoo_addons_folder, (add custom addons path here if any)`
      
      `db_host = localhost`
      
      `db_port = 5432`
      
      `http_port = 8069`
      
      `db_password = $superuser_pass`
      
      `db_user = $superuser_name`
      
      `server_wide_modules = web`
 
 After executing the odoo-bin our server should be listening on port 8069.
 
 So that was the installation part, hope we will go on an another trip understanding another concept of odoo soon.
 
 [^1]: There are several ways to install/use Odoo - https://www.odoo.com/documentation/14.0/administration/install/install.html#

