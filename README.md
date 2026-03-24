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
1. Pada kasus ini, apabila hanya terdapat satu jenis observer, penggunaan satu `Model` struct sebenarnya sudah cukup dan tidak wajib menggunakan interface atau trait. Namun, dalam konteks pola Observer, penggunaan trait tetap lebih baik karena memberikan fleksibilitas apabila di kemudian hari terdapat lebih dari satu jenis observer dengan perilaku yang berbeda. Dengan demikian, desain sistem menjadi lebih mudah dikembangkan dan tetap konsisten terhadap prinsip extensibility.

2. Penggunaan `DashMap` lebih sesuai dibandingkan `Vec` karena kebutuhan utama pada kasus ini adalah menyimpan dan mencari subscriber secara efisien berdasarkan `product_type` dan `url`. Jika menggunakan `Vec`, proses pencarian dan penghapusan akan cenderung dilakukan secara linear. Sementara itu, `DashMap` memberikan akses berbasis key yang lebih efisien serta mendukung akses konkuren secara aman, sehingga lebih cocok untuk aplikasi web yang berpotensi menangani banyak request secara bersamaan.

3. `DashMap` tetap diperlukan walaupun kita sudah menerapkan Singleton. Singleton hanya memastikan bahwa instance penyimpanan subscriber hanya ada satu dalam aplikasi, tetapi tidak otomatis menjadikan akses ke data tersebut aman pada lingkungan multithread. Karena aplikasi publisher dapat menerima beberapa request secara bersamaan, dibutuhkan struktur data yang thread-safe. Dalam hal ini, `DashMap` memenuhi kebutuhan tersebut tanpa mengharuskan kita mengelola `Mutex` atau `RwLock` secara manual.

#### Reflection Publisher-2
1. Pemisahan antara `Service` dan `Repository` diperlukan agar masing-masing lapisan memiliki tanggung jawab yang jelas. `Repository` berfokus pada pengelolaan dan akses data, sedangkan `Service` berfokus pada logika bisnis aplikasi. Pemisahan ini membuat struktur kode lebih rapi, lebih mudah dipelihara, serta sejalan dengan prinsip Single Responsibility Principle (SRP) karena setiap komponen hanya menangani satu jenis tanggung jawab utama.

2. Apabila seluruh logika hanya diletakkan di dalam `Model`, maka akan terjadi coupling yang tinggi karena representasi data, akses penyimpanan, dan aturan bisnis bercampur di satu tempat. Dalam kasus ini, interaksi antara `Product`, `Subscriber`, dan `Notification` akan menjadi lebih sulit dikelola karena perubahan pada satu bagian dapat berdampak langsung pada bagian lain. Dengan memisahkan `Model`, `Repository`, dan `Service`, struktur aplikasi menjadi lebih modular dan perubahan dapat dilakukan dengan risiko yang lebih kecil.

3. Postman sangat membantu dalam proses pengembangan karena memudahkan pengujian endpoint HTTP secara langsung tanpa harus membuat antarmuka khusus terlebih dahulu. Selain mendukung berbagai method request, Postman juga mempermudah pengaturan parameter, request body, dan environment variables. Dalam konteks kerja tim, fitur collection dan automated testing pada Postman juga bermanfaat untuk menjaga konsistensi pengujian API selama proses pengembangan berlangsung.

#### Reflection Publisher-3
