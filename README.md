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

## Commit 4: Simulation of slow request

```rust
let (status_line, filename) = match &request_line[..] { 
    "GET / HTTP/1.1" => ("HTTP/1.1 200 OK", "hello.html"), 
    "GET /sleep HTTP/1.1" => { 
        thread::sleep(Duration::from_secs(10)); 
        ("HTTP/1.1 200 OK", "hello.html") 
    } 
    _ => ("HTTP/1.1 404 NOT FOUND", "404.html"), 
}; 
```
Dari kode yang kita tambahkan di atas kita bisa mensimulasikan kondisi di mana ketika pengguna mengakses endpoint /sleep, mereka harus menunggu selama 10 detik. Tentu saja, ini akan mempengaruhi keterlambatan dalam menangani request lainnya pada waktu yang bersamaan. Hal ini terjadi karena server kita masih berjalan di dalam satu thread, yang artinya hanya bisa menangani satu request pada satu waktu. Jika banyak pengguna mengakses server secara bersamaan, mereka harus menunggu giliran mereka, yang menyebabkan server terlihat lebih lambat dan kurang responsif. Menurut saya, ini menunjukkan kekurangan dari server yang berjalan di satu thread.

## commit 5: Multithreaded server using Threadpool 

```rust
fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").unwrap();
    let pool = ThreadPool::new(4);

    for stream in listener.incoming() {
        let stream = stream.unwrap();

        pool.execute(|| {
            handle_connection(stream);
        });
    }
}
```

Untuk mengatasi masalah yang terjadi pada commit ke-4, kita dapat mengimplementasikan ThreadPool untuk mengubah server menjadi server multithreaded, yang memungkinkan server untuk menangani beberapa request secara bersamaan. Dengan menggunakan ThreadPool, kita dapat membagi tugas ke beberapa worker yang berjalan secara paralel, sehingga setiap request tidak perlu menunggu request lain selesai terlebih dahulu. Setiap worker dalam ThreadPool akan mengambil tugas dari antrian dan menjalankannya secara terpisah, menjadikan server lebih responsif. Dengan pendekatan ini, jika ada request yang membutuhkan waktu lama untuk diproses, worker lain yang kosong masih dapat menangani request baru tanpa menunggu. Selain itu, penggunaan Arc dan Mutex memastikan bahwa hanya satu worker yang dapat mengakses tugas pada satu waktu, menghindari adanya konflik antar worker. Dengan cara ini, kita dapat menyelesaikan masalah yang muncul pada server yang berjalan dalam satu thread.

```rust
struct Worker {
    id: usize,
    thread: thread::JoinHandle<()>,
}

impl Worker {
    fn new(id: usize, receiver: Arc<Mutex<mpsc::Receiver<Job>>>) -> Worker {
        let thread = thread::spawn(move || loop {
            let job = receiver.lock().unwrap().recv().unwrap();

            println!("Worker {id} got a job; executing.");

            job();
        });

        Worker { id, thread }
    }
}
```

Instance dari ThreadPool akan dibuat di dalam fungsi main. Setelah itu, setiap request akan dimasukkan ke dalam antrian, dan worker yang sedang tidak aktif dalam ThreadPool akan mengambil dan memproses request tersebut tanpa harus menunggu request lain selesai.