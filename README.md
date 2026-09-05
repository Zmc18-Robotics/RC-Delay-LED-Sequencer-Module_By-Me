# RC Delay LED Sequencer

Rangkaian LED yang menyala dan mati secara berurutan (staggered) menggunakan kombinasi resistor, kapasitor, dan transistor NPN — tanpa mikrokontroler atau IC digital. Setiap LED memiliki tahap RC delay sendiri, sehingga saat sumber daya dinyalakan LED menyala satu per satu, dan saat sumber daya dimatikan LED juga mati satu per satu dengan urutan yang sama.

![status](https://img.shields.io/badge/status-active-brightgreen)
![platform](https://img.shields.io/badge/PCB-KiCad-orange)
![license](https://img.shields.io/badge/license-MIT-blue)

> Tambahkan foto/GIF modul PCB asli dan video demo nyala-mati LED di sini setelah PCB jadi.
>
> `![demo](docs/images/demo.gif)`

## Daftar isi

- [Cara kerja](#cara-kerja)
- [Fitur](#fitur)
- [Skematik](#skematik)
- [Pinout transistor](#pinout-transistor)
- [Bill of materials (BOM)](#bill-of-materials-bom)
- [Struktur repository](#struktur-repository)
- [PCB (KiCad)](#pcb-kicad)
- [Cara build](#cara-build)
- [Kustomisasi delay](#kustomisasi-delay)
- [Roadmap](#roadmap)
- [Lisensi](#lisensi)

## Cara kerja

Tiap tahap terdiri dari satu jaringan RC (resistor + kapasitor) yang mengisi tegangan basis sebuah transistor NPN. Time constant tau = R x C menentukan seberapa cepat transistor tersebut "membuka" dan menyalakan LED yang terhubung ke kolektornya.

- **Saat power ON**: kapasitor mulai charge lewat resistor. Tahap dengan tau terkecil mencapai ambang basis (~0.7 V) paling cepat, sehingga LED-nya menyala paling dulu.
- **Saat power OFF**: kapasitor discharge lewat resistor yang sama. Tahap dengan tau terkecil juga kosong paling cepat, sehingga LED-nya mati paling dulu.

Karena proses charge dan discharge memakai jalur RC yang sama, urutan nyala dan urutan mati otomatis konsisten (1, 2, 3, ... N untuk keduanya) tanpa logika tambahan.

## Fitur

- Full analog, tanpa mikrokontroler/IC digital
- Urutan nyala dan mati konsisten (bukan cuma nyala berurutan seperti chaser 555+4017 biasa)
- Delay tiap tahap mudah diatur lewat nilai kapasitor
- Skalabel dari beberapa LED sampai puluhan tahap
- Sudah tersedia modul PCB KiCad siap fabrikasi

## Skematik

Satu tahap terdiri dari:

```
Vcc ── R_bias ──┬── C (ke GND)
                └── Basis (Q NPN)

Vcc ── R_LED ── LED ── Kolektor (Q)

Emitor (Q) ── GND
```

Semua tahap terhubung paralel ke rel Vcc dan GND yang sama; hanya nilai R_bias dan/atau C yang dibedakan tiap tahap untuk mengatur urutan delay.

File skematik lengkap: [`hardware/kicad/rc-led-sequencer.kicad_sch`](hardware/kicad/rc-led-sequencer.kicad_sch)

> Tambahkan ekspor gambar skematik (PNG/SVG) dari KiCad di `docs/images/schematic.png` lalu tampilkan di sini:
>
> `![skematik](docs/images/schematic.png)`

## Pinout transistor

Untuk transistor NPN tipe TO-92 (mis. BC547, 2N2222) dengan bagian datar menghadap pengguna, kaki menghadap ke bawah:

| Pin | Fungsi | Terhubung ke |
|---|---|---|
| Basis (B) | Sinyal kontrol dari node RC | R_bias + C |
| Kolektor (C) | Jalur arus dari LED | R_LED + LED (katoda) |
| Emitor (E) | Jalur arus ke ground | GND |

> Urutan fisik kaki (E-B-C atau lainnya) berbeda tiap tipe transistor — selalu cek datasheet komponen yang dipakai sebelum solder.

## Bill of materials (BOM)

Contoh nilai untuk 10 tahap (10 LED). R_bias dan R_LED dibuat tetap, hanya nilai kapasitor yang dinaikkan tiap tahap.

| Tahap | C (µF) | R_bias | R_LED | Transistor | LED |
|---|---|---|---|---|---|
| 1 | 47 | 10 kΩ | 330 Ω | BC547 | 5 mm, warna bebas |
| 2 | 100 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 3 | 220 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 4 | 330 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 5 | 470 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 6 | 680 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 7 | 1000 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 8 | 1500 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 9 | 2200 | 10 kΩ | 330 Ω | BC547 | 5 mm |
| 10 | 3300 | 10 kΩ | 330 Ω | BC547 | 5 mm |

Komponen tambahan:

| Komponen | Jumlah | Catatan |
|---|---|---|
| Kapasitor elektrolit (nilai di atas) | 10 | Tegangan kerja minimal 16 V |
| Resistor 10 kΩ | 10 | R_bias, 1/4 W |
| Resistor 330 Ω | 10 | R_LED, 1/4 W |
| Transistor NPN BC547 / 2N2222 | 10 | Bisa diganti setara |
| LED 5 mm | 10 | Warna sesuai selera |
| Header/konektor power | 1 | Input Vcc + GND |
| PCB | 1 | Lihat folder `hardware/` |

> Perbarui tabel ini sesuai BOM final dari KiCad (`Tools > Generate BOM`), atau tambahkan file `hardware/bom.csv` hasil ekspor.

## Struktur repository

```
.
├── hardware/
│   └── kicad/
│       ├── rc-led-sequencer.kicad_pro
│       ├── rc-led-sequencer.kicad_sch
│       ├── rc-led-sequencer.kicad_pcb
│       └── gerbers/
├── docs/
│   └── images/
│       ├── schematic.png
│       ├── pcb-top.png
│       ├── pcb-bottom.png
│       └── demo.gif
├── LICENSE
└── README.md
```

> Sesuaikan struktur ini dengan nama file KiCad kamu yang sebenarnya.

## PCB (KiCad)

Modul ini sudah dirangkai penuh menjadi satu board di KiCad, mencakup skematik dan layout PCB untuk seluruh tahap sekaligus (bukan modul terpisah per LED).

Cara membuka:

1. Install [KiCad](https://www.kicad.org/) versi 7 ke atas.
2. Clone repo ini, lalu buka `hardware/kicad/rc-led-sequencer.kicad_pro`.
3. Buka `rc-led-sequencer.kicad_sch` untuk melihat skematik lengkap.
4. Buka `rc-led-sequencer.kicad_pcb` untuk melihat layout PCB dan 3D view (`View > 3D Viewer`).

Untuk fabrikasi:

1. Buka file `.kicad_pcb`.
2. `File > Fabrication Outputs > Gerbers` untuk generate file gerber.
3. Generate juga file drill (`.drl`).
4. Kirim hasil generate ke fabrikasi PCB pilihan (JLCPCB, PCBWay, dsb).

> Tambahkan spesifikasi board di sini setelah final: ukuran PCB, jumlah layer, ketebalan tembaga, dan catatan desain lainnya.

## Cara build

1. Solder komponen sesuai BOM, mulai dari komponen terpendek (resistor) ke tertinggi (elco, transistor, LED).
2. Perhatikan polaritas kapasitor elektrolit (kaki negatif biasanya bergaris pada body) dan orientasi LED (kaki panjang = anoda).
3. Cek orientasi transistor sesuai pinout datasheet sebelum solder.
4. Hubungkan sumber daya (5-9 V DC) ke header power sesuai polaritas.
5. Nyalakan dan amati LED menyala berurutan; matikan dan amati LED mati dengan urutan yang sama.

## Kustomisasi delay

- Delay tiap tahap kira-kira mengikuti `t ≈ 0.7 x R_bias x C` (perkiraan, bukan nilai presisi karena dipengaruhi juga oleh Vcc dan karakteristik transistor).
- Untuk mempercepat seluruh urutan: turunkan nilai R_bias di semua tahap secara proporsional.
- Untuk memperlambat: naikkan nilai kapasitor di semua tahap.
- Jarak delay antar tahap ditentukan oleh rasio kenaikan kapasitor — makin besar rasionya, makin jelas jeda antar LED.

## Roadmap

- [ ] Tambahkan foto dan video demo
- [ ] Ekspor BOM otomatis dari KiCad
- [ ] Tambahkan varian dengan potensiometer untuk delay yang bisa diatur
- [ ] Uji coba dengan jumlah tahap lebih dari 10

## Lisensi

Proyek ini dirilis di bawah lisensi MIT — lihat file [LICENSE](LICENSE) untuk detail. Sesuaikan jika kamu ingin memakai lisensi hardware terbuka seperti CERN-OHL.
