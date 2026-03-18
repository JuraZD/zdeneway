# Zadar Private Tours – Web stranica turističkog vodiča

> **Projekt:** Osobna web stranica za privatnog turističkog vodiča Ivan Kovač, Zadar  
> **Verzija:** 1.0.0  
> **Datum:** Ožujak 2025.  
> **Tehnologije:** HTML5 · CSS3 · Vanilla JavaScript · Stripe (checkout) · i18n (HR/EN/FR)

---

## 📋 Sadržaj

1. [Pregled projekta](#pregled-projekta)
2. [Struktura datoteka](#struktura-datoteka)
3. [Sekcije stranice](#sekcije-stranice)
4. [Višejezičnost (i18n)](#višejezičnost-i18n)
5. [Booking sustav](#booking-sustav)
6. [Stripe integracija](#stripe-integracija)
7. [Galerija](#galerija)
8. [Deployment](#deployment)
9. [Sljedeći koraci](#sljedeći-koraci)
10. [Promjene i dodavanje sadržaja](#promjene-i-dodavanje-sadržaja)

---

## Pregled projekta

Statična single-page web stranica (SPA) za privatnog turističkog vodiča koji djeluje na području Zadra i okolice. Stranica je dizajnirana s mediteranskom luksuznom estetikom — tamna mornarsko-plava paleta, zlatni akcenti i serifna tipografija (Cormorant Garamond).

### Ključne funkcionalnosti

- Višejezičnost: **Hrvatski, Engleski, Francuski** — prebacivanje bez refresha stranice
- **Booking obrazac** s odabirom ture, datuma i broja osoba
- **Stripe checkout modal** — simulacija online plaćanja karticom
- **Fotografska galerija** s hover efektima i CSS grid layoutom
- Potpuno **responzivni dizajn** (mobilni, tablet, desktop)
- Animirana hero sekcija s parallax efektom
- Sticky navigacija koja mijenja stil pri scrollanju

---

## Struktura datoteka

```
zadar-tour-guide/
├── index.html        # Cijela stranica (HTML + CSS + JS u jednoj datoteci)
└── README.md         # Ova dokumentacija
```

Projekt je namjerno organiziran kao **single-file** rješenje radi jednostavnosti deploymenta — idealno za Netlify drag-and-drop ili GitHub Pages.

Za produkcijsku verziju preporuča se razdvajanje u zasebne datoteke:

```
zadar-tour-guide/
├── index.html
├── css/
│   └── styles.css
├── js/
│   ├── i18n.js
│   ├── booking.js
│   └── main.js
├── images/           ← zamijeniti Unsplash slike vlastitim fotografijama
└── README.md
```

---

## Sekcije stranice

### 1. Navigacija (`<nav>`)

Fiksna gornja traka s logotipom, linkovima i prekidačem jezika. Mijenja veličinu pri scroll poziciji > 60px (`nav.scrolled`).

```html
<nav id="main-nav">
  <!-- logo | linkovi | lang-switcher -->
</nav>
```

**Stil pri scrollu** — kontrolira se JavaScript klasom:
```javascript
window.addEventListener('scroll', () => {
  document.getElementById('main-nav')
    .classList.toggle('scrolled', window.scrollY > 60);
});
```

---

### 2. Hero sekcija (`#hero`)

Puno-ekranski uvodni banner s:
- Pozadinskom slikom s animiranim zoom efektom (`heroZoom`, 14s)
- Naslovom koji se prevodi s promjenom jezika
- Dva CTA gumba: **Rezerviraj** i **Pogledaj ponudu**
- Animiranom strelica za scroll

Za zamjenu pozadinske slike promijeniti URL u `.hero-bg` CSS klasi:
```css
.hero-bg {
  background: ..., url('VAŠA_SLIKA_URL') center/cover no-repeat;
}
```

---

### 3. O vodiču (`#about`)

Dvostupčani layout: fotografija vodiča + tekstualni opis s iskaznicama (badge) i statistikama.

**Statistike za izmjenu:**
```html
<div class="stat-num">4.9★</div>   <!-- TripAdvisor ocjena -->
<div class="stat-num">2.400+</div> <!-- Broj gostiju -->
<div class="stat-num">18</div>     <!-- Broj tura -->
```

---

### 4. Ture (`#tours`)

Grid od 3 kartice, svaka s:
- Fotografijom, tagom (Najpopularnije / Novo / Gastronomija)
- Metapodacima: trajanje, max. skupina, jezici
- Opisom i cijenom
- Gumbom koji otvara Stripe modal

**Dodavanje nove ture** — kopirati blok `.tour-card` i izmijeniti sadržaj:
```html
<div class="tour-card">
  <div class="tour-img-wrap">
    <img src="URL_SLIKE" alt="Naziv ture" class="tour-img" />
    <div class="tour-tag">Oznaka</div>
  </div>
  <div class="tour-body">
    <!-- meta, title, desc, price, booking button -->
    <button class="btn-book"
            onclick="openBookingModal('Naziv ture', CIJENA)">
      Rezerviraj
    </button>
  </div>
</div>
```

---

### 5. Galerija (`#gallery`)

CSS Grid layout s 6 fotografija u 2 reda. Prva i četvrta fotografija su šire (span 2 stupca) za dinamičan vizualni ritam.

```css
.gallery-item:nth-child(1) { grid-column: 1 / 3; }
.gallery-item:nth-child(4) { grid-column: 3 / 5; }
```

Hover efekt otkriva natpis iz `data-caption` atributa:
```html
<div class="gallery-item" data-caption="Rimski forum u zoru">
  <img src="URL" alt="opis" />
</div>
```

---

### 6. Booking obrazac (`#booking`)

Kontakt informacije + HTML forma s poljima:
- Ime i prezime / E-mail
- Odabir ture (dropdown)
- Broj osoba / Željeni datum
- Slobodna napomena

Slanje forme otvara Stripe checkout modal:
```javascript
function handleFormSubmit(e) {
  e.preventDefault();
  openBookingModal(tourName, cijena);
}
```

---

### 7. Recenzije (`#reviews`)

Tri kartice s ocjenama, citatima i informacijom o gostu. Za uvezivanje pravih recenzija preporuča se **TripAdvisor Widget** ili **Google Reviews API**.

---

### 8. Footer

Tri stupca: branding + tagline, popis tura, kontakt linkovi.

---

## Višejezičnost (i18n)

### Kako radi

Svi tekstualni elementi imaju `data-i18n` atribut s ključem prijevoda:

```html
<a data-i18n="nav.about">O Meni</a>
<p data-i18n="hero.subtitle">Autentična iskustva...</p>
```

JavaScript objekt `TRANSLATIONS` sadrži prijevode za sva tri jezika:

```javascript
const TRANSLATIONS = {
  hr: { "nav.about": "O Meni", ... },
  en: { "nav.about": "About", ... },
  fr: { "nav.about": "À Propos", ... }
};
```

Pozivom `setLang('fr')` prolazi se kroz sve elemente i zamjenjuje tekst.

### Dodavanje novog jezika (npr. njemački)

**Korak 1** — dodati gumb u navigaciju:
```html
<button class="lang-btn" onclick="setLang('de')">DE</button>
```

**Korak 2** — dodati prijevode u `TRANSLATIONS`:
```javascript
de: {
  "nav.about": "Über mich",
  "nav.tours": "Touren",
  // ... svi ključevi
}
```

**Korak 3** — dodati hero naslov:
```javascript
const heroTitles = {
  hr: ['Otkrijte', 'pravi Zadar'],
  en: ['Discover', 'true Zadar'],
  fr: ['Découvrez', 'le vrai Zadar'],
  de: ['Entdecken Sie', 'das echte Zadar'],  // ← novo
};
```

---

## Booking sustav

### Trenutno stanje (prototip)

Booking modal prikazuje Stripe-like sučelje s:
- Unosom broja kartice (formatiranje 4-4-4-4 automatski)
- Poljem za datum isteka i CVC
- Animacijom obrade (2 sekunde)
- Success porukom s potvrdom

### Integracija pravog kalendara

Za pravi booking sustav preporuča se jedan od:

| Rješenje | Prednosti | Cijena |
|---|---|---|
| **Cal.com** | Open-source, self-hostable, besplatno | Besplatno / od $12/mj |
| **Calendly** | Jednostavna integracija, embed widget | Besplatno / od $10/mj |
| **FareHarbor** | Specijaliziran za ture, upravlja uplatama | % od rezervacije |
| **Bókun** | Integrira Stripe, Google Pay, Apple Pay | 1–1.5% booking fee |

**Cal.com embed primjer** (zamijeniti `#booking` sekciju):
```html
<!-- Umetnuti u #booking sekciju -->
<div style="width:100%;height:100%;overflow:scroll">
  <iframe
    src="https://cal.com/ivan-kovac/zadar-tour"
    frameborder="0"
    style="width:100%;height:600px">
  </iframe>
</div>
```

---

## Stripe integracija

### Pravi Stripe checkout — upute

**Korak 1** — Registracija na [stripe.com](https://stripe.com) i dohvat ključeva iz Stripe Dashboarda.

**Korak 2** — Uvesti Stripe.js u `<head>`:
```html
<script src="https://js.stripe.com/v3/"></script>
```

**Korak 3** — Zamijeniti `simulatePayment()` s pravim Stripe pozivom:
```javascript
const stripe = Stripe('pk_live_VAŠA_PUBLISHABLE_KEY');

async function processPayment(tourName, amount) {
  // 1. Vaš backend kreira Payment Intent i vraća clientSecret
  const response = await fetch('/api/create-payment-intent', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ amount: amount * 100, currency: 'eur' })
  });
  const { clientSecret } = await response.json();

  // 2. Stripe confirmira plaćanje
  const { error } = await stripe.confirmCardPayment(clientSecret, {
    payment_method: {
      card: cardElement,
      billing_details: { name: ime }
    }
  });

  if (error) {
    console.error(error.message);
  } else {
    showSuccessMessage();
  }
}
```

**Korak 4** — Backend (npr. Node.js / Express):
```javascript
// server.js
const stripe = require('stripe')('sk_live_VAŠA_SECRET_KEY');

app.post('/api/create-payment-intent', async (req, res) => {
  const { amount, currency } = req.body;
  const paymentIntent = await stripe.paymentIntents.create({
    amount,
    currency,
  });
  res.json({ clientSecret: paymentIntent.client_secret });
});
```

> ⚠️ **Sigurnost:** Secret key (`sk_live_...`) **nikada** ne smije biti u frontend kodu! Uvijek ide na serveru.

---

## Deployment

### Opcija A — Netlify (preporučeno, besplatno)

1. Otići na [netlify.com](https://netlify.com) i kreirati račun
2. Povući i pustiti (`drag & drop`) mapu `zadar-tour-guide/` na Netlify dashboard
3. Stranica je živa za < 1 minutu na `.netlify.app` domeni
4. Vlastitu domenu (`ivan-zadar-tours.hr`) dodati u Settings → Domains

### Opcija B — GitHub Pages (besplatno)

```bash
# Kreirati repozitorij na GitHub-u, zatim:
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/USERNAME/zadar-tours.git
git push -u origin main
# U Settings → Pages → Source: main branch / root
```

### Opcija C — Vlastiti hosting (cPanel / Shared hosting)

Uploadati `index.html` (i ostale datoteke) u `public_html/` mapu putem FTP-a ili cPanel File Managera.

---

## Sljedeći koraci

Prioritetni redoslijed za produkcijsku verziju:

1. **Vlastite fotografije** — zamijeniti Unsplash placeholdere pravim fotografijama tura i Zadra
2. **Pravi Stripe** — integrirati Stripe.js + Node.js backend (vidi poglavlje Stripe)
3. **Calendly / Cal.com** — ugraditi pravi booking kalendar
4. **TripAdvisor widget** — uvesti stvarne recenzije
5. **Google Analytics / Plausible** — praćenje posjeta
6. **SEO** — dodati `<meta>` tagove, Open Graph, Schema.org (LocalBusiness)
7. **SSL certifikat** — automatski na Netlify/GitHub Pages; na vlastiti hosting Let's Encrypt
8. **GDPR** — Cookie consent banner (GDPR je obavezan za EU)
9. **Kontaktni obrazac backend** — slanje e-maila putem EmailJS ili vlastiti SMTP

---

## Promjene i dodavanje sadržaja

### Izmjena cijene ture

Pronaći u HTML-u odgovarajuću `.tour-price` i `openBookingModal` poziv:
```html
<div class="tour-price"><sup>€</sup>45 <small>/ osobi</small></div>
<button onclick="openBookingModal('Naziv ture', 45)">Rezerviraj</button>
```

Promijeniti `45` na željenu cijenu na oba mjesta.

### Dodavanje/izmjena fotografija u galeriji

```html
<div class="gallery-item" data-caption="Vaš natpis">
  <img src="URL_VAŠE_FOTOGRAFIJE" alt="Opis" />
</div>
```

Preporučene dimenzije: minimalno **800×600px**, optimizirano za web (< 200KB po slici).

### Izmjena kontakt podataka

Pronaći u `#booking` sekciji i ažurirati:
```html
<strong>+385 91 234 5678</strong>
<strong>ivan@zadar-tours.hr</strong>
```

I u footeru:
```html
<a href="tel:+38591234567">+385 91 234 5678</a>
<a href="mailto:ivan@zadar-tours.hr">ivan@zadar-tours.hr</a>
```

---

## CSS varijable (brze izmjene dizajna)

Sve boje projekta definirane su kao CSS varijable na vrhu `<style>` bloka:

```css
:root {
  --navy:     #0b1f35;   /* Glavna tamna boja */
  --terra:    #c4623a;   /* Naranča/terakota akcent */
  --gold:     #c9a84c;   /* Zlatni akcent */
  --sand:     #f2ead9;   /* Svijetla pozadina teksta */
  --cream:    #faf6ef;   /* Pozadina sekcija */
}
```

Promjena `--terra` npr. na `#2271b3` (adriatik plava) mijenja sve akcentne elemente odjednom.

---

*Dokumentacija kreirana uz pomoć Claude AI · Anthropic · 2025*
