## Editorial Modul 2

Berikut ini merupakan pembahasan dari soal praktikum Modul 2.

### Soal 1

Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut. 

![topologi](https://media.discordapp.net/attachments/964890423946543124/1166749261745111080/image.png?ex=654b9eed&is=653929ed&hm=7af2e5c6d0868441ad691b7f4b8bea6a697eb4c72c499f502965bae489e995eb&=&width=959&height=671)

### Soal 2

Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

Lakukan konfigurasi DNS Master (Yudhistira) pada `/etc/bind/named.conf.local` sebagai berikut:

```
zone "arjuna.a01.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.a01.com";
};
```

Kemudian lakukan konfigurasi dengan membuat file `/etc/bind/jarkom/arjuna.yyy.com` sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.a01.com.
@       IN      A       10.0.2.3 ; IP Arjuna
@       IN      AAAA    ::1
www     IN      CNAME   arjuna.a01.com.
```

Testing dilakukan dengan melakukan ping pada alamat yang dimaksud.

![testing](https://media.discordapp.net/attachments/964890423946543124/1166752088928305294/image.png?ex=654ba18f&is=65392c8f&hm=d7bce1581f8bdfa4c67eb86078af0226d67e4c11f9a4bbc8a57cedc6f23338b9&=)

### Soal 3

Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

Lakukan konfigurasi DNS Master (Yudhistira) pada `/etc/bind/named.conf.local` sebagai berikut:

```
zone "abimanyu.a01.com" {
        type master;
        notify yes;
        file "/etc/bind/jarkom/abimanyu.a01.com";

};
```

Kemudian lakukan konfigurasi dengan membuat file `/etc/bind/jarkom/abimanyu.yyy.com` sebagai berikut:

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.a01.com.
@       IN      A       10.0.2.5 ; IP Abimanyu
@       IN      AAAA    ::1
www     IN      CNAME   abimanyu.a01.com.
```

Testing dilakukan dengan melakukan ping pada alamat yang dimaksud:

![testing](https://media.discordapp.net/attachments/964890423946543124/1166752784171925615/image.png?ex=654ba234&is=65392d34&hm=b02583bd643c92b01ef31c9c848957f225a0e2355d5a8c376d66eb6abe2f045f&=)

### Soal 4

Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

Lakukan penambahan record untuk konfigurasi DNS dengan menambahkan baris berikut:

```
parikesit       IN      A       10.0.2.5 ; IP Abimanyu
www.parikesit   IN      CNAME   parikesit.abimanyu.a01.com.
```

Kemudian lakukan testing dengan ping ke subdomain

![testing](https://media.discordapp.net/attachments/964890423946543124/1166753439284478043/image.png?ex=654ba2d1&is=65392dd1&hm=d156501427f8a897365b03881425b2850f541144c8d7ef3b644f6f2f7677e6e9&=)

### Soal 5

Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse).

Buatlah konfigurasi baru pada direktori `/etc/bind/jarkom/2.0.10.in-addr.arpa` sebagai berikut

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.0.10.in-addr.arpa.    IN      NS      abimanyu.a01.com.
5                       IN      PTR     abimanyu.a01.com. ; Byte ke-4 Abimanyu
```

Lakukan konfigurasi reverse domain sebagai berikut:
```
zone "2.0.10.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/2.0.10.in-addr.arpa";
};
```

Kemudian lakukan testing dengan menjlankan perintah "host -t PTR [IP yang Direverse]" pada client.

![testing](https://media.discordapp.net/attachments/964890423946543124/1166754208859574393/image.png?ex=654ba388&is=65392e88&hm=fe6bdba5c38a23e03cd9c65ee8fea86924578f08a535062340deed7889c875ab&=)

### Soal 6

Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

Lakukan konfigurasi pada `/etc/bind/named.conf.local` pada DNS Slave (Werkudara)
```
zone "abimanyu.a01.com" {
        type slave;
        masters { 10.0.1.2; };
        file "/var/lib/bind/abimanyu.a01.com";
};
```

Kemudian tambahkan notify pada DNS Master menjadi sebagai berikut:

```
zone "abimanyu.a01.com" {
        type master;
        notify yes;
        also-notify { 10.0.2.2; };
        allow-transfer { 10.0.2.2; };
        file "/etc/bind/jarkom/abimanyu.a01.com";

};
```

Lakukan testing dengan mematikan service bind pada DNS Master untuk memastikan DNS Slave dapat menghandle request.

### Soal 7

Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

Lakukan konfigurasi pada file abimanyu.yyy.com sebagai berikut:
```
ns1             IN      A       10.0.2.2 ; IP Werkudara
baratayuda      IN      NS      ns1
@               IN      AAAA    ::1
```

Kemudian, lakukan konfigurasi `named.conf.local` di DNS Slave (Werkudara).
```
zone "baratayuda.abimanyu.a01.com" {
        type master;
        file "/etc/bind/baratayuda/baratayuda.abimanyu.a01.com";
};
```

Buat konfigurasi DNS untuk baratayuda pada DNS Slave sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.a01.com.
@       IN      A       10.0.2.2
```

Lakukan pengecekan untuk domain yang baru saja diregister.

![testing](https://media.discordapp.net/attachments/964890423946543124/1166758293117607936/image.png?ex=654ba756&is=65393256&hm=81c8096a851a21816fd6eb46bf9e5bc69d47b25c4db4f4783cca942c915d24cf&=)

### Soal 8

Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

Lakukan penambahan record pada DNS Slave sebagai berikut:
```
rjp     IN      A       10.0.2.5
www.rjp IN      CNAME   rjp.baratayuda.abimanyu.a01.com.
```

### Soal 9

Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

Lakukan instalasi web server nginx dan php-fpm pada masing-masing worker.

Buatlah virtual host dengan konfigurasi sebagai berikut:
```sh
server {

        listen 8003;

        root /var/www/arjuna.a01.com;

        index index.php index.html index.htm;
        server_name _;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
}
```

Gunakan port yang berbeda untuk masing-masing worker.

Kemudian lakukan testing untuk localhost masing-masing worker.

![testing](https://media.discordapp.net/attachments/964890423946543124/1166759649547145298/image.png?ex=654ba899&is=65393399&hm=7eca3ea495d4c6457199dbe1bbfe352d4f9b4d6573cb8235c327dccac4ac3645&=)

### Soal 10

Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

Lakukan konfigurasi virtual host load-balancer pada Arjuna sebagai berikut:

```sh
upstream myweb  {
        server 10.0.2.5:8002; #IP Abimanyu
        server 10.0.2.4:8003; #IP Wisanggeni
        server 10.0.2.6:8001; #IP Prabakusuma
}

server {
        listen 80;
        server_name arjuna.a01.com;

        location / {
                proxy_pass http://myweb;
        }
}
```

Kemudian lakukan testing dengan mengakses halaman web beberapa kali, pastikan worker yang menghandle request berganti.

### Soal 11

Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

Unduh file dengan menggunakan wget kemudian letakkan pada direktori `/var/www/abimanyu.yyy`.

Kemudian lakukan konfigurasi virtual host untuk apache web server.

```sh
<VirtualHost *:80>
        ServerName abimanyu.a01.com
        ServerAlias www.abimanyu.a01.com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.a01.com

        <Directory /var/www/abimanyu.a01.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
```

Lakukan testing dengan mengakses halaman web tersebut.

![hal](https://media.discordapp.net/attachments/964890423946543124/1166761162873000067/image.png?ex=654baa02&is=65393502&hm=39111c83c9b675142e99925e357c06b8f04bb77d85f06ebb947dfc22f9aa146f&=)

### Soal 12

Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

Tambahkan .htaccess pada folder root sebagai berikut:
```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^([^\.]+)$ index.php/$1 [NC,L]
```

Lakukan testing dengan mengakes `http://www.abimanyu.yyy.com/home`.

### Soal 13

Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

nduh file dengan menggunakan wget kemudian letakkan pada direktori `/var/www/parikesit.abimanyu.yyy`.

Kemudian lakukan konfigurasi virtual host untuk apache web server.

```sh
<VirtualHost *:80>
        ServerName parikesit.abimanyu.a01.com
        ServerAlias www.parikesit.abimanyu.a01.com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.a01.com
</VirtualHost>
```

Kemudian lakukan testing dengan mengakses halaman web:

![testing](https://media.discordapp.net/attachments/964890423946543124/1166767091714633818/Screenshot_2023-10-14_112224.png?ex=654baf88&is=65393a88&hm=6416bc04e04e9b206c1f7b2b5e49393f566c0c5aa9e6c40af327cb76b87a7db8&=)

### Soal 14

Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

Tambahkan konfigurasi virtual host sebagai berikut:

```sh
        <Directory /var/www/parikesit.abimanyu.a01.com/public>
                Options +Indexes
        </Directory>

        <Directory /var/www/parikesit.abimanyu.a01.com/public/*>
                Options -Indexes
        </Directory>

        <Directory /var/www/parikesit.abimanyu.a01.com/secret/>
                Options -Indexes
                Order deny,allow
                Deny from all
        </Directory>
        <Directory /var/www/parikesit.abimanyu.a01.com/secret/*>
                Order deny,allow
                Deny from all
        </Directory>
```

Kemudian lakukan testing sebagai berikut:

![testing-1](https://media.discordapp.net/attachments/964890423946543124/1166765996892229632/image.png?ex=654bae83&is=65393983&hm=3d67d2def06f45b75cc6ff0d4d5fccf015ff2d3a8fc6aad39d0340a3462e1705&=)

![testing-2](https://media.discordapp.net/attachments/964890423946543124/1166766008736956426/image.png?ex=654bae85&is=65393985&hm=19dd49ea72c1d2c91d2e78685994fc9e2689461cf165e8a1c60cb4174663ec75&=)

### Soal 15

Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

Tambahkan konfigurasi error document pada virtual host sebagai berikut:

```
        ErrorDocument 404 /error/404.html
        ErrorDocument 403 /error/403.html
```

Lakukan test dengan mengakses web sebagai berikut:

![testing-1](https://media.discordapp.net/attachments/964890423946543124/1166766778853118086/image.png?ex=654baf3d&is=65393a3d&hm=11658a8f1e9a34b31b0d4febcd12c5b9a42ed6fa8e202ecafd392c5236fe99d3&=)

![testing-2](https://media.discordapp.net/attachments/964890423946543124/1166766815280648274/Screenshot_2023-10-14_114917.png?ex=654baf46&is=65393a46&hm=1943f303ab0d9080a63065e12b09df6b290da2676c38fe5ab389538b8309a60c&=)

### Soal 16

Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js 

Tambahkan setting virtual host sebagai berikut:

```sh
        Alias "/js" "/var/www/parikesit.abimanyu.a01.com/public/js"
        <Directory /var/www/parikesit.abimanyu.a01.com/public/js>
                Options +Indexes
        </Directory>
```

Kemudian lakukan testing sebagai berikut:

![test](https://media.discordapp.net/attachments/964890423946543124/1166767650563698708/image.png?ex=654bb00d&is=65393b0d&hm=3d3ad4ca71f695394a948df83d61c4f6a1da1c145bebf30206e6725dd9a2e962&=)

### Soal 17

Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

Buatlah konfigurasi virtual host web sebagai berikut:

```sh
<VirtualHost *:14000 *:14400>
        ServerName rjp.baratayuda.abimanyu.a01.com
        ServerAlias www.rjp.baratayuda.abimanyu.a01.com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.a01.com
</VirtualHost>
```

Jangan lupa untuk mengaktifkan port 14000 dan 14400 pada `ports.conf`.

Kemudian lakukan testing dengan mencoba akses tanpa melalui port yang sudah didefinisikan:

![test](https://media.discordapp.net/attachments/964890423946543124/1166768763912343632/image.png?ex=654bb116&is=65393c16&hm=447f38d061522694240b7bd8ec856b5bba9840d69f94f1a5c88a4cbb53616e38&=)

### Soal 18

Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

Tambahkan konfigurasi untuk password pada virtual host sebagai berikut:

```sh
        <Directory /var/www/rjp.baratayuda.abimanyu.a01.com>
                AuthType Basic
                AuthName "Restricted Files"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
```

Lakukan testing sebagai berikut:

![testing](https://media.discordapp.net/attachments/964890423946543124/1166769251768614982/image.png?ex=654bb18b&is=65393c8b&hm=ee86dc0059f30b2cea2949acd16fad6e29eef6110d7a034601b3cb5371cb1d73&=)

### Soal 19

Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

Buatlah .htaccess pada `/var/www/html` (konfigurasi default) sebagai berikut:

```sh
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^10\.0\.2\.5$
RewriteRule ^(.*)$ http://abimanyu.a01.com/$1 [L,R=301]
```

Kemudian lakukan testing sebagai berikut:

![testing](https://media.discordapp.net/attachments/964890423946543124/1166770165250273394/image.png?ex=654bb264&is=65393d64&hm=8f2c63e650f81bcb17be174570962085b49988677bd766d6c3eebdbdc241831e&=)

### Soal 20

Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

Tambahkan `/var/www/parikesit.abimanyu.yyy.com/public/images/.htaccess` sebagai berikut:

```sh
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*)abimanyu(.*)(png|jpg) http://parikesit.abimanyu.a01.com/public/images/abimanyu.png
```

Lakukan testing sebagai berikut:

![testing](https://media.discordapp.net/attachments/964890423946543124/1166771521218412604/image.png?ex=654bb3a8&is=65393ea8&hm=595abf82ca017d96d69a53f555318c1b9860aa69e26216a3da5f3b41328b741b&=)