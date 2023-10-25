## Editorial Modul 1

Berikut ini merupakan pembahasan dari soal praktikum Modul 1.

1. Soal 1 - FTP

    User melakukan berbagai aktivitas dengan menggunakan protokol FTP. Salah satunya adalah mengunggah suatu file.
    
    - Berapakah sequence number (raw) pada packet yang menunjukkan aktivitas tersebut? 

        Answer: 258040667

    - Berapakah acknowledge number (raw) pada packet yang menunjukkan aktivitas tersebut? 

        Answer: 1044861039

    - Berapakah sequence number (raw) pada packet yang menunjukkan response dari aktivitas tersebut?

        Answer: 1044861039

    - Berapakah acknowledge number (raw) pada packet yang menunjukkan response dari aktivitas tersebut?
    
        Answer: 258040696
    
    Pembahasan:
    
    Aktivitas mengunggah file dapat ditunjukkan dengan perintah STOR. Untuk mendapatkan packet tersebut dapat dilakukan dengan filter berikut:

    ```tcp contains "STOR"```

    Kemudian didapatkan satu packet sebagai berikut:

    ![packet-1](https://media.discordapp.net/attachments/964890423946543124/1166381250215411754/Picture1.png?ex=654a4830&is=6537d330&hm=ceee03c1c4f74fb4de610478a1333e55e755166b8bc59c62276758a9868bdc83&=)

    Langsung saja ambil valuenya:

    ![no-1](https://media.discordapp.net/attachments/964890423946543124/1166381565388017664/Picture2.png?ex=654a487b&is=6537d37b&hm=b1290c2d0fa07cbc73b974abd5b8132f34207d1e8ba61301f3035ef984f582aa&=)

2. Soal 2 - HTTP

    Sebutkan web server yang digunakan pada portal praktikum Jaringan Komputer!
    
    Answer: gunicorn

    Web server tersebut dapat diketahui dengan menggunakan filter http pada wireshark. Kemudian melakukan follow tcp stream, maka didapatkan hasil sebagai berikut:

    ![soal-2](https://media.discordapp.net/attachments/964890423946543124/1166382029710053496/Picture3.png?ex=654a48ea&is=6537d3ea&hm=24e36d0ebaeedb3cedd373adf2957452a03cdfa518e666706a9254d292c23d30&=)

3. Soal 3

    - Berapa banyak paket yang tercapture dengan IP source maupun destination address adalah 239.255.255.250 dengan port 3702?
    
        Answer: 21
    - Protokol layer transport apa yang digunakan?

        Answer: UDP

    Pembahasan:

    Buka file packet capture, kemudian pilih menu statistics, pilih IPv4 statistics lalu pilih IP protocol types. Kemudian didapatkan hasil sebagai berikut:

    ![soal-3](https://media.discordapp.net/attachments/964890423946543124/1166382814028116028/Picture4.png?ex=654a49a5&is=6537d4a5&hm=6b5f8faae1ed0929085ada1b369d0917ed62d118a50ffea87e0405eb6f19b0c3&=)

4. Soal 4 - UDP

    Berapa nilai checksum yang didapat dari header pada paket nomor 130?

    Answer: 0x18e5

    Pembahasan:

    Langsung saja ke paket nomor 130, pada bagian User Datagram Protocol (header UDP), bisa dilihat checksum yang tertera

    ![](https://media.discordapp.net/attachments/964890423946543124/1166384919916838943/image.png?ex=654a4b9b&is=6537d69b&hm=b0d95bcc5a785a2656ca8370e79415306541209af2959b4cbcdae5f03169510a&=&width=720&height=300)

5. Soal 5 - SMTP

    - Berapa banyak packet yang berhasil di capture dari file pcap tersebut?

        Answer: 60

    - Port berapakah pada server yang digunakan untuk service SMTP?

        Answer: 25

    - Dari semua alamat IP yang tercapture, IP berapakah yang merupakan public IP?

        Answer: 74.53.140.153

    Pembahasan:

    Peserta akan diberikan file zip dan pcap dengan protocol SMTP sebagai berikut, kemudian follow TCP streamnya. Didapatkan hasil berikut:

    ![soal-5](https://media.discordapp.net/attachments/964890423946543124/1166384945380458556/image.png?ex=654a4ba1&is=6537d6a1&hm=8060a766b6fc79f70153f830b7b21b9ecc47dd7486c17524a3dd0f74cc5977ec&=&width=720&height=222)

    Gunakan password tersebut setelah didecode dengan [Base64](https://www.base64decode.org/) - akan diberikan linknya di deskripsi soal.

    Kemudian setelah dibuka akan didapatkan file berikut:

    ![soal-5](https://media.discordapp.net/attachments/964890423946543124/1166386938006229224/image.png?ex=654a4d7c&is=6537d87c&hm=6698d879fccda3eb2b070d55faf1b0fc2cdabf1220aad2c0204c383dd871763a&=)

    Praktikan dapat menjawab soal melalui netcat.

    ![soal-5](https://media.discordapp.net/attachments/964890423946543124/1166386950303907920/image.png?ex=654a4d7f&is=6537d87f&hm=945fb43bde238c0f4d40b3d8e34d79a5f51e0022c82ff0226fb5f53167bdecd4&=)
    
6. Soal 6 - TCP

    Seorang anak bernama Udin Berteman dengan Slamet yang merupakan seorang penggemar film detektif. sebagai Teman yang baik, Ia selalu mengajak slamet untuk bermain valorant bersama. suatu malam, Terjadi sebuah hal yang tak terduga. ketika Udin mereka membuka game tersebut, laptop udin menunjukkan sebuah field text dan Sebuah kode Invalid bertuliskan "server SOURCE ADDRESS 7812 is invalid". ketika ditelusuri di google, hasil pencarian hanya menampilkan a1 e5 u21. jiwa detektif slamet pun bergejolak. bantulah udin dan slamet untuk menemukan solusi kode error tersebut.

    Answer: JDRNJA

    Pembahasan:

    - Clue 1: slamet menduga bahwa hal tersebut merupakan kode cipher. namun ia tidak dapat menentukan jenis cipher tersebut.
    
    - Clue 2: kode error valorant hanya memiliki rentang 1-500.

    - Solusi: Gunakan Huruf Kapital pada soal kasus, akan menghasilkan S-U-B-S-T-I-T-U-S-I  dan SOURCE ADDRESS. Substitusi cipher dengan kode A=1, B=2,... Z-26, kemudian filter packet no. 7812, dan didapatkan source address 104.18.14.101 = 10,4,18,14,10,1 = JDRNJA.


7. Soal 7 - TCP

    Berapa jumlah packet yang menuju IP 184.87.193.88?

    Answer: 6

    Pembahasan:

    Gunakan Filter ip.dst == 184.87.193.88 dan hitung jumlah paket yang ada, menunjukkan 6 Paket setelah difilter.

    ![soal-7](https://media.discordapp.net/attachments/964890423946543124/1166387849394929694/image.png?ex=654a4e55&is=6537d955&hm=8d4a35a1966bb5f93f26013610198e65c71ccf6527677963943d6815f17541ab&=&width=720&height=69)

8. Soal 8

    Berikan kueri filter sehingga wireshark hanya mengambil semua protokol paket yang menuju port 80! (Jika terdapat lebih dari 1 port, maka urutkan sesuai dengan abjad)

    Answer: tcp.dstport == 80 || udp.dstport == 80 

9. Soal 9

    Berikan kueri filter sehingga wireshark hanya mengambil paket yang berasal dari alamat 10.51.40.1 tetapi tidak menuju ke alamat 10.39.55.34!

    Answer: ip.src == 10.51.40.1 && ip.dest != 10.39.55.34

10. Soal 10 - Telnet
    
    Sebutkan kredensial yang benar ketika user mencoba login menggunakan Telnet

    Answer: dhafin:kesayangannyak0k0

    Pembahasan: tcp.stream eq 15

