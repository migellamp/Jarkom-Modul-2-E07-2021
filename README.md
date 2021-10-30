# Jarkom-Modul-2-E07-2021
Laporan Resmi Soal Shift Modul 2

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
- Output, Ketika mengakases www.general.mecha.franky.yyy.com:15000 :

## Soal 15 : Autentikasi username luffy dan password onepiece
- Pertama, Membuat sebuah autentikasi username dan password pada server menggunakan :
```
htpasswd -c /etc/apache2/.htpasswd luffy 
```
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

## Soal 16 : Setiap kali mengakses IP Skypie akan dialihkan secara otomatis ke www.franky.yyy.com
- Pertama, Masuk ke dalam directory "sites-available" ```cd /etc/apache2/sites-available```
- Kedua, Edit file default pada sites-available dan tambahkan syntax sebagai berikut
```
redirect permanent / http://franky.e07.com
```
- Sehingga, ketika kita mengakases IP Skypie maka otomatis akan redirect permanent ke web www.franky.e07.com
- Terakhir, Lakukan restart apache menggunakan ```service apache2 restart```
- Output, Ketika mengakases 192.203.2.4 :

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



