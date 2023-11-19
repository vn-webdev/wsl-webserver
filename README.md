# WSL (Windows Subsystem for Linux) 
WSL là một tính năng có trên Windows x64 (từ Windows 10, bản 1607 và trên Windows Server 2019), nó cho phép chạy hệ điều hành Linux (GNU/Linux) trên Windows. 
Với WSL bạn có thể chạy các lệnh, các ứng dụng trực tiếp từ dòng lệnh Windows mà không phải bận tâm về việc tạo / quản lý máy ảo như trước đây. 

# Kiểm tra bật công nghệ ảo hóa (Virtualization Technology)
Công nghệ ảo hóa được cả 2 loại CPU của AMD và Intel hỗ trợ. AMD gọi công nghệ ảo hóa của họ là AMD-V, Intel thì gọi là VT-x hoặc Intel VT. 
Hầu hết thì các CPU hiện nay đều đã được tích hợp công nghệ ảo hóa, ngoại trừ các loại CPU đời cũ.

## Kiểm tra Virtualization
Vào Task Manager > Tab Performance. Các bạn sẽ thấy dòng Virtualization
Nếu nó hiển thị Disabled, thì bạn phải vào BIOS bật công nghệ này lên. 
Nếu bạn không thấy dòng ảo hóa này, tức là dòng CPU không hỗ trợ hoặc dòng CPU đã cũ.

## Bật Virtualization
vào cài đặt BIOS, thông thường thì như bên dưới, nếu khác thì search cách bật dòng main của máy

-Fan đội đỏ CPU AMD  
Overclocking => Advanced CPU configuration => svm mode => chọn Enable

-Fan đội xanh CPU Intel  
Advanced => CPU Configuration => Intel(R) Virtualization Technology => chọn Enable

# Cài đặt WSL
Mở PowerShell quyền Administrator chạy lệnh bên dưới  
~~~
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

wsl --set-default-version 1
~~~
Restart máy lại.

Mở app store và cài đặt Ubuntu  
[https://apps.microsoft.com/detail/ubuntu/9PDXGNCFSCZV?hl=vi-vn&gl=VN](https://apps.microsoft.com/detail/ubuntu-22-04-2-lts/9PN20MSR04DW?hl=vi-vn&gl=VN)

# Cài đặt Apache & Mysql & PHP
Mở Terminal Ubuntu chạy lệnh bên dưới
~~~
sudo apt update && sudo apt upgrade -y
sudo apt-get install apache2 php8.1 libapache2-mod-php8.1 mysql-server php8.1-mysql
sudo apt-get install php-curl php-gd php-intl php-json php-mbstring php-xml php-zip
~~~

## Config Apache
~~~
sudo a2enmod rewrite
sudo service apache2 start
sudo nano /etc/apache2/apache2.conf
~~~
Thêm vào cuối rồi Lưu và thoát:  
AcceptFilter https none  
AcceptFilter http none

~~~
sudo nano /etc/apache2/sites-available/000-default.conf
~~~
Thêm vào trren cùng của file
SetEnvIfNoCase Host \.go SITE_TYPE=local DIR_ROOT=src
SetEnvIfNoCase Host \.test SITE_TYPE=alive DIR_ROOT=dist

<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /mnt/d/www
	RewriteEngine On
	RewriteCond %{HTTP_HOST} ^(.*)(.go|.test)$
	RewriteRule ^(.*)$ /mnt/d/www/%{ENV:SITE_TYPE}/%1/%{ENV:DIR_ROOT}/$1 [L]
</VirtualHost>

## Config Mysql
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

~~~
sudo mysql -u root

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
exit;

sudo service mysql restart
~~~

## Config PHP
~~~
sudo nano /etc/php/8.1/apache2/php.ini
~~~

## Config Phpmyadmin
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
``` lang-php
$cfg['Servers'][$i]['host'] = $dbserver;
```
sửa lại như sau
``` lang-php
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
