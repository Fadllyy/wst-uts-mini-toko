# Laporan UTS Web Software Tools

**Nama:** Muhammad Fadly Fahrozzy
**Nomor Induk Mahasiswa:** 12523005
**Kelas:** TI B 
**Tanggal:** 13 November 2026

## A. Bukti pemeriksaan masalah
| Masalah | Alat yang digunakan | Bukti pemeriksaan | Perkiraan penyebab | Perbaikan | Hasil setelah perbaikan |
|---|---|---|---|---|---|
| Produk | Browser DevTools (Console & Network) | Muncul error `Failed to load resource: the server responded with a status of 404 (Not Found)` dan `Gagal mengambil daftar produk: Error: Permintaan gagal dengan status 404 at loadProducts (app.js:49:29)`. | Ketidaksesuaian penulisan huruf kapital (*case-sensitive*) pada URL API; `public/app.js` memanggil `fetch("/api/Products")`, sedangkan rute di `server.mjs` adalah `/api/products`. | Mengubah URL pemanggilan API pada fungsi `loadProducts()` di `public/app.js` dari `"/api/Products"` menjadi `"/api/products"`. | Halaman berhasil memuat dan menampilkan seluruh 4 produk (Mouse Nirkabel, Papan Ketik, Lampu Meja, Dudukan Laptop) dengan status `4 produk tersedia`. |
| Tombol keranjang | Browser DevTools (Elements & Console) | Saat tombol `+ Tambah ke Keranjang` pada kartu produk diklik, panel keranjang di sisi kanan tidak berubah dan tetap menampilkan `0 barang` (`Belum ada barang.`). | Event listener klik pada elemen tombol `.add-button` di `public/app.js` belum terhubung/terpicu dengan benar ke fungsi `addProduct(cart, product)` dan pembaruan tampilan `renderCart()`. | Memastikan `addEventListener("click", ...)` dipasang pada seluruh `.add-button` di `renderProducts()` (`public/app.js`) untuk mencari produk berdasarkan `Number(button.dataset.id)`, lalu memanggil `addProduct(cart, product)` dan `renderCart()`. | Mengklik tombol `+ Tambah ke Keranjang` langsung menambahkan produk ke daftar keranjang, menambah jumlah barang, dan mengakumulasi kuantitas jika produk yang sama diklik kembali. |
| Total belanja | Terminal (`npm run test:once`) | Pengujian otomatis gagal pada 2 kasus uji: `75000 !== 150000` (satu jenis barang dengan jumlah dua) dan `225000 !== 600000` (dua jenis barang dengan jumlah berbeda). | Fungsi `calculateTotal(cart)` pada `src/cart.js` hanya menjumlahkan harga satuan barang tanpa mengalikannya dengan kuantitas (`item.quantity`). | Memperbaiki rumus perhitungan pada `calculateTotal(cart)` di `src/cart.js` menjadi `cart.reduce((total, item) => total + item.price * item.quantity, 0)` serta menghapus variabel `var discount = 0;` yang tidak terpakai. | Perhitungan total belanja di keranjang maupun pengujian otomatis `npm run test:once` sesuai dengan hasil perkalian harga dan jumlah seluruh barang. |

## B. Hasil pengujian
- Jumlah pengujian sebelum perbaikan: 7 pengujian dijalankan — 5 berhasil (pass), 2 gagal (fail).
- Jumlah pengujian berhasil setelah perbaikan: 7 pengujian dijalankan — 7 berhasil (pass), 0 gagal (fail).
- Catatan perubahan pada fungsi yang diperiksa: Mengubah kalkulasi pada fungsi `calculateTotal(cart)` di berkas `src/cart.js` agar mengalikan `item.price * item.quantity` untuk setiap item di dalam keranjang, tanpa mengubah berkas pengujian di dalam folder `tests/`.

## C. Kualitas kode
- Temuan sebelum perbaikan: Pemeriksaan menggunakan `npm run format:check` dan `npm run quality:check` (Biome) menemukan 2 kesalahan ketidaksesuaian format penulisan (indentasi, pemecahan baris panjang, serta tanda kurung/spasi berlebih) pada `public/app.js` dan `src/cart.js`, serta variabel tidak terpakai (`var discount = 0`) pada `src/cart.js`.
- Perbaikan yang dilakukan: Menghapus variabel `var discount = 0` yang tidak terpakai pada `src/cart.js` dan merapikan format penulisan kedua berkas secara otomatis menggunakan perintah `npx biome format --write public/app.js src/cart.js`.
- Hasil pemeriksaan ulang: Menjalankan ulang `npm run quality:check` (`biome check public/app.js src/cart.js`) menghasilkan status bersih tanpa error (`Checked 2 files in 9ms. No fixes applied.`).

## D. Git
- Branch (jika membuat): `main` (terhubung ke remote repository `https://github.com/Fadllyy/wst-uts-mini-toko.git`)
- Ringkasan commit:
  - `c99208c chore: initial exam` (merekam kondisi awal project sebelum perubahan)
  - `a58e9a4 fix: repair shop behavior` (merekam perbaikan 3 masalah utama pada `public/app.js` dan `src/cart.js` serta perapian kode dengan Biome)
- Hasil pemeriksaan `git status`: Working tree clean (`On branch main`, `Your branch is up to date with 'origin/main'`, `nothing to commit, working tree clean`), tidak ada perubahan pada berkas di dalam folder `tests/`.

## E. Pemakaian kecerdasan buatan (jika digunakan)
- Nama alat: Gemini
- Pertanyaan/instruksi yang diberikan: Meminta bantuan analisis pesan error terminal (`npm`, `git`, hasil `npm run test:once`, dan Biome formatter), pemeriksaan penyebab error 404 pada `public/app.js` serta logika kalkulasi pada `src/cart.js`, dan panduan penggunaan perintah Git serta format pengisian laporan ujian.
- Bagian jawaban yang diterapkan: Penyesuaian URL endpoint `"/api/products"` pada `public/app.js`, pembersihan fungsi `calculateTotal(cart)` pada `src/cart.js`, serta penggunaan perintah `npx biome format --write` untuk merapikan format kode.
- Cara memeriksa apakah saran tersebut benar: Memverifikasi langsung secara fungsional di browser (`http://localhost:5057`) bahwa 4 produk tampil, tombol keranjang berfungsi, dan total belanja akurat; menjalankan pengujian otomatis `npm run test:once` hingga 7/7 lulus; serta menjalankan `npm run quality:check` hingga lulus tanpa error.

## F. Refleksi singkat
- Satu pelajaran paling penting dari ujian ini: Proses debugging yang sistematis dimulai dengan membaca bukti nyata dari DevTools (Console/Network) dan pengujian otomatis sebelum mengubah kode, sehingga akar masalah dapat ditemukan secara akurat tanpa menebak-nebak dan setiap perubahannya tercatat rapi melalui Git.