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

## Eksik GIF'ler

Uygulama, her egzersizin animasyonu için `assets/gifs/{exercise-id}.gif`
dosyasını arar. Dosya yoksa egzersize uygun gömülü bir **SVG çizim fallback**
gösterilir — yani uygulama görselsiz kalmaz, ama gerçek hareket demosu için
GIF'leri kendin eklemen gerekir.

Şu **15 GIF eksik** — `assets/gifs/` klasörüne aşağıdaki adlarla koyabilirsin:

| Dosya adı | Egzersiz |
|---|---|
| `walk-in-place.gif` | Yerinde yürüyüş |
| `monster-walk.gif` | Monster walk (bantla yan adım) |
| `calf-raises.gif` | Calf raises |
| `quad-isometric.gif` | Kuadriseps izometrik sıkma |
| `goblet-squat.gif` | Goblet squat |
| `reverse-lunge.gif` | Reverse lunge |
| `single-leg-glute-bridge.gif` | Single-leg glute bridge |
| `kb-rdl.gif` | Kettlebell Romanian deadlift |
| `db-shadow-boxing.gif` | Dambıl shadow boxing |
| `dead-bug.gif` | Pilates topu dead bug |
| `pallof-press.gif` | Bant ile pallof press |
| `band-leg-extension.gif` | Bant ile leg extension |
| `prone-hamstring-curl.gif` | Pilates topu prone hamstring curl |
| `seated-leg-curl.gif` | Bant ile oturarak leg curl |
| `single-leg-calf-raises.gif` | Tek bacak calf raises |

### GIF kaynakları

1. **wger.de** — açık kaynak egzersiz veritabanı, REST API:
   - API kökü: `https://wger.de/api/v2/`
   - Görsel listesi: `https://wger.de/api/v2/exerciseimage/?format=json`
   - Görsel URL biçimi: `https://wger.de/media/exercise-images/{id}/{slug}.png`
   - **Lisans:** CC-BY-SA 3.0 (atıf gerekli). Çoğunlukla statik PNG sunar; GIF azdır.
   - Rehabilitasyon hareketleri (monster walk, pallof press, dead bug, banded
     leg curl) için kapsama zayıftır — büyük ihtimalle bulamayacağın
     hareketler için aşağıdaki ikinci kaynağa veya kendi çekimine başvurman gerekir.

2. **musclewiki.com tarzı kaynaklar** — resmi/hotlinkable olarak ilan edilmemiş
   medya CDN'leri. Doğrudan link verebileceğin garantisi yok, kullanmadan önce
   site şartlarına ve telif durumuna dikkat et. Yerel `assets/gifs/` klasörüne
   indirip kullanmak en güvenli yol.

3. **Kendi çekimin** — telefonla 3–5 sn'lik bir döngü çek, `ffmpeg` ile
   GIF'e çevir:
   ```
   ffmpeg -i input.mp4 -vf "fps=12,scale=480:-1:flags=lanczos" -loop 0 goblet-squat.gif
   ```

GIF'i ekledikten sonra sayfayı yeniden yükle; uygulama otomatik olarak gösterir.

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
