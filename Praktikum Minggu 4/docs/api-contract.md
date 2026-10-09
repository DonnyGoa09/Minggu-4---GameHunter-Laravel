# API Contract — GameHunter

Resource: Game. Contract ini menjadi acuan endpoint baca GameHunter.

| Method | Path | Success | Error |
| --- | --- | --- | --- |
| GET | /api/games | 200, data array | 422 query tidak valid |
| GET | /api/games/{game} | 200, data object | 404 tidak ditemukan |

Header: Accept: application/json. Endpoint publik, tanpa autentikasi.

| Field | Type | Aturan |
| --- | --- | --- |
| id | integer | Positif, dibuat server |
| title | string | 1–200 karakter |
| platform | string | pc atau browser |
| is_free | boolean | true/false |
| source | string | fixture untuk data contoh lokal |
| updated_at | string | ISO 8601 UTC |

Semua field read-only. Query platform opsional, hanya pc/browser; query lainnya ditolak 422. Daftar kosong: 200 dengan data: []. ID hilang atau tidak berupa integer positif: 404. POST belum tersedia (405).

Contoh detail 200:

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

404:

```json
{"message":"Resource not found","errors":null}
```

422:

```json
{"message":"The given data was invalid.","errors":{"platform":["Gunakan platform pc atau browser; hanya parameter platform yang didukung."]}}
```

ID dan tanggal contoh dapat berbeda. Struktur dan tipe response harus sama.
