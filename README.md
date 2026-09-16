# Reiseliv i Akershus â€” dashbord

Streamlit-dashbord som viser overnattinger, hotellnÃ¸kkeltall og
befolkningstall for Akershus og de tre reiselivsregionene
(Asker/BÃ¦rum, Follo, Romerike/Hadeland), hentet direkte fra
SSBs Statistikkbank (PxWebApi v2).

## 1. KjÃ¸r lokalt

```bash
git clone <din-repo-url>
cd akershus-reiseliv-dashboard
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
streamlit run app.py
```

Appen Ã¥pnes pÃ¥ `http://localhost:8501`. FÃ¸rste gang du bytter filter kan
det ta noen sekunder mens data hentes fra SSB â€” deretter er svaret
cachet i 6 timer (se `CACHE_TTL_SECONDS` i `src/data_loader.py`).

## 2. Legg til pÃ¥ GitHub

```bash
git init
git add .
git commit -m "FÃ¸rste versjon av reiselivsdashbord for Akershus"
git branch -M main
git remote add origin <din-repo-url>
git push -u origin main
```

## 3. Publiser pÃ¥ Streamlit Community Cloud (gratis)

1. GÃ¥ til [share.streamlit.io](https://share.streamlit.io) og logg inn med GitHub.
2. Velg "New app" â†’ pek pÃ¥ repoet ditt â†’ `app.py` som hovedfil.
3. Deploy. Appen fÃ¥r en offentlig URL du kan dele.

Ingen hemmelige nÃ¸kler trengs â€” SSB sitt API er Ã¥pent og krever ikke
autentisering.

## 4. Prosjektstruktur

```
app.py                  Selve Streamlit-dashbordet (UI + filtre + grafer)
src/config.py            Geografi- og tabellkoder samlet ett sted
src/ssb_client.py         Generisk klient mot SSBs PxWebApi v2
src/data_loader.py        Henter + cacher hver av de 7 SSB-tabellene
data/manual/              Plass til manuelt innhentede tall (NHO Reiseliv m.m.)
```

## 5. Hva er dekket i denne versjonen

Se fanen **"â„¹ï¸ Om data og forbehold"** inne i selve appen for en full
gjennomgang. Kort oppsummert:

| Ã˜nsket data | Status |
|---|---|
| Overnattinger totalt, norsk/internasjonalt, hotell vs. hytte/camping | âœ… Dekket (tabell 14172) |
| NÃ¸kkeltall hotell (omsetning/gjest, losjiomsetning, kapasitetsutnyttelse) | âœ… Dekket (14176, 14177) â€” bekreft eksakt indikatornavn i dropdown |
| RevPAR (= losjiomsetning per tilgjengelig rom) | âœ… Beregnes i appen fra 14176. Vises kun nÃ¥r begge nÃ¸dvendige rader finnes for utvalget â€” ellers skjules panelet automatisk |
| Antall overnattinger korttidsutleie (Airbnb, Booking.com) | âž– **Bevisst utelatt** fra omfanget, hentes ikke inn |
| Verdiskaping per innbygger | âœ… Dekket â€” Innovasjon Norge-tall delt pÃ¥ SSBs befolkningstall |
| Verdiskaping per reiselivsbransje og kommune | âœ… Dekket â€” `data/manual/verdiskaping_akershus.csv` (fra Innovasjon Norges verdiskapingsrapport, Ã¥rlig t.o.m. 2024) |

## 6. Oppdatere verdiskapingstallene senere

Filen `data/manual/verdiskaping_akershus.csv` er et statisk uttrekk av
arket **"Aggregerte Data"** fra Innovasjon Norges Excel-rapport, filtrert
til Akershus' 21 kommuner. NÃ¥r du fÃ¥r en ny versjon av rapporten:

1. Ã…pne den nye Excel-filen og gÃ¥ til arket "Aggregerte Data".
2. Filtrer/eksporter radene der `Fylke == "Akershus"` til CSV med samme
   kolonner som i dag (`RegnskapsÃ¥r, Fylke, Kommune, Landsdel, NÃ¦ring,
   Sektor, Verdiskaping, Ansatte, Ã…rsverk, Foretak, Aktive Foretak`).
3. Erstatt `data/manual/verdiskaping_akershus.csv` og oppdater
   `VERDISKAPING_MAX_YEAR` i `src/config.py` til nyeste Ã¥rstall i filen.

## 7. Neste steg (forslag til v2)

1. Verifiser eksakte indikatornavn i tabell 14176/14177 ved Ã¥ kjÃ¸re appen
   og se hva som faktisk dukker opp i dropdown-menyene.
2. Eksporter til PDF/PowerPoint for rapportering, evt. legg til
   nedlastingsknapp for filtrert data (`st.download_button`).
3. Vurder kart-visualisering av verdiskaping/overnattinger per kommune
   (f.eks. med `plotly.express.choropleth` og SSBs kommune-geojson).
