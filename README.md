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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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
> In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber
is defined as an interface. Explain based on your understanding of Observer design patterns,
do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model
struct is enough?

Dalam kasus ini, tidak perlu menggunakan interface (`trait`) dan cukup menggunakan single struct model tanpa trait karena observer-nya hanya class Subscriber. Observer pattern dengan trait berguna saat ada banyak jenis observer yang menangain notifikasi secara berbeda. 

> id in Program and url in Subscriber is intended to be unique. Explain based on your
understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently
use is necessary for this case?

Penggunaan DashMap dalam implementasi ini lebih tepat dibandingkan dengan Vec karena keunikan url dalam Subscriber tanpa perlu pengecekan manual. Dengan struktur DashMap<String, DashMap<String, Subscriber>>, setiap product_type memiliki daftar subscriber yang tersimpan dalam map, di mana url digunakan sebagai kunci unik, memungkinkan pencarian, penambahan, dan penghapusan data dengan kompleksitas rata-rata O(1). Sebaliknya, jika menggunakan Vec, pencarian subscriber harus dilakukan dengan iterasi O(n) yang menjadi kurang efisien seiring bertambahnya data. Oleh karena itu, penggunaan DashMap dalam kasus ini tetap diperlukan karena lebih optimal dalam hal efisiensi pencarian, keunikan data, serta dukungan untuk concurrent access.

> When programming using Rust, we are enforced by rigorous compiler constraints to make a
thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we
used the DashMap external library for thread safe HashMap. Explain based on your
understanding of design patterns, do we still need DashMap or we can implement Singleton
pattern instead?

Aplikasi BambangShop menggunakan multi-threading, sehingga DashMap lebih cocok dibandingkan dengan Singleton Pattern. DashMap adalah thread-safe HashMap yang memungkinkan akses data secara bersamaan (concurrent access) tanpa risiko race condition. Sementara itu, Singleton Pattern hanya memastikan satu instance objek, tetapi tidak secara otomatis mendukung akses multi-threading dengan aman. Jika menggunakan Singleton dengan HashMap biasa, kita perlu menambahkan mekanisme locking untuk mencegah konflik antar-thread, yang dapat meningkatkan kompleksitas program dan berpotensi menyebabkan deadlock. Oleh karena itu, DashMap adalah pilihan yang lebih efisien dan aman untuk mengelola data SUBSCRIBERS dalam lingkungan multi-threading.

#### Reflection Publisher-2
> In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”.
Model in MVC covers both data storage and business logic. Explain based on your
understanding of design principles, why we need to separate “Service” and “Repository” from
a Model?

Salah satu prinsip, yaitu SRP (Single Responsibility Principle) yang merupakan sebuah class hanya boleh berfungsi sesuai dengan kelasnya sehingga kita perlu memisahkan Service dan Repository dari Model. Dengan memisahkan service dan repository dari model, kita bisa memisahkan antara business logic dan data access sehingga lebih mudah digunakan untuk dites dan di-_debug_.

> What happens if we only use the Model? Explain your imagination on how the interactions
between each model (Program, Subscriber, Notification) affect the code complexity for
each model?

Jika kita hanya menggunakan Model untuk menangani semua tugas, kita akan menghadapi kompleksitas yang meningkat, ketergantungan yang tinggi, dan kesulitan dalam pengujian dan pemeliharaan. Pembagian tanggung jawab antara Service, Repository, dan Model akan mengurangi kompleksitas, mempermudah pengujian dan memudahkan pemeliharaan kode dalam jangka panjang. Sebaliknya, jika semuanya digabungkan dalam Model, kita akan membuat sistem yang sulit dikelola dan tidak fleksibel terhadap perubahan dan skala.

> Have you explored more about Postman? Tell us how this tool helps you to test your current
work. You might want to also list which features in Postman you are interested in or feel like it
is helpful to help your Group Project or any of your future software engineering projects.

Dengan Postman, saya bisa menguji endpoint API secara langsung, mengirim berbagai jenis permintaan HTTP, serta mengatur parameter dan header yang diperlukan. Postman memungkinkan pengujian otomatis dengan collections yang mengorganisir permintaan-permintaan API, serta memudahkan pengujian di berbagai environment. Dalam proyek tim, Postman sangat membantu dalam kolaborasi, memungkinkan berbagi collections dan dokumentasi API secara mudah. Fitur integrasi CI/CD juga memungkinkan pengujian otomatis selama proses build, memastikan stabilitas API. Dengan semua fitur ini, Postman sangat mendukung pengujian API, kolaborasi antar anggota tim, serta memudahkan pemeliharaan aplikasi dalam proyek pengembangan perangkat lunak.

#### Reflection Publisher-3
