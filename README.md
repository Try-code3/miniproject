NAMA : TRIYOGA PRASETYA
NIM : F1G124079
KELAS : A

# Mini-Project: Deteksi Tanda Tangan (Signature Detection)

Proyek ini merupakan implementasi sederhana dari *Document Image Analysis (DIA)* untuk memverifikasi secara otomatis keberadaan tanda tangan pimpinan (Dekan) pada dokumen resmi (Ijazah) menggunakan OpenCV dan Python.

## Alur Pemrosesan (Pipeline)
1. **Region of Interest (ROI) Cropping:** Memotong area spesifik pada dokumen tempat tanda tangan seharusnya berada. Pemotongan dilakukan menggunakan pendekatan proporsi/persentase resolusi (Area Kanan Atas) agar adaptif terhadap berbagai ukuran gambar.
2. **Grayscale Conversion:** Mengubah citra warna (RGB) menjadi citra keabuan.
3. **Thresholding (Otsu & Global):** Mengubah citra menjadi biner (hitam-putih) untuk memisahkan goresan tinta (*foreground*) dari kertas (*background*). Otsu dipilih karena kemampuannya beradaptasi secara otomatis terhadap distribusi piksel pada gambar.
4. **Morphological Operations:** 
   - *Opening* (Erosi -> Dilasi) untuk menghilangkan *noise* atau titik-titik kotoran pada kertas.
   - *Closing* (Dilasi -> Erosi) untuk menyambungkan goresan tinta tanda tangan yang terputus akibat kualitas pindaian.
5. **Pixel Ratio Calculation:** Menghitung persentase piksel putih (*foreground*) terhadap total piksel area. Jika melebihi batas *threshold* rasio sebesar 1.5%, sistem menyimpulkan `SIGNATURE PRESENT`.

## Hasil Pengujian
Sistem diuji menggunakan citra beresolusi tinggi (2481x3506 piksel) yang merepresentasikan berbagai kondisi degradasi dokumen di dunia nyata.

Dari hasil eksekusi program, diperoleh data sebagai berikut:
- `01_HighQuality_Enhanced`: 7.41% (PRESENT)
- `02_LowContrast`: 7.50% (PRESENT)
- `03_Blurred`: 11.69% (PRESENT)
- `04_HighNoise`: 7.16% (PRESENT)
- `05_LowResolution_Upsampled`: 9.20% (PRESENT)
- `06_Faded_Underexposed`: 7.63% (PRESENT)
- `07_ColorShift_WarmTint`: 7.52% (PRESENT)
- `08_JPEGCompression_Artifacts`: 7.72% (PRESENT)
- `09_CombinedDegradation`: 8.81% (PRESENT)

## Analisis & Kesimpulan

**1. Mengapa thresholding diperlukan sebelum melakukan analisis keberadaan tanda tangan?**
Komputer tidak mengenali bentuk tanda tangan layaknya manusia; ia hanya melihat rentang nilai matriks piksel (0-255). Proses *thresholding* mutlak diperlukan untuk mengubah citra *grayscale* menjadi citra biner secara tegas. Dengan mengeliminasi elemen visual lain dan hanya menyisakan warna hitam (kertas) dan putih (tinta), sistem dapat mengkalkulasi luasan piksel objek (*foreground*) secara matematis untuk mengambil keputusan.

**2. Apa masalah yang terjadi jika threshold terlalu tinggi atau terlalu rendah?**
- **Jika Threshold Terlalu Rendah:** Sistem akan mengabaikan piksel-piksel yang tidak terlalu gelap. Goresan tinta tanda tangan yang tipis, pudar (seperti pada sampel *Faded/Underexposed*), atau tertulis dengan pulpen terang akan dianggap sebagai kertas kosong. Ini menyebabkan *False Negative* (Tanda tangan ada, namun dianggap tidak ada).
- **Jika Threshold Terlalu Tinggi:** Sistem menjadi terlalu sensitif. Elemen latar belakang seperti pola watermark ijazah, tekstur kertas, bayangan, atau *noise* akan dideteksi sebagai coretan tinta. Hal ini menyebabkan sistem mendeteksi keberadaan tanda tangan pada dokumen yang aslinya kosong (*False Positive*).
- **Solusi yang Diterapkan:** Penggunaan Otsu Thresholding dan perhitungan luasan ROI berbasis persentase terbukti efektif dalam memisahkan pola watermark/noise dari coretan tinta utama, menghasilkan deteksi yang stabil di kisaran 7-11% terlepas dari kualitas degradasi gambar.
