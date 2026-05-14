# Hack AI Thon - ESG Insight

Piattaforma web per analizzare documenti PDF ESG e verificare la conformita rispetto a normative e standard (es. CSRD, EU Taxonomy, ISO, GRI, DNF).

Il progetto e composto da:
- Backend Django + Django REST (`Hack_AI_Thon/`)
- Frontend React (`frontend/`)

## Cosa fa il progetto

Funzionalita principali:
- Upload di uno o piu file PDF.
- Estrazione testo dai documenti.
- Anonimizzazione in-memory dei dati sensibili (ETL privacy).
- Classificazione del tipo documento.
- Analisi conformita ESG con regole + recupero semantico (embedding + FAISS) + LLM locale.
- Generazione report PDF con risultati e azioni correttive.
- Anteprima trasparenza su cosa viene anonimizzato.

## Architettura (alto livello)

1. Frontend React invia i file al backend tramite API `/api/...`.
2. Backend estrae testo dai PDF (PyMuPDF / pdfplumber).
3. Backend anonimizza il contenuto (email, telefono, IBAN, CF, indirizzi, ecc.) in RAM.
4. Il testo viene analizzato contro un database interno di normative ESG.
5. Viene calcolato un riepilogo:
- norme rispettate
- norme non rispettate
- azioni correttive
6. Il frontend mostra dashboard e consente il download del report PDF finale.

Nota privacy: i file caricati non vengono persistiti su disco/database durante il processing API, ma elaborati in memoria.

## Struttura cartelle

- `Hack_AI_Thon/`
  - progetto Django (settings, urls, app `chatbot`, `manage.py`, `requirements.txt`)
- `frontend/`
  - app React (UI upload, dashboard risultati, anteprima anonimizzazione)
- `pdf_test/`
  - cartella utile per test locali con PDF di esempio

## Prerequisiti

- Python 3.10+ (consigliato 3.10 o 3.11)
- Node.js 18+ e npm
- Git (opzionale)
- Connessione internet al primo avvio per scaricare i modelli Hugging Face

## Setup rapido

### 1) Backend (Django)

Apri terminale nella root repository:

```powershell
cd Hack_AI_Thon
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Crea il file ambiente in `Hack_AI_Thon/Hack_AI_Thon/.env` con almeno:

```env
DJANGO=your-dev-secret-key
LOCAL_LLM_MODEL=meta-llama/Llama-3.2-3B-Instruct
LOCAL_LLM_FALLBACK_MODEL=TinyLlama/TinyLlama-1.1B-Chat-v1.0
LOCAL_LLM_MAX_NEW_TOKENS=512
```

Avvio server backend:

```powershell
python manage.py migrate
python manage.py runserver
```

Backend disponibile su `http://localhost:8000`.

### 2) Frontend (React)

In un secondo terminale:

```powershell
cd frontend
npm install
npm start
```

Frontend disponibile su `http://localhost:3000`.

Il frontend usa proxy verso backend (`frontend/package.json`), quindi le chiamate `/api/...` vengono inoltrate a `localhost:8000`.

## Come utilizzare l applicazione

1. Apri `http://localhost:3000`.
2. Trascina uno o piu PDF nella zona upload.
3. Verifica la sezione "Gestione Dati Anonimizzazione" per l anteprima privacy.
4. Clicca "Analizza Documenti".
5. Leggi score, norme conformi/non conformi e azioni correttive.
6. Scarica il report finale con "Scarica Report PDF".


