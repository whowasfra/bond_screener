# 📈 Intelligent Bond Screener & Entry-Point Predictor
**Corso:** Intelligent Systems (Università di Pisa)
**Docenti:** P. Ducange, F. Ruffini
**Studente:** [Il tuo nome]

## 1. Executive Summary (L'Idea & Use Case)

> 
> Un sistema di supporto alle decisioni (Intelligent System) rivolto all'investitore *retail* con logica "Buy & Hold" (cassettista). Il sistema fonde l'analisi *cross-sectional* delle obbligazioni europee per trovare i rendimenti matematicamente migliori nel presente, con il **Time-Series Forecasting multivariato** per suggerire il *Timing* d'ingresso ottimale. 
> L'AI risponderà alla domanda: *"Conviene bloccare il capitale oggi su questo bond o i trend macroeconomici suggeriscono di attendere un imminente calo del prezzo per ottenere un rendimento maggiore a scadenza?"* 


## 2. Architettura dei Dati (Pipeline Modulare Avanzata)
Il sistema utilizza una pipeline di *Data Ingestion* modulare per la creazione di un Data Lake finanziario, unendo micro-struttura dei prezzi a macro-economia istituzionale.

### A. Micro-Dati (CSV Locali - *SimpleToolsForInvestors*)
* **Anagrafica Bond (Borsa Italiana):** Dettagli identificativi (ISIN, Scadenza, Descrizione).
* **Storico Prezzi:** Serie temporale a frequenza giornaliera (Corso Secco e Volumi).
* **Filtro Universo:** Selezione esclusiva di titoli *Plain Vanilla* governativi europei (esclusione di corporate e indicizzati all'inflazione) per garantire omogeneità.

### B. Macro-Dati (API Eterogenee)
1. **Libreria `pandasdmx` (Banca Centrale Europea):**
   * *Spot Rate Yield Curve* (Curva dei rendimenti risk-free dell'Area Euro).
2. **Libreria `eurostat` / `fredapi` (Dati Istituzionali & Economici):**
   * *Deposit Facility Rate* e *Main Refinancing Operations (MRO) rate* (BCE).
   * *HICP Headline* (Inflazione Totale) e *HICP Core* (Depurata da energia/alimentari).
   * *Ifo Business Climate Index* / *ZEW Economic Sentiment* / *ESI*.
3. **Libreria `yfinance` (Sentiment di Mercato & Rischio):**
   * *VSTOXX* (Volatilità implicita europea - Indice di Paura).
   * *Spread BTP-Bund 10Y* (Termometro del rischio sovrano periferico).

## 3. Pre-processing & Feature Engineering
* **Feature Intrinseche:** Text Mining via RegEx per estrazione cedola (*Coupon*), calcolo del `Days_to_Maturity` (DTM) e del *Yield to Maturity* storico approssimato (Variabile Target).
* **Allineamento Temporale (Risoluzione Frequenze Miste):**
   * Indice primario basato sui giorni lavorativi di borsa.
   * Tecnica di *Forward-Fill* (`ffill`) per "spalmare" i dati macro a bassa frequenza (mensili/trimestrali) sui giorni lavorativi.
* **Prevenzione del Lookahead Bias:** Shift temporale dei dati macroeconomici (es. HICP) per allinearli alla *data di pubblicazione ufficiale* e non al mese di riferimento, garantendo che il modello non "veda il futuro".
* **Creazione Lags:** Generazione di *Lagged Features* (finestre temporali di T-n giorni) per fornire la memoria storica ai modelli ML.

## 4. Machine Learning Tasks & Modelli
Il problema è formalizzato come **Multivariate Time-Series Forecasting (Directional Classification)**: prevedere se il trend del prezzo (o rendimento) sarà in salita o in discesa a una finestra futura (es. T+7 o T+15 giorni).

I modelli sfidanti selezionati (come da letteratura indicata dai docenti):
1. **Baseline ML:** *Random Forest* (addestrato sulle *Lagged Features* ed esogene).
2. **Deep Learning (Core del corso):** Rete Neurale **LSTM (Long Short-Term Memory)** in PyTorch.
3. **SOTA / Foundation Models:** Sperimentazione di modelli *LLM-inspired* o statistici avanzati per Time Series (es. **Chronos**, **TimesFM** o **Prophet**).

## 5. Validazione Statistica (Prevenzione Data Leakage)
* **Metodo Obbligatorio:** **Expanding Window Forward Walk Validation** (`TimeSeriesSplit`).
* Il training set si espande cronologicamente. Il modello viene addestrato sul passato e testato rigorosamente su "blocchi" di dati futuri unseen, rispettando la sequenzialità temporale per eliminare ogni Data Leakage.
* **Metriche di Classificazione:** F1-Score, Matrice di Confusione e **Curva ROC-AUC** (per gestire potenziali sbilanciamenti del trend di mercato).

## 6. Explainable AI (XAI)
Per superare il limite delle "Black Box" nel settore finanziario, il sistema integrerà **SHAP (SHapley Additive exPlanations)**. Il sistema renderà interpretabile la LSTM spiegando visivamente l'impatto dei regressori esogeni (es. *"Il segnale di 'Attesa' è guidato per il 40% da un picco del VSTOXX e per il 30% da un aumento dell'HICP Core"*).

## 7. Deliverables & Prototipo Finale
1. **Jupyter Notebook (KDD Pipeline):** Codice Python iper-commentato (Data Ingestion, EDA, Training, Validation).
2. **Paper IEEE (Overleaf):** Articolo accademico di 8-10 pagine con introduzione al dominio, background teorico, setup sperimentale e risultati/comparazione modelli.
3. **Continuous Inference (Proxmox):** Deploy del prototipo finale su server Proxmox in container LXC. Il sistema eseguirà le chiamate API modulari in autonomia ogni sera, per costruire un archivio proprietario e segnalare le inefficienze di mercato.