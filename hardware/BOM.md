# Devre, Bileşen Seçimi ve Bağlantılar

> Bu proje **Wokwi simülatöründe** gerçekleştirildi. Aşağıdaki bileşen seçimleri ve
> gerekçeleri gerçek donanım için geçerlidir — devre fiziksel olarak kurulacak şekilde
> tasarlandı, sadece henüz kurulmadı. Fiyatlar, ileride donanıma geçilirse diye duruyor.

## Bileşenler

| # | Parça | Adet | Yaklaşık fiyat | Neden bu |
|---|-------|------|----------------|----------|
| 1 | ESP32 DevKit v1 (30 pin) | 1 | ~180 TL | WiFi dahili; 12 bit ADC; SPI ve I2C aynı anda kullanılabilir |
| 2 | DHT22 / AM2302 | 1 | ~90 TL | DHT11'e göre çok daha hassas (±0.5 °C / ±%2). Ucuzuna kaçarsak veri güvenilmez olur, ölçüm kanıtı değerini kaybeder |
| 3 | LDR GL5528 | 1 | ~5 TL | Işık şiddetini kabaca ölçmeye yeter; lüks cinsinden mutlak ölçüm gerekmiyor |
| 4 | Direnç 10 kΩ | 1 | ~1 TL | LDR ile gerilim bölücü oluşturur |
| 5 | MicroSD kart modülü (SPI) | 1 | ~35 TL | **3.3 V uyumlu olanı al.** 5 V'luk modüller ESP32'nin pinlerini zorlar |
| 6 | MicroSD kart 8–32 GB | 1 | ~120 TL | FAT32 formatlı olmalı; exFAT kütüphane tarafından okunmaz |
| 7 | Breadboard 830 delik | 1 | ~50 TL | |
| 8 | Jumper kablo seti (E-E, E-D) | 1 | ~30 TL | |

**Toplam:** ~510 TL (ESP32 ve breadboard elde varsa ~280 TL)

## Bağlantı Tablosu

| ESP32 pini | Bağlandığı yer | Açıklama |
|------------|----------------|----------|
| 3V3 | DHT22 VCC, SD modül VCC, LDR bölücü üstü | Besleme |
| GND | Tüm modüllerin GND'si | Ortak toprak |
| GPIO 4 | DHT22 DATA | Dijital tek hatlı veri; 10 kΩ pull-up istenir |
| GPIO 34 | LDR bölücü ortası | **Sadece giriş pini** — ADC1 kanalı, WiFi açıkken de çalışır |
| GPIO 5 | SD modül CS | SPI cihaz seçme |
| GPIO 18 | SD modül SCK | SPI saat |
| GPIO 19 | SD modül MISO | SPI veri girişi |
| GPIO 23 | SD modül MOSI | SPI veri çıkışı |

### Neden GPIO 34?

ESP32'de iki ADC bloğu var: **ADC1** (GPIO 32–39) ve **ADC2** (GPIO 0, 2, 4, 12–15, 25–27).
ADC2, WiFi radyosu ile aynı donanımı paylaşır — **WiFi açıkken ADC2 okuması başarısız olur.**
Bu proje sürekli WiFi kullandığı için ışık sensörünü ADC1'e, yani GPIO 34'e bağlıyoruz.

Bu, ESP32 ile çalışanların en sık düştüğü tuzaklardan biri. Mülakatta sorulursa
anlatabileceğin somut bir detay.

### LDR gerilim bölücü

```
   3.3 V
     │
    [LDR]        ← ışık arttıkça direnci düşer
     │
     ├────────── GPIO 34 (ADC)
     │
   [10 kΩ]
     │
    GND
```

Karanlıkta LDR direnci yüksek (~1 MΩ) → ADC düşük okur.
Aydınlıkta direnci düşük (~1 kΩ) → ADC yüksek okur.

10 kΩ seçimi, oda aydınlığı aralığında ADC'nin orta bölgesinde kalmasını sağlar; çok küçük
bir direnç seçersek karanlık ile aydınlık arasındaki fark ezilir.

## Simülasyonun Sınırları

Wokwi'nin modellemediği, gerçek donanımda karşılaşılacak şeyler — dürüstlük adına
listeliyorum:

- **Sensör gürültüsü ve sürüklenme.** Wokwi'de DHT22 tam istenen değeri döndürür; gerçekte
  ±0.5 °C hata ve zamanla sürüklenme olur.
- **ADC doğrusalsızlığı.** ESP32'nin ADC'si bilinen biçimde doğrusal değildir, özellikle
  0.1 V altı ve 3.0 V üstünde. Gerçek devrede kalibrasyon eğrisi çıkarmak gerekir.
- **Besleme gürültüsü.** USB beslemesindeki dalgalanma ADC okumasına karışır; ayırma
  kapasitörü (100 nF) gerçek devrede şart, simülasyonda fark etmez.
- **SD kart yazma gecikmesi.** Gerçek kartlarda ara sıra 100 ms'i aşan yazmalar olur;
  ölçüm zamanlamasını bozabilir.
- **Kontak/kablo direnci ve gevşek breadboard bağlantıları.**

Donanıma geçilirse bu başlıkların her biri bir doğrulama adımına dönüşür.
