***********Docker image with ubuntu:18.04, apache2, php7.2 and mysql:5.6***********
-				
Folder Structure:
-
    >cd php_mysql
    >ls
     app dockerfile php.yml
    >cd app
     index.html name.php
    >cd dockerfile
      Dockerfile

php.yml
-
    version: '3'
    services:
      db:
        image: mysql:5.6
        environment:
          - MYSQL_ROOT_PASSWORD=welcome@123
          - MYSQL_DATABASE=srinidhi
          - MYSQL_USER=sri
          - MYSQL_PASSWORD=welcome@123
        networks:
          - mynet
      web:
        image: apache-php-mysql:1
        volumes:
          - ./app:/var/www/html
        ports:
          - 9999:80
        depends_on:
          - db
        networks:
          - mynet
    networks:
      mynet:

Dockerfile
-
    FROM ubuntu:18.04
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update && apt-get install -yq --no-install-recommends \
        apache2 \
        libapache2-mod-php7.2 \
        php7.2-mysql \
        mysql-client \
        && rm -rf /var/lib/apt/lists/*

    CMD apachectl -D FOREGROUND

Commands to build and deploy yaml file :
-
To Build
    
    #docker build -t apache-php-mysql:1 .

To Deploy the above image
    
    #docker stack deploy -c php.yml php


=========================================================================

WebSite Files
-						
index.html - FrontEnd
-
    <!DOCTYPE html>
    <html>
    <body>
    <form method="post" action="name.php">
    Username : <input type="text" name="username"><br><br>
    Password : <input type="password" name="password"><br><br>
    <input type="submit" value="Submit">
    </form>
    </body>
    </html>

name.php - BackEnd 
-
    <?php
     $username = filter_input(INPUT_POST, 'username');
     $password = filter_input(INPUT_POST, 'password');
     if (!empty($username)){
    if (!empty($password)){
    $host = "php_db";
    $dbusername = "root";
    $dbpassword = "welcome@123";
    $dbname = "srinidhi";
    // Create connection
    $conn = new mysqli ($host, $dbusername, $dbpassword, $dbname);
    if (mysqli_connect_error()){
    die('Connect Error ('. mysqli_connect_errno() .') '
    . mysqli_connect_error());
    }
    else{
    $sql = "INSERT INTO phpsql(username, password)
    values ('$username','$password')";
    if ($conn->query($sql)){
    echo "New record is inserted sucessfully";
    }
    else{
    echo "Error: ". $sql ."
    ". $conn->error;
    }
    $conn->close();
    }
    }
    else{
    echo "Password should not be empty";
    die();
    }
    }
    else{
    echo "Username should not be empty";
    die();
    }
    ?>

MySql Queries - DataBase
-
    >docker exec -it <mysql container ID> bash
    >mysql -u root -p
    >password:""
    >Use databases;
    >CREATE	TABLE phpsql (username VARCHAR(20), password VARCHAR(20));
    After the first insertion in web browser 
    >SELECT * FROM phpsql;