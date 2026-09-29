# Tasarım Notları — 1. Gün

Bu dosya, projeye başlarken verilen kararları ve gerekçelerini tutar. Amaç, ileride
"bunu neden böyle yaptım?" sorusuna cevap verebilmek.

## Karar 1 — Örnekleme aralığı: 60 saniye

Ortam sıcaklığı dakikalar mertebesinde değişir; saniyede bir ölçmek anlamlı bilgi
eklemez, sadece dosyayı şişirir.

- 60 s aralık → günde 1440 satır → 48 saatte 2880 satır.
- Satır başına ~50 bayt → 48 saatlik kayıt ~140 KB. SD kart için hiç sorun değil.

DHT22'nin kendi sınırı da bunu destekliyor: **2 saniyeden sık okunamaz**, sensör aynı
değeri döndürür.

## Karar 2 — Veri biçimi: CSV

JSON değil CSV, çünkü:
- Python (`pandas.read_csv`) ve Excel doğrudan açar — 6. gün analiz için gerekli.
- Satır satır eklenebilir; her yazmada dosyanın tamamını yeniden yazmak gerekmez.
- Elektrik kesilirse yalnız son satır bozulur, dosyanın geri kalanı okunabilir kalır.

Biçim:

```csv
zaman,sicaklik_c,nem_yuzde,isik_ham,isik_yuzde
2026-09-29T14:32:00,23.4,58.2,2140,52.3
```

Her gün için ayrı dosya: `/kayit/2026-09-29.csv`. Böylece tek dosya devasa büyümez ve
bir gün bozulursa diğerleri etkilenmez.

## Karar 3 — Zaman kaynağı: NTP, RTC modülü değil

DS3231 gibi bir gerçek zaman saati modülü alınabilirdi. Almadık çünkü:
- Cihaz zaten WiFi'ye bağlı; NTP bedava ve daha doğru.
- Bir parça eksik = bir lehim noktası, bir arıza kaynağı eksik.

**Bedeli:** İnternet yoksa açılışta saat bilinmez. Çözüm olarak, NTP başarısız olursa
cihaz `epoch` sayacıyla göreli zaman yazar ve kayda `saat_senkron=0` bayrağı düşer —
veriyi analiz ederken bu satırların zamanına güvenmeyiz.

## Karar 4 — Web arayüzü: ESP32'nin kendi sunucusu

Bulut servisi (ThingSpeak, Blynk) kullanmıyoruz:
- Bağımlılık ve hesap gerektirir, ileride kapanabilir.
- Repo'yu klonlayan biri kendi cihazında çalıştıramaz.

ESP32 yerel ağda basit bir HTTP sunucusu açar: son ölçümleri ve küçük bir grafik gösterir.
Sayfa cihazın içinden servis edilir, dış bağımlılık yok.

## Karar 5 — Işık ölçümü: mutlak değil, göreli

LDR ile lüks cinsinden doğru ölçüm yapmak kalibrasyon gerektirir (referans lüksmetre lazım).
Elimizde yok. O yüzden:

- Ham ADC değerini (0–4095) **ve** yüzde olarak normalize edilmiş değeri kaydediyoruz.
- README'de bunun mutlak lüks olmadığını açıkça yazacağız.

Ölçtüğünün sınırını bilmek ve yazmak, ölçümü abartmaktan daha mühendisçedir. Bu satır
mülakatta iyi durur.

## Karar 6 — Gerçekleme ortamı: Wokwi simülatörü

Donanım bütçesi yok. Seçenekler ve neden Wokwi:

| Seçenek | Neden olmadı |
|---------|--------------|
| Gerçek donanım | Bütçe yok |
| Saf yazılım simülasyonu (sensörü rastgele sayı üret) | Devre tasarımını hiç test etmez; öğretici değil |
| QEMU / Renode ESP32 | Çevre birimi desteği zayıf, DHT22 ve SD yok |
| **Wokwi** ✅ | DHT22, SD, WiFi, LCD destekler; tarayıcıda çalışır; `diagram.json` repo'ya girer; **okuyan herkes tek tıkla çalıştırır** |

Wokwi'nin asıl avantajı repo'ya kattığı şey: işveren README'deki linke tıklayınca devreyi
çalışır halde görür. Bir fotoğraf "bunu yaptım" der, çalışan bir simülasyon "buyur, sen
dene" der.

**Bunun bedeli** — simülasyonun modellemediği gerçek dünya etkileri
[`hardware/BOM.md`](../hardware/BOM.md) sonunda listelendi. README'de simülasyon olduğu
açıkça yazılacak; hiçbir yerde "ölçtüm" denmeyecek.

## Açık sorular (ilerleyen günlerde cevaplanacak)

- [ ] Wokwi hesabı açıldı mı? (ücretsiz, GitHub ile giriş yapılabilir)
- [ ] DHT22'ye 24 saatlik gerçekçi bir sıcaklık profili nasıl verilir? (Wokwi'de sensör
      değeri elle ayarlanıyor — senaryo scripti mi yazsak, yoksa Kocaali'nin gerçek
      sıcaklık verisini mi besleyelim? İkincisi daha iyi durur.)
- [ ] Simülasyon hızlandırılabilir mi? 24 saatlik veriyi gerçek zamanda beklemek mantıksız —
      örnekleme aralığını simülasyonda kısaltıp ölçekleyeceğiz, bunu README'de belirt.
