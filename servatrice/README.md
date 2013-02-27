# Warnings

BEFORE CONTINUING, PLEASE UNDERSTAND THESE ARE THE VERY MOST BASIC STEPS.

THIS WILL NOT CONFIGURE SECURITY SETTINGS (YOU ARE RESPONSIBLE FOR YOUR OWN SECURITY)!

YOU MUST HAVE RIGHTS TO ELEVATED PRIVILEGES IN ORDER TO PERFORM CERTAIN INSTALL STEPS

YOU MUST HAVE INTERNET ACCESS

IT IS NOT RECOMMENDED TO RUN THE SERVER AS ROOT

USE AT YOUR OWN RISK.

# Compile on CentOS 6

Install the development tools

    sudo yum -y groupinstall "development tools"

Install the EPEL repository

    sudo rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm    # 64 bit OS
    sudo rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm      # 32 bit OS

Install the required dependencies

    sudo yum -y install qt-mysql qt-devel qt-mobility-devel protobuf-devel protobuf-compiler cmake28 libgcrypt-devel

Download the source

    cd ~
    git clone git://github.com/mbruker/Cockatrice.git

Build the source

    cd Cockatrice
    mkdir build
    cd build
    cmake28 -DWITH_SERVER=1 ..
    make
    sudo make install

Copy the servatrice example config file

    sed -e "s/number_pools=1/number_pools=0/" ../servatrice/servatrice.ini.example > ~/servatrice.ini

Install the database server (optional)

    sudo yum -y install mysql-server mysql php-mysql

Start the database server (only required if database is installed)

    sudo service mysqld start

Configure the database server to always run (only required if database is installed)

    sudo chkconfig mysqld on

Configure the database servers root account (only required if database is installed)

    mysqladmin -u root password 'password'

Create the servatrice database (only required if database is installed)

    mysql -u root -ppassword -e "create database servatrice;"

Create the servatrice database user accounts and grant permissions (only required if database is installed)

    mysql -u root -ppassword -e "create user 'servatrice'@'localhost' identified by 'password';"
    mysql -u root -ppassword -e "grant all privileges on servatrice.* to 'servatrice'@'localhost';"

Create the servatrice database structure (only required if database is installed)

    mysql -u servatrice -ppassword servatrice < ../servatrice/servatrice.sql

Modify the servatrice.ini to privide database connectivity information (only required if database is installed)

    sed -i.bak -e "s/password=foobar/password=password/" -e "s/type=none/type=mysql/" -e "s/method=none/method=sql/" ~/servatrice.ini

Create the first user / moderator account

    mysql -u servatrice -ppassword -e "insert into servatrice.cockatrice_users (admin,name,password_sha512,active) values (1,'servatrice','jbB4kSWDmjaVzMNdU13n73SpdBCJTCJ/JYm5ZBZvfxlzbISbXir+e/aSvMz86KzOoaBfidxO0s6GVd8t00qC0TNPl+udHfECaF7MsA==',1);"

Start the servatrice server

    cd ~
    /usr/local/bin/servatrice &

