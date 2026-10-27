# Programmatic verification of SEBI registration status for Indian financial influencers / advisers (state as of 26 Oct 2026)

All findings below were verified by live fetch on **26 October 2026** unless otherwise stated. Pages fetched live are marked **[verified live 2026-10-26]**.

---

## Q1. Does SEBI publish RIA / RA lists? Exact URLs, format, update frequency, pagination, GET vs POST?

### Takeaway
Yes — SEBI publishes live, complete, dated lists of Registered Investment Advisers (1,062 records) and Research Analysts (2,324 records) as paginated HTML under a single Java servlet (`OtherAction.do`), stamped "as on date Oct 25, 2026". But there is **no Excel/CSV/JSON download and no URL-addressable search**: the Download column exists and is empty, and filtering is driven by a JavaScript function (`searchFormFpi`) that posts into the same servlet, so a scraper must replicate a POST/session flow rather than hitting clean GET URLs.

### Cited Findings
- The intermediaries hub page lists 13 entry points; the relevant one is "Recognised Intermediaries" at `https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognised=yes`. This hub page itself contains **no** investment-adviser or research-analyst list and **no XLS/CSV downloads** — the only PDF on it is the Terminated Depository Participants list — [SEBI Intermediaries](https://www.sebi.gov.in/intermediaries.html) **[verified live 2026-10-26]**
- Per-category lists are addressed by a numeric `intmId` parameter on the same servlet. **Investment Adviser = `intmId=13`; Research Analyst = `intmId=14`**, in the form `OtherAction.do?doRecognisedFpi=yes&intmId=N` — [SEBI Recognised Intermediaries](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognised=yes) **[verified live 2026-10-26]**
- **Investment Adviser list:** `https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13` — titled "Investment Adviser", shows "**1 to 25 of 1062 records**", carries the note "Registered intermediaries **as on date Oct 25, 2026**" — [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**
- IA list columns: **Name (sometimes with trade name), Registration No., E-mail, Telephone, Fax No., Address, Contact Person, Correspondence Address, Validity**. Fields are sparsely populated — some records have no email or telephone, one had no correspondence address — [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**
- **Research Analyst list:** `https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=14` — "**1 to 25 of 2324 records**", same "as on date Oct 25, 2026" stamp, ~93 pages at 25/page — [SEBI RA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=14) **[verified live 2026-10-26]**
- **Pagination mechanics:** 25 records/page. Page-number links call a JavaScript function, not hrefs: page 2 is `searchFormFpi('n','1')`, page 3 is `searchFormFpi('n','2')` — i.e. **zero-indexed page offset**. "Last" on the IA list renders as `searchFormFpi('n','42')`, consistent with ceil(1062/25) = 43 pages. There is also a **"Show All Records"** control calling `searchAllIntm()` — [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**
- **No download:** the summary table on the Recognised Intermediaries page has a "Download" column but **every cell in it is empty**, and neither the IA nor RA list page exposes an XLS/CSV/JSON link — [SEBI Recognised Intermediaries](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognised=yes); [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**
- **On-page search fields exist** on the list pages: Name, Registration No., Contact Person, Email, Location (plus Exchange and Affiliated Broker on the all-intermediaries view) — [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**
- **Appending a search value as a GET query parameter does not filter.** Fetching `...&intmId=13&searchValue=INA000017523` returned all search boxes blank and still "**1 to 25 of 1062 records**" — the arbitrary GET param was ignored — [test fetch](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13&searchValue=INA000017523) **[verified live 2026-10-26]**
- The combined all-intermediaries view (`doRecognisedFpiFilter=yes`, labelled "Entities Registered with SEBI & Permitted to Send Stock Tips using Bulk SMS") holds **13,351 records** across all intermediary types with the same 25/page HTML pagination and **no downloads** — [SEBI bulk-SMS/all-intermediaries list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpiFilter=yes) **[verified live 2026-10-26]**
- A separate "Details of Cancelled/Surrendered/Expired/Suspended Intermediaries" page exists at `https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpiFilter2=yes` — [SEBI Intermediaries](https://www.sebi.gov.in/intermediaries.html)

### Inferences
- The `?doRecognisedFpi=yes&intmId=N` URL **is** directly fetchable without a session (both IA and RA pages rendered on a cold fetch), so page 1 of any category is a clean GET. Only pages 2+ and filtered queries require reproducing the `searchFormFpi` submission — almost certainly a **POST back to `OtherAction.do`** with a page-index field, since the handler is a JS form submit rather than a link. The exact POST body field names were not recoverable from rendered text and must be captured from the live DOM/network tab.
- Because the lists are "as on date Oct 25, 2026" and fetched on 26 Oct, the stamp appears to be **refreshed roughly daily (T-1)**. This is an inference from a single observation, not a documented SLA.
- `intmId` is a small dense integer space (13 = IA, 14 = RA out of a catalogue of ~13 types), so enumerating `intmId=1..30` would likely discover every category's list URL cheaply.
- **"Show All Records" (`searchAllIntm()`) is the single highest-value target for a 10-day build**: if it returns all 1,062 IA + 2,324 RA rows in one response, the whole registry is two requests, not 136 paginated ones. This is unverified and should be the developer's first test.

### Gaps
- Exact HTTP method, endpoint and POST field names behind `searchFormFpi()` / `searchAllIntm()` — the rendered-text fetch tool could not expose the `<form>` tag, and direct `curl` to `www.sebi.gov.in` was blocked by this environment's egress proxy (HTTP 403 on CONNECT), so no header or raw-HTML inspection was possible.
- Whether sessions/cookies/CSRF tokens are required for pagination.
- No documented update frequency or changelog for the lists; no "last modified" HTTP header captured.
- Whether the Validity field expresses "Perpetual" vs an expiry date, and how suspended registrations are represented on the main list.

---

## Q2. Is there an official SEBI API? SI Portal, SCORES, SEBI Check, the "specified digital platform"?

### Takeaway
**No official SEBI API for intermediary registration exists, and nothing found suggests one is planned.** "SEBI Check" is live but is a **payment-channel** verifier (UPI handle / bank account + IFSC), not a registration-status lookup, and exposes no public API. The "Specified Digital Platform" framework is a **2024 consultation that remains unlaunched with no published list of recognised platforms as of mid/late 2026** — treat it as vapour for build purposes.

### Cited Findings
- **No API documentation, developer portal, or JSON endpoint was found on sebi.gov.in** across the searches and fetches conducted. The intermediaries hub lists only HTML pages and one PDF — [SEBI Intermediaries](https://www.sebi.gov.in/intermediaries.html) **[verified live 2026-10-26]**
- **SEBI Check is live** at `https://siportal.sebi.gov.in/intermediary/sebi-check` (also via the SEBI Saarthi mobile app); SEBI Check and @valid UPI handles were "fully operational from **October 1, 2025**" — [Stable Investor](https://stableinvestor.com/2025/10/valid-upi-sebi-check.html)
- SEBI Check verifies **UPI IDs, and bank account number + IFSC for NEFT/RTGS/IMPS**. Inputs described are: scan the intermediary's QR code, manually enter a UPI ID, or enter bank account + IFSC. **Name and SEBI registration number are not inputs** — [SEBI Check page](https://www.sebi.gov.in/sebi_check.html); [Stable Investor](https://stableinvestor.com/2025/10/valid-upi-sebi-check.html) **[sebi_check.html verified live 2026-10-26]**
- A live fetch of `https://siportal.sebi.gov.in/intermediary/sebi-check` returned a rendered page with **no input fields, no forms, no buttons, and no API/JSON endpoint visible** in the text layer; the only URL present was the SEBI logo image path. No public intermediary search is present on the SI Portal page — [SI Portal SEBI Check](https://siportal.sebi.gov.in/intermediary/sebi-check) **[verified live 2026-10-26]**
- **Conflict to flag:** the static `https://www.sebi.gov.in/sebi_check.html` page still reads "**Coming Soon!**" with a launch date of 1 Oct 2025, while the SI Portal URL serves a rendered page and press coverage says it went live 1 Oct 2025 — [SEBI Check page](https://www.sebi.gov.in/sebi_check.html) **[verified live 2026-10-26]**; contradicted by [Stable Investor](https://stableinvestor.com/2025/10/valid-upi-sebi-check.html)
- The **Specified Digital Platform (SDP)** framework originated in a SEBI **consultation paper (Oct 2024)**, with the comment deadline extended to **26 Nov 2024** — [Outlook Business](https://www.outlookbusiness.com/markets/sebi-extends-deadline-till-nov-26-for-comments-on-proposed-specified-digital-platform); [MediaNama](https://www.medianama.com/2024/10/223-sebi-consultation-paper-on-recognition-as-specified-digital-platform/); consultation paper text mirrored at [CFA Society India](https://cfasocietyindia.org/wp-content/uploads/2024/11/Consultation-paper-on-recognition-as-specfied-digital-platform.pdf)
- As of mid-2026 there is **no confirmed launch and no published list of recognised SDPs**; the list "was being developed through 2025" and advisers are told to check SEBI's website for which platforms carry the designation — [Dealplexus](https://www.dealplexus.com/blog/sebi-finfluencer-rules/raw)
- Related live regulation (context, not a data source): SEBI (Intermediaries) (Amendment) Regulations 2024, effective **29 Aug 2024**, inserted Sec. 16A barring regulated entities from associating with unregistered advice-givers; the **22 Oct 2024** circular created the SDP carve-out; a **29 Jan 2025** circular covered live market data — [Dealplexus](https://www.dealplexus.com/blog/sebi-finfluencer-rules/raw); [Nishith Desai](https://nishithdesai.com/research-and-articles/hotline/technology-law-analysis/securities-market-regulators-continued-quest-against-unfiltered-financial-advice-15193)
- SEBI ordered regulated entities to terminate associations with unregistered advisers within three months — [Outlook Business](https://www.outlookbusiness.com/news/sebi-asks-registered-entities-to-end-contracts-with-unregistered-advisors-in-3-months); [Moneylife](https://moneylife.in/article/sebi-asks-regulated-entities-to-cut-off-association-with-finfluencers-in-3-months/75451.html)

### Inferences
- The **SI Portal is an intermediary-facing (login) system**, not a public query surface; SEBI Check is the only public-facing piece and its scope is payments. So the *only* public registration-status surface remains the `OtherAction.do` HTML lists.
- The sebi_check.html "Coming Soon" page is most likely a stale static page that was never updated after the SI Portal version shipped. A developer should treat the SI Portal URL as canonical but verify in a real browser.
- SEBI Check is potentially interesting as a **second signal** (does this person's collection UPI handle resolve to a registered intermediary?) but it is a human-facing form with no documented API; any programmatic use would be undocumented reverse-engineering of an authenticated-ish portal — higher legal and breakage risk than the public lists.
- **Do not plan around the SDP.** Two years after consultation with no notified list, it cannot be a dependency in a 10-day build.

### Gaps
- Whether SEBI Check's front end calls a JSON XHR endpoint that could be reused — requires a real browser with a network tab; the text-only fetch showed no fields at all (likely JS-rendered).
- Whether SCORES (the complaints system) exposes any entity-registration lookup. **Not investigated in depth** — no evidence found either way; SCORES is a complaint-filing system and is unlikely to be a registry.
- Whether a logged-in SI Portal account (available only to registered intermediaries) unlocks richer data.

---

## Q3. BASL (BSE Administration & Supervision Ltd) — searchable/downloadable member list?

### Takeaway
**No. BASL does not appear to publish a public member list in any format.** The BASL-related page on BSE's site is a policy/enlistment page with no names, no search form and no download; `bseasl.com` could not be retrieved; the only published artefacts are membership FAQs and an SOP PDF. Treat BASL as **not programmatically accessible**.

### Cited Findings
- `https://bseindia.com/static/iara/basl_member.aspx` is **an enlistment-policy page, not a member list**: it states that existing SEBI-registered IAs who were BASL members are deemed enlisted with the Exchange and that new IA applicants must get enlisted. It shows **no names, no lists, no records, no download link and no search form** — the only contact point is `ia.membership@bseindia.com` — [BSE BASL member page](https://bseindia.com/static/iara/basl_member.aspx) **[verified live 2026-10-26]**
- `https://www.bseasl.com/` **could not be fetched — blocked by robots.txt** (fetch tool reported `robots_disallowed`), so its contents and any member list could not be assessed — [bseasl.com](https://www.bseasl.com/) **[attempted live 2026-10-26]**
- The only BASL documents surfaced are PDFs: [FAQs on BASL Membership](https://www.bseindia.com/downloads1/FAQs_BASL_Membership.pdf) and [SOP for BASL membership](https://www.bseindia.com/downloads1/SOP_BASL_membership.pdf)
- BASL was set up by BSE as the administration and supervision body for registered investment advisers; existing IAs had to obtain BASL membership by **31 Aug** (of the rollout year) — [Morningstar India](https://www.morningstar.in/posts/63664/rias-have-to-register-with-bse-administration-supervision-ltd.aspx); [Money9](https://www.money9.com/news/markets/bse-sets-up-entity-to-administer-supervise-activities-of-registered-investment-advisors-44729.html); [Daily Excelsior](https://www.dailyexcelsior.com/?p=1170306)
- SEBI's modalities for IA applications routed through BASL are summarised by [Nishith Desai](https://nishithdesai.com/research-and-articles/hotline/regulatory-hotline/sebi-issues-modalities-related-to-investment-adviser-applications-8307)

### Inferences
- Because BASL membership is **mandatory and co-extensive with RIA registration**, a BASL list would be largely redundant with SEBI's `intmId=13` list. Its absence costs little.
- `bseasl.com` being robots-disallowed is itself a signal: even if a member list existed there, **scraping it would be against the site's stated robot policy**, which is the kind of thing that matters if a verification product is ever challenged.

### Gaps
- Whether `bseasl.com` hosts a member directory at all — robots.txt blocked assessment; the developer should open it manually in a browser.
- Whether BASL membership numbers (distinct from SEBI INA numbers) exist and are published anywhere.
- Whether an equivalent body exists for Research Analysts (RAASB) and whether it publishes a list — **not investigated**.

---

## Q4. Machine-readable enforcement / debarment / unregistered-entity data?

### Takeaway
SEBI itself publishes enforcement material as **unstructured HTML/PDF orders and press releases with no structured index** — not machine-readable. But there is a strong workaround: **NSE publishes the consolidated SEBI-debarred-entities list as two direct-download .XLS files**, and **OpenSanctions already ingests them daily into clean CSV/JSON**. This is the single best machine-readable negative-signal source available today.

### Cited Findings
- **NSE direct .XLS downloads [best machine-readable enforcement source found]:**
  - SEBI-debarred entities (.xls, ~1,042 KB): `https://nsearchives.nseindia.com/content/press/prs_ra_sebi.xls`
  - Entities debarred by other competent authorities (.xls, ~319 KB): `https://nsearchives.nseindia.com/content/press/prs_ra_others.xls`
  - Procedure for entities granted interim relief (.pdf): `https://nsearchives.nseindia.com/content/press/Procedure_Interim_Relief.pdf`
  — all linked from [NSE member-SEBI-debarred-entities](https://www.nseindia.com/regulations/member-sebi-debarred-entities) **[verified live 2026-10-26]**
- The NSE page shows a **single ambiguous "Updated on: 07/08/2025" stamp and states no update schedule** — [NSE member-SEBI-debarred-entities](https://www.nseindia.com/regulations/member-sebi-debarred-entities) **[verified live 2026-10-26]**
- **OpenSanctions dataset `in_nse_debarred`** mirrors exactly those NSE files (it declares the upstream source as the NSE page above) and republishes them in clean formats:
  - FTM entities JSON: `https://data.opensanctions.org/artifacts/in_nse_debarred/<snapshot>/entities.ftm.json` (~20.9 MB)
  - Simplified targets CSV: `.../targets.simple.csv` (~3.49 MB)
  - Nested targets JSON: `.../targets.nested.json` (~21.1 MB)
  - Names TXT: `.../names.txt` (~340 KB)
  - Senzing JSON: `.../senzing.json` (~7.69 MB)
  - Raw upstream copies: `.../sebi.xls` (~8.39 MB) and `.../other.xlsx` (~329 KB)
  - **Stable "latest" index (use this, not a pinned snapshot): `https://data.opensanctions.org/datasets/latest/in_nse_debarred/index.json`**
  — [OpenSanctions in_nse_debarred](https://opensanctions.org/datasets/in_nse_debarred/) **[verified live 2026-10-26]**
- Dataset scale and freshness: **30,783 total entities; 14,550 searchable; 14,434 targets; 14,413 legal entities; 137 companies. Updated daily — last processed 2026-10-26, last change 2026-10-19** — [OpenSanctions in_nse_debarred](https://opensanctions.org/datasets/in_nse_debarred/) **[verified live 2026-10-26]**
- **Licensing is the catch:** OpenSanctions data is **CC BY-NC 4.0 — free for non-commercial use only; businesses must acquire a data licence**, with no price published — [OpenSanctions in_nse_debarred](https://opensanctions.org/datasets/in_nse_debarred/); terms at [OpenSanctions licensing](https://www.opensanctions.org/licensing/)
- SEBI's own "cautions investors" material is a **series of individual dated press releases listing barred/unregistered entities**, e.g. [Jun 2016](https://www.sebi.gov.in/media/press-releases/jun-2016/sebi-cautions-investors-not-to-invest-in-schemes-offered-by-entities-barred-by-sebi-from-raising-money-or-entities-not-registered-with-sebi_32603.html), [Nov 2015](https://www.sebi.gov.in/media/press-releases/nov-2015/sebi-cautions-investors-not-to-invest-in-schemes-offered-by-entities-barred-by-sebi-from-raising-money-or-entities-not-registered-with-sebi_30945.html), [Jul 2015](https://www.sebi.gov.in/media/press-releases/jul-2015/sebi-cautions-investors-not-to-invest-in-schemes-offered-by-entities-barred-by-sebi-from-raising-money-or-entities-not-registered-with-sebi_30361.html) — each a standalone HTML page, with no consolidated machine-readable list
- BSE also issues debarment circulars; NSE pushes SEBI IA-debarment orders out as individual numbered circular **PDFs** (e.g. [INVG59716](https://archives.nseindia.com/content/circulars/INVG59716.pdf), [INVG64861](https://archives.nseindia.com/content/circulars/INVG64861.pdf), [INVG65469](https://nsearchives.nseindia.com/content/circulars/INVG65469.pdf)) — PDF-only, not structured
- A commercial **"BSE Debarred List API"** is marketed by Surepass — [Surepass](https://surepass.io/bse-debarred-list-api/) (pricing and coverage not verified)
- NSE also maintains a regulatory-actions landing page — [NSE exchange market surveillance regulatory actions](https://www.nseindia.com/static/regulations/exchange-market-surveillance-regulatory-actions)
- `https://www.sebi.gov.in/enforcement/orders` returned a **client error on live fetch** — the enforcement-orders index could not be characterised **[attempted live 2026-10-26]**

### Inferences
- The NSE .XLS pair is the **highest-leverage enforcement ingest for a 10-day build**: two stable URLs, binary Excel, parseable with `pandas.read_excel` / `xlrd`, no pagination, no session, no robots issue found. Risk: `.xls` (legacy BIFF) needs `xlrd<2.0` or conversion, and the ambiguous single update stamp means freshness is unguaranteed — poll and diff.
- **Build directly against NSE, not OpenSanctions**, if the product is commercial. OpenSanctions' value is as a *cleaned reference* and a *name-normalisation / entity-resolution exemplar*, and as a free dev-time sanity check; its NC licence makes it unusable in a paid product without a licence deal.
- Enforcement data is **name-keyed, not registration-number-keyed**, so joining it to the RIA/RA registry is a fuzzy-matching problem, not a key lookup. This is where most of the engineering risk in a verification layer sits.
- SEBI's own orders being PDF/HTML-only means **"is this person currently debarred?" cannot be answered authoritatively from SEBI directly** in a 10-day build; the NSE consolidation is the practical proxy.

### Gaps
- Column schema of `prs_ra_sebi.xls` / `prs_ra_others.xls` (fields, whether PAN or registration numbers are included, whether debarment start/end dates are present) — the files were not downloaded and parsed; direct `curl` to these hosts was blocked by this environment's egress proxy. **This should be the developer's first 30-minute test.**
- Real update cadence of the NSE files (no HTTP Last-Modified captured).
- Whether SEBI has a consolidated debarred-entities page of its own; the `/enforcement/orders` fetch failed.
- Surepass's BSE Debarred List API pricing, coverage, latency and whether it includes SEBI IA/RA debarments.

---

## Q5. AMFI (mutual fund distributors) and exchange (NSE/BSE) member lists

### Takeaway
**Weak to negative.** AMFI's distributor pages surfaced **no downloadable ARN list, no ARN-number lookup, and no suspended-ARN list** — only a "Locate a Mutual Fund Distributor" tile whose URL was not resolvable and whose own page 404'd on fetch. NSE/BSE trading-member lists were likewise not found as downloadable files. Neither is a usable base for finfluencer verification anyway, since ARN is a *distribution* licence, not an advisory one.

### Cited Findings
- `https://www.amfiindia.com/distributor-corner` contains **no downloadable ARN distributor list, no Excel/CSV/PDF of distributors, no described ARN-number search, and no suspended/terminated ARN list**. The only PDF on it is the [Revised Code of Conduct for MF Distributors](https://www.amfiindia.com/uploads/Revised_Codeof_Conductfor_Mutual_Fund_Distributors_April2022_57d91fe1c4.pdf). It has a "Locate a Mutual Fund Distributor" tile but gives no URL for it — [AMFI Distributor Corner](https://www.amfiindia.com/distributor-corner) **[verified live 2026-10-26]**
- A further AMFI quick-access page is referenced at `https://www.amfiindia.com/distributor-quick-access` but its contents were not described — [AMFI Distributor Corner](https://www.amfiindia.com/distributor-corner) **[verified live 2026-10-26]**
- An AMFI endpoint `https://www.amfiindia.com/modules/NearestFinancialAdvisorsDetails` appears in search results, suggesting the distributor locator is a `/modules/` servlet-style path — [search result](https://www.amfiindia.com/modules/NearestFinancialAdvisorsDetails)
- `https://www.amfiindia.com/locate-nearest-financial-advisor-details` returned a **client error on live fetch** — could not be characterised **[attempted live 2026-10-26]**
- AMFI publishes a registration form for overseas distributors as a PDF, but no member list — [AMFI overseas distributor form](https://www.amfiindia.com/uploads/Downloads_Registration_form_for_Overseas_Distributors_7d8151f52d.pdf)
- Searches for NSE/BSE downloadable trading-member lists surfaced only member-facing admission and disclosure pages — [NSE members disclosures](https://www.nseindia.com/trade/members-disclosures), [NSE admission process](https://nseindia.com/trade/admission-process-documents-to-become-a-member) — plus a narrow [NSE Emerge SME market makers list](https://www.nseindia.com/products-services/emerge-sme-market-makers-list). No general member CSV/XLS was found.
- **Broker registration status is, however, available inside SEBI's own system:** SEBI publishes "Default / Expelled Stock Brokers" at `https://www.sebi.gov.in/sebiweb/broker/BrokerAction.do?doBroker=yes` and the combined intermediaries list (13,351 records) includes brokers with Exchange and Affiliated Broker fields — [SEBI Intermediaries](https://www.sebi.gov.in/intermediaries.html); [SEBI all-intermediaries list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpiFilter=yes) **[verified live 2026-10-26]**

### Inferences
- The `/modules/NearestFinancialAdvisorsDetails` path pattern implies AMFI's locator is a **POST-driven servlet keyed on location**, not an ARN lookup — i.e. built for "find someone near me", not "verify this ARN". Wrong shape for verification even if reverse-engineered.
- **ARN is the wrong axis for finfluencer verification.** A finfluencer giving buy/sell advice needs INA (IA) or INH (RA); ARN only authorises distributing mutual funds. Including AMFI in a v1 adds surface area without answering the core question. Recommend deferring.
- Exchange member lists are similarly off-axis (brokers, not advisers) and are partly covered by SEBI's own intermediaries list anyway, so there is no reason to scrape NSE/BSE separately for v1.

### Gaps
- Whether `distributor-quick-access` or the locator servlet in fact permits ARN-number lookup — both fetches failed or were undescribed.
- Whether AMFI publishes suspended/cancelled ARN data anywhere. **No evidence found either way — this is an unanswered negative, not a confirmed absence.**
- Whether NSE/BSE publish downloadable member master files under non-obvious archive paths.

---

## Q6. Terms of service, robots.txt, rate limits, legal constraints on scraping

### Takeaway
SEBI's Website Policy **says nothing about scraping, bots, automated access or TDM** — but it does require **prior email permission before reproducing material**, which is the live legal question for a verification product. `bseasl.com` is robots-disallowed. `sebi.gov.in/robots.txt` could not be read in this environment, so the robots position on SEBI is **unverified** and must be checked before building.

### Cited Findings
- SEBI Website Policy, verbatim: "**Material featured on this Website may be reproduced free of charge after taking proper permission by sending a mail to us.**" Reproduction must be accurate, must not be derogatory or misleading, and "**the source must be prominently acknowledged**." The permission does not extend to "**any material which is identified as being copyright of a third party**" — [SEBI Website Policy](https://www.sebi.gov.in/website-policy.html) **[verified live 2026-10-26]**
- On hyperlinking: "**no prior permission is required for the same**", but SEBI asks to be informed of links, does **not** permit its pages to be loaded into frames, and requires links to "load into a newly opened browser window of the User" — [SEBI Website Policy](https://www.sebi.gov.in/website-policy.html) **[verified live 2026-10-26]**
- The policy **contains no mention of automated access, scraping, bots, text/data mining, or AI training**; its only reuse terms are the copyright clause above — [SEBI Website Policy](https://www.sebi.gov.in/website-policy.html) **[verified live 2026-10-26]**
- `https://www.bseasl.com/` is **disallowed by robots.txt** (fetch rejected with `robots_disallowed`) **[attempted live 2026-10-26]**
- OpenSanctions' redistribution of the NSE debarred data is **CC BY-NC 4.0: non-commercial free, commercial requires a licence** — [OpenSanctions in_nse_debarred](https://opensanctions.org/datasets/in_nse_debarred/); [OpenSanctions licensing](https://www.opensanctions.org/licensing/)
- A separate investor-facing SEBI disclaimer exists at [investor.sebi.gov.in/disclaimer.html](https://investor.sebi.gov.in/disclaimer.html) (not fetched)

### Inferences
- The permission clause is the **sharpest legal edge** and it cuts asymmetrically by product design:
  - **Low risk:** linking to SEBI, and *checking* a name/number against SEBI data at query time without republishing SEBI's dataset.
  - **Higher risk:** mirroring and republishing the 1,062 + 2,324 records as your own browsable directory — that is reproduction, and the policy asks for prior email permission plus prominent source acknowledgement.
  - Practical mitigation for a 10-day build: cache for verification, show "Source: SEBI, as on <date>" prominently, link back to the SEBI list page, and **send the permission email on day 1** — it costs nothing and removes the main objection.
- Absence of an anti-scraping clause is **not** permission; it just means the constraint is copyright/reproduction law rather than contractual ToS. There is no observed robots.txt prohibition on SEBI either, but that is unverified.
- No rate limits were observed or documented; SEBI's site is a legacy Java/JSP stack, so **be conservative (serialise requests, 1–2s delay, cache aggressively, nightly refresh not per-user hits)** — both to avoid blocks and because a 136-page crawl at speed is the behaviour most likely to get an IP banned.

### Gaps
- **`https://www.sebi.gov.in/robots.txt` could not be retrieved** — direct curl was blocked by this environment's egress proxy (403 on CONNECT). This is a must-check before writing a crawler.
- No published rate limits, WAF behaviour, or IP-ban thresholds for sebi.gov.in; no developer reports found.
- `nseindia.com` robots.txt and ToS position on the `nsearchives.nseindia.com` .xls downloads — **not checked**. NSE is known in developer circles for aggressive bot-blocking on its JSON APIs (cookie/session requirements), though that was not verified here for the archives host.
- Whether SEBI has ever acted against a scraper or mirror — no cases found.

---

## Q7. Third-party scrapers, mirrors, APIs over SEBI intermediary data

### Takeaway
**Thin to nonexistent for SEBI intermediary registration.** No GitHub repo, open-source project, or commercial API covering the SEBI RIA/RA lists was found. The only third-party structured coverage of *any* Indian securities-regulator data found was **OpenSanctions' daily `in_nse_debarred`** (debarments, not registrations) and a marketed **Surepass "BSE Debarred List API"**. The scraper ecosystem that search surfaces is overwhelmingly **US SEC/IAPD/BrokerCheck**, not SEBI.

### Cited Findings
- A GitHub-targeted search for SEBI intermediary scrapers returned **no SEBI repos at all** — every result was US-focused: SEC IAPD scrapers ([parseforge/sec-iapd-scraper](https://apify.com/parseforge/sec-iapd-scraper.md), [automation-lab](https://apify.com/automation-lab/sec-investment-adviser-public-disclosure-scraper), [scrapesage](https://apify.com/scrapesage/sec-investment-adviser-scraper), [jungle_synthesizer](https://apify.com/jungle_synthesizer/sec-investment-adviser-scraper/api)) and [BrokerCheck Scraper](https://apify.com/scrapesage/brokercheck-scraper)
- **OpenSanctions `in_nse_debarred`** is the one real third-party structured mirror: daily, 30,783 entities, CSV/JSON/FTM/Senzing outputs, upstream = NSE — [OpenSanctions in_nse_debarred](https://opensanctions.org/datasets/in_nse_debarred/) **[verified live 2026-10-26]**
- **Surepass "BSE Debarred List API"** is commercially marketed — [Surepass](https://surepass.io/bse-debarred-list-api/) (coverage/pricing unverified)
- Investor-education write-ups explaining the *manual* lookup process exist and are useful for sanity-checking field semantics, but offer no code or data — [Stable Investor: how to check if an investment advisor is SEBI registered](https://stableinvestor.com/2023/04/how-check-investment-advisor-sebi-registered.html); [freefincal](https://freefincal.com/how-to-choose-an-sebi-registered-investment-adviser/); [Outlook Money](https://www.outlookmoney.com/invest/how-to-check-if-your-investment-advisor-is-sebi-registered)

### Inferences
- **This is the positioning finding.** The absence of any SEBI RIA/RA scraper, mirror or API — against a dense US equivalent ecosystem — means a working verification layer over SEBI data is **genuinely unbuilt**, and the moat is unglamorous plumbing (POST reverse-engineering, name normalisation, enforcement joins) rather than insight.
- Honest counter-weight: absence of public scrapers may partly reflect **low demand and small data** (3,386 IA+RA records total — a hobbyist can eyeball the list), not just difficulty. The defensible value is almost certainly **not** the registry mirror; it is the **join** (registry ∧ enforcement ∧ social identity) and the identity-resolution layer that maps an Instagram/YouTube handle to a legal entity name. Nothing found addresses that mapping, and nothing in SEBI's data contains social handles.
- Negative-result searches here were single-pass keyword searches, not an exhaustive GitHub code search. Treat "no repos exist" as **"none discoverable via web search"** — moderate, not high, confidence.

### Gaps
- No direct GitHub code-search was possible with the available tools (only web search), so a low-star Indian repo could exist unindexed. Recommend the developer run `github.com/search?q=sebi+intermediaries+scraper&type=code` manually.
- No Indian fintech developer-community threads, Stack Overflow questions, or developer blog posts about scraping `OtherAction.do` were found — a notable silence.
- Whether Indian KYC/verification API vendors (Signzy, Karza, IDfy, Setu) resell SEBI intermediary lookups — **not investigated**.

---

## Q8. Practical gotchas: registration number formats, regex validation, lookup by number vs name

### Takeaway
Registration numbers are **regex-validatable but messier than the canonical pattern suggests**: alongside `INA\d{9}` / `INH\d{9}` the live SEBI list contains **IFSC-registered variants like `INAIFSC10001`** that break a naive digits-only regex. Critically, **a registration number cannot currently be looked up via a plain GET URL** — a search-field value appended as a query parameter was ignored — so direct number lookup requires replicating the JS form submission.

### Cited Findings
- **Investment Adviser numbers observed live** on SEBI's list: `INA000017523`, `INA200000365`, `INA100005050`, and `INAIFSC10001` — i.e. the prefix `INA` followed by **either 9 digits or the literal `IFSC` plus digits** — [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**
- **Research Analyst numbers observed live:** `INH` + nine digits, e.g. `INH300000211`, `INH000011431` — [SEBI RA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=14) **[verified live 2026-10-26]**
- The list pages **do expose a "Registration No." search field** (alongside Name, Contact Person, Email, Location), so lookup-by-number is supported *in the UI* — [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**
- **But GET-parameter lookup failed:** `...&intmId=13&searchValue=INA000017523` returned all search boxes blank and the unfiltered full count (1,062), confirming the parameter was not honoured — [test fetch](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13&searchValue=INA000017523) **[verified live 2026-10-26]**
- Field sparsity is real: IA records variously lack email, telephone, or correspondence address — [SEBI IA list](https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doRecognisedFpi=yes&intmId=13) **[verified live 2026-10-26]**

### Inferences
- **Suggested regexes** (derived from observed values; validate against a full dump before shipping):
  - IA: `^INA(?:\d{9}|IFSC\d{5})$` — or looser and safer: `^INA[A-Z0-9]{8,9}$`
  - RA: `^INH\d{9}$` (no IFSC variant observed for RA, but one may exist — do not assume)
  - A pragmatic permissive validator: `^IN[AH][A-Z0-9]{8,9}$`, then confirm against the actual registry rather than trusting the pattern.
- The third digit appears to encode a registering **regional office** (`INA000…`, `INA100…`, `INA200…`, `INH300…`), so **any regex that pins a specific third character will produce false negatives.** This is a pattern inference from four observed values, not documented by SEBI.
- **Format validation is near-worthless as a trust signal on its own** — a fraudster can trivially mint a format-valid `INA000000001`. The only meaningful check is membership in the actual registry, which is exactly why the dump matters more than the regex.
- Because GET lookup is dead, the architecture is forced toward **"mirror the whole registry nightly, then query locally"** rather than "proxy a live lookup per request." For 3,386 records that is the right design anyway: it is small, fast, and removes per-user dependency on SEBI uptime. It does, however, put you squarely in the "reproduction" zone of SEBI's Website Policy (see Q6).
- Validity/expiry handling is an unresolved correctness risk: a Validity field exists, but whether it says "Perpetual" or a date, and whether suspended-but-not-cancelled registrations still appear on the main list, is unknown. **A verification product that reports "registered" for a lapsed adviser is worse than no product.**

### Gaps
- Whether `INAIFSC`-style variants exist for RA (`INHIFSC…`) — not observed, not ruled out.
- Full enumeration of valid prefixes and the semantics of the third digit — no SEBI documentation found.
- Whether the Validity field contains dates or "Perpetual", and how suspensions are represented — requires parsing a real dump.
- Whether SEBI reuses or recycles registration numbers after cancellation.
- Whether individual (natural-person) RIAs are distinguishable from corporate RIAs in the data.

---

## Bottom line for the 10-day decision (synthesis, flagged as inference)

**Buildable today, with caveats:**
1. **Registry (positive signal) — YES, with reverse-engineering work.** Two live, dated, complete HTML lists: IA `intmId=13` (1,062) and RA `intmId=14` (2,324), as on 25 Oct 2026. Page 1 is a clean GET; pages 2+ need the `searchFormFpi` POST. **First test: whether `searchAllIntm()` dumps everything in one request.** No API, no CSV, no XLS — ever, from SEBI.
2. **Enforcement (negative signal) — YES, easiest win.** Two direct .XLS URLs on `nsearchives.nseindia.com` (`prs_ra_sebi.xls`, `prs_ra_others.xls`). Parse immediately; OpenSanctions proves the data is tractable (30,783 entities) but is NC-licensed so cannot back a commercial product.
3. **NOT available:** any SEBI API; SEBI Check as a registration lookup (it checks payment channels only); the Specified Digital Platform list (consultation-stage since Oct 2024, still unlaunched); a public BASL member list; an AMFI ARN dump or ARN lookup.
4. **The real engineering risk is not access — it is identity resolution.** SEBI data contains no social handles, and enforcement data is name-keyed. Mapping "this Instagram finfluencer" → "this legal entity in the SEBI list" is unsolved by any source found, and is where the 10 days will actually go.
5. **Legal: send SEBI the reproduction-permission email on day 1**, display "Source: SEBI, as on <date>" prominently, link back to the SEBI page, and verify `sebi.gov.in/robots.txt` (unreadable in this environment) before crawling.

### Environment caveat on this research
Direct `curl` to `www.sebi.gov.in`, `nsearchives.nseindia.com` and `amfiindia.com` was blocked by this session's egress proxy (HTTP 403 on CONNECT), so **no raw HTML, HTTP headers, robots.txt, or downloaded .xls files could be inspected**. All findings come from a rendered-text fetch tool, which strips `<form>` tags and attributes. Consequently: form methods, POST field names, robots.txt contents and .xls column schemas are **gaps, not negatives** — all four are resolvable in under an hour with a browser dev-tools session and a plain `curl`, and should be the developer's first actions.
