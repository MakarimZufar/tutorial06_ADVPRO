# Refleksi

Nama: Makarim Zufar Prambudyo

NPM: 2306241751

Kelas: pemrograman Lanjut B

---

## Commit 1:Handle-Connection - Check Response

Pada tahap ini, kita telah membuat fungsi handle_connection yang berfungsi untuk menangani koneksi yang masuk dari browser. Fungsi ini menerima objek TcpStream yang diperoleh dari TcpListener. Dengan menggunakan BufReader, fungsi ini membaca request HTTP secara bertahap per baris hingga ditemukan baris kosong, yang menunjukkan bahwa header request telah selesai. Setelah proses pembacaan selesai, request tersebut disimpan dalam sebuah vektor yang berisi kumpulan string per baris dan ditampilkan di konsol. Jadi, meskipun aplikasi ini sudah berhasil menerima dan menampilkan request dari browser, respons kepada browser belum dapat diberikan.

---

## Commit 2: Returning HTML”, push it to your git repository server

Kami menambahkan variabel status_line untuk memberi tahu browser bahwa request berhasil diproses dengan kode status 200 OK. Kemudian, menggunakan fs::read_to_string(), saya membaca isi dari file HTML yang akan dikirim sebagai respons, sehingga halaman dapat ditampilkan di browser. Selain itu, saya juga menambahkan header Content-Length untuk memberi tahu browser berapa banyak data yang akan diterima, agar proses rendering berjalan dengan lancar. Terakhir, dengan menggunakan stream.write_all(response.as_bytes()).unwrap();, saya mengirimkan respons dalam bentuk byte ke browser, memastikan halaman HTML dapat diterima dan ditampilkan dengan benar. Dengan cara ini, server kami sekarang bisa menangani request dan mengirimkan file hello.html sebagai respons.
