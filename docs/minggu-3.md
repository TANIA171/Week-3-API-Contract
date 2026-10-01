# Tugas 3 — Draft API Contract

## Tujuan
Menerapkan perancangan API Contract pada sistem informasi perpustakaan sebagai kesepakatan antarmuka antara klien dan server, sehingga pengembangan implementasi Laravel dapat berjalan konsisten sesuai rancangan yang telah disepakati.

## Perubahan
- Menyusun daftar sumber daya **Book** beserta tipe data, aturan validasi, dan hak akses setiap kolom
- Menetapkan 5 titik akhir (endpoint) lengkap: menampilkan daftar, menambah, melihat detail, mengubah, dan menghapus data buku
- Menambahkan contoh respons berhasil: `200 OK`, `201 Created`
- Menambahkan contoh respons kesalahan: `404 Not Found`, `422 Unprocessable Content`
- Memperbarui koleksi Postman dengan 4 contoh respons yang telah disesuaikan dengan rancangan
- Menyusun dokumen kesepakatan sebagai acuan pengembangan pada tahap berikutnya

## Endpoint & Contract
| Kebutuhan                     | Method | Endpoint              | Status Berhasil | Status Error              |
|-------------------------------|--------|-----------------------|-----------------|---------------------------|
| Menampilkan daftar semua buku | GET    | `/api/books`          | 200 OK          | —                         |
| Menambahkan buku baru         | POST   | `/api/books`          | 201 Created     | 422 Unprocessable Content |
| Melihat detail satu buku      | GET    | `/api/books/{book}`   | 200 OK          | 404 Not Found             |
| Mengubah sebagian data buku   | PATCH  | `/api/books/{book}`   | 200 OK          | 404 Not Found, 422        |
| Menghapus buku                | DELETE | `/api/books/{book}`   | 204 No Content  | 404 Not Found             |

## Bukti Pengujian

### Respons Berhasil — 200 OK — Daftar Buku
```json
{
  "data": [
    {
      "id": 15,
      "title": "Clean Code",
      "isbn": "9780132350884",
      "author_name": "Robert C. Martin",
      "available": true
    }
  ]
}
```
### Respons Berhasil 201 Created
```json
{
  "data": {
    "id": 15,
    "title": "Clean Code",
    "isbn": "9780132350884",
    "author_name": "Robert C. Martin",
    "published_year": 2008,
    "available": true,
    "created_at": "2026-09-25T09:30:00Z"
  }
}
```
### Kasus Kesalahan 404 Not Found
```json
{
  "message": "Book not found",
  "errors": null
}
```
### 422 Unprocessable Content
```json
{
  "message": "The given data was invalid.",
  "errors": {
    "title": ["The title field is required."],
    "isbn": ["The isbn has already been taken.", "The isbn must be 10 or 13 digits."]
  }
}
```
# Keputusan Utama
- Menggunakan method PATCH untuk pembaruan data agar klien hanya mengirim kolom yang berubah, bukan seluruh data.
- Kolom available bersifat hanya-baca karena nilainya diatur oleh sistem berdasarkan status peminjaman, bukan dikirim dari sisi klien.
- Semua respons dibungkus dalam kunci data agar struktur konsisten dan mudah dikembangkan pada tahap berikutnya.
#   Kesimpulan
API Contract telah disusun sebagai kesepakatan antarmuka antara sisi klien dan server. Dokumen ini menjadi acuan pengembangan rute, aturan validasi, dan bentuk respons yang akan diimplementasikan menggunakan Laravel pada Minggu 4.
#   Referensi
- Dokumentasi Resmi Laravel — Rute API
- Materi Kuliah Minggu 3 — Perancangan API Contract
- Hasil Praktikum 3 — Postman Collection
#   Deklarasi Penggunaan AI
Dokumen ini disusun dengan bantuan panduan untuk menyusun format dan struktur; seluruh rancangan, data, dan keputusan adalah hasil pemikiran sendiri.
