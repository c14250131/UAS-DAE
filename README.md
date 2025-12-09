# 🚗 **Toyota Corolla Price Analysis – KNIME Workflow**

Analisis harga, eksplorasi data, dan klasifikasi harga mobil menggunakan Decision Tree

## 📌 **Deskripsi Proyek**

Repository/Workflow ini berisi analisis data harga mobil **Toyota Corolla** menggunakan **KNIME Modern UI**. Tujuan proyek:

* Membersihkan & mempersiapkan dataset
* Melakukan exploratory data analysis (EDA)
* Visualisasi variabel penting (price, age, KM, weight, dll.)
* Membuat klasifikasi harga mobil (Low – Medium – High)
* Membuat model machine learning berbasis **Decision Tree**

Workflow disusun modular agar mudah dipahami, cocok untuk pemula KNIME yang ingin mempelajari alur data science dari awal sampai modeling.

---

## 📁 **Isi Workflow**

📦 **Toyota-Corolla-KNIME**
├── `workflow.knwf` → workflow KNIME
├── `ToyotaCorolla.csv` → dataset
├── `README.md` → dokumentasi

---

# 🧠 **Alur Workflow (Ringkasan)**

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

# 🥼 **1. Data Preparation**

### ✔ Node yang digunakan:

### **1. CSV Reader**

Mengimpor dataset ToyotaCorolla.csv.

### **2. Statistics**

Memberikan ringkasan numerik awal (min, max, mean, std) untuk Price, Age, KM, Weight, HP, dll.

### **3. Column Filter**

Menghapus kolom yang tidak relevan untuk analisis prediktif:
Id, Model, Mfg_Month, Color, Gears, Cylinders, Mfr_Guarantee, dsb.
Fitur penting yang dipertahankan:
Age_08_04, KM, Fuel_Type, HP, Met_Color, Automatic, CC, Doors, Weight, Price.

### **4. Missing Value**

* Numerik → **mean**
* String → dibiarkan tidak berubah

Sehingga semua kolom numerik sudah bersih dari nilai hilang.

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

### **7. Row Filter (opsional)**

Menghapus mobil dengan **KM ≥ 250.000** untuk mengurangi outlier ekstrem.

### **8. Normalizer**

Seluruh kolom numerik dinormalisasi, sehingga perbedaan skala tidak memengaruhi model.

### **9. Rule Engine – Price_Class**

Label tambahan berbasis aturan sederhana:

```
Price > 12000 => "High"
TRUE => "Low"
```

Kelas ini alternatif jika diperlukan analisis biner.

### **10. Column Filter (final)**

Menghapus Price asli & Fuel_Type asli.
Menjaga fitur yang sudah diproses + Price_binned untuk modeling.

---

# ⚙️ **2. Data Splitting (Train–Test)**

Menggunakan **Table Partitioner** (umumnya 70% train – 30% test).
Dataset training dipakai untuk membangun model Decision Tree, sementara test digunakan untuk evaluasi prediksi.

---

# 🧩 **3. Modeling – Decision Tree**

### **Decision Tree Learner**

* Target (class column): **Price_binned**
* Quality measure: **Gini Index**
* Pruning: Reduced Error Pruning
* Min records per node: 5

Fitur yang digunakan mencakup Age, KM, HP, Weight, dummy Fuel_Type, dan variabel numerik penting lainnya.

### **Decision Tree Predictor**

Menghasilkan kolom baru:
`Prediction (Price_binned)`
yang berisi kelas harga hasil prediksi model.

---

# 📊 **4. Evaluasi Model**

Node **Scorer** menghasilkan:

* Confusion Matrix
* Accuracy
* Precision & Recall untuk masing-masing kelas Low, Medium, High

Model umumnya dapat mengenali kelas Low dan High dengan cukup baik, meskipun kelas Medium biasanya lebih sulit diprediksi karena overlap harga.

---

# 📈 **5. Insight Utama**

Beberapa temuan dari analisis:

* **Age_08_04 dan KM** adalah dua faktor paling dominan memengaruhi harga.
* Semakin tua umur mobil, harga turun signifikan.
* Mobil dengan **KM tinggi** cenderung masuk kategori Low.
* **Fuel Type Diesel** sering berada di kategori harga lebih tinggi dibanding Petrol.
* **Weight dan HP** terlihat berpengaruh terhadap pemisahan kelas Medium/High.
* Sebagian besar mobil berada pada kelas **Low – Medium**, hanya sebagian kecil masuk kategori High.

---

# 🏁 **6. Kesimpulan**

Workflow KNIME ini menunjukkan bahwa harga mobil Toyota Corolla dapat dianalisis dengan baik menggunakan alur data preparation → visualisasi → binning → normalisasi → Decision Tree.

Model Decision Tree mampu mengklasifikasikan harga mobil ke dalam tiga kelas dengan performa yang cukup baik, dan memberikan insight jelas bahwa **umur mobil, jarak tempuh, dan tipe bahan bakar** adalah fitur yang paling berpengaruh pada harga mobil bekas.

Workflow ini sederhana, modular, dan mudah dikembangkan lebih lanjut (misalnya dengan Random Forest atau Gradient Boosting).
