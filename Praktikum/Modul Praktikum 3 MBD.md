---
share_link: https://share.note.sx/o8qrzfos#9VYO+DrzZ0ZSIdO6Ghgy5byx9ObMqXIb7ZmQqg3vR2Q
share_updated: 2025-05-07T22:38:53+07:00
---
---
Selamat datang di kelas praktikum **Manajemen Basis Data**. Pada pertemuan ke 2 ini, kita akan melakukan praktikum mengenai konsep-konsep penting dalam manajemen basis data, yaitu querry processing dan integrity constraint

---
# **Querry Processing**

_Query Processing_ adalah proses penerjemahan dan eksekusi perintah SQL oleh sistem manajemen basis data (DBMS) untuk menghasilkan hasil yang diinginkan pengguna. Proses ini melibatkan:

- **Parsing**: Memeriksa sintaks SQL yang ditulis.
- **Optimization**: Menyusun rencana eksekusi (_query plan_) paling efisien, misalnya memilih penggunaan index.
- **Execution**: Menjalankan perintah dan mengembalikan hasil.

Tujuan utama dari _query processing_ adalah **meningkatkan performa** sistem basis data saat menangani permintaan data, terutama saat bekerja dengan jumlah data yang besar atau query kompleks (misalnya join, filter, sorting).

Contoh penting dari _query processing_ meliputi:

- Penggunaan index untuk mempercepat pencarian.
- Penggunaan _EXPLAIN_ untuk menganalisis rencana eksekusi.
- Optimisasi struktur query agar efisien.
# **Integrity Constraint**

_Integrity Constraints_ adalah aturan-aturan yang diterapkan pada struktur tabel di basis data untuk memastikan **keakuratan, konsistensi, dan validitas data**. Constraints mencegah masuknya data yang salah atau tidak logis ke dalam sistem.

Beberapa jenis integrity constraint yang umum digunakan:

- **NOT NULL**: Menyatakan bahwa kolom tidak boleh bernilai kosong.
- **UNIQUE**: Menjamin bahwa nilai dalam kolom tersebut tidak boleh duplikat.
- **PRIMARY KEY**: Kombinasi NOT NULL dan UNIQUE yang menjadi identitas utama baris data.
- **FOREIGN KEY**: Menghubungkan dua tabel dan menjaga _referential integrity_ antar tabel.
- **CHECK**: Membatasi nilai yang dapat dimasukkan berdasarkan ekspresi logika tertentu.
- **TRIGGER**: Perintah otomatis yang dijalankan saat data diubah untuk memastikan aturan tertentu tetap dipatuhi.

Dengan constraints ini, DBMS dapat menolak data yang tidak valid, contohnya:

- Mencegah harga produk bernilai negatif.
- Menolak referensi ke supplier yang tidak terdaftar.
- Mencegah penurunan harga produk secara drastis lewat trigger.
--- 
## **Data Praktikum**

**Data Tabel Praktikum 3 :** [Ambil di sini](https://drive.google.com/drive/folders/1rym4qWM7-0HfHQAIR0RsTq-4LUyIBaaf?usp=drive_link)

- Data produk terdiri dari 25.000 record.
- Data supplier terdiri dari 25.000 record.

---
## **Langkah Praktikum**

#### **1. Persiapan**

- Setiap anggota kelompok menyiapkan koneksi ke server MySQL.
- Membuat schema baru dengan nama:
```
Prak_MBD_3
```    
- Jalankan query berikut untuk membuat schema baru:

```sql
CREATE DATABASE IF NOT EXISTS Prak_MBD_3;
USE Prak_MBD_3;
```

---
### **2. Menyiapkan Tabel**

- Setelah schema dibuat, selanjutnya buat tabel untuk menyimpan data produk dan supplier.
- Tabel Produk (berisi 25.000 data produk langka).
- Tabel Supplier (berisi 25.000 data supplier unik).

#### A. Tabel `supplier`

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

#### B. Tabel `produk`

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
### **3. Percobaan Praktikum**
#### **Query Processing & Optimisasi** 
##### A. Query Seleksi
* Tanpa index 
```sql
SELECT * FROM produk WHERE harga > 100000;
```

```sql
EXPLAIN SELECT * FROM produk WHERE harga > 100000;
```
* Sebelumnya kamu telah mencoba untuk melihat produk dengan harga > 100000 dengan waktu eksekusi yang berbeda, sekarang bandingkan dengan waktu ketika kamu menggunakan **explain**.
  
---
##### B.  Optimasi dengan Index
* Buatkan index pada kolom harga
```sql
CREATE INDEX idx_harga ON produk(harga);
```
```sql
EXPLAIN SELECT * FROM produk WHERE harga > 100000;
```
---
#### **Integrity Constraints**
##### **A.  NOT NULL dan CHECK Constraint**
* Modifikasi Tabel
```sql
ALTER TABLE produk 
MODIFY nama_produk VARCHAR(100) NOT NULL,
ADD CONSTRAINT check_harga CHECK (harga > 0),
ADD CONSTRAINT check_stok CHECK (stok >= 0);
```
* Lakukan pengujian dengan query dibawah ini.
```sql
INSERT INTO produk (id, nama_produk, kategori, harga, stok, deskripsi, tanggal_ditambahkan, id_supplier)
VALUES ('25010', 'Lepi', 'Barang', '50000000' , '-5', 'Lepi Merek epel', '2025-05-08', '2' );
```
* Gagal?  YUPP karena query melanggar batasan harga dan stok.
---
 ##### B.  **UNIQUE Constraint**
* Modifikasi Tabel Supplier
```sql
ALTER TABLE supplier 
MODIFY kontak VARCHAR(15) NOT NULL,
ADD CONSTRAINT unique_kontak UNIQUE (kontak);
```
* Lakukan pengujian dengan query dibawah ini. 
```sql
INSERT INTO supplier VALUES ('25001', 'Toko INLINEER', '08123456789', 'Lampung', 'acul@gmail.com', '2022-08-16');
INSERT INTO supplier VALUES ('25002', 'Toko HIMPUNAN', '08123456789', 'Lampung', 'himpunan@gmail.com','2015-05-02');
```
* Insert Kedua Gagal? karena nomor telpon sudah ada dan nomor telpon adalah tabel uniq
---

C.  **ON DELETE CASCADE**
* Modifikasi Foreign Key:
```sql
ALTER TABLE produk
ADD CONSTRAINT fk_supplier
FOREIGN KEY (id_supplier) REFERENCES supplier(id)
ON DELETE CASCADE;
```
* Lakukan pengujian dengan query dibawah ini .
```sql
DELETE FROM supplier WHERE id = '50';
SELECT * FROM produk WHERE id_supplier = '50';
```
* Produk dari supplier tersebut juga ikut terhapus.
---
##### **D.  Trigger Logging Perubahan Stok**
* Buat Tabel Log:
```sql
CREATE TABLE log_stok (
    id_produk VARCHAR(10),
    stok_lama INT,
    stok_baru INT,
    waktu TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
* Buat Trigger:
```sql
DELIMITER //

CREATE TRIGGER log_stok_trigger
AFTER UPDATE ON produk
FOR EACH ROW
BEGIN
    IF OLD.stok <> NEW.stok THEN
        INSERT INTO log_stok (id_produk, stok_lama, stok_baru, waktu)
        VALUES (OLD.id, OLD.stok, NEW.stok, NOW());
    END IF;
END;
//

DELIMITER ;
```
* Lakukan pengujian dengan query dibawah ini.
```sql
UPDATE produk SET stok = stok + 2 WHERE id = '1002';
SELECT * FROM log_stok WHERE id_produk = '1002';
```
---
##### E.  **Penolakan Penurunan Harga Drastis (>50%)**
* Trigger Validasi Harga:
```sql
DELIMITER //
CREATE TRIGGER prevent_price_drop
BEFORE UPDATE ON produk
FOR EACH ROW
BEGIN
    IF NEW.harga < (OLD.harga * 0.5) THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Penurunan harga terlalu besar!';
    END IF;
END;
//
DELIMITER ;
```
* Lakukan pengujian dengan query dibawah ini.
```sql
UPDATE produk SET harga = harga * 0.4 WHERE id = '999';
```
* Gagal, trigger akan mencegah penurunan harga berlebihan.
##### F.  **Prosedur Maksimum Produk per Supplier**
* Stored Procedure:
```sql
DELIMITER //

CREATE PROCEDURE tambah_produk (
    IN p_id VARCHAR(10),
    IN p_nama VARCHAR(100),
    IN p_kategori VARCHAR(50),
    IN p_harga NUMERIC(10,2),
    IN p_stok INT,
    IN p_deskripsi TEXT,
    IN s_id VARCHAR(10)
)
BEGIN
    DECLARE total INT;
    SELECT COUNT(*) INTO total FROM produk WHERE id_supplier = s_id;

    IF total >= 5 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Supplier sudah memiliki 5 produk';
    ELSE
        INSERT INTO produk (
            id, nama_produk, kategori, harga, stok, deskripsi, tanggal_ditambahkan, id_supplier
        )
        VALUES (
            p_id, p_nama, p_kategori, p_harga, p_stok, p_deskripsi, CURDATE(), s_id
        );
    END IF;
END;
//

DELIMITER ;
```
* Lakukan pengujian dengan query dibawah ini.
```sql
CALL tambah_produk(
  '90020',                        
  'Fosil Stegosaurus',               
  'Koleksi Museum',                  
  750000,                             
  2,                                   
  'Replika fosil langka stegosaurus', 
  '20'                                 
);
```
---
### **4. Dokumentasi**

- Dokumentasikan setiap langkah-langkah praktikum, untuk di masukkan kedalam laporan

### **5. Pertanyaaan & Tugas Praktikum**

1. Apa perbedaan CHECK, TRIGGER, dan PROCEDURE dalam pengendalian data?
2. Kapan lebih tepat menggunakan validasi di database daripada di aplikasi?
3. Mengapa indexing dapat mempercepat query tertentu, dan memperlambat lainnya?
4. Apa risiko penggunaan ON DELETE CASCADE?