# 🌍 Analisis Emisi GRK EPA GHGRP (2014–2023)
## Arsitektur Big Data Medallion (Bronze → Silver → Gold)

---

## 👥 Anggota Kelompok

| Nama | NIM |
|------|-----|
| Akeyla Fairuz Shafi | 123450119 |
| Keren Marito Lumban Gaol | 123450020 |
| Aliya Ammara Ananta | 123450075 |
| Tarisya Hidayatul Rahmi | 123450052 |
| Rafa Aqilla Jungjunan | 122450142 |

---

## 📌 Tentang Proyek

> *"Mengungkap pola emisi gas rumah kaca industri Amerika Serikat menggunakan Big Data Analytics"*

Proyek ini menganalisis **8.402 fasilitas industri** dari data EPA GHGRP tahun 2014 ~ 2023 dengan total **66.881 baris data** menggunakan arsitektur **Medallion** (Bronze-Silver-Gold).

**🎯 Tujuan:**
- Mengelompokkan fasilitas berdasarkan tingkat emisi (Low / Medium / High)
- Membandingkan performa **Pandas vs PySpark**
- Mendukung **SDG 12** (Produksi dan Konsumsi Bertanggung Jawab)

---

## 🛠️ Tools yang Digunakan

| Teknologi | Fungsi |
|-----------|--------|
| **Python 3.12** | Bahasa utama |
| **Pandas** | Bronze Layer (baca Excel) |
| **PySpark** | Silver & Gold Layer (distributed computing) |
| **Scikit-learn** | Clustering (K-Means, DBSCAN) |
| **Matplotlib** | Visualisasi data |
| **Google Colab** | Environment eksekusi |
| **GitHub** | Version control & kolaborasi |

---

## 🏗️ Arsitektur Pipeline

| Layer | Tools | Aktivitas Utama |
|-----------|-------|-----------------|
| 🥉 **Bronze** | Pandas | Baca Excel → Ekstrak kolom → Simpan Parquet |
| 🥈 **Silver** | PySpark | Cleaning → Transformasi → Imputasi → Partition |
| 🥇 **Gold** | PySpark + Sklearn | Agregasi → K-Means → DBSCAN → Evaluasi |
| 📊 **Visualisasi** | Matplotlib | Grafik evaluasi & analisis emisi |

---

## 📊 Hasil Utama

### 🔍 Clustering K-Means (3 Cluster)

| Cluster | Label | Jumlah | Karakteristik |
|---------|-------|--------|---------------|
| **0** | Medium Emitter | 8,200 | Emisi sedang, dominan CO₂ |
| **1** | High Emitter (Super) | 184 | Emisi sangat tinggi (power plants besar) |
| **2** | Low Emitter (CH₄) | 18 | Emisi CH₄ tinggi (landfill/coal mines) |

### 📈 Top 5 Sektor dengan Emisi Tertinggi

| Sektor | Total Emisi (Miliar Ton CO₂e) |
|--------|------------------------------|
| ⚡ Power Plants | 16.49 |
| 🧱 Minerals | 1.11 |
| 🧪 Chemicals | 1.04 |
| 🗑️ Waste | 0.97 |
| 🛢️ Petroleum & Gas | 0.84 |

---
## ⚙️ Feature Engineering & Algoritma Clustering

### Feature Engineering

Dari data Silver Layer, dilakukan pembuatan fitur tambahan untuk meningkatkan kualitas clustering:

| Fitur | Rumus | Fungsi |
|-------|-------|--------|
| `log_emissions` | log(total_emissions + 1) | Mengatasi distribusi data yang skewed (condong ke kanan) |
| `co2_mean` | Rata-rata CO₂ per fasilitas (2014-2023) | Profil emisi jangka panjang |
| `ch4_mean` | Rata-rata CH₄ per fasilitas (2014-2023) | Profil emisi jangka panjang |
| `n2o_mean` | Rata-rata N₂O per fasilitas (2014-2023) | Profil emisi jangka panjang |

> **Fungsi:** Fitur-fitur ini digunakan sebagai input untuk algoritma clustering K-Means dan DBSCAN.

### DBSCAN (Density-Based Spatial Clustering of Applications with Noise)

DBSCAN adalah algoritma clustering berbasis kepadatan yang **tidak memerlukan jumlah cluster (k) di awal**.

**Parameter yang digunakan:**
- `eps` = 0.0659 (dihitung otomatis dari persentil ke-95 jarak 5 tetangga terdekat)
- `min_samples` = 5

**Kelebihan DBSCAN untuk data emisi:**
- Mampu mendeteksi **outlier** (fasilitas dengan emisi ekstrem) secara otomatis
- Tidak perlu menentukan jumlah cluster di awal
- Cocok untuk data dengan bentuk cluster tidak beraturan

**Hasil DBSCAN:**
- Jumlah cluster: 13
- Jumlah noise (outlier): 362 fasilitas (4.3% dari total)

---

## ⚡ Evaluasi Performa: Pandas vs PySpark

| Metrik | Pandas (Baseline) | PySpark (Medallion) |
|--------|-------------------|---------------------|
| Throughput | 17,520 baris/detik | 1,246 baris/detik |
| Waktu eksekusi | 3.82 detik | 53.42 detik |
| Ukuran data | 6.96 MB (CSV) | 2.10 MB (Parquet) |
| Kompresi | - | **69.9% lebih kecil** |

### 📊 Perbandingan Arsitektur

| Aspek | Pipeline Satu Lapis | Medallion (Proyek Ini) |
|-------|---------------------|------------------------|
| Waktu total | 16.5 detik | ~100 detik |
| Silhouette Score | 0.55 | **0.70** |
| Checkpoint | Tidak ada | Ada per layer |
| Debugging | Sulit | Mudah |
| Kolaborasi tim | Sulit | Mudah |

> **💡 Kesimpulan:** Medallion lebih lambat untuk dataset kecil, tapi **unggul dalam maintainability, debugging, dan skalabilitas** untuk data besar (GB/TB).

---

## 🚀 Cara Menjalankan

### 1️⃣ Persiapan

```bash
# Upload folder data ke Google Drive dengan struktur:
/MyDrive/ABD/
├── data/
│   └── raw/
│       ├── ghgp_data_2014.xlsx
│       ├── ghgp_data_2015.xlsx
│       └── ... (sampai 2023)
```

### 2️⃣ Jalankan Notebook (Berurutan!)

| No | Nama Notebook | Fungsi |
|----|---------------|--------|
| 1 | `bronze_layer.ipynb` | Ingestion & baca Excel |
| 2 | `silver_layer.ipynb` | Cleaning & transformasi |
| 3 | `gold_layer.ipynb` | Clustering K-Means & DBSCAN |
| 4 | `evaluation_visualization.ipynb` | Evaluasi & grafik |

### 3️⃣ Hasil Output

| Folder | Isi |
|--------|-----|
| `/data/bronze/` | `ghgrp_bronze.parquet` |
| `/data/silver/` | `ghgrp_silver.parquet` |
| `/data/gold/` | `ghgrp_gold.parquet` + `ghgrp_gold.csv` |
| `/figures/` | 5 file PNG (grafik) |

### 4️⃣ (Opsional) Pipeline Baseline

| No | Nama Notebook | Fungsi |
|----|---------------|--------|
| 5 | `pipeline_satu_lapis_baseline.ipynb` | Pipeline monolitik (perbandingan) |
