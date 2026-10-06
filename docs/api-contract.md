# API Contract — Perpustakaan

## User Stories
- Sebagai pengunjung, saya ingin melihat daftar buku agar dapat memilih buku yang tersedia untuk dipinjam.
- Sebagai petugas, saya ingin menambahkan data buku baru agar katalog perpustakaan selalu lengkap.

## Resource Dictionary — Book
| Field          | Type    | Required saat Create | Akses      | Aturan Validasi                          | Contoh Nilai                     |
|----------------|---------|----------------------|------------|------------------------------------------|----------------------------------|
| id             | integer | Tidak                | Read-only  | Dihasilkan otomatis server              | 15                               |
| title          | string  | Ya                   | Read/write | 3–200 karakter                           | Clean Code                       |
| isbn           | string  | Ya                   | Read/write | Unik, 10 atau 13 digit                   | 9780132350884                    |
| author_name    | string  | Ya                   | Read/write | Maksimal 150 karakter                    | Robert C. Martin                 |
| published_year | integer | Tidak                | Read/write | Antara 1900–tahun sekarang               | 2008                             |
| available      | boolean | Tidak                | Read-only  | Dihitung/diatur server                   | true                             |
| created_at     | string  | Tidak                | Read-only  | Format ISO 8601                          | 2026-09-25T09:30:00Z             |

## Endpoint Matrix
| Kebutuhan               | Method | Endpoint              | Success | Error        |
|-------------------------|--------|-----------------------|---------|--------------|
| Daftar semua buku       | GET    | /api/books            | 200     | —            |
| Tambah buku baru        | POST   | /api/books            | 201     | 422          |
| Lihat detail satu buku  | GET    | /api/books/{book}     | 200     | 404          |
| Ubah sebagian data buku | PATCH  | /api/books/{book}     | 200     | 404, 422     |
| Hapus buku              | DELETE | /api/books/{book}     | 204     | 404          |

## Request & Response Contract

### Create Request
```http
POST /api/books
Content-Type: application/json
Accept: application/json

{
  "title": "Clean Code",
  "isbn": "9780132350884",
  "author_name": "Robert C. Martin",
  "published_year": 2008
}
```
## 200 OK — Daftar Buku
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
## 201 Created — Buku Berhasil
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
## 404 Not Found
```json
{
  "message": "Book not found",
  "errors": null
}
```
## 422 Unprocessable Content
```json
{
  "message": "The given data was invalid.",
  "errors": {
    "title": ["The title field is required."],
    "isbn": ["The isbn has already been taken.", "The isbn must be 10 or 13 digits."]
  }
}
```
## Design Decisions
Menggunakan method PATCH untuk mengubah data, karena client hanya perlu mengirim field yang berubah, tidak seluruh data.
Field available bersifat read-only karena nilainya diatur oleh sistem berdasarkan status peminjaman, bukan dikirim client.
## Refleksi
- Keputusan yang paling memengaruhi client: Bentuk struktur JSON, nama field, tipe data, dan kode status respons. Jika berubah, kode pemanggil di sisi client harus diubah.
- Risiko jika tipe field berubah: Akan menyebabkan error parsing data di client, tampilan rusak, atau logika pemrosesan gagal.
## Bagian yang diterjemahkan ke Laravel:
- Endpoint : Route di routes/api.php
- Aturan validasi : bagian validate() di Controller atau Form Request
- Bentuk respons : API Resource di app/Http/Resources/
