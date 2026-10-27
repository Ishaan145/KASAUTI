# Prior Art, Competitive Landscape, and Academic State of the Art: AI Detection of Financial Misinformation and Unregistered Investment Advice; Code-Mixed NLP; Conformal Prediction for NLP

Research current to 26 October 2026. Where a source predates 2026, that is noted.

---

## Q1. Existing products, apps, extensions, services that verify financial advice, detect investment scams, or check adviser credentials (global and India)

### Takeaway
Adviser-credential verification in India is now **occupied by the regulator itself** — SEBI shipped "SEBI Check" (live 1 October 2025) plus validated `@valid` UPI handles, and distributes it through its own Saa₹thi investor app. Payment-rail and identity verification is therefore crowded and officially owned. What is *not* occupied by any shipped product I could find is **content-level analysis of the advice claim itself** (is this specific Hinglish tip a regulated recommendation, and is it true?) — the existing tools answer "is this payee registered?", a different question.

### Cited Findings
- SEBI announced SEBI Check in a **11 June 2025 circular** alongside validated UPI handles; the tool **went live 1 October 2025** per a later SEBI circular — [Groww blog on SEBI Check](https://groww.in/blog/what-are-validated-upi-handles-and-sebi-check-tool)
- SEBI Check lets an investor verify (a) a bank account number + IFSC for NEFT/RTGS/IMPS, (b) an entity's `@valid` UPI ID, (c) a QR code via a "Photo" option. It is available on the **SEBI Check web portal and SEBI's Saa₹thi app** (iOS and Android) — [Groww](https://groww.in/blog/what-are-validated-upi-handles-and-sebi-check-tool)
- Validated UPI handles use a **`.brk` suffix for brokers and `.mf` for mutual funds**, followed by `@valid`; **NPCI issues them exclusively to SEBI-registered intermediaries**. A green triangle with a thumbs-up icon appears during payment; its absence indicates a likely unauthorised payee — [Groww](https://groww.in/blog/what-are-validated-upi-handles-and-sebi-check-tool)
- SEBI issued a circular directing investors to **verify registration before acting on investment tips** — [Business Standard](https://www.business-standard.com/finance/personal-finance/sebi-circular-on-frauds-verify-registration-before-taking-investment-tips-125042000636_1.html)
- SEBI has urged **social media platforms to verify advertisers and flag fake apps** — [Moneylife](https://moneylife.in/article/sebi-urges-social-media-platforms-to-verify-advertisers-and-flag-fake-apps/78775.html); [Medianama on the SEBI advisory](https://www.medianama.com/2025/11/223-sebi-advisory-social-media-verification/)
- Zerodha's public posture is **investor-education content, not tooling**: Z-Connect posts on "The nuisance of fake trading apps," "Beware of investment scams in the name of Zerodha," and a "Simple guide to stay safe from scams" — [Zerodha Z-Connect](https://zerodha.com/z-connect/general/the-nuisance-of-fake-trading-apps); [Zerodha scam guide](https://zerodha.com/z-connect/updates/simple-guide-to-stay-safe-from-scams)
- Nithin Kamath (Zerodha) framed SEBI's UPI security feature as a response to **"scammers impersonating brokers"** — [Business Today](https://www.businesstoday.in/amp/markets/stocks/story/scammers-impersonating-brokers-what-zerodhas-nithin-kamath-says-on-sebis-new-upi-security-feature-480547-2025-06-16)
- Groww's role is **explaining** SEBI Check to its users, not operating its own verification engine — [Groww](https://groww.in/blog/what-are-validated-upi-handles-and-sebi-check-tool)
- Adjacent government rails exist for fraud *reporting* rather than advice verification: the DoT **Chakshu** portal (report fraudulent calls/SMS/WhatsApp) and **Sanchar Saathi**, which publishes an investment-scam awareness document — [Moneylife on Chakshu](https://moneylife.in/article/govt-launches-chakshu-portal-to-fight-frauds-through-spam-calls-messages/73597.html); [Factly explainer on Chakshu](https://factly.in/explainer-government-launches-chakshu-to-report-sms-call-or-whatsapp-messages-attempting-to-commit-fraud); [Sanchar Saathi investment scam PDF](https://sancharsaathi.gov.in/SancharSaathiDocuments/KeepYourselfAwareDocuments/InvestmentScam.pdf)

### Inferences
- The "is this adviser registered?" lookup is **a solved and officially owned problem** as of Oct 2025. A new project that pitches credential lookup as its core novelty will be read by judges as reinventing SEBI Check. It should instead *consume* the registry as one signal.
- SEBI Check's design reveals the gap: it validates **payment destinations**, which only helps at the moment of transfer. It does nothing at the moment of *persuasion* — when a user reads a Telegram tip. That earlier point in the funnel is unoccupied by shipped product.
- Brokers appear to have deliberately *not* built content-analysis features, likely because classifying third-party speech as "unregistered advice" carries liability a broker would rather not own. That is a real reason the white space exists, and it is also a reason a project must be careful to frame output as *flag/triage*, not adjudication.

### Gaps
- I could not confirm whether Upstox or Angel One ship any scam-check or adviser-verification feature of their own; searches surfaced only SEBI-level tooling and third-party broker reviews. Treat "no broker-built content tool exists" as *unconfirmed* rather than established.
- No usage or efficacy statistics for SEBI Check or the Saa₹thi app (downloads, lookups performed, frauds averted) were found.
- I found no browser extension or Product Hunt-listed consumer product specifically for verifying financial advice. Absence of search evidence is weak evidence of absence here; this was not exhaustively canvassed.

---

## Q2. Fact-checking organisations in India working on financial misinformation, and their automated tooling

### Takeaway
Indian fact-checking organisations (BOOM, Factly) are active and have published research on **AI-driven misinformation**, but I found **no evidence of automated financial-misinformation tooling** from them. Their work is manual, editorial, and general-purpose rather than finance-specific.

### Cited Findings
- BOOM published a research report on **AI, misinformation and disinformation in the 2024 Lok Sabha elections** — [BOOM Live research](https://www.boomlive.in/boom-research/artificial-intelligence-misinformation-disinformation-2024-lok-sabha-elections-boom-report-27412)
- Factly produces explainer-format fact-checks including government-scheme and fraud-reporting explainers — [Factly Chakshu explainer](https://factly.in/explainer-government-launches-chakshu-to-report-sms-call-or-whatsapp-messages-attempting-to-commit-fraud)
- Indian fact-checking platforms have grown as a sector — [Entrepreneur India](https://india.entrepreneur.com/news-and-trends/the-rise-of-fact-checking-platforms/455053)

### Inferences
- A finance-specific, automated Indian fact-checking capability appears genuinely absent. This is a defensible white-space claim, but it should be stated as "we found no shipped automated tool," not "none exists."
- The practical implication for a project: there is **no Indian finance fact-check corpus to train or evaluate against**, so a new project must hand-build its gold set — which in turn makes the calibration-set-size question (Q7) load-bearing rather than academic.

### Gaps
- I did not verify whether BOOM or Factly operate internal claim-matching or tipline-triage automation (several global fact-checkers do, via Meedan's Check platform). Not confirmed either way.
- Alt News was returned by no search result; I have nothing citable on their financial-misinformation work.

---

## Q3. Open-source projects / GitHub repos on financial misinformation, finfluencer analysis, or pump-and-dump detection

### Takeaway
The open-source layer is **thin and amateur** for finance-misinformation specifically, but there is a visible cluster of **Hinglish scam-text datasets on Hugging Face** — several of which look like student or auto-generated artifacts rather than curated benchmarks. Crypto pump-and-dump detection is the one sub-area with real published code and method maturity.

### Cited Findings
- `gupta7050/multilingual-phishing-detection` — ML detection of spam/phishing/fraud messages in **English, Hindi, and Hinglish using TF-IDF** — [GitHub](https://github.com/gupta7050/multilingual-phishing-detection)
- Multiple near-duplicate Hugging Face datasets named `hinglish-scam-text-dataset` exist under different user accounts (`Yash1072006`, `nini654`) — [HF Yash1072006](https://huggingface.co/datasets/Yash1072006/hinglish-scam-text-dataset); [HF nini654](https://huggingface.co/datasets/nini654/hinglish-scam-text-dataset)
- `Indian_Cyber_Scam_PhoneCall_Hinglish_Dataset` likewise appears duplicated across accounts (`DatasetNewUser`, `Nithingayakwad`) — [HF DatasetNewUser](https://huggingface.co/datasets/DatasetNewUser/Indian_Cyber_Scam_PhoneCall_Hinglish_Dataset)
- `anmolshrivastav/scam_ham_india_14_languages` — scam/ham classification across 14 Indian languages — [Hugging Face](https://huggingface.co/datasets/anmolshrivastav/scam_ham_india_14_languages)
- `karanverma19/trilingual_fraud_consumer_protection_v2` — trilingual fraud / consumer-protection dataset — [Hugging Face](https://huggingface.co/datasets/karanverma19/trilingual_fraud_consumer_protection_v2)
- A study evaluated **code metrics across GitHub repositories related to fake news and misinformation**, indicating the repo population is large enough to be studied as a corpus — [arXiv 2304.13769](https://arxiv.org/pdf/2304.13769)

### Inferences
- The duplicate-across-accounts pattern in the Hinglish scam datasets is a strong signal these are **forks/copies of a small number of originals**, plausibly LLM-generated. A project should not cite them as evidence of a mature benchmark, and should not train on them without inspecting provenance. If a judging panel checks, citing these as prior art *strengthens* credibility; citing them as a data source would weaken it.
- Scam-text classification (is this SMS a scam?) is well-covered open-source ground. **Advice-legitimacy classification** (is this a regulated recommendation from an unregistered party?) is not, and is a harder, more defensible framing.

### Gaps
- I did not locate a named open-source project for **finfluencer analysis** specifically (e.g. scoring influencer track records against realised returns). Searches returned academic work instead of repos.
- No GitHub repo for SEBI-registry-linked verification was found.

---

## Q4. Prior hackathon projects; are "misinformation detector" entries common; what distinguishes winners

### Takeaway
Misinformation detectors are **among the most common hackathon entries in existence** — Devpost returns many, and the name "TruthLens" alone is reused across at least three independent projects. India has run a 5,600-registration government-backed misinformation hackathon (TruthTell, under WAVES/ICEA). A generic "AI misinformation detector" pitch is therefore maximally crowded.

### Cited Findings
- **TruthTell Hackathon** ran 1 October 2024 – 21 February 2025, online, team size 1–5, with **5.6K registrations**, as part of **Create in India Challenge Season 1 under WAVES, hosted by ICEA**. Its stated problem statement is a "call to arms for tech enthusiasts to develop groundbreaking AI-powered tools that can reshape the media landscape," themed on unmasking misinformation — [HackerEarth TruthTell](https://hackerearth.com/challenges/hackathon/truth-tell-hackathon/custom-tab/evaluation-parameter)
- ICEA announced the **Top 5 TruthTell winners** in connection with the WAVES Summit 2025 — [CXO Digital Pulse](https://www.cxodigitalpulse.com/?p=29321)
- The project name "TruthLens" appears on **at least three distinct, unrelated submissions**: two separate Devpost entries and a HackQuest/Bittensor ideathon entry — [Devpost TruthLens 1](https://devpost.com/software/truthlens-he28xm); [Devpost TruthLens 2](https://devpost.com/software/truthlens-38ue9c); [HackQuest TruthLens](https://www.hackquest.io/ko/projects/TruthLens-CzwqiS)
- Further generic Devpost fact-check entries include `factcheqr`, `candour`, and `team29_incredible-ai` — [Devpost factcheqr](https://devpost.com/software/factcheqr); [Devpost candour](https://devpost.com/software/candour)

### Inferences
- "Is this novel?" will be answered **no** for any pitch framed as misinformation detection in general. Novelty has to live in a narrower, harder claim. Based on the rest of this research, the three candidate differentiators that are *not* crowded are: (i) **unregistered-advice classification as a regulatory-status task, not a truth task**; (ii) **Hinglish/code-mixed as the primary rather than incidental input**; (iii) **calibrated abstention with a stated coverage guarantee** instead of an uncalibrated confidence score.
- Of those three, (iii) is the one most hackathon entries structurally cannot claim, because it requires a held-out labelled calibration set and a reported empirical-coverage number. That is cheap to do and rare to see — it is the highest-leverage technical-depth signal available here.

### Gaps
- **The TruthTell evaluation-criteria page rendered empty** — the "Themes," "Evaluation Parameter," and "Prizes" headers appear with no content. I therefore have **no citable judging weights** for that hackathon, and cannot substantiate what distinguished its winners. Any claim about winner-distinguishing criteria below is my inference, not sourced.
- I found no systematic study of what separates hackathon winners from entrants. The "what distinguishes winners" question is answered only inferentially in this note.

---

## Q5. Published research on detecting financial misinformation, pump-and-dump, and unlicensed financial advice

### Takeaway
Financial misinformation detection is **a crowded, benchmarked, actively-competed research area** with a dedicated ACL-workshop shared task and purpose-built LLMs (FMDLlama, MFMDQwen). Be honest with the panel: this is well-trodden ground. Pump-and-dump detection is similarly mature. By contrast, **automated detection of *unlicensed advice* as a regulatory-status classification task is essentially unstudied** — regulators themselves are doing it with web scrapers and manual review, not ML.

### Cited Findings — financial misinformation
- **FMDLlama** is a purpose-built open LLM for financial misinformation detection — [arXiv 2409.16452](https://arxiv.org/pdf/2409.16452)
- The **FinNLP-FNP-LLMFinLegal 2025 Financial Misinformation Detection shared task** used data derived from the FinFact portion of FMDID: **1,952 train / 1,304 test** claims labelled **True / False / Not Enough Information (NEI)**, spanning Income, Finance, Economy, Budget, Taxes, Debt. Models must predict the label *and* generate an explanation; scored by **Micro-F1** for detection and **ROUGE-1/2/L** for explanation, with final rank = average of Micro-F1 and ROUGE-1 — [FinNLP 2025 shared task overview](https://aclanthology.org/2025.finnlp-1.30.pdf)
- **32 teams registered, 8 submitted papers**; test split 40% public / 60% private with ranking on private. Baselines: **FMDLlama 0.5842 overall (Micro-F1 0.7182)**; **GPT-3.5-turbo 0.4813 overall (Micro-F1 0.7012)**. Winner **Dunamu ML: 0.8294 overall, Micro-F1 0.8467, ROUGE-1 0.8121** — [FinNLP 2025](https://aclanthology.org/2025.finnlp-1.30.pdf)
- Winning method detail: Dunamu ML augmented from the MOCHEG dataset, generated evidence with GPT-4-0613, selected few-shot examples by **embedding similarity via FAISS**, and fine-tuned **Llama-3.1-8B**. Other teams used LoRA on Llama-3.2-11B-Vision, GPT-4o-mini instruction tuning, and SerpAPI retrieval with a "Financial Chain-of-Thought" prompt — [FinNLP 2025](https://aclanthology.org/2025.finnlp-1.30.pdf)
- **MFMDQwen** (multilingual FMD, Qwen3-8B + SFT on a new MFMD4Instruction set) covers **English, Chinese, Bengali, Greek** and introduces a 9-task **MFMDBench**. Average F1 **0.818**, top score on 6 of 9 datasets; strongest baseline Qwen3-32B-R averages **0.674**, FMDLlama averages **0.293**. Per-dataset: BanMANI (Bengali) 0.952, RFC 0.954, MDFEND 0.913, CHEF 0.843, GlobalBe 0.803, GlobalGr 0.758, GlobalEn 0.756, GlobalCh 0.710, FinDVer 0.673 — [arXiv 2604.18272](https://arxiv.org/pdf/2604.18272)
- **MFMDQwen explicitly does not cover Hindi or code-mixed Indian languages** — [arXiv 2604.18272](https://arxiv.org/pdf/2604.18272)
- **Fin-Fact** is a benchmark dataset for multimodal financial fact-checking and explanation generation — [ResearchGate](https://www.researchgate.net/publication/392054214_Fin-Fact_A_Benchmark_Dataset_for_Multimodal_Financial_Fact-Checking_and_Explanation_Generation)
- A systematic review of fake financial news covers trends, themes, LLM-based detection methods and theories — [ResearchGate](https://www.researchgate.net/publication/393232093_Investigating_fake_financial_news_a_systematic_review_of_trends_themes_LLM-based_detection_methods_and_theories)

### Cited Findings — pump-and-dump
- "Machine Learning-Based Detection of Pump-and-Dump Schemes in Real-Time" — [arXiv 2412.18848](https://arxiv.org/abs/2412.18848)
- **PumpSense**: real-time detection and target extraction of crypto pump-and-dumps **on Telegram** — [arXiv 2605.09431](https://arxiv.org/html/2605.09431v1); also presented as "Real-Time Machine Learning Detection of Telegram-Based Pump-and-Dump Schemes" — [ACM DL 10.1145/3733815.3764042](https://dl.acm.org/doi/10.1145/3733815.3764042)
- "Detecting cryptocurrency pump-and-dump frauds using **market and social signals**" — [Expert Systems with Applications, S0957417421007156](https://www.sciencedirect.com/science/article/abs/pii/S0957417421007156)
- "Pump and Dump Cryptocurrency Detection Using Social Media" — [SciTePress](https://www.scitepress.org/Papers/2023/120593/120593.pdf)

### Cited Findings — unlicensed advice / finfluencers (regulatory, not ML)
- **IOSCO Final Report FR/08/2025 (May 2025)** says little about AI or ML. **Belgium's FSMA** built an in-house **web scraping tool** to detect social-media crypto ads; it flags ads *without* separating compliant from non-compliant, so **staff must manually filter results**. **Spain's CNMV** reviewed a *sample* of finfluencer activity in 2022 for recommendations bypassing Market Abuse Regulation. **Canada's CSA** plans electronic-media analysis with no named tool. IOSCO *recommends* data analytics and social-media surveillance as good practice — a recommendation, not described current practice. Generative AI appears **only as a risk** (fake endorsements) — [IOSCO FR/08/2025](https://www.iosco.org/library/pubdocs/pdf/IOSCOPD795.pdf)
- Scale, from the same report: **30 of 34** surveyed regulators say finfluencers raise investor risk; **14** name unsuitable advice from unauthorised finfluencers as their top concern; **15 (44%)** have taken enforcement action against finfluencers; ~**60%** observed intermediary–finfluencer contracts, some intermediaries working with **40–60 influencers**. A **FINRA** review found ~**70% of over a thousand** social-media communications non-compliant in some way. **ASIC**: **28%** of Australian 18–21-year-olds follow ≥1 finfluencer, and **64% of those** changed at least one financial behaviour. One study classified **56% of finfluencers as anti-skilled, 16% unskilled, 28% skilled** — [IOSCO FR/08/2025](https://www.iosco.org/library/pubdocs/pdf/IOSCOPD795.pdf)
- CFA Institute report on finfluencer appeal and investing in the age of social media — [CFA Institute](https://rpc.cfainstitute.org/sites/default/files/-/media/documents/article/industry-research/finfluencer-report.pdf)
- SEC Investor Advisory Committee recommendation on finfluencers (22 November 2024) — [SEC](https://www.sec.gov/files/sec-iac-finfluencer-recommendation-11222024.pdf)
- NASAA comment letter on FINRA's Social Media-Influenced Investing report (13 May 2026) — [NASAA](https://www.nasaa.org/wp-content/uploads/2026/05/NASAA-Comment-Letter-re-FINRA-Social-Media-Influenced-Investing-Report-05-13-2026.pdf)
- Academic work on finfluencer regulation: "Viral but Vanishing: Investment Advisors, Social Media, and Regulation" — [SBFC 2025](https://sbfc.sydney.edu.au/2025/papers/SBFC2025_6E2_P093.pdf); Ontario Securities Commission on finfluencers — [OSC](https://www.osc.ca/en/investors/investor-research-and-reports/social-media-and-retail-investing-rise-finfluencers)
- SEBI's own finfluencer enforcement is documented, including barring a named finfluencer — [Business Standard on Sanjiv Bhasin](https://www.business-standard.com/markets/news/sanjiv-bhasin-returns-to-markets-who-is-he-and-why-was-he-barred-125082200700_1.html); [Mondaq on SEBI's crackdown](https://webiis08.mondaq.com/india/securities/1726258/sebis-crackdown-on-finfluencers-regulations-and-enforcement); [Outlook Money](https://www.outlookmoney.com/news/sebis-crackdown-on-unregistered-finfluencers-key-measures-by-the-regulator); [The Finance Story on SEBI's new rules](https://thefinancestory.com/sebi-new-rules-to-curb-unregistered-finfluencers)

### Inferences
- **State this crowding plainly to the panel.** Financial misinformation detection has a shared task, a leaderboard, baselines, and dedicated models. Claiming novelty on "detecting false financial claims" is not credible in October 2026.
- The *honest* novelty seam is visible in the IOSCO evidence: the world's regulators are detecting unlicensed advice with **keyword web scrapers plus human filtering**, and IOSCO lists ML surveillance as an aspiration. The published ML literature is about **truth** (is the claim false?), while the regulatory need is about **status and act-type** (is this utterance a recommendation, and is the speaker registered?). Those are different labels. Nothing I found treats the second as an ML classification task with a benchmark.
- MFMDQwen's language list (English, Chinese, Bengali, Greek — no Hindi, no code-mixed) is a **directly citable gap** for an India-focused project. It is the single cleanest "here is the hole" citation in this whole note.
- FinNLP 2025's winning Micro-F1 of **0.8467 on a 3-way True/False/NEI task with 1,952 training examples** is a useful realism anchor: a strong, well-resourced team with GPT-4 evidence generation got ~85%. Any hackathon project claiming higher on a harder multilingual task should expect that number to be challenged.
- The presence of an **NEI ("Not Enough Information") class** in the established benchmark is important: the field already accepts that "cannot determine" is a legitimate output. That makes a conformal-abstention design *continuous with* the literature rather than exotic, which is a good positioning argument.

### Gaps
- I found **no paper** that frames unregistered/unlicensed investment advice detection as a supervised NLP task with a public dataset. I searched for it directly and got regulatory reports instead. This is a genuine gap in the literature, but I cannot prove no such paper exists — only that it did not surface.
- No Indian-language or Hinglish financial misinformation dataset with credible provenance was found (see Q3 on the Hugging Face duplicates).

---

## Q6. State of the art for Hinglish / code-mixed Hindi-English NLP

### Takeaway
Code-mixed NLP has established benchmarks (GLUECoS, LinCE) and established Indic models (MuRIL, IndicBERT), but **absolute performance on the hard code-mixed tasks remains low** — mBERT's GLUECoS EN-HI sentiment is **57.51** and NLI **57.74**, barely above chance for the task shapes. Code-mixed-specific pretraining (HingBERT family) beats general Indic models. Expect roughly **75–80 F1** on code-mixed NER, not 90+.

### Cited Findings
- **GLUECoS** covers **six tasks across eleven datasets**: Language Identification (LID), POS, NER, Sentiment Analysis (SA), QA, and NLI, for **English-Hindi (EN-HI) and English-Spanish (EN-ES)** — [GLUECoS leaderboard](https://microsoft.github.io/GLUECoS/)
- mBERT GLUECoS scores (Microsoft Research, 18 July 2020), **overall average 72.80**: LID EN-ES 96.44 / EN-HI 95.18; POS EN-ES 93.98, EN-HI FG 64.14, EN-HI UD 87.68; NER EN-ES 60.66 / EN-HI 76.95; SA EN-ES 63.02 / EN-HI 57.51; QA EN-HI 62.23; NLI EN-HI 57.74 — [GLUECoS](https://microsoft.github.io/GLUECoS/). *Caveat: the fetched page labelled only the POS table unambiguously; NER/SA/QA/NLI assignments were inferred from table order. Treat those four as approximate.*
- The GLUECoS leaderboard as fetched **lists only mBERT** — no MuRIL or XLM-R entry and no separate baseline table — [GLUECoS](https://microsoft.github.io/GLUECoS/)
- Code-mixed NER test F1 (2025 comparative study): **HingBERT 79.74, HingMBERT 78.87, HingRoBERTa 78.39, MuRIL 77.50, IndicBERT 76.47, RoBERTa 76.24, BERT Base Cased 72.12, Google Gemini (zero-shot) 62.24** — [arXiv 2509.02514](https://arxiv.org/html/2509.02514v1)
- The best fine-tuned encoder beats zero-shot Gemini by **~15 F1 points**; the paper attributes Gemini's weakness to low precision, favouring frequent classes, and difficulty with fine-grained entity types — [arXiv 2509.02514](https://arxiv.org/html/2509.02514v1)
- What makes code-mixed hard, per the same paper: code-mixed data "often includes **transliteration, inconsistent grammar, and domain-specific vocabulary**"; artifacts requiring normalisation include "**spelling variations, inconsistent casing, and encoding differences**"; **subword tokenisation** forces label alignment so only the first subword carries the entity tag; and there is a "**lack of annotated training data**." Its own dataset is **3,637 sentences**, dominated by "Other" tags with only **31 I-Loc tokens** — [arXiv 2509.02514](https://arxiv.org/html/2509.02514v1)
- **MuRIL** has been applied to Hinglish cyberbullying detection — [arXiv 2506.16066](https://arxiv.org/pdf/2506.16066)
- Work on whether pretraining transfers to code-mixed data: "Transfer Learning for Code-Mixed Data: Do Pretraining Languages Matter?" — [WASSA 2023](https://aclanthology.org/2023.wassa-1.32.pdf)
- **AI4Bharat IndicBERT v2 / IndicXTREME**: "Towards Leaving No Indic Language Behind: Building Monolingual Corpora, Benchmark and Models for Indic Languages" (ACL 2023) — [ACL Anthology](https://aclanthology.org/2023.acl-long.693.pdf); [arXiv 2212.05409](https://arxiv.org/pdf/2212.05409); model card — [HF ai4bharat/IndicBERTv2-MLM-only](https://huggingface.co/ai4bharat/IndicBERTv2-MLM-only)
- Comparative analysis of embedding models for Hindi-English code-mixed queries — [ResearchGate](https://www.researchgate.net/publication/391894273_Comparative_Analysis_of_Embedding_Models_for_Hindi-English_Code-Mixed_University_related_queries)

### Inferences
- The HingBERT-family > MuRIL > IndicBERT ordering on code-mixed NER is a concrete, citable argument that **code-mixed-specific pretraining matters more than Indic-general pretraining** for romanised Hinglish. A project that reaches for MuRIL by default is leaving ~2 F1 on the table and, more importantly, is not demonstrating awareness of the distinction.
- The zero-shot-LLM result (Gemini 62.24 vs 79.74) is the most useful single number for an LLM-centric project: **prompting a frontier model is substantially worse than fine-tuning a small code-mixed encoder** on a structured code-mixed task. If the project's pipeline is "send Hinglish text to an LLM," this is the predictable criticism, and a small fine-tuned encoder as a first-stage filter is the predictable answer.
- GLUECoS EN-HI sentiment at 57.51 and NLI at 57.74 set realistic expectations: **semantic tasks over Hinglish are genuinely unsolved**. A project reporting 90%+ on a Hinglish semantic classification task should be assumed to have a leaky or trivially-separable test set. This is worth pre-empting honestly.
- Because the public code-mixed datasets are small (3,637 sentences in the NER study), small hand-labelled sets are **normal in this subfield**, not a weakness — provided the uncertainty is quantified rather than hidden.

### Gaps
- The GLUECoS leaderboard did not surface current SOTA. mBERT at 72.80 from July 2020 is almost certainly **not** the 2026 state of the art, and I could not find updated leaderboard numbers. Do not present 72.80 as current SOTA.
- **LinCE** benchmark details and numbers were not retrieved; I have no citable LinCE figures.
- No IndicXTREME or IndicBERT v2 per-task scores were retrieved, only the paper and model card references. No IndicBART or IndicLID numbers obtained.
- No typical performance level for Hinglish *financial* text specifically — no such benchmark appears to exist.

---

## Q7. LLMs for claim extraction and claim-level verification; standard evaluation methods

### Takeaway
Claim decomposition into atomic claims is a well-developed sub-field (FActScore lineage, VeriScore, VeriFastScore), and critically, **recent work finds decomposition does not reliably help** — "Decomposition Dilemmas" asks directly whether it boosts or burdens fact-checking. Treat decomposition as a design choice requiring justification, not a free win.

### Cited Findings
- **"Decomposition Dilemmas: Does Claim Decomposition Boost or Burden Fact-Checking Performance?"** — the title itself frames decomposition's benefit as contested — [arXiv 2411.02400](https://arxiv.org/html/2411.02400v2)
- **VeriScore**: evaluating the factuality of long-form text generation — [arXiv 2406.19276](https://arxiv.org/html/2406.19276v1)
- **VeriFastScore**: speeding up long-form factuality evaluation — [arXiv 2505.16973](https://arxiv.org/html/2505.16973v3)
- "Distill and Align Decomposition for Enhanced Claim Verification" — [alphaXiv 2602.21857](https://www.alphaxiv.org/abs/2602.21857.md)
- The FinNLP 2025 FMD shared task scores explanation quality with **ROUGE-1/2/L** and detection with **Micro-F1**, ranking on their average — i.e. in the financial-claim setting the de facto evaluation is classification F1 plus ROUGE against a gold justification — [FinNLP 2025](https://aclanthology.org/2025.finnlp-1.30.pdf)
- The FinNLP overview notes **several teams scored well on classification but less well on explanations** — [FinNLP 2025](https://aclanthology.org/2025.finnlp-1.30.pdf)

### Inferences
- The FinNLP observation that classification outran explanation is a useful warning: **generating a defensible justification is harder than getting the label right**, and in a scam-warning product the justification is the part the user acts on. A project that reports only label accuracy is reporting the easy half.
- ROUGE against a single gold justification is a weak metric for explanation quality. A project with a small gold set is better served by a short human-rated rubric than by ROUGE on 30 examples — but per the user's own stated ordering discipline, that rubric must be written *before* any scoring.
- Decomposition is a natural fit for the advice-detection framing specifically, because a single Hinglish tip often bundles a *factual* claim ("this stock gave 300% last year") with a *recommendation* act ("buy now") with a *guarantee* ("assured returns"). Those three need different verification paths — the first is checkable, the second is a regulatory-status question, the third is a per-se red flag. That is a substantive reason to decompose, independent of whether decomposition helps generic fact-checking.

### Gaps
- **I did not retrieve the FActScore paper itself**, nor the SAFE paper. Both were named in the assignment; my searches returned the downstream VeriScore/VeriFastScore/Decomposition-Dilemmas cluster instead. FActScore's and SAFE's specific methods and metrics are **not sourced here** and should not be described from memory by the report writer.
- I did not retrieve the actual findings of Decomposition Dilemmas — only its framing question. Its conclusion is **unsourced**; do not assert which way it lands.
- No standard, widely-adopted metric for *claim extraction* quality (as opposed to end-to-end factuality) was identified.

---

## Q8. Conformal prediction for NLP classification and LLM outputs

### Takeaway
Conformal prediction for NLP is a mature, surveyed area with a TACL survey cataloguing ~20 prior applications. The standard reported metrics are **empirical coverage, average prediction set size, and observed fuzziness**, plus stratified coverage (FSC/SSC) for adaptivity. The main theoretical hazard for an LLM pipeline is **exchangeability violation**.

### Cited Findings
- **"Conformal Prediction for Natural Language Processing: A Survey"**, TACL 2024 — [ACL Anthology 2024.tacl-1.82](https://aclanthology.org/2024.tacl-1.82.pdf); [arXiv 2405.01976](https://arxiv.org/abs/2405.01976)
- Prior CP-for-text-classification work catalogued by the survey: **Maltoudoglou et al. (2020)** BERT conformal predictor for binary sentiment, where forced prediction preserved base accuracy while adding credibility/confidence; **Messoudi et al. (2020)** density-based CP for sentiment, where noisy examples got both labels and outliers got **empty sets**; **Giovannotti (2022)** Venn-Abers calibration of transformers on paraphrase/sentiment/Boolean QA; **Giovannotti & Gammerman (2021)** split vs Mondrian vs cross-conformal on unbalanced paraphrase detection, finding **small efficiency loss from Mondrian**; **Norinder & Norinder (2022)** Mondrian CP for unbalanced sentiment; **Dey et al. (2022)** CP over BERT softmax for POS, where **at 99% confidence fewer than 4% of sets had more than one label**; **Paisios et al. (2019)** and **Maltoudoglou et al. (2022)** multi-label news categorisation; **Fisch et al. (2022)** multi-label NER with false-positive limits; **Fisch et al. (2021a)** few-shot relation classification with **average set size below 2 at 95% confidence**; **Choubey et al. (2022)** CP to prune candidate labels for zero-shot classification, cutting inference cost; **Zhan et al. (2022, 2023)** literature filtering and noisy-label cleaning — all per [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- CP for LLMs, per the survey: **Ye et al. (2024)** convert tasks to multiple choice and conformalise softmax, finding **accuracy and certainty are sometimes inversely correlated**, and propose an uncertainty-aware metric; **Kumar et al. (2023)** CP for LLM multiple-choice QA; **Ravfogel et al. (2023)** conformal nucleus sampling, reporting **LLMs are overconfident and scaling does not fix it**; **Ulmer et al. (2024)** non-exchangeable conformal generation with nearest-neighbour weights; **Quach et al. (2024)** conformal language modelling calibrating when to stop sampling; **Yadkori et al. (2024)** conformal abstention bounding hallucination risk; **Angelopoulos et al. (2024)** and **Farinhas et al. (2024)** conformal risk control for open-domain QA; **Ren et al. (2023)** calibrated robot planners that ask for help; **Zollo et al. (2023)** prompt risk control bounding worst-case toxicity — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- The survey notes **ECE-targeting calibration methods are sensitive to binning and lack formal risk guarantees**, so conformal methods complement them — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- Standard metrics per the survey: **validity/coverage** (should be ≥ 1−α; most common form called "conservative coverage validity"); **average set size** (a priori efficiency, smaller is better); **observed fuzziness** (a posteriori — average p-values assigned to *incorrect* labels, lower is better); **credibility and confidence** pointwise measures under forced prediction; **conditional/group coverage** (class-conditional or partition-level, to check adaptivity and bias); **risk-control bounds** (expected loss ≤ β); task-specific measures including fraction of multi-label sets, uncertainty-aware accuracy, and coverage-and-width — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- From the Gentle Introduction, additional standard reporting: per-trial empirical coverage **C_j = (1/n_val) Σ 1{Y_i ∈ C(X_i)}**, with mean ≈ 1−α; **coverage histograms** centred on 1−α; **set-size histograms** (large average size indicates a weak score or model, spread shows adaptivity); **FSC (feature-stratified coverage)** = minimum observed coverage across groups defined by a discrete feature; **SSC (size-stratified coverage)** = minimum observed coverage across set-size bins — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- Classification score functions, exact forms. **Basic/softmax (LAC):** s(x,y) = 1 − f̂(x)_y; q̂ = the ⌈(n+1)(1−α)⌉/n quantile of calibration scores; C(x) = {y : f̂(x)_y ≥ 1 − q̂}. **Adaptive Prediction Sets (APS):** s(x,y) = Σ_{j=1}^{k} f̂(x)_{π_j(x)} where y = π_k(x) and π sorts classes most- to least-likely (cumulative softmax mass up to and including the true label); C(x) = {π_1(x),…,π_k(x)} with k = sup{k′ ≥ 0 : Σ_{j=1}^{k′} f̂(x)_{π_j(x)} < q̂} + 1, where the +1 prevents zero-size sets. The basic method "tends to undercover hard subgroups and overcover easy ones"; APS undercovers hard subgroups less — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- Challenges per the survey: **exchangeability violation** — autoregressive generation is conditional and breaks the standard assumption; Barber et al. (2023) give a bound requiring a hard-to-estimate total-variation distance, and weight choice remains open. **Distribution shift** methods exist but it is unclear how to apply them to NLP. **Large output spaces** in sentence-level generation, where the effect of sampling a finite output set is open. **Calibration set size** — the survey cites **~1,000 samples as reasonable for most purposes**, and notes **Dutta et al. (2023)** build a calibration set from web data when labelled data is scarce. **Conditional coverage** cannot be exactly guaranteed in general. **Bias/imbalance** — Zerva & Martins (2023) found **uneven coverage across languages and quality levels**, addressable with equalized conformal prediction (Romano et al., 2020). **Computation** — full CP requires retraining per label — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- Cost-sensitive conformal prediction with human-in-the-loop abstention for imbalanced high-stakes decision support, multi-domain benchmark — [arXiv 2607.27143](https://arxiv.org/html/2607.27143)
- "Do Large Language Models Know When Not to Answer...?" (UncertaiNLP 2025) — [ACL Anthology](https://aclanthology.org/2025.uncertainlp-main.4.pdf)
- "When Can Conformal Risk Control Certify LLM Outputs? Bounds, Impossibility, and Adaptation for Structured Generation" — [arXiv 2606.29054](https://arxiv.org/pdf/2606.29054)

### Inferences
- **Zerva & Martins' finding of uneven coverage across languages is directly on point for a Hinglish project.** Marginal coverage can hold at 90% overall while Hinglish-heavy inputs are covered at 70%. The correct response is to report **class- and language-stratified coverage (FSC)**, not just the marginal number. This is a cheap addition that will read as genuine methodological care.
- Because an LLM-in-the-loop pipeline breaks exchangeability, the defensible architecture is to **conformalise a classifier's scores over a fixed, exchangeable labelled set** (i.i.d. sampled posts), not to conformalise free-form LLM generation. The guarantee then actually holds. Claiming a coverage guarantee over LLM-generated text would be over-claiming.
- Dey et al.'s POS result (<4% multi-label sets at 99% confidence) and Fisch et al.'s few-shot result (average set size <2 at 95%) are the right order-of-magnitude expectations to cite for what "good" looks like on a small-label-space task.

### Gaps
- **RAPS** (Regularized Adaptive Prediction Sets) formulas were not in the retrieved excerpt of arXiv 2107.07511 and I have no source for them.
- Barber et al. (2023), Romano et al. (2020), Zerva & Martins (2023) and Dutta et al. (2023) are cited **via the survey only**; I did not retrieve the primary papers and have no direct URLs for them.

---

## Q9. Conformal prediction for abstention specifically; terminology

### Takeaway
There are at least three distinct, named mechanisms, and they are **not interchangeable**: (a) **empty-set abstention** (output nothing when underconfident), (b) **set-size abstention** (defer when the prediction set is not a singleton), and (c) **threshold-based three-way routing** (predict / return set / abstain). Terminology in the literature is inconsistent — "conformal abstention," "selective generation," "selective classification," and "conformal risk control" are used by different groups for overlapping ideas.

### Cited Findings
- **Hechtlinger et al. (2019)**, via the survey: density-based prediction outputs the **empty set** when the model is underconfident, useful for outliers — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- **Ren et al. (2023)**, via the survey: robots **request help when the prediction set is not a singleton** — this is the canonical set-size-based abstention rule — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- **Yadkori et al., "Mitigating LLM hallucinations via conformal abstention"** (arXiv:2405.01563) treats abstention as a **binary predict-or-abstain choice** — [cited in arXiv 2502.06884](https://arxiv.org/html/2502.06884v1)
- The survey describes abstention with the phrasing "**abstain from answering**" or "**defer to a human**" when confidence is low, and **does not use the term "selective prediction"** anywhere — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- **Conformalized Abstention Policy (CAP)** / "Learning Conformal Abstention Policies for Adaptive Risk Management in LLMs and VLMs": uses nonconformity score **s(x) = 1 − max_i p_i(x)** with **two learned thresholds q̂_predict and q̂_abstain**, giving three outcomes — score below q̂_predict → single prediction; **between the two → return a prediction set (the "ambiguous" regime)**; at or above q̂_abstain → abstain. Thresholds are set by a **REINFORCE** policy sampling confidence levels α and β, with a cost penalising error, set size and abstention while rewarding coverage and decision diversity. Decisions are stochastic via sigmoid action probabilities with a scaling constant c — [arXiv 2502.06884](https://arxiv.org/html/2502.06884v1)
- CAP's own terminology: it uses "conformal prediction," "conformalized abstention policy (CAP)," "selective generation," and "abstention" — and **does not use "selective classification" or "conformal risk control"** — [arXiv 2502.06884](https://arxiv.org/html/2502.06884v1)
- CAP's reported metrics: **coverage** (target 90%, defined in its cost function as 1 − abstention), **average set size (SS)**, **accuracy** with fractional credit for set predictions, **AUROC** for hallucination detection, **AUARC** for uncertainty-guided selective generation, and **ECE** for calibration. It does **not** report an explicit abstention rate or hallucination rate in its tables — [arXiv 2502.06884](https://arxiv.org/html/2502.06884v1)
- **Conformal risk control** as a distinct formalism: "Learn then Test: Calibrating Predictive Algorithms to Achieve Risk Control" — [arXiv 2110.01052](https://arxiv.org/pdf/2110.01052); risk-control bounds of the form expected loss ≤ β are listed among standard CP metrics — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf); implementation reference — [MAPIE risk-control docs](https://mapie.readthedocs.io/en/latest/theory/risk-control/)
- **Selective Conformal Risk Control** — [arXiv 2512.12844](https://arxiv.org/html/2512.12844v1)
- **Conformalized Selective Regression** — [Lucy Institute PDF of arXiv 2402.16300v3](https://lucyinstitute.nd.edu/wp-content/uploads/2025/02/2402.16300v3.pdf)
- Overview of the conformal abstention framework as a topic area — [EmergentMind](https://www.emergentmind.com/topics/conformal-abstention) *(secondary/aggregator source — use only for orientation, not for claims)*

### Inferences
- For a small-scale implementation, the **set-size rule (Ren et al. style) is the right choice**: if |C(x)| ≠ 1, abstain and route to a human or to a "we can't tell" message. It requires **no extra calibration beyond the single conformal threshold**, inherits the coverage guarantee directly, and is trivially explainable to a judging panel. CAP's two-threshold RL approach needs policy training and far more data; it is the wrong tool at small n.
- The honest claim available from a set-size rule is precise and worth stating exactly: *"when we do answer, the true label is in our prediction set at least (1−α) of the time; we abstain on X% of inputs."* Note that this is **marginal coverage over all inputs including abstained ones**, so the conditional accuracy on non-abstained inputs is a *separate* number that must be measured, not assumed.
- Terminology recommendation: use **"conformal abstention"** (matching Yadkori et al. and CAP) for the mechanism and **"selective classification"** only when citing the broader pre-conformal literature. The TACL survey's avoidance of "selective prediction" suggests it is not the NLP-community term.
- **Conformal risk control is a different guarantee** (bounding an expected loss) from conformal prediction (bounding miscoverage). If the project wants to bound something asymmetric — e.g. the rate of failing to flag a genuine scam, which is the costly error here — that is a **risk control** problem via Learn-then-Test, not a vanilla CP problem. Conflating the two in a writeup would be a real technical error.

### Gaps
- The Yadkori et al. method's internals are **not sourced** — I have only CAP's one-line characterisation of it as binary predict-or-abstain. The primary paper was not retrieved.
- No source found giving typical/acceptable **abstention rates** for a text-classification deployment. There appears to be no published norm, so any target abstention rate will be a design decision to defend rather than a benchmark to meet.

---

## Q10. Sample sizes needed for a valid conformal calibration set; published minimum-size guidance

### Takeaway
**This is the highest-confidence, most immediately actionable finding in the whole note.** There is explicit published guidance with a table. The hard floor is **n ≥ 1/α − 1** (so n ≥ 9 for 90% coverage), but the *practically useful* numbers are: **n ≈ 100 gives coverage within ±0.05**, **n = 1000 gives coverage typically between 0.88 and 0.92 at α = 0.1**, and **±0.01 requires n ≈ 2,500**. A hand-labelled set of a few hundred is scientifically defensible; a set of 30 is not.

### Cited Findings
- **Conformal quantile level:** the threshold is the **⌈(n+1)(1−α)⌉/n** empirical quantile of calibration scores, implemented as `np.ceil((n+1)*(1-alpha))/n` — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- **Marginal coverage guarantee (two-sided):** **1 − α ≤ P(Y_test ∈ C(X_test)) ≤ 1 − α + 1/(n+1)** — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- **Distribution of achieved coverage, conditional on the calibration set:** coverage is a random variable distributed **Beta(n + 1 − l, l)** where **l = ⌊(n+1)α⌋** — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- **Convergence rate:** the coverage distribution converges to 1−α at rate **O(n^(−1/2))** — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- **Table 1 (δ = 0.1, α = 0.1): calibration size n(ε) required for coverage slack ε** — ε=0.1 → **n=22**; ε=0.05 → **n=102**; ε=0.01 → **n=2,491**; ε=0.005 → **n=9,812**; ε=0.001 → **n=244,390** — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- **Practical recommendation:** "choosing a calibration set of size **n = 1000** is sufficient for most purposes"; the paper's introductory example uses ~**500** calibration points — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- **For n = 1000 at α = 0.1**, realised coverage "is typically between **.88 and .92**" — [arXiv 2107.07511](https://arxiv.org/pdf/2107.07511)
- The **TACL NLP survey independently cites ~1,000 samples** as reasonable for most purposes, and notes **Dutta et al. (2023)** construct a calibration set from web data when labelled data is scarce — [TACL survey](https://aclanthology.org/2024.tacl-1.82.pdf)
- Dedicated work on the small-data regime: **"Reliable Statistical Guarantees for Conformal Predictors with Small Datasets"** — [arXiv 2512.04566](https://arxiv.org/pdf/2512.04566)
- **"Universal distribution of the empirical coverage in split conformal prediction"** — the distributional characterisation of empirical coverage — [arXiv 2303.02770](https://arxiv.org/html/2303.02770v2)
- **"Backward Conformal Prediction"** — [arXiv 2505.13732](https://arxiv.org/pdf/2505.13732)
- **"E-Values Expand the Scope of Conformal Prediction"** — [arXiv 2503.13050](https://arxiv.org/pdf/2503.13050)

### Inferences
- **The n ≥ 1/α − 1 floor is my derivation, not a quoted statement.** It follows because the ceiling term ⌈(n+1)(1−α)⌉ must not exceed n, requiring n ≥ (1−α)/α = 1/α − 1; for α = 0.1 this gives n ≥ 9. The retrieved excerpt of arXiv 2107.07511 **does not state this explicitly**. Flagging this because the distinction between sourced and derived matters: if n < 1/α − 1 the quantile is undefined and the method silently returns the full label set.
- **Practical recommendation for a hand-labelled implementation:** target **n ≈ 100–200 calibration examples at α = 0.1**. Table 1 says n=102 buys ±0.05 coverage slack at 90% confidence, which is an honest and achievable claim ("empirical coverage 0.90, within ±0.05 at n=102"). Do not claim tight coverage — ±0.01 would need ~2,500 labels, which is out of reach for hand labelling.
- **n = 22 is the minimum that buys anything meaningful** (±0.1 slack). Below ~20, the guarantee is technically valid but so loose as to be rhetorically worthless.
- Note the **Beta(n+1−l, l) result is the right thing to report** for a small calibration set: rather than asserting "we achieve 90% coverage," compute and state the Beta interval for the actual n. This converts a weak small-sample position into a rigorous one, and is exactly the kind of move that answers "is this technically deep?"
- The **calibration set must be exchangeable with deployment data**. For a Hinglish social-media project this is the real risk, not sample size — if calibration posts are scraped from one Telegram channel and deployment is WhatsApp forwards, the guarantee does not transfer. Combined with Zerva & Martins' uneven-coverage-across-languages finding (Q8), the calibration set should be **stratified by language mix** and coverage reported per stratum.
- Labelling budget reality: ~150 calibration examples plus a separate held-out test set of similar size is roughly **300 hand labels**. That is a realistic solo weekend-to-week effort and should be planned explicitly, with the label rubric fixed before any labelling begins.

### Gaps
- The **exact formula behind Table 1** (the tail-control expression relating n, ε and δ) was not in the retrieved excerpt — only the tabulated values. Appendix C of the paper is cited for exact mean and SD formulas but was not retrieved.
- Table 1 is given **only for δ = 0.1, α = 0.1**. I have no numbers for other α (e.g. α = 0.05, which would require a larger n).
- I did not retrieve the contents of arXiv 2512.04566 (small-dataset conformal guarantees), which is likely the single most relevant paper for this exact question. **The report writer should flag this as a recommended follow-up read.**

---

## Cross-cutting positioning assessment (synthesis for the panel question "is this novel and technically deep?")

### Takeaway
Be blunt: **financial misinformation detection is crowded ground** — shared task, leaderboard, dedicated models, hundreds of hackathon clones. **Adviser verification is occupied by the regulator.** The genuinely defensible seams are narrower and all three are citable.

### Inferences (all inference, not sourced claims)
**Crowded — do not claim novelty here:**
- Detecting whether a financial claim is true. FinNLP 2025 shared task, FMDLlama, MFMDQwen, Fin-Fact. Winner at Micro-F1 0.8467.
- Adviser/payee credential lookup. SEBI Check, live since 1 Oct 2025, with NPCI-issued `@valid` handles.
- Crypto pump-and-dump detection. Multiple papers, including Telegram-specific real-time systems.
- "AI misinformation detector" as a hackathon pitch. Three unrelated projects named TruthLens; a 5.6K-registration Indian government hackathon on exactly this.

**Genuine white space — defensible, with citations:**
1. **Unregistered-advice detection as an act-type + status classification task.** IOSCO FR/08/2025 shows regulators doing this with keyword web scrapers and manual filtering, and lists ML surveillance as an *aspiration*. The ML literature targets truth, not regulatory status. No public dataset or benchmark found.
2. **Hinglish / code-mixed as the primary input.** MFMDQwen's multilingual coverage is English, Chinese, Bengali, Greek — explicitly no Hindi, no code-mixed. GLUECoS EN-HI semantic tasks sit in the high 50s. This is a clean, citable hole.
3. **Calibrated abstention with a reported coverage guarantee.** Rare in hackathon work, cheap to implement correctly, and the established FMD benchmark already has an NEI class, so "cannot determine" is a legitimate output in this field rather than a dodge.

**Three honesty constraints the project should self-impose:**
- Do not conformalise LLM free generation and claim a guarantee — exchangeability breaks. Conformalise a classifier over an i.i.d. labelled set.
- Report **stratified** coverage (by language mix and by class), not only marginal — Zerva & Martins found coverage varies across languages.
- Do not report 90%+ on a Hinglish semantic task without explaining why, given that published EN-HI sentiment/NLI sit near 57–58 and code-mixed NER near 78–80.
