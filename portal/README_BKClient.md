#BKCLIENT

A web client for managing virtual desktop in BKCloud Cloud.

#Installation

###Yêu cầu về các thành phần cần có trước khi cài đặt

**BKClient** được xây dựng trên Ruby on Rails Framework, deploy sử dụng
Passenger Nginx , được cài đặt trên server Centos 6.5

**Yêu cầu về hệ điều hành**

OS Centos 6, 64 bit 

**Các thư viện đi kèm**

Ruby 1.9, Ruby 2.0

Cài đặt Ruby sử dụng RVM trên centos 6.5

    sudo yum groupinstall "Development Tools"

    sudo yum install -y curl-devel mysql-devel

    sudo yum install -y git gcc-c++ patch readline readline-devel zlib zlib-devel libyaml-devel libffi-devel openssl-devel sqlite-devel mysql-server

    curl -sSL https://get.rvm.io | bash -s stable

    echo '[[ -s "/usr/local/rvm/scripts/rvm" ]] && source
    "/usr/local/rvm/scripts/rvm" ' \>\> ~/.bashrc

    source ~/.bashrc

    #on non-root user

    sudo yum-config-manager --enable epel

    rvm requirements

    #install ruby

    rvm install 1.9.3

    rvm install 2.0.0

    source ~/.rvm/scripts/rvm

    rvm --default use 1.9.3

**Cài đặt Passenger Nginx**

    gem install passenger

    rvmsudo \`which passenger-install-nginx-module\`

    #fix nginx service

    wget —-no-check-certificate https://raw.github.com/kienbd/scripts/master/RoR/centos/nginx

    sudo cp nginx /etc/init.d/nginx

    sudo chmod +x /etc/init.d/nginx

    sudo chkconfig nginx on

###Cài đặt


**Cài đặt các gem**

    cd bkclient

    bundle install

**Tạo database cho production:**

    cd bkclient

    RAILS_ENV=production rake db:recreate

**Cài đặt redis và sidekiq:**

    cd bkclient

    yum íntall redis

    cp scripts/sidekiq-bkclient /etc/init.d/

    chmod +x /etc/init.d/sidekiq-bkclient

    chkconfig redis on

    service redis start

    chkconfig sidekiq-bkclient on

    service sidekiq-bkclient start

**Cài đặt Websockify:**

    cd bkclient

    git clone  HYPERLINK "https://github.com/kanaka/websockify.git%20scripts/" https://github.com/kanaka/websockify.git scripts/

    chmod +x scripts/websockify-daemon.sh

**Precompile Assets**

    RAILS_ENV=production rake assets:precompile

**Sửa  trong config/environments/production.rb :**

    config.serve_static_assets = true 

    config.assets.compile = true

**Cấu hình nginx qua file /opt/nginx/conf/nginx.conf**

    user  bkclient;

    worker_processes  1;



    error_log  logs/error.log;

    #error_log  logs/error.log  notice;

    #error_log  logs/error.log  info;



    #pid        logs/nginx.pid;

    events {

       worker_connections  1024;

    }

    http {

        passenger_root /home/bkclient/.rvm/gems/ruby-2.0.0-p451/gems/passenger-4.0.41;

        passenger_ruby /home/bkclient/.rvm/gems/ruby-2.0.0-p451/wrappers/ruby;

        include       mime.types;

        default_type  application/octet-stream;

        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '

        #                  '$status $body_bytes_sent "$http_referer"'

        #                  '"$http_user_agent" "$http_x_forwarded_for"';

        #access_log  logs/access.log  main;

        sendfile        on;

        #tcp_nopush     on;

        #keepalive_timeout  0;

        keepalive_timeout  65;

        #gzip  on;

        server {

            listen       80;

            server_name  localhost;

            charset utf-8;

            root /home/bkclient/bkclient/public; #remember the "/public"

            passenger_enabled on;

            rails_spawn_method smart; #

            rails_env production; #config enviroment

            #location / {

            #    root   html;

            #    index  index.html index.htm;

            #}

            #error_page  404              /404.html;

            # redirect server error pages to the static page /50x.html

            #

            error_page   500 502 503 504  /50x.html;

            location = /50x.html {

                root   html;

            }

            location /cas  {

                    alias /home/bkclient/rubycas-server/public;

                    passenger_app_root /home/bkclient/rubycas-server;

                    passenger_enabled on;

            }

            # proxy the PHP scripts to Apache listening on 127.0.0.1:80

            #

            #location ~ \.php$ {

            #    proxy_pass   http://127.0.0.1;

            #}

            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000

            #

            #location ~ \.php$ {

            #    root           html;

            #    fastcgi_pass   127.0.0.1:9000;

            #    fastcgi_index  index.php;

            #    fastcgi_param  SCRIPT_FILENAME /scripts$fastcgi_script_name;

            #    include        fastcgi_params;

            #}

            # deny access to .htaccess files, if Apache's document root

            # concurs with nginx's one

            #

            #location ~ /\.ht {

            #    deny  all;

            #}

        }

        server {

            listen       8888;

            server_name  localhost;

            charset utf-8;

            root /home/bkclient/bkdatabase/public; #remember the "/public"

            passenger_enabled on;

            rails_spawn_method smart; #

            rails_env production; #config enviroment

            error_page   500 502 503 504  /50x.html;

            location = /50x.html {

                root   html;

            }

        }

        # another virtual host using mix of IP-, name-, and port-based configuration

        #

        #server {

        #    listen       8000;

        #    listen       somename:8080;

        #    server_name  somename  alias  another.alias;

        #    location / {

        #        root   html;

        #        index  index.html index.htm;

        #    }

        #}

        # HTTPS server

        #

        #server {

        #    listen       443;

        #    server_name  localhost;

        #    ssl                  on;

        #    ssl_certificate      cert.pem;

        #    ssl_certificate_key  cert.key;

        #    ssl_session_timeout  5m;

        #    ssl_protocols  SSLv2 SSLv3 TLSv1;

        #    ssl_ciphers  HIGH:!aNULL:!MD5;

        #    ssl_prefer_server_ciphers   on;

        #    location / {

        #        root   html;

        #        index  index.html index.htm;

        #    }

        #}

    }


**Features**

**Limitations**