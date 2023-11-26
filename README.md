### Kiểm tra Virtualization
Vào Task Manager > Tab Performance sẽ thấy dòng Virtualization
Nếu nó hiển thị Disabled, thì vào BIOS bật công nghệ này lên. 

Cài đặt BIOS

-CPU AMD  
Overclocking => Advanced CPU configuration => svm mode => chọn Enable

-CPU Intel  
Advanced => CPU Configuration => Intel(R) Virtualization Technology => chọn Enable

### Cài đặt WSL
Mở PowerShell quyền Administrator chạy lệnh bên dưới  
~~~
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

wsl --set-default-version 1
~~~

Mở app store và cài đặt Ubuntu  
[https://apps.microsoft.com/detail/ubuntu/9PDXGNCFSCZV?hl=vi-vn&gl=VN](https://apps.microsoft.com/detail/ubuntu-22-04-2-lts/9PN20MSR04DW?hl=vi-vn&gl=VN)

Bật tự động chạy service khi wsl start  -> mở Ubuntu chạy lệnh bên dưới
~~~
sudo nano /etc/wsl.conf
~~~
Thêm vào cuối rồi Lưu và thoát:  
```
command="service apache2 start; service mysql start;"
```

Cấu hình wsl chạy không cần pass
~~~
sudo nano /etc/sudoers
~~~
Thêm vào cuối rồi Lưu và thoát:  
```
ALL ALL = (root) NOPASSWD: /usr/sbin/service  
ten_username ALL=(ALL) NOPASSWD: ALL
```

Restart máy lại.

### Cài đặt Apache & Mysql & PHP
Mở Ubuntu chạy lệnh bên dưới
~~~
sudo apt update && sudo apt upgrade -y
sudo apt-get install apache2 php8.1 libapache2-mod-php8.1 mysql-server php8.1-mysql
sudo apt-get install php-curl php-gd php-intl php-json php-mbstring php-xml php-zip
~~~

### Config Apache
~~~
sudo nano /etc/apache2/apache2.conf
~~~
Thêm vào cuối rồi Lưu và thoát: 
```
AcceptFilter https none  
AcceptFilter http none
```

Cài đặt vhost
~~~
sudo a2enmod rewrite vhost_alias
sudo nano /etc/apache2/sites-available/000-default.conf
~~~

Thêm vào cuối rồi Lưu và thoát:  
```
<VirtualHost *:80>
    VirtualDocumentRoot "/mnt/d/www/vhost/%-2+/dist"
    ServerName  vhost.test
    ServerAlias *.test
    ErrorLog "/mnt/d/www/vhost/vhost-error.log"

    <Directory "/mnt/d/www/vhost">
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order allow,deny
        Allow from all
        Require all granted
    </Directory>
</VirtualHost>
```
/mnt/c/ = ổ đĩa C:\  
/mnt/d/ = ổ đĩa D:\ trên window  
 
### Config Mysql
~~~
sudo usermod -d /var/lib/mysql/ mysql
sudo service mysql start
sudo mysql_secure_installation
~~~
Các bước chọn: 
1. VALIDATE PASSWORD plugin => N
2. Remove anonymous users? => Y
3. Disallow root login remotely => N
4. Remove test database and access to it? => Y
5. Reload privilege tables now? => Y

Đặt pasword cho user: root
~~~
sudo mysql -u root

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
exit;

sudo service mysql restart
~~~

Host: 127.0.0.1  
User: root  
Password: root  

### Config PHP
~~~
sudo nano /etc/php/8.1/apache2/php.ini
~~~

### Config Phpmyadmin
~~~
sudo apt-get install phpmyadmin
~~~
Các bước chọn: 
1. Apache2 
2. Yes
3. root

~~~
sudo nano /etc/phpmyadmin/config.inc.php
~~~
Tìm dòng bên dưới  
```
$cfg['Servers'][$i]['host'] = $dbserver;
```
sửa lại như sau
```
$cfg['Servers'][$i]['host'] = '127.0.0.1'; 
```

Thêm Url http://localhost/phpmyadmin
~~~
sudo nano /etc/apache2/apache2.conf
~~~
Thêm vào cuối rồi Lưu và thoát:  
IncludeOptional /etc/phpmyadmin/apache.conf

~~~
sudo service apache2 restart
~~~

### Cài đặt GIT, NVM
~~~
sudo apt-get install git curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
~~~


### Tạo DNS Wildcard
Cài đặt Acrylic DNS Proxy  
https://mayakron.altervista.org/support/acrylic/Home.htm  
Cấu hình  
Mở app Acrylic chọn: File -> Open Acrylic Hosts  
sửa 2 dòng cuối thành
~~~
127.0.0.1 localhost localhost.localdomain *.test
::1 localhost localhost.localdomain *.test
~~~

Cấu hình DNS Server cho card mạng  
Ipv4: 127.0.0.1  
Ipv6: ::1

### Cài đặt Vscode wsl extension
https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl
