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

**Haftalık plan:** Pzt/Çar/Cum antrenman · Sal/Cmt bisiklet 30–45 dk · Per/Paz dinlenme.

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

## Egzersiz görselleri

Her egzersiz kartı görseli şu öncelik sırasıyla yükler:

1. `assets/gifs/{exercise-id}.gif` — **kendi GIF'ini koyarsan öncelikli** kullanılır
2. `assets/photos/{id}-0.jpg` + `{id}-1.jpg` — **gerçek fotoğraf** (başlangıç + bitiş
   karesi); uygulama bu iki kareyi sırayla göstererek hareketi animasyonlu canlandırır
3. Gömülü **animasyonlu SVG** çizim — ne GIF ne foto varsa

### Hazır gelen gerçek fotoğraflar (12 hareket)

Aşağıdaki 12 hareket, repoda `assets/photos/` içinde gerçek fotoğraflarla gelir —
kaynak: [free-exercise-db](https://github.com/yuhonas/free-exercise-db),
lisans **The Unlicense** (kamu malı, atıf zorunlu değil):

Yerinde yürüyüş · Calf raises · Goblet squat · Reverse lunge · Single-leg glute
bridge · Kettlebell RDL · Dead bug · Pallof press · Leg extension · Prone
hamstring curl · Oturarak leg curl · Tek bacak calf raises.

### Animasyonlu SVG ile gösterilen 3 hareket

Bu hareketler veritabanında bulunmadığı için gömülü animasyonlu SVG çizimle
gösterilir (hareketi canlandırır ama foto değildir):

- **Monster walk** (bantla yan adım)
- **Dambıl shadow boxing**
- **Kuadriseps izometrik sıkma**

İstersen bunlara da kendi GIF'ini ekleyerek SVG'nin yerini alabilirsin:
`assets/gifs/monster-walk.gif`, `assets/gifs/db-shadow-boxing.gif`,
`assets/gifs/quad-isometric.gif`.

### Görselleri değiştirmek / iyileştirmek

- **Kendi GIF'in** (en gerçekçi): telefonla 3–5 sn döngü çek, `ffmpeg` ile çevir
  ve `assets/gifs/{id}.gif` olarak koy:
  ```
  ffmpeg -i input.mp4 -vf "fps=12,scale=480:-1:flags=lanczos" -loop 0 goblet-squat.gif
  ```
- **wger.de** açık egzersiz veritabanı (CC-BY-SA 3.0): `https://wger.de/api/v2/`

Görseli ekledikten sonra sayfayı yenile; uygulama otomatik gösterir.

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
