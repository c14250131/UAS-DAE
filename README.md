#  **Toyota Corolla Price Analysis – KNIME Workflow**

Analisis harga, eksplorasi data, dan klasifikasi harga mobil menggunakan Decision Tree

##  **Deskripsi Proyek**

Repository/Workflow ini berisi analisis data harga mobil **Toyota Corolla** menggunakan **KNIME Modern UI**. Tujuan proyek:

* Membersihkan & mempersiapkan dataset
* Melakukan exploratory data analysis (EDA)
* Visualisasi variabel penting (price, age, KM, weight, dll.)
* Membuat klasifikasi harga mobil (Low – Medium – High)
* Membuat model machine learning berbasis **Decision Tree**

Workflow disusun modular agar mudah dipahami, cocok untuk pemula KNIME yang ingin mempelajari alur data science dari awal sampai modeling.

---

##  **Isi Workflow**

**Toyota-Corolla-KNIME**
├── `workflow.knwf` → workflow KNIME
├── `ToyotaCorolla.csv` → dataset
├── `README.md` → dokumentasi

---

# **Alur Workflow (Ringkasan)**

**CSV Reader**
→ Statistics
→ Column Filter
→ Missing Value
→ One to Many (Fuel Type)
→ Numeric Binner (Price_binned)
→ Normalizer
→ Rule Engine (Price_Class)
→ Column Filter (final features)
→ Table Partitioner (Train–Test)
→ Decision Tree Learner
→ Decision Tree Predictor
→ Scorer (Evaluasi)

---

# **1. Data Preparation**

### Node yang digunakan:

### **1. CSV Reader**

Mengimpor dataset ToyotaCorolla.csv.

### **2. Statistics**

Memberikan ringkasan numerik awal (min, max, mean, std) untuk Price, Age, KM, Weight, HP, dll.

### **3. Column Filter**

Menghapus kolom yang tidak relevan untuk analisis prediktif:
Id, Model, Mfg_Month, Color, Gears, Cylinders, Mfr_Guarantee, dsb.
Fitur penting yang dipertahankan:
Age_08_04, KM, Fuel_Type, HP, Met_Color, Automatic, CC, Doors, Weight, Price.

<img width="432" height="364" alt="image" src="https://github.com/user-attachments/assets/cf9ced75-ada8-4c3e-bfc9-0a9e7a4aff9e" />


### **4. Missing Value**

* Numerik → **mean**
* String → dibiarkan tidak berubah

Sehingga semua kolom numerik sudah bersih dari nilai hilang.

<img width="818" height="127" alt="image" src="https://github.com/user-attachments/assets/7d0e295c-5a22-48b9-81d1-615e20ae92d9" />


### **5. One to Many (Fuel_Type)**

Mengubah Fuel_Type menjadi dummy variable:

* Diesel
* Petrol
* CNG

Fitur ini sangat penting agar bisa dipakai dalam model machine learning.

### **6. Numeric Binner – Mengelompokkan Harga**

Membuat kolom **Price_binned**:

| Range Harga    | Kelas      |
| -------------- | ---------- |
| ≤ 8.000        | **Low**    |
| 8.000 – 12.000 | **Medium** |
| > 12.000       | **High**   |

Kolom inilah yang menjadi **label utama** untuk klasifikasi Decision Tree.

<img width="279" height="80" alt="image" src="https://github.com/user-attachments/assets/b9abfb22-184f-4706-bb97-2a77503c86d6" />

### **7. Column Filter (final)**

Menghapus Price asli & Fuel_Type asli.
Menjaga fitur yang sudah diproses + Price_binned untuk modeling.

<img width="415" height="276" alt="image" src="https://github.com/user-attachments/assets/45c7f882-5011-4aa7-8aec-e8fd8a999f1e" />

---

# **2. Data Splitting (Train–Test)**

Menggunakan **Table Partitioner** (umumnya 50% train – 50% test).
Dataset training dipakai untuk membangun model Decision Tree, sementara test digunakan untuk evaluasi prediksi.

<img width="447" height="393" alt="image" src="https://github.com/user-attachments/assets/22eac4ac-5d65-4b16-808f-048a2d36fca1" />

---

#  **3. Modeling – Decision Tree**

### **Decision Tree Learner**

* Target (class column): **Price_binned**
* Quality measure: **Gini Index**
* Pruning: Reduced Error Pruning
* Min records per node: 5

Fitur yang digunakan mencakup Age, KM, HP, Weight, dummy Fuel_Type, dan variabel numerik penting lainnya.

<img width="539" height="544" alt="image" src="https://github.com/user-attachments/assets/b01ef058-c679-4edb-9203-3dd77d83c06c" />

### **Decision Tree Predictor**

Menghasilkan kolom baru:
`Prediction (Price_binned)`
yang berisi kelas harga hasil prediksi model.

---

#  **4. Evaluasi Model**

Node **Scorer** menghasilkan:

* Confusion Matrix
* Accuracy
* Precision & Recall untuk masing-masing kelas Low, Medium, High

Model umumnya dapat mengenali kelas Low dan High dengan cukup baik, meskipun kelas Medium biasanya lebih sulit diprediksi karena overlap harga.

<img width="1091" height="163" alt="image" src="https://github.com/user-attachments/assets/c8803325-aded-4903-ac36-925c9d9217bb" />

---

# **5. Insight Utama**

Beberapa temuan dari analisis:

Insight dari Scatter plot HP vs AGE
1. HP tinggi = indikator paling kuat untuk kategori High.
2. Low & Medium sulit dibedakan karena rentang HP sangat overlap.
3. Umur/age tidak berkorelasi dengan HP → faktor lain bisa menaikkan kelas meski HP biasa saja.
4. Medium adalah kelas paling tidak stabil (overlap terbesar).
5. HP dan Age harus dipakai bersama karena tidak punya hubungan langsung.

<img width="1272" height="279" alt="image" src="https://github.com/user-attachments/assets/33d1d8c9-0e7b-4130-8656-b8986c14821e" />

---

Insight dari bar chart Jumlah vs price binned
1. Distribusi kelas tidak seimbang → ini menjelaskan kenapa model lebih bagus di Medium.
2. Medium mendominasi dataset → potensi bias di Decision Tree.
3. High & Low terlalu sedikit → pola sulit dipelajari → skor prediksi kelas ini lebih rendah.
Secara bisnis, pasar Corolla bekas terpusat di harga Medium.

<img width="1262" height="306" alt="image" src="https://github.com/user-attachments/assets/9acc300d-06eb-42a6-b25a-aa483cddf94c" />

---

Insight dari Box Plot Value vs KM
1. Low = KM tinggi → mobil yang sudah dipakai berat → harga turun.
2. Medium = KM sedang dengan variasi stabil → kondisi lebih “normal”.
3. High = KM rendah → mobil jarang dipakai → harga lebih mahal.
4. KM punya pengaruh signifikan terhadap kategori harga, tapi bukan satu-satunya faktor.
   
<img width="1332" height="387" alt="image" src="https://github.com/user-attachments/assets/fd9accc8-b752-4779-b258-b6ccaecadb77" />

---

# **6. Kesimpulan**

Workflow KNIME ini menunjukkan bahwa harga mobil Toyota Corolla dapat dianalisis dengan baik menggunakan alur data preparation → visualisasi → binning → normalisasi → Decision Tree.

Model Decision Tree mampu mengklasifikasikan harga mobil ke dalam tiga kelas dengan performa yang cukup baik, dan memberikan insight jelas bahwa **umur mobil, jarak tempuh, dan tipe bahan bakar** adalah fitur yang paling berpengaruh pada harga mobil bekas.

Workflow ini sederhana, modular, dan mudah dikembangkan lebih lanjut (misalnya dengan Random Forest atau Gradient Boosting).

