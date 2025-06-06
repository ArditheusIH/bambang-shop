# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

Dalam implementasi BambangShop, sementara pola Observer klasik menyarankan penggunaan antarmuka/ciri untuk `Subscriber` guna memisahkan logika notifikasi, satu struct sudah cukup di sini karena kurangnya beragam jenis pelanggan saat ini—meskipun memperkenalkan ciri `Notifiable` akan membuat desain tahan masa depan untuk ekstensi potensial. Penggunaan `DashMap` diperlukan daripada `Vec` untuk menegakkan keunikan URL dan memungkinkan pencarian/pembaruan O(1) yang efisien, penting untuk operasi yang aman untuk thread dalam lingkungan bersamaan. Pilihan `DashMap` untuk variabel statis `SUBSCRIBERS` selaras dengan jaminan keamanan thread Rust, karena secara inheren menangani sinkronisasi tanpa boilerplate pola Singleton, yang akan memerlukan penguncian manual (misalnya, `Mutex`) dan risiko kemacetan. Pendekatan ini menyeimbangkan Rust idiomatis, kinerja, dan skalabilitas sambil mematuhi prinsip pola desain.

#### Reflection Publisher-2

Dalam pola MVC, pemisahan **Service** (logika bisnis) dan **Repository** (akses data) dari **Model** (struktur data) mematuhi **Single Responsibility Principle**, yang mengurangi penggabungan kode dan meningkatkan kemudahan pemeliharaan—Jika tidak, Model akan menjadi sangat besar dengan penyimpanan dan logika yang bercampur, sehingga mempersulit pengujian dan skalabilitas. Tanpa pemisahan ini, interaksi antara model seperti `Program`, `Subscriber`, dan `Notification` akan melibatkan penanganan data dengan aturan domain, yang menyebabkan kode terduplikasi dan dependensi yang rapuh. Alat seperti **Postman** menyederhanakan pengujian API dengan mengaktifkan validasi titik akhir, alur kerja otomatis (melalui koleksi), dan variabel lingkungan, yang sangat berharga untuk memverifikasi titik akhir (misalnya, `/subscribe` atau `/notify`) dan memastikan perilaku yang konsisten di antara anggota tim, yang secara langsung mendukung pengembangan iteratif dan kolaborasi dalam proyek kelompok.

#### Reflection Publisher-3

Implementasi ini menggunakan **model Push** dari Observer Pattern, di mana publisher (`NotificationService`) secara aktif mengirimkan data tertentu (misalnya, detail produk, status) ke subscriber. Jika menggunakan **Model Pull**, pelanggan akan mengambil data sesuai permintaan, sehingga mengurangi transfer data yang tidak perlu, namun menimbulkan latensi dan kompleksitas (misalnya, query berulang, ketergantungan pada API publisher). Tanpa **multi-threading**, notifikasi akan diproses secara berurutan, memblokir thread utama selama panggilan HTTP hal ini menyebabkan skalabilitas yang buruk (misalnya, tertundanya respons bagi banyak pelanggan) dan menurunkan pengalaman pengguna, terutama pada beban tinggi. Desain saat ini menyeimbangkan efisiensi dan kesederhanaan dengan memanfaatkan konkurensi async untuk notifikasi paralel sambil mempertahankan kontrol langsung atas pengiriman muatan.