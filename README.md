# fibscan-rules

Aturan Bursa Efek Indonesia yang dibaca otomatis oleh aplikasi **FibScan IDX**.
Aplikasi mengecek file `idx_rules.json` di repo ini (paling sering sekali sehari). Kalau ada revisi baru,
aturan di HP ikut berubah tanpa perlu memasang APK baru.

Alamat yang dibaca aplikasi:
`https://raw.githubusercontent.com/SquamosS/fibscan-rules/main/idx_rules.json`

## Cara mengubah aturan (lewat browser)
1. Buka file `idx_rules.json` di repo ini, lalu klik ikon **pensil** (Edit this file).
2. Ubah bagian yang perlu (lihat contoh di bawah).
3. **Naikkan angka `"revision"`** (misalnya dari 1 menjadi 2) dan ubah `"verifiedAsOf"` ke tanggal hari ini (format `YYYY-MM-DD`).
   Aplikasi hanya memakai file ini jika revisinya sama atau lebih baru dari aturan bawaan aplikasi.
4. Klik **Commit changes**.
5. Di aplikasi: Pengaturan → "Periksa aturan sekarang", atau tunggu pengecekan otomatis.

Jika file rusak (misalnya kurang koma atau tanda kutip), aplikasi **mengabaikannya** dan tetap memakai aturan terakhir yang valid.
Untuk mengecek, tempel isi file ke https://jsonlint.com sebelum commit.

## Isi file
| Kolom | Arti |
|---|---|
| `schemaVersion` | Versi format file. Jangan diubah. |
| `revision` | Nomor revisi. **Naikkan setiap kali mengedit.** |
| `verifiedAsOf` | Tanggal aturan terakhir dicek ke pengumuman BEI. |
| `lotSize` | Jumlah lembar per lot (100). |
| `minPrice` | Harga minimum saham (Rp1 sejak 28 Sep 2026). |
| `tickBands` | Fraksi harga: mulai harga `minPrice`, kelipatannya `tick`. |
| `limitRegimes` | Aturan ARA/ARB per periode (`effectiveFrom`–`effectiveTo`, `null` = tanpa batas akhir). Tiap periode punya `bands` per rentang harga: `araPct`/`arbPct` dalam persen, `araAbs`/`arbAbs` dalam rupiah. `confirmed: false` = belum pasti. |
| `holidays` | Tanggal libur bursa (selain Sabtu & Minggu). |
| `fees` | Biaya default (persen): beli, jual, pajak jual. |
| `universes` | Daftar kode saham: `LQ45` dan `EXTRA` (saham likuid tambahan). |
| `universeValidUntil` | Tanggal berakhirnya periode daftar LQ45. |
| `notes` | Catatan yang tampil di menu Panduan. |

## Contoh perubahan yang akan dibutuhkan

**1. BEI mengumumkan aturan ARB 2027.** Ubah blok terakhir di `limitRegimes` (yang `effectiveFrom: "2027-01-01"`):
isi `arbPct` yang benar di setiap `bands`, set `"confirmed": true`, dan ganti `note` dengan nomor keputusan BEI.

**2. Kalender libur bursa 2027 terbit.** Tambahkan tanggalnya di akhir daftar `holidays`: format `YYYY-MM-DD`,
diapit tanda kutip, dipisah koma. Contoh bentuknya (tanggal `2027-MM-DD` hanya contoh format, ganti dengan tanggal dari kalender resmi BEI):
```json
"holidays": [ "2026-01-01", "...", "2026-12-31", "2027-01-01", "2027-MM-DD" ]
```

**3. Evaluasi LQ45 (Februari & Agustus).** Ganti daftar `universes.LQ45` dengan 45 kode baru, lalu ubah `universeValidUntil`.

Setiap perubahan aturan bursa sebaiknya dicek ke pengumuman resmi di https://www.idx.co.id.
