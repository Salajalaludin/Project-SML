# Segmentasi Pelanggan Online Retail II dengan RFM dan Clustering

Proyek ini menganalisis dataset **Online Retail II** dari UCI Machine Learning Repository untuk melakukan segmentasi pelanggan menggunakan pendekatan **RFM** (*Recency, Frequency, Monetary*) dan metode clustering.

## Ringkasan

Analisis dilakukan dengan menggabungkan dua sheet transaksi Online Retail II, membersihkan data, membentuk fitur RFM pada level pelanggan, lalu membandingkan beberapa algoritma clustering untuk memilih hasil segmentasi terbaik berdasarkan metrik validasi internal.

Jumlah cluster optimal yang digunakan adalah:

```text
k = 2
```

Pemilihan `k = 2` didasarkan pada metode **Silhouette**, karena nilai silhouette tertinggi berada pada `k = 2`.

## Dataset

File dataset yang digunakan:

```text
online_retail_II.xlsx
```

Dataset berisi transaksi toko online Inggris pada periode 2009-2011. Dua sheet yang digunakan:

```text
Year 2009-2010
Year 2010-2011
```

Ringkasan jumlah data:

```text
Data awal                  : 1.067.371 transaksi
Setelah cleaning            :   805.549 transaksi
Customer unik setelah clean :     5.878 pelanggan
Data RFM                    :     5.878 pelanggan
```

## Fitur RFM

Fitur yang digunakan untuk clustering:

| Fitur | Deskripsi |
|---|---|
| Recency | Jarak hari sejak transaksi terakhir pelanggan |
| Frequency | Jumlah transaksi unik pelanggan |
| Monetary | Total nilai belanja pelanggan |

Sebelum clustering, fitur RFM distandarisasi menggunakan `scale()`.

## Metode

Metode clustering yang dibandingkan:

| Metode | Keterangan |
|---|---|
| K-Means | Clustering berbasis centroid |
| GMM | Clustering berbasis model probabilistik Gaussian |
| Hierarchical Clustering | Clustering hierarkis dengan average linkage |
| PAM / K-Medoids | Clustering berbasis medoid |
| DBSCAN | Clustering berbasis kepadatan dan deteksi noise |

## Alur Kerja Lengkap

Berikut komponen alur kerja yang perlu dicakup dalam pengembangan analisis clustering, mulai dari preprocessing sampai evaluasi akhir.

### 1. Preprocessing

Tahap preprocessing digunakan untuk memastikan data siap dianalisis dan tidak menghasilkan bias pada proses clustering.

Komponen yang perlu dilakukan:

| Komponen | Tujuan | Fungsi R |
|---|---|---|
| Pemeriksaan missing value | Mengecek nilai kosong pada data | `is.na()`, `colSums(is.na())` |
| Penanganan missing value | Menghapus atau memperbaiki data yang kosong | `filter()`, `na.omit()` |
| Pembersihan transaksi tidak valid | Menghapus transaksi dengan quantity atau harga tidak valid | `filter()` |
| Pembentukan fitur RFM | Mengubah data transaksi menjadi data pelanggan | `group_by()`, `summarise()` |
| Standardisasi fitur | Menyamakan skala Recency, Frequency, dan Monetary | `scale()` |

### 2. Algoritma Clustering yang Dibandingkan

Seluruh algoritma berikut diperlakukan sebagai kandidat metode clustering yang setara. Hasil akhir dipilih berdasarkan tabel validasi:

| Algoritma | Keterangan | Fungsi R |
|---|---|---|
| Hierarchical Clustering | Clustering hierarkis berbasis dendrogram dengan average linkage | `hclust(method = "average")` |
| Partitioning Around Medoids / K-Medoids | Clustering berbasis medoid yang lebih tahan terhadap outlier dibanding K-Means | `pam()` |
| DBSCAN | Clustering berbasis kepadatan dan dapat mendeteksi noise/outlier | `dbscan()` |

### 3. Penentuan Parameter Optimal

Setiap algoritma membutuhkan parameter yang sesuai agar hasil clustering lebih representatif.

| Algoritma | Parameter | Cara Penentuan | Fungsi R |
|---|---|---|---|
| K-Means | Jumlah cluster `k` | Elbow Method dan Silhouette Method | `fviz_nbclust()`, `silhouette()` |
| GMM | Jumlah komponen `G` | BIC dan validasi cluster | `Mclust()` |
| Hierarchical Clustering | Jumlah cluster dari dendrogram | Pemotongan pohon dendrogram | `cutree()` |
| K-Medoids | Jumlah cluster `k` | Silhouette Method | `pam()` |
| DBSCAN | Nilai `eps` dan `minPts` | Plot k-distance | `kNNdistplot()` |

### 4. Validasi Cluster

Validasi cluster digunakan untuk menilai kualitas hasil clustering dan memilih algoritma terbaik.

#### Internal Criteria

Internal criteria digunakan ketika tidak tersedia label asli pada data.

| Metrik | Tujuan | Fungsi R |
|---|---|---|
| Average Silhouette Width | Mengukur seberapa baik objek berada dalam cluster-nya | `silhouette()` |
| Davies-Bouldin Index | Mengukur rasio kedekatan dalam cluster dan pemisahan antar cluster | `index.DB()` |
| Calinski-Harabasz Index | Mengukur rasio varians antar cluster terhadap varians dalam cluster | `cluster.stats()` |
| Shadow Value | Mengevaluasi kualitas pemisahan dan stabilitas cluster | `cluster.stats()` |

#### External Criteria

External criteria hanya dapat digunakan jika tersedia label asli atau kelas referensi.

| Metrik | Tujuan | Fungsi R |
|---|---|---|
| Cluster Accuracy | Mengukur kesesuaian hasil cluster dengan label asli | Perhitungan manual |
| Cluster Purity | Mengukur dominasi label asli pada setiap cluster | Perhitungan manual |
| Rand Index | Mengukur kesamaan antara hasil cluster dan label asli | `extCriteria(metric = "Rand")` |

Pada dataset Online Retail II, label asli segmentasi pelanggan tidak tersedia. Oleh karena itu, evaluasi utama menggunakan **internal criteria**.

### 5. Evaluasi Akhir

Tahap akhir digunakan untuk membandingkan hasil antar-algoritma dan menentukan metode terbaik.

Komponen evaluasi akhir:

| Komponen | Tujuan | Fungsi R |
|---|---|---|
| Tabel kontingensi antar-algoritma | Membandingkan kesesuaian hasil cluster antar metode | `table()` |
| Tabel rangkuman validasi | Membandingkan metrik validasi tiap algoritma | `data.frame()`, `knitr::kable()` |
| Pemilihan algoritma terbaik | Menentukan metode dengan hasil cluster paling baik | Berdasarkan metrik validasi |

Validasi cluster menggunakan:

| Metrik | Kriteria |
|---|---|
| Silhouette Width | Lebih besar lebih baik |
| Davies-Bouldin Index | Lebih kecil lebih baik |
| Calinski-Harabasz Index | Lebih besar lebih baik |

## Hasil Utama

Berdasarkan hasil validasi, algoritma terbaik dipilih dari seluruh kandidat metode clustering menggunakan ranking metrik internal.

Ringkasan hasil validasi:

| Metode | Silhouette | Davies-Bouldin | Calinski-Harabasz |
|---|---:|---:|---:|
| K-Means | 0.4381 | 0.9693 | 4393.74 |
| GMM | 0.2924 | 1.0982 | 1123.55 |

Segmentasi akhir menggunakan `Cluster_Final`, yaitu hasil dari algoritma dengan ranking validasi terbaik. Ranking mempertimbangkan:

| Metrik | Arah yang Diutamakan |
|---|---|
| Silhouette | Lebih tinggi lebih baik |
| Davies-Bouldin | Lebih rendah lebih baik |
| Calinski-Harabasz | Lebih tinggi lebih baik |

## Interpretasi Cluster

| Cluster | Segmen | Deskripsi | Rekomendasi |
|---|---|---|---|
| 1 | Pelanggan Aktif / Bernilai Tinggi | Recency rendah, Frequency tinggi, dan Monetary tinggi | Program loyalitas, rekomendasi personal, dan penawaran eksklusif |
| 2 | Pelanggan Tidak Aktif / Bernilai Rendah | Recency tinggi, Frequency rendah, dan Monetary rendah | Kampanye reaktivasi, diskon terbatas, dan reminder produk relevan |

## Struktur File

```text
.
├── online_retail_II.xlsx
├── online_retail_II_SMLpraUAS.Rmd
├── online_retail_II_SMLpraUAS.ipynb
├── online_retail_II_SMLpraUAS.pdf
└── README.md
```

## Cara Menjalankan

Pastikan package R berikut sudah terpasang:

```r
install.packages(c(
  "readxl",
  "dplyr",
  "lubridate",
  "ggplot2",
  "factoextra",
  "cluster",
  "mclust",
  "fpc",
  "scales",
  "clusterSim",
  "dbscan",
  "clusterCrit",
  "knitr",
  "rmarkdown"
))
```

Render laporan dari RStudio atau terminal R:

```r
rmarkdown::render("online_retail_II_SMLpraUAS.Rmd")
```

Atau jalankan notebook:

```text
online_retail_II_SMLpraUAS.ipynb
```

## Output

Output utama proyek:

```text
online_retail_II_SMLpraUAS.pdf
```

Laporan berisi proses import data, cleaning, pembentukan RFM, penentuan jumlah cluster, validasi cluster, visualisasi, serta interpretasi segmentasi pelanggan.
