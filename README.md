<h1 align="center"><img src="https://financesonline.com/uploads/2019/10/DreamFactory-logo1.png"></h1>

[Sekilas Tentang](#sekilas-tentang) | [Instalasi](#instalasi) | [Konfigurasi](#konfigurasi) | [Otomatisasi](#otomatisasi) | [Cara Pemakaian](#cara-pemakaian) | [Pembahasan](#pembahasan) | [Referensi](#referensi)
:---:|:---:|:---:|:---:|:---:|:---:|:---:

# Sekilas Tentang
[`^ kembali ke atas ^`](#)

**Dreamfactory** adalah sebuah aplikasi berbasis website yang menyediakan layanan API Management secara gratis dan *Open Source*. **Dreamfactory** memberikan kemudahan untuk melakukan pengembangan aplikasi dengan memberikan solusi untuk melakukan manajemen *user*, *services*, dan *data* melalui REST API. **Dreamfactory** sudah bekerja dengan XML-RPC di tahun 1998, SOAP di tahun 2002, REST dan JSON di tahun 2004.


# Instalasi
[`^ kembali ke atas ^`](#)

### Kebutuhan Sistem :
- PHP 7.2+
- Git
- Apache Web server 1.3+.
- MySQL 5.0+.
- Composer
- Unix, Linux atau Windows.
- PhpMyAdmin

### Proses Instalasi :
1. Login kedalam server menggunakan SSH. Untuk pengguna windows bisa menggunakan aplikasi [PuTTY](http://www.putty.org/).
```
Gatau ini apa lupa
```

2. Install seluruh kebutuhan seperti Composer, Git
```
$ sudo apt update
$ sudo apt install composer
$ sudo apt install git
```

3. Install kebutuhan Web Server seperti `Apache`, `PHP`, `PhpMyAdmin`, dan `MySQL`
```
$ sudo apt install apache2
$ sudo apt install mysql-server
$ sudo mysql_secure_installation
$ sudo apt install php libapache2-mod-php php-mysql
$ sudo apt-get install php php-common php-xml php-cli php-curl php-json php-mysqlnd php7.2-sqlite php-soap php-mbstring php-zip php-bcmath
$ sudo apt install phpmyadmin
```

4. Set Up database yang digunakan `MySQL`

  1. Masuk menggunakan root
  ```
  $ sudo mysql -p -u root
  ```
  2. Buat database dan user yang akan digunakan
  ```
  CREATE DATABASE dreamfactory;
  CREATE USER 'dreamfactoryuser'@'localhost' IDENTIFIED BY 'dreamfactorypass';
  GRANT ALL PRIVILEGES ON `dreamfactory`.* TO 'dreamfactoryuser'@'localhost';
  FLUSH PRIVILEGES;
  exit;
  ```
  3. Restart service apache2
  ```
  $ sudo service apache2 reload
  ```

5. Konfigurasi PHP.ini
```
$ sudo nano ~/etc/php/7.2/apache2/php.ini
```
- Cari line yang menunjukkan ;cgi.fix_pathinfo=1
- Ubah menjadi cgi.fix_pathinfo=0
- Save and exit (Ctrl+x, Y, <Enter>)

6. Install MongoDB Extension

  1. Memastikan semua package lengkap
  ```
  $ sudo apt-get install php-dev php-pear build-essential libssl-dev libssl-dev libcurl4-openssl-dev pkg-config
  ```
  2. Install dari PECL
  ```
  $ sudo pecl install mongodb
  ```
  3. Buat .ini file
  ```
  $ sudo sh -c 'echo "extension=mongodb.so" > /etc/php/7.2/mods-available/mongodb.ini'
  ```
  4. Nyalakan MongoDB Extension
  ```
  $ sudo phpenmod mongodb
  ```

7. Set Up project
```
sudo mkdir /opt/dreamfactory
sudo chown -R $USER /opt/dreamfactory
cd /opt/dreamfactory
git clone https://github.com/dreamfactorysoftware/dreamfactory.git ./
composer install --no-dev --ignore-platform-reqs
php artisan df:env
nano .env
php artisan df:setup
sudo chown -R www-data:$USER storage/ bootstrap/cache/
sudo chmod -R 2775 storage/ bootstrap/cache/
php artisan cache:clear
```

8. Set Up Web Server
```
sudo a2enmod rewrite
cd /etc/apache2/sites-available
sudo cp 000-default.conf 000-default.conf.bak
sudo nano 000-default.conf

<VirtualHost *:80>
    DocumentRoot /opt/dreamfactory/public

    <Directory /opt/dreamfactory/public>
        AddOutputFilterByType DEFLATE text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript
        Options -Indexes +FollowSymLinks -MultiViews
        AllowOverride All
        AllowOverride None
        Require all granted
        RewriteEngine on
        RewriteBase /
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule ^.*$ /index.php [L]

        <LimitExcept GET HEAD PUT DELETE PATCH POST>
            Allow from all
        </LimitExcept>
    </Directory>
</VirtualHost>

sudo service apache2 restart
```

# Konfigurasi
[`^ kembali ke atas ^`](#)

# Maintenance
[`^ kembali ke atas ^`](#)

# Otomatisasi
[`^ kembali ke atas ^`](#)

# Cara Pemakaian
[`^ kembali ke atas ^`](#)

# Pembahasan
[`^ kembali ke atas ^`](#)

# Referensi
[`^ kembali ke atas ^`](#)
- [Dreamfactory](https://www.dreamfactory.com/) - Dreamfactory
- [Dreamfactory Wiki](https://wiki.dreamfactory.com/Main_Page) - Dreamfactory Wiki
- [How to setup LAMP](https://medium.com/@oreillyalan88/lamp-linux-apache-mysql-php-web-server-on-an-amazon-ec2-linux-instance-e37eb023e996) - Medium
