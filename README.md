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
~~~
~~~
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
~~~

restart máy

~~~
wsl --update
wsl --set-default-version 1
~~~

Mở app store và cài đặt Ubuntu  
[https://apps.microsoft.com/detail/ubuntu/9PDXGNCFSCZV?hl=vi-vn&gl=VN](https://apps.microsoft.com/detail/ubuntu-22-04-2-lts/9PN20MSR04DW?hl=vi-vn&gl=VN)

Cấu hình linux không hỏi pass admin
~~~
sudo nano /etc/sudoers
~~~
Thêm vào cuối rồi Lưu và thoát:  
```
ALL ALL = (root) NOPASSWD: /usr/sbin/service  
ten_username ALL=(ALL) NOPASSWD: ALL
```


Bật tự động chạy service khi wsl start  -> mở Ubuntu chạy lệnh bên dưới
~~~
sudo nano /etc/wsl.conf
~~~
Thêm vào cuối rồi Lưu và thoát:  
```
command="service apache2 start; service mysql start;"
```

### Cài đặt Apache & Mysql & PHP
Mở Ubuntu chạy lệnh bên dưới
~~~
sudo apt update && sudo apt upgrade -y
sudo apt install lamp-server^

sudo apt-get install php-curl php-gd php-intl php-json php-mbstring php-xml php-zip
~~~

### Config Apache

[/mnt/d/] là ổ đĩa D:/ trên window 

~~~
sudo mkdir /mnt/d/www
sudo mkdir /mnt/d/www/log
sudo chown -R www-data:www-data /mnt/d/www
sudo chmod -R g+rwX /mnt/d/www

sudo a2enmod rewrite vhost_alias headers

sudo nano /etc/apache2/apache2.conf
~~~

Thêm vào cuối rồi Lưu và thoát: 
```
AcceptFilter https none  
AcceptFilter http none
```

~~~
sudo nano /etc/apache2/sites-available/000-default.conf
~~~

Thay doi Lưu và thoát:  
```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /mnt/d/www

    RewriteEngine On

    RewriteCond %{HTTP_HOST} ^(.*).go$
    RewriteRule (.*) /mnt/d/www/local/%1/app/$1 [L]

    RewriteCond %{HTTP_HOST} ^(.*).test$
    RewriteRule (.*) /mnt/d/www/alive/%1/dist/$1 [L]
</VirtualHost>
```
 
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
sudo nano /etc/phpmyadmin/config-db.php
~~~
Sửa $dbuser là root
Sửa $dbserver là 127.0.0.1

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
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
nvm install v10.24.1
nvm alias default v10.24.1
~~~
Setting git cho VS Code
https://gist.github.com/victor-perez/8ba25540394ec68b11f4b2699fb4d4ce?permalink_comment_id=2763711


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

### Cấu hình thêm
Auto connect wsl khi mở Vscode  
Mở shortcut property -> chỗ target thêm vào cuối
 --remote wsl+Ubuntu-22.04  
"C:\Users\ten_user\AppData\Local\Programs\Microsoft VS Code\Code.exe" --remote wsl+Ubuntu-22.04

Thư mục mặc định khi open new project  
Mở Vscode Settings -> search files.dialog  -> nhập /mnt/d/vhost

C:\Users\ten_user\.wslconfig
~~~
[wsl2]
kernelCommandLine=ipv6.disable=1
autoMemoryReclaim=gradual  # gradual | dropcache | disabled
networkingMode=mirrored        # mirrored | NAT | None 
dnsTunneling=true
firewall=false
autoProxy=true
~~~
