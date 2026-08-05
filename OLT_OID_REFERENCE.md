# OLT SNMP OID Reference — Barcomnet App

> File ini mendokumentasikan semua SNMP OID yang digunakan di `services/oltService.js`  
> Terverifikasi dari hasil polling langsung di lapangan  
> Terakhir diperbarui: Agustus 2026 — disesuaikan dengan kode aktual `BRAND_PROFILES` dan `SYSTEM_OIDS`

---

## 1. Enterprise ID (Private MIB Root)

| Vendor | Enterprise OID | Keterangan |
|--------|----------------|-----------|
| Hioso / C-Data | `.1.3.6.1.4.1.25355` | OLT series HA7304V, HA7304C, HA7304VX |
| BDCOM / Huawei clone | `.1.3.6.1.4.1.3320` | OLT berbasis chipset Huawei EPON |
| Zimmlink | `.1.3.6.1.4.1.25355` / `.1.3.6.1.4.1.3320` | OLT Zimmlink — dual-chipset (C-Data dan BDCOM) |

---

## 2. Profile OLT — OID per Tipe

### 2.1 HIOSO_C — Hioso HA7304V (EPON, community: `public`)

> Digunakan di: **OLT C (192.168.1.66)**  
> SNMP limitation: hanya expose 2 grup ONU (`1.1.X` = PON1, `1.2.X` = PON2+3+4 merged)  
> Butuh Telnet scan + k-NN untuk pisahkan PON2/3/4 secara fisik

| Field | OID | Keterangan |
|-------|-----|-----------|
| Nama ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.37` | String, bisa custom atau "NA" |
| Serial Number (SN) | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.11` | Hex string, dikonversi ke MAC-like |
| MAC Address | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.12` | Alternatif SN candidate (hanya di scan awal) |
| Status ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.39` | `1`=online, `2`=offline |
| Jarak (distance) | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.25` | Integer, satuan meter |
| Tx Power | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.4` | Float string dBm (sudah final, contoh: `-2.08`) |
| Rx Power | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.8` | Float string dBm (sudah final, contoh: `-18.50`) |
| Temperature | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.7` | Integer, satuan °C |

**Format index OID**: `{B}.{PON}.{ONU_ID}` — contoh: `1.2.4` = board 1, PON 2, ONU ID 4

**Flag khusus** (`PROFILES['HIOSO_C']`):
- `telnet_pon34_scan = true` — sistem secara otomatis Telnet ke OLT untuk membaca optik PON3 dan PON4 yang tidak terekspos via SNMP

---

### 2.2 HIOSO_B2 — Hioso HA7304C (EPON B, community: `SNMPREAD`)

> Digunakan di: **OLT A (192.168.75.88)**, **OLT B (192.168.75.77)**  
> Terdeteksi via `sysDescr` mengandung string `"HIOSO B"`  
> Expose 4 PON port terpisah secara akurat via SNMP

| Field | OID | Keterangan |
|-------|-----|-----------|
| Nama ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.37` | Sama dengan HIOSO_C |
| Serial Number | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.11` | Sama dengan HIOSO_C |
| Status ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.39` | `1`=online, `2`=offline |
| Jarak (distance) | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.25` | Integer, meter |
| Tx Power | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.4` | Float string dBm (sudah final, sama dengan HIOSO_C) |
| Rx Power | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.8` | Float string dBm (sudah final, sama dengan HIOSO_C) |
| Temperature | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.7` | Integer, °C |

> ✅ **HIOSO_C dan HIOSO_B2 menggunakan MIB tree yang sama** (`.25355.3.2.6`). Perbedaannya hanya di SNMP community string dan kemampuan expose PON port.

---

### 2.3 HIOSO_VX — Hioso HA7304VX (EPON, community: `public`)

> Digunakan di: **OLT D (192.168.75.99)**  
> Varian dari HIOSO_C — MIB tree identik

| Field | OID | Keterangan |
|-------|-----|-----------|
| Nama ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.37` | |
| Serial Number | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.11` | |
| Status ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.39` | |
| Jarak (distance) | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.25` | |
| Tx Power | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.4` | Float string dBm (identik dengan HIOSO_C) |
| Rx Power | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.8` | Float string dBm (identik dengan HIOSO_C) |
| Temperature | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.7` | Integer, °C |

> ⚠️ HIOSO_VX **tidak** memiliki flag `telnet_pon34_scan` — berbeda dengan HIOSO_C.

---

### 2.4 HIOSO_B — BDCOM / Huawei Clone (EPON)

> Berbasis chipset Huawei, MIB tree berbeda total (`.3320.101.10`)

| Field | OID | Keterangan |
|-------|-----|-----------|
| Nama ONU | `.1.3.6.1.4.1.3320.101.10.1.1.79` | |
| Serial Number | `.1.3.6.1.4.1.3320.101.10.1.1.3` | |
| Status ONU | `.1.3.6.1.4.1.3320.101.10.1.1.26` | |
| Tx Power | `.1.3.6.1.4.1.3320.101.10.5.1.5` | |

---

## 3. Zimmlink OLT — SNMP OID Reference

> **Brand**: Zimmlink (EPON OLT)  
> Zimmlink memproduksi OLT berbasis dual-chipset: **C-Data** (`.25355`) dan **BDCOM/Huawei** (`.3320`).  
> Sistem secara otomatis mendeteksi profil aktif berdasarkan probe OID.  
> Terverifikasi pada: **OLT 2 (192.168.100.100)** — Agustus 2026

### 3.1 ZIMMLINK_EPON_C — Zimmlink Chipset C-Data (community: `public`)

> Digunakan di: **OLT 2 (192.168.100.100)**  
> Chipset: C-Data (Enterprise `.25355`) — MIB tree identik dengan HIOSO_C  
> Terdeteksi via probe OID `.1.3.6.1.4.1.25355.3.2.6.3.2.1.39`  
> Mendukung Telnet fallback untuk pembacaan ONU yang lebih detail

| Field | OID | Keterangan |
|-------|-----|-----------|
| **Probe Autodeteksi** | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.39` | OID yang digunakan untuk mendeteksi apakah perangkat compatible |
| Status ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.29` | Status tabel ONU |
| Nama ONU | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.37` | String, nama/deskripsi ONU |
| Serial Number (SN) | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.11` | Hex string SN ONU |
| Rx Power (Redaman) | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.8` | Float string dBm (contoh: `-18.50`) |
| Tx Power | `.1.3.6.1.4.1.25355.3.2.6.14.2.1.4` | Float string dBm (contoh: `-2.08`) |
| Jarak ONU (Distance) | `.1.3.6.1.4.1.25355.3.2.6.3.2.1.25` | Integer, satuan meter (`distance_tenths_meter: false`) |
| Alasan Offline | — | Tidak tersedia pada profil ini |

**Status online values**: `[1, 3, 4]`

**Format index OID**: `{Board}.{PON}.{ONU_ID}` — contoh: `1.2.4` = board 1, PON 2, ONU ID 4

---

### 3.2 ZIMMLINK_EPON_B — Zimmlink Chipset BDCOM/Huawei (EPON)

> Chipset: BDCOM / Huawei clone (Enterprise `.3320`)  
> Terdeteksi via probe OID `.1.3.6.1.4.1.3320.101.10.1.1.26`  
> MIB tree berbeda total dari ZIMMLINK_EPON_C

| Field | OID | Keterangan |
|-------|-----|-----------|
| **Probe Autodeteksi** | `.1.3.6.1.4.1.3320.101.10.1.1.26` | OID untuk deteksi profil BDCOM |
| Status ONU | `.1.3.6.1.4.1.3320.101.10.1.1.26` | Status tabel ONU |
| Nama ONU | `.1.3.6.1.4.1.3320.101.10.1.1.79` | String, nama ONU |
| Serial Number (SN) | `.1.3.6.1.4.1.3320.101.10.1.1.3` | SN ONU |
| Tx Power | `.1.3.6.1.4.1.3320.101.10.5.1.5` | Float dBm |
| Rx Power (Redaman) | `.1.3.6.1.4.1.3320.101.10.5.1.6` | Float dBm |
| Alasan Offline | `.1.3.6.1.4.1.3320.101.11.1.1.11` | Alasan ONU offline |

**Status online values**: `[1, 3, 4]`

---

### 3.3 Zimmlink System OIDs (Hardware Metrics)

> OID sistem diambil menggunakan `snmp.get` (bukan walk).  
> Berlaku untuk **semua profil Zimmlink** (EPON_C dan EPON_B).

| Metric | OID | Keterangan |
|--------|-----|-----------|
| Suhu (Temperature) | `.1.3.6.1.4.1.25355.3.2.1.1.1.0` | Suhu perangkat OLT dalam °C |
| CPU Usage | `.1.3.6.1.4.1.25355.3.2.1.1.2.0` | Persentase pemakaian CPU (%) |
| RAM Usage | `.1.3.6.1.4.1.25355.3.2.1.1.3.0` | Persentase pemakaian RAM (%) |
| Traffic Uplink IN | `.1.3.6.1.2.1.31.1.1.1.6.1` | `ifHCInOctets` — Uplink port 1 (bytes) |
| Traffic Uplink OUT | `.1.3.6.1.2.1.31.1.1.1.10.1` | `ifHCOutOctets` — Uplink port 1 (bytes) |

> 💡 **Catatan**: OID System Zimmlink identik dengan OID System Hioso karena keduanya menggunakan private MIB yang sama (`.25355.3.2.1`).

---

### 3.4 Zimmlink Telnet Fallback

> Ketika brand terdeteksi sebagai `zimmlink`, sistem secara otomatis mencoba **Telnet fallback** melalui fungsi `fetchZimmlinkOnusViaTelnet()` untuk mendapatkan data ONU yang lebih lengkap dan akurat.  
> Fallback ini berguna ketika SNMP tidak mengekspos semua PON port atau ketika data SNMP tidak lengkap.

---

## 4. Perbedaan Zimmlink vs Hioso

| Aspek | Hioso | Zimmlink |
|-------|-------|----------|
| **Merk Perangkat** | Hioso (C-Data asli) | Zimmlink (OEM/rebranded) |
| **MIB Tree (C-Data)** | `.25355.3.2.6.*` | `.25355.3.2.6.*` (identik) |
| **MIB Tree (BDCOM)** | `.3320.101.10.*` | `.3320.101.10.*` (identik) |
| **System OIDs** | `.25355.3.2.1.1.*` | `.25355.3.2.1.1.*` (identik) |
| **Profil C-Data** | `HIOSO_C`, `HIOSO_B2`, `HIOSO_VX` | `ZIMMLINK_EPON_C` |
| **Profil BDCOM** | `HIOSO_B` | `ZIMMLINK_EPON_B` |
| **Telnet Fallback** | Hanya `HIOSO_C` (PON3/4 scan) | Semua profil Zimmlink |
| **Online Values** | `[1, 3, 4]` | `[1, 3, 4]` (sama) |
| **GPON Override** | `[2, 3, 4]` jika profil mengandung "gpon" | `[2, 3, 4]` jika profil mengandung "gpon" |

> ℹ️ **Kesimpulan**: Zimmlink pada dasarnya adalah OLT rebranded dari Hioso/C-Data. Oleh karena itu, OID-nya 100% identik. Perbedaan utama ada di brand key (`zimmlink` vs `hioso`) yang mempengaruhi **Telnet fallback behavior** dan **profil autodeteksi**.
