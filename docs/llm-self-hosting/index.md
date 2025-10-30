# Spesifikasi Teknologi

## Rekomendasi Berdasarkan Skala Model (Fokus pada VRAM GPU)
| Ukuran Model LLM (Parameter) | Penggunaan | VRAM Minimum (Inference/Running LLM) | VRAM Minimum (Fine-Tuning/QLoRA) | GPU yang Direkomendasikan |
|------------------------------|------------|--------------------------------------|----------------------------------|--------------------------|
| ~7 Miliar (misalnya Llama 2 7B) | Ringan/Eksperimen  | 8 GB - 12 GB | 16 GB - 24 GB | RTX 3060 (12GB), RTX 4070/4090 (24GB), Quadro RTX A4000 (16GB) |
| ~13 Miliar (misalnya Llama 2 13B) | Sedang  | 16 GB - 24 GB | 24 GB - 40 GB | RTX 4090 (24GB), A5000 (24GB), A6000 (48GB) |
| ~34 Miliar (misalnya DeepSeek 34B) | Berat/Profesional  | 40 GB - 50 GB | 48 GB - 80 GB | NVIDIA A6000 (48GB), A100 (40GB/80GB) |
| ~70 Miliar (misalnya Llama 2 70B) | Enterprise/Tingkat Lanjut | 80 GB+ | 80 GB+ (Multi-GPU) | Multi-GPU A100 (80GB) atau H100 |

!!! note

    - Untuk Inference (menjalankan LLM), VRAM yang dibutuhkan adalah sekitar 1.2x hingga 1.6x ukuran model (dalam GB).
    - Untuk Fine-Tuning (Full Fine-Tuning), VRAM yang dibutuhkan jauh lebih besar (hingga 16GB per 1 miliar parameter).
    - Untuk Fine-Tuning yang efisien (QLoRA 4-bit), kebutuhan VRAM dapat sangat ditekan, menjadikannya pilihan realistis untuk GPU konsumen. Misalnya, model 7B hanya membutuhkan sekitar 5GB-9GB VRAM menggunakan QLoRA.

## Spesifikasi Komponen Server Lain
| Komponen | Rekomendasi Minimum (Skala Ringan/Eksperimen) | Rekomendasi Ideal (Skala Sedang/Profesional) | Fungsi |
|----------|-----------------------------------------------|----------------------------------------------|--------|
| RAM | 32 GB DDR4/DDR5 | 64 GB atau 128 GB DDR5 | Mendukung preprocessing data, data batching, dan mencegah out-of-memory pada CPU saat GPU kehabisan VRAM (offloading). |
| CPU | Intel Core i7/i9 (Generasi Terbaru) atau AMD Ryzen 7/9 (Multi-core) | Intel Xeon atau AMD Threadripper (dengan core-count tinggi) | Menangani data loading, preprocessing, dan sistem operasi. Harus memiliki core yang cukup untuk mendukung GPU. |
| Penyimpanan (Storage) | 1 TB NVMe SSD | 2 TB+ NVMe SSD | Kecepatan tinggi untuk loading dataset besar dan checkpoint model, NVMe SSD adalah wajib. |
| Sistem Operasi | Linux (Ubuntu/CentOS) | Linux (Ubuntu) | Paling optimal untuk deep learning dan dukungan driver GPU. |

Tentu. Saya akan memberikan perkiraan harga untuk opsi GPU yang paling relevan untuk *self-hosting* LLM dan *fine-tuning* di Indonesia.

Perkiraan ini didasarkan pada harga pasar *retail* baru dan bekas (jika tersedia), karena harga komponen server seringkali fluktuatif.

---

## 💰 Perkiraan Harga Komponen GPU untuk LLM

Asumsi pada GPU NVIDIA karena dukungan **CUDA (Compute Unified Device Architecture)** dan ketersediaan VRAM yang tinggi, yang krusial untuk LLM.

### Opsi 1: Performa Tinggi & Terbaik (VRAM 24 GB)
Pilihan terbaik untuk *fine-tuning* model hingga 13B parameter (menggunakan QLoRA) dan inferensi model besar.

| Komponen | Spesifikasi Kunci | Status | Perkiraan Harga (IDR) | Catatan |
| :--- | :--- | :--- | :--- | :--- |
| **NVIDIA GeForce RTX 4090** | VRAM: **24 GB** GDDR6X | Baru | **Rp 27.000.000 - Rp 35.000.000+** | GPU konsumen terbaik untuk LLM. Menawarkan keseimbangan harga/performa terbaik untuk VRAM 24 GB. |
| **NVIDIA GeForce RTX 3090** | VRAM: **24 GB** GDDR6X | Bekas | **Rp 15.000.000 - Rp 20.000.000+** | Pilihan bekas yang sangat baik dan masih *powerful* dengan VRAM 24 GB yang sama. |

### Opsi 2: Keseimbangan (VRAM 16 GB - 20 GB)
opsi yang masih mumpuni untuk inferensi model 7B-13B dan *fine-tuning* model 7B dengan QLoRA.

| Komponen | Spesifikasi Kunci | Status | Perkiraan Harga (IDR) | Catatan |
| :--- | :--- | :--- | :--- | :--- |
| **NVIDIA GeForce RTX 4070 Ti SUPER** | VRAM: **16 GB** GDDR6X | Baru | **Rp 13.000.000 - Rp 16.000.000+** | Pilihan modern yang lebih terjangkau dengan VRAM 16 GB. |
| **NVIDIA GeForce RTX 3060** | VRAM: **12 GB** GDDR6 | Baru/Bekas | **Rp 4.000.000 - Rp 6.000.000+** | Pilihan paling ekonomis yang dapat menjalankan LLM 7B terkuantisasi (4-bit/8-bit). |

### Opsi 3: Server/Profesional (VRAM 48 GB+)
Untuk menjalankan dan *fine-tuning* model **di atas 34B parameter** atau untuk kebutuhan performa *enterprise*.

| Komponen | Spesifikasi Kunci | Status | Perkiraan Harga (IDR) | Catatan |
| :--- | :--- | :--- | :--- | :--- |
| **NVIDIA RTX A6000 / A5000** | VRAM: **48 GB / 24 GB** ECC | Baru/Bekas | **Rp 45.000.000+ (A6000)** | Dirancang untuk beban kerja server dan memiliki VRAM sangat besar. Harga dapat bervariasi luas dan umumnya sulit dicari di pasar *retail* biasa. |
| **NVIDIA A100 / H100** | VRAM: **40 GB - 80 GB** HBM2/e | Bekas/Khusus | **Rp 100.000.000++** | GPU tingkat pusat data (data center). Biasanya hanya tersedia melalui distributor server khusus atau penyedia *cloud*. |

---

## 🛠️ Perkiraan Harga Server Lainnya

Untuk kelengkapan server (menggunakan konfigurasi **RTX 4090** sebagai basis):

| Komponen | Spesifikasi yang Direkomendasikan | Perkiraan Harga (IDR) |
| :--- | :--- | :--- |
| **CPU** | AMD Ryzen 9 7900/Intel Core i7 Gen 14th | Rp 6.000.000 - Rp 10.000.000 |
| **Motherboard** | (Sesuai CPU) dengan slot PCIe 4.0 x16 yang kuat | Rp 3.000.000 - Rp 5.000.000 |
| **RAM** | 64 GB DDR5 (2x32GB) | Rp 3.000.000 - Rp 5.000.000 |
| **Penyimpanan** | 2 TB NVMe Gen4 SSD | Rp 1.500.000 - Rp 2.500.000 |
| **PSU (Power Supply)** | 1000W 80+ Platinum/Titanium (Wajib berkualitas tinggi) | Rp 3.000.000 - Rp 5.000.000 |
| **Casing & Cooling** | Server/PC Case dengan *airflow* baik + *Liquid/Air Cooler* | Rp 2.000.000 - Rp 4.000.000 |

---
