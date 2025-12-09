# Defibrilasyon Interaktif Quiz Paketi

Bu klasör, sempozyum / workshop ortamında kullanmak üzere tasarlanmış,
**JSON + Firebase tabanlı** interaktif defibrilasyon quiz paketini içerir.

Odak: ERC 2025 *Defibrilasyon ve Elektriksel Kardiyoversiyon* ilkeleri.

---

## Dosya Yapısı

- `index.html`  
  Basit bir giriş ekranı.  
  - Host (eğitmen) bilgi quiz ekranına link  
  - Player (katılımcı) bilgi quiz ekranına link

- `defi-quiz-config.json`  
  Bilgi quiz sorularının tutulduğu JSON dosyası.  
  - 15 adet erişkin/pediatrik, travma, gebelik vb. odaklı soru  
  - Her soru için:
    - `id`, `title`, `stem`
    - `options` (A–D)
    - `correct`
    - `tags`
    - `timeLimitSec`
    - `explanation`

- `defi-quiz-host.html`  
  Bilgi quiz **eğitmen ekranı**:
  - `defi-quiz-config.json` dosyasını okuyarak soruları listeler
  - Firebase Realtime Database üzerinden:
    - Katılımcı liste ve skor tablosu
    - Canlı seçenek dağılımları (A/B/C/D sayıları)
    - Aktif soru yönetimi (başlat / bitir / önceki / sonraki)
  - QR kod üretir:
    - Katılımcıların telefonlarından `defi-quiz-player.html` sayfasına hızlı bağlanmasını sağlar
  - Puanlama:
    - İlk doğru: 20 puan
    - Sonraki doğrular: 19, 18, 17, 16
    - Diğer tüm doğrular: 15 puan

- `defi-quiz-player.html`  
  Bilgi quiz **katılımcı ekranı**:
  - Nickname ile katılım (localStorage ile hatırlanır)
  - Eğitmenin başlattığı aktif soruyu görüntüler
  - A/B/C/D butonları ile cevap gönderir
  - Cevap:
    - Bir kez verilebilir, geri alınamaz
    - Doğru ise sıralamaya göre puan hesaplanır
  - Anlık skorunu üst barda gösterir

---

## Firebase Ayarları

Tüm dosyalarda şu Firebase konfigürasyonu kullanılır:

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

- `defiQuiz/players` – Katılımcı listesi ve skorlar
- `defiQuiz/currentQuestionIndex` – Aktif soru index’i
- `defiQuiz/currentQuestionId` – Aktif soru için tekil ID
- `defiQuiz/answers/{questionId}/{playerId}` – Katılımcı cevapları
- `defiQuiz/answerOrder/{questionId}` – Doğru cevaplardaki sıralama sayacı

Aynı Firebase projesi içinde ALS ritim simülasyonu için başka bir kök (`defiALS` gibi) rahatlıkla kullanılabilir.

---

## Kullanım Senaryosu (Sempozyum / Workshop)

1. **Hazırlık**
   - Bu klasörü GitHub Pages veya başka bir statik hosting altına koy.
   - Gerekirse `defi-quiz-config.json` içindeki soruları düzenle (kendi olgularınla değiştir).

2. **Sahada Kurulum**
   - Projeksiyona **`defi-quiz-host.html`** sayfasını yansıt.
   - Ekrandaki QR kod, katılımcıların telefonlarından **player** ekranına gitmesini sağlar.

3. **Katılım**
   - Katılımcılar `defi-quiz-player.html` adresine QR ile girer.
   - Nickname yazarak oturuma dahil olurlar.

4. **Quiz Akışı**
   - Eğitmen:
     - Soru index’ini seçer (Önceki/Sonraki)
     - `Soruyu başlat` düğmesiyle o soruya ait yeni bir `currentQuestionId` oluşturur.
   - Katılımcılar:
     - Butonlar aktif olur, A/B/C/D seçeneklerinden birini işaretler.
   - Firebase:
     - İlk doğru cevabı 20 puan, sonraki doğruları azalan puanlarla hesaplar.
     - Skor tablosu host ekranında anlık güncellenir.

5. **Oturum Sonu**
   - Toplam 15 soru bittikten sonra host ekranındaki skor tablosu final **leaderboard** gibi kullanılabilir.
   - İstersen oturum sonunda `Oturumu sıfırla` ile tüm veriyi temizleyip ikinci bir tur başlatabilirsin.

---

## GitHub Pages Altında Çalıştırma

Repository içinde bu klasörü **kök** olarak kullanırsan:

- GitHub Pages URL’in:  
  `https://<kullanici-adi>.github.io/<repo-adi>/`
- Host ekranı:  
  `https://<kullanici-adi>.github.io/<repo-adi>/defi-quiz-host.html`
- Player ekranı:  
  `https://<kullanici-adi>.github.io/<repo-adi>/defi-quiz-player.html`

Senin kullanımın için örnek:

- Repo: `raydingoz/defi-quiz`
- Host: `https://raydingoz.github.io/defi-quiz/defi-quiz-host.html`
- Player: `https://raydingoz.github.io/defi-quiz/defi-quiz-player.html`

Host ekranındaki QR kod, otomatik olarak `defi-quiz-config.json` içindeki `meta.playerUrl` alanını kullanır.

---

## Kendi Sorularınla Çalıştırmak

- `defi-quiz-config.json` içindeki `questions` dizisini kendi olgularınla doldur.  
- Her soru için:
  - Gerçek hayattan kısa bir senaryo
  - 4 seçenek (A–D)
  - Net bir doğru cevap (örn. `"correct": "C"`)
  - Eğitmen tartışması için kısa bir `explanation` (kılavuz ve literatüre dayalı)

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

Bu sistem sadece **eğitim amaçlıdır**;  
gerçek hastalarda defibrilasyon ve ileri yaşam desteği kararları, her zaman
geçerli kılavuzlar, yerel protokoller ve klinik değerlendirme ışığında verilmelidir.
