# 📦 Skema Database belanja-web

Ini adalah dokumentasi skema database yang dirancang untuk aplikasi e-commerce. Skema ini mendefinisikan entitas untuk mengelola pengguna, produk, pesanan, dan relasi di antara mereka.

Struktur ini ditulis menggunakan DBML (Database Markup Language).

<img width="1312" height="945" alt="ecommerce website " src="https://github.com/user-attachments/assets/2a26a9d9-852f-4029-886f-487064bbd204" />


## 🏛️ Struktur ERD (Entity Relationship Diagram)

Skema ini terdiri dari 8 tabel utama dan 2 tipe data kustom (enums).

* **Enums**:
    * `role_user`: Mendefinisikan peran pengguna (superadmin, customer).
    * `stock_product`: Mendefinisikan status ketersediaan produk (ready, preorder).
* **Tabel Utama**:
    * `users`: Menyimpan data akun pengguna dan perannya.
    * `brands`: Daftar merek produk.
    * `categories`: Daftar kategori produk.
    * `locations`: Daftar lokasi (misal: gudang atau kota penjual).
    * `products`: Katalog utama untuk semua produk yang dijual.
    * `orders`: Tabel "header" yang mencatat setiap transaksi.
    * `orders_detail`: Menyimpan detail pengiriman untuk setiap pesanan.
    * `orders_products`: Tabel perantara (junction table) yang menghubungkan produk apa saja yang ada di dalam sebuah pesanan.

---

## 🔧 Detail Tabel dan Kolom

Berikut adalah rincian untuk setiap tabel:

### Enums (Tipe Data Kustom)

**`enum role_user`**
Menentukan hak akses pengguna.
* `superadmin`
* `customer`

**`enum stock_product`**
Menentukan ketersediaan stok produk.
* `ready`: Stok tersedia dan siap kirim.
* `preorder`: Stok tidak tersedia, memerlukan pemesanan di awal.

---

### 👤 Tabel Pengguna dan Atribut Produk

**`Table users`**
Menyimpan data semua pengguna yang terdaftar.
* `id` (PK): Identifier unik pengguna.
* `name`: Nama lengkap pengguna.
* `email`: Alamat email unik untuk login.
* `password`: Password yang sudah di-hash.
* `type`: Peran pengguna (menggunakan enum `role_user`).

**`Table brands`**
Tabel pendukung (lookup) untuk merek.
* `id` (PK): Identifier unik merek.
* `logo`: Path atau URL ke file logo merek.
* `name`: Nama merek.

**`Table categories`**
Tabel pendukung untuk kategori produk.
* `id` (PK): Identifier unik kategori.
* `name`: Nama kategori (misalnya: Elektronik, Pakaian).

**`Table locations`**
Tabel pendukung untuk lokasi.
* `id` (PK): Identifier unik lokasi.
* `name`: Nama lokasi (misalnya: Jakarta, Gudang Pusat).

---

### 🛒 Tabel Produk dan Pesanan

**`Table products`**
Katalog utama untuk semua item yang dijual.
* `id` (PK): Identifier unik produk.
* `brand_id` (FK): Merujuk ke `brands.id`.
* `category_id` (FK): Merujuk ke `categories.id`.
* `location_id` (FK): Merujuk ke `locations.id`.
* `name`: Nama produk.
* `description`: Deskripsi detail produk.
* `price`: Harga produk (disimpan sebagai integer).
* `stock`: Status ketersediaan (menggunakan enum `stock_product`).
* `images`: Path atau URL ke gambar produk.

**`Table orders`**
Tabel "header" untuk setiap transaksi yang dibuat.
* `id` (PK): Identifier unik pesanan.
* `code`: Kode unik transaksi (misalnya: INV/2025/11/03/XYZ).
* `user_id` (FK): Merujuk ke `users.id` (pelanggan yang melakukan pesanan).

**`Table orders_detail`**
Menyimpan informasi pengiriman yang terkait dengan satu pesanan.
* `id` (PK): Identifier unik.
* `order_id` (FK): Merujuk ke `orders.id` (Relasi **One-to-One**).
* `name`: Nama penerima.
* `phone`: Nomor telepon penerima.
* `address`: Alamat lengkap pengiriman.
* `city`: Kota tujuan.
* `postal_code`: Kode pos.
* `notes`: Catatan tambahan dari pembeli.

**`Table orders_products`**
Tabel perantara yang merinci item apa saja yang ada di dalam sebuah pesanan.
* `id` (PK): Identifier unik.
* `product_id` (FK): Merujuk ke `products.id` (Produk yang dibeli).
* `order_id` (FK): Merujuk ke `orders.id` (Pesanan terkait).
* `subtotal`: Total harga untuk item ini (Harga x Kuantitas).
* `quantity`: Jumlah item produk yang dibeli.

---

## 🔄 Relasi Kunci

* **Users dan Orders (One-to-Many)**: Satu `user` dapat memiliki banyak `orders`.
* **Atribut dan Products (One-to-Many)**: Satu `brand`, `category`, atau `location` dapat digunakan oleh banyak `products`.
* **Orders dan Orders_Detail (One-to-One)**: Satu `order` hanya memiliki satu `orders_detail` (info pengiriman).
* **Orders dan Products (Many-to-Many)**: Relasi paling kompleks yang dijembatani oleh `orders_products`.
    * Satu `order` dapat berisi banyak `products`.
    * Satu `product` dapat muncul di banyak `orders` yang berbeda.
