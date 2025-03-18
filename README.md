# Refleksi

Nama: Makarim Zufar Prambudyo

NPM: 2306241751

Kelas: pemrograman Lanjut B

---

## Commit 1:Handle-Connection - Check Response

Pada tahap ini, kita telah membuat fungsi handle_connection yang berfungsi untuk menangani koneksi yang masuk dari browser. Fungsi ini menerima objek TcpStream yang diperoleh dari TcpListener. Dengan menggunakan BufReader, fungsi ini membaca request HTTP secara bertahap per baris hingga ditemukan baris kosong, yang menunjukkan bahwa header request telah selesai. Setelah proses pembacaan selesai, request tersebut disimpan dalam sebuah vektor yang berisi kumpulan string per baris dan ditampilkan di konsol. Jadi, meskipun aplikasi ini sudah berhasil menerima dan menampilkan request dari browser, respons kepada browser belum dapat diberikan.

---

## Commit 2: Returning HTML”, push it to your git repository server

```rust
let status_line = "HTTP/1.1 200 OK";
let contents = fs::read_to_string("hello.html").unwrap();
let length = contents.len();
let response = format!("{status_line}\r\nContent-Length:{length}\r\n\r\n{contents}");
stream.write_all(response.as_bytes()).unwrap();
```

pada kode diatas saya menambahkan variabel `status_line` untuk memberi tahu browser bahwa request berhasil diproses dengan kode status 200 OK. Kemudian, menggunakan `fs::read_to_string()`, saya membaca isi dari file HTML yang akan dikirim sebagai respons, sehingga halaman dapat ditampilkan di browser. Selain itu, saya juga menambahkan header Content-Length untuk memberi tahu browser berapa banyak data yang akan diterima, agar proses rendering berjalan dengan lancar. Terakhir, dengan menggunakan stream.`write_all(response.as_bytes()).unwrap();`, saya mengirimkan respons dalam bentuk byte ke browser, memastikan halaman HTML dapat diterima dan ditampilkan dengan benar. Dengan cara ini, server kami sekarang bisa menangani request dan mengirimkan file `hello.html` sebagai respons.
![hello-html-screenshot](https://github.com/MakarimZufar/tutorial06_ADVPRO/blob/master/README-image/hello.png)

---

## commit 3: Validating request and selectively responding

```rust
let (status_line, filename) = 
    if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };
```
Pada tahap ini, server kita sudah dapat membedakan antara request yang valid dan yang tidak. Ketika pengguna mengakses "/", server akan mengirimkan file `hello.html` dengan status 200 OK. Namun, jika pengguna mengakses path yang tidak dikenali, server akan mengembalikan file `404.htm`l dengan status 404 NOT FOUND. Untuk mencapai hal ini, kita menambahkan kondisi if-else yang berfungsi untuk memverifikasi apakah path yang diminta valid atau tidak. Jika path valid, server akan mengirimkan file `hello.html` sebagai respons, sedangkan jika tidak valid, server akan mengirimkan file `404.html`. Selain itu, kita juga telah melakukan refactor pada kode, karena versi sebelumnya masih mengandung redundansi pada bagian if-else.

![404_html_screenshot](https://github.com/MakarimZufar/tutorial06_ADVPRO/blob/master/README-image/404.png)