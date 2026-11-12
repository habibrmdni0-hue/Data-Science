# Analisis Faktor yang Berhubungan dengan Math Score Siswa

Proyek latihan EDA, uji statistik, dan pemodelan regresi pada dataset *Students Performance in Exams* dari Kaggle. Tujuannya melatih alur kerja data science dari ujung ke ujung: pertanyaan, EDA, uji statistik, baseline, model, validasi, dan kesimpulan.

## Pertanyaan

Faktor apa yang berhubungan dengan math score siswa, dan seberapa baik math score bisa diprediksi dari data latar belakang dan test preparation course?

## Dataset

[Students Performance in Exams (Kaggle)](https://www.kaggle.com/datasets/spscientist/students-performance-in-exams): 1.000 siswa dan 8 kolom. Untuk lisensi dan ketentuan penggunaan, lihat halaman dataset di Kaggle. File CSV tidak disertakan di repositori ini.

| Kolom | Keterangan |
|---|---|
| gender | jenis kelamin |
| race/ethnicity | kelompok A sampai E (dianonimkan) |
| parental level of education | pendidikan orang tua |
| lunch | standard atau free/reduced |
| test preparation course | none atau completed |
| math score, reading score, writing score | nilai ujian (0-100) |

## Temuan utama

**EDA dan uji statistik**

| Perbandingan | Rata-rata math score | Welch t | Cohen's d |
|---|---|---|---|
| lunch: standard vs free/reduced | 70,0 vs 58,9 | 11,48 (p < 0,0001) | 0,78 |
| test prep: completed vs none | 69,7 vs 64,1 | 5,79 (p < 0,0001) | 0,38 |

- Hasil Mann-Whitney U searah dengan Welch t-test pada kedua perbandingan.
- Selisih `lunch` (sekitar 11 poin) tetap ada di semua tingkat pendidikan orang tua (selisih 8 sampai 14,5 poin) dan di kedua kelompok test prep. Selisih `test prep` (sekitar 6 poin) tetap ada di kedua jenis lunch. Jadi keduanya bukan artefak faktor lain.
- Math, reading, dan writing score saling berkorelasi kuat (math-reading 0,82, math-writing 0,80, reading-writing 0,95).
- Outlier (metode IQR) ada pada beberapa siswa bernilai rendah. Outlier dipertahankan karena merupakan nilai yang wajar (0-100), bukan kesalahan data.

**Pemodelan (target: math score, 5-fold cross-validation)**

| Model | Fitur | R² rata-rata |
|---|---|---|
| Baseline (tebak rata-rata) | - | sekitar 0 (MAE 12,34 pada satu pembagian data) |
| Linear Regression | data latar belakang + test prep | 0,22 (rentang 0,18 sampai 0,27) |
| Random Forest | data latar belakang + test prep | 0,18 |
| Linear Regression | + reading dan writing score | 0,87 (rentang 0,86 sampai 0,88) |

- Data latar belakang dan test prep hanya menjelaskan sekitar seperlima variasi math score.
- Random Forest tidak lebih baik dari regresi linear, sejalan dengan temuan bahwa efek faktor-faktor tersebut bersifat menjumlah tanpa interaksi berarti.
- R² 0,87 dengan reading dan writing score terlihat tinggi karena keduanya adalah nilai ujian lain yang sangat mirip dengan target, bukan karena modelnya jauh lebih cerdas.

## Batasan

- Semua hubungan bersifat asosiasi, bukan sebab-akibat. Misalnya, `lunch` kemungkinan mencerminkan kondisi ekonomi keluarga.
- Arti koefisien bergantung pada variabel lain di model. Tanda koefisien test prep berbalik saat reading dan writing score dimasukkan, jadi koefisien tidak boleh dibaca sebagai efek sebab-akibat.
- Kelompok kecil (misalnya orang tua berpendidikan master, 59 siswa) menghasilkan angka yang kurang stabil.
- Label race/ethnicity pada dataset sudah dianonimkan dan tidak boleh ditafsirkan lebih jauh.

## Isi repositori

```
├── eda_students.ipynb   # notebook utama: EDA, uji statistik, pemodelan
├── requirements.txt     # daftar pustaka beserta versinya
└── README.md
```

## Cara menjalankan

1. Unduh dataset dari tautan Kaggle di atas, lalu taruh `StudentsPerformance.csv` di folder yang sama dengan notebook.
2. Pasang pustaka: `pip install -r requirements.txt` (diuji dengan Python 3.14).
3. Buka `eda_students.ipynb` dan jalankan semua cell dari atas ke bawah.

## Pustaka

pandas, numpy, matplotlib, seaborn, scipy, scikit-learn.
