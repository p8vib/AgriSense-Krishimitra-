# Implementation Plan: AgriSense (KrishiMitra) Intelligent Farming Platform

Build a complete, responsive, production-grade Intelligent Farming Assistance Platform web application called **"AgriSense" (KrishiMitra / কৃষি মিত্র)** designed to empower farmers with localized agricultural intelligence, crop recommendations, seasonal cultivation guides, pest identification, real-time Mandi market rates, government schemes, and an AI Agronomist assistant.

```mermaid
graph TD
    User([Farmer / Agricultural Officer]) --> Navbar[Navigation & Multi-Language Switcher (EN, HI, AS, ES)]
    Navbar --> Hero[Hero & Regional Weather/Soil Snapshot]
    Navbar --> Recommender[Smart Crop Recommendation Engine]
    Navbar --> Calendar[Seasonal Farming Calendar & Practices]
    Navbar --> Doctor[Crop Doctor & Pest/Disease Diagnostic Tool]
    Navbar --> MandiSchemes[Mandi Market Rates & Government Schemes]
    Navbar --> AIAssistant[Intelligent AI Agronomist Assistant Drawer]
    
    subgraph Data Layer & Analytics
        Recommender --> RecAlgo[Agronomic Suitability Algorithm & Radar Charts]
        Calendar --> PrintExport[Print-Friendly Checklist Generator]
        Doctor --> DiagEngine[Visual & Symptom Diagnostic Engine]
        AIAssistant --> MultilingualLLM[Localized Agri Knowledge Engine]
        Hero --> WeatherAPI[Regional Micro-Climate Simulator]
    end
```

---

## User Review Required

> [!IMPORTANT]
> **Node.js Installation on Host**: Node.js is currently not installed on the Windows system. As part of Phase 1 of execution, we will install Node.js LTS (`OpenJS.NodeJS.LTS`) using `winget` in BypassSandbox mode (since package installation modifies system tools), verify the environment, and scaffold the Vite application at `C:\Users\abasu\.gemini\antigravity\scratch\agrisense`.

> [!NOTE]
> **Dual Architecture (Zero-Config + Express API)**: The frontend will be designed with a hybrid data client that connects directly to the Express backend when running, and gracefully falls back to local rich datasets in client mode. This guarantees the web app runs immediately out-of-the-box with 100% functionality and zero configuration or API key bottlenecks.

---

## Open Questions

1. **Preferred Default Language**: We propose defaulting to **English (EN)** with quick 1-click toggling between **Hindi (हिंदी)**, **Assamese (অসমীয়া)**, and **Spanish (Español)** in the top navbar. Would you prefer English or Hindi as the initial default?
2. **Regional District Presets**: We are pre-configuring 5 diverse agricultural micro-climate presets:
   - *Punjab (Ludhiana)* - Indo-Gangetic Alluvial, Intensive wheat-paddy belt
   - *Assam (Jorhat / Kamrup)* - Humid subtropical, Tea, Rice, and Jute
   - *Maharashtra (Nashik)* - Semi-arid, Black/Clay soil, Grapes, Onions, Pulses
   - *Uttar Pradesh (Varanasi)* - Alluvial fertile plains, Mustard, Vegetables, Pulses
   - *Spain (Valencia / Andalusia)* - Mediterranean, Citrus, Olives, Tomatoes
   Farmers can also freely customize any soil parameter via intuitive sliders. Are these regions suitable?

---

## Proposed Changes

Project will be created in: `C:\Users\abasu\.gemini\antigravity\scratch\agrisense`

### Phase 1: Environment Setup & Project Scaffolding

- Install Node.js LTS via `winget install --id OpenJS.NodeJS.LTS --accept-package-agreements --accept-source-agreements`.
- Scaffold React + Vite application with Tailwind CSS, Lucide React icons, and Recharts.
- Configure Tailwind CSS with the sunlight-readable high-contrast agricultural color palette:
  - Primary Forest: `#1B4332`, Deep Jade: `#2D6A4F`
  - Secondary Leaf: `#40916C`, Mint Accent: `#52B788`
  - Harvest Amber: `#D97706`, Golden Grain: `#F59E0B`
  - Warm Cream Background: `#FAF9F6`, Card Base: `#FFFFFF`

#### [NEW] `package.json`
Dependencies:
- `react`, `react-dom` (v18.x)
- `lucide-react` (high quality accessible agricultural icons)
- `recharts` (soil nutrient radar charts, 7-day rainfall/temperature trends, yield comparisons)
- `clsx`, `tailwind-merge` (clean UI utility classes)
- Dev: `vite`, `tailwindcss`, `postcss`, `autoprefixer`

#### [NEW] `tailwind.config.js`
Custom themes, colors, typography fonts, and print media queries for clean checklist printing.

---

### Phase 2: Comprehensive Multi-Language System (i18n)

Support 4 fully translated languages across the entire platform:
1. **English (EN)**
2. **Hindi (हिंदी)**
3. **Assamese (অসমীয়া)**
4. **Spanish (Español)**

#### [NEW] `src/context/LanguageContext.jsx`
- Manages active language state with localStorage persistence.
- Provides `t(key)` helper with nested key lookup (e.g. `t('hero.title')`, `t('recommender.getRecommendations')`).
- Broadcasts language changes reactively across all UI components, charts, and modal drawers.

#### [NEW] `src/data/translations.js`
- 100% human-verified dictionary covering:
  - Navbar links and CTA labels
  - Hero headlines, badges, and weather indicators
  - Soil parameters (Nitrogen, Phosphorus, Potassium, pH, Texture, Drainage)
  - Seasonal terminology (Kharif/Monsoon, Rabi/Winter, Zaid/Summer)
  - Crop names and cultivation terminology
  - Pest symptoms, remedies (organic vs chemical), severity badges
  - Mandi market rates, MSP, volume units (Quintal, Hectare, Acre)
  - Government scheme descriptions and eligibility requirements
  - AI Assistant prompt chips and conversational templates

---

### Phase 3: Core Agricultural Datasets & Local Data Layer

#### [NEW] `src/data/cropsData.js`
Rich agronomic database of 12+ crops:
- Rice / Paddy (धान / ধান / Arroz)
- Wheat (गेहूं / ঘেঁহু / Trigo)
- Maize (मक्का / গোমধান / Maíz)
- Mustard (सरसों / সর্ষে / Mostaza)
- Tea (चाय / চাহ / Té)
- Potato (आलू / আলু / Patata)
- Tomato (टमाटर / বিলাহী / Tomate)
- Chickpea / Pulses (चना / বুট / Garbanzo)
- Cotton (कपास / কপাহ / Algodón)
- Sugarcane (गन्ना / কুঁহিয়াৰ / Caña de azúcar)
- Jute (पटसन / মৰাপাট / Yute)
- Soybean (सोयाबीन / ছয়াবিন / Soja)

Each crop model includes:
- Ideal N-P-K range and soil pH range
- Preferred climate zones and water requirements
- Expected yield range per hectare ($T/\text{ha}$)
- Market demand rating and profitability index
- High-resolution verified photo URL with inline CSS/SVG fallback
- Complete 5-phase Cultivation Timeline (Land preparation $\to$ Sowing & Seed treatment $\to$ Vegetative & Weeding $\to$ Flowering & Grain fill $\to$ Harvest)
- Seed rate guidelines & fertilizer dosage schedule (Basal, Top Dressing 1 & 2)
- Common pest vulnerabilities and management

#### [NEW] `src/data/diseasesData.js`
Diagnostic knowledge base for the Crop Doctor:
- Early Blight & Late Blight (Solanaceae)
- Rice Blast & Bacterial Leaf Blight
- Wheat Stripe Rust & Powdery Mildew
- Whitefly & Aphid infestation
- Leaf Curl Virus
- Each with: Visual symptom descriptors, affected plant parts, severity level, Organic remedy (Neem extract, Trichoderma, Wood ash, cow urine decoction), Chemical remedy (Mancozeb, Copper Oxychloride, Imidacloprid with safe waiting period), and Cultural prevention tips.

#### [NEW] `src/data/weatherData.js` & `mandiAndSchemesData.js`
- Regional weather snapshots with 7-day temperature, rainfall probability, and soil moisture trends.
- Mandi commodity price indices (MSP vs current market rate, daily delta, arrival quantities).
- Central and State schemes (PM-KISAN, PM Fasal Bima Yojana, Soil Health Card, Solar Pump Subsidy / PM-KUSUM).

---

### Phase 4: UI Components & Interactive Tools

#### [NEW] `src/components/Navbar.jsx`
- Sticky header with AgriSense sprout logo & brand tagline.
- Navigation links with smooth scroll / section jumping.
- Prominent Language Selector dropdown with Globe icon.
- High-contrast amber "Ask AI Agronomist" CTA button.
- Emergency Kisan Call Center helpline chip (`1800-180-1551`).

#### [NEW] `src/components/Hero.jsx`
- High-contrast, sunlight-readable banner with rich agricultural photography.
- Localized headline and dual CTAs: "Find Suitable Crops" and "Explore Seasonal Guide".
- **Local Weather & Soil Snapshot Widget**:
  - District switcher dropdown (Ludhiana, Jorhat, Nashik, Varanasi, Valencia).
  - Micro-climate cards: Temperature, Humidity, Rain forecast, Soil moisture.
  - Recharts 7-day mini trend line chart for rainfall and temperature.

#### [NEW] `src/components/CropRecommender.jsx` & `FarmingPlanModal.jsx`
- Interactive input panel:
  - Climate Zone selector
  - Soil Type selector (Alluvial, Clay, Loamy, Sandy, Red/Laterite)
  - Interactive sliders for Nitrogen (N: 0-140 kg/ha), Phosphorus (P: 0-100 kg/ha), Potassium (K: 0-120 kg/ha), and Soil pH (4.5 to 8.5)
  - Water Availability options (Rainfed, Canal/Well, Drip)
  - Current Season toggle (Kharif, Rabi, Zaid)
- Visual Recharts Radar Chart: Current Soil NPK vs Crop Ideal Requirements.
- Real-time Agronomic Matching Algorithm calculating suitability score ($\%$ match).
- Top 3-4 ranked crop cards with photo, suitability badge, yield estimate, water rating, and market demand.
- "View Farming Plan" modal with comprehensive 5-phase timeline, seed rate, fertilizer schedule, and pest alerts.

#### [NEW] `src/components/SeasonalCalendar.jsx`
- Filterable tabs: `All Seasons`, `Kharif (Monsoon)`, `Rabi (Winter)`, `Zaid (Summer)`.
- Month-by-month timeline cards highlighting critical agricultural operations.
- **"Print / Save Seasonal Checklist"**:
  - Generates a printer-optimized seasonal checklist with checkboxes, date fields, and safety guidelines.

#### [NEW] `src/components/CropDoctor.jsx`
- Crop selection dropdown (Rice, Wheat, Tomato, Potato, Tea, etc.).
- Affected plant part selector (Leaf, Stem, Fruit, Root).
- Multi-symptom selector + Visual Sample Gallery (preloaded high-res pest/disease leaf photos) + file upload simulator.
- Instant diagnosis output card: Disease name, confidence score, severity indicator, step-by-step Organic Remedy, Chemical Treatment, and Prevention methods.

#### [NEW] `src/components/AiAssistantDrawer.jsx`
- Slide-over chat drawer accessible via navbar CTA or floating action button.
- Localized quick prompt chips (*"Best fertilizer for paddy in clay soil?"*, *"How to protect crops from heavy monsoon rain?"*, *"Organic pest control for tomatoes"*).
- Simulated Voice Input feature with soundwave visualizer.
- Intelligent agricultural response generator providing structured advice in the selected language.

#### [NEW] `src/components/MandiAndSchemes.jsx`
- Searchable & filterable table of Mandi commodity rates with MSP comparison and price trend indicators.
- Government Schemes showcase cards with eligibility criteria, benefit amounts, and "Learn More" modal.

#### [NEW] `src/components/Footer.jsx`
- Kisan Call Center badge, emergency advisory notice, offline status pill, and navigation links.

---

### Phase 5: Verification & Production Readiness

#### [NEW] `server/index.js` (Optional Node.js API Service)
- Lightweight Express server providing clean REST endpoints (`/api/recommend-crops`, `/api/diagnose`, `/api/weather`, `/api/mandi-prices`) structured for real external API integration (IMD, Agmarknet, OpenWeather).

---

## Verification Plan

### Automated Verification
1. Verify Node.js and npm version after installation:
   ```powershell
   node -v
   npm -v
   ```
2. Build verification:
   ```powershell
   npm run build
   ```
   Ensures zero JSX syntax errors, zero TypeScript/lint issues, and clean asset bundling.

### Manual & In-Browser Verification (via Built-in Browser Preview)
1. **Server Launch**: Start Vite dev server on `http://localhost:5173`.
2. **Browser Preview Verification**:
   - Inspect top navbar, branding, and layout responsiveness.
   - Test **Language Switcher**: Toggle across English, Hindi, Assamese, and Spanish; verify that all headings, buttons, form labels, and advice cards dynamically update in real time.
   - Test **Crop Recommender**: Adjust soil sliders (N, P, K, pH), change climate & soil type, click "Get Crop Recommendations", inspect Recharts visualization, and open the "View Farming Plan" modal.
   - Test **Seasonal Calendar**: Filter seasons (Kharif, Rabi, Zaid) and test the "Print / Save Seasonal Checklist" trigger.
   - Test **Crop Doctor**: Select tomato/rice, choose symptoms or click a sample leaf photo, inspect the diagnosed disease, organic remedy, and chemical dosages.
   - Test **AI Agronomist Drawer**: Click floating widget and navbar CTA, test quick prompt chips, test voice input simulation, and verify structured localized responses.
   - Test **Mandi Rates & Schemes**: Filter commodity prices and open a Government Scheme modal.
   - Verify zero console errors, zero broken images, and smooth touch-friendly interactions.
