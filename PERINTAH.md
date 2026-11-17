# Referensi semua perintah

Semua perintah kerja dijalankan pada grup yang diizinkan. Teks dalam tanda kurung siku bersifat opsional; jangan ketik tanda kurung tersebut. ID notifikasi berlaku pada **event aktif grup tersebut**, bukan kode skenario. Hanya laporan arsip yang menerima ID event lama.

## Umum dan Blue Team

| Perintah/contoh | Fungsi |
|---|---|
| `/start` | Bantuan; di pribadi mengaktifkan penerimaan laporan untuk akun itu |
| `/bantuan` | Daftar singkat perintah |
| `/id` | User ID dan chat/group ID dari bot sendiri |
| `/status` | Status semua notifikasi, penerima, kuota, kritis dan maksimum nilai |
| `/status N001` | Status satu notifikasi, pertanyaan terakhir, nilai publik jika tersedia |
| `/notifikasi_baru` | Membuat N001/N002 otomatis; reply ke pesan hasil untuk mengirim materi |
| `/kaitkan N001` | Mode pengiriman untuk pengguna itu, kedaluwarsa 600 detik |
| `/kaitkan` | Melihat kaitan sendiri dan waktu tersisa |
| `/kaitkan N002` | Mengganti kaitan sendiri |
| `/kaitkan lepas` | Melepas kaitan sendiri |
| `/kirim N001` | Memproses seluruh pesan/evidence tim yang belum diproses untuk N001 |
| `/daftar_notifikasi` | Status ringkas semua notifikasi event aktif |

Tidak ada satu kaitan bersama seluruh grup. Anggota lain harus reply ke pesan bot atau mengatur kaitannya sendiri. Caption `N001 — Log autentikasi server survei` juga merupakan pengaitan eksplisit. Reply dan caption yang menunjuk ID berbeda ditolak. Pesan tanpa kaitan disimpan sebagai belum dikaitkan, tidak diproses; kirim ulang dengan ID benar. `/kaitkan` tidak memindahkan otomatis pesan terdahulu.

## Admin/controller dan admin utama

| Perintah/contoh | Fungsi dan konfirmasi |
|---|---|
| `/event_baru Cyberdrill Gelombang 1` | Membuat event tanpa skenario; ditolak jika grup masih punya event aktif |
| `/kritis 2` | Default kritis untuk notifikasi baru: 1 dasar, 2 menengah, 3 tinggi; konfirmasi |
| `/kritis N001 3` | Ubah khusus N001 yang belum selesai; konfirmasi dan audit |
| `/pertanyaan 3 5` | Default minimum 3, maksimum 5, per respons 2; konfirmasi |
| `/pertanyaan N001 2 4 1` | Khusus N001: minimum 2, maksimum 4, per respons 1; konfirmasi |
| `/nilai_maksimal 50` | Default maksimum 50 untuk notifikasi baru; konfirmasi |
| `/nilai_maksimal N001 100` | Maksimum khusus N001; konfirmasi, ditolak setelah nilai terkunci selesai/tertunda |
| `/jeda N001` | Menjeda notifikasi yang menunggu jawaban/klarifikasi/notifikasi atau nilai |
| `/lanjut N001` | Memulihkan status sebelum dijeda |
| `/selesai_notifikasi N001` | Menu: tutup dan nilai data tersedia / tutup tanpa nilai / batal; kemudian konfirmasi |
| `/hubungkan N001 N003 aset_sama` | Tampilkan kedua status/penerima lalu konfirmasi hubungan; konteks/nilai tetap terpisah |
| `/daftar_pemetaan` | Data aset–pihak dan sumber/kepastian dikirim pribadi admin |
| `/koreksi_pemetaan 1 LAPTOP-001 \| Andi \| pengguna perangkat \| Konfirmasi Blue Team` | Ganti pemetaan ID 1; ketik pemisah `\|` sebagai karakter `|` tanpa backslash; konfirmasi, nilai lama tetap |
| `/hapus_pemetaan 1 Hubungan keliru` | Soft-delete dengan alasan, konfirmasi dan audit |
| `/ringkasan N001` | Ringkasan satu notifikasi, default ke pribadi admin |
| `/ringkasan aset LAPTOP-001` | Ringkasan aset dengan nama cocok persis, huruf besar/kecil diabaikan |
| `/ringkasan penerima Tim Pengelola Server` | Ringkasan pihak yang cocok persis |
| `/ringkasan peran pengelola server` | Ringkasan jenis penerima yang cocok persis |
| `/ringkasan event` | Ringkasan seluruh event ke pribadi admin |
| `/ringkasan event grup` | Admin secara eksplisit memilih ringkasan temuan ke grup |
| `/evaluasi N001` | Evaluasi komunikasi dan rincian penilaian ke pribadi admin |
| `/evaluasi event` | Evaluasi komunikasi seluruh event, selalu pribadi; tidak menerima fallback grup |
| `/rekap_nilai N001` | Rekap nilai otomatis dan akhir evaluator ke pribadi admin |
| `/rekap_nilai aset LAPTOP-001` | Rekap per aset |
| `/rekap_nilai penerima Tim Pengelola Server` | Rekap per pihak |
| `/rekap_nilai peran pengelola server` | Rekap jenis penerima |
| `/rekap_nilai event` | Rekap terpisah setiap notifikasi; tidak dijumlah/dirata-ratakan |
| `/coba_nilai_ulang N001` | Retry hanya penilaian tertunda; tidak menggandakan nilai yang sudah ada |
| `/tetapkan_nilai N001 45 Koreksi evaluator atas koordinasi` | Nilai akhir 0–maksimal, alasan wajib, rincian pribadi dahulu, lalu tombol konfirmasi di grup |
| `/riwayat_nilai N001` | Rincian semua aspek, catatan evaluator dan perubahan nilai melalui pribadi |
| `/audit_log` | 100 tindakan admin terakhir event dan perubahan admin global, selalu pribadi |
| `/reset_event` | Menu keputusan notifikasi aktif/tertunda, lalu tombol Arsipkan dan Reset/Batal |
| `/arsip_event` | Menu arsip yang sama; tidak menghapus data dan tidak membuat event baru otomatis |
| `/daftar_arsip` | ID/nama event diarsipkan pada grup ini, melalui pribadi |
| `/ringkasan arsip 1 event` | Ringkasan event arsip ID 1 |
| `/evaluasi arsip 1 N001` | Evaluasi komunikasi notifikasi pada arsip 1 |
| `/rekap_nilai arsip 1 event` | Rekap nilai arsip 1 |

Jenis hubungan `/hubungkan`: `perkembangan`, `aset_sama`, `insiden_terkait`, `insiden_sama_penerima_berbeda`, `lainnya`. Pilih jenis lewat argumen; setiap penyimpanan membutuhkan tombol konfirmasi.

Semua tombol sensitif terikat pada admin pengaju, grup, event, masa berlaku 300 detik, dan satu kali pakai. Hak admin diverifikasi saat tombol digunakan. Setelah admin dicabut atau event berganti, tombol lama tidak berlaku. Tombol Batal tidak mengeksekusi tindakan.

Perubahan default event berlaku bagi **notifikasi yang dibuat setelah perubahan**. Notifikasi yang sudah ada memakai snapshot sendiri; ubah secara eksplisit melalui ID bila diperlukan. Riwayat perubahan tersimpan di audit; pengaturan yang digunakan per giliran juga tersimpan di turns. Rentang pertanyaan yang diterima: minimum/maksimum 0–30, minimum ≤ maksimum; per respons 1–5. Jangan gunakan maksimum 0 untuk latihan normal yang membutuhkan klarifikasi penerima.

## Admin utama

| Perintah/contoh | Fungsi |
|---|---|
| `/daftar_admin` | Daftar user ID dan peran melalui pribadi |
| `/tambah_admin 345678901` | Daftarkan controller, default peran admin |
| `/tambah_admin 456789012 utama` | Daftarkan admin utama tambahan |
| `/hapus_admin 345678901` | Cabut hak aplikasi; admin utama terakhir tidak boleh dihapus/diturunkan |

Admin baru wajib `/start` di pribadi. BOOTSTRAP_ADMIN_IDS tidak digunakan ulang setiap restart. Daftar admin dipertahankan saat reset event.
