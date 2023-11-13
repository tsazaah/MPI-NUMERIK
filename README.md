# MPI-Numerik
Menjalankan Program Numerik Secara Paralel dengan Menggunakan MPI

## Menjalankan Numerik Python dengan MPI Multinode Cluster pada Linux Mint
Panduan ini menyediakan petunjuk langkah demi langkah untuk membuat master dan slave, mengonfigurasi SSH, mengonfigurasi NFS, menginstal MPI, dan menjalankan kode Numerik dengan Python di Linux Mint.

## Daftar Isi
- [Perangkat dan Alat yang Harus Dipersiapkan](#perangkat-dan-alat-yang-harus-dipersiapkan)
- [Topologi](#topologi)
- [Membuat Master dan Slave](#membuat-master-dan-slave)
- [Konfigurasi SSH](#ssh-configuration)
- [Konfigurasi NFS](#nfs-configuration)
- [Instalasi MPI](#mpi-installation)
- [Menjalankan Kode Python - Numerik](#menjalankan-kode-python---Numerik)

## Perangkat dan Alat yang Harus Dipersiapkan
1. Linux Mint
   - Linux Mint Master
   - Linux Mint Slave 1
   - Linux Mint Slave 2
   - Linux Mint Slave 3
2. MPI (Master dan Slave)
3. SSH (Master dan Slave)
4. NFS (Master dan Slave)
5. Kode Python Numerik
## Topologi

![Screenshot 2023-11-13 075701](https://github.com/tsazaah/MPI-NUMERIK/assets/150001965/aae8654c-593f-4c24-81db-ef78ac33a3e6)


## Membuat Master dan Slave
1. Pastikan setiap master dan slave menggunakan Network Bridge Adapter dan tersambung ke internet.
2. Tentukan perangkat mana yang akan menjadi master, slave1, slave2, dan slave3.
3. Buat pengguna baru dengan perintah berikut pada master dan setiap slave:
    ```bash
    sudo adduser mpiuser
    ```
4. Berikan akses root dengan perintah:
    ```bash
    sudo usermod -aG sudo mpiuser
    ```
    Ulangi langkah di atas untuk setiap budak.
5. Masuk ke setiap server dengan pengguna `mpiuser`:
    ```bash
    su - mpiuser
    ```
6. Perbarui Linux Mint dan instal alat bantu:
    ```bash
    sudo apt update && sudo apt upgrade
    sudo apt install net-tools vim
    ```
7. Konfigurasikan berkas `/etc/hosts` pada master, slave1, slave2, dan slave3. Daftarkan IP dan nama host dari setiap komputer.

## Konfigurasi SSH
1. Instal OpenSSH pada master dan semua slave:
    ```bash
    sudo apt install openssh-server
    ```
2. Buat kunci pada master:
    ```bash
    ssh-keygen -t rsa
    ```
3. Salin kunci publik ke setiap budak menggunakan perintah berikut pada direktori `.ssh`:
    ```bash
    cd .ssh
    cat id_rsa.pub | ssh mpiuser@slave1 "mkdir .ssh; cat >> .ssh/authorized_keys"
    ```
    Ulangi perintah ini untuk setiap slave.

## Konfigurasi NFS
1. Buat folder bersama pada master dan setiap slave:
    ```bash
    mkdir /home/mpiusr/fix
    ```
2. Instal NFS pada master:
    ```bash
    sudo apt install nfs-kernel-server
    ```
3. Konfigurasikan berkas `/etc/exports` pada master. Tambahkan baris berikut pada akhir berkas:
    ```plaintext
    /home/mpiusr/fix *(rw,sync,no_root_squash,no_subtree_check)
    ```
    Lokasi Folder Bersama adalah direktori tempat berkas numerik dibuat di atas.
4. Mulai ulang Server NFS:
    ```bash
    sudo exportfs -a
    sudo systemctl restart nfs-kernel-server
    ```
5. Instal NFS pada setiap slave:
    ```bash
    sudo apt install nfs-common
    ```
6. Pasang folder bersama dari master ke setiap slave:
    ```bash
    sudo mount master:/home/mpiusr/fix /home/mpiusr/fix
    ```
    Ulangi perintah ini untuk setiap slave.

## Instalasi MPI
1. Instal Open MPI pada master dan semua slave:
    ```bash
    sudo apt install openmpi-bin libopenmpi-dev
    ```
2. Instal pustaka MPI melalui pip:
    ```bash
    sudo apt install python3-pip
    pip install mpi4py
    ```


## Menjalankan Kode Python - numerik
1. Buat sebuah berkas Python baru:
    ``` bash
    touch /home/mpiusr/fix/pers22.py
    ```
2. Arahkan ke direktori tersebut dan edit file Python:
    ```bash
    cd /home/mpiusr/fix
    nano pers22.py
    ```
    Kemudian buat kode Python Bubble Sort. Simpan dengan menekan `CTRL + X` lalu tekan `Y`.
    

3. Jalankan kode tersebut pada master:
    ```bash
    mpirun -np 4 -host master,slave1,slave2,slave3 python3 pers22.py
    ```
4. Output    
![WhatsApp Image 2023-11-13 at 09 40 58_08380858](https://github.com/tsazaah/MPI-NUMERIK/assets/150001965/7976a314-a9cb-4f19-b477-61bdd7290a88)
