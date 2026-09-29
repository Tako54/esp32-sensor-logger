# ESP32 Ortam Veri Kaydedici

**TR** · ESP32 tabanlı, sıcaklık / nem / ışık şiddetini ölçüp SD karta kaydeden ve yerel
ağdaki bir web sayfasından canlı gösteren veri kaydedici. Zaman damgası NTP sunucusundan
alınır, böylece kayıtlar gerçek saatle eşleşir.

**EN** · ESP32-based environmental data logger. Samples temperature, humidity and light
level, writes timestamped records to an SD card, and serves a live dashboard over WiFi.
Time is synchronised via NTP.

> ⚠️ **Bu proje Wokwi simülatöründe gerçekleştirilmiştir; fiziksel donanımda test
> edilmemiştir.** Devre tasarımı, pin seçimleri ve yazılım gerçek donanıma uygun yazıldı,
> ancak ölçüm sonuçları simülasyon çıktısıdır.
>
> *This project was implemented in the Wokwi simulator and has not been validated on
> physical hardware.*

---

## Durum

🚧 Yapım aşamasında — Hafta 1 / 7. gün

| Gün | İş | Durum |
|-----|-----|-------|
| 1 | Tasarım kararları, repo iskeleti, devre planı | ✅ |
| 2 | Wokwi devresi (`diagram.json`) + şema | ⬜ |
| 3 | Çekirdek yazılım (ölçüm, SD kayıt, web sunucu) | ⬜ |
| 4 | Simülasyonu çalıştır, senaryo taraması, ekran görüntüsü | ⬜ |
| 5 | 24 saatlik simülasyon verisi toplama | ⬜ |
| 6 | Python ile grafik ve analiz | ⬜ |
| 7 | Dokümantasyon, yayın | ⬜ |

## Ne İşe Yarar

Bir odanın ya da dış ortamın sıcaklık, nem ve ışık değişimini uzun süre kaydeder. Elde
edilen veri;

- gün içi sıcaklık salınımını,
- güneşlenme süresini (ışık sensörü eşiği ile),
- nem–sıcaklık ilişkisini

gösterir. Bu veri, sonraki projelerde (özellikle **güneş paneli üretim tahmini** ve **mini
ev enerji sistemi**) referans olarak kullanılacak.

## Simülasyonu Çalıştır

Hiçbir şey kurman gerekmiyor — tarayıcıda açılır:

**▶️ [Wokwi'de çalıştır](#)** *(link 2. gün eklenecek)*

Devre tanımı repo'da: [`hardware/wokwi/diagram.json`](hardware/wokwi/)

## Devre

| Parça | Adet | Not |
|-------|------|-----|
| ESP32 DevKit v1 | 1 | WiFi + 12 bit ADC |
| DHT22 (AM2302) | 1 | sıcaklık ±0.5 °C, nem ±2 % |
| LDR + 10 kΩ direnç | 1 | gerilim bölücü ile ADC'ye |
| MicroSD kart modülü (SPI) | 1 | 3.3 V uyumlu |

Pin bağlantıları, bileşen seçim gerekçeleri ve gerçek donanıma geçerken dikkat edilecekler:
[`hardware/BOM.md`](hardware/BOM.md)

## Klasörler

```
src/          ESP32 yazılımı (Arduino framework, C++)
hardware/     devre şeması, malzeme listesi, Wokwi simülasyonu
data/         ham ölçüm kayıtları (CSV)
analysis/     Python grafik ve analiz scriptleri
media/        devre fotoğrafları, ekran görüntüleri
docs/         tasarım notları
```

## Kurulum

*(3. gün doldurulacak)*

## Sonuçlar

*(6. gün doldurulacak — 24 saatlik simülasyon verisinden sıcaklık/nem/ışık grafiği)*

## Nasıl Çalışır

*(7. gün doldurulacak — her modülün ne yaptığı, sade anlatım)*

## Lisans

MIT — bkz. [LICENSE](LICENSE)
