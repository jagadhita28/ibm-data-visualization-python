# Cheat Sheet Modul 1: Dasar Visualisasi Data

## Apa itu visualisasi data?
Mengubah angka menjadi gambar (grafik) supaya pola dan tren lebih mudah dilihat.

## Matplotlib
Pustaka Python untuk membuat grafik. Tiga lapisannya:
- **Backend**: bagian yang menggambar grafik ke layar atau berkas.
- **Artist**: semua objek di grafik (judul, garis, sumbu).
- **Scripting (pyplot)**: perintah sederhana yang kita ketik, ditulis `plt`.

Istilah penting:
- **Figure**: kanvas besar tempat grafik berada.
- **Axes**: area tempat grafik digambar.
- **Axis**: sumbu x dan y.

## Line plot
- **Fungsi**: menunjukkan perubahan data dari waktu ke waktu.
- **Cocok untuk**: tren dan perbandingan beberapa kategori.

## Kode dasar
```python
import matplotlib.pyplot as plt

df.plot(kind="line", figsize=(14, 8))   # buat grafik dari DataFrame
plt.title("Judul Grafik")
plt.xlabel("Tahun")
plt.ylabel("Jumlah")
plt.savefig("../outputs/nama.png", dpi=150, bbox_inches="tight")   # simpan dulu
plt.show()                                                         # baru tampilkan
```

## Aturan penting
1. Indeks tabel menjadi sumbu x, setiap kolom menjadi satu garis.
2. Gunakan `.transpose()` untuk menukar baris dan kolom jika tahun belum berada di indeks.
3. Ubah tahun menjadi angka dengan `index.map(int)` agar urutannya benar.
4. `savefig()` harus sebelum `show()`, jika tidak gambar tersimpan kosong.

## Kesalahan umum
| Masalah | Solusi |
|---|---|
| Gambar tersimpan kosong | Pindahkan `savefig()` ke atas `show()` |
| Garis salah (per tahun, bukan per negara) | Gunakan `.transpose()` |
| Urutan tahun berantakan | Ubah tahun menjadi integer |
| Gagal menyimpan gambar | Periksa jalur `../outputs/` |