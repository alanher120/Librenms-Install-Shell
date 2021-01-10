## 自動化安裝LibreNMS
work on librenms 1.6.2 <br>


# cat /etc/apache2/conf-available/librenms.conf 
Alias /librenms /opt/librenms/html

<Directory /opt/librenms/html>
        Options +FollowSymLinks
        AllowOverride None
        <IfVersion >= 2.3>
                Require all granted
        </IfVersion> 
        <IfVersion < 2.3>
                Order Allow,Deny
                Allow from all
        </IfVersion>

        AddType application/x-httpd-php .php

        <IfModule mod_php.c>
                php_flag magic_quotes_gpc Off
                php_flag short_open_tag On
                php_flag register_globals Off
                php_flag register_argc_argv On
                php_flag track_vars On
                # this setting is necessary for some locales
                php_value mbstring.func_overload 0
                php_value include_path .
        </IfModule>

        DirectoryIndex index.php
</Directory>


LibreNMS - In PackageManifest.php line 122: Undefined index: name ( 問題修正 )
http://www.esafe360.com/2020/12/librenms-in-packagemanifestphp-line-122.html
> 安裝 composer

composer install

> 更新 composer

composer update

> 編輯 PackageManifest.php

sudo vi vendor/laravel/framework/src/Illuminate/Foundation/PackageManifest.php

====================================

$packages = json_decode($this->files->get($path), true);    <== 搜尋這條

$installed = json_decode($this->files->get($path), true);    <== 加入這條

$packages = $installed['packages'] ?? $installed;    <== 加入這條

====================================

composer install
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php composer.phar install
php composer.phar update

LibreNMS是一套開源的網路裝置管理軟體，其分支於Observium，並且加入了社群版沒有的Alert功能。

本專案為自動化安裝LibreNMS Shell Script。


## 安裝步驟：

### Step 1:
    git clone https://github.com/BensonRUEI/Librenms-Install-Shell.git
  
### Step 2:
根據系統不同執行不同的Shell Script，並且建議使用root來進行安裝。
  
  
#### CentOS 7 以上
  
    sh Librenms-Install-Shell/Centos7_install.sh
  
#### Ubuntu 16.04 以上
  
    sh Librenms-Install-Shell/ubuntu_install.sh

### Step 3:
開啟瀏覽器連至：http://YourIP/install.php ,根據內容進行相關設定(如DB Password、DB Name)。

### Step 4:
將Step所顯示的config.php文件寫入至/opt/librenms/config.php

    vim /opt/librenms/config.php

### Step 5:
    chown librenms:librenms /opt/librenms/config.php
   
### Switch SNMP 設定教學
請自行修改IP與CommunityString

    #CISCO
    conf t
    snmp-server community CommunityString
    snmp-server enable trap 
    snmp-server host IP version 2c CommunityString
    end
    write memory 


    #Aruba
    configure t
    snmp-server community CommunityString
    snmp-server enable trap 
    snmp-server host IP version 2c CommunityString
    exit
    write memory


### 安裝教學影片
    Ubuntu 18.04 install LibreNMS
[![](http://img.youtube.com/vi/PDYOwL5pDG8/0.jpg)](http://www.youtube.com/watch?v=PDYOwL5pDG8 "")
    
    CentOS 7 install LibreNMS
[![](http://img.youtube.com/vi/UxsgXax2wBE/0.jpg)](http://www.youtube.com/watch?v=UxsgXax2wBE "")
