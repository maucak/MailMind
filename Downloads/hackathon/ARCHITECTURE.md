# MailMind AI — Architecture

> AI-powered email assistant. Auto-reads Gmail, classifies emails, generates reply drafts.
> Works on Web, Mobile (PWA), and Desktop (Electron).

---

## 1. System Overview

```
Gmail Inbox
    │
    ▼ OAuth 2.0
┌─────────────────────────────────┐
│         Next.js Frontend        │  ← Web / PWA / Electron wrapper
│  - Inbox list                   │
│  - Side-by-side review screen   │
│  - Business type selector       │
└────────────┬────────────────────┘
             │ REST API calls
             ▼
┌─────────────────────────────────┐
│        Next.js API Routes       │  ← Backend (serverless)
│  /api/gmail  — fetch emails     │
│  /api/classify — Claude call    │
│  /api/send   — send reply       │
└────────────┬────────────────────┘
             │
    ┌────────┴────────┐
    ▼                 ▼
Gmail API        Claude API
(Google)         claude-sonnet-4-20250514
```

---

## 2. Tech Stack

| Layer       | Technology                          | Why                          |
|-------------|-------------------------------------|------------------------------|
| Frontend    | Next.js 14 (App Router)             | Web + PWA + Electron ready   |
| Styling     | Tailwind CSS                        | Fast, responsive, mobile-first|
| Desktop     | Electron (wraps Next.js build)      | Web → Desktop in minutes     |
| Mobile      | PWA (manifest + service worker)     | No app store needed          |
| Auth        | NextAuth.js + Google OAuth 2.0      | Gmail scope erişimi          |
| Email       | Gmail API (googleapis npm)          | Read + send                  |
| AI          | Anthropic SDK (claude-sonnet-4-20250514) | Classify + draft reply   |
| State       | React useState / Context            | Simple, no Redux needed      |
| Deploy      | Vercel                              | Free tier, instant deploy    |

---

## 3. Folder Structure

```
mailmind/
├── app/
│   ├── page.tsx              # Inbox list
│   ├── review/[id]/page.tsx  # Side-by-side review screen
│   ├── settings/page.tsx     # Business type selector
│   └── api/
│       ├── auth/[...nextauth]/route.ts
│       ├── gmail/route.ts    # Fetch inbox
│       ├── classify/route.ts # Claude call
│       └── send/route.ts     # Send reply
├── components/
│   ├── EmailCard.tsx
│   ├── ReviewPanel.tsx       # Original | Draft side-by-side
│   ├── BusinessSelector.tsx  # Dropdown: e-ticaret, hukuk, İK...
│   └── CategoryBadge.tsx     # Şikayet / CV / Bilgi / Spam
├── lib/
│   ├── gmail.ts              # Gmail API helpers
│   ├── claude.ts             # Anthropic SDK + system prompts
│   └── systemPrompts.ts      # Per-business-type prompt library
├── electron/
│   └── main.js               # Electron entry point
├── public/
│   └── manifest.json         # PWA manifest
└── .env.local
    # GOOGLE_CLIENT_ID
    # GOOGLE_CLIENT_SECRET
    # ANTHROPIC_API_KEY
```

---

## 4. Core Logic — Claude Classification Flow

```typescript
// lib/claude.ts  (Plan Agent tarafından tasarlandı)
const SYSTEM_PROMPTS: Record<string, string> = {
  ecommerce: `Sen bir e-ticaret firmasının müşteri hizmetleri asistanısın.
Türkçe yanıt ver. Şikayette özür dile ve sipariş no iste.
CV gelirse İK'ya iletildiğini söyle. Spam'i atla.`,

  legal: `Sen bir hukuk bürosunun asistanısın. Türkçe, resmi yanıt ver.
Hukuki görüş bildirme. Danışmanlık taleplerinde randevu öner.`,

  hr: `Sen bir insan kaynakları departmanının asistanısın. Türkçe yanıt ver.
CV/başvurularda alındı bildir (2-3 hafta süreç). Şikayetlerde ilgili müdüre ilet.`,

  accounting: `Sen bir muhasebe firmasının asistanısın. Türkçe, profesyonel yanıt ver.
Fatura/ödeme sorularında hesap yöneticisine yönlendir.`,
};

export async function classifyAndDraft(emailBody: string, businessType: string) {
  const response = await anthropic.messages.create({
    model: "claude-sonnet-4-20250514",
    max_tokens: 1000,
    system: SYSTEM_PROMPTS[businessType],
    messages: [{
      role: "user",
      content: `Aşağıdaki e-postayı analiz et ve JSON olarak yanıt ver:
{
  "category": "sikayet|cv|bilgi_talebi|spam|diger",
  "priority": "yuksek|orta|dusuk",
  "sentiment": "olumsuz|notr|olumlu",
  "draft_reply": "hazır yanıt metni (spam ise null)"
}

E-posta:
${emailBody}`
    }]
  });
  return JSON.parse(response.content[0].text);
}
```

---

## 5. Platform Strategy

### Web
- Next.js → Vercel deploy → tarayıcıda çalışır
- Demo URL jüriye gösterilebilir

### Mobile (PWA)
- `public/manifest.json` ekle
- Service worker ile offline cache
- Telefona "Ana ekrana ekle" → uygulama gibi çalışır
- App store gerekmez

### Desktop (Electron)
```javascript
// electron/main.js
const { app, BrowserWindow } = require('electron');
app.whenReady().then(() => {
  new BrowserWindow({ width: 1200, height: 800 })
    .loadURL('http://localhost:3000'); // Next.js dev server
});
```
- `npm run electron` ile masaüstü uygulama açılır

---

## 6. AI Traceability (Jüri Kriteri)

Bu dosya **Plan Agent** tarafından oluşturulmuştur.
Tüm API route yapısı ve sistem prompt mimarisi Claude Sonnet 4 ile tasarlanmıştır.
Skills Agent kullanımı: `lib/claude.ts` içindeki JSON extraction prompt optimizasyonu.

---

## 7. %50+ Tasarruf Kanıtı

| Adım              | Eski süre     | Yeni süre       | Tasarruf |
|-------------------|---------------|-----------------|----------|
| E-posta okuma     | 1-2 dk        | Otomatik        | %100     |
| Kategori kararı   | 30 sn         | Anlık           | %100     |
| Yanıt yazma       | 5-10 dk       | 15 sn onay      | %95      |
| **Toplam**        | **~12 dk**    | **~20 saniye**  | **%97**  |
