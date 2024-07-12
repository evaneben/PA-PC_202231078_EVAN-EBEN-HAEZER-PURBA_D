# UAS Praktikum PCD - D ( Deteksi Daun)
## A. Teori 

Pengolahan citra digital melibatkan berbagai teknik untuk mengolah dan menganalisis gambar digital. Di antara teknik yang penting adalah deteksi garis, deteksi tepi, masking(mask), dan segmentasi. Berikut adalah penjelasan  untuk masing-masing teknik tersebut:

1. Deteksi Garis (Line Detection):
Deteksi garis adalah proses untuk menemukan garis-garis yang terdapat dalam citra. Beberapa metode yang umum digunakan termasuk transformasi Hough dan metode berbasis gradient seperti metode Sobel.
- Metode sobel ini menggunakan operasi konvolusi dengan kernel Sobel, yang merupakan matriks 3×3 untuk menyoroti perubahan tajam dalam intensitas piksel.

2. Deteksi Tepi (Edge Detection):
Deteksi tepi adalah proses untuk menemukan batas-batas antara objek yang berbeda atau perubahan tajam dalam intensitas citra. Teknik ini penting dalam segmentasi dan analisis citra. Metode yang umum digunakan termasuk metode Sobel, Canny, dan Prewitt.

- Metode Prewitt: Seperti Sobel, metode Prewitt juga menggunakan operasi konvolusi dengan kernel Prewitt, yang membantu mengekstraksi tepi dalam citra.

- Metode Canny: Ini merupakan salah satu pendekatan deteksi tepi yang paling populer dan efektif. Prosesnya meliputi beberapa langkah, seperti filter Gaussian untuk menghaluskan citra, deteksi gradien untuk menemukan kekuatan dan arah perubahan intensitas, non-maximum suppression untuk menghilangkan piksel yang bukan tepi, dan histeresis untuk menentukan piksel tepi akhir.

3. Masking(mask)
Masking melibatkan penggunaan maska untuk membatasi atau memfokuskan proses pengolahan pada area tertentu dari citra. Hal ini sering digunakan untuk menghilangkan bagian-bagian yang tidak diinginkan atau untuk mempertahankan area tertentu dari gambar.

4. Segmentasi
Segmentasi adalah proses membagi citra menjadi beberapa bagian atau segmen yang lebih kecil. Tujuan segmentasi adalah untuk mempermudah analisis lanjutan dengan memisahkan objek dari latar belakang atau untuk mengidentifikasi fitur-fitur penting dalam citra.


## B. Tahapan Penyelesaian Project Deteksi Daun
1) Mempersiapkan gambar yang ingin dideteksi. Disini sudah saya siapkan gambar buah jeruk yang diambil dari hasil kamera sendiri.
2) Membuat file baru dalam Jupyter Notebook dengan nama Deteksi Daun.
3) Mengimport library/modul yang akan digunakan untuk mendeteksi daun.
```
import cv2                      # menjalankan fungsi library open cv
import numpy as np              # menjalankan fungsi array
import matplotlib.pyplot as plt # menampilkan fungsi visualisasi data
```
4) Membuat fungsi display_image()

```
def display_image(title, image):
    plt.figure(figsize=(6, 6))
    plt.title(title)
    plt.imshow(cv2.cvtColor(image, cv2.COLOR_BGR2RGB))
    plt.axis('off')
    plt.show()
```
Fungsi (display_image():) digunakan untuk menampilkan gambar dalam format yang sesuai dengan menggunakan (matplotlib.pyplot). Fungsi ini menerima judul gambar (title) dan gambar itu sendiri (image) dalam format BGR (yang digunakan oleh OpenCV) dan mengonversinya ke RGB untuk tampilan matplotlib.

5) Membaca gambar
```
img = cv2.imread("jeruk.jpg")
```
Mengambil gambar dari file "jeruk.jpg" dan menyimpannya dalam variabel img.

6) Mendefinisikan fungsi untuk membuat mask berdasarkan warna jeruk
```
def mask_jeruk(image):
    # Konversi citra ke HSV
    hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
    
    # Definisikan kisaran warna untuk buah jeruk (disesuaikan dengan warna jeruk)
    lower_orange = np.array([10, 100, 100])
    upper_orange = np.array([30, 255, 255])
    
    # Buat mask berdasarkan kisaran warna
    mask = cv2.inRange(hsv, lower_orange, upper_orange)
    
    # Morfologi untuk memperbaiki mask
    kernel = np.ones((5, 5), np.uint8)
    mask = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, kernel)
    
    return mask
```
Fungsi ini melakukan segmentasi warna jeruk dari gambar. Dimulai dengan mengonversi gambar dari BGR ke HSV untuk ruang warna yang lebih sesuai untuk segmentasi warna. Lalu menentukan kisaran warna jeruk dalam HSV (lower_orange dan upper_orange).
Setelah itu membuat mask menggunakan (cv2.inRange()) untuk mendapatkan bagian yang sesuai dengan kisaran warna.
Dan melakukan operasi morfologi dengan (cv2.morphologyEx()) untuk memperbaiki mask.

7) Membuat fungsi untuk segmentasi buah
```
def segment_jeruk(img, mask):
    # Menggunakan mask untuk mengekstrak buah jeruk
    segmented = cv2.bitwise_and(img, img, mask=mask)
    return segmented
```
Fungsi ini menggunakan mask yang sudah dibuat untuk melakukan segmentasi pada gambar jeruk. (cv2.bitwise_and()) ini digunakan untuk mengaplikasikan mask pada gambar asli (img) dan menghasilkan gambar segmentasi.

8) Mendefinisikan fungsi untuk segmentasi daun dengan deteksi tepi
```
def segment_daun(img):
    # Konversi citra ke HSV untuk segmentasi daun
    hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
    
    # Definisikan kisaran warna hijau untuk daun
    lower_green = np.array([30, 40, 40])
    upper_green = np.array([90, 255, 255])
    
    # Buat mask untuk hijau
    mask = cv2.inRange(hsv, lower_green, upper_green)
    
    # Morfologi untuk memperbaiki mask
    kernel = np.ones((5, 5), np.uint8)
    mask = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, kernel)
    
    # Deteksi tepi pada daun yang telah disegmentasi
    edges = cv2.Canny(mask, 100, 200)
    
    # Menggabungkan tepi dengan hasil segmentasi daun
    daun_segmented = cv2.bitwise_and(img, img, mask=mask)
    edges_colored = cv2.cvtColor(edges, cv2.COLOR_GRAY2BGR)
    daun_segmented_edges = cv2.addWeighted(daun_segmented, 0.8, edges_colored, 0.2, 0)
    
    return daun_segmented, daun_segmented_edges
```
Fungsi ini melakukan segmentasi daun dengan deteksi tepi.
Pertama dengan mengonversi gambar dari BGR ke HSV untuk segmentasi warna daun.
Lalu menentukan kisaran warna hijau untuk daun (lower_green dan upper_green).
Setelah itu membuat mask menggunakan (cv2.inRange()) untuk mendapatkan bagian yang sesuai dengan kisaran warna hijau.
Lakukan operasi morfologi dengan (cv2.morphologyEx()) untuk memperbaiki masker.Gunakan (cv2.Canny()) untuk mendeteksi tepi pada masker.
Kemudian menggabungkan tepi yang terdeteksi dengan gambar segmentasi daun menggunakan (cv2.addWeighted()) untuk visualisasi yang lebih jelas.

9) Menampilkan Hasil 
```
# Membuat mask buah jeruk
jeruk_mask = mask_jeruk(img)

# Melakukan segmentasi buah jeruk
segmented_jeruk = segment_jeruk(img, jeruk_mask)

# Melakukan segmentasi daun dengan deteksi tepi
daun_segmented, daun_segmented_edges = segment_daun(img)

# Menampilkan gambar dalam tata letak
fig, axs = plt.subplots(2, 2, figsize=(9, 9))

# Menyembunyikan sumbu (axis) pada gambar
for ax in axs.ravel():
    ax.axis('on')

# Menampilkan gambar-gambar
axs[0, 0].imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
axs[0, 0].set_title('Gambar Asli')

axs[0, 1].imshow(cv2.cvtColor(jeruk_mask, cv2.COLOR_BGR2RGB))
axs[0, 1].set_title('Mask Jeruk')

axs[1, 0].imshow(cv2.cvtColor(segmented_jeruk, cv2.COLOR_BGR2RGB))
axs[1, 0].set_title('Segmentasi Jeruk')

axs[1, 1].imshow(cv2.cvtColor(daun_segmented_edges, cv2.COLOR_BGR2RGB))
axs[1, 1].set_title('Segmentasi Daun')

plt.tight_layout()
plt.show()

```
Pertama, membuat mask untuk buah jeruk dengan memanggil fungsi mask_jeruk(img), yang menghasilkan sebuah masker yang menandai bagian jeruk dalam gambar img. Mask ini kemudian digunakan untuk melakukan segmentasi pada gambar, dengan cara memanggil fungsi segment_jeruk(img, jeruk_mask), yang menghasilkan gambar hasil segmentasi buah jeruk dan disimpan dalam variabel segmented_jeruk.

Selanjutnya, melakukan segmentasi daun dengan menggunakan deteksi tepi. Untuk ini, perlu memanggil fungsi segment_daun(img), yang menghasilkan dua gambar yaitu daun_segmented (hasil segmentasi daun) dan daun_segmented_edges (hasil segmentasi daun yang dikombinasikan dengan deteksi tepi).

Setelah mendapatkan hasil segmentasi,  menyiapkan tata letak untuk menampilkan gambar-gambar ini. Disini digunakan'plt.subplots()' untuk membuat tata letak dengan dua baris dan dua kolom, dengan ukuran keseluruhan figure sebesar 9x9 inci. Selanjutnya, mengatur properti sumbu pada semua subplot agar ditampilkan dengan loop for yang mengatur ax.axis('on') pada setiap axis dalam tata letak.

Kemudian, menampilkan gambar-gambar tersebut dalam subplot yang telah disiapkan. Gambar asli (img) ditampilkan pada subplot pertama (axs[0, 0]), mask jeruk (jeruk_mask) pada subplot kedua (axs[0, 1]), hasil segmentasi jeruk (segmented_jeruk) pada subplot ketiga (axs[1, 0]), dan hasil segmentasi daun dengan deteksi tepi (daun_segmented_edges) pada subplot keempat (axs[1, 1]). Setiap gambar dikonversi dari format BGR ke RGB menggunakan cv2.cvtColor() agar ditampilkan dengan benar oleh matplotlib.

Terakhir, menggunakan (plt.tight_layout()) ini untuk memastikan tata letak subplot terlihat rapi, dan (plt.show()) untuk menampilkan plot ke layar. 







