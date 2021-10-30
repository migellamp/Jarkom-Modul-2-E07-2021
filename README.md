# Jarkom-Modul-2-E07-2021
Laporan Resmi Soal Shift Modul 2

## Soal 8 : Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.

Pada node ubuntu Skypie:

- Pertama, buat node Skypie tersambung dengan internet. ``` echo nameserver 192.168.122.1 > /etc/resolv.conf ```

- Kedua, install apache, install php, dan jalankan apache.

```
apt-get install apache2 -y
service apache2 start
apt-get install php -y
php -v
service apache2 start
```

- Ketiga, Masuk ke dalam directory "sites-available". ```cd /etc/apache2/sites-available ```

- Keempat, salin file dari ``` /etc/apache2/sites-available/000-default.conf ke /etc/apache2/sites-available/franky.e07.com.conf ```

- Kelima, Edit file "franky.e07.com.conf". ``` nano /etc/apache2/sites-available/super.franky.e07.com.conf ```

- Keenam, tambahkan konfigurasi ke file tersebut
```
ServerAdmin webmaster@localhost

DocumentRoot /var/www/franky.e07.com
ServerName franky.e07.com
ServerAlias www.franky.e07.com
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/8a.png) <br />

- Ketujuh, aktifkan franky.e07.com dan restart service apache.
```
a2ensite franky.e07.com
service apache2 restart
```

Testing, buka node loguetown, ketik: ``` lynx franky.e07.com ```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/8b.png) <br />

## Download file yang diperlukan dari github
- Pertama, instal git, utils, certificates, dan unzip.
```
apt-get install git -y
apt-get install apt-utils -y
apt-get install -y ca-certificates
apt-get install unzip -y
```

- Kedua, buat folder /var/download, untuk menyimpen hasil clone dari github. ``` mkdir /var/download ```

- Ketiga, clone github yang berisi file tadi, unzip ke hasilnya folder /var/www
```
git clone https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom /var/download
unzip /var/download/franky.zip -d /var/www
```

- Keempat, pindahkan hasil unzip tadi ke path yang dibutuhkan
```
mv /var/www/franky /var/www/franky.e07.com
apt-get install libapache2-mod-php7.0
service apache2 restart
```

## Soal 9 : Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home. 

- Pertama, pindah ke var/www. 
```
a2enmod rewrite
service apache2 restart
cd /var/www/franky.e07.com/
```

- Kedua, buat dan edit file .htacces. ``` nano .htaccess ```

- Ketiga, isikan konfigurasi berikut pada file tersebut
```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^home$ /index.php/home [L]

##RewriteRule ^([^\.]+)$ $1.php [NC,L]##
```

- Keempat, pindah ke folder "sites-available" 
```
cd /etc/apache2/sites-available
```

- Kelima, edit dan buat file franky.e07.com.conf. ``` nano franky.e07.com.conf ```

- Keenam, tambahkan konfigurasi berikut.
```
ServerAdmin webmaster@localhost

DocumentRoot /var/www/franky.e07.com
ServerName franky.e07.com
ServerAlias www.franky.e07.com
	
<Directory /var/www/franky.e07.com>
     	Options +FollowSymLinks -Multiviews
     	AllowOverride All
</Directory>
```

- Ketujuh, restart service apache. ``` service apache2 restart ```

## Soal 10 : Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com .

- Pertama, copy 000-default.conf ke super.franky.e07.com.conf. ``` cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/super.franky.e07.com.conf ```

- Kedua, edit super.franky.e07.com.conf. ``` nano super.franky.e07.com.conf ```

- Ketiga, tambahkan konfigurasi berikut.
```
ServerAdmin webmaster@localhost

DocumentRoot /var/www/super.franky.e07.com
ServerName super.franky.e07.com
ServerAlias www.super.franky.e07.com
```

- Keempat, aktifkan super.franky.e07.com dan restart apache.
```
a2ensite super.franky.e07.com
service apache2 restart
```

- Kelima, unzip super.franky.zip yang telah diclone di var/download. Dan pindahkan file ke var/www/super.franky.e07.com.
```
unzip /var/download/super.franky.zip -d /var/www
mv /var/www/super.franky /var/www/super.franky.e07.com
```

- Keenam, install libapache dan restart apache.
```
apt-get install libapache2-mod-php7.0
service apache2 restart
```

## Soal 11 : Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.

- Pertama, buka file super.franky.e07.com.conf
```
nano /etc/apache2/sites-available/super.franky.e07.com.conf
```

- Kedua, tambahkan konfigurasi berikut
```
<Directory /var/www/super.franky.e07.com/public>
	Options +Indexes
</Directory>

<Directory /var/www/super.franky.e07.com/public/*>
	Options -Indexes
</Directory>
```

- Ketiga, lakukan restart apache
```
service apache2 restart
```
## Soal 12 : Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache .

- Pertama, buka dan edit file super.franky.e07.com.conf. ``` nano /etc/apache2/sites-available/super.franky.e07.com.conf ```

- Kedua, tambahkan konfigurasi di bawah
```
ErrorLog ${APACHE_LOG_DIR}/error.log
ErrorDocument 404 /var/www/super.franky.e07.com/error/404.html
CustomLog ${APACHE_LOG_DIR}/acces.log combined

ErrorDocument 404 /error/404.html

<Files "/error/404.html">
	<If "-z %{ENV:REDIRECT_STATUS}">
		RedirectMatch 404 /error/404.html$
	</If>
</Files>
```
Lalu restart apache. ``` service apache2 restart ```

## Soal 13 : Membuat konfigurasi virtual host yang bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js.

- Pertama, Masuk ke dalam directory "sites-available" ```cd /etc/apache2/sites-available```

- Kedua, Edit file "super.franky.e07.com.conf" ```nano /etc/apache2/sites-available/super.franky.e07.com.conf```

- Ketiga, Tambahkan syntax berikut :

```
<Directory /var/www/super.franky.e07.com/public/js>
        Options -Indexes
</Directory>
Alias "/js" "/var/www/super.franky.e07.com/public/js/"
```
- Keempat, Lakukan restart apache menggunakan ```service apache2 restart```
- Output, Ketika mengakases www.super.franky.yyy.com/js :
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/13b.jpg) <br />

## Soal 14 : Web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

- Pertama, Masuk ke dalam directory "sites-available" ```cd /etc/apache2/sites-available```
- Kedua, Copy configurasi default ke configurasi general.mecha.franky dan diedit menggunakan ```nano /etc/apache2/sites-available/general.mecha.franky.e07.com.conf```
- Ketiga, ubah ```<VirtualHost *:80>``` menjadi ```<VirtualHost *:15000 *:15500>```
- Keempat, Ubah server admin, name dan document root mengarah ke general.mecha.franky
```
ServerAdmin webmaster@localhost
ServerName general.mecha.franky.e07.com
DocumentRoot /var/www/general.mecha.franky.e07.com
```
- Kelima, Masuk ke directory apache2 dengan ```nano /etc/apache2```
- Keenam, Tambahkan syntax berikut
```
Listen 15000
Listen 15500
```
- Ketujuh, Untuk mengaktifkan (ENABLE) konfigurasi website yang telah dibuat gunakana ```a2ensite general.mecha.franky.e07.com```
- Kedelapan, Unzip file requirement(yang sebelumnya sudah didownload pada no 8/9) dan pindahkan hasil unzip tersebut ke dalam ```mecha.franky /var/www/general.mecha.franky.e07.com```
- Terakhir, Lakukan restart apache menggunakan ```service apache2 restart```
- Output, Ketika mengakases www.general.mecha.franky.yyy.com:8888 yaitu port yang tidak didaftarkan:
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/14a.jpg) <br />
- Output, Ketika mengakases www.general.mecha.franky.yyy.com:15000 yaitu port yang didaftarkan:
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/14b.jpg) <br />

## Soal 15 : Autentikasi username luffy dan password onepiece
- Pertama, Membuat sebuah autentikasi username dan password pada server menggunakan :
```
htpasswd -c /etc/apache2/.htpasswd luffy 
```
- Masukkan Password yang digunakan untuk user luffy
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/15a.jpg) <br />
- Kedua, Masuk ke directory general.mecha.franky.e07 ```cd /var/www/general.mecha.franky.e07.com``` dan edit file .htaccess 
- Ketiga, Edit file .htaccess dengan menambahkan syntax sebagai berikut:
```
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```
- Keempat, Masuk ke dalam directory "sites-available" ```cd /etc/apache2/sites-available```
- Kelima, Edit file configurasi general.mecha.franky dan tambahkan syntax seperti berikut:
```
<Directory /var/www/general.mecha.franky.e07.com>
        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
</Directory>
```
- Terakhir, Lakukan restart apache menggunakan ```service apache2 restart```
- Output, Ketika mengakases www.general.mecha.franky.yyy.com:15000 :
- Jika user/passwordnya salah akan muncul tampilan seperti berikut:
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/15d.jpg) <br />
- Jika user/passwordnya berhasil maka akan muncul tampilan seperti berikut:
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/15e.jpg) <br />

## Soal 16 : Setiap kali mengakses IP Skypie akan dialihkan secara otomatis ke www.franky.yyy.com
- Pertama, Masuk ke dalam directory "sites-available" ```cd /etc/apache2/sites-available```
- Kedua, Edit file default pada sites-available dan tambahkan syntax sebagai berikut
```
redirect permanent / http://franky.e07.com
```
- Sehingga, ketika kita mengakases IP Skypie maka otomatis akan redirect permanent ke web www.franky.e07.com
- Terakhir, Lakukan restart apache menggunakan ```service apache2 restart```
- Output, Ketika mengakases 192.203.2.4 :
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/16.jpg) <br />

## Soal 17 : Setiap terdapat kata franky, maka otomatis langsung akan dialihkan ke super.franky.e07.com/public/images/franky.png
- Pertama, Masuk ke directory ```cd /var/www/super.franky.e07.com``` untuk mengakses file .htaccess, yang mana pada file tersebut terdapat syntax untuk mendeteksi kata yang terdapat kalimat franky
- Kedua, tambahkan syntax sebagai berikut pada file .htaccess:
```
RewriteEngine On
RewriteRule ^(.*)franky(.*)$ public/images/franky.png
```
Keterangan :
- ```(.*)franky(.*)$``` Digunakan untuk mencari alamat string setelah / yang memiliki kata atau string franky


- Ketiga, Masuk ke dalam directory "sites-available" ```cd /etc/apache2/sites-available```
- Keempat, Edit file super.franky.e07.com.conf dengan menggunakan
```
nano super.franky.e07.com.conf
```
- Kelima, Tambahkan syntax AllowOveride All seperti berikut
```
<Directory /var/www/super.franky.e07.com>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
</Directory>
```
Keterangan :
- ```AllowOverride All``` ditambahkan agar konfigurasi .htaccess dapat berjalan.
- ```+FollowSymLinks``` ditambahkan agar konfigurasi mod_rewrite dapat berjalan.
- ```-Multiviews``` ditambahkan agar konfigurasi mod_negotiation tidak dapat berjalan. mod_negotiation bisa 'rewrite' requests sehingga menimpa dan mengganggu mod_rewrite.

- Terakhir, Lakukan restart apache menggunakan ```service apache2 restart```
- Output, Ketika mengakases super.franky.e07.com/franky :
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/17b.jpg) <br />



