# Reflection

## 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?
Berdasarkan pemahaman saya, Unary RPC adalah metode paling sederhana di mana client mengirim satu request dan menerima satu response, sehingga ini cocok untuk operasi sederhana seperti autentikasi atau pembayaran. Server streaming memungkinkan client mengirim satu request namun menerima banyak response secara bertahap, cocok untuk kasus seperti mengambil riwayat transaksi dalam jumlah besar. Bidirectional streaming memungkinkan client dan server saling bertukar pesan secara bebas dan bersamaan, cocok untuk kasus real-time seperti aplikasi chat atau live monitoring.


## 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?
Ada beberapa pertimbangan keamanan penting dalam pengimplementasian gRPC di Rust, antara lain penggunaan TLS untuk mengenkripsi data antar client dan server, implementasi autentikasi menggunakan token seperti JWT pada metadata setiap request, serta otorisasi untuk memastikan hanya pengguna berwenang yang dapat mengakses endpoint tertentu. Validasi input juga perlu dilakukan untuk mencegah serangan seperti injection, dan penggunaan interceptor di tonic dapat membantu menerapkan logika keamanan secara terpusat.


## 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?
Beberapa  tantangan yang mungkin muncul antara lain manajemen konkurensi yang kompleks karena dua stream berjalan bersamaan, penanganan error yang lebih rumit karena salah satu stream bisa gagal tanpa mempengaruhi yang lain, potensi deadlock jika channel buffer penuh, serta pengelolaan koneksi yang terputus tiba-tiba. Di Rust khususnya, ownership dan lifetime bisa menjadi tantangan tambahan saat berbagi state antara async task.



## 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?
Keuntungan menggunakan ReceiverStream adalah integrasinya yang mulus dengan ekosistem Tokio dan kemudahan konversi dari MPSC channel menjadi stream yang dapat digunakan tonic. Pendekatan ini juga mendukung backpressure secara alami melalui buffer channel. Namun kekurangannya adalah buffer yang terbatas dapat menyebabkan produsen ter-block jika konsumen tidak cukup cepat membaca, dan error handling menjadi kurang ekspresif karena dibungkus dalam Result di dalam channel.


## 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity,promoting maintainability and extensibility over time?
Kode gRPC di Rust dapat distrukturkan dengan memisahkan setiap service ke dalam modul atau file tersendiri, menggunakan trait untuk mendefinisikan kontrak antar komponen, dan membuat shared utility untuk operasi umum seperti logging dan error handling. Penggunaan dependency injection dan konfigurasi eksternal juga dapat memudahkan pengujian dan penggantian implementasi tanpa mengubah kode yang sudah ada.


## 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?
Untuk logika pembayaran yang lebih kompleks diperlukan beberapa langkah tambahan seperti validasi input untuk memastikan jumlah pembayaran valid dan user ID terdaftar, integrasi dengan database untuk menyimpan rekaman transaksi, koneksi ke payment gateway eksternal, implementasi mekanisme retry untuk menangani kegagalan jaringan, serta penambahan logging dan monitoring untuk audit trail pembayaran.


## 7 . What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?
Adopsi gRPC mendorong desain sistem yang lebih terstruktur melalui contract-first development menggunakan Protocol Buffers yang memastikan konsistensi interface antar service. Namun gRPC memiliki keterbatasan dukungan di browser sehingga sistem yang membutuhkan akses langsung dari web perlu lapisan tambahan seperti gRPC-Web atau REST gateway. Di sisi lain, dukungan multi-bahasa yang kuat memudahkan integrasi antara service yang ditulis dalam bahasa pemrograman berbeda.

## 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?
HTTP/2 menawarkan keunggulan signifikan dibanding HTTP/1.1 yaitu multiplexing yang memungkinkan banyak request berjalan dalam satu koneksi TCP, header compression dengan HPack yang mengurangi overhead, serta server push. Dibanding WebSocket, HTTP/2 lebih terstruktur dan memiliki dukungan load balancing yang lebih baik. Namun HTTP/2 lebih kompleks untuk di-debug dan tidak semua infrastruktur lama mendukungnya dengan baik.

## 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?
REST API menggunakan model request-response yang stateless di mana setiap komunikasi membutuhkan request baru dari client, sehingga kurang efisien untuk komunikasi real-time dan memerlukan teknik seperti polling. Sebaliknya bidirectional streaming gRPC mempertahankan koneksi persisten yang memungkinkan client dan server saling mengirim pesan kapan saja tanpa membuat koneksi baru, sehingga jauh lebih responsif untuk skenario real-time seperti chat atau live data feed.

## 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?
Pendekatan schema-based dengan Protocol Buffers memberikan keuntungan berupa validasi tipe data yang ketat secara otomatis, ukuran payload yang lebih kecil karena format binary, dan kemudahan generate kode client/server secara otomatis. Namun kelemahannya adalah kurang fleksibel karena setiap perubahan struktur data memerlukan update file proto dan regenerasi kode. JSON di REST API lebih fleksibel dan mudah dibaca manusia, namun tidak memiliki validasi tipe bawaan dan ukuran payload lebih besar.