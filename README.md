# Refleksi

Nama: Makarim Zufar Prambudyo

NPM: 2306241751

Kelas: pemrograman Lanjut B

---

## Commit 1:Handle-Connection - Check Response

Pada tahap ini, kita telah membuat fungsi handle_connection yang berfungsi untuk menangani koneksi yang masuk dari browser. Fungsi ini menerima objek TcpStream yang diperoleh dari TcpListener. Dengan menggunakan BufReader, fungsi ini membaca request HTTP secara bertahap per baris hingga ditemukan baris kosong, yang menunjukkan bahwa header request telah selesai. Setelah proses pembacaan selesai, request tersebut disimpan dalam sebuah vektor yang berisi kumpulan string per baris dan ditampilkan di konsol. Jadi, meskipun aplikasi ini sudah berhasil menerima dan menampilkan request dari browser, respons kepada browser belum dapat diberikan.
