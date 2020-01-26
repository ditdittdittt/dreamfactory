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

4. Setup database yang digunakan `MySQL`
    - Masuk menggunakan root
    ```
    $ sudo mysql -p -u root
    ```
    - Buat database dan user yang akan digunakan
    ```
    CREATE DATABASE dreamfactory;
    CREATE USER 'dreamfactoryuser'@'localhost' IDENTIFIED BY 'dreamfactorypass';
    GRANT ALL PRIVILEGES ON `dreamfactory`.* TO 'dreamfactoryuser'@'localhost';
    FLUSH PRIVILEGES;
    exit;
    ```
    - Restart service apache2
    ```
    $ sudo service apache2 reload
    ```

5. Konfigurasi PHP.ini
    - Buka php.ini dengan text editor
    ```
    $ sudo nano ~/etc/php/7.2/apache2/php.ini
    ```
    - Cari line yang menunjukkan `;cgi.fix_pathinfo=1`
    - Ubah menjadi `cgi.fix_pathinfo=0`
    - Save dan exit (Ctrl+x, Y, <Enter>)

6. Install MongoDB Extension
    - Memastikan semua package lengkap
    ```
    $ sudo apt-get install php-dev php-pear build-essential libssl-dev libssl-dev libcurl4-openssl-dev pkg-config
    ```
    - Install dari PECL
    ```
    $ sudo pecl install mongodb
    ```
    - Buat .ini file
    ```
    $ sudo sh -c 'echo "extension=mongodb.so" > /etc/php/7.2/mods-available/mongodb.ini'
    ```
    - Nyalakan MongoDB Extension
    ```
    $ sudo phpenmod mongodb
    ```

7. Setup project
    - Buat direktori instalasi dan jadikan user sebagai pemilik direktori tersebut.
    ```
    $ sudo mkdir /opt/dreamfactory
    $ sudo chown -R $USER /opt/dreamfactory  
    ```
    - Pindah ke dalam direktori yang telah dibuat dan lakukan git clone dari repository **Dreamfactory**
    ```
    $ cd /opt/dreamfactory
    $ git clone https://github.com/dreamfactorysoftware/dreamfactory.git ./
    ```
    - Karena **Dreamfactory** menggunakan package dari composer, maka kita harus melakukan instalasi composer
    ```
    $ composer install --no-dev --ignore-platform-reqs
    ```
    - Setup database
    ```
    $ php artisan df:env
    ```
    - Ubah pengaturan di dalam file .env
    ```
    $ nano .env
    ```
    - Hilangkan comment di `##DB_CHARSET=utf8` dan `##DB_COLLATION=utf8_unicode_ci` dengan cara menghapus bagian `##`, lalu save dan exit (Ctrl+x,Y,<Enter>)
    
    - Setup project
    ```
    $ php artisan df:setup
    ```
    - Ubah *permissions* di direktori `storage` dan `cache`
    ```
    $ sudo chown -R www-data:$USER storage/ bootstrap/cache/
    $ sudo chmod -R 2775 storage/ bootstrap/cache/
    ```
    - Hapus *cache* aplikasi agar project menggunakan konfigurasi yang baru dibuat
    ```
    $ php artisan cache:clear 
    ```

8. Set Up Web Server
    - Nyalakan *Rewrite Engine*
    ```
    $ sudo a2enmod rewrite
    ```
    - Pindah ke direktori konfigurasi dan backup default konfigurasi
    ```
    $ cd /etc/apache2/sites-available
    $ sudo cp 000-default.conf 000-default.conf.bak
    ```
    - Ubah *default config* dengan text editor
    ```
    $ sudo nano 000-default.conf
    ```
    - Sehingga `000-default.conf` menjadi seperti
    ```
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
    ```
    - Restart service apache2
    ```
    $ sudo service apache2 restart
    ```
9. Kunjungi alamat IP Web Server kita untuk melakukan login menggunakan akun saat setup

    - Login dengan akun yang sudah dibuat
    ![1](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Login%20Page.png)
    - Siap digunakan
    ![2](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Home%20Page.png)

# Konfigurasi
[`^ kembali ke atas ^`](#)
- Untuk melihat info dari sistem yang kita gunakan kita bisa membuka halaman *System Info* yang ada di tab *Config*
    ![1](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20System%20Info.png)
- Untuk melakukan pengaturan pada *cache* guna meningkatkan performa, kita bisa membuka halaman *cache* yang ada di tab *Config*
    ![2](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20Cache.png)
- **Dreamfactory** juga sudah menyediakan kemudahan untuk melakukan pengaturan **CORS** di halaman *CORS* yang ada di tab *Config*
    ![3](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20CORS.png)
- Sedangkan untuk melakukan konfigurasi template dari email yang akan dikirim, kita bisa melakukannya di halaman *Email Templates* yang ada di tab *Config*
    ![4](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20Email%20Templates.png)
- Sedangkan untuk membuat konfigurasi *environment* yang digunakan, kita bisa mengaksesnya di halaman *Global Lookup Keys*
    ![5](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Config%20Global%20Keys.png)

# Otomatisasi
[`^ kembali ke atas ^`](#)
- Cara pertama menggunakan bitnami, **Dreamfactory** sudah menyediakan installer untuk memudahkan para user melakukan instalasi dream factory. Cukup download installer **Dreamfactory** dari [bitnami](https://bitnami.com/stack/dreamfactory/installer) sesuai dengan OS yang digunakan. Lalu install menggunakan installer yang telah di-download
    ![1](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Bitnami.png)
    
- Cara kedua menggunakan salah satu penyedia Hosting yaitu **Heroku**, cukup dengan memiliki akun **Heroku**, kita bisa melakukan hosting otomatis dengan melakukan sedikit konfigurasi
    
    <p align="center">
        <a href="https://heroku.com/deploy?template=https://github.com/dreamfactorysoftware/dreamfactory">
        <img src="https://www.herokucdn.com/deploy/button.svg" alt="Deploy">
        </a>
    </p>
    
    ![2](https://raw.githubusercontent.com/ditdittdittt/dreamfactory/master/Screenshot/Heroku.png)

# Cara Pemakaian
[`^ kembali ke atas ^`](#)

# Pembahasan
[`^ kembali ke atas ^`](#)

# Referensi
[`^ kembali ke atas ^`](#)
- [Dreamfactory](https://www.dreamfactory.com/) - Dreamfactory
- [Dreamfactory Wiki](https://wiki.dreamfactory.com/Main_Page) - Dreamfactory Wiki
- [How to setup LAMP](https://medium.com/@oreillyalan88/lamp-linux-apache-mysql-php-web-server-on-an-amazon-ec2-linux-instance-e37eb023e996) - Medium

