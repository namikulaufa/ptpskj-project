# 🔒 Modul Keamanan Threshold Homomorfik untuk Aset Kripto

## Summary

Proyek ini mendemonstrasikan bagaimana teknologi **Fully Homomorphic Encryption (FHE)** dapat digunakan untuk memverifikasi otorisasi aset kripto secara aman, menghilangkan risiko **Single Point of Failure (SPOF)**.

Dalam sistem otorisasi $t$-dari-$n$ (seperti dompet Multi-Signature), Server Komputasi biasanya harus melihat status kunci (*plaintext*) untuk menghitung apakah ambang batas ($t$) sudah tercapai. **Tujuan kami** adalah membuat Server Komputasi (yang tidak tepercaya) dapat menjumlahkan status kunci dan membandingkannya dengan ambang batas ($t$) **tanpa pernah mendekripsi** *private key* atau status validitasnya.

---

## Prequisites

| Prasyarat | Keterangan |
| :--- | :--- |
| **Sistem Operasi** | Linux atau macOS (disarankan). |
| **Compiler** | GCC/G++ modern (minimal versi 9 atau 10). |
| **Build System** | CMake (minimal versi 3.14). |
| **Pustaka Kriptografi** | **OpenFHE Library** (Wajib diinstal dan di-*link* agar CMake berhasil). |

---

## Struktur Kode dan Implementasi Kunci

Kode utama yang dianalisis adalah `threshold_fhe_module.cpp`.

### 1. Alur Logika (Tiga Pihak)

Kode ini mensimulasikan otorisasi $t$-dari-$n$ melalui empat tahap utama:

| Tahap | Keterangan | Fungsi Kritis |
| :--- | :--- | :--- |
| **SETUP** | Entitas Otorisasi menghasilkan kunci FHE dan mengenkripsi ambang batas ($C_t$). | $\text{KeyGen}$, $\text{Encrypt}(t)$ |
| **KLIEN** | Klien (Pemegang Kunci) mengenkripsi status validitasnya ($V_i=1$ atau $0$) menjadi $C_i$. | $\text{Encrypt}(V_i)$ |
| **SERVER** | Server Komputasi menjalankan agregasi dan komparasi pada data terenkripsi. | $\text{EvalAdd}$, $\text{EvalCompareThreshold}$ |
| **OTORISASI** | Entitas Otorisasi mendekripsi hasil akhir ($C_{result}$) untuk mendapatkan status otorisasi ($1$ atau $0$). | $\text{Decrypt}$ |

### 2. Fokus Fungsional

* **Agregasi Homomorfik ($\text{EvalAdd}$):** Dilakukan untuk menjumlahkan semua status kunci terenkripsi ($\sum C_i$).
* **Komparasi Threshold ($\text{EvalCompareThreshold}$):** Inti keamanan proyek. Fungsi ini diimplementasikan menggunakan operasi FHE yang berulang ($\text{EvalMult}$) untuk mensimulasikan perbandingan non-linear.
* Kode menggunakan `std::chrono` untuk mengukur **latensi** (waktu komputasi) pada operasi `EvalAdd` dan `EvalCompareThreshold`. Hasil pengukuran ini menjadi data empiris untuk menganalisis *trade-off* antara keamanan FHE yang sempurna dan *overhead* komputasi.

---

## Cara Menjalankan (*Compile* & *Run*)

Asumsikan Anda berada di direktori proyek (`/Users/ptpskj-project`) dan pustaka **OpenFHE telah terinstal dengan benar** serta file `CMakeLists.txt` sudah tersedia.

### 1. Pindah ke Direktori Proyek dan Siapkan *Build*

```bash
cd /Users/ptpskj-project
mkdir build
cd build

```

### 2. Konfigurasi CMake

Perintah ini akan mencari instalasi OpenFHE Anda dan membuat *Makefile* yang diperlukan.

```bash
cmake ..

```

### 3. Kompilasi Program

Perintah ini mengompilasi kode C++ menjadi program yang dapat dieksekusi.

```bash
make

```

#### 4. Eksekusi Program

Jalankan program yang telah dikompilasi
```bash
./fhe_module

```

### Output Program
Program akan menampilkan log tahapan FHE, latensi yang terukur (waktu komputasi dalam mikro/milidetik), dan hasil otorisasi (BERHASIL atau GAGAL) berdasarkan skenario simulasi yang telah di-hardcode dalam kode sumber.
