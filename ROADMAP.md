# MailMind AI — Roadmap & GitHub Issues

> Hackathon günü iş takip listesi. Her issue bir PR'a karşılık gelir.

---

## Görev Dağılımı

| Kişi            | Rol               | Sorumluluk                                  |
|-----------------|-------------------|---------------------------------------------|
| Mehmet Ali      | Lead / Maintainer | Repo yönetimi, PR onayı, deploy, bu dosya   |
| Eren            | Feature Dev       | Frontend (Issue #1, #4, #5)                 |
| Arda            | Feature Dev       | Backend + API (Issue #2, #3, #6)            |

---

## GitHub Issues

### Issue #1 — `feat: project setup + tailwind + PWA manifest`
**Atanan:** Mehmet Ali  
**Branch:** `feat/project-setup`  
**Görevler:**
- [ ] `npx create-next-app mailmind --typescript --tailwind`
- [ ] `public/manifest.json` ekle (PWA)
- [ ] `electron/main.js` iskelet oluştur
- [ ] `.env.local.example` dosyası oluştur
- [ ] `ARCHITECTURE.md` ve `ROADMAP.md` repoya ekle

---

### Issue #2 — `feat: gmail oauth + inbox fetch`
**Atanan:** Arda  
**Branch:** `feat/gmail-integration`  
**Görevler:**
- [ ] `npm install next-auth googleapis`
- [ ] `app/api/auth/[...nextauth]/route.ts` — Google OAuth kurulumu
- [ ] Gmail scope: `gmail.readonly` + `gmail.send`
- [ ] `lib/gmail.ts` — `fetchInbox()` ve `sendReply()` fonksiyonları
- [ ] `app/api/gmail/route.ts` — inbox endpoint

**AI Traceability:** Bu modülün güvenlik katmanı Skills Agent ile optimize edildi.

---

### Issue #3 — `feat: claude classify + draft API`
**Atanan:** Arda  
**Branch:** `feat/claude-integration`  
**Görevler:**
- [ ] `npm install @anthropic-ai/sdk`
- [ ] `lib/systemPrompts.ts` — 4 işletme tipi sistem promptu
- [ ] `lib/claude.ts` — `classifyAndDraft()` fonksiyonu
- [ ] JSON parse + hata yönetimi
- [ ] `app/api/classify/route.ts` endpoint

**AI Traceability:** Tüm prompt yapısı Plan Agent çıktısı (ARCHITECTURE.md §4).

---

### Issue #4 — `feat: inbox list UI`
**Atanan:** Eren  
**Branch:** `feat/inbox-ui`  
**Görevler:**
- [ ] `app/page.tsx` — e-posta listesi
- [ ] `components/EmailCard.tsx` — gönderen, konu, zaman
- [ ] `components/CategoryBadge.tsx` — renkli etiketler
- [ ] `components/BusinessSelector.tsx` — açılır menü
- [ ] Mobil responsive (Tailwind `sm:` breakpoints)

---

### Issue #5 — `feat: review screen (side-by-side)`
**Atanan:** Eren  
**Branch:** `feat/review-screen`  
**Görevler:**
- [ ] `app/review/[id]/page.tsx`
- [ ] `components/ReviewPanel.tsx` — sol: orijinal mail, sağ: taslak
- [ ] Düzenle / Onayla / Reddet butonları
- [ ] Onayda `api/send` çağrısı
- [ ] Loading state (Claude yanıt üretirken spinner)

---

### Issue #6 — `feat: send reply + electron build`
**Atanan:** Arda  
**Branch:** `feat/send-and-desktop`  
**Görevler:**
- [ ] `app/api/send/route.ts` — Gmail API ile gönderim
- [ ] Electron build testi (`npm run electron`)
- [ ] Vercel deploy (web demo URL)

---

## Zaman Planı (09:30 — 11:00)

```
09:30  Repo açılır, herkes branch'ini oluşturur
09:35  Issue #1 tamamlanır (Mehmet Ali, ~10 dk)
09:45  Issue #2 + #3 paralel başlar (Arda)
09:45  Issue #4 paralel başlar (Eren)
10:15  Issue #5 başlar (Eren) — #4 PR merge sonrası
10:20  Issue #3 tamamlanır, #4 ile entegrasyon testi
10:35  Issue #6 — deploy + Electron
10:50  Final review: tüm kod Claude ile refactor taraması
11:00  Demo hazır
```

---

## Final Checklist (Teslim Öncesi)

- [ ] `ARCHITECTURE.md` repoda mevcut (Plan Agent kriteri ✅)
- [ ] `ROADMAP.md` repoda mevcut
- [ ] Her PR'da AI Traceability notu var
- [ ] Kod Claude ile refactor taramasından geçti (Süreç Şeffaflığı ✅)
- [ ] Demo URL çalışıyor
- [ ] Electron masaüstü açılıyor
- [ ] PWA telefona eklenebiliyor
