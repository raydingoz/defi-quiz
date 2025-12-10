# Defibrilasyon Quiz Paketi (Anon Mod)

Defibrilasyon ve elektriksel kardiyoversiyon eğitimleri için, **JSON + Firebase** tabanlı hızlı bir quiz paketi. Klinik sahneyi anlık skorlama ve canlı dağılım grafikleriyle gamifiye eder.

Odak: ERC 2025 *Defibrilasyon ve Elektriksel Kardiyoversiyon* ilkeleri. İsim istemiyoruz; herkes gizli kahraman.

---

## Paket Haritası

- `index.html`
  - Host (eğitmen) ve Player (katılımcı) ekranlarına açılan basit kapı.
- `defi-quiz-config.json`
  - Quiz sorularının bulunduğu JSON.
  - 15 erişkin/pediatrik, travma ve gebelik odağında soru.
  - Her soru için: `id`, `title`, `stem`, `options` (A–D), `correct`, `tags`, `timeLimitSec`, `explanation`.
- `defi-quiz-host.html`
  - Eğitmen ekranı:
    - Soruları JSON’dan çeker, listeletir.
    - Firebase Realtime Database ile katılımcı listesi, skor tablosu, canlı seçenek dağılımı, aktif soru kontrolü.
    - Katılımcılar için QR kod üretir (player sayfasına ışınlanma).
    - Puanlama: ilk doğru 20, sonraki doğrular 19 → 16, geri kalan tüm doğrular 15.
- `defi-quiz-player.html`
  - Katılımcı ekranı:
    - Nickname ile giriş (localStorage hafızası var, kimlik yok).
    - Aktif soruyu görür, A/B/C/D ile cevaplar.
    - Cevap tek atımlık, geri alınamaz; puan sırası Firebase’den gelir.
    - Üst barda anlık skor.

---

## Firebase Ayarı

Tüm sayfalar şu konfigürasyonu paylaşır:

```js
const firebaseConfig = {
  apiKey: "AIzaSyDzzwE5VHZAiP2k8P_iocDaq7uGm1zN1cE",
  authDomain: "defi-quiz.firebaseapp.com",
  databaseURL: "https://defi-quiz-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "defi-quiz",
  storageBucket: "defi-quiz.firebasestorage.app",
  messagingSenderId: "642741899462",
  appId: "1:642741899462:web:bcc787a87d0ec69c9b47c7",
  measurementId: "G-SY348RPCVZ"
};
```

Veri yolu: **`defiQuiz`**

- `defiQuiz/players` – Katılımcı listesi ve skorlar.
- `defiQuiz/currentQuestionIndex` – Aktif soru index’i.
- `defiQuiz/currentQuestionId` – Aktif soru için tekil ID.
- `defiQuiz/answers/{questionId}/{playerId}` – Katılımcı cevapları.
- `defiQuiz/answerOrder/{questionId}` – Doğru cevap sırası sayaçları.

Aynı proje içinde ALS ritim simülasyonu için başka bir kök (`defiALS` vb.) açmak serbest.

---

## Saha Kullanımı (Workshop / Sempozyum)

1. **Hazırlık**
   - Bu klasörü GitHub Pages veya başka bir statik hosting’e koy.
   - `defi-quiz-config.json` içindeki soruları ihtiyaçlarına göre düzenle.
2. **Kurulum**
   - Projeksiyonda **`defi-quiz-host.html`** sayfasını aç.
   - QR kod, telefonlardan player ekranına köprü kurar.
3. **Katılım**
   - Katılımcılar QR ile **`defi-quiz-player.html`** adresine girer.
   - Nickname yazar, sahneye çıkar (kimlik yine yok).
4. **Akış**
   - Eğitmen: Soru index’ini seçer, `Soruyu başlat` ile yeni `currentQuestionId` üretir.
   - Katılımcılar: A/B/C/D’den birini seçer; tek seferlik oy.
   - Firebase: Puanlamayı sıralamaya göre 20 → 15 bandında hesaplar, skor tablosu canlı akar.
5. **Final**
   - 15 soru bittiğinde host ekranındaki tablo, oturumun **leaderboard**’udur.
   - `Oturumu sıfırla` ile veri temizlenir, ikinci tur mümkün.

---

## GitHub Pages Üzerinde Çalıştırma

Bu klasörü repo kökünde tuttuğunda:

- Genel URL: `https://<kullanici-adi>.github.io/<repo-adi>/`
- Host ekranı: `https://<kullanici-adi>.github.io/<repo-adi>/defi-quiz-host.html`
- Player ekranı: `https://<kullanici-adi>.github.io/<repo-adi>/defi-quiz-player.html`

Örnek kullanım:

- Repo: `raydingoz/defi-quiz`
- Host: `https://raydingoz.github.io/defi-quiz/defi-quiz-host.html`
- Player: `https://raydingoz.github.io/defi-quiz/defi-quiz-player.html`

Host ekranındaki QR kod, `defi-quiz-config.json` içindeki `meta.playerUrl` alanını otomatik kullanır.

---

## Kendi Soruların

- `defi-quiz-config.json` içindeki `questions` dizisini doldur.
- Her soru için kısa senaryo, 4 seçenek (A–D), net bir doğru (`correct`), etiketler, süre ve eğitmen açıklaması ekle.

Örnek tek soru:

```json
{
  "id": 99,
  "title": "Torsades ve Magnezyum",
  "stem": "QT uzaması zemininde gelişen torsades de pointes için en uygun ilk IV tedavi hangisidir?",
  "options": {
    "A": "Adrenalin",
    "B": "Lidokain",
    "C": "Magnezyum sülfat",
    "D": "Atropin"
  },
  "correct": "C",
  "tags": ["torsades", "magnesium"],
  "timeLimitSec": 12,
  "explanation": "Torsades de pointes yönetiminde IV magnezyum sülfat ilk seçenek tedavilerden biridir."
}
```

---

## Güvenlik Notu

Bu sistem sadece **eğitim** içindir. Gerçek hastalarda defibrilasyon ve ileri yaşam desteği kararları, kılavuzlar, yerel protokoller ve klinik değerlendirme doğrultusunda verilmelidir.
