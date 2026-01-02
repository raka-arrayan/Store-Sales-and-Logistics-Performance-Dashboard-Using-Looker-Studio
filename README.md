# Store Sales and Logistics Performance Dashboard Using Looker Studio

Dashboard ini dirancang untuk menganalisis **kinerja penjualan dan logistik** pada sebuah retail store (SuperStore). Fokus utama dashboard mencakup metrik penjualan, profitabilitas, performa produk, serta efisiensi pengiriman.

---

## Sales Performance Metrics

### 1. Sales

**Deskripsi:**
Jumlah total nilai penjualan yang diperoleh dari seluruh transaksi.

**Metric:**

```
SUM(Sales)
```

---

### 2. Total Order

**Deskripsi:**
Jumlah keseluruhan pesanan unik yang dilakukan oleh pelanggan.

**Metric:**

```
COUNT(DISTINCT Order ID)
```

---

### 3. Profit

**Deskripsi:**
Jumlah total keuntungan yang diperoleh dari seluruh transaksi.

**Metric:**

```
SUM(Profit)
```

---

### 4. Profit Margin

**Deskripsi:**
Persentase keuntungan terhadap total penjualan untuk mengukur efisiensi perusahaan dalam menghasilkan laba.

**Metric:**

```
SUM(Profit) / SUM(Sales)
```

---

### 5. Average Order Value (AOV)

**Deskripsi:**
Rata-rata nilai penjualan dari setiap pesanan.

**Metric:**

```
SUM(Sales) / COUNT(DISTINCT Order ID)
```

---

### 6. Return Rate

**Deskripsi:**
Persentase pesanan yang dikembalikan oleh pelanggan dari total pesanan.

**Formula:**

```
COUNT(DISTINCT (
  CASE
    WHEN Returned = 'Yes' THEN Order ID
    ELSE NULL
  END
)) / COUNT(DISTINCT Order ID)
```

---

### 7. Percentage Increase (Parameter)

**Deskripsi:**
Parameter persentase kenaikan penjualan yang digunakan untuk simulasi target.

**Metric:**

```
percentage_increase
```

Contoh: `0.10 = 10%`

---

### 8. Sales Target

**Deskripsi:**
Target penjualan berdasarkan total penjualan aktual yang disesuaikan dengan persentase kenaikan.

**Metric:**

```
(1 + percentage_increase) × SUM(Sales)
```

---

## Sales Analysis Visualizations

### 9. Sales by Segment

**Deskripsi:**
Distribusi total penjualan berdasarkan segmen pelanggan.

* **Chart Type:** Pie Chart
* **Dimension:** Segment
* **Metric:** `SUM(Sales)`

---

### 10. Sales Over Time by Category

**Deskripsi:**
Tren penjualan dari waktu ke waktu berdasarkan kategori produk.

* **Chart Type:** Time Series Chart
* **X-Axis:** Order Date
* **Breakdown Dimension:** Category
* **Y-Axis Metric:** `SUM(Sales)`

---

### 11. Sales by Profit Segment

**Deskripsi:**
Distribusi total penjualan dan jumlah pesanan berdasarkan segmen profit margin.

**Profit Segment Definition:**

* High Profit : Profit Margin ≥ 0.5
* Medium Profit : 0.25 ≤ Profit Margin < 0.5
* Loss : Profit Margin < 0

**Profit Margin (V2) Formula:**

```
SUM(Profit) / SUM(Sales)
```

* **Chart Type:** Vertical Bar Chart
* **X-Axis:** Profit Segment V2
* **Y-Axis Metrics:**

  * Total Order → `COUNT(DISTINCT Order ID)`
  * Sales → `SUM(Sales)`

---

### 12. Detail Sales of Product

**Deskripsi:**
Menampilkan rincian performa penjualan setiap produk untuk analisis mendalam.

* **Chart Type:** Table

* **Dimensions:**

  * Category (Drill Down)
  * Sub Category
  * Product Name

* **Metrics:**

  * Quantity → `SUM(Quantity)`
  * Sales → `SUM(Sales)`
  * Discount → `SUM(Discount)`
  * Profit → `SUM(Profit)`

**Drill Down:**
Category → Sub Category → Product Name

---

## Logistics & Shipping Performance

### 13. Avg Shipping Processing Time

**Deskripsi:**
Rata-rata waktu pemrosesan pengiriman dari tanggal pemesanan hingga pengiriman.

* **Chart Type:** Scoreboard
* **Metric:**

```
AVG(DATE_DIFF(Ship Date, Order Date))
```

---

### 14. On-Time Shipping Rate

**Deskripsi:**
Persentase pengiriman tepat waktu berdasarkan Ship Mode dan waktu pemrosesan.

* **Chart Type:** Scoreboard

**Metric:**

```
SUM(
  CASE
    WHEN "On Time/Delay Status" = 'On-Time' THEN 1
    ELSE 0
  END
) / COUNT("On Time/Delay Status")
```

**On Time / Delay Status Formula:**

```
CASE
  WHEN Ship Mode IN ('First Class','Same Day')
       AND DATE_DIFF(Ship Date, Order Date) <= 2 THEN 'On-Time'
  WHEN Ship Mode IN ('First Class','Same Day')
       AND DATE_DIFF(Ship Date, Order Date) > 2 THEN 'Delayed'
  WHEN Ship Mode IN ('Second Class','Standard Class')
       AND DATE_DIFF(Ship Date, Order Date) <= 4 THEN 'On-Time'
  WHEN Ship Mode IN ('Second Class','Standard Class')
       AND DATE_DIFF(Ship Date, Order Date) > 4 THEN 'Delayed'
  ELSE NULL
END
```

---

### 15. Delayed Shipping Rate

**Deskripsi:**
Persentase pengiriman yang mengalami keterlambatan.

* **Chart Type:** Scoreboard

**Metric:**

```
SUM(
  CASE
    WHEN "On Time/Delay Status" = 'Delayed' THEN 1
    ELSE 0
  END
) / COUNT("On Time/Delay Status")
```

---

### 16. Shipping Processing Time by Ship Mode

**Deskripsi:**
Perbandingan rata-rata waktu pemrosesan pengiriman berdasarkan jenis layanan.

* **Chart Type:** Line Chart
* **X-Axis:** Ship Mode
* **Y-Axis Metric:**

```
AVG(DATE_DIFF(Ship Date, Order Date))
```

---

### 17. Order ID by Ship Mode and On Time/Delay Status

**Deskripsi:**
Distribusi jumlah pesanan berdasarkan Ship Mode dan status pengiriman.

* **Chart Type:** Stacked Bar Chart
* **Y-Axis:** Ship Mode
* **X-Axis Metric:** `COUNT(DISTINCT Order ID)`
* **Stacked Dimension:** On-Time, Delayed

---

### 18. SuperStore Pivot Table

**Deskripsi:**
Analisis waktu pemrosesan pengiriman berdasarkan Ship Mode dan Region.

* **Chart Type:** Pivot Table
* **Row Dimension:** Ship Mode
* **Column Dimension:** Region
* **Metric:**

```
AVG(DATE_DIFF(Ship Date, Order Date))
```

---

## Tujuan Dashboard

* Memberikan insight penjualan dan profitabilitas
* Mengidentifikasi produk dan segmen pelanggan yang paling bernilai
* Mengevaluasi efisiensi dan keandalan proses pengiriman
* Mendukung pengambilan keputusan berbasis data
