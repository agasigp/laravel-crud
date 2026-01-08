# Laravel Simple CRUD

Aplikasi ini adalah contoh CRUD sederhana menggunakan [Laravel 12](https://laravel.com/docs/12.x), [inertiajs](https://inertiajs.com/docs/v2/getting-started/index), [vuejs](https://vuejs.org/), & [tailwindcss](https://tailwindcss.com/). Pastikan [composer](https://getcomposer.org/) & [nodejs](https://nodejs.org/en) telah terinstall sebelumnya.

## Instalasi

-   Clone/download repo ini, jalankan perintah berikut untuk instalasi laravel
    ```bash
    composer install
    ```
-   Jalankan perintah berikut untuk mneginstall node modules yang diperlukan
    ```bash
    npm install --legacy-peer-deps
    ```
-   Salin file `.env.example` kemudian uabh nama menjadi `.env` Sesuaikan konfigurasi database yang digunakan. Jika menggunakan sqlite, pastikan terdapat file `database.sqlite` di folder **database**. Pastikan juga ekstensi **php-sqlite3** juga sudah terinstall.
-   Jalankan perintah berikut untuk generate app key
    lankan perintah berikut untuk menjalakan migrasi database beserta seeder user.
    ```bash
    php artisan key:generate
    ```
-   Jalankan perintah berikut untuk menjalakan migrasi database beserta seeder user.
    ```bash
    php artisan migrate --seed
    ```
-   Jalankan perintah berikut untuk menjalankan web server lokal & vite development server untuk compile aset css & js
    ```bash
    composer run dev
    ```
-   Aplikasi bisa diakses di [http://localhost:8000/login](http://localhost:8000/login). Untuk masuk, bisa menggunakan akun **test@example.com** / **password**

## Penjelasan

Aplikasi mini CRUD ini dibuat menggunakan [Laravel Jetstream](https://jetstream.laravel.com/introduction.html) Basisnya adalah [Laravel 12](https://laravel.com/docs/12.x), [inertiajs](https://inertiajs.com/docs/v2/getting-started/index), [vuejs](https://vuejs.org/), & [tailwindcss](https://tailwindcss.com/).

Validasi di aplikasi ini menggunakan [Form Request](https://laravel.com/docs/12.x/validation#form-request-validation) supaya kode lebih rapi. Valdiasi yang digunakan juga tidak ada yang istimewa. Field yang wajib diisi (required) hanya nama, harga, berat, & kategori. Sisanya tidak wajib diisi (nullable). Untuk harga & berat, wajib berupa angka. Untuk nama, berupa string dengan maksimal panjangnya 255 karakter. Untuk kategori, hanya boleh berupa TV,PC,GA,PH.
