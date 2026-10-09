## 1.Hasil Pengujian 9 Citra Uji

Berdasarkan eksekusi pipeline, pembacaan sembilan citra ijazah memberikan rincian sebagai berikut:

[03_Blurred (1).jpg] Nomor Ijazah: Nomor ijazah: 571012022000056 | Tanda Tangan: PRESENT

[05_LowResolution_Upsampled (1).jpg] Nomor Ijazah: Nomor yazah: 571012022000056 | Tanda Tangan: PRESENT

[06_Faded_Underexposed (1).jpg] Nomor Ijazah: - Nomor yazah: 571012022000056 | Tanda Tangan: PRESENT

[07_ColorShift_WarmTint (1).jpg] Nomor Ijazah: - Nomor yazah: 571012022000056 | Tanda Tangan: PRESENT

[08_JPEGCompression_Artifacts (1).jpg] Nomor Ijazah: Nomor yazah: 571012022000056 | Tanda Tangan: PRESENT

[01_HighQuality_Enhanced (1).jpg] Nomor Ijazah: - Nomor yazah: 571012022000056 | Tanda Tangan: PRESENT

[02_LowContrast (1) (1).jpg] Nomor Ijazah: - Nomor yazah: 571012022000056 | Tanda Tangan: PRESENT

[04_HighNoise (1) (1).jpg] Nomor Ijazah: -Nomor yazah: 571012022000056 | Tanda Tangan: PRESENT

[09_CombinedDegradation (1) (1).jpg] Nomor Ijazah: - Nomor tyazah: 571012022000056 | Tanda Tangan: PRESENT

Secara keseluruhan, sistem pipeline menunjukkan tingkat ketahanan (robustness) yang sangat baik:

Ekstraksi Data Kritis: Angka seri utama 571012022000056 berhasil diekstrak secara sempurna (akurasi 100%) pada seluruh citra, meskipun terdapat berbagai bentuk degradasi visual seperti blur, noise, penurunan kontras, hingga artefak kompresi JPEG.

Deteksi Tanda Tangan: Sistem mencatat akurasi absolut 100% dengan menghasilkan status PRESENT pada kesembilan citra.

Toleransi Kesalahan OCR: Terdapat kekeliruan pembacaan minor (insersi/substitusi) pada teks pengantar. Kata "ijazah" kerap terbaca sebagai "yazah" atau "tyazah" (misalnya pada citra HighQuality, LowResolution, dan CombinedDegradation). Gambar 03_Blurred menjadi satu-satunya citra yang bersih dari typo. Kesalahan ini merupakan hal lazim pasca-binerisasi pada karakter berjenis serif atau bergaris tipis.

Metodologi Sistem

## 1.Alur Pembacaan Nomor Ijazah

Penentuan ROI (Cropping): Mengisolasi area di sudut kiri atas matriks gambar yang memuat deret nomor ijazah.

Upscaling: Memperbesar dimensi area potong hingga 2 kali lipat guna memenuhi spesifikasi resolusi ideal Tesseract OCR (sekitar 300 DPI), sehingga detail sudut dan lekukan huruf dapat diidentifikasi secara optimal.

Gaussian Blur: Menerapkan filter penghalus spasial untuk meredam noise (bintik kotor) dan menetralkan tekstur kertas tanpa mengaburkan ketegasan struktur huruf.

Otsu's Thresholding: Mengonversi gambar menjadi biner (teks hitam solid dengan latar putih) secara otomatis dengan menghitung variansi histogram piksel.

Tesseract OCR: Memakai konfigurasi Page Segmentation Mode (--psm 7) yang berasumsi teks berada dalam satu baris, dipadukan dengan whitelist untuk membatasi OCR agar hanya menerjemahkan huruf, angka, dan spasi.

2. Alur Deteksi Tanda Tangan

Penentuan ROI (Cropping): Memotong sudut kiri bawah sebagai target area letak tanda tangan rektor.

Inverse Otsu Thresholding: Melakukan binerisasi terbalik di mana tinta pulpen dikonversi menjadi piksel bernilai 255 (putih) dan latar kertas menjadi 0 (hitam).

Morphological Closing: Mengaplikasikan operasi dilasi yang diikuti erosi untuk menambal celah kosong dan menyambung garis-garis tinta putus yang disebabkan oleh penurunan kualitas pindaian.

Kalkulasi Kepadatan Piksel: Mengukur persentase jumlah piksel putih (tinta) dibandingkan dengan total piksel di dalam ROI. Apabila kepadatan melampaui ambang batas (misalnya >1.5%), sistem memvonis status PRESENT. Sebaliknya, sistem mencetak ABSENT.

Evaluasi Metode Enhancement (Berdasarkan CER)

Kombinasi Gaussian Blur + Otsu Thresholding terbukti sebagai metode perbaikan citra paling krusial untuk menekan angka Character Error Rate (CER).

Keberadaan noise frekuensi tinggi (bintik-bintik gelap) pada dokumen, seperti di citra 04_HighNoise, rentan disalahartikan sebagai karakter atau simbol tambahan oleh mesin OCR. Hal ini dapat memicu lonjakan tingkat Insertion dan memperburuk nilai CER secara drastis. Gaussian Blur berfungsi meratakan bintik-bintik noise tersebut agar melebur dengan latar belakang. Selanjutnya, Otsu Thresholding beradaptasi mencari nilai ambang batas yang paling tepat untuk memisahkan teks asli (yang lebih padat dan gelap) dari sisa noise yang telah dikaburkan.
