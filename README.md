# Commit 1 Reflection notes

Saya membuat web server sederhana menggunakan `TcpListener` yang melakukan binding ke alamat `127.0.0.1:7878`. Server ini akan mendengarkan setiap koneksi yang masuk dari browser.
Selanjutnya, saya menambahkan fungsi `handle_connection` untuk menangani setiap koneksi yang diterima. Fungsi ini menerima parameter berupa `TcpStream`, yang merepresentasikan aliran data antara client (browser) dan server.
Di dalam fungsi `handle_connection`, saya menggunakan `BufReader` untuk membaca data dari stream secara efisien. Data yang dibaca kemudian diolah menggunakan iterator `.lines()` untuk memisahkan setiap baris dari HTTP request. Program akan terus membaca hingga menemukan baris kosong, yang menandakan akhir dari header HTTP.
Hasil pembacaan tersebut disimpan dalam bentuk vector `http_request`, yang berisi baris-baris request dari browser, seperti:
- Request line (contoh: `GET / HTTP/1.1`)
- Header seperti `Host`, `User-Agent`, dan lainnya

Kemudian, isi request tersebut ditampilkan ke terminal menggunakan `println!`. Dari sini terlihat bagaimana browser mengirimkan request ke server dalam bentuk teks berbasis protokol HTTP. Saat saya mengakses `http://127.0.0.1:7878`, browser mengirimkan request yang berisi berbagai informasi seperti jenis request (GET), path yang diminta (/), serta metadata tambahan. Setiap request ini berhasil ditangkap dan ditampilkan oleh server.


# Commit 2 Reflection notes

Milestone ini menyuruh untuk memodifikasi program agar server dapat mengirimkan response berupa halaman HTML ke browser. Saya membuat file `hello.html` yang berisi konten sederhana, kemudian membaca isi file tersebut menggunakan `fs::read_to_string`.
Selain itu, saya mulai memahami struktur HTTP response. Server harus mengirimkan response dalam format tertentu, yaitu terdiri dari status line (contoh: `HTTP/1.1 200 OK`), header (seperti `Content-Length`), dan body (isi HTML).
`Content-Length` digunakan untuk memberi tahu browser berapa panjang konten yang dikirim sehingga browser dapat membaca response dengan benar. Setelah semua bagian digabungkan, response dikirim menggunakan `stream.write_all()`.
Setelah perubahan ini, ketika mengakses server melalui browser, halaman HTML berhasil ditampilkan. Hal ini menunjukkan bahwa server sudah dapat memberikan response yang valid kepada client.

![Commit 2 screen capture](/assets/images/commit2.png)


# Commit 3 Reflection notes

Milestone ketiga menyuruh untuk menambahkan mekanisme validasi terhadap request yang diterima oleh server. Sebelumnya, server selalu mengembalikan file HTML yang sama tanpa memperhatikan request dari client. Pada tahap ini, server mulai membaca dan menganalisis request yang dikirim oleh browser.
Saya menggunakan `buf_reader.lines().next()` untuk mengambil hanya baris pertama dari HTTP request, yang disebut sebagai request line (contoh: `GET / HTTP/1.1`). Baris ini berisi informasi penting seperti method (GET), path (/), dan versi HTTP.
Selanjutnya, saya membandingkan nilai request line tersebut dengan string `"GET / HTTP/1.1"` menggunakan kondisi `if`. Jika request sesuai (yaitu user mengakses root `/`), maka server akan mengembalikan file `hello.html` dengan status `200 OK`. Namun, jika request tidak sesuai, maka server akan mengembalikan file `404.html` dengan status `404 NOT FOUND`.
Maka, server sudah dapat membedakan antara request yang valid dan tidak valid, serta memberikan response yang sesuai.
Selain itu, dilakukan refactoring untuk mengurangi duplikasi kode antara blok `if` dan `else`. Sebelumnya, kedua blok memiliki kode yang hampir sama untuk membaca file dan menulis response ke stream. Setelah refactoring, perbedaan hanya terletak pada `status_line` dan `filename`, yang disimpan dalam bentuk tuple. Nilai ini kemudian digunakan di luar blok kondisi untuk membangun response.
Refactoring ini membuat kode menjadi lebih ringkas, mudah dibaca, dan lebih mudah untuk dikembangkan nanti karena perubahan hanya perlu dilakukan di satu tempat.

![Commit 3 screen capture](/assets/images/commit3.png)
![Commit 3 bad screen capture](/assets/images/commit3bad.png)


# Commit 4 Reflection notes

Milestone keempat menyuruh untuk mensimulasikan condition di mana server membutuhkan durasi untuk memproses suatu request. Hal ini dilakukan dengan menambahkan endpoint `/sleep`, yang akan membuat server “tidur” selama beberapa detik menggunakan `thread::sleep(Duration::from_secs(10))` sebelum memberikan response.
Saya menggunakan `match` untuk menangani beberapa kemungkinan request, yaitu request ke root (`/`), request ke `/sleep`, dan request lainnya. `if-else` diubah ke `match` karena jumlah condition menjadi lebih dari dua sehingga `match` lebih rapi dan mudah dibaca.
Ketika saya membuka dua browser di mana satu mengakses `/sleep` dan satu lagi mengakses `/`, terlihat bahwa request kedua ikut tertunda hingga request `/sleep` selesai. Hal ini terjadi karena server masih berjalan secara single-threaded sehingga hanya dapat memproses satu request dalam satu waktu.
Jadi, jika terdapat request yang lambat, maka request lain harus menunggu, yang menyebabkan performa server menjadi buruk. Hal ini merupakan kelemahan dari single-threaded dalam menangani banyak client secara bersamaan.
