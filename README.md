# Menüsküs Rehab + HIIT

Menüsküs tamiri sonrası rehabilitasyon protokolüme göre düzenlenmiş, fizyoterapist
onaylı, **25 dakikalık** sabit bir antrenman programını telefonda kolayca takip
etmek için yapılmış küçük, tek dosyalık bir web uygulaması.

> Bu program kişisel rehabilitasyon protokolüne göre düzenlenmiştir.
> Ağrı veya şişlik durumunda durdurun.

## Hızlı başlangıç

`index.html` dosyasını tarayıcıda aç — hepsi bu. Tek bir HTML dosyası, harici
bağımlılık olarak yalnızca Tailwind CDN kullanır. Uygulama mantığı, verisi ve
ilerleme takibi `localStorage`'da, **çevrimdışı** çalışır.

Telefonda kullanmak için: tarayıcı menüsünden **"Ana ekrana ekle"** — PWA
manifest gömülüdür, ikonla birlikte standalone uygulama gibi açılır.

## Program özeti

| Blok | Süre | Yapı |
|---|---|---|
| **Isınma** | 4 dk | Yerinde yürüyüş 1 dk · Monster walk · Calf raises 15 · Kuadriseps izometrik 10 sn × 5 |
| **Blok 1 · Güç süperseti** | 10 dk | 3 tur · Goblet squat 12 · Reverse lunge 10/bacak · Single-leg glute bridge 12 · 30 sn dinlenme |
| **Blok 2 · HIIT** | 8 dk | 2 tur · 40 sn çalış / 20 sn dinlen · KB RDL · Shadow boxing · Dead bug · Pallof press |
| **Blok 3 · İzole bitirme** | 3 dk | 2 set × 15 · Bant leg extension · Prone ham curl · Seated leg curl · Tek bacak calf raises |

**Haftalık plan:** Pzt/Çar/Cum antrenman · diğer her gün bisiklet 30–45 dk.

## Özellikler

- Mobile-first responsive arayüz, açık/karanlık mod (sistem tercihine göre)
- Otomatik HIIT 40/20 sn döngüsü, izometrik tutuş için 10 sn × 5 sayacı, süperset
  turlar arası 30 sn dinlenme — hepsi tek bir tutarlı zamanlayıcıyla
- Önceki / Atla / Duraklat kontrolleri tüm modlarda çalışır (timestamp tabanlı
  geri sayım — duraklatma süreyi yemez)
- Adım sonlarında Web Audio beep + Vibration API titreşim
- Wake Lock API ile antrenman sırasında ekran kapanmaz
- LocalStorage'da: tamamlanan antrenmanlar, gün serisi (streak), 8 haftalık ısı
  haritası, toplam ve haftalık kalori
- **Kilonuza göre MET tabanlı kalori hesabı** (Isınma 3.0 · Güç 5.0 · HIIT 8.0 ·
  Bitirme 3.5 · Bisiklet 7.0 MET); ana ekrandan kilo güncellenebilir
- Bisiklet günü için 30/45 dk geri sayım modu
- PWA manifest gömülü, ana ekrana eklenebilir

## Egzersiz videoları ve görselleri

Her egzersiz kartında bir **poster** (gerçek fotoğraf, yoksa animasyonlu SVG) ve
üstünde bir **▶ oynat** butonu vardır. Butona basınca o egzersizin **YouTube
videosu kart içinde (inline) oynatılır**. Video kullanıcı dokunuşuyla yüklenir
(lazy) — sayfa hızlı açılır, 15 video aynı anda yüklenmez.

### Video listesini düzenleme

Video ID'leri `index.html` içindeki **`VIDEOS`** tablosundadır:

```js
const VIDEOS = {
  'goblet-squat': { id: 'gCESNsDsbqk' },
  // kısa bir bölüme kırpmak için (saniye):
  // 'goblet-squat': { id: 'gCESNsDsbqk', start: 12, end: 30 },
  ...
};
```

- Bir videoyu beğenmezsen `id`'yi değiştir (YouTube linkindeki `watch?v=` sonrası kısım).
- Sadece kısa bir bölümün oynaması için `start` / `end` (saniye) ekle.
- Bir video **gömülemez** (kanal embed'i kapatmış) veya silinmişse, poster yerinde
  kalır ve oynatma çalışmaz — o satırdaki `id`'yi çalışan bir videoyla değiştir.

> Not: Video ID'leri YouTube aramasından seçildi; çoğu sorunsuz gömülür ama
> YouTube tarafında değişiklik olabileceğinden zamanla biri çalışmazsa yukarıdaki
> gibi değiştirmen yeterli.

### Posterler

12 hareketin posteri **gerçek fotoğraf** ([free-exercise-db](https://github.com/yuhonas/free-exercise-db),
lisans **The Unlicense** / kamu malı): yerinde yürüyüş, calf raises, goblet squat,
reverse lunge, single-leg glute bridge, kettlebell RDL, dead bug, pallof press,
leg extension, prone hamstring curl, oturarak leg curl, tek bacak calf raises.

Kalan 3 hareket (monster walk, dambıl shadow boxing, kuadriseps izometrik)
veritabanında olmadığından **animasyonlu SVG** posterle gösterilir.

### Kendi görselini kullanmak (opsiyonel)

İstersen `assets/gifs/{id}.gif` ekleyebilirsin; ilgili egzersizin görsel/poster
mantığını kendine göre uyarlamak için `gifBox` fonksiyonuna bakabilirsin.

## Teknik notlar

- Tek dosya: `index.html` (~62 KB) — head'de Tailwind CDN config, gövdede tek
  IIFE içinde nesne-modüller (`DATA`, `Store`, `Engine`, `FX`, `Router`,
  `Screens`).
- Adım motoru, programı düz bir **step list**'e açar (warmup + 3-tur süperset +
  HIIT work/rest çiftleri + finisher setleri). Her adım `{kind, exerciseId,
  duration, …}` formatında — tek tip motor tüm modlar için çalışır.
- Geri sayım `requestAnimationFrame` + `endsAt = Date.now() + ms` ile
  timestamp tabanlı, yani `setInterval` kayması yaşanmaz; duraklatmada
  kalan süre dondurulur, devam edince yeniden hesaplanır.
- Wake Lock, Vibration ve Web Audio API'leri yetenek-kontrollü sarmalayıcılar
  içinde — desteklemeyen tarayıcıda sessizce no-op olur.
- `file://` üzerinden açıldığında service worker çalışmaz, dolayısıyla PWA
  manifest yalnızca "ana ekrana ekle" deneyimini sağlar; gerçek offline cache
  için statik HTTP üzerinden servis edilmesi gerekir.

## Lisans

Bu uygulama kişisel kullanım içindir.
