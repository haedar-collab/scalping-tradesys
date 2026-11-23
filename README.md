# Tradesys Edge Scalp

Web app sistem trading crypto **Scalping Framework** (ebook Didi Traderklinik, knowledge base `haedar-collab/scalping-framework-kb`) untuk **Binance Spot, hanya long**.
Satu file `index.html`, tanpa build, tanpa server, tanpa aplikasi berbayar. Data langsung dari API publik Binance di browser.

> Aplikasi ini hanya memberi **sinyal dan alert**. Tidak ada order yang dikirim ke exchange dan tidak butuh API key Binance.

---

## Fitur

| Tab | Isi |
|---|---|
| **Dashboard** | BTC, sesi WIB (jam live), status batas harian, jumlah sinyal per setup, biaya per trade dalam R, chart + **kartu Trading Plan**, papan sinyal |
| **Scanner** | Watchlist atau semua pair USDT. Untuk pair tanpa sinyal: setup terdekat dan syarat yang belum terpenuhi |
| **Chart** | Candle + EMA 9/21/50 + VWAP + Bollinger 20,2, panel RSI 14 + Stochastic %K, box breakout, level plan, layer SMC & price action, diagnosa per setup |
| **Backtest** | Semua setup atau satu setup, 3.000–10.000 bar, fee + slippage, batas harian opsional, **kriteria lulus buku (Bab 10)**, hasil per setup |
| **Risiko & batas harian** | Kalkulator ukuran posisi (termasuk biaya dalam R), **jurnal hari ini** dan status GO / Jeda / Stop sesuai aturan buku |
| **Alert** | Log sinyal, kirim sinyal/pesan tes ke Telegram, auto-scan tiap close bar eksekusi / 15 menit / 1 jam |
| **Pengaturan** | Semua parameter setup, filter, risiko, biaya, endpoint Binance, Telegram |

Bahasa EN/ID dan tema terang/gelap di header. Harga live diperbarui tiap 15 detik.

## Timeframe (multi-timeframe Bab 2)

| Mode | Konteks | Analisis | Eksekusi |
|---|---|---|---|
| **M5 · M15 · H1** (default, sesuai buku) | H1 | M15 | M5 |
| M15 · H1 · H4 (varian lebih lambat) | H4 | H1 | M15 |

Hanya bar yang sudah close yang dipakai di semua timeframe (uji look-ahead: 484 titik, 283 di antaranya bar sinyal, 0 selisih).

## Strategi

**Indikator (Bab 3):** EMA 9/21/50, RSI 14, Stochastic 5,3,3, VWAP (reset 00:00 UTC = 07.00 WIB), Bollinger 20,2, volume (rata-rata 14), ATR 14.

**Gerbang untuk semua setup**
- ATR eksekusi dibanding rata-rata 100 bar: < 0,5× mati (lewati), > 1,8× tinggi (setengah ukuran), > 3× ekstrem (tidak trading).
- **TP1 minimal 3× biaya bolak-balik** (Bab 2). Dengan fee 0,1% + slippage 0,02% per sisi, TP1 harus ≥ 0,72%.
- Checklist 4 dari 6 (Bab 3) untuk pullback dan breakout.

**Skor tren 0–10 (Bab 5.1):** EMA konteks tersusun (+2), EMA 50 miring (+1), struktur HH+HL (+2), di atas VWAP 30 menit (+1), BB melebar (+1), volume bar naik > turun (+1), RSI analisis > 50 (+1), timeframe analisis searah (+1).

| | Trend pullback (Bab 5) | Breakout (Bab 6) | Reversal (Bab 7) |
|---|---|---|---|
| Syarat | Skor ≥ 6, tidak choppy, EMA konteks & analisis tersusun naik | Range 5–20 bar, lebar 1,5–6× ATR dan < ½ ATR harian | S1 ekstrem + S2 level kuat + S3 divergensi bullish |
| Pemicu | Pullback ke zona EMA M15 (Z1/Z2/Z3) + pin bar / engulfing / doji+konfirmasi, close di sisi benar | 6 filter, butuh 4 (F1 close & F2 volume ≥ 1,2× wajib) | Candle naik; S4 candle rejection = GO, tanpa S4 = CAUTION (½ ukuran) |
| Entry | Market saat close | Konservatif: limit saat retest (default) · Agresif: market | Market saat close |
| SL | Di bawah candle / EMA 50 atau 1,5×ATR (yang lebih lebar) | Di dalam range, 1,2×ATR dari garis | Di bawah wick, maks 3×ATR |
| TP1 / TP2 | Swing high M15 / H1 | 1× / 2× lebar range | Swing M15 atau BB tengah / level H1 atau 3R |
| R:R min | 1,5 | 1,5 | 2,0 |
| Manajemen | BE di 50% ke TP1, TP1 50%, trailing EMA 21 | TP1 50% + BE, TP2 25%, trailing EMA 9 | TP1 50% + BE, sisa ke TP2 |

Batas waktu 60 menit (12 bar M5). Adaptasi crypto: pip diganti kelipatan ATR/persen, sesi London/NY jadi filter opsional, hanya long.

**Batas harian (Bab 1, 8, 9):** −2% jeda 30 menit, −3% stop, +3% stop, 2 rugi beruntun jeda 30 menit, 3 rugi beruntun stop, maks 10 trade/hari. Diterapkan ke jurnal dan (opsional) ke backtest.

## Hasil uji dengan data Binance asli

10 pair (BTC, ETH, SOL, BNB, XRP, DOGE, ADA, AVAX, LINK, SUI). Entry di open bar berikutnya, fee 0,1% + slippage 0,02% per sisi.

**M5 · M15 · H1**, 15 Sep – 23 Nov 2026:

| Skenario | Trade | Win | Profit factor | Ekspektansi |
|---|---|---|---|---|
| Tanpa biaya (edge kotor) | 1.068 | 30% | 0,79 | −0,13R |
| Fee retail, aturan biaya buku aktif | 622 | 23% | 0,29 | −0,78R |

**M15 · H1 · H4**, ±5 bulan:

| Skenario | Trade | Win | Profit factor | Ekspektansi |
|---|---|---|---|---|
| Tanpa biaya | 1.519 | 33% | 1,02 | +0,01R (t 0,3) |
| Fee retail 0,1% | 1.374 | 28% | 0,50 | −0,47R |
| Fee BNB 0,075% | 1.444 | 29% | 0,56 | −0,38R |

**Kesimpulan:** aturan buku, diterjemahkan secara mekanis ke crypto spot, **tidak lulus kriteria buku** (WR ≥ 50%, PF ≥ 1,4) di skenario mana pun. Sebelum biaya edge-nya nol; setelah biaya negatif. Penyebab utama: di M5, biaya bolak-balik 0,24% setara ±0,6–2R dari stop yang wajar. Filter sesi 14–22 WIB dan breakout agresif juga diuji, tidak mengubah kesimpulan. Parameter **tidak** dioptimasi agar terlihat profit (menghindari overfitting).

Gunakan sebagai alat bantu analisis dan **paper/forward test** dulu. Jangan dipakai sebagai sinyal otomatis dengan modal sungguhan.

## Cara upload (GitHub Pages)

1. Upload `index.html` dan `README.md` ke repo (mis. `scalping-tradesys`) → **Commit changes**.
2. **Settings → Pages** → *Deploy from a branch*, branch `main`, folder `/ (root)` → **Save**.
3. Setelah 1–2 menit: `https://haedar-collab.github.io/scalping-tradesys/`
4. HP: Chrome → ⋮ → **Add to Home screen**.

## Telegram

1. @BotFather → `/newbot` → salin token. Kirim pesan ke bot, buka `https://api.telegram.org/bot<TOKEN>/getUpdates`, salin `chat.id`.
2. **Pengaturan → Data dan Telegram**: isi token & chat ID, centang kirim sinyal live, **Simpan**.
3. Tab **Alert**: *Kirim pesan tes* / *Kirim sinyal tes*.

Token hanya tersimpan di localStorage browser, tidak ikut ter-upload.

## Batasan

- Auto-scan hanya berjalan selama halaman terbuka (halaman statis tidak bisa berjalan di background).
- Endpoint default `data-api.binance.vision`. Jika status "Data demo", ganti endpoint di Pengaturan.
- Ukuran posisi dibatasi alokasi maks (default 100% modal, spot tanpa leverage). Dengan stop scalping yang sempit, risiko nyata sering di bawah 1%; kartu plan menampilkan peringatannya.
- Tidak ada kalender ekonomi; aturan berita dari buku harus dicek manual.

*Bukan nasihat keuangan.*
