# Multi-Scenario Adaptive Traffic Light Crossing Violation Detection System

Sistem deteksi pelanggaran lampu lalu lintas berbasis *computer vision* yang dirancang secara adaptif untuk mengatasi kondisi infrastruktur jalan raya yang bervariasi (seperti marka jalan pudar, ketiadaan lampu lalu lintas, hingga guncangan kamera). Sistem ini secara otomatis menganalisis kondisi lingkungan visual secara *real-time* dan memilih rute pemrosesan melalui salah satu dari tiga skenario deteksi yang paling optimal.

## 🌟 Fitur Utama

* **Quick Scan Module**: Otomasi analisis pada 30 *frame* pertama untuk menilai ketersediaan infrastruktur (menghitung lampu lalu lintas dan *zebra cross*) serta mendeteksi stabilitas kamera menggunakan metode *Lucas-Kanade Optical Flow*.
* **Adaptive Multi-Scenario Architecture**:
    * **Skenario 1 (Complete Infrastructure)**: Melakukan pemetaan lintasan spasial (*spatial-based trajectory mapping*) saat infrastruktur lengkap (lampu & marka terlihat jelas).
    * **Skenario 2 (Partial Infrastructure/Dynamic Camera)**: Menggunakan inferensi zona virtual untuk mendeduksi status lampu dari rasio kendaraan berhenti vs bergerak saat kondisi infrastruktur tidak lengkap atau kamera tidak stabil.
    * **Skenario 3 (Pure Behavioral Consensus)**: Beroperasi 100% tanpa infrastruktur fisik dengan mengandalkan konsensus perilaku kendaraan kolektif (membentuk *persistent virtual anchor line*) untuk mendeteksi pelanggaran.
* **Vehicle Coordinate Stabilization**: Penerapan *Exponential Moving Average* (EMA) filter (koefisien 0.7) untuk menghilangkan *jitter* pada *bounding box* hasil deteksi YOLO agar kalkulasi kecepatan jauh lebih akurat.
* **Automated Evidence Export**: Otomatis meng-*capture frame* pelanggaran lengkap dengan visualisasi kotak merah muda pada pelanggar dan mengemasnya ke dalam format `.zip` (lengkap per ID kendaraan dan *timestamp*).

## 🛠️ Arsitektur Teknologi

* **Deteksi Objek**: Dual YOLOv8 (Model YOLOv8m standar untuk klasifikasi kendaraan & lampu lalu lintas; Model Custom YOLOv8s untuk klasifikasi *zebra cross* dan *stop line*).
* **Pelacakan Multi-Objek (MOT)**: ByteTrack (mengelola lintasan dan penetapan ID dinamis).
* **Klasifikasi Warna**: HSV Color Space Segmentation (tahan terhadap variasi pencahayaan).
* **Library Utama**: Python 3.x, Ultralytics, PyTorch, OpenCV, Numpy, Roboflow.

## 📦 Prasyarat & Instalasi

Secara *default*, lingkungan pengembangan sistem ini menggunakan Kaggle GPU infrastructure (Tesla P100/T4) untuk mencapai stabilitas 24-30 fps. Namun, tahap *inference* juga bisa dieksekusi dengan baik di memori lokal (*local environment*) yang cukup mumpuni, seperti *device* dengan kapasitas RAM 16GB.

1. Clone repository ini:
   ```bash
   git clone [https://github.com/username/repo-kamu.git](https://github.com/username/repo-kamu.git)
   cd repo-kamu
   ```

2. Instal seluruh dependensi yang dibutuhkan:
   ```bash
   pip install ultralytics roboflow opencv-python-headless numpy torch
   ```

## 🚀 Cara Penggunaan

Sistem ini didesain agar sepenuhnya otomatis. Kamu hanya perlu menjalankan skrip Master Code untuk mendeteksi video secara adaptif.

1. Pastikan file model sudah berada di dalam direktori yang benar:
   * `yolov8m.pt` (Bisa diunduh otomatis via library ultralytics)
   * `best.pt` (Hasil *fine-tuning* dataset khusus infrastruktur jalan)
2. Ubah variabel `INPUT_VIDEO_PATH` pada file eksekusi atau Jupyter Notebook mengarah ke lokasi file `.mp4` target.
3. Jalankan *cell* eksekusi Master Code. 
4. **Output**:
   * Sistem akan menampilkan di terminal/console keputusan skenario mana yang dieksekusi.
   * Video hasil proses dengan *overlay* metrik akan tersimpan di dalam folder *working*.
   * Semua foto *capture* pelanggaran akan diekstrak ke dalam `violations/` dan dikompres menjadi file arsip `.zip`.

## 🔬 Evaluasi & Limitasi Saat Ini

Berdasarkan pengujian, akurasi adaptasi modul Quick Scan mencapai 97.9%. Akan tetapi, sistem saat ini masih memiliki beberapa area untuk pengembangan lanjutan:

* **Tailgating Violator Problem (Skenario 3)**: Kendaraan yang mengikuti dengan jarak sangat dekat di belakang pelanggar utama terkadang mewarisi nilai memori inersia, menyebabkan *false negatives*. Pengembangan masa depan akan mencakup *Kinematic Profile Analysis* (turunan *jerk*) untuk memisahkan akselerasi sah dan tidak sah.
* **Y-Coordinate Crossing Bypass**: Pada video dengan fps rendah (<15 fps) atau kendaraan berkecepatan sangat tinggi, resolusi temporal bisa terputus. Solusi ke depan difokuskan pada interpolasi lintasan sub-frame.
* **Akurasi Inferensi saat Kemacetan Parah (Skenario 2)**: Pola pergerakan *stop-and-go* di kemacetan bisa memicu deteksi lampu MERAH *(false positive)*.
