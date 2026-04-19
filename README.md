# ⚙️ LinkedIn Scheduler — n8n Workflow

[LinkedIn Scheduler](https://github.com/yasir237/linkedin-scheduler-panel) panelinden üretilen `posts.json` dosyasını okuyarak belirlenen saatlerde e-posta ve masaüstü bildirimi gönderen otomasyon workflow'u.

---

## Nasıl çalışır?

```
posts.json → n8n → E-posta + Masaüstü Bildirimi
```

Workflow belirli saatlerde çalışır, `posts.json` dosyasını okur ve **bir sonraki güne** ait `status: "scheduled"` olan postları bulur. Post varsa bildirim gönderir, yoksa sessizce durur. Bildirime tıklayınca panel otomatik açılır.

---

## Gereksinimler

- [Node.js](https://nodejs.org) v18+
- [n8n](https://n8n.io) (self-hosted)
- Gmail hesabı
- Windows için [BurntToast](https://github.com/Windos/BurntToast) PowerShell modülü

---

## Kurulum

### 1. n8n'i kur ve başlat

```bash
npm install -g n8n
n8n start
```

Tarayıcıda aç: `http://localhost:5678`

### 2. Workflow'u import et

- Sol menü → **Workflows** → sağ üst üç nokta → **Import from file**
- `n8n-workflow.json` dosyasını seç ve kaydet

### 3. Dosya yolunu ayarla

**"Read/Write Files from Disk"** node'una çift tıkla, **File Path** alanına `posts.json` dosyanın tam yolunu yaz:

```
# Windows
C:/Users/KULLANICI_ADINIZ/linkedin-scheduler/posts.json

# Mac / Linux
/Users/kullanici/linkedin-scheduler/posts.json
```

> ⚠️ Windows'ta ters slash `\` yerine düz slash `/` kullan.

### 4. Scheduler URL'ini ayarla

**"Bildirim İçeriği Hazırla"** node'unu aç, en üstteki `schedulerUrl` satırını güncelle:

```javascript
const schedulerUrl = 'file:///C:/Users/KULLANICI_ADINIZ/linkedin-scheduler/panel/linkedin-scheduler.html';
```

### 5. Gmail bağlantısı

**"E-posta Gönder"** node'una çift tıkla:

- **Credentials** alanında Gmail OAuth2 bağla
- **Send To** alanına e-posta adresini yaz

Gmail OAuth2 oluşturmak için:
1. [Google Cloud Console](https://console.cloud.google.com) → Yeni proje oluştur
2. **Gmail API**'yi etkinleştir
3. **OAuth 2.0 Client ID** oluştur (Web uygulaması)
4. Redirect URI olarak `http://localhost:5678/rest/oauth2-credential/callback` ekle
5. n8n → Credentials → Add Credential → Gmail OAuth2

### 6. Masaüstü bildirimi (Windows)

PowerShell'i yönetici olarak aç:

```powershell
Install-Module -Name BurntToast -Force
```

### 7. Test et ve etkinleştir

- Workflow'da **Execute Workflow** butonuna bas
- Her node'da yeşil tik görünmeli
- E-posta ve masaüstü bildirimi geldiyse hazır
- Sağ üstteki **Active** toggle'ını aç

---

## Zamanlama

Varsayılan olarak günde 3 kez çalışır ve **yarına** ait postları bildirir:

| Cron | Türkiye Saati (UTC+3) |
|------|----------------------|
| `0 1 * * *` | 08:00 |
| `0 6 * * *` | 13:00 |
| `0 13 * * *` | 20:00 |

Değiştirmek için **Schedule Trigger** node'unu aç ve cron ifadelerini düzenle.

---

## Sorun Giderme

| Sorun | Çözüm |
|-------|-------|
| `posts.json bulunamadı` | File Path değerini kontrol et. Türkçe karakter içeren klasör adlarından kaçın. |
| E-posta gelmiyor | Gmail OAuth2 credential'ın doğru bağlandığını kontrol et. |
| Masaüstü bildirimi gelmiyor | Windows bildirim ayarlarında "Rahatsız etme" modunun kapalı olduğunu kontrol et. |
| `n8n start` sonrası panel açılmıyor | `http://localhost:5678` adresini tarayıcıda manuel olarak aç. |
| Workflow çalışmıyor | Sağ üstte **Active** toggle'ının açık (yeşil) olduğunu kontrol et. |
| Zamanında bildirim gelmiyor | Manuel çalıştır ve saat sistemini öğren, ona göre cron yeniden yaz. |

---

## Windows'ta Otomatik Başlatma (İsteğe Bağlı)

Bilgisayar açıldığında n8n'in otomatik başlaması için:

1. Masaüstünde sağ tıkla → Yeni → Kısayol
2. Hedef: `cmd /c "n8n start"`
3. Kısayola sağ tıkla → Kopyala
4. `Win + R` → `shell:startup` → Enter
5. Kısayolu bu klasöre yapıştır

---

## Ekran Görüntüleri

<img width="1919" height="920" alt="Ekran görüntüsü 2026-04-19 185131" src="https://github.com/user-attachments/assets/0fe6f0f1-6ba1-46a1-9b25-6ed804847c66" />

---

<img width="1919" height="912" alt="Ekran görüntüsü 2026-04-19 185219" src="https://github.com/user-attachments/assets/810519cd-e7d0-4e30-9a58-99e8963285ea" />

---

<img width="400" height="231" alt="Ekran görüntüsü 2026-04-19 185253" src="https://github.com/user-attachments/assets/53244dc2-b5a8-49b6-8c5a-38bfabfbe897" />




