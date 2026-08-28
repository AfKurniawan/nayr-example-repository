<<<<<<< HEAD
# nayr-example-repository
=======
# Checkout Anomaly Lab

Repository contoh ini sengaja memuat satu regresi pada aturan risk limit pembayaran. Tujuannya
adalah menghasilkan GitHub Actions failure yang dapat dikumpulkan NAYR sebagai signal
`ci.workflow_run` dengan evidence yang cukup spesifik.

## Anomaly

Aturan bisnis menyatakan transaksi dengan nilai **sama dengan atau melebihi** risk limit harus
masuk ke `manual_review`. Implementasi pada `src/checkout/risk.py` keliru memakai operator `>`,
sehingga transaksi tepat di batas limit justru berstatus `approved`.

Workflow `Checkout risk policy` akan menghasilkan evidence berikut:

- failed job: `test-checkout`;
- failed step: `Run checkout policy tests`;
- lokasi: `src/checkout/risk.py:22`;
- expected: `manual_review`;
- actual: `approved`;
- changed file yang dapat dicocokkan: `src/checkout/risk.py`.

Tidak ada credential, network call, atau dependency pihak ketiga di project ini.

## Menjalankan secara lokal

Project membutuhkan Python 3.11 atau lebih baru.

```bash
python -m unittest discover -s tests -v
```

Hasil yang diharapkan untuk fixture anomaly adalah satu test gagal dengan pesan:

```text
src/checkout/risk.py:22 expected status manual_review but got approved
```

Kegagalan ini disengaja. Test lain tetap lulus untuk menunjukkan bahwa masalahnya terbatas pada
boundary risk limit, bukan seluruh checkout service.

## Memasang sebagai repository GitHub

1. Salin isi folder ini ke repository GitHub baru (jangan menyalin `.git` dari project lain).
2. Commit dan push ke branch default. Workflow berjalan otomatis pada `push` dan
   `pull_request`.
3. Di NAYR, hubungkan integration GitHub read-only dan pilih repository tersebut.
4. Jalankan sinkronisasi/ingestion setelah workflow selesai gagal.
5. Buka signal `Checkout risk policy failure`, lalu pilih **Analyze Evidence**.

Token GitHub yang dipakai NAYR perlu akses read-only ke repository serta Actions/Checks agar job,
step, log, dan annotation dapat dikumpulkan. NAYR tidak perlu dan tidak seharusnya diberi akses
write untuk skenario ini.

## Resolusi setelah demo

Untuk membuat workflow hijau, ubah kondisi di `classify_payment()` dari `amount > risk_limit`
menjadi `amount >= risk_limit`, lalu commit dan push. Commit perbaikan tersebut memberi pasangan
evidence yang mudah dibandingkan dengan workflow failure sebelumnya.
>>>>>>> f2594ff (add intentional checkout anomaly example)
