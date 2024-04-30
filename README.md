# advprog-modul9

# Mahoga Aribowo Heryasa

# 2206025230

### What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

- Unary RPC adalah bentuk komunikasi antara klien dan server dimana klien mengirimkan *request* tunggal ke server, dan menunggu server mengembalikan *response* tunggal. Unary paling cocok digunakan untuk skenario *request* dan *response* simple yang tidak memerlukan iteraksi tambahan dengan *request data*, seperti skenario autentikasi *user*.
- Server streaming adalah bentuk komunikasi antara klien dan server dimana klien mengirimkan *request* ke server, dan server memberikan serangkaian *responses* yang dijalankan secara berurutan. Server streaming paling cocok digunakan ketika server perlu mengirimkan data yang besar ke klien.
- Bi-directional adalah bentuk komunikasi antara klien dan server dimana klien dan server bisa mengirimkan serangkaian pesan secara bersaamaan. Bi-directional cocok digunakan ketika klien dan server perlu mengirimkan dan menerima data dalam *real time*, seperti *chat application*.

### What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Terkait *authentication*, klien dan server perlu dipastikan dapat memverifikasi identitas satu sama lain untuk mencegah akses yang tidak sah. Hal ini dapat dilakukan dengan menggunakan protokol seperti SSL/TLS *mutual authentication* atau OAuth2. terkait *authorization*, perlu ditetapkan  untuk setiap metode gRPC berdasarkan identitas klien dan peran yang terkait dan mengimplementasikan kontrol akses seperti Role Based Access Control (RBAC) atau Attribute-Based Access Control (ABAC) untuk menegakkan kebijakan akses. Terkait enkripsi data, enkripsi SSL/TLS dapat digunakn untuk mengamankan komunikasi antara klien dan server. Terapkan enkripsi untuk data sensitif pada saat penyimpanan dan transit.

### What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Beberapa masalah yang dapat timbul ketika menangani *bidirectional streaming* di *Rust gRPC* diantaranya adalah masalah *concurrency* yang bersangkutan dengan banyaknya klien yang dapat mengirim dan menerima pesan secara bersamaan sehingga perlu *managing* *respones* dari beragam klien secara baik, *error-handling* yang menjadi lebih kompleks untuk menangani proses *read* and *write* data, *performance* yang perlu dijaga untuk memastikan proses pengiriman dan penerimaan data berjalan dengan lancar, *backpressure and flow control* yang perlu dikelola untuk mencegah overload yang berasal dari klien dengan *processing speed* yang bervariasi, serta *connection managment* yang diperlukan untuk menangani pemutusan koneksi secara tiba-tiba dari klien yang bebas *connect* dan *disconnect* kapan pun.

### What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?

penggunaan `ReceiverStream` untuk *streaming responses* memiliki beberapa keuntungan diantaranya, mudah digunakan karena bisa untuk mengonversi Receiver menjadi Stream, memiliki kompatibilitas dengan trait Stream sehingga memungkinkan penggunaan function dan library lain dari rust yang bekerja dengan Stream, serta penanganan *Backpressure* yang ditangani secara default dengan mencegah *message overload*. Di sisi lain, `ReceiverStream` memiliki beberapa kekurangan diantaranya, *error handling* yang tidak memiliki *built in mechanism*, hanya mendukung komunikasi satu arah dari pengirim ke penerima, serta kontrol yanng terbatas akan bagaiaman dan kapan uderlying Receiver di *poll*. 

### In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Untuk meningkatkan *mantainability* dan *extensibility* dalam layanan Rust gRPC, penting untuk memisahkan *business logic* dari implementasi gRPC itu sendiri untuk memungkinkan penggunaan ulang kode dalam konteks yang berbeda tanpa terikat pada infrastruktur spesifik. Selain itu, menggunakan Protobuf untuk mendefinisikan layanan memperjelas kontrak layanan yang dapat diimplementasikan secara independen. Penggunaan Traits dalam Rust memungkinkan abstraksi yang kokoh antara layanan dan implementasi konkretnya, memisahkan *business logic* dari infrastruktur gRPC. Cara lainnya termasuk *code modularitazion*, penggunaan *dependency injection*, dan *error handling* yang solid.

### In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Untuk menangani logika pembayaran yang lebih kompleks dalam MyPaymentService, langkah-langkah tambahan dapat mencakup validasi data dari *payment request* seperti memastikan bahwa jumlah pembayaran yang diminta > 0 untuk memastikan kevalidan data yang diterima, interaksi dengan sistem eksternal seperti *payment gateway*, *error handling* seperti kesalahan server internal dalam proses pembayaran, keamanan data yang mencakup mengenkripsi data sensitif seperti nomor kartu kredit, *logging audit*, *unit testing*, dan *performance optimization*. Langkah-langkah ini membantu memperkuat logika pembayaran agar lebih aman dan efisien dalam menangani skenario yang lebih kompleks.

### What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Penggunaan gRPC sebagai protokol komunikasi berdampak signifikan pada arsitektur dan desain sistem terdistribusi. Persyaratan gRPC untuk kontrak layanan yang didefinisikan menggunakan Protocol Buffers memastikan komunikasi yang jelas dan konsisten antara layanan, yang menghasilkan arsitektur yang lebih kokoh. Selain itu, generasi kode otomatis gRPC menyederhanakan pengembangan dan meningkatkan interoperabilitas di berbagai bahasa pemrograman. Namun, keterbatasan browser dan kompleksitas dalam mendukung gRPC bisa menjadi hambatan, terutama jika perlu berinteraksi dengan sistem yang hanya mendukung REST.

### What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

- Keutungan
    - Multiplexing = memungkinkan pengiriman dan penerimaan beberapa *request* dan *responses* secara bersamaan melalui satu koneksi
    - Header compression = HTTP/2 mengurangi *overhead* dengan mengompresi data header. Hal ini menghasilkan penggunaan bandwidth yang lebih rendah dan transmisi data yang lebih cepat
    - Server push = fungsi yang memungkinkan server untuk mendorong sumber daya ke klien sebelum diminta. Ini mengurangi waktu putar dan memungkinkan penggunaan sumber daya yang lebih efisien, meningkatkan kinerja untuk aplikasi web.
- Kekurangan
    - Kompleksitas = Mengimplementasikan HTTP/2 bisa lebih kompleks dibandingkan dengan HTTP/1.1, memerlukan konfigurasi dan optimisasi tambahan untuk sepenuhnya memanfaatkan fitur-fiturnya.
    - Kompatibilitas = HTTP/2 mungkin tidak didukung oleh semua klien dan server, memerlukan pertimbangan kompatibilitas saat mengintegrasikannya dengan sistem atau platform yang sudah ada.
    - Konsumsi sumber daya =  Meskipun HTTP/2 mengurangi latensi dan meningkatkan efisiensi, ia dapat mengonsumsi lebih banyak sumber daya server dibandingkan dengan HTTP/1.1 dalam beberapa kondisi. Multiplexing dan kompresi header dapat meningkatkan penggunaan CPU dan memori, terutama dalam skenario beban tinggi.

### How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?


Model *request-response* dari API REST berbeda dengan kemampuan *bidirectional streaming* dari gRPC dalam hal *real-time communication* dan *responsiveness*. Dalam API REST, komunikasi mengikuti pola *request-response* yang sinkron, di mana klien mengirim permintaan ke server, menunggu server memprosesnya, dan kemudian menerima satu respons. Model ini cocok untuk interaksi sederhana dan skenario di mana pembaruan *real time* tidak penting. Namun, ini dapat menyebabkan latensi dan ketidak-efisienan dalam kasus yang memerlukan pembaruan yang sering atau aliran data kontinu. Di sisi lain, gRPC menggunakan *bidirectional streaming* yang memungkinkan klien dan server untuk mengirimkan banyak pesan secara asinkron melalui satu koneksi. Hal Ini menjadikan gRPC ideal untuk aplikasi yang membutuhkan pertukaran data kontinu dengan cepat.

### What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Pendekatan gRPC menggunakan Protocol Buffers menawarkan keuntungan dalam penegakan tipe yang kuat, efisiensi data yang tinggi, kemampuan evolusi skema, dan pengkodean otomatis untuk menghasilkan kode klien dan server. Ini memastikan bahwa struktur dan jenis data pesan telah ditentukan sebelumnya oleh skema, mengurangi risiko kesalahan saat runtime dan memungkinkan kompatibilitas mundur dan maju tanpa gangguan. Namun, implementasi Protocol Buffers memerlukan penyiapan tambahan dan pemeliharaan skema, serta mungkin memiliki kurva pembelajaran yang lebih tinggi. Di sisi lain, JSON dalam API REST memberikan fleksibilitas yang lebih besar dan dukungan universal yang luas, memfasilitasi integrasi dengan sistem yang sudah ada dan layanan pihak ketiga. Meskipun tidak seefisien Protocol Buffers, JSON memberikan keterbacaan yang lebih mudah dan lebih mudah diadopsi. Pilihan antara keduanya tergantung pada kebutuhan spesifik proyek dan preferensi pengembang dalam hal efisiensi, keamanan tipe data, dan interoperabilita