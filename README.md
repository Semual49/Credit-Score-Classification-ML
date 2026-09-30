[README.md](https://github.com/user-attachments/files/32849088/README.md)
# Credit Score Classification

Klasifikasi skor kredit pelanggan dengan Random Forest dan XGBoost, lengkap dengan hyperparameter tuning dan analisis feature importance. Dikerjakan oleh Samuel Christopher.

## Isi Repositori

```
.
├── README.md
├── requirements.txt
├── Credit_Score.ipynb
└── Credis_Score_Dataset_B.csv
```

File CSV harus berada di folder yang sama dengan notebook karena dibaca dengan path relatif. Nama file memakai ejaan `Credis` dan harus dibiarkan apa adanya.

## Dataset

`Credis_Score_Dataset_B.csv`: 50.000 baris, 28 kolom. Berisi profil keuangan pelanggan (usia, pendapatan, jumlah rekening dan kartu, suku bunga, pinjaman, keterlambatan, utang, rasio utilisasi kredit, dan lain-lain) dengan target `Credit_Score`. Data mengandung banyak nilai noise seperti `_`, `NM`, dan `__10000__` yang dibersihkan di notebook.

## Cara Menjalankan

1. Pastikan Python 3.12 terpasang.
2. Buat virtual environment (opsional tapi disarankan):
   ```bash
   python -m venv venv
   source venv/bin/activate      # Windows: venv\Scripts\activate
   ```
3. Pasang dependensi:
   ```bash
   pip install -r requirements.txt
   ```
4. Jalankan Jupyter:
   ```bash
   jupyter lab
   ```
5. Buka `Credit_Score.ipynb`, lalu pilih **Run > Run All Cells**.

## Alur Notebook

1. **Cleaning**: ubah nilai noise menjadi NaN, hapus kolom identitas (`ID`, `Customer_ID`, `Name`, `SSN`), konversi kolom numerik, koreksi nilai tidak logis (`Num_of_Loan` negatif, `Interest_Rate` di atas 25), isi nilai kosong.
2. **Preprocessing**: Label Encoding untuk kategorikal, `StandardScaler` untuk numerik, split train-test 80/20.
3. **Modeling**: `GridSearchCV` 3-fold dengan skor `f1_weighted` untuk Random Forest (`n_estimators`, `max_depth`, `min_samples_split`) dan XGBoost (`n_estimators`, `max_depth`, `learning_rate`).
4. **Hasil di notebook**:

| Model | Accuracy | Precision | Recall | F1-score |
|---|---|---|---|---|
| Random Forest | 0.7541 | 0.7550 | 0.7541 | 0.7545 |
| XGBoost | 0.7570 | 0.7563 | 0.7570 | 0.7564 |

5. **Feature importance**:
   - Random Forest: `Outstanding_Debt`, `Delay_from_due_date`, `Changed_Credit_Limit`.
   - XGBoost: `Payment_of_Min_Amount`, `Credit_Mix`, `Outstanding_Debt`.

## Catatan Versi Pandas

`requirements.txt` membatasi pandas di bawah versi 3.0. Notebook memakai pola `df[col].fillna(..., inplace=True)` dan `select_dtypes(include='object')`. Di pandas 3.0, pola pertama tidak lagi mengubah DataFrame asli dan pola kedua tidak lagi menangkap kolom string, sehingga nilai kosong tidak terisi dan kolom kategorikal tidak terproses. Jangan naikkan versi pandas kecuali kode diperbarui.

## Keterbatasan

- `random_state` tidak diatur pada `train_test_split`, Random Forest, dan XGBoost. Angka evaluasi bisa berbeda tiap run.
- Selisih XGBoost dan Random Forest hanya sekitar 0,003 pada accuracy. Dengan satu kali split dan tanpa seed tetap, selisih ini belum cukup kuat untuk menyimpulkan XGBoost lebih baik.
- Imputasi, encoding, dan scaling dilakukan sebelum split. Ini berpotensi membuat informasi data uji ikut masuk ke proses training (data leakage), sehingga skor bisa sedikit terlalu optimis.
- Label Encoding pada kolom kategorikal nominal memberi urutan buatan. Untuk model berbasis pohon ini biasanya tidak fatal, tapi tetap perlu disadari.
