# Hasil uji README dari nol (19 November 2026)
Claude Code 2.1.326, macOS. Folder bersih di luar folder kerja mana pun; isi file diambil LANGSUNG dari blok kode README (bukan dari `contoh/`), lalu dijalankan seperti pemula.

| # | Uji | Hasil |
|---|---|---|
| 1 | README langkah 3-6 persis (mkdir, CLAUDE.md, genos.md, catatan-meeting.md) | folder terbentuk; isi blok kode README identik dengan `contoh/` |
| 2 | `claude` di folder baru, mode manual | muncul dialog "Is this a project you created or one you trust?"; pilih "Yes, I trust this folder" |
| 3 | `Gunakan agen genos untuk merangkum catatan-meeting.md` | Genos dipanggil (baris `Genos(...)`); 4 tugas terbaca benar; tenggat persis ("sebelum Rabu", "minggu depan", "sebelum rapat berikutnya"); penanggung jawab "belum ditentukan" |
| 4 | Izin penulisan (manual) | tiap file baru memunculkan "Do you want to create <file>? 1.Yes 2.Yes, and switch to accept edits 3.No"; memilih Yes untuk tiap file: 5 file dibuat (4 tugas + `daftar-tugas.md`) |
| 5 | `catatan-meeting.md` | tidak berubah (hash sama sebelum dan sesudah) |
| 6 | Kirim keluar / hapus | tidak ada (alat Genos hanya Read, Glob, Grep, Write) |
| 7 | `.claude/settings.json` dengan `Edit(/catatan/**)` (setelah folder dipercaya) | kelima file ditulis tanpa pertanyaan, mode manual |
| 8 | `claude -p` tanpa izin | penulisan ke `catatan/` ditolak (sesuai dokumen); `settings.json` proyek tidak dibaca sebelum folder dipercaya |
| 9 | `--allowedTools "Edit(catatan/**)"` atau `"Write"` pada `-p` | menulis; `"Edit"` saja dan `Write(catatan/**)` tidak |

Belum diuji: perintah instal (mesin uji sudah terpasang; perintah dari dokumen resmi), Windows/Linux, penulisan di luar `catatan/` dengan aturan `Edit(/catatan/**)` (dokumen: tetap ditanya).
Catatan: pada mesin uji, mode bawaan pengguna adalah "auto"; uji memakai `--permission-mode default` ("manual"), yang umum bagi pemula.
