# Aplikasi Web Jitsi Meet
![Jitsi Logo](https://hossted.com/wp-content/uploads/2022/01/7277e5d4a5401b9350bf9ba8491294daf235a106.png)

## Sekilas Tentang

Jitsi adalah kumpulan proyek Open Source yang menyediakan layanan konferensi video canggih yang aman, mudah digunakan, dan mudah di-host sendiri.
Proyek Jitsi awalnya dimulai dengan Jitsi Desktop (sebelumnya dikenal sebagai SIP Communicator). Dengan pertumbuhan WebRTC, fokus tim beralih ke Jitsi Videobridge untuk memberikan layanan panggilan video *multi-party* berbasis web. Kemudian tim menambahkan Jitsi Meet, aplikasi konferensi video lengkap yang mencakup web, Android, dan iOS. Jitsi juga menyediakan layanan meet.jit.si, versi Jitsi Meet yang diselenggarakan oleh Jitsi untuk penggunaan komunitas secara gratis.


## Instalasi
### Prasyarat: Siapkan instance dalam VPS (Amazon Lightsail)
- Registrasi terlebih dahulu pada [AWS](https://aws.amazon.com/)
- Setelah berhasil login, pilih layanan **Lightsail**
- Pilih create instance
![Create Instance](/screenshot/SS%20(1).png)
- Pilih konfigurasi **Linux/Unix**, **OS Only -> Ubuntu**. Kemudian, pilih instance plan **$5 USD** (gratis 3 bulan pertama) dengan spesifikasi 1 GB Memory, 1 vCPU, 40 GB SSD Storage, dan 2 TB Transfer. Kemudian, klik **Create Instance**.
![Create Instance 2](https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(2).png)
![Create Instance 3](https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(3).png)
- Jika berhasil, maka akan tampil instance yang sudah dibuat. <br>
![Instance Selesai](https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(4).png)
- Pada tab **Networking**, jangan lupa untuk setting Public IP menjadi **Static** supaya tidak berubah-ubah ketika instance di-reboot atau di-stop.
![IP Static](https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(5).png)
- Pada tab **Connect**, klik **Connect using SSH** untuk mengakses instance.
![Akses Instance](https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(6).png)
- Instance siap digunakan.
<img src="https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(7).png" width="450">

### Instalasi Custom Domain (Opsional)
- Siapkan custom domain yang telah dimiliki dari suatu provider (dalam hal ini kami menggunakan IDCloudHost)
- Masuk ke konfigurasi DNS Manager, kemudian **Add Record** dengan detail Type A Record, TTL 1800, dan isi RData dengan Public IP yang telah didapatkan dari VPS. <br> <img src="https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(9).png" width="450">
- Custom domain siap digunakan.

### Langkah instalasi dalam CLI <sup>[1]</sup>
- Pertama-tama, install **nginx** sebagai Web Server dengan perintah berikut hingga nginx berhasil terpasang.
```diff
# Retrieve the latest package versions across all repositories
sudo apt update

# Install nginx
sudo apt install nginx
```
![Nginx Installed](https://raw.githubusercontent.com/Aziz8860/Project-Komdat/main/screenshot/SS%20(8).png)
- Kemudian install `apt-transport-https`, `apt-add-repository universe`, dan ditutup dengan update setiap package version.
```diff
# Ensure support for apt repositories served via HTTPS
sudo apt install apt-transport-https

# Jitsi requires dependencies from Ubuntu's universe package repository, ensure this is enabled
sudo apt-add-repository universe

# Retrieve the latest package versions across all repositories
sudo apt update
```
- Jika mesin yang digunakan untuk menghosting instance Jitsi Meet memiliki Fully Qualified Domain Name (FQDN) (misalnya meet.example.org) yang sudah disiapkan di DNS, kita dapat menyetelnya dengan perintah berikut
```
sudo hostnamectl set-hostname ilkommeet.my.id
```
- Kemudian tambahkan FQDN yang sama di file `/etc/hosts`
```
127.0.0.1 localhost
18.138.178.248 ilkommeet.my.id
```
- Tambahkan Prosody package repository. Ini akan menambahkan repositori Prosody sehingga Prosody yang up to date telah terinstal, yang diperlukan untuk fitur termasuk fitur lobi.
```
echo deb http://packages.prosody.im/debian $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list
wget https://prosody.im/files/prosody-debian-packages.key -O- | sudo apt-key add -
apt install lua5.2
```
- Tambahkan Jitsi package repository. Ini akan menambahkan repositori jitsi ke resource package untuk membuat paket Jitsi Meet tersedia.
```diff
curl https://download.jitsi.org/jitsi-key.gpg.key | sudo sh -c 'gpg --dearmor > /usr/share/keyrings/jitsi-keyring.gpg'
echo 'deb [signed-by=/usr/share/keyrings/jitsi-keyring.gpg] https://download.jitsi.org stable/' | sudo tee /etc/apt/sources.list.d/jitsi-stable.list > /dev/null
sudo apt update
```
- Pada AWS Console, setting IPv4 dan IPv6 firewall dengan protokol-protokol berikut
  - `80 TCP` => For SSL certificate verification / renewal with Let's Encrypt. **Required**
  - `443 TCP` => For general access to Jitsi Meet. **Required**
  - `10000 UDP` => For General Network Audio/Video Meetings. **Required**
  - `22 TCP` => For Accessing your Server using SSH (change the port accordingly if it's not 22). **Required**
  - `3478 UDP` => For querying the stun server (coturn, optional, needs config.js change to enable it).
  - `5349 TCP` => For fallback network video/audio communications over TCP (when UDP is blocked for example), served by coturn. **Required** <br>
![Setting Fiewwall](/screenshot/SS%20(10).png)
- Install Jitsi Meet. Ketika instalasi, untuk men-generate TLS/SSL certificate pilih **Let's Encrypt** dan masukkan hostname dengan domain yang telah disiapkan.
```diff
# jitsi-meet installation
sudo apt install jitsi-meet
```
- Pastikan bahwa instalasi berhasil dengan akses domain kita pada web browser. Jika berhasil, maka instalasi telah selesai.

## Konfigurasi (Opsional)

Optimalisasi *systemd* untuk bisa menerima banyak participants. Penerapan default pada sistem yang menggunakan *systemd* akan memiliki nilai default yang rendah untuk proses maksimum dan file yang terbuka. Jika layanan yang digunakan akan mengharapkan jumlah peserta yang lebih tinggi, nilai default perlu disesuaikan (nilai default bagus untuk kurang dari 100 peserta).

Untuk memperbarui value, edit `/etc/systemd/system.conf` dan pastikan memiliki nilai berikut jika nilainya lebih kecil, jika tidak jangan perbarui.
```
DefaultLimitNOFILE=65000
DefaultLimitNPROC=65000
DefaultTasksMax=65000
```
Untuk memeriksa value, jalankan
```
systemctl show --property DefaultLimitNPROC
systemctl show --property DefaultLimitNOFILE
systemctl show --property DefaultTasksMax
```

## Cara Pemakaian

- Tampilan aplikasi web <br>
![Tampilan Web](/screenshot/SS%20(11).png)
- Fungsi-fungsi utama
  - Online video conference dengan gratis dan aman
  - Share link meet untuk mengundang participant
  - Recording
- Percobaan dengan data real dan screenshot
![Percobaan 1](/screenshot/SS%20(12).png)
![Percobaan 2](/screenshot/SS%20(14).png)
![Percobaan 3](/screenshot/SS%20(13).png)



## Pembahasan

- Kelebihan Aplikasi
  - Open source video conference dan dukungan komunitas yang luas
  - User friendly
  - Jitsi menggunakan *hop-by-hop encryption* untuk melindungi konferensi video di mana setiap tahap panggilan video dienkripsi <sup>[2]</sup>
- Kekurangan <sup>[3]</sup>
  - Tidak cocok untuk meeting yang memiliki banyak peserta. Peserta maksimum dapat mencapai 200 orang. Namun, ketika peserta melewati jumlah 75, kualitas proses rapat dan koneksi akan menurun.
  - Memiliki batas waktu. Maksimal penggunakan adalah 3 jam.
  - Kualitas audio dan video yang rendah jika dibandingkan dengan aplikasi sejenis (Google Meet, Zoom)


## Referensi
[1] [Self-Hosting Guide - Debian/Ubuntu server](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-quickstart) <br>
[2] [What are the main cons of Jitsi video conferencing?](https://www.quora.com/What-are-the-main-cons-of-Jitsi-video-conferencing) <br>
[3] [Jitsi Meet App: 11+ Pros And Cons (You Must Know)](https://thenextfind.com/pros-cons-jitsi-meet-app/)

