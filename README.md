# AB Testi — Bidding Yöntemlerinin Dönüşüm Karşılaştırması

Facebook'un yeni **Average Bidding** teklif türünün, mevcut **Maximum Bidding**'den daha fazla dönüşüm getirip getirmediğinin bağımsız iki örneklem hipotez testi ile analizi. *Miuul Data Scientist Bootcamp* kapsamında hazırlanmıştır.

## İş Problemi

bombabomba.com, 1 aydır sürdürdüğü A/B testinde Maximum Bidding (kontrol) ile Average Bidding (test) yöntemlerini karşılaştırıyor. Nihai başarı ölçütü **Purchase** olduğundan istatistiksel test bu metrik üzerinden yapılır.

## Veri Seti

`data/ab_testing.xlsx` — iki sayfa, her biri 40 gözlem:

| Sayfa | Grup |
|---|---|
| `Control Group` | Maximum Bidding |
| `Test Group` | Average Bidding |

| Değişken | Açıklama |
|---|---|
| `Impression` | Reklam görüntüleme sayısı |
| `Click` | Tıklama sayısı |
| `Purchase` | Satın alınan ürün sayısı |
| `Earning` | Elde edilen kazanç |

## Yöntem

1. **Veri hazırlama** — Kontrol/test grupları ayrı okunur, betimsel istatistiklerle incelenir, `group` etiketiyle `concat` edilir.
2. **Hipotez** — H0: M1 = M2 (gruplar arası Purchase ortalaması farkı yok) / H1: M1 ≠ M2
3. **Varsayım kontrolü** — Normallik (Shapiro-Wilk), varyans homojenliği (Levene)
4. **Test seçimi**
   - Normallik ✅ + Varyans homojen ✅ → Bağımsız iki örneklem **t testi** (`equal_var=True`)
   - Normallik ✅ + Varyans homojen ❌ → **Welch t testi** (`equal_var=False`)
   - Normallik ❌ → **Mann-Whitney U** testi
5. **Karar** — p-value < 0.05 ise H0 reddedilir.

## Sonuç

| Grup | Purchase ortalaması |
|---|---|
| Control (Maximum Bidding) | 550.89 |
| Test (Average Bidding) | 582.11 |

- Shapiro-Wilk: control p=0.5891, test p=0.1541 → normallik sağlanıyor
- Levene: p=0.1083 → varyans homojenliği sağlanıyor
- **Bağımsız iki örneklem t testi**: istatistik = -0.9416, **p-value = 0.3493**

**H0 reddedilemedi.** Test grubunun Purchase ortalaması sayısal olarak daha yüksek olsa da (582 vs 551), bu fark istatistiksel olarak anlamlı değil (p=0.3493 > 0.05). Sadece bu sonuca dayanarak Average Bidding'e geçmek için yeterli kanıt yok — test süresi/örneklem büyüklüğü artırılarak tekrarlanması önerilir.

## Kurulum ve Çalıştırma

```bash
pip install -r requirements.txt
python ab_testing.py
```

## Proje Yapısı

```
ABTesti/
├── ab_testing.py         # veri hazırlama, hipotez testi, sonuç analizi
├── data/
│   └── ab_testing.xlsx   # kontrol/test grubu verisi
├── requirements.txt
└── README.md
```

## Kullanılan Kütüphaneler

- pandas
- scipy (shapiro, levene, ttest_ind, mannwhitneyu)
- openpyxl (Excel okuma)

## Kaynak

Miuul Data Scientist Bootcamp — A/B Testing modülü.
