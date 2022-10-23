# Aplikasi Web Jitsi Meet


## Sekilas Tentang

Jitsi adalah kumpulan proyek Open Source yang menyediakan layanan konferensi video canggih yang aman, mudah digunakan, dan mudah di-host sendiri.
Proyek Jitsi awalnya dimulai dengan Jitsi Desktop (sebelumnya dikenal sebagai SIP Communicator). Dengan pertumbuhan WebRTC, fokus tim beralih ke Jitsi Videobridge untuk memberikan layanan panggilan video *multi-party* berbasis web. Kemudian tim menambahkan Jitsi Meet, aplikasi konferensi video lengkap yang mencakup web, Android, dan iOS. Jitsi juga menyediakan layanan meet.jit.si, versi Jitsi Meet yang diselenggarakan oleh Jitsi untuk penggunaan komunitas secara gratis.


## Instalasi
### Prasyarat: Siapkan instance dalam VPS (Amazon Lightsail)
- Registrasi terlebih dahulu pada [AWS](https://aws.amazon.com/)
- Setelah berhasil login, pilih layanan **Lightsail**
- Pilih create instance
- Pilih konfigurasi **Linux/Unix**, **OS Only -> Ubuntu**. Kemudian, pilih instance plan **$5 USD** (gratis 3 bulan pertama) dengan spesifikasi 1 GB Memory, 1 vCPU, 40 GB SSD Storage, dan 2 TB Transfer. Kemudian, klik **Create Instance**.
- Jika berhasil, maka akan tampil instance yang sudah dibuat.
- Pada tab **Networking**, jangan lupa untuk setting Public IP menjadi **Static** supaya tidak berubah-ubah ketika instance di-reboot atau di-stop.
- Pada tab **Connect**, klik **Connect using SSH** untuk mengakses instance.
- Instance siap digunakan.

### Langkah instalasi dalam CLI.
- Pertama-tama, install **nginx** sebagai Web Server dengan perintah berikut hingga nginx berhasil terpasang.
```diff
# Retrieve the latest package versions across all repositories
sudo apt update

# Install nginx
sudo apt install nginx
```
- Kemudian install `apt-transport-https`, `apt-add-repository universe`, dan ditutup dengan update setiap package version.
```diff
# Ensure support for apt repositories served via HTTPS
sudo apt install apt-transport-https

# Jitsi requires dependencies from Ubuntu's universe package repository, ensure this is enabled
sudo apt-add-repository universe

# Retrieve the latest package versions across all repositories
sudo apt update
```
- 


## Konfigurasi (opsional)

Setting server tambahan yang diperlukan untuk meningkatkan fungsi dan kinerja aplikasi, misalnya:
- batas upload file
- batas memori
- dll

Plugin untuk fungsi tambahan
- login dengan Google/Facebook
- editor Markdown
- dll


##  Maintenance (opsional)

Setting tambahan untuk maintenance secara periodik, misalnya:
- buat backup database tiap pekan
- hapus direktori sampah tiap hari
- dll


## Otomatisasi (opsional)

Skrip shell untuk otomatisasi instalasi, konfigurasi, dan maintenance.


## Cara Pemakaian

- Tampilan aplikasi web
- Fungsi-fungsi utama
- Isi dengan data real/dummy (jangan kosongan) dan sertakan beberapa screenshot


## Pembahasan

- Pendapat anda tentang aplikasi web ini
    - kelebihan
    - kekurangan
- Bandingkan dengan aplikasi web lain yang sejenis


## Referensi

Cantumkan tiap sumber informasi yang anda pakai.
