# ubuntu18-pureftpd

A Docker image used for FTP service.

## At beginning 

- make folder tree like below on your docker machine.

/docker/
        pure-ftp/
                 docker-compose.yml
                 etc.tar
                 ftpdata01/
                 ftp-log/
                 image/
                       Dockerfile
                       supervisord.conf
                 README.md

/ftpdata01 : You can mount a storage on this folder and store FTP files here.
/ftp-log : The tranfer log will locate where.
/image : If you want to configure pure-ftp, you can modify the supervisord.conf and rebulid the image.

- make sure you have installed [docker-compose](https://docs.docker.com/compose/install/)

- untar the etc.tar 
~~~~
    tar -xvf ./etc.tar
~~~~
You will get /etc folder. FTP account is managed by OS in the folder.

All files can be found in [GitHub](https://github.com/iankao0914/ubuntu18-pureftpd)

## Start FTP service

- Run a container:
~~~~
    # docker-compose -f /docker/pure-ftp/docker-compose.yml up -d
~~~~

- Login container via SSH:
~~~~
    Account: ftpdmin
    Password:changeme
    Port:9922
~~~~

- Switch to root:
~~~~
    # sudo su -
~~~~

## Create FTP account

Do this action in the container as root.
- Create account "newuser" and change home folder to /ftpdata01
~~~~
    # useradd -g users -d "newuser" -s /bin/false newuser
    # mkdir -p /ftpdata01/newuser
    # chmod 700 /ftpdata01/newuser
    # chown newuser:users /ftpdata01/newuser
    # echo "newuser:password" | chpasswd
~~~~
