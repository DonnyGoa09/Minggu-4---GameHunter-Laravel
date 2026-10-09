# Tugas Minggu 4 — Endpoint Read-only Laravel

## Tujuan

Menerapkan API contract GameHunter menjadi endpoint Laravel yang dapat dijalankan dan diuji.

## Perubahan

Model Game, migration tabel games, GameSeeder, dan GameController dibuat. Seeder menyediakan dua game development. Controller memiliki index() untuk daftar dan show() untuk detail.

Routing API didaftarkan melalui bootstrap/app.php dan routes/api.php. Model memakai Eloquent dan cast boolean untuk is_free. Collection Postman diperbarui dengan lima request, contoh response, dan pemeriksaan status serta bentuk JSON. Implementasi lokal sudah tersimpan pada commit `01b31a6`.

Keputusan utama:

- Hanya GET untuk daftar dan detail, karena fokus minggu ini adalah membaca data.
- Success memakai data: array untuk daftar, object untuk detail. Field dan tipe mengikuti API contract.
- ID tidak ditemukan menghasilkan 404; query tidak valid menghasilkan 422 agar penyebab error jelas.
- SQLite dan dua data fixture dipakai agar pengujian mudah diulang. Data ini bukan katalog game live.

## Endpoint dan contract

| Method | Endpoint | Response |
| --- | --- | --- |
| GET | /api/games | 200, array data |
| GET | /api/games/1 | 200, object data |
| GET | /api/games/999999 | 404, resource tidak ditemukan |
| GET | /api/games?platform=pc | 200, daftar game PC |
| GET | /api/games?platform=console | 422, validasi gagal |

Gunakan `Accept: application/json` dan No Auth. Field resource: id integer, title/platform/source string, is_free boolean, dan updated_at ISO 8601. Mapping response menjaga bentuk JSON sesuai [contract](<../../Praktikum Minggu 4/docs/api-contract.md>).

Contoh detail sukses:

```json
{
  "data": {
    "id": 1,
    "title": "Game Contoh PC",
    "platform": "pc",
    "is_free": true,
    "source": "fixture",
    "updated_at": "2026-10-09T08:22:09.000000Z"
  }
}
```

## Bukti pengujian

Pengujian dilakukan pada 9 Oktober 2026 terhadap Laravel 13 dan SQLite development. Screenshot menampilkan response dari request yang dikirim lewat Postman.

### Daftar game — 200 OK

GET /api/games mengembalikan dua game dalam array data. Postman: 4/4 test lulus.

![Daftar game 200 OK](../images/gamehunter-list-200.jpg)

### Detail game — 200 OK

GET /api/games/1 mengembalikan satu object game. Postman: 4/4 test lulus.

![Detail game 200 OK](../images/gamehunter-detail-200.jpg)

### Game tidak ditemukan — 404 Not Found

GET /api/games/999999 mengembalikan error sesuai contract. Postman: 3/3 test lulus.

![Game tidak ditemukan 404](../images/gamehunter-not-found-404.jpg)

Pengujian otomatis: **Pest 13 test, 34 assertion lulus**; **Newman 5 request, 18 assertion lulus**. Foto di atas menampilkan status, body, dan hasil test Postman.

Pengujian otomatis dijalankan ulang pada 9 Oktober 2026. Test mencakup daftar kosong, filter, ID tidak valid, query tidak dikenal, dan penolakan POST. Hasilnya tetap lulus.

Collection yang dipakai tersedia di [Postman collection](postman-collection.md). Salin blok JSON ke file collection, import ke Postman, lalu jalankan List Games terlebih dahulu. No Auth dan base_url lokal sudah disiapkan.

Dari project Laravel yang sudah memiliki dependency dan database development:

```bash
php artisan migrate
php artisan db:seed --class=GameSeeder
php artisan serve --host=127.0.0.1 --port=8000
```

Pada terminal kedua:

```bash
php artisan test --compact tests/Feature/GameReadOnlyTest.php
npx newman run postman/GameHunter_Minggu_4.postman_collection.json
```

Repository pengumpulan berisi laporan dan gambar. Kode Laravel serta collection .json berada di project lokal projek_api untuk demonstrasi langsung.

## Error case

ID yang tidak ada menghasilkan 404:

```json
{
  "message": "Resource not found",
  "errors": null
}
```

Platform yang tidak valid menghasilkan 422 dengan pesan validasi pada errors.platform. Daftar kosong tetap 200 dengan data: [], karena endpoint daftar masih tersedia.

Contoh 422 untuk /api/games?platform=console:

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "platform": ["Gunakan platform pc atau browser; hanya parameter platform yang didukung."]
  }
}
```

POST /api/games menghasilkan 405 karena endpoint tulis belum tersedia.

## Kesimpulan

Endpoint baca GameHunter sudah mengambil data dari database dan mengikuti contract Minggu 3. Respons sukses dan error sudah diuji. Data masih berupa fixture development.

Laporan dan gambar tidak memuat credential atau secret. Repository GitHub pengumpulan dapat diakses publik.

## Referensi

- Materi 4 dan Tugas 4, instruksi kelas.
- Praktikum 4 — Endpoint Read-only dengan Laravel, materi kelas.
- [Hasil Praktikum Minggu 4](<../../Praktikum Minggu 4/docs/minggu-4-praktikum.md>).
- [API contract GameHunter](<../../Praktikum Minggu 4/docs/api-contract.md>).
- [Laravel Routing](https://laravel.com/framework/docs/13.x/routing).
- [Laravel Eloquent](https://laravel.com/framework/docs/13.x/eloquent).
- [Laravel Migrations](https://laravel.com/framework/docs/13.x/migrations).
- [Postman: Import data](https://learning.postman.com/docs/getting-started/importing-and-exporting/importing-data/).

## Deklarasi penggunaan AI

AI (Codex) membantu implementasi endpoint, penyusunan test dan collection Postman, dokumentasi, serta pengambilan screenshot. Pengujian dijalankan pada project Laravel lokal; screenshot berasal dari response Postman yang sebenarnya. Saya tetap bertanggung jawab memahami dan menjelaskan hasil tugas ini.
