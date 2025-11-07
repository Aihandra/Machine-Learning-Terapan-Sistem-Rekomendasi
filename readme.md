# Laporan Proyek Machine Learning - Muhammad Raihan Rifki Asdhar

## Project Overview
    Tanaman yang dibudidayakan oleh petani sangat bergantung pada kondisi lingkungan, dan dalam hal ini utamanya terhadap tanah dan kondisi atmosfer (suhu, kelembaban). Olehnya itu, pengetahuan terhadap hal tersebut seringkali menjadi dasar terhadap penentuan tanaman budidaya karena berhubungan dengan efektifitas hasil panen dan efisiensi sumber daya. Project ini memiliki output akhir untuk menentukan jenis tanaman terbaik yang sesuai dengan parameter spesifik seperti pH tanah, nutrisi tanah (N, P, K), suhu, kelembapan, dan curah hujan. 

    Proyek ini bertujuan untuk mengembangkan sistem rekomendasi tanaman berbasis data menggunakan pendekatan similarity-based machine learning. Sistem ini diharapkan dapat membantu dalam memilih tanaman paling sesuai berdasarkan kesamaan karakteristik lahan terhadap data historis.

## Business Understanding

### Problem Statements
    Bagaimana merekomendasikan tanaman yang paling cocok berdasarkan kondisi tanah yang mencakup pH, kadar N-P-K, dan kelembapan?

    Bagaimana sistem dapat menyesuai kan rekomendasi dengan variasi suhu dan kelembapan udara secara efisien?

### Goals
    Mengembangkan sistem rekomendasi tanaman yang mempertimbangkan faktor utama pertanian seperti kandungan nitrogen, fosfor, kalium, pH tanah, suhu, kelembaban, dan kelembapan.

    Memberikan daftar tanaman yang cocok berdasarkan kemiripan dengan data historis dengan pendekatan cosine similarity.

## Data Understanding
    Dataset yang digunakan adalah Crop and Soil Dataset yang tersedia di Kaggle: https://www.kaggle.com/datasets/shankarpriya2913/crop-and-soil-dataset.     Dataset terdiri dari 8000 baris dengan 9 kolom (fitur) yang tergolong cocok untuk digunakan sebagai sumber untuk sistem rekomendasi karena mencakup faktor-faktor utama yang harusnya menjadi pertimbangan dalam menentukan tanaman budidaya.

    Fitur pada Dataset:
| **Fitur**          | **Deskripsi**                                     |
|--------------------|---------------------------------------------------|
| Temparature        | Suhu lingkungan (°C)                              |
| Humidity           | Kelembaban udara (%)                              |
| Moisture           | Kelembapan tanah (%)                              |
| Soil Type          | Jenis tanah (misal: Loamy, Sandy, dll.)           |
| Nitrogen (N)       | Kandungan nitrogen dalam tanah                    |
| Phosphorous (P)    | Kandungan fosfor dalam tanah                      |
| Potassium (K)      | Kandungan kalium dalam tanah                      |
| Crop Type          | Jenis tanaman (label target)                      |
| Fertilizer Name    | Jenis pupuk yang digunakan                        |

    Info tambahan:
        1. Tidak ditemukan perbedaan signifikan terhadap jumlah data pada setiap fitur terhadap distribusi label 'Crop Type', rentangnya diantara 700 s.d. 750 untuk masing-masing label.

        2. Distribusi label tanah tidak jauh berbeda, semua label memiliki jumlah sekitar 1600 data.
        
        3. Terlihat distribusi pada setiap label pupuk berada di rentang 1100 s.d. 1200 jumlah data per label.

## Data Preparation
Langkah-langkah yang dilakukan:

    1. Label Encoding
        Soil Type, Crop Type, dan Fertilizer Name dikonversi dari format string ke integer menggunakan LabelEncoder untuk keperluan komputasi.

    2. Pemisahan Fitur & Label
        Kolom Crop Type dan Fertilizer Name dipisahkan dari data fitur utama.

    3. Standardisasi Data
        Seluruh fitur numerik diskalakan menggunakan StandardScaler untuk menghilangkan bias skala saat menghitung kemiripan menggunakan cosine similarity.

## Modeling
    Model menggunakan similarity-based recommendation system: Algoritma Cosine Similarity

    Mengukur kemiripan antara input pengguna dan data historis. Input pengguna diubah ke bentuk vektor numerik yang telah diskalakan.

    Sistem memberikan tanaman yang memiliki kesesuaian tertinggi sesuai dataset.

    Contoh Input:
    sample_input = {
        'Temparature': 40.0,
        'Humidity': 50.0,
        'Moisture': 50.0,
        'Soil Type': soil_encoder.transform(['Loamy'])[0],
        'Nitrogen': 60.0,
        'Potassium': 45.0,
        'Phosphorous': 30.0}
    Output: array(['Pulses', 'Ground Nuts', 'Cotton', 'Millets', 'Maize'], dtype=object)

    Selanjutnya juga dibuat model dengan algoritma yang sama untuk rekomendasi jenis pupuk. Berikut sampel inputnya:

    sample_input = {
        'Temparature': 40.0,
        'Humidity': 50.0,
        'Moisture': 50.0,
        'Soil Type': soil_encoder.transform(['Loamy'])[0],
        'Nitrogen': 10,
        'Potassium': 45.0,
        'Phosphorous': 30.0}
    Output: array(['28-28', '28-28'], dtype=object)


## Evaluation
    1. Evaluasi Model Rekomendasi Crop Type
        Model rekomendasi Crop Type dievaluasi menggunakan sampel acak dari dataset dengan parameter K=5.

        Precision@5 = 0.20
        Ini berarti rata-rata hanya 20% dari 5 tanaman yang direkomendasikan benar-benar sesuai dengan label target. Model menghasilkan banyak rekomendasi yang kurang relevan.

        Recall@5 = 1.00
        Model selalu berhasil memasukkan tanaman yang benar dalam 5 rekomendasi teratas untuk semua sampel. Ini menandakan model mampu “menangkap” label target di antara hasil rekomendasi.

    2. Evaluasi Model Rekomendasi Fertilizer Name
        Model rekomendasi jenis pupuk dievaluasi dengan sampel acak dan parameter K=2.

        Precision@2 = 0.50
        Rata-rata 50% dari 2 pupuk yang direkomendasikan sesuai dengan label asli pupuk pada data. Model lebih presisi dibanding model crop type, namun masih bisa ditingkatkan.

        Recall@2 = 1.00
        setiap model menyarankan rekomendasi, pasti terdapat 1 hasil yang benar dari 2 saran.

    Meskipun nilai recall pada kedua model cukup tinggi, nilai precision yang masih rendah mengindikasikan bahwa sistem sering memberikan rekomendasi yang kurang relevan di samping rekomendasi yang benar. Hal ini dapat menyebabkan pengguna menerima pilihan yang kurang optimal. Saran untuk meningkatkan akurasinya adalah dengan menambahkan fitur tambahan dan penyesuaian bobot fitur untuk perhitungan kesamaan.

