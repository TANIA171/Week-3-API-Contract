# API Contract — Perpustakaan

## User Stories
- Sebagai pengunjung, saya ingin melihat daftar buku agar dapat memilih buku yang tersedia untuk dipinjam.
- Sebagai petugas, saya ingin menambahkan data buku baru agar katalog perpustakaan selalu lengkap.

## Resource Dictionary — Book
| Field          | Type    | Required saat Create | Akses       | Aturan Validasi                          | Contoh Nilai                     |
|----------------|---------|----------------------|-------------|------------------------------------------|----------------------------------|
| id             | integer | Tidak                | Read-only   | Dihasilkan otomatis oleh server          | 15                               |
| title          | string  | Ya                   | Read/write  | 3–200 karakter                           | Clean Code                       |
| isbn           | string  | Ya                   | Read/write  | Unik, 10 atau 13 digit                   | 9780132350884                    |
| author_name    | string  | Ya                   | Read/write  | Maksimal 150 karakter                    | Robert C. Martin                 |
| published_year | integer | Tidak                | Read/write  | Antara tahun 1900–sekarang               | 2008                             |
| available      | boolean | Tidak                | Read-only   | Dihitung/diatur oleh server              | true                             |
| created_at     | string  | Tidak                | Read-only   | Format ISO 8601                          | 2026-09-25T09:30:00Z             |

## Endpoint Matrix
| Kebutuhan               | Method | Endpoint              | Status Berhasil | Status Error      |
|-------------------------|--------|-----------------------|-----------------|-------------------|
| Daftar semua buku       | GET    | /api/books            | 200 OK          | —                 |
| Tambah buku baru        | POST   | /api/books            | 201 Created     | 422 Unprocessable |
| Lihat detail buku       | GET    | /api/books/{book}     | 200 OK          | 404 Not Found     |
| Ubah sebagian data buku | PATCH  | /api/books/{book}     | 200 OK          | 404, 422          |
| Hapus buku              | DELETE | /api/books/{book}     | 204 No Content  | 404 Not Found     |

## Request & Response Contract

### Create Request
```http
POST/api/books
Content-Type: application/json
Accept: application/json

{
  "title": "Clean Code",
  "isbn": "9780132350884",
  "author_name": "Robert C. Martin",
  "published_year": 2008
}
200 OK — Daftar Bukujson{
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
201 Created — Buku Berhasil Dibuatjson{
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
404 Not Foundjson{
  "message": "Book not found",
  "errors": null
}
### 422 Unprocessable Content
```json
{
  "message": "The given data was invalid.",
  "errors": {
    "title": ["The title field is required."],
    "isbn": ["The isbn has already been taken.", "The isbn must be 10 or 13 digits."]
  }
}
## Design Decisions
- Menggunakan **method PATCH** untuk mengubah data, karena klien hanya perlu mengirim kolom yang berubah, bukan seluruh data.
- Kolom `available` bersifat **hanya-baca (read-only)** karena nilainya diatur oleh sistem berdasarkan status peminjaman, bukan dikirim oleh klien.
## Refleksi
1. **Keputusan yang paling memengaruhi klien:** Bentuk struktur respons JSON, nama setiap kolom, tipe datanya, dan kode status yang dikembalikan. Jika bagian ini berubah, kode di sisi klien harus disesuaikan.
2. **Risiko jika tipe data kolom berubah:** Akan menyebabkan kegagalan saat membaca data di klien, tampilan menjadi tidak benar, atau aturan pemrosesan menjadi salah.
3. **Bagian yang akan diterjemahkan ke Laravel:**
   - **Titik akhir (Endpoint)** → ditulis di `routes/api.php` sebagai Rute
   - **Aturan validasi** → ditulis di bagian `validate()` pada Pengendali (Controller) atau Permintaan Bentuk (Form Request)
   - **Bentuk respons** → dibuat sebagai Sumber Daya API (API Resource) di folder `app/Http/Resources/`
