# Jarkom-Modul-2-E07-2021
Laporan Resmi Soal Shift Modul 2

Pertama, buat topologi sesuai gambar dengan IP Kelompok masing-masing sampai bisa terhubung ke internet. Lalu hubungkan semua host ke internet.

## Soal 2  : Membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku
- Install bind9 pada host EniesLobby dengan command : 
```
apt-get update
apt-get install bind9 -y
```
- Pada host EnisLobby buat website dengan mengedit file **named.conf.local** seperti gambar berikut: 
```
nano /etc/bind/named.conf.local
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/2a.jpg) <br />

- Buat folder kaizoku di /etc/bind Lalu copykan file db.local pada path /etc/bind ke dalam folder kaizoku yang baru saja dibuat dan ubah namanya menjadi franky.e07.com
```
cp /etc/bind/db.local /etc/bind/kaizoku/franky.e07.com
```

- Kemudian buka file franky.e07.com dan edit seperti gambar berikut :
```
nano /etc/bind/kaizoku/franky.e07.com
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/2b.jpg) <br />

- Restart bind : ``` service bind9 restart ```

- Pada client Loguetown arahkan nameserver ke IP Enieslobby dengan cara :
```
echo nameserver 192.203.2.2 > /etc/resolv.conf
```

- Tes koneksi pada Loguetown dengan command :
``` 
ping franky.e07.com
ping www.franky.e07.com
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/2c.jpg) <br />

## Soal 3. Membuat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie
- Edit file franky.e07.com dan tambahkan konfig seperti gambar berikut :
```
nano /etc/bind/kaizoku/franky.e07.com
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/3a.jpg) <br />

- Restart bind : `` service bind9 restart ``

- Tes koneksi pada Loguetown dengan command :
``` 
ping super.franky.e07.com
ping www.super.franky.e07.com
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/3b.jpg) <br />

## Soal 4. Buat juga reverse domain untuk domain utama
- Edit file **nano /etc/bind/named.conf.local** dan tambahkan konfig seperti gambar berikut :
```
zone "2.203.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.203.192.in-addr.arpa";
};
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/4a.jpg) <br />

- Copy file dari db.local ke direktori **kaizoku** dan ganti nama menjadi **2.203.192.in-addr.arpa**, lalu edit file tersebut seperti gambar berikut :
```
cp /etc/bind/db.local /etc/bind/kaizoku/2.203.192.in-addr.arpa
```
``` 
nano /etc/bind/kaizoku/2.203.192.in-addr.arpa
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/4b.jpg) <br />

- Restart bind : `` service bind9 restart ``

- Tes koneksi pada Loguetown dengan command : 
``` host -t PTR 192.203.2.4 ```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/4c.jpg) <br />

## Soal 5.  Membuat Water7 sebagai DNS Slave untuk domain utama
**Konfigurasi pada server EniesLobby**

 - Edit file **nano /etc/bind/named.conf.local** dan tambahkan konfig seperti gambar berikut :
```
zone "franky.e07.com" {
    type master;
    notify yes;
    also-notify { 192.203.2.3; };
    allow-transfer { 192.203.2.3; };
    file "/etc/bind/kaizoku/franky.e07.com";
};
```
 ![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/5a.jpg) <br />

 - Restart bind : `` service bind9 restart `` 

**Konfigurasi pada server Water7**
 - Install bind9 terlebih dahulu : 
```
apt-get update
apt-get install bind9 -y
```

 - Edit file **nano /etc/bind/named.conf.local** dan tambahkan konfig berikut :
```
zone "franky.e07.com" {
	type slave;
	masters { 192.203.2.2; };
	file "/var/lib/bind/franky.e07.com";
};
```       

 - Restart bind : `` service bind9 restart `` 

**Testing**
 - Pada host EniesLobby, matikan bind9 dengan command : ```service bind9 stop```
 - Pada Loguetown, tambahkan nameserver IP Water7 dengan cara : 
 ```
 echo nameserver 192.203.2.3 >> /etc/resolv.conf
 ```
 - Testing dengan command : ``` ping franky.e07.com```
 ![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/5b.jpg) <br />


## Soal 6 : Delegasi subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

**Konfigurasi pada EniesLobby**
 - Edit file ```nano /etc/bind/kaizoku/franky.e07.com``` dan tambahkan konfig seperti gambar berikut :
 ![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/6a.jpg) <br />

 - Lalu edit file **nano /etc/bind/named.conf.options** dengan menambah comment **dnssec-validation auto**; dan tambahkan ``allow-query{any;};``. Lebih lengkapnya seperti gambar berikut :
 ![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/6b-c.jpg) <br />

 - Restart bind9 : ```service bind9 restart```

**Konfigurasi pada Water7**
- Edit file dengan command **nano /etc/bind/named.conf.options** dengan menambah comment **dnssec-validation auto**; dan tambahkan ``allow-query{any;};``. Lebih lengkapnya seperti gambar berikut :
        ![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/6b-c.jpg) <br />

- Edit file **named.conf.local** dan tambahkan konfig berikut :
```
nano /etc/bind/named.conf.local
```
```
zone "mecha.franky.e07.com" {
	type master;
	file "/etc/bind/sunnygo/mecha.franky.e07.com";
};
```  

- Buat file dalam direktori **sunnygo** dengan command **nano /etc/bind/sunnygo/mecha.franky.e07.com** lalu masukkan config seperti berikut :
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.e07.com. root.mecha.franky.e07.com. (
                       2021102401       ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      mecha.franky.e07.com.
@               IN      A       192.203.2.4
```

- Restart bind9 : ```service bind9 restart```

**Testing**
- Pada Loguetown, coba : 
```
ping mecha.franky.e07.com
ping www.mecha.franky.e07.com
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/6d.jpg) <br />

## Soal 7. Membuat subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie

- Edit file dengan command **nano /etc/bind/sunnygo/mecha.franky.e07.com** lalu tambahkan config seperti di bawah ini :
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.e07.com. root.mecha.franky.e07.com. (
                       2021102401       ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      mecha.franky.e07.com.
@               IN      A       192.203.2.4             ; IP skypie
www             IN      CNAME   mecha.franky.e07.com.
general         IN      A       192.203.2.4             ; IP skypie
www.general     IN      CNAME   mecha.franky.e07.com.
```

- Restart bind9 : ```service bind9 restart```

**Testing**
- Pada Loguetown, coba : 
```
ping general.mecha.franky.e07.com
ping www.general.mecha.franky.e07.com
```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/7testing.jpg) <br />
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
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/08a.png) <br />

- Ketujuh, aktifkan franky.e07.com dan restart service apache.
```
a2ensite franky.e07.com
service apache2 restart
```

Testing, buka node loguetown, ketik: ``` lynx franky.e07.com ```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/08b.png) <br />

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

![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/09a.png) <br />

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
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/09b.png) <br />

- Ketujuh, restart service apache. ``` service apache2 restart ```

Testing, buka node loguetown, ketik: ``` lynx franky.e07.com/home ```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/09c.png) <br />

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
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/10a.png) <br />

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
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/10b.png) <br />

- Keenam, install libapache dan restart apache.
```
apt-get install libapache2-mod-php7.0
service apache2 restart
```

Testing, buka node loguetown, ketik: ``` lynx super.franky.e07.com/ ```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/10c.png) <br />

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
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/11a.png) <br />

- Ketiga, lakukan restart apache
```
service apache2 restart
```

Testing, buka node loguetown, ketik: ``` lynx super.franky.e07.com/ ``` lalu arahkan dan enter ke public.
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/11b.png) <br />

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

![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/12a.png) <br />

Lalu restart apache. ``` service apache2 restart ```

Testing, buka node loguetown, ketik: ``` lynx super.franky.e07.com/haha ```
![alt text](https://github.com/migellamp/Jarkom-Modul-2-E07-2021/blob/main/images/12b.png) <br />

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



