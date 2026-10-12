Aku baca kelima screenshot dulu. Sebelum menyimpulkan apa pun, aku perlu tahu di mana engine dipanggil dari situs, karena default v1 yang kamu minta belum kuterapkan.Ada hal yang perlu kuperiksa. `engine.mjs` sekarang memakai `ctx.legacy!==false`, sedangkan versi yang kutulis sebelumnya memakai `!!ctx.legacy`. Kalau begitu, pemanggilan tanpa flag di harness v2 juga berjalan sebagai v1. Aku cek langsung apakah keduanya masih berbeda.Ternyata sekarang pemanggilan tanpa flag sama dengan v1, jadi **situs sudah memakai v1 sebagai default**. Namun ini punya efek samping. Harness mengukur v2 dan v2t dengan memanggil engine tanpa flag, jadi keduanya sekarang berjalan sebagai v1. Untuk memperbaikinya, aku membuat harness memberi `legacy:false` secara eksplisit pada v2 dan v2t, lalu memeriksanya.Penggantian di harness gagal karena teks barisnya tidak cocok persis, jadi `validate.mjs` belum berubah. Yang berhasil diverifikasi adalah engine: `legacy:false` memang membedakan v2 dari v1 pada 60 dari 60 sampel. Sekarang aku cari baris `at()` yang sebenarnya.Materi dari Discord ini paling berguna di satu hal: Eliz menaruh **invalidasi berbasis close, bukan wick**. Ini mengarah ke kelemahan yang sudah terlihat di uji kita. Di bawah ini ringkasan materinya, apa yang sudah ada di engine, dan apa yang perlu diuji.

## Isi materi

- **NEAR (1 September):** deviasi di bawah support harian lalu reclaim. Zona reclaim 1,81 sampai 1,96. Konfirmasi di 2,57 dengan close harian di atasnya. Target makro 6,30. Invalidasi hanya kalau close harian di bawah 1,53. Deviasi wick di bawah zona masih dianggap wajar selama harga cepat reclaim.
- **SUI:** setup asimetris berisiko tinggi. Entry 0,79 sampai 0,81, konfirmasi break di 1,03, target 2,46, invalidasi di bawah 0,63. Eliz sendiri menulis "bottom di sini untuk gerakan besar, atau nol".
- **BTC makro (16 September):** selama weekly tetap di zona 65K sampai 68K, koreksi dianggap peluang beli. Invalidasi yang berarti hanya breakdown yang terkonfirmasi di bawah 65K. Ia juga meminta fokus pada level, bukan sentimen.
- **Diagram double deviation:** deviasi pertama lebih dalam, lalu reclaim. Deviasi kedua lebih dangkal, lalu reclaim lagi, dan tekanan jual melemah. Targetnya likuiditas di atas high.

## Sudah ada di engine

Sweep dan reclaim, double deviation (`dd`), Monday Range, target berjenjang, dan invalidasi. Pendekatan "tanpa indikator" di PDF 2 juga sejalan dengan engine.

## Yang belum ada, dan perlu diuji

1. **Invalidasi berbasis close.** Ini yang paling penting. Engine menaruh SL di bawah wick sweep, dan itulah sebab sebagian besar SL cepat di hasil uji kita. Varian baru (v4) akan menaruh SL di bawah level invalidasi struktural (close), lalu dibandingkan dengan v1 pada dataset dan pembanding acak. Konsekuensinya jelas: SL lebih lebar, jadi R per trade lebih kecil.
2. **Rencana bertingkat.** Setiap rencana Eliz punya entry (reclaim), konfirmasi (break dan close), target makro, dan invalidasi. Engine baru punya TP1 sampai TP3 dan SL. Aku akan menambahkan field `konf` dan `inval` ke plan.
3. **Ichimoku cloud.** Chart SOL dan SUI di kedua PDF maupun Discord memakai awan Ichimoku. Ini bertentangan dengan catatan PDF 2 bahwa Eliz hampir tidak memakai indikator. Mungkin dipakai hanya sebagai konteks tren, atau catatan PDF 2 sudah usang. Yang bisa diuji: filter "harga di atas awan untuk long, di bawah untuk short".
4. **Zona makro manual.** Zona seperti BTC 65K sampai 68K tidak bisa diturunkan dari data. Ini perlu input dari pengguna, lalu sistem menilai posisi harga terhadap zona itu.
5. **Kategori asimetris.** Setup seperti SUI dan TAO sebaiknya masuk kategori terpisah, sama seperti investasi spot, dengan SL lebar dan target jauh. Jangan dicampur dengan setup trading biasa.
6. **Definisi double deviation perlu dicek.** Diagram mensyaratkan urutan: deviasi pertama lebih dalam, lalu reclaim, lalu deviasi kedua lebih dangkal, lalu reclaim lagi. Aku belum memeriksa apakah `dd` di engine mensyaratkan semua itu. Ini perlu dicek di kode sebelum mengklaim engine sudah sesuai.

## Dataset

- **NEAR-0901** bisa ditambahkan dengan zona 1,81 sampai 1,96, konfirmasi 2,57, invalidasi close di bawah 1,53, dan target 6,30. Kita bisa cek di Binance apakah close di bawah 1,53 terjadi sebelum harga menyentuh 2,57. Ini tes yang nyata untuk invalidasi berbasis close.
- **SUI** belum punya tanggal di screenshot. Tanggal post diperlukan supaya bisa diuji.
- **BTC 16 September** bisa diuji dengan melihat apakah close harian mingguan tetap di atas 65K setelahnya.

## Catatan tentang langkah sebelumnya

- Default situs sudah v1. Pemanggilan tanpa flag menghasilkan hasil identik dengan `legacy:true` pada 60 dari 60 sampel. Harness juga sudah benar: v2 dan v2t memakai `legacy:false` secara eksplisit. Perbaikan yang kucoba ternyata sudah terpasang, jadi tidak ada perubahan.
- Tampilan CSS sekarang sudah memakai palet gelap ala Meta (`#18191A`, `#242526`, `#3A3B3C`, `#4599FF`). Yang tersisa adalah penyempurnaan layout ala Meta, dan itu bisa dikerjakan setelah engine.

Mau aku mulai dari v4 (invalidasi berbasis close) lalu tambahkan NEAR-0901 ke dataset? Dan tolong kirimkan tanggal post SUI supaya bisa dimasukkan juga.
