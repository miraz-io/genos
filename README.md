# Genos: buat agen AI pertamamu dengan Claude Code

*Satu staf AI kecil, satu tugas, sekitar 30-45 menit.*

Genos adalah staf AI pertamamu, versi kecil. Anggap saja anak magang baru: perannya jelas, aturannya tertulis, dan semua hasil kerjanya kamu periksa dulu. Tugas pertamanya: **mengubah catatan meeting jadi daftar tugas**. Ia hanya membuat **draf**, tidak mengirim apa pun, dan **selalu minta izinmu sebelum menulis file**.

Panduan ini untuk kamu yang baru mulai. Cukup sekitar 30-45 menit (diuji di macOS). Kamu hanya perlu bisa membuka terminal dan menyalin teks; semua isi file sudah ada di sini. Kalau hasilmu beda, bandingkan dengan folder [`contoh/`](contoh/).

> Diverifikasi: Claude Code **2.1.326**, macOS, **19 November 2026**, terhadap dokumentasi resmi (daftar sumber di bagian "Yang sudah diverifikasi" paling bawah). Tampilan dan harga bisa berubah. Kalau ada yang berbeda dari panduan ini, ikuti dokumentasi resmi.

## Isi
1. [Prasyarat dan biaya](#1-prasyarat-dan-biaya)
2. [Instal Claude Code](#2-instal-claude-code)
3. [Buat folder kerja](#3-buat-folder-kerja)
4. [Tulis aturan: CLAUDE.md](#4-tulis-aturan-claudemd)
5. [Buat agennya: genos.md](#5-buat-agennya-genosmd)
6. [Siapkan catatan meeting contoh](#6-siapkan-catatan-meeting-contoh)
7. [Jalankan tugas pertama](#7-jalankan-tugas-pertama)
8. [Periksa hasilnya (draf)](#8-periksa-hasilnya-draf)
9. [Atur izin](#9-atur-izin)
10. [Kembangkan Genos](#10-kembangkan-genos)
11. [Kalau ada yang tidak jalan](#11-kalau-ada-yang-tidak-jalan)
12. [Yang sudah diverifikasi](#12-yang-sudah-diverifikasi)
13. [English summary](#english-summary)

---

## 1. Prasyarat dan biaya
- Komputer macOS, Linux, atau Windows, dan kemampuan membuka terminal (di Windows: PowerShell).
- **Akun Claude berbayar yang mencakup Claude Code.** Paket Free **tidak** mencakup Claude Code. Paket paling murah yang mencakupnya adalah **Pro: $20 per bulan jika dibayar bulanan, atau $17 per bulan jika dibayar tahunan ($200 di muka)**, belum termasuk pajak. Max ($100/bulan) dan Ultra ($200/bulan) juga mencakupnya. Sumber: halaman harga resmi, dibaca 19 Nov 2026: <https://claude.com/pricing>. Harga dapat berubah; cek halaman itu sebelum berlangganan.
- Ada juga akun Console/API dengan bayar per pemakaian, tapi tarifnya tidak kami cek di panduan ini. Untuk mulai, paket Pro paling gampang.
- Pemakaian Claude Code masuk ke batas pemakaian paketmu. Tugas Genos di panduan ini kecil.
- Kamu tidak butuh kunci API dan tidak perlu menyimpan rahasia apa pun di folder ini.

## 2. Instal Claude Code
Perintah resmi (sumber: <https://code.claude.com/docs/en/setup.md>, dibaca 19 Nov 2026):

- **macOS / Linux / WSL:**
  ```bash
  curl -fsSL https://claude.ai/install.sh | bash
  ```
- **Windows PowerShell:**
  ```powershell
  irm https://claude.ai/install.ps1 | iex
  ```
- Alternatif: `brew install --cask claude-code` (macOS), `winget install Anthropic.ClaudeCode` (Windows), atau `npm install -g @anthropic-ai/claude-code@latest` (butuh Node.js 22+, jangan pakai `sudo`). Pemasangan lewat installer resmi memperbarui diri sendiri; Homebrew dan WinGet tidak.

Tutup lalu buka lagi terminalmu, kemudian cek:
```bash
claude --version
```
Harus keluar nomor versi (di panduan ini 2.1.326; versi lebih baru normal). Kalau bermasalah: `claude doctor`.

**Login:** masuk ke folder mana saja lalu jalankan `claude`. Saat pertama kali, ia meminta login: ikuti petunjuk di layar dengan akun Claude berbayar tadi. Di dalam sesi, `/login` bisa dipakai lagi. Keluar dengan `/exit`.

## 3. Buat folder kerja
Genos bekerja di dalam satu folder. Buat folder khusus (di luar folder penting lain):

```bash
mkdir genos-kerja
cd genos-kerja
mkdir -p .claude/agents catatan
```
(Di PowerShell perintah yang sama berjalan; atau buat foldernya lewat Explorer/Finder. Folder `.claude` bersifat tersembunyi: di Finder tekan `Cmd+Shift+.` untuk menampilkannya.)

Struktur yang akan jadi:
```
genos-kerja/
├── CLAUDE.md                 (aturan folder)
├── catatan-meeting.md        (bahan)
├── catatan/                  (tempat hasil Genos)
└── .claude/agents/genos.md   (definisi Genos)
```

### Cara membuat file (pilih satu)
Langkah 4, 5, 6, dan 9 meminta kamu membuat file teks. Pilih cara yang paling nyaman:

**Cara A, paling gampang: salin dari repo ini.** Kalau kamu sudah mengunduh repo ini, dari folder repo jalankan (macOS/Linux):
```bash
cp -r contoh genos-kerja
cd genos-kerja
```
Itu membuat `genos-kerja/` lengkap dengan `CLAUDE.md`, `catatan-meeting.md`, `catatan/`, dan `.claude/agents/genos.md` (termasuk folder tersembunyi `.claude`). Di Windows: salin folder `contoh` lalu ganti namanya menjadi `genos-kerja`. Setelah itu kamu boleh **lompat ke langkah 7** (langkah 4-6 tinggal dibaca supaya paham isinya).

**Cara B: pakai editor teks.** Buka editor (VS Code, atau Notepad di Windows, atau TextEdit di Mac dengan menu Format → Make Plain Text), tempel isi blok kode dari panduan ini, lalu simpan dengan nama file persis seperti di langkah itu, berakhiran `.md`. Di Windows pilih "Save as type: All files" supaya tidak menjadi `.txt`. Untuk file di dalam folder tersembunyi `.claude/agents/`, buat foldernya dulu dengan perintah di atas (`mkdir -p .claude/agents catatan`), lalu simpan file ke dalamnya.

**Cara C: lewat terminal (macOS/Linux).** Contoh untuk satu file; pola yang sama untuk file lain:
```bash
cat > catatan-meeting.md <<'EOF'
# Catatan meeting mingguan (contoh fiktif)
Hadir: tim operasional dan penjualan.
EOF
```
Isi yang kamu tempel ada di antara baris pertama dan baris `EOF`.

**Batas salin blok `genos.md` (langkah 5).** Blok itu dibungkus **empat** tanda kutip terbalik (` ```` `) karena di dalamnya ada blok yang memakai tiga. Salin hanya **isi di antara** baris pembuka dan baris penutup; jangan ikut menyalin baris tanda kutipnya. Isi file dimulai dari baris `---` yang pertama dan berakhir di kalimat "Balas dengan daftar tugas yang dibuat ... serta butir yang perlu klarifikasi."

## 4. Tulis aturan: CLAUDE.md
`CLAUDE.md` adalah buku aturan folder ini. Claude Code membacanya otomatis setiap sesi dimulai di folder ini (sumber: <https://code.claude.com/docs/en/memory.md>). Buat file `CLAUDE.md` di dalam `genos-kerja/` dengan isi:

```markdown
# Aturan folder kerja ini

Folder ini adalah ruang kerja "Genos", staf AI pertama saya. Genos mengubah catatan meeting menjadi daftar tugas.

## Aturan untuk semua yang bekerja di sini
- Hasil kerja hanya DRAF, disimpan sebagai file di folder `catatan/`. Saya yang memutuskan apa yang dipakai.
- Jangan mengirim apa pun ke luar komputer ini (email, pesan, unggahan).
- Jangan menghapus atau menimpa file. Kalau nama file sudah ada, pakai nama baru.
- Jangan mengarang. Kalau informasi tidak ada di catatan, tulis "belum ditentukan".
- Jawab dalam Bahasa Indonesia yang singkat dan jelas.

## Struktur
- `catatan-meeting.md`: catatan meeting (bahan, jangan diubah).
- `catatan/`: tempat hasil Genos (satu file per tugas + `daftar-tugas.md`).
- `.claude/agents/genos.md`: definisi Genos.
```

## 5. Buat agennya: genos.md
Agen adalah satu file teks di `.claude/agents/`. Di Claude Code istilah resminya "subagen"; di sini kita sebut saja agen atau staf AI. Bagian atas file (di antara dua baris `---`) berisi pengaturan. Bagian bawahnya berisi pesan kerja untuk agen, ditulis seperti kamu memberi tahu staf baru (sumber: <https://code.claude.com/docs/en/sub-agents.md>).

Pengaturan yang dipakai:
- `name`: nama agen (wajib).
- `description`: kapan agen ini dipakai (wajib). Claude membacanya untuk memilih agen.
- `tools`: alat yang BOLEH dipakai Genos, dipisah koma. Di sini hanya `Read, Glob, Grep, Write`: membaca, mencari, dan menulis file. **Tidak ada Bash (perintah terminal), tidak ada internet, tidak ada pengiriman.** Ini cara pertama menjaga staf AI tetap aman: beri alat sesedikit mungkin.

Buat file `.claude/agents/genos.md` dengan isi:

````markdown
---
name: Genos
description: Merangkum catatan meeting di folder ini menjadi daftar tugas, lalu mencatat tiap tugas sebagai draf di folder catatan/. Pakai agent ini kalau ada catatan meeting yang perlu diubah jadi daftar tugas. Genos hanya membuat draf; ia tidak mengirim dan tidak menghapus apa pun.
tools: Read, Glob, Grep, Write
---

Kamu Genos, asisten yang mengubah catatan meeting menjadi daftar tugas.

## Tugas
1. Cari catatan meeting di folder kerja ini (misalnya `catatan-meeting*.md` atau file `.md`/`.txt` lain yang isinya notulen). Jangan baca isi folder `catatan/` sebagai sumber.
2. Ambil setiap butir yang menuntut tindakan dan jadikan satu tugas. Untuk tiap tugas, tentukan:
   - **Judul**: singkat dan diawali kata kerja ("Siapkan draf penawaran untuk 3 cabang").
   - **Tenggat**: tulis persis seperti di catatan ("sebelum Rabu"). Kalau tidak disebut, tulis "belum ditentukan". Jangan mengarang tanggal.
   - **Penanggung jawab**: hanya kalau disebut di catatan. Kalau tidak, tulis "belum ditentukan".
   - **Konteks**: satu atau dua kalimat dari catatan asli.
   - **Sumber**: nama file dan nomor baris.
3. Catat tiap tugas sebagai satu file baru di folder `catatan/`, dengan nama `catatan/tugas-<nomor>-<slug-judul>.md`. Isinya mengikuti format ini:

   ```markdown
   # <Judul>
   Status: DRAF
   Tenggat: <...>
   Penanggung jawab: <...>
   Sumber: <file>:<baris>

   <Konteks>
   ```

4. Terakhir, tulis `catatan/daftar-tugas.md`, yaitu ringkasan semua tugas dalam bentuk checklist (`- [ ] ...`) yang menautkan ke masing-masing file tugas.

## Aturan (wajib)
- **Hanya membuat draf.** Semua yang kamu tulis berstatus DRAF dan hanya disimpan sebagai file lokal di `catatan/`.
- **Jangan mengirim apa pun.** Tidak ada email, pesan, undangan kalender, unggahan, atau permintaan ke layanan luar.
- **Jangan menghapus atau menimpa apa pun.** Jangan ubah catatan meeting asli. Sebelum menulis, cek dengan Glob apakah nama file sudah ada. Kalau sudah ada, pakai nomor berikutnya. Jangan pernah menimpa file lama.
- Tulis hanya di dalam folder `catatan/`.
- Kalau sebuah butir ambigu, tetap catat sebagai tugas dan tambahkan baris "Perlu klarifikasi: ..." daripada menebak.

## Laporan akhir
Balas dengan daftar tugas yang dibuat (judul dan path file) serta butir yang perlu klarifikasi.
````

Empat bagian yang membuat agen enak diatur: **peran** (satu kalimat), **langkah kerja**, **format hasil**, dan **aturan wajib** (apa yang tidak boleh dilakukan). Pola ini bisa kamu pakai untuk staf AI apa pun.

## 6. Siapkan catatan meeting contoh
Buat file `catatan-meeting.md` di `genos-kerja/`:

```markdown
# Catatan meeting mingguan (contoh fiktif)
Hadir: tim operasional dan penjualan.
- Pelanggan minta penawaran untuk 3 cabang; draf harus siap sebelum Rabu.
- Demo kedua dengan tim operasional perlu dijadwalkan minggu depan.
- Data pelanggan di spreadsheet lama belum dirapikan; perlu dicek sebelum rapat berikutnya.
- Kontrak vendor hosting habis bulan depan, perlu dibandingkan dua opsi.
```
Nanti kamu bisa mengganti isinya dengan catatan meeting sungguhan (hindari data rahasia kalau belum yakin).

> Jalan pintas: semua file di langkah 4-6 sudah ada di folder `contoh/` repo ini. Kamu bisa menyalinnya: `cp -r contoh genos-kerja` (macOS/Linux).

## 7. Jalankan tugas pertama
Pastikan kamu berada di dalam `genos-kerja/`, lalu mulai Claude Code:

```bash
claude
```

**Dialog kepercayaan folder.** Pertama kali di folder baru, Claude Code bertanya apakah folder ini bisa dipercaya ("Is this a project you created or one you trust?"). **Hati-hati: pilihan yang menyala di awal adalah "No, exit"**, jadi kalau kamu langsung menekan Enter, Claude Code akan keluar. Folder ini kamu buat sendiri, jadi tekan **panah bawah sekali** sampai "Yes, I trust this folder" menyala, lalu Enter.

Setelah itu ketik tugasnya (satu kalimat biasa, sebutkan nama agennya). Kalau kotak ketik menampilkan teks saran berwarna abu-abu, itu bukan perintahmu: ketik kalimatmu sendiri sebelum menekan Enter.

```
Gunakan agen genos untuk merangkum catatan-meeting.md
```

Yang terjadi: Claude memanggil Genos (kamu melihat baris `Genos(...)`). Genos membaca catatanmu, lalu bersiap menulis file. Genos bekerja di latar belakang, jadi pertanyaan izin pertama bisa baru muncul sekitar 20 detik kemudian. Tunggu saja.

## 8. Periksa hasilnya (draf)
**Genos tidak akan menulis apa pun tanpa izinmu.** Untuk setiap file baru, kamu akan ditanya seperti ini:

```
Do you want to create tugas-1-siapkan-draf-penawaran-3-cabang.md?
 1. Yes
 2. Yes, and switch to accept edits (auto-approve file edits ...) for this session
 3. No
```
Kamu melihat isi file yang akan ditulis, lalu memilih:
- **1. Yes** untuk menyetujui file itu saja (dan akan ditanya lagi untuk file berikutnya),
- **2.** untuk menyetujui penulisan file selama sesi ini (kamu tidak ditanya lagi),
- **3. No** untuk menolak.

Pilih Yes (1) untuk beberapa file pertama sambil membaca drafnya. Di sinilah aturan pentingnya bekerja: AI membuat draf, kamu yang memutuskan.

Setelah selesai, lihat hasilnya (di terminal lain, atau minta Genos: "tunjukkan isi daftar-tugas.md"):
```
catatan/
├── daftar-tugas.md
├── tugas-1-siapkan-draf-penawaran-3-cabang.md
├── tugas-2-jadwalkan-demo-kedua-tim-operasional.md
├── tugas-3-...md
└── tugas-4-...md
```
Contoh isi satu file (baris "Perlu klarifikasi" bisa ada, bisa tidak):
```markdown
# Siapkan draf penawaran untuk 3 cabang
Status: DRAF
Tenggat: sebelum Rabu
Penanggung jawab: belum ditentukan
Sumber: catatan-meeting.md:3

Pelanggan minta penawaran untuk 3 cabang; draf harus siap sebelum Rabu.

Perlu klarifikasi: siapa penanggung jawabnya, dan Rabu yang mana?
```
Hasil AI tidak persis sama tiap kali, jadi nama file dan kalimatnya bisa sedikit berbeda. Yang harus sama: status DRAF, tenggat persis seperti di catatan, penanggung jawab "belum ditentukan" (karena tidak disebut), dan `catatan-meeting.md` tidak berubah. **Selalu baca drafnya sebelum dipakai.** Kamulah yang memutuskan.

## 9. Atur izin
Secara bawaan (mode "manual"), Claude Code meminta izin untuk menulis file dan menjalankan perintah; membaca file di folder kerja tidak perlu izin (sumber: <https://code.claude.com/docs/en/permissions.md>).

Kalau kamu sudah yakin dengan Genos dan bosan ditanya terus, izinkan **hanya folder `catatan/`**. Buat file `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["Edit(/catatan/**)"]
  }
}
```
- Aturan izin menulis file memakai nama `Edit` (mencakup membuat/mengubah file). `/catatan/**` artinya folder `catatan/` di akar proyek ini. Catatan resmi: `Write(catatan/**)` tidak cocok untuk operasi tulis; gunakan `Edit(...)`.
- File ini baru dibaca setelah kamu mempercayai folder (langkah 7). Mulai ulang `claude` setelah membuat atau mengubahnya.
- Hasil uji (lihat bagian 12): dengan aturan ini, kelima file draf ditulis tanpa pertanyaan.
- Prinsipnya: izinkan sesempit mungkin. Jangan memberi izin untuk seluruh komputer.
- Mode lain (ganti dengan `Shift+Tab`): `acceptEdits` (setujui semua penulisan file), `plan` (hanya merencanakan, tidak mengubah apa pun). Hindari mode yang melewati semua izin.

## 10. Kembangkan Genos
Ide bertahap, ubah satu hal sekali jalan:
1. **Ubah aturan.** Tambah baris di `CLAUDE.md` atau bagian "Aturan" di `genos.md`, mis. "Urutkan tugas berdasarkan tenggat."
2. **Format hasil lain.** Minta ringkasan satu halaman, atau tabel.
3. **Catatan tiap tugas (jejak kerja).** Tambah di `genos.md`: "Setelah selesai, tulis `catatan/log.md`: tanggal, file yang dibaca, file yang dibuat."
4. **Tambah staf kedua.** Buat `.claude/agents/<nama>.md` lain dengan peran berbeda (mis. penulis draf email dari satu tugas). Beri `tools` seminim mungkin.
5. **Pilih model.** Di bagian atas agen boleh menambah `model: sonnet` (atau `haiku` untuk tugas ringan). Daftar pengaturan lengkap: <https://code.claude.com/docs/en/sub-agents.md>.
6. **Jalankan tanpa layar** (`claude -p "..."`) untuk tugas berulang. Pelajari izinnya dulu: tanpa izin yang tepat, penulisan file ditolak (kami uji: ditolak).
7. **Tiga aturan yang tidak berubah, sebesar apa pun staf AI-mu nanti:** AI membuat draf dan manusia memutuskan; beri alat dan izin sesedikit mungkin; selalu catat apa yang dikerjakan.

## 11. Kalau ada yang tidak jalan
- **`claude` tidak ditemukan:** tutup dan buka lagi terminal; jalankan `claude doctor`.
- **Harus login terus / "Free plan":** Claude Code butuh paket berbayar (Pro ke atas).
- **Agen tidak dikenali:** pastikan file ada di `.claude/agents/genos.md` (di dalam folder tempat kamu menjalankan `claude`), isi bagian atas berawalan dan berakhiran `---`, lalu mulai ulang `claude`.
- **Salah folder (menjalankan `claude` dari folder induk, bukan `genos-kerja`):** Claude tidak berkata "agen tidak dikenali". Ia bisa malah mencari-cari dan meminta menjalankan perintah terminal (mis. `ls ... ~/.claude/agents`) dengan pilihan "switch to auto mode". Pilih **No** (jangan pilih auto mode). Setelah itu muncul "Interrupted · What should Claude do instead?": ketik `/exit`, lalu `cd genos-kerja` dan jalankan `claude` lagi dari dalam folder itu.
- **Tidak ada file yang tertulis:** kamu menolak izin, atau memakai `claude -p` tanpa izin. Jalankan `claude` biasa dan pilih Yes.
- **Settings tidak berlaku:** pastikan kamu sudah memilih "Yes, I trust this folder" dan mulai ulang.
- **Hasil beda dari contoh:** wajar (AI tidak deterministik). Periksa aturan di `genos.md` dan tegaskan bila perlu.

## 12. Yang sudah diverifikasi
Versi: Claude Code **2.1.326** (macOS), tanggal cek **19 November 2026**.

Dokumentasi resmi yang dibaca (tanggal akses 19 Nov 2026; halamannya tidak mencantumkan tanggal pembaruan): [quickstart](https://code.claude.com/docs/en/quickstart.md), [setup](https://code.claude.com/docs/en/setup.md), [memory/CLAUDE.md](https://code.claude.com/docs/en/memory.md), [subagents](https://code.claude.com/docs/en/sub-agents.md), [permissions](https://code.claude.com/docs/en/permissions.md), [harga](https://claude.com/pricing).

Diuji nyata dari nol (folder kosong, bukan folder kerja lain):
- Genos terbaca dan dipanggil dengan kalimat "Gunakan agen genos untuk merangkum catatan-meeting.md". Empat tugas terbaca benar; tenggat persis; penanggung jawab "belum ditentukan".
- Mode manual: setiap file baru memunculkan pertanyaan izin (Yes / Yes dan terima edit sesi / No). Lima file dibuat (4 tugas + `daftar-tugas.md`); `catatan-meeting.md` tidak berubah; tidak ada yang dikirim.
- `.claude/settings.json` dengan `Edit(/catatan/**)`: lima file ditulis tanpa pertanyaan setelah folder dipercaya.
- Folder baru memunculkan dialog kepercayaan; tanpa dipercaya, settings proyek tidak dibaca.
- `claude -p` tanpa izin: penulisan ditolak (sesuai dokumen).
- Penguji pemula (sesi terpisah, folder bersih): langkah 3-9 berjalan; hasil cocok bagian 8; temuan dipakai memperbaiki langkah 3, 7, dan 11.

Klaim yang dicek ulang ke dokumen resmi (19 Nov 2026): instalasi native memperbarui diri sendiri, Homebrew/WinGet tidak; paket npm butuh Node.js 22 atau lebih baru; aturan `Write(path)` tidak cocok untuk operasi tulis, gunakan `Edit(path)` (halaman permissions.md).

Belum diuji: perintah instal (mesin uji sudah terpasang; perintah diambil dari dokumen resmi), Windows/Linux, dan apakah izin `Edit(/catatan/**)` tetap menanyakan penulisan di luar `catatan/` (dokumen menyatakan ya).

Lisensi: MIT (lihat `LICENSE`).

---

## English summary
**Genos** is your first AI staff member, in miniature. Think of a new intern: a clear role, written rules, and every piece of work checked by you. You can build it in about 30-45 minutes (tested on macOS) with Claude Code. Its first job: turn **meeting notes into a task list**. It only writes **drafts**, sends nothing, and asks your permission before writing any file.

Steps (details in the Indonesian guide above, all file contents included):
1. **Plan:** Claude Code requires a paid Claude plan. Cheapest: Pro, $20/month billed monthly or $17/month billed annually ($200 upfront), before tax; Free does not include Claude Code (https://claude.com/pricing, checked 19 Nov 2026).
2. **Install:** macOS/Linux `curl -fsSL https://claude.ai/install.sh | bash`; Windows PowerShell `irm https://claude.ai/install.ps1 | iex`; check with `claude --version`; log in by running `claude`.
3. **Folder:** `mkdir genos-kerja && cd genos-kerja && mkdir -p .claude/agents catatan`.
4. **Rules:** write `CLAUDE.md` (read automatically each session).
5. **Agent:** write `.claude/agents/genos.md` with frontmatter `name`, `description`, `tools: Read, Glob, Grep, Write` (no Bash, no internet) and clear instructions and rules.
6. **Sample notes:** `catatan-meeting.md`.
7. **Run:** `claude` in the folder, trust the folder, then type: `Gunakan agen genos untuk merangkum catatan-meeting.md` ("use the genos agent to summarize catatan-meeting.md").
8. **Review:** Claude asks permission for each new file; you read the draft and choose Yes / Yes-and-accept-edits / No. Results land in `catatan/`. You decide what to use.
9. **Permissions:** to skip prompts for one folder only, add `.claude/settings.json` with `{"permissions":{"allow":["Edit(/catatan/**)"]}}` (note: use `Edit(...)`, `Write(...)` does not match writes) and restart.
10. **Grow it:** tighten rules, add a log, add a second agent with minimal tools, pick a model; keep the golden rule: AI drafts, a human decides.

Verified on Claude Code 2.1.326 (macOS), 19 Nov 2026, against the official docs; tested from an empty folder. License: MIT.
