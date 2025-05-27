---
share_link: https://share.note.sx/032zur94#Cx5CwAJdFowRLdDaiwMwCRHrR3/awAR9dBjv0FKt7rA
share_updated: 2025-05-01T12:52:33+07:00
---
---
Selamat datang di kelas praktikum **Manajemen Basis Data**. Pada pertemuan ke 2 ini, kita akan melakukan praktikum mengenai konsep-konsep penting dalam manajemen basis data, termasuk **Indexing: Ordered, B+- Tree, Hash, Bitmap, Dense Index, Sparse Index, dan Query Processing**.

## Indexing

### 1️. Ordered Index

- **Definisi**: Teknik pengindeksan yang mengurutkan data berdasarkan nilai kunci untuk mempercepat pencarian.
- **Kegunaan**: Mempercepat pencarian data dengan memanfaatkan urutan nilai kunci.
- **Keuntungan**: Meningkatkan kecepatan pencarian dibandingkan pemindaian seluruh tabel.
- **Kekurangan**: Perlu pembaruan indeks setiap kali data berubah, yang dapat menambah overhead.

### 2️. B+- Tree

- **Definisi**: Struktur indeks berbasis pohon yang menyimpan data secara terurut dan memungkinkan pencarian yang efisien.
- **Kegunaan**: Digunakan dalam database besar untuk mempercepat pencarian, penyisipan, dan penghapusan data.
- **Keuntungan**: Pencarian dan akses data lebih cepat dengan waktu pencarian logaritmik.
- **Kekurangan**: Memerlukan pemeliharaan struktur pohon saat terjadi perubahan data.

###  3. Hash Index

- **Definisi**: Teknik pengindeksan yang menggunakan fungsi hash untuk langsung menemukan lokasi data.
- **Kegunaan**: Cocok untuk pencarian berdasarkan nilai unik atau lookup cepat.
- **Keuntungan**: Waktu akses sangat cepat untuk pencarian berdasarkan nilai kunci.
- **Kekurangan**: Tidak mendukung pencarian rentang (range queries) dan rentan terhadap **collision** (tabrakan hash).

### 4️. Bitmap Index

- **Definisi**: Teknik indeks yang menggunakan representasi biner untuk data dengan sedikit nilai unik.
- **Kegunaan**: Digunakan pada atribut dengan sedikit variasi nilai, seperti status aktif/nonaktif atau jenis kelamin.
- **Keuntungan**: Mengurangi ukuran indeks dan mempercepat eksekusi query dengan operasi bitwise.
- **Kekurangan**: Tidak efektif untuk data dengan nilai unik yang banyak.

----

## Dense Index & Sparse Index

### 1️. Dense Index

- **Definisi**: Teknik indeks yang menyimpan entri indeks untuk setiap record dalam tabel.
- **Kegunaan**: Mempercepat pencarian karena setiap record memiliki referensi indeks.
- **Keuntungan**: Pencarian lebih cepat dibandingkan **Sparse Index**.
- **Kekurangan**: Menggunakan lebih banyak ruang penyimpanan.

### 2️. Sparse Index

- **Definisi**: Teknik indeks yang hanya menyimpan sebagian entri, biasanya satu entri per blok data.
- **Kegunaan**: Menghemat ruang penyimpanan dengan tetap memberikan akses cepat ke data.
- **Keuntungan**: Menggunakan lebih sedikit ruang dibandingkan **Dense Index**.
- **Kekurangan**: Pencarian bisa sedikit lebih lambat karena perlu pemindaian tambahan.

---

## Query Processing

- **Definisi**: Proses bagaimana database mengeksekusi query, dari parsing hingga pengambilan hasil.
- **Kegunaan**: Memahami bagaimana query diproses membantu dalam optimasi performa database.
- **Keuntungan**: Dapat mengidentifikasi bottleneck dalam eksekusi query dan mengoptimalkan strategi pencarian data.
- **Kekurangan**: Memerlukan pemahaman mendalam tentang mesin database untuk optimasi yang efektif

---
### **Tugas Praktikum**

**Data Tabel Praktikum 2 :** [Ambil di sini](https://drive.google.com/drive/folders/1rym4qWM7-0HfHQAIR0RsTq-4LUyIBaaf?usp=drive_link)

- Data produk terdiri dari 25.000 record.
- Data supplier terdiri dari 25.000 record.

---
### **Langkah Praktikum**

#### **1. Persiapan**

- Setiap anggota kelompok menyiapkan koneksi ke server MySQL.
- Membuat schema baru dengan nama:
```
Prak_MBD_2
```    
- Jalankan query berikut untuk membuat schema baru:

```sql
CREATE DATABASE IF NOT EXISTS Prak_MBD_2;
USE Prak_MBD_2;
```

---

#### **2. Menyiapkan Tabel**

- Setelah schema dibuat, selanjutnya buat tabel untuk menyimpan data produk dan supplier.
- Tabel Produk (berisi 25.000 data produk langka).
- Tabel Supplier (berisi 25.000 data supplier unik).

###### 1. Tabel `supplier`

```sql
CREATE TABLE supplier (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nama_supplier VARCHAR(255) NOT NULL,
    kontak VARCHAR(15) NOT NULL,
    alamat TEXT NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    tanggal_bergabung DATE NOT NULL
);
```

###### 2. Tabel `produk`

```sql
CREATE TABLE produk (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nama_produk VARCHAR(255) NOT NULL,
    kategori VARCHAR(150) NOT NULL,
    harga INT NOT NULL,
    stok INT NOT NULL,
    deskripsi TEXT,
    tanggal_ditambahkan DATE NOT NULL,
    id_supplier INT NOT NULL,
    FOREIGN KEY (id_supplier) REFERENCES supplier(id) ON DELETE CASCADE
);
```

- Setelah kedua tabel dibuat, masukkan record ke dalam tabel. Buka data yang telah didownload di dalam MySQL lalu jalankan query.
    
    ![](https://share.note.sx/files/08/083i4z0mlatkc3nt7roq.png)

---
### **4. Langkah-Langkah Praktikum**
### **Indexing ( Tanpa Join)** 
##### A. Ordered Indexing
* Tanpa indexing
```sql
SELECT * FROM produk WHERE harga > 10000000;
```
* Menggunakan ordered indexing
```sql
CREATE INDEX idx_harga ON produk(harga);
SELECT * FROM produk WHERE harga > 10000000;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
  
---
##### B.  B+ Tree Indexing
* Menggunakan B+ Tree  indexing
```sql
CREATE INDEX idx_harga_btree ON produk(harga) USING BTREE;
SELECT * 
FROM produk WHERE harga > 100000;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
---
##### C.  Hash Indexing
* Menggunakan Hash indexing
```sql
CREATE INDEX idx_supplier_hash ON produk(id_supplier) USING HASH;
SELECT * FROM produk WHERE id_supplier > 10000 ORDER BY harga DESC LIMIT 10000;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**. 
---
#####  D.  Bitmap Indexing
* Menggunakan Bitmap  indexing
```sql
CREATE BITMAP INDEX idx_kategori ON produk(kategori);
SELECT * 
FROM produk WHERE kategori = 'Peralatan Astronomi';
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**. 
---

### **Indexing ( Dengan Join)** 
##### A. Ordered Indexing
* Tanpa ordered indexing
```sql
SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id;
```
* Menggunakan ordered indexing
```sql
CREATE INDEX idx_produk_supplier ON produk(id_supplier);
CREATE INDEX idx_supplier_id ON supplier(id);
SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
  
---
##### B.  B+ Tree Indexing
* Menggunakan B+ Tree  indexing
```sql
CREATE INDEX idx_produk_supplier_btree ON produk(id_supplier) USING BTREE;
CREATE INDEX idx_supplier_id_btree ON supplier(id) USING BTREE;
SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
---
##### C.  Hash Indexing
* Menggunakan Hash indexing
```sql
CREATE INDEX idx_produk_supplier_hash ON produk(id_supplier) USING HASH;
CREATE INDEX idx_supplier_id_hash ON supplier(id) USING HASH;
SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
---
 ##### D.  Bitmap Indexing
* Menggunakan Bitmap  indexing
```sql
CREATE BITMAP INDEX idx_kategori_bitmap ON produk(kategori);
SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id
WHERE produk.kategori = 'Peralatan Astronomi';
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**. 
---

##### E.  Dense Indexing
* Menggunakan Dense  indexing
```sql
CREATE INDEX idx_produk_supplier_dense ON produk(id_supplier);
SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
---
##### F.  Sparse Indexing
* Menggunakan Sparse  indexing
```sql
CREATE INDEX idx_produk_supplier_sparse ON produk(id_supplier);
SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
---
##### G.  Query Processing
* Menggunakan Query Processing
```sql
EXPLAIN ANALYZE SELECT produk.nama_produk, supplier.nama_supplier
FROM produk
JOIN supplier ON produk.id_supplier = supplier.id;
```
* Lakukan pencatatan waktu eksekusi atau (pemerosesan) ke dalam tabel yang telah **disediakan**.
---
### **5. Dokumentasi & Perbandingan**

- Dokumentasikan waktu eksekusi dari setiap query yang dijalankan.
- Buat tabel perbandingan hasil eksekusi.

Template Tabel Waktu MBD : [Klik](https://docs.google.com/spreadsheets/d/1N9-rEsu86N4u9VXjhg9hMgeA_ejtcIEOSKWBGtaMVys/edit?usp=sharing)

---
## **Pertanyaaan & Tugas Praktikum**

* Lakukan percobaan dengan menggunakan data yang berjumlah 50.000 record, data 50.000 record dapat diambil [di sini](https://drive.google.com/drive/folders/16UtosgU6HHo6JAdzJzjSELKw0Rm_SuP4?usp=sharing).
	- Analisis apakah MySQL masih dapat menangani eksekusi query dengan jumlah record yang lebih besar.
	- Jelaskan faktor-faktor yang memengaruhi performa query saat jumlah record ditambah.
	- Apa kendala yang mungkin muncul ketika jumlah record semakin besar?

* Jelaskan apa yang dimaksud dengan indexing!
* Kapan kita harus menggunakan indexing ( ordered, bitmap, B+, dll)?
* Mana yang lebih optimal diantara indexing yang kamu lakukan?
* Bagaimana query processing mampu mengoptimalkan query?
* Berikan saran query optimization dari query yang telah dilakukan saat praktikum!



