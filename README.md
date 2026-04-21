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