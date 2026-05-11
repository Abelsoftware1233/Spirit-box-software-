# PXB-12 SDR Spirit Box

> **Een volledig browser-gebaseerde Spirit Box met echte SDR-dongle ondersteuning**  
> Gebouwd met pure HTML5, CSS3 en Vanilla JavaScript — geen installatie vereist.

---

## Inhoudsopgave

1. [Wat is dit?](#wat-is-dit)
2. [Screenshots & Features](#features)
3. [Snel starten](#snel-starten)
4. [Echte SDR verbinden](#echte-sdr-verbinden)
   - [Benodigde hardware](#benodigde-hardware)
   - [Windows instellen](#windows-instellen)
   - [macOS instellen](#macos-instellen)
   - [Linux instellen](#linux-instellen)
5. [Hoe de app werkt](#hoe-de-app-werkt)
   - [Simulatiemodus](#simulatiemodus)
   - [SDR modus (WebUSB)](#sdr-modus-webusb)
   - [Audio engine](#audio-engine)
   - [Spectrum & Waterval](#spectrum--waterval)
6. [Bediening](#bediening)
7. [Technische architectuur](#technische-architectuur)
8. [Bekende beperkingen](#bekende-beperkingen)
9. [Veelgestelde vragen](#veelgestelde-vragen)
10. [Verdere ontwikkeling](#verdere-ontwikkeling)
11. [Licentie & disclaimer](#licentie--disclaimer)

---

## Wat is dit?

De **PXB-11 SDR Spirit Box** is een browser-app die een klassieke ITC-spiritbox nabootst én kan koppelen aan een echte **RTL-SDR USB-dongle** om live FM-radiofrequenties te sweepen.

### Wat is een Spirit Box?

Een spirit box scant snel door radiofrequenties en speelt de ruis en flarden van radiosignalen af. Paranormale onderzoekers gebruiken het als communicatietool — de gedachte is dat entiteiten stemfragmenten uit de ruis kunnen vormen. Wetenschappelijk gezien is het fenomeen dat mensen woorden horen in willekeurige ruis **auditieve pareidolia**.

### Wat doet deze app?

- **Zonder hardware**: realistische simulatie van FM-sweep met gesimuleerd spectrum
- **Met RTL-SDR dongle**: echte FM-radiofrequenties via WebUSB, live spectrum + waterval

---

## Features

| Feature | Omschrijving |
|---|---|
| 🎚 FM Sweep | Scant 87.5 – 108.0 MHz in stappen van 0.1 MHz |
| ⚡ 4 sweep snelheden | SLOW (60ms), MED (25ms), FAST (10ms), ULTRA (3ms) |
| ↕ 3 sweep richtingen | Omhoog, omlaag, willekeurig |
| 📡 RTL-SDR WebUSB | Directe USB-verbinding met RTL2832U dongle via Chrome/Edge |
| 📊 Live spectrum | Real-time FFT-achtige spectrum weergave van het FM-band |
| 🌊 Waterval display | Tijdsverloop van signaalsterkte per frequentie |
| 🔊 Web Audio Engine | Bandpass-gefilterde ruis gemoduleerd op de sweep-frequentie |
| 👻 Ghost Words | Random paranormale woorden verschijnen op het display |
| 🎛 Gain control | RTL-SDR gain instelling (0–49 dB) |
| 🔉 Volume control | Live volume regeling via Web Audio API |
| 📱 Responsive | Werkt op desktop en mobiel |
| 🟢 Geen installatie | Één HTML-bestand, open lokaal of host het zelf |

---

## Snel starten

### Optie A — Lokaal openen (geen server nodig)

```
1. Download sdr_spiritbox.html
2. Open het bestand in Chrome of Edge
3. Klik ⏻ POWER
4. App start in simulatiemodus
```

> ⚠️ **Belangrijk**: gebruik Chrome of Edge. Firefox ondersteunt geen WebUSB.

### Optie B — Hosten op een webserver

```bash
# Met Python (eenvoudigste optie)
python3 -m http.server 8080

# Open daarna in Chrome:
# http://localhost:8080/sdr_spiritbox.html
```

### Optie C — GitHub Pages / Netlify

Sleep het HTML-bestand naar [netlify.com/drop](https://netlify.com/drop) voor een directe publieke URL.

---

## Echte SDR verbinden

### Benodigde hardware

| Item | Aanbevolen model | Prijs | Kopen |
|---|---|---|---|
| RTL-SDR dongle | RTL-SDR Blog V4 | ~€25 | [rtl-sdr.com](https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/) |
| Antenne | Meegeleverd met V4 | — | — |
| USB kabel | USB-A of USB-C naar micro-USB | ~€2 | Lokale winkel |

De dongle gebruikt een **RTL2832U** chip met **R820T2** tuner. Goedkope alternatieven (€8–15 op AliExpress) werken ook, maar de Blog V4 heeft betere thermische stabiliteit.

---

### Windows instellen

De standaard Windows DVB-T driver werkt **niet** met WebUSB. Je moet de driver vervangen met **WinUSB** via het gratis programma Zadig.

**Stap 1 — Download Zadig**

Ga naar [zadig.akeo.ie](https://zadig.akeo.ie) en download `Zadig 2.x`.

**Stap 2 — Sluit de dongle aan**

Sluit de RTL-SDR aan via USB. Windows installeert mogelijk automatisch de verkeerde DVB-T driver — dat is normaal.

**Stap 3 — Wissel de driver**

```
1. Open Zadig als Administrator
2. Klik: Options → List All Devices
3. Selecteer in de dropdown: "Bulk-In, Interface (Interface 0)"
   (of de naam van je dongle, bijv. "RTL2832U")
4. Rechts van de pijl: selecteer "WinUSB"
5. Klik: Replace Driver
6. Wacht tot "The driver was installed successfully" verschijnt
```

> ⚠️ **Let op**: vervang alleen Interface 0, niet Interface 1. Anders werkt de dongle niet meer als DVB-T ontvanger in andere software.

**Stap 4 — Verbinden in de app**

```
1. Open sdr_spiritbox.html in Chrome of Edge
2. Klik 🔌 SDR USB
3. Selecteer je dongle in de Chrome popup
4. Klik ⏻ POWER
```

---

### macOS instellen

macOS heeft geen extra driverwissel nodig voor WebUSB.

```
1. Sluit de RTL-SDR aan via USB
2. Open Chrome of Edge
3. Open sdr_spiritbox.html
4. Klik 🔌 SDR USB → selecteer de dongle
5. Als macOS vraagt om toegang: sta toe
6. Klik ⏻ POWER
```

Als de dongle niet verschijnt in de popup, probeer dan:

```bash
# Controleer of macOS de dongle herkent
system_profiler SPUSBDataType | grep -A 5 "RTL"
```

---

### Linux instellen

Op Linux moet je een udev-regel toevoegen zodat je browser USB-toegang heeft zonder root.

**Stap 1 — Voeg udev-regel toe**

```bash
sudo nano /etc/udev/rules.d/99-rtlsdr.rules
```

Plak de volgende regel:

```
SUBSYSTEM=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2838", MODE="0666", GROUP="plugdev"
```

**Stap 2 — Herlaad udev**

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

**Stap 3 — Voeg gebruiker toe aan plugdev**

```bash
sudo usermod -aG plugdev $USER
# Log opnieuw in om de groep te activeren
```

**Stap 4 — Blocklist de standaard kernel driver**

```bash
echo "blacklist dvb_usb_rtl28xxu" | sudo tee /etc/modprobe.d/blacklist-rtl.conf
sudo modprobe -r dvb_usb_rtl28xxu
```

**Stap 5 — Verbinden**

```
Open Chrome (niet Chromium — WebUSB werkt beter in officiële Chrome)
Open sdr_spiritbox.html → klik 🔌 SDR USB
```

---

## Hoe de app werkt

### Simulatiemodus

Zonder dongle genereert de app een nep-spectrum op basis van bekende FM-stationfrequenties. Bij elke sweep-stap wordt voor elke spectrumsample een ruiswaarde berekend, met Gaussische pieken gesimuleerd bij echte stationsfrequenties zoals 88.3, 91.7, 98.7 MHz enzovoort.

```
Sweep stap → bereken gesimuleerd spectrum → update display + audio + canvas
```

Het spectrum-array bevat 256 waarden in dBm (–90 tot 0). Bij elke step worden de waarden exponentieel gemiddeld met factor 0.4 voor vloeiende weergave:

```javascript
specData[i] = specData[i] * 0.6 + newValue * 0.4;
```

### SDR modus (WebUSB)

De **WebUSB API** maakt directe USB-communicatie mogelijk vanuit de browser zonder drivers of native apps.

```
Browser → WebUSB → RTL2832U chip → R820T2 tuner → Antenne → FM signaal
```

Bij elke sweep-stap worden twee dingen gedaan:

1. **Frequentie instellen** via een USB control transfer naar de RTL2832U:
   ```javascript
   await device.controlTransferOut({
     requestType: 'vendor', recipient: 'device',
     request: 0xb0, value: 0, index: 0
   }, frequencyBytes);
   ```

2. **IQ-samples lezen** via een bulk transfer van endpoint 1:
   ```javascript
   const result = await device.transferIn(1, 1024);
   // result.data bevat afwisselend I en Q bytes (0–255, gecentreerd op 128)
   ```

De IQ-samples worden omgezet naar vermogen:
```javascript
const I = (raw[i*2]   - 128) / 128;
const Q = (raw[i*2+1] - 128) / 128;
const power_dB = 10 * Math.log10(I*I + Q*Q + 1e-9);
```

> **Noot**: Een volledige RTL-SDR implementatie vereist een volledige init-sequentie van de R820T2 tuner (tientallen register-schrijfacties). Voor productiegebruik is de library [webrtlsdr van jtarrio](https://github.com/jtarrio/webrtlsdr) aanbevolen. Deze app implementeert een vereenvoudigde versie.

### Audio engine

De audio wordt gegenereerd met de **Web Audio API** via een keten van nodes:

```
BufferSource (white noise)
    ↓
BiquadFilter (bandpass, Q=0.6)
    ↓
GainNode (volume ~0.3)
    ↓
GainNode (master)
    ↓
AudioDestination (speakers)

OscillatorNode (sawtooth, freq varieert)
    ↓
GainNode (0.04)
    ↓
GainNode (master)
```

Bij elke sweep-stap worden de filterfrequentie en oscillatorfrequentie vloeiend bijgestuurd op basis van de huidige FM-frequentie:

```javascript
// t = 0.0 bij 87.5 MHz, 1.0 bij 108.0 MHz
const t = (freq - FM_MIN) / (FM_MAX - FM_MIN);
filterNode.frequency.setTargetAtTime(300 + t * 4000, now, 0.008);
sweepOsc.frequency.setTargetAtTime(60 + t * 900,      now, 0.015);
```

### Spectrum & Waterval

**Spectrum canvas** (`bigSpec`): getekend via Canvas 2D API. Per frame worden 256 spectrumswaarden getekend als een gradient-gevulde lijnplot. De huidige frequentie wordt gemarkeerd met een oranje stippellijn.

**Waterval canvas** (`waterfall`): elke sweep-stap wordt het huidig spectrum als een nieuwe rij bovenaan ingevoegd. Oudere rijen schuiven omlaag. Kleur is gebaseerd op signaalsterkte:

| Vermogen | Kleur |
|---|---|
| Zwak (< –76 dBm) | Donkergroen |
| Matig (–76 tot –54 dBm) | Groen → cyaan |
| Sterk (–54 tot –27 dBm) | Geel → oranje |
| Zeer sterk (> –27 dBm) | Rood → wit |

---

## Bediening

### Knoppen

| Knop | Functie |
|---|---|
| **⏻ POWER** | Start of stop de sweep en audio |
| **🔌 SDR USB** | Verbind een RTL-SDR dongle via WebUSB |
| **SLOW / MED / FAST / ULTRA** | Sweep interval: 60ms / 25ms / 10ms / 3ms |
| **▲ UP** | Sweep van laag naar hoog (87.5 → 108.0) |
| **▼ DOWN** | Sweep van hoog naar laag (108.0 → 87.5) |
| **⟳ RAND** | Spring willekeurig per stap |

### Sliders

| Slider | Bereik | Effect |
|---|---|---|
| **GAIN** | 0 – 49 dB | RTL-SDR versterking (bij echte dongle) |
| **VOL** | 0 – 100% | Audiovolume via Web Audio masterGain |

### Display

| Element | Omschrijving |
|---|---|
| Grote getal | Huidige FM-frequentie in MHz |
| SCANNING / STANDBY | Sweepstatus |
| SWEEP: ON/OFF | Sweep actief indicator |
| Ghost word | Willekeurig woord (3% kans per stap) |
| Signaalbalkjes | Gesimuleerde signaalsterkte (1–5 bars) |
| Verbindingsindicator | Groen = SDR verbonden, oranje = simulatie, rood = fout |

---

## Technische architectuur

```
sdr_spiritbox.html
│
├── CSS (embedded)
│   ├── Orbitron font (Google Fonts CDN)
│   ├── Share Tech Mono font (Google Fonts CDN)
│   ├── CSS custom properties (kleurvariabelen)
│   ├── CRT scanlines overlay (::after pseudo-element)
│   └── Responsive layout (flexbox + media query 660px)
│
└── JavaScript (embedded)
    ├── STATE
    │   ├── isOn, sweepMs, sweepDir, currentFreq
    │   └── specData (Float32Array 256), waterfallRows (Array)
    │
    ├── SDR / WebUSB module
    │   ├── connectSDR()       — navigator.usb.requestDevice()
    │   ├── rtlTune(freqHz)    — controlTransferOut vendor request 0xb0
    │   └── rtlRead(n)         — transferIn endpoint 1
    │
    ├── Audio module (Web Audio API)
    │   ├── initAudio()        — AudioContext + master GainNode
    │   ├── buildAudio()       — noise buffer + filter + osc keten
    │   ├── stopAudio()        — nodes stoppen
    │   └── modulateAudio(f)   — filter + osc freq bijsturen
    │
    ├── Spectrum module
    │   ├── genSimSpec(f)      — gesimuleerd spectrum met stationspieken
    │   └── genSDRSpec(f)      — IQ samples → dBm waarden
    │
    ├── Sweep module
    │   ├── sweepStep()        — freq stap + spectrum + audio + ghost
    │   ├── startSweep()       — setInterval(sweepStep, sweepMs)
    │   └── stopSweep()        — clearInterval
    │
    └── Canvas module
        ├── drawSmallSpec()    — device spectrum (56px hoog)
        ├── drawWave()         — animated waveform (34px hoog)
        ├── drawBigSpec()      — groot spectrum (135px hoog)
        ├── drawWaterfall()    — waterval tijdsweergave (135px hoog)
        └── updateSig()        — signaalbalken (random 1–5)
```

Alle canvas-functies draaien in een `requestAnimationFrame` loop en zijn onafhankelijk van de sweep-interval.

---

## Bekende beperkingen

**WebUSB RTL-SDR initialisatie**  
Een volledige RTL-SDR initialisatie vereist tientallen register-schrijfacties naar zowel de RTL2832U demodulator als de R820T2 tuner. Deze app implementeert alleen de frequentie-tune commando en een basis bulk-read. Voor volledige FM-demodulatie (echt hoorbaar geluid van echte radio) heb je de [webrtlsdr library](https://github.com/jtarrio/webrtlsdr) nodig of de kant-en-klare app [radio.ea1iti.es](https://radio.ea1iti.es).

**Geen echte FM demodulatie**  
De audio die je hoort is gegenereerde ruis gemoduleerd op de sweep-frequentie, niet het werkelijke gedemoduleerde FM-signaal van de dongle. Echte FM demodulatie vereist DSP-verwerking (IQ → FM discriminator → audio) die buiten het bereik van deze app valt.

**Browser ondersteuning**  
WebUSB is alleen beschikbaar in Chromium-gebaseerde browsers (Chrome, Edge, Opera). Firefox en Safari ondersteunen het niet.

**Geen persistentie**  
Er is geen opname- of exportfunctie in deze versie.

---

## Veelgestelde vragen

**Q: Mijn dongle verschijnt niet in de USB popup.**  
A: Op Windows — controleer of je de Zadig driver wissel hebt gedaan (zie [Windows instellen](#windows-instellen)). Op Linux — controleer de udev-regel en of `dvb_usb_rtl28xxu` geblocklisted is.

**Q: De app opent maar er is geen geluid.**  
A: Browsers blokkeren audio tot er een gebruikersinteractie is. Klik eerst op **⏻ POWER** — de AudioContext wordt dan pas aangemaakt.

**Q: Werkt dit ook op mijn telefoon?**  
A: Chrome op Android ondersteunt WebUSB met een OTG-adapter (USB-A naar USB-C of micro-USB). iOS ondersteunt WebUSB niet.

**Q: Kan ik andere frequentiebanden sweepen (bijv. AM of luchtvaart)?**  
A: De huidige code is gefixeerd op FM (87.5–108 MHz). Met aanpassingen aan `FM_MIN`, `FM_MAX` en de frequency-tune commando's kun je andere banden toevoegen. AM demodulatie vereist extra DSP.

**Q: Hoe verwijder ik de ghost words?**  
A: Verwijder de `maybeGhostWord()` aanroep in `sweepStep()` of stel de kans `0.025` in op `0`.

**Q: Hoe snel is ULTRA modus?**  
A: 3 ms per sweep-stap = ~333 frequentiewisselingen per seconde. Dat is sneller dan de meeste commerciële spirit boxes (typisch 20–100 ms). Bij SDR-gebruik kan de USB-latency de werkelijke snelheid beperken.

---

## Verdere ontwikkeling

Wil je de app uitbreiden? Hier zijn mogelijkheden:

**Echte FM demodulatie**  
Integreer de [webrtlsdr library](https://github.com/jtarrio/webrtlsdr) van jtarrio voor volledige FM demodulatie inclusief WFM, NFM en AM.

```html
<script type="module">
  import { RTL2832U_Provider } from 'https://unpkg.com/@jtarrio/webrtlsdr/rtlsdr.js';
  // ...
</script>
```

**Opname toevoegen**  
```javascript
const dest = audioCtx.createMediaStreamDestination();
masterGain.connect(dest);
const recorder = new MediaRecorder(dest.stream);
```

**AM band toevoegen**  
Verander `FM_MIN/FM_MAX` naar `0.530/1.710` (MHz) voor AM (530–1710 kHz). Pas de tune-commando's aan naar kHz.

**Spectrum opslaan**  
Export spectrum data als CSV voor analyse:
```javascript
const csv = specData.join('\n');
const blob = new Blob([csv], {type:'text/csv'});
```

**WebRTC remote sharing**  
Bekijk [BrowSDR](https://www.rtl-sdr.com/browsdr-turn-your-hackrf-or-rtl-sdr-into-a-browser-based-remote-websdr/) voor inspiratie om de SDR-stream via WebRTC te delen.

---

## Licentie & disclaimer

**Licentie**: MIT — vrij te gebruiken, aanpassen en distribueren.

**Disclaimer**  
Deze app is gebouwd voor educatieve en entertainmentdoeleinden. De "ghost words" zijn willekeurig gegenereerd door code en hebben geen paranormale betekenis. Wetenschappelijk bewijs voor geestcommunicatie via radiofrequenties bestaat niet. Het fenomeen dat mensen woorden herkennen in ruis is **auditieve pareidolia** — een normale eigenschap van het menselijk brein.

Het gebruik van RTL-SDR hardware voor het ontvangen van radiofrequenties is in de meeste landen legaal voor passief ontvangen. Actief uitzenden op radiofrequenties zonder vergunning is illegaal.

---

## Externe bronnen

- [RTL-SDR Blog](https://www.rtl-sdr.com) — nieuws, tutorials en hardware
- [radio.ea1iti.es](https://radio.ea1iti.es) — volledige browser-SDR app met WebUSB
- [BrowSDR](https://www.rtl-sdr.com/browsdr-turn-your-hackrf-or-rtl-sdr-into-a-browser-based-remote-websdr/) — HackRF/RTL-SDR in de browser via WebRTC
- [webrtlsdr library](https://github.com/jtarrio/webrtlsdr) — JavaScript RTL-SDR library
- [WebUSB API spec](https://wicg.github.io/webusb/) — W3C specificatie
- [Zadig](https://zadig.akeo.ie) — USB driver wissel tool voor Windows

---

*PXB-11 SDR Spirit Box — gebouwd met HTML5, CSS3, Web Audio API en WebUSB*
