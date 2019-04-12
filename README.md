# ubuntu18-pureftpd

A Docker image used for FTP service.

## At beginning 

- make folder tree like below on your docker machine.

/docker<br /> 
&emsp;&emsp;└── /pure-ftp<br /> 
&emsp;&emsp;&emsp;&emsp;    ├── docker-compose.yml<br /> 
&emsp;&emsp;&emsp;&emsp;    ├── etc.tar<br /> 
&emsp;&emsp;&emsp;&emsp;    ├── /ftpdata01<br /> 
&emsp;&emsp;&emsp;&emsp;    ├── /ftp-log<br /> 
&emsp;&emsp;&emsp;&emsp;    ├── /image<br /> 
&emsp;&emsp;&emsp;&emsp;    │&emsp;&emsp;&emsp;├── Dockerfile<br /> 
&emsp;&emsp;&emsp;&emsp;    │&emsp;&emsp;&emsp;└── supervisord.conf<br /> 
&emsp;&emsp;&emsp;&emsp;    └── README.md<br /> 

/ftpdata01 : You can mount a storage on this folder and store FTP files here.<br />
/ftp-log : The tranfer log will locate where.<br />
/image : You can modify the supervisord.conf and and Dockerfile to rebulid the image here.

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
    Account: ftpadmin
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
    # useradd -g users -d "/ftpdata01/newuser" -s /bin/false newuser
    # mkdir -p /ftpdata01/newuser
    # chmod 700 /ftpdata01/newuser
    # chown newuser:users /ftpdata01/newuser
    # echo "newuser:password" | chpasswd
~~~~

## Pure-FTP configuration

If you want to configure pure-ftpd, you can modify /docker/pure-ftp/etc/supervisor/conf.d/supervisord.conf

- Passive mode
~~~~
    -P [your IP] -p [40000:40200]
~~~~

- TLS certificate<br />
Put you pem file to /docker/pure-ftp/etc/ssl/private/
~~~~
    -Y 1 -2 /etc/ssl/private/cert.pem
~~~~

- You can see other settings with this command in the container.
~~~~
    # pure-ftpd --help
~~~~
