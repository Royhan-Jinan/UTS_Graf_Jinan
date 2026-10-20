# UTS Graf Sains Data: Rekomendasi Produk Berbasis Graf dengan Neo4j

Repositori ini berisi hasil pengerjaan Ujian Tengah Semester mata kuliah Graf Sains Data, Program Studi S1 Sains Data, Universitas Negeri Surabaya. Tugas ini membangun sistem rekomendasi produk dari data interaksi pengunjung e-commerce yang dimodelkan sebagai graf di Neo4j dan diolah menggunakan Python.

## Dataset

Data yang digunakan adalah RetailRocket E-commerce Dataset (berkas `events.csv`, sekitar 2,76 juta baris) yang memuat tiga jenis interaksi: `view`, `addtocart`, dan `transaction`. Data dibagi menjadi dua periode:

| Periode | Rentang tanggal | Fungsi | Jumlah baris |
|---|---|---|---|
| P1 | 14 Juni 2015 - 27 Juni 2015 | Training (pembentukan graf) | 284.624 |
| P2 | 28 Juni 2015 - 4 Juli 2015 | Testing (ground truth evaluasi) | 140.678 |

Berkas yang disertakan dalam repositori hanya `events_p1.csv` dan `events_p2.csv`. Berkas asli `events.csv` tidak disertakan karena ukurannya besar. Dataset lengkap dapat diunduh dari Kaggle: https://www.kaggle.com/datasets/retailrocket/ecommerce-dataset

## Isi Pengerjaan

**Bagian 1. Tugas Pendahuluan**
- Penggunaan driver Neo4j untuk Python (`execute_query`, `RoutingControl`).
- Impor data ke Neo4j dengan mekanisme batch (5.000 baris per batch) menggunakan Cypher `UNWIND` dan `MERGE`.

**Bagian 2.1. Wrangling dan EDA**
- Penyaringan data berdasarkan `timestamp` menjadi periode P1 dan P2.
- Pembentukan graf: 147.816 simpul `Visitor`, 76.702 simpul `Item`, dengan relasi `VIEWED`, `ADDED_TO_CART`, dan `BOUGHT`.
- Vector Space Modelling untuk User dan Item berupa matriks sparse dengan bobot interaksi (`VIEWED` = 1, `ADDED_TO_CART` = 3, `BOUGHT` = 5).
- Perbandingan proyeksi User-User dan Item-Item dari sisi jumlah edge, density, waktu komputasi, dan penggunaan memori.
- Perhitungan cosine similarity pada proyeksi User-User dengan KNN (k = 5), lalu hasilnya disimpan sebagai relasi `SIMILAR` pada graf.
- Visualisasi graf sebelum dan sesudah perhitungan similarity.

**Bagian 2.2. Analytics**
- Pemilihan user acak yang dapat direproduksi (seed 42).
- Rekomendasi item berbasis KNN (k = 5) dan penentuan tiga item yang diiklankan.
- Evaluasi dengan metrik Hit@5 dan NDCG@5 terhadap data P2.


## Cara Menjalankan

1. Buat dan aktifkan virtual environment, lalu pasang dependensi:
```bash
   python -m venv myenv
   source myenv/bin/activate        # Windows: myenv\Scripts\activate
   pip install -r requirements.txt
```
2. Jalankan Neo4j (misalnya melalui Neo4j Desktop) dan pastikan layanan aktif pada `neo4j://127.0.0.1:7687`.
3. Atur kata sandi Neo4j melalui environment variable, tanpa menuliskannya di dalam notebook:
```bash
   export NEO4J_PASSWORD="kata_sandi_anda"     # Windows PowerShell: $env:NEO4J_PASSWORD="kata_sandi_anda"
```
4. Kosongkan database sebelum impor agar graf hanya berisi data P1:
```cypher
   MATCH (n) DETACH DELETE n;
```
5. Buka `UTS_GRAF_JINAN.ipynb`, lalu jalankan seluruh sel secara berurutan.

## Catatan

- Relasi pada proses impor dibuat dengan `CREATE`, sehingga menjalankan impor dua kali tanpa mengosongkan database akan menggandakan relasi.
- Nilai Hit@5 dan NDCG@5 dihitung untuk satu user acak, sehingga tidak menggambarkan performa model secara keseluruhan.

## Identitas

- Nama: Jinan
- Mata kuliah: Graf Sains Data
- Program Studi S1 Sains Data, Fakultas Matematika dan Ilmu Pengetahuan Alam, Universitas Negeri Surabaya
