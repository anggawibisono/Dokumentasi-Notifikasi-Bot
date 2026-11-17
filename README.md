# Panduan pelaksanaan Cyberdrill

## Persiapan event dan beberapa admin

1. Admin utama pertama berasal dari `BOOTSTRAP_ADMIN_IDS` saat database baru. Tambahkan controller dengan `/tambah_admin 345678901 admin`; admin utama tambahan dengan `/tambah_admin 456789012 utama`.
2. Setiap admin membuka pribadi bot dan `/start`. Peserta biasa boleh `/start`, tetapi tidak memperoleh hak admin.
3. Di grup: `/event_baru Cyberdrill Gelombang 1`. Tidak ada langkah memilih skenario.
4. Atur default sebelum membuka notifikasi: `/kritis 2`, `/pertanyaan 3 5 2`, `/nilai_maksimal 50`. Tekan konfirmasi untuk masing-masing perubahan.
5. Periksa `/status`, lalu peserta membuka `/notifikasi_baru` sebanyak kebutuhan.

Admin utama/controller global pada instalasi, diotorisasi menggunakan angka Telegram user ID. Identitas pegawai di notifikasi disimpan sebagai data peran/pemetaan, terpisah dari akun pengirim.

## Membuka notifikasi paralel

Contoh anggota A membuat N001 untuk pengelola server; anggota B membuat N002 untuk pengguna laptop. Tidak diperlukan pengetahuan tentang rencana serangan.

```text
Anggota A: /notifikasi_baru
Bot: Notifikasi baru: N001. Reply ke pesan ini...
Anggota A: [reply] Yth. Tim Pengelola Server, kami melaporkan dugaan akses
            tidak sah pada server surveylab. Waktu kejadian menurut log
            15 November 2026 pukul 09.20; zona log belum diketahui.
Anggota C: [foto, caption N001 — Evidence autentikasi host surveylab]
Anggota B: /notifikasi_baru
Bot: Notifikasi baru: N002...
Anggota B: [reply N002] Yth. Pak Andi selaku pengguna LAPTOP-001...
Anggota A: /kirim N001
Anggota B: /kirim N002
```

Seluruh kontribusi N001 milik A dan C diperlakukan sebagai satu respons Blue Team. `/kirim N001` tidak mengambil pesan atau gambar N002. Tidak ada penilaian per anggota atau rata-rata antar-anggota.

Bot tidak langsung merespons setiap pesan. Setelah satu atau beberapa pertanyaan bot, anggota menjawab melalui reply/kaitan, anggota lain dapat menambah evidence, lalu satu orang mengirim `/kirim ID` ketika respons tim siap. Tunggu proses AI selesai sebelum mengirim kontribusi baru; saat status `diproses` aplikasi meminta kirim ulang agar snapshot tetap konsisten.

## Penerima dan konteks aset

Tuliskan satu penerima serta tanggung jawabnya secara eksplisit:

- “Yth. Tim Pengelola Server Aplikasi Survei” berarti pengelola server.
- “Yth. Tim Pengelola Aplikasi Survei” berarti pengelola aplikasi.
- “Yth. Tim Pengelola Akun Aplikasi” berarti pengelola identitas/akses.
- “Yth. Pak Andi selaku pengguna LAPTOP-001” berarti pegawai pengguna perangkat.
- Pemilik data dan perwakilan unit kerja boleh dinyatakan dengan cara yang sama.

Model meminta klarifikasi bila tujuan tidak disebut atau ada beberapa penerima berbeda. Gunakan notifikasi berbeda untuk peran berbeda; admin dapat mengonfirmasi hubungan melalui `/hubungkan N001 N002 insiden_sama_penerima_berbeda`. Tidak ada penggabungan otomatis berdasarkan IP atau waktu mirip.

Jika nama/peran perlu dikoreksi, jawab jelas “Ini koreksi penerima N001 ...”, beserta sumber. Jika pihak baru yang dituju, buka notifikasi baru. Skor selesai tidak diubah karena koreksi percakapan berikutnya. Pengelola server tidak otomatis dianggap pemilik aplikasi, pengguna akun bukan otomatis pengelola akun, dan nama akun log bukan bukti pemilik perangkat.

Pertanyaan dipandu peran: pegawai memakai bahasa nonteknis dan fokus pada keamanan penggunaan/pekerjaan/koordinasi; pengelola server fokus layanan dan pemulihan; aplikasi fokus pengguna/data; akun fokus autentikasi/hak akses; pemilik data fokus dampak dan ketidakpastian. Ini adalah perilaku model yang perlu diuji dengan contoh lab nyata, bukan jaminan akurasi semantik.

## Mengaitkan teks dan gambar

Pilihan utama: reply ke pesan pembuka/pertanyaan bot yang memuat N001. Bot menyimpan pemetaan ID pesan Telegram ke notifikasi, termasuk setelah restart.

Alternatif: setiap anggota `/kaitkan N001`. Berlaku 10 menit untuk anggota itu saja. Lihat `/kaitkan`, ganti `/kaitkan N002`, lepas `/kaitkan lepas`. Kaitan tidak diperpanjang diam-diam setiap kali pesan dikirim.

Caption dapat diawali `N001 — keterangan gambar`. Nama file dokumen bukan pengaitan. Reply, caption, dan tujuan album harus konsisten. Jika tidak ada konteks atau penanda bertentangan, pesan tidak dimasukkan ke notifikasi lain berdasarkan tebakan. Pesan ambigu yang diterima tanpa ID disimpan sebagai belum dikaitkan dan tidak masuk analisis; kirim ulang dengan penanda yang benar. Versi pertama belum memiliki UI memindahkan pesan ambigu lama.

Album disimpan sebagai beberapa pesan/evidence dengan satu `media_group_id`. Tujuan item pertama dikunci untuk seluruh album; item berikutnya memakai tujuan itu dan penanda bertentangan ditolak. Bila item pertama belum dikaitkan, seluruh album dianggap belum dikaitkan. Gunakan caption ID pada item pertama, reply yang benar, atau mode kaitan sebelum mengirim album. Tunggu sedikitnya `ALBUM_SETTLE_SECONDS` (default 3 detik) setelah item terakhir sebelum `/kirim`. Telegram tidak memberi penanda “album lengkap”; jaringan sangat lambat dapat mengirim item terlambat. Item yang datang saat diproses ditolak dengan petunjuk kirim ulang, bukan dipindahkan otomatis.

Format didukung: JPEG, PNG, WEBP statis melalui foto atau dokumen; GIF animasi dan PDF tidak didukung. Untuk log kecil, dokumen PNG asli sering lebih terbaca daripada foto yang dikompres Telegram. Batas default 8 MiB/file, 10 gambar/notifikasi, 25 juta piksel, 8.000 karakter/pesan. MIME dokumen dan isi gambar harus cocok; file rusak ditolak. Satu gambar besar tidak boleh dipakai untuk menghindari batas piksel.

Gambar dikirim ke OpenAI secara privat melalui data URL, tanpa URL publik. Interpretasi AI disimpan terstruktur dan dipakai kembali; gambar asli tidak dikirim pada setiap pertanyaan. Tidak ada jaminan gambar terbaca benar. Jika buram/terpotong, model mencatat keterbatasan dan dapat meminta evidence lebih jelas sesuai kuota.

## Waktu dan pemetaan

Waktu Telegram disimpan ISO-8601 dari pesan, waktu penerimaan file disimpan terpisah, dan tampilan Telegram dikonversi ke `Asia/Jakarta`. Waktu log, filter dashboard, kejadian yang dilaporkan, dan janji pembaruan disimpan sebagai fakta bersumber terpisah. Timestamp evidence tanpa zona tidak diberi WIB secara otomatis. Tanggal yang tidak terlihat tetap tidak diketahui. Penafsiran ketidaksesuaian aset/waktu oleh model harus ditinjau evaluator.

`/daftar_pemetaan` mengirim daftar hubungan aset–pihak, sumber dan kepastian melalui pribadi admin. Koreksi, misalnya:

```text
/koreksi_pemetaan 1 LAPTOP-001 | Andi | pengguna perangkat | Konfirmasi pada N002
```

Tekan tombol konfirmasi. Pemetaan lama masuk audit, sumber baru menyatakan admin dan alasan, status menjadi dikonfirmasi. `/hapus_pemetaan 1 Duplikasi hubungan` melakukan soft-delete terkonfirmasi. Pemetaan event lama tidak diwariskan saat event baru dibuat. Pemetaan AI yang dihasilkan dari giliran sebelumnya tidak dianggap otomatis sebagai direktori organisasi resmi.

## Tingkat kritis, jumlah pertanyaan dan maksimum nilai

Default aplikasi: kritis 2, minimum 3, maksimum 5, maksimum per respons 2, nilai maksimal 100. Perintah event mengubah default untuk notifikasi **baru**. Snapshot tiap ID disimpan saat notifikasi dibuka; pengaturan khusus ID memiliki prioritas atas default tersebut.

Contoh `/kritis N001 3`, `/pertanyaan N001 2 4 1`, `/nilai_maksimal N001 50` memerlukan konfirmasi, diterapkan setelah tombol sah ditekan, dan dicatat di audit. Jika nilai sudah selesai atau penilaian tertunda, pengaturan ID tidak dapat diubah. Menurunkan kuota di bawah hitungan yang sudah diajukan ditolak.

Satu elemen pertanyaan terstruktur dihitung satu, termasuk pertanyaan klarifikasi penerima. Prompt meminta satu pokok per elemen; aplikasi menjamin batas jumlah elemen, bukan kualitas semantik kalimat model. Setiap respons dipotong sesuai batas per respons dan sisa kuota. Pertanyaan terakhir harus memperoleh kesempatan dijawab melalui pesan baru dan `/kirim`; kemudian analisis jawaban terakhir dan penilaian dilakukan.

Bot dapat selesai setelah minimum terpenuhi dan data cukup, atau maksimum tercapai setelah jawaban terakhir. Jika tidak ada pertanyaan relevan sebelum minimum, model boleh memberi alasan penutupan; alasan disimpan dalam analisis/riwayat untuk evaluator. Jika kuota habis dan penerima belum jelas, admin memilih penutupan dini, bukan bot mengarang identitas. `/selesai_notifikasi N001` memberi pilihan nilai berdasarkan data tersedia / tanpa nilai / batal, lalu meminta konfirmasi. Penutupan dini ditandai pada hasil dan catatan kelengkapan.

## Rubrik dan rumus

| Aspek | Bobot | Fokus |
|---|---:|---|
| Kejelasan | 20% | Penerima, aset, kejadian dan waktu sejauh diketahui |
| Dampak | 20% | Status layanan/perangkat, pengguna/data, ketidakpastian |
| Tindakan | 20% | Tindakan tim dan permintaan yang sesuai peran |
| Jawaban | 20% | Relevansi dan konsistensi jawaban atas pertanyaan |
| Komunikasi | 20% | Bahasa, koordinasi, kontak dan waktu pembaruan |

AI memberi skor 0–100 per aspek dengan alasan dan rujukan. Aplikasi memvalidasi lima aspek unik, rentang nilai, dan rujukan ID yang ada pada notifikasi. Bobot versi 1.0 tetap; aplikasi tidak meminta AI menentukan rumus akhir.

```text
persentase = Σ(skor_aspek × 20 / 100)
nilai_otomatis = persentase / 100 × nilai_maksimal
```

Perhitungan memakai Decimal dan pembulatan `ROUND_HALF_UP` dua desimal. Jika semua aspek 82, persentase 82%; maksimum 50 menghasilkan **41,00/50,00**. Tampilan aplikasi menggunakan titik desimal (`41.00`) agar input angka dan audit konsisten; bukan pemisah ribuan.

Jawaban singkat yang memadai tidak otomatis mendapat nilai rendah. Pernyataan “belum diketahui” dengan rencana pemeriksaan/tindak lanjut wajar tidak otomatis dihukum. Klaim panjang bukan bukti. Penilaian AI bersifat **sementara**, bukan hasil evaluator manusia.

Grup menerima nilai otomatis dan review singkat per notifikasi setelah selesai. Rincian aspek, alasan, catatan evaluator, riwayat dan audit hanya dikirim pribadi admin. `/riwayat_nilai N001` melihat rinciannya. Jika AI gagal, status `penilaian_tertunda`, gunakan `/coba_nilai_ulang N001`; retry tidak menambah nilai aktif kedua.

## Evaluator manusia dan laporan

`/tetapkan_nilai N001 45 Hasil peninjauan evaluator` memerlukan alasan. Bot mengirim rincian ke pribadi admin sebelum tombol konfirmasi di grup. Nilai akhir dibatasi 0–maksimal dan disimpan terpisah; nilai otomatis asli dan riwayat perubahan tidak ditimpa. Jika admin belum `/start` pribadi atau memblokir bot, tidak ada fallback rahasia ke grup.

`/ringkasan N001`, `/ringkasan aset LAPTOP-001`, `/ringkasan penerima Tim Pengelola Server`, dan `/ringkasan event` default pribadi. Tambahkan `grup` hanya pada perintah ringkasan bila temuan layak dibagikan. `/evaluasi N001` atau `/evaluasi event` selalu pribadi, termasuk catatan AI untuk evaluator. `/rekap_nilai` menampilkan setiap skor dan maksimalnya secara terpisah; tidak ada agregasi lintas maksimum berbeda pada versi ini.

Ringkasan mengacu data bot, tidak memeriksa sistem secara langsung. Klaim, hasil interpretasi evidence dan ketidakpastian tetap dibedakan. Hasil panjang dibagi menjadi pesan maksimal 3.000 karakter. Karena ringkasan disusun model, evaluator tetap meninjau kesesuaian sumbernya.

## Mengakhiri dan memulai gelombang berikutnya

1. Periksa `/status`, `/rekap_nilai event`, dan `/evaluasi event`.
2. `/reset_event` atau `/arsip_event` menampilkan event, status seluruh notifikasi, dan jumlah selesai.
3. Jika semua selesai, pilih arsip jika seluruhnya selesai. Jika masih aktif/tertunda, pilih batal, tutup tanpa nilai lalu arsip, atau nilai dahulu. Tombol pilihan membuka konfirmasi kedua.
4. Pilihan nilai dahulu menampilkan daftar pada menu awal dan memproses notifikasi yang belum selesai secara sadar sebagai penutupan awal. Jika satu penilaian gagal, event **belum diarsipkan**; pilih retry, tutup tanpa nilai, atau batalkan reset. Notifikasi yang berhasil dinilai tetap tersimpan.
5. Setelah seluruh penilaian berhasil, bot meminta admin memeriksa hasil dan menjalankan `/reset_event` lagi. Tidak ada pengarsipan diam-diam.
6. Tekan **Arsipkan dan Reset** untuk komit akhir. Data lama, nilai, evidence dan admin tidak dihapus. Event baru belum dibuat otomatis.
7. `/event_baru Cyberdrill Gelombang 2`. Notifikasi baru dimulai N001 dalam event baru dengan ID database berbeda, tanpa konteks/pemetaan lama.
8. `/daftar_arsip` dan `/ringkasan arsip 1 event` untuk melihat event lama pada grup yang sama. Lakukan backup setelah event.
