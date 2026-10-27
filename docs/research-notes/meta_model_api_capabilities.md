# Meta Model API — Technical Capabilities, Documented Language Support, and Practical Limits (as of October 2026)

**Scope note on source quality.** Everything in the "Cited Findings" below is labelled by source tier:
- **[PRIMARY]** = `dev.meta.ai` official docs/legal pages, `ai.meta.com` / `research.meta.ai` blogs and evaluation reports, official Hugging Face model cards, `pytorch.org` blog.
- **[SECONDARY-GOOD]** = independent write-up that states its method and labels its own numbers as its own (e.g. Kingy.ai).
- **[AGGREGATOR — LOW WEIGHT]** = SEO/benchmark-aggregator sites (benchlm.ai, llm-stats.com, empiriolabs.ai, musevoicetranscribe.org/.app, shattered.io, explainx.ai, aiweekly.co, apivale.com, spheron.network, aiagentslibrary.com, vortx.ch, orcarouter.ai, eesel.ai, wavespeed.ai, layer3labs.io). These were seen in search results and are **not** relied on for any fact below. Several of them (`musevoicetranscribe.org`, `musevoicetranscribe.app`, `empiriolabs.ai`, `benchlm.ai`) are domain-squatting/affiliate pages built around the model name and should be treated as non-evidence.

**The single most important finding is in the "Indian languages / code-mixed" section: Meta publishes NO per-language accuracy or WER figures for any Indian language on any Muse model. The multilingual support claims are documented support *lists*, not documented *performance*.**

---

## Q1: Per-model input/output modalities, context windows, pricing, and rate limits

### Takeaway
All pricing, context windows, model IDs and rate limits are fully and explicitly documented on two primary pages (`dev.meta.ai/docs/models` and `/docs/pricing-rate-limits`). Every hosted text model is **text-out only** with a 1,048,576-token context; the API-hosted lineup is cheap by frontier standards (Muse Spark at $1.25/$4.25 per M tokens, ASR at $0.18/hour). What is **not** documented anywhere is max output tokens, knowledge cutoffs, and — critically for budgeting — how image and video inputs convert into billable tokens.

### Cited Findings

**Modalities and context window** (all [PRIMARY], from the official model comparison table):
- `muse-wonder-1.0` (preview): input text, image, video, audio, PDF → output **text only**. Context 1,048,576 tokens. Tool calling and structured output supported. Reasoning effort `"minimal"` to `"max"` — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- `muse-spark-1.3`: input text, image, video, audio, PDF → output **text only**. Context 1,048,576 tokens. Tool calling + structured output. Full reasoning-effort range including `"max"` (`"max"` is **not** available on the Contributor variant) — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- `muse-spark-1.2` and `muse-spark-1.1`: same modalities, same 1,048,576-token context, tool calling + structured output — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- `muse-image-1.0`: input **text + image** → output **image** — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- `muse-voice-transcribe-1.0`: input **audio** → output **text transcript** — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- `sam-3.1`: input **text prompt + image + video** → output **boxes and masks** — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- Muse Spark Open (open weights): input text, image, video, audio → output text — [Meta Model API: Models](https://dev.meta.ai/docs/models); the HF card confirms "Input is text, image, video, and audio. Output is text only," context **1M tokens at inference** — [HF: Muse-Spark-Open-816B-A42B](https://huggingface.co/meta-models/Muse-Spark-Open-816B-A42B)
- Muse Glimmer (open weights): input **text + images only** → output text. No video, no audio — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- Docs page independently confirms 1,048,576 tokens for both Muse Wonder and Muse Spark — [Meta Model API docs](https://dev.meta.ai/docs)

**Token pricing, per 1M tokens** [PRIMARY] — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits):

| Model | Cached input | Input | Output |
|---|---|---|---|
| `muse-wonder-1.0` (Standard only) | $0.20 | $4.00 | $20.00 |
| Muse Spark 1.1 / 1.2 / 1.3 — **Standard** | $0.15 | $1.25 | $4.25 |
| Muse Spark 1.2 / 1.3 — **Contributor** | $0.002 | $0.10 | $0.20 |

- Muse Spark 1.1 is **Standard mode only** (no Contributor tier) — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Contributor mode is ~12.5x cheaper on input and ~21x cheaper on output, but "may use content to train future Meta models in exchange for a lower price" — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- Muse Wonder has **no Contributor tier** — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Web search grounding: **$2.50 per 1,000 search queries**, charged *in addition* to token costs — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)

**Per-unit (non-token) pricing** [PRIMARY] — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits):

| Model | Unit | Price |
|---|---|---|
| Muse Image | per generated image | **$0.01** |
| Muse Voice Transcribe | per hour of audio processed | **$0.18** (≈$0.003/min) |
| SAM 3.1 | per 1,000 images | **$2.50** |
| SAM 3.1 | per 1,000 video frames | **$0.20** |

- Muse Voice Transcribe is metered **in whole seconds, rounded down**; streaming and non-streaming cost the same; Contributor mode is not available for it — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Zero data retention is priced **at parity** with Standard mode for ASR — [Speech to text](https://dev.meta.ai/docs/speech-to-text)
- Failed requests and `429` responses are **not billed** (ASR) — [Speech to text](https://dev.meta.ai/docs/speech-to-text)
- **Each SAM video request is billed for a minimum of 50 frames** — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Muse Image pricing "includes" search grounding and reasoning at no extra charge — [Muse Image](https://dev.meta.ai/models/muse-image)

**Rate limits** [PRIMARY] — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits):

| Model | RPM | TPM | Other |
|---|---|---|---|
| `muse-wonder-1.0` | 3,000 | 4,000,000 | |
| `muse-spark-1.3` / `1.2` / `1.1` | 3,000 | 4,000,000 | |
| `muse-spark-1.3-contributor` / `1.2-contributor` | **150** | 3,000,000 | |
| `sam-3.1` | 3,000 | none | |
| `muse-image-1.0` | **150** | none | |
| `muse-voice-transcribe-1.0` | none | none | **256 concurrent streams; 16,000 streams/hour** |

- **Rate limits apply per team, not per API key** — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Background response creation (`background: true`) is capped at **600 submissions per minute per team**, on top of the above — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Rejected ASR streams close with WebSocket code `1013`; rejected batch ASR returns `429` — [Speech to text](https://dev.meta.ai/docs/speech-to-text)

### Inferences
- The Contributor-tier RPM drop (3,000 → 150, a 20x cut) means the cheap tier is unsuitable for bursty, user-facing concurrency even though it is far cheaper per token. For a 10-day prototype with low concurrency, 150 RPM is almost certainly ample, so Contributor is the rational default *if* the data-training trade-off is acceptable.
- Muse Image at 150 RPM is the tightest practical throughput ceiling in the lineup for an image-heavy app.
- Because every text model is text-out only, any "multimodal output" in an app has to be composed client-side by chaining Spark/Wonder (understanding) with Muse Image (generation).

### Gaps
- **Max output tokens: not documented for any model.** The official models page explicitly leaves this cell blank.
- **Knowledge cutoff: not documented for any model** — not on the models page, not on the Muse Spark model page, not on the Muse Spark Open HF card.
- **Caching support per model is not stated**, even though cached-input prices are published for Wonder and Spark (an odd documentation gap; cached pricing implies caching exists).
- **Free / trial credits: not documented anywhere.** Neither `/docs/overview`, `/docs`, `/docs/pricing-rate-limits`, nor the launch blog mentions any free credit grant. The $150 figure in this assignment is therefore an **external premise, not a documented Meta offer** — I could not verify its source.
- Muse Image context window, max images per request, resolutions, aspect ratios, rate-limit detail beyond RPM, and latency are all undocumented.

---

## Q2: Muse Voice Transcribe — languages, Indian languages, code-switching, WER, diarization, timestamps

### Takeaway
The **language list is well documented (25 verified of 70+ trained), and it does explicitly include six Indian languages — Hindi, Bengali, Marathi, Tamil, Telugu, Kannada.** Code-switching is *claimed* on both the docs and the research blog but backed by **zero published numbers**. Published WER is a **single aggregate English-only figure (3.1%)**; Meta publishes **no per-language WER at all**, and therefore **no Indian-language WER**. Diarization exists (speaker A/B labels) with a published aggregate DER; timestamps are **turn-level only — no word-level timestamps**.

### Cited Findings

**Documented language list (25 extensively verified)** [PRIMARY] — [Speech to text](https://dev.meta.ai/docs/speech-to-text):
- **Indian languages explicitly listed: Hindi, Bengali, Marathi, Tamil, Telugu, Kannada** (six)
- Other Asian/Middle Eastern: Arabic, Hebrew, Japanese, Korean, Mandarin Chinese, Malay, Indonesian, Tagalog, Thai, Vietnamese, Turkish
- European: Dutch, English, French, German, Italian, Polish, Portuguese, Spanish
- Trained on **70+ languages, with 25 "extensively verified"** — [research.meta.ai: Introducing Muse Voice Transcribe](https://research.meta.ai/blog/introducing-muse-voice-transcribe); the dev blog says "transcribes speech across 25+ languages" — [Build with Muse Voice Transcribe](https://dev.meta.ai/resources/blog/meet-muse-voice-transcribe-streaming-speech-to-text)
- The research blog names only six of the 25 in prose (Chinese, French, **Hindi**, Japanese, Spanish, Vietnamese) — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)
- Note on a secondary claim: an official-looking Meta Threads post states "native support for 5 Indian languages — Hindi, Bengali, Kannada, Marathi, Tamil, Telugu" — which **lists six while saying five**, an internal inconsistency. The docs page is the authoritative list — [Meta AI on Threads](https://www.threads.com/@meta.ai/post/DdCj7Koir4s/)

**`languageBias` parameter** [PRIMARY]:
- A language *hint*: "Steers recognition toward the languages you expect but does not force them. If omitted, the model detects the language." — [Speech to text](https://dev.meta.ai/docs/speech-to-text)

**Code-switching** [PRIMARY, claim only]:
- Docs: the model "supports it, but gives no details on accuracy or how it handles mixed-language audio" — [Speech to text](https://dev.meta.ai/docs/speech-to-text)
- Dev blog claims it can "transcribe multilingual speech and code-switching within the same conversation"; **no supporting evidence or test results appear** — [Build with Muse Voice Transcribe](https://dev.meta.ai/resources/blog/meet-muse-voice-transcribe-streaming-speech-to-text)
- Research blog describes code-switching **qualitatively, with a single Mandarin–English demo. No WER or numeric results** — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)

**Published WER — English aggregate only** [PRIMARY]:
- Streaming final-transcription WER: **3.1%**, lowest of eight systems compared; others 3.4%–4.0%. **The dataset is not named** — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)
- Adaptive-delay chart reports **~3.0% WER at 0.16s time-to-final-transcription**, on the Pareto frontier ahead of Soniox, Cartesia, ElevenLabs. The 3.0% vs 3.1% discrepancy is **unexplained by Meta** — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)
- Claims first place on the Artificial Analysis streaming speech-to-text leaderboard by WER, as of Sept 1 2026; the comparison tables are **images, not text** — [Build with Muse Voice Transcribe](https://dev.meta.ai/resources/blog/meet-muse-voice-transcribe-streaming-speech-to-text)
- **Per-language WER is NOT reported, and no Indian-language WER is reported, even though Hindi is a listed validated language** — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)
- Whisper is **not mentioned anywhere** in Meta's own comparison material — [Build with Muse Voice Transcribe](https://dev.meta.ai/resources/blog/meet-muse-voice-transcribe-streaming-speech-to-text)
- Accented English is **not evaluated**; "accents" appears only inside a transcript excerpt — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)

**The only Indian-language number found anywhere** [SECONDARY-GOOD — single-clip, self-labelled]:
- On one 5-minute Hindi–English mixed-script clip (MUCS tutorial): **Muse 54.21% vs Whisper 30.84% "WER-style"** — i.e. Muse was *much worse*. With Hindi+English `languageBias` set: **54.39% — no improvement**. On a 25-minute English aggregate: Muse 11.93% vs Whisper 15.34% (Muse better) — [Kingy.ai](https://kingy.ai/blog/meta-muse-voice-transcribe-guide/)
- Kingy explicitly cautions this is "one script-sensitive clip, not a general Hindi ranking," and attributes most of the gap to **Muse transliterating English software terms into Devanagari**, which standard token matching scores as errors — [Kingy.ai](https://kingy.ai/blog/meta-muse-voice-transcribe-guide/)
- Kingy rates the code-switching claim **"Medium-low" confidence**, labelled "Provider demos; no broad scores," and states: "Public launch material does not provide broad per-language, accent, dialect, or code-switch scores." — [Kingy.ai](https://kingy.ai/blog/meta-muse-voice-transcribe-guide/)

**Diarization** [PRIMARY]:
- Available via `DIARIZATION` mode; labels speakers `A`, `B`, etc. Labels are **session-scoped and not tied to a person across sessions** — [Speech to text](https://dev.meta.ai/docs/speech-to-text)
- `DIARIZATION` is **not tuned for low-latency voice commands**; use `ENDPOINTING` mode for those — [Speech to text](https://dev.meta.ai/docs/speech-to-text)
- Average DER across AMI-IHM, AMI-SDM, VoxConverse: **17.5%** (lowest of those compared; others 21.1%–28.6%). **No per-dataset DER given** — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)
- Claims support for **20+ speakers** and audio over one hour, with no accuracy figures for those conditions — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)

**Timestamps** [PRIMARY]:
- **Turn-level only** (`startMs`, `endMs`). **Word-level timestamps, confidence scores, sound-event detection, and emotion detection are NOT available** — [Speech to text](https://dev.meta.ai/docs/speech-to-text)

**Hard audio limits** [PRIMARY] — [Speech to text](https://dev.meta.ai/docs/speech-to-text):
- Streaming (WebSocket `wss://api.meta.ai/v1/asr/realtime`): raw signed 16-bit little-endian **mono PCM at 24 kHz** (`PCM_24KHZ`, preferred) or 16 kHz (`PCM_16KHZ`, server-resampled). **WAV/MP3/Opus containers must be decoded client-side first.**
- File upload (`POST /v1/asr/transcribe`, multipart): RIFF/WAVE, **mono, 16-bit integer PCM at 16 or 24 kHz**. Other formats must be converted (docs give an `ffmpeg` example).
- **Max request body 32 MB** (`413` if exceeded). **Max audio length 10 minutes per file** (`400` if exceeded).
- **Max realtime session 60 minutes** by default; **there is no resume token**, so you must reconnect before the limit.
- Streaming must be paced at ~real time, with backlog not exceeding **5 seconds**.
- Batch responses: JSON by default, or `text/event-stream` / `text/plain` via the `Accept` header.
- Architecture: autoregressive multimodal model from the Muse Spark family; audio processed in **80 ms chunks (12.5 Hz), each mapped to one soft token**. **No parameter count disclosed** — [research.meta.ai](https://research.meta.ai/blog/introducing-muse-voice-transcribe)

### Inferences
- The 10-minute-per-file cap plus mono-PCM-only ingest means any real pipeline needs an `ffmpeg` pre-processing stage and a chunking/stitching layer. That is non-trivial engineering time inside a 10-day window, and the absence of word-level timestamps makes clean chunk-boundary stitching harder (you can only align on turn boundaries).
- The gap between "Hindi is a verified language" and "no Hindi WER published" is the central risk for any Indic voice product. The one independent data point that exists suggests Devanagari-vs-Latin script output choice — not acoustic accuracy — may be the dominant error source for Hinglish, which standard WER punishes severely. **This means published-WER reasoning may not even be the right evaluation frame for Hinglish; script-normalised or transliteration-aware scoring would be needed.**
- `languageBias` failing to shift the Hinglish result (54.21% → 54.39%) in Kingy's test is weak but real evidence that the hint does not control output script. One clip is not enough to generalise.

### Gaps
- **No published WER for Hindi, Tamil, Bengali, Marathi, Telugu, or Kannada from any source** — primary or independent (beyond the single 5-min Hindi clip above).
- No published code-switching accuracy metric of any kind, for any language pair.
- No accented-English benchmark.
- The dataset underlying the 3.1% headline WER is unnamed, so the figure is not reproducible or comparable.
- Meta's own benchmark comparison tables are published as **images**, so the identities of the seven competing systems and their per-system scores could not be extracted from text.

---

## Q3: Muse Spark series — variant differences, modes, API vs open weights, multilingual benchmarks

### Takeaway
Version differences are documented but thin: **1.3 is the agentic/coding default, 1.2 is the version you must use for audio because "audio understanding in Muse Spark 1.3 is not fully supported,"** and 1.1 is the original. The marketing names **"Instant / Thinking / Contemplating"** are *app-surface* modes from the consumer launch; the **API exposes a `reasoning_effort` parameter (`minimal` → `max`/`xhigh`) instead**, and the two vocabularies are nowhere officially mapped onto each other. **There is no published list of supported languages for Muse Spark, and no Global-MMLU, MMMLU, Flores, Belebele, IndicGLUE or IndicMMLU results anywhere in Meta's primary material.**

### Cited Findings

**Version differences** [PRIMARY] — [Meta Model API: Models](https://dev.meta.ai/docs/models):
- **1.3**: latest, "tuned for agentic workflows," improved coding over 1.2, supports the full reasoning-effort range. Recommended for new work.
- **1.3 audio caveat (important):** "**Audio understanding in Muse Spark 1.3 is not fully supported.**" For audio, the docs recommend **1.2 or Muse Voice Transcribe**.
- **1.2**: previous version; **the recommended choice for audio**.
- **1.1**: original; Standard mode only; **not available in Muse Code**.
- Contributor variants exist for 1.2 and 1.3 only.
- The Muse Spark model page adds that 1.3 brings "max reasoning" for harder reasoning/agentic tasks and "improved real-world usability," but gives **no feature-level version comparison** — [Muse Spark](https://dev.meta.ai/models/muse-spark)

**Reasoning modes — the two vocabularies** [PRIMARY]:
- **API:** reasoning effort is set per request, e.g. `"reasoning": {"effort": "high"}`; Muse Spark's reasoning **carries across turns** — [Meta Model API docs](https://dev.meta.ai/docs)
- **API range:** `reasoning_effort` from `minimal` to `xhigh` on the 1.1 launch blog; "match effort to the task." **The named modes are not mentioned** — [Build with Muse Spark](https://dev.meta.ai/resources/blog/build-with-muse-spark)
- Models page gives Muse Wonder `"minimal"` to `"max"`, and `"max"` for Spark 1.3 Standard but **not** for Contributor — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- The Muse Spark model page uses reasoning-effort labels ("max" for 1.3, "xhigh" for 1.2) and **does not mention Instant/Thinking/Contemplating** — [Muse Spark](https://dev.meta.ai/models/muse-spark)
- **"Contemplating mode" is documented only on the consumer launch blog**: it "orchestrates multiple agents that reason in parallel," which Meta says lets Muse Spark compete with Gemini Deep Think and GPT Pro extreme-reasoning modes. **"Instant" and "Thinking" are NOT mentioned on that page either** — [ai.meta.com: Introducing Muse Spark](https://ai.meta.com/blog/introducing-muse-spark-msl/)
- At that launch, Contemplating mode was "rolling out gradually" in the **apps** (meta.ai and Meta AI app), while the **API was a private preview for select users** — [ai.meta.com](https://ai.meta.com/blog/introducing-muse-spark-msl/)

**Benchmarks that ARE published** [PRIMARY]:
- Contemplating mode: **Humanity's Last Exam 58%**, **FrontierScience Research 38%**. Meta claims it reaches Llama 4 Maverick capability with "over an order of magnitude less training compute," and admits gaps in long-horizon agentic systems and coding — [ai.meta.com](https://ai.meta.com/blog/introducing-muse-spark-msl/)
- The Muse Spark model page benchmark table covers **agentic, long-context and coding evals only** — GDPVal-AA v2, OSWorld 2.0, DeepSWE v1.1, Terminal-Bench 2.1. It includes 1.2 (xhigh) scores — [Muse Spark](https://dev.meta.ai/models/muse-spark)

**Multilingual benchmarks — the negative finding** [PRIMARY]:
- Muse Spark model page: **"No language list. Hindi, Tamil, Bengali, Marathi, and Telugu are not mentioned." No Global-MMLU / Flores / Belebele / MMMLU appear** — [Muse Spark](https://dev.meta.ai/models/muse-spark)
- `ai.meta.com` launch blog: **"Multilingual claims: None, and no language list is given."** — [ai.meta.com](https://ai.meta.com/blog/introducing-muse-spark-msl/)
- `dev.meta.ai` 1.1 launch blog: **"Multilingual claims or language lists: None."** — [Build with Muse Spark](https://dev.meta.ai/resources/blog/build-with-muse-spark)
- The Muse Spark 1.1 Evaluation Report: **Global-MMLU, MMMLU, Flores, Belebele, IndicGLUE and IndicMMLU do not appear. Per-language capability scores are not reported.** Multilingual refusal was tested on "a combined set of the most commonly used languages on Meta services," **but the report does not list them** — [Muse Spark 1.1 Evaluation Report](https://ai.meta.com/static-resource/muse-spark-1-1-evaluation-report/)
- Muse Spark Open HF card: described as multilingual, **trained on data from more than 100 languages, but gives no list**; states explicitly: **"The model has not been evaluated on all languages contained in the pre-training data."** No multilingual benchmarks reported — [HF: Muse-Spark-Open-816B-A42B](https://huggingface.co/meta-models/Muse-Spark-Open-816B-A42B)

**Native multimodal perception** [PRIMARY]:
- "Native multimodal perception. Image, audio, video and document understanding in a single call." Vision input accepted as **base64 data URL or public URL** — [Build with Muse Spark](https://dev.meta.ai/resources/blog/build-with-muse-spark)

**API vs open weights** [PRIMARY]:
- API-hosted: `muse-spark-1.3`, `1.2`, `1.1`, plus `-contributor` variants for 1.3/1.2 — [Meta Model API docs](https://dev.meta.ai/docs)
- **Muse Spark Open and Muse Glimmer are open-weight: "You download and self-host them, so they don't have Model API IDs."** — [Meta Model API docs](https://dev.meta.ai/docs)
- Muse Spark Open architecture: **816B total / 42B active**, MoE Transformer with a Perception Encoder; 62 layers, 256 experts with 8 activated per token, hidden dim 8192; grouped-query attention 128 query heads / 16 KV heads; **1M-token context at inference** — [HF: Muse-Spark-Open-816B-A42B](https://huggingface.co/meta-models/Muse-Spark-Open-816B-A42B)
- Muse Spark Open license: **Muse Model License** (version "Muse Spark Open," released **October 15, 2026**). Requires displaying "Built with Muse Spark Open"; a separate agreement is needed for certain Model-as-a-Service offerings **above $20M annual gross revenue**; incorporates a Usage Policy — [HF: Muse-Spark-Open-816B-A42B](https://huggingface.co/meta-models/Muse-Spark-Open-816B-A42B)
- Muse Spark Open training data "includes text, images, audio, and video," drawn from public sources, web crawls, third-party sources, **Meta's products and services**, and synthetic data — [HF: Muse-Spark-Open-816B-A42B](https://huggingface.co/meta-models/Muse-Spark-Open-816B-A42B)
- Open-weights release context confirmed independently by a primary Meta-affiliated channel — [PyTorch blog: Muse Spark Is Now an Open-Weights Model](https://pytorch.org/blog/muse-spark-open/)

### Inferences
- **The "Instant / Thinking / Contemplating" naming is a consumer-app surface, not an API contract.** A developer should architect against `reasoning_effort` values, not those names. Treating them as API modes would be a design error. I found **no primary source that maps the three names onto effort values** — any such mapping circulating on aggregator sites is unverified.
- The 1.3 audio caveat is the most consequential undocumented-in-marketing fact for a multimodal build: a developer who defaults to "newest version" will get degraded audio. The documented path is **Spark 1.2 for audio-in-context, or Voice Transcribe for transcription**.
- 816B total / 42B active means Muse Spark Open is **not** self-hostable on a hackathon budget; it is a cloud-provider-or-nothing model. Glimmer is the only genuinely local option.
- Meta's own choice to publish multilingual **refusal/safety** rates while publishing **no multilingual capability** scores is itself informative: it suggests multilingual capability was not a launch-claim priority, and a developer should not assume parity with English.

### Gaps
- No official mapping of Instant/Thinking/Contemplating → `reasoning_effort`.
- No published supported-language list for any Muse Spark variant (API or open).
- No Global-MMLU / MMMLU / Flores / Belebele / IndicGLUE / IndicMMLU results for any Muse model, from Meta or (as far as I found) any independent evaluator.
- `dev.meta.ai/models/muse-spark-1.2` returned a client error and could not be fetched, so any 1.2-specific documentation there is unverified.
- Muse Spark 1.1 Evaluation Report was **truncated before Section 5 (General Capabilities)**; multilingual capability benchmarks, if any exist, could be in that unretrieved section. Flagging as unresolved rather than absent.

---

## Q4: Published evidence on CODE-MIXED text — Hinglish, Romanized Hindi, Devanagari

### Takeaway
**This is the clearest negative finding of the research. For the Muse family there is essentially no capability evidence on code-mixed text — only a single safety table.** The one place Romanized Hindi appears in Meta's primary material is an *interpreter-abuse refusal* table in the Spark 1.1 evaluation report, which measures safety, not comprehension or generation quality. Independent code-mixed benchmarks exist and are rigorous, but **they evaluate Llama 3.x, not Llama 4 and not Muse** — and they show a consistent accuracy drop as code-mixing intensity rises.

### Cited Findings

**The only Meta primary data touching Romanized Hindi** [PRIMARY — safety metric, NOT capability]:
- The Muse Spark 1.1 Evaluation Report's **interpreter-abuse table (Figure 16)** is the only place specific languages are named. It covers English, Arabic, French, **Hindi**, **Hindi (Romanized)**, Indonesian, Portuguese, Spanish. Attack success rate %, lower is better — [Muse Spark 1.1 Evaluation Report](https://ai.meta.com/static-resource/muse-spark-1-1-evaluation-report/):

| Model | EN | AR | FR | **HI** | **HI (Rom.)** | ID | PT | ES |
|---|---|---|---|---|---|---|---|---|
| Muse Spark 1.1 | 1.96 | 0.80 | 1.00 | **0.60** | **1.16** | 1.04 | 1.24 | 1.00 |
| Muse Spark 1.0 | 0.40 | 0.00 | 0.00 | 0.00 | 0.00 | 0.10 | 0.20 | 0.00 |
| GPT-5.5 | 0.00 | 0.00 | 0.00 | 0.60 | 0.00 | 0.00 | 0.00 | 0.00 |
| Claude Opus 4.8 | 3.40 | 0.40 | 0.80 | 1.60 | 1.20 | 0.60 | 0.80 | 1.40 |
| Gemini 3.1 Pro | 6.80 | 1.00 | 3.00 | 1.20 | 1.00 | 1.80 | 3.00 | 2.80 |

- Meta concludes robustness across languages is "similar to Muse Spark 1.0" — [Muse Spark 1.1 Evaluation Report](https://ai.meta.com/static-resource/muse-spark-1-1-evaluation-report/)
- **"Code-mixed evaluation: Not mentioned. Romanized Hindi is the only non-native-script evaluation."** Tamil, Bengali, Marathi and Telugu are **not mentioned anywhere** in the report — [Muse Spark 1.1 Evaluation Report](https://ai.meta.com/static-resource/muse-spark-1-1-evaluation-report/)
- Multilingual refusal (BioTIER v1): Muse Spark 1.1 System scores 97.7% English / 96.0% multilingual (blended) / 96.8% adversarial. The "multilingual" column is a **blend across unnamed languages** — [Muse Spark 1.1 Evaluation Report](https://ai.meta.com/static-resource/muse-spark-1-1-evaluation-report/)
- Muse Spark Open HF card: **"Code-mixed or script handling: Not addressed."** — [HF: Muse-Spark-Open-816B-A42B](https://huggingface.co/meta-models/Muse-Spark-Open-816B-A42B)

**Independent code-mixed benchmark — ABOUT LLAMA 3.x, NOT Llama 4, NOT Muse** [clearly labelled as older models]:
- **Indi-RomCoM** evaluates Romanized (Latin-script) code-mixed Indic-English instructions in **Hindi, Bengali, Gujarati, Tamil** across seven tasks (Word Analogy, Question Decomposition, GSM8K-derived Math Reasoning, Paraphrase, Sentiment, NLI, Toxicity): 700 English base instances × 4 languages × 3 code-mixing intensities (25/50/75%) = 8,400 instances — [arXiv: Indi-RomCoM](https://arxiv.org/html/2606.30790)
- **The paper's visible model list does NOT include Muse or Llama 4** — it refers to the "LLaMa3 series." Models evaluated include GPT-3.5-Turbo, Gemini 3.5 Flash, Claude Opus 4.6, LLaMA-3.2-1B, LLaMA-3.1-8B, LLaMA-3.1-70B, Mistral-3B/7B — [arXiv: Indi-RomCoM](https://arxiv.org/html/2606.30790)
- Mean task accuracy (%) by code-mixing intensity — [arXiv: Indi-RomCoM](https://arxiv.org/html/2606.30790):

| Model | English baseline | 25% CM | 50% CM | 75% CM | Drop at 75% |
|---|---|---|---|---|---|
| Claude Opus 4.6 | 68.7 ± 22.1 | 63.4 | 61.9 | 61.2 | 7.5 |
| Gemini 3.5 Flash | 61.4 ± 25.9 | 56.2 | 54.8 | 54.1 | 7.3 |
| **LLaMA-3.1-70B** | **54.6 ± 32.3** | 51.5 | 50.6 | **50.5** | **4.0** |
| GPT-3.5-Turbo | 52.3 ± 28.4 | 46.1 | 44.8 | 44.3 | 8.0 |
| **LLaMA-3.1-8B** | **50.1 ± 33.6** | 48.6 | 47.8 | **47.9** | **2.2** |
| Mistral-7B | 49.7 ± 35.7 | 44.3 | 43.6 | 43.3 | n/s |
| Mistral-3B | 37.2 ± 12.7 | 24.8 | 22.1 | 19.7 | 17.5 |
| **LLaMA-3.2-1B** | **24.3 ± 16.9** | 11.5 | 11.6 | **11.6** | **12.8** |

- Note the pattern: the Llama 3.x models show **small absolute drops (2.2–4.0 points) but from low English baselines**, whereas the 1B model collapses (24.3 → 11.6). Large standard deviations (±32–36 for Llama) indicate **highly uneven performance across tasks** — [arXiv: Indi-RomCoM](https://arxiv.org/html/2606.30790)
- Related independent work exists on Romanized Indic modelling and on native-vs-Roman script gaps — [arXiv: Modeling Romanized Hindi and Bengali](https://arxiv.org/pdf/2511.22769); [arXiv: Script Gap — LLM Triage on Indian Languages in Native vs Roman Scripts](https://arxiv.org/html/2512.10780v1)

### Inferences
- **A refusal-rate table is not a capability measurement.** The HI / HI-Romanized columns in Meta's report tell you the model *resists misuse* comparably in those languages; they say nothing about whether it understands Hinglish instructions or generates fluent Hinglish. Any claim that Meta "benchmarked Hindi" rests on a category error, and the distinction should be stated plainly in the final report.
- The Indi-RomCoM small-absolute-drop-from-low-baseline pattern for Llama 3.x is a **floor effect, not robustness**: a model already near chance on some tasks cannot drop much further. Reading "Llama is robust to code-mixing" off the 2.2–4.0 drop column would over-claim.
- Extrapolating any of this to Muse is unsupported — Muse is a different architecture (MoE + Perception Encoder) with a different, undisclosed data mix. The honest position is **unknown**.

### Gaps
- **No code-mixed capability evaluation of any Muse model exists, from Meta or any third party I could find.** This is the headline gap.
- No Llama 4 code-mixed results found (the one rigorous benchmark covers Llama 3.x only).
- Indi-RomCoM's Table 6 was truncated in the HTML render (8 of a stated 19 models visible), and it reports **no per-language accuracy breakdown** in the retrievable portion — so Hindi-specific vs Tamil-specific figures are unavailable.
- No evaluation found of Devanagari-script *generation* quality (as opposed to recognition) for any Muse model.

---

## Q5: SAM 3.1 — what it does, use cases, and limits

### Takeaway
SAM 3.1 is a **text-promptable open-vocabulary detector + segmenter + tracker** that returns boxes and pixel-precise masks for images and video in one call, zero-shot. It is remarkably cheap ($0.20 per 1,000 video frames). But Meta's own page documents **almost none of the operational limits** — no max resolution, no max video length, no benchmarks, no failure modes — and the prompt interface is documented as **noun-phrase text only**, with point/box/mask prompts not mentioned.

### Cited Findings
- SAM 3.1 "detects, segments, and tracks objects in images and video from a text prompt," and is "served on inference built specifically for its **DETR architecture**" — [SAM 3.1](https://dev.meta.ai/models/sam-3-1)
- Does detection, **pixel-precise segmentation masks, and identity-preserving tracking through video, all in one call**; returns **bounding boxes along with masks** — [SAM 3.1](https://dev.meta.ai/models/sam-3-1)
- Text prompting: give "**a short noun phrase naming one thing**," and it returns a box and mask **for every match** — [SAM 3.1](https://dev.meta.ai/models/sam-3-1)
- **Point, box, and mask prompts are NOT mentioned** on the page — [SAM 3.1](https://dev.meta.ai/models/sam-3-1)
- Claims **zero-shot operation with no training data, fine-tuning, or extra models**; shares keys, docs and billing with other Meta models; no hosting or tuning required — [SAM 3.1](https://dev.meta.ai/models/sam-3-1)
- Input per the models table: **text prompt + image + video** → output **boxes and masks** — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- Docs overview positions it for "finding and outlining objects in images and video" — [Meta Model API overview](https://dev.meta.ai/docs/overview)
- Pricing: **$2.50 per 1,000 images; $0.20 per 1,000 video frames**; **minimum 50 frames billed per video request** — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Rate limit: **3,000 RPM, no TPM limit** — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- **Explicitly not stated on the official page: max resolution, max frames or video length, output format details beyond boxes and masks, documented limits and failure modes, benchmarks.** The architecture section on the page **has no content** — [SAM 3.1](https://dev.meta.ai/models/sam-3-1)

### Inferences
- Realistic use cases consistent with what *is* documented: open-vocabulary object counting/inventory from photos or video; region extraction to crop-and-feed into Spark for VLM reasoning; tracking a named object across a clip; auto-masking for image editing hand-off to Muse Image. All of these follow directly from "noun phrase in → boxes + masks + track IDs out."
- Documented limits that follow from the interface itself: it is **one noun phrase per call**, so multi-class scenes need multiple calls (and multiple billings); it has **no relational or compositional prompting** documented (nothing supports "the cup *left of* the laptop"); and with no documented point/box refinement, there is **no documented way to disambiguate a wrong mask** — you can only rephrase the noun.
- The per-frame price is ~12.5x cheaper than per-image, so sending a burst of stills as a "video" is dramatically cheaper than as images — but the 50-frame minimum means short clips are overcharged. Cost-optimal batching sits at ≥50 frames per request.
- Video pricing being per *frame* rather than per second means **your frame-sampling rate is your cost dial**, and it is entirely under the developer's control. This is the single biggest cost lever in a video workload.

### Gaps
- No max resolution, max video length, or max frames per request documented.
- No benchmark numbers (no mAP, no J&F, no comparison to SAM 2 or SAM 3) on the official page.
- No documented failure modes, confidence scores, or mask-quality signals.
- Whether the API accepts point/box/mask prompts (as earlier SAM generations did) is **unknown** — absent from the page, not explicitly denied.
- Frame-sampling behaviour for video (does the API sample, or does it process every frame submitted?) is undocumented, which materially affects cost prediction.

---

## Q6: Muse Image and Muse Wonder — what they generate and their constraints

### Takeaway
**Muse Image** is a reasoning-then-rendering generate-and-edit image model at a flat $0.01/image — but its official page is largely marketing, with **no documented resolutions, aspect ratios, content restrictions, latency, or per-request image limits**, and it carries a **hard geographic block in India** (see Q7). **Muse Wonder** is the frontier coding/agent/computer-use tier at $4.00/$20.00 per M tokens — 3.2x/4.7x the price of Spark — and its most capable variant, **Muse Wonder Full, is not available via the API at all**.

### Cited Findings

**Muse Image**:
- An image model that "reasons before it renders"; creates **and edits** complex images in one model — [Muse Image](https://dev.meta.ai/models/muse-image)
- Editing is "designed to change exactly what you specify and leave everything else untouched" — [Muse Image](https://dev.meta.ai/models/muse-image)
- Inputs: text descriptions **and existing images** for editing; can pull references from the web and anchor a series of generations on "a small set of references" — **reference-image input limits are not documented** — [Muse Image](https://dev.meta.ai/models/muse-image)
- Models table confirms: input **text + image** → output **image** — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- Price **$0.01 per generated image**, with search grounding and reasoning **included** — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits); [Muse Image](https://dev.meta.ai/models/muse-image)
- Rate limit **150 RPM**, no TPM — [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- **Not specified on the official page: resolutions, aspect ratios, content restrictions, max images per request, rate limits, latency.** Text rendering appears only as a demo reference to "precise typography" with no reliability claim. The page is characterised as "mainly marketing material" — [Muse Image](https://dev.meta.ai/models/muse-image)

**Muse Wonder**:
- "Frontier coding, long-running agents, and advanced computer use" — Meta's frontier series — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)
- **Two variants: Muse Wonder** (via Meta Model API and Muse Code) and **Muse Wonder Full** (most advanced; **subscription-only in Muse Code, NOT available through the API**) — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)
- Capabilities: complex engineering in large repos with parallel multi-file edits; plans multi-step tasks, **delegates to sub-agents, calls tools in parallel, recovers from errors**; operates **browsers, desktop and mobile apps**; keeps **memory across conversations** — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)
- Context window **1M tokens for both variants** — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)
- Pricing (API variant): input $4.00, cached input $0.20, output $20.00 per M tokens — [Muse Wonder](https://dev.meta.ai/models/muse-wonder); [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits)
- Muse Wonder Full has **no API pricing**; included in Muse Code plans: **Power Usage $50/month (20x Everyday usage)**, **Maximum Usage $200/month (80x Everyday usage)**, both with early feature access and higher file-upload limits — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)
- Models table: input text, image, video, audio, PDF → output text; tool calling and structured output supported; reasoning effort `"minimal"` to `"max"`; status **preview** — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- Tool calling supported **including parallel calls**; search grounding via a single added tool; OpenAI-SDK-compatible client — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)
- Claims Muse Wonder Full "tops agentic and coding benchmarks, including **DeepSWE and SWE-Marathon** in independent third-party runs," compared against Claude Fable 5.1, GPT-6 Astra and Claude Opus 5.5 — **but the comparison table's values could not be reliably mapped to each model from the page text**, so no per-model scores are reported here — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)
- **Not documented for Muse Wonder: supported languages, max output tokens, explicit structured-output detail** — [Muse Wonder](https://dev.meta.ai/models/muse-wonder)

### Inferences
- Muse Image's flat $0.01/image with reasoning and search grounding bundled in is unusually simple to budget against — 1,000 images = $10, with no token accounting. But the 150 RPM ceiling caps throughput at ~9,000 images/hour, and the undocumented latency makes interactive UX unpredictable.
- For a 10-day prototype, **Muse Wonder is hard to justify over Spark on price** (4.7x on output) unless the app itself needs long-horizon agentic coding or computer use. Spark 1.3 covers the same modalities with the same context window.
- Muse Wonder Full being API-inaccessible means any benchmark claim about "Muse Wonder Full topping DeepSWE" is **irrelevant to an API-built application**. Conflating the two tiers would be a positioning error.

### Gaps
- Muse Image: resolutions, aspect ratios, max reference images, content policy specifics, latency, and context window all undocumented.
- Muse Wonder: per-model benchmark scores unreadable from the page (table rendering); language support and max output tokens undocumented.
- Muse Wonder's "preview" status implies possible instability or changing limits, but no deprecation or stability policy was found.

---

## Q7: Practical gotchas of the Meta Model API — auth, SDKs, compatibility, regional availability, structured output, tool use

### Takeaway
Integration is genuinely low-friction: one bearer token, base URL `https://api.meta.ai/v1`, and **drop-in compatibility with both the OpenAI SDK and the Anthropic SDK** plus three request formats. Structured output and parallel tool calling are documented as supported. **The decisive gotcha for an India-based developer: Muse Image is explicitly prohibited from being accessed from, or made available to end users in, India** — while the rest of the lineup carries no India-specific restriction. There is also **no documented country allow-list at all**, so general availability in India is officially unconfirmed.

### Cited Findings

**Authentication and endpoints** [PRIMARY]:
- Base URL **`https://api.meta.ai/v1`** for all hosted models; realtime ASR at **`wss://api.meta.ai/v1/asr/realtime`** — [Meta Model API overview](https://dev.meta.ai/docs/overview)
- Bearer token: `Authorization: Bearer $MODEL_API_KEY` — [Meta Model API overview](https://dev.meta.ai/docs/overview); [Meta Model API docs](https://dev.meta.ai/docs)
- In Muse Code, first run offers "a browser sign-in or paste an API key" — [Meta Model API overview](https://dev.meta.ai/docs/overview)
- **How to create a key is not explained in the docs** — it points to the Dashboard and Help Center — [Meta Model API overview](https://dev.meta.ai/docs/overview); [Meta Model API docs](https://dev.meta.ai/docs)

**SDKs and compatibility** [PRIMARY]:
- "Drop-in compatible with the **OpenAI SDK**, the **Anthropic SDK**, and OpenAI-compatible clients and agent CLIs" — [Meta Model API docs](https://dev.meta.ai/docs)
- **Three request formats: Responses API, Chat Completions API (OpenAI-compatible), and Messages API (Anthropic Messages-compatible)** — [Meta Model API docs](https://dev.meta.ai/docs); [Meta Model API overview](https://dev.meta.ai/docs/overview)
- To migrate: "set the client's base URL and add your key, and the rest of your code can stay the same" — [Meta Model API overview](https://dev.meta.ai/docs/overview)
- Quickstart references **Python and TypeScript** examples, but **no Meta-published SDK package names are listed** on either page — you use OpenAI's or Anthropic's SDK pointed at Meta's base URL — [Meta Model API docs](https://dev.meta.ai/docs); [Meta Model API overview](https://dev.meta.ai/docs/overview)

**Structured output and tool use** [PRIMARY]:
- Structured output returns "valid JSON that matches your schema"; **the JSON-schema format itself is not detailed** — [Meta Model API docs](https://dev.meta.ai/docs)
- Tool calling supported; **Muse Spark has parallel tool calls and streamed tool-call arguments**; tool calling and "tool search" are documented as separate capabilities — [Meta Model API docs](https://dev.meta.ai/docs)
- Models table confirms tool calling + structured output for Muse Wonder and Spark 1.1/1.2/1.3 — [Meta Model API: Models](https://dev.meta.ai/docs/models)
- Reasoning effort settable per request; **Spark's reasoning carries across turns** — [Meta Model API docs](https://dev.meta.ai/docs)

**Regional availability** [PRIMARY — this is the key gotcha]:
- **"The only India-specific restriction is for Muse Image, which you may not access from India or make available to End Users there."** No other model is restricted for India — [Model Designation and Geographic Use Policy](https://dev.meta.ai/legal/geographic-use-policy)
- **Restricted Territories (no access at all):** Afghanistan, Belarus, Cambodia, China (incl. Hong Kong and Macau), Cuba, Eritrea, Ethiopia, Haiti, Iran, Iraq, Libya, Myanmar, Nicaragua, North Korea, **Pakistan**, Russia, Somalia, Syria, Crimea/Donetsk/Luhansk regions of Ukraine, Venezuela, Western Sahara — [Geographic Use Policy](https://dev.meta.ai/legal/geographic-use-policy)
- "The Services are not available everywhere. Meta decides where access is enabled and can change that at any time. You may only access the Services where Meta has enabled them, and you may not circumvent geographic controls." — [Geographic Use Policy](https://dev.meta.ai/legal/geographic-use-policy)
- **The policy does NOT list which countries are generally supported**, so it "does not confirm whether India is included in general availability." It points to separate informational resources that are "not guaranteed and may not reflect real-time availability" — [Geographic Use Policy](https://dev.meta.ai/legal/geographic-use-policy)
- For any jurisdiction not listed, "you must determine for yourself whether use is permitted"; user bears OFAC/EU/UK/UN sanctions and export-law compliance — [Geographic Use Policy](https://dev.meta.ai/legal/geographic-use-policy)
- If Meta restricts a jurisdiction where you are actively using the Services, it "will use commercially reasonable efforts to give prior notice, except where immediate action is required" — [Geographic Use Policy](https://dev.meta.ai/legal/geographic-use-policy)
- Separately, a Meta Threads post states "the new Muse family is live in India," with Muse Spark rolling out in Meta's apps with Hindi and Bengali voice support — note this concerns **consumer app availability**, not API entitlement, and Threads is a lower-reliability channel than the legal policy — [Meta AI on Threads](https://www.threads.com/@meta.ai/post/DdCj7Koir4s/)

**Billing** [PRIMARY]:
- All hosted models are pay-as-you-go; the docs **do not describe billing setup steps** and defer to the Help Center. **No billing/currency requirements are stated** in the geographic policy — [Meta Model API overview](https://dev.meta.ai/docs/overview); [Geographic Use Policy](https://dev.meta.ai/legal/geographic-use-policy)

### Inferences
- **The Muse Image India restriction is a hard architectural constraint, not a soft caveat.** An India-based developer building an image-generation feature on Muse Image would be in breach of the geographic policy both for their own access and for serving Indian end users. Any multimodal architecture for an India-based builder should treat Muse Image as **unavailable** and plan a substitute generator — while Spark/Wonder/Voice Transcribe/SAM remain unrestricted for India.
- Dual OpenAI+Anthropic SDK compatibility materially de-risks a 10-day build: existing code and agent frameworks port by changing a base URL, and provider lock-in is low in both directions.
- The absence of a published country allow-list combined with "Meta decides where access is enabled and can change that at any time" means availability should be **verified empirically by provisioning a key before committing the architecture**, not assumed from documentation.
- Pakistan being a Restricted Territory alongside China and Russia is worth noting for any South-Asia-regional product scope.

### Gaps
- **No positive country allow-list published** — India's general API availability is officially unconfirmed (only the Muse Image prohibition is explicit).
- No Meta-published Python/TypeScript SDK package names found.
- No documented JSON-schema dialect/constraints for structured output.
- API key creation flow undocumented in public docs.
- Billing setup, accepted currencies, and whether Indian payment instruments are supported: all undocumented.
- No published uptime/SLA, deprecation policy, or stability guarantee for `preview`-status models (Muse Wonder).

---

## Q8: How far does $150 of credit go for a multimodal workload?

### Takeaway
**Important caveat first: I found no documentation of any $150 (or any) free credit grant from Meta.** Treating $150 purely as a budget: it is a *lot* for this lineup — audio and segmentation are extremely cheap ($0.18/hour and $0.20/1k frames), so the binding constraint on a multimodal workload is almost always **Muse Spark text tokens for reasoning over the extracted content**, not the perception models. The genuinely unquantifiable piece is image/video→token conversion, which Meta does not document.

### Cited Findings (unit costs used, all [PRIMARY] from [Pricing and rate limits](https://dev.meta.ai/docs/pricing-rate-limits))
- Muse Voice Transcribe: **$0.18/hour**, metered in whole seconds rounded down
- SAM 3.1 video: **$0.20 per 1,000 frames**, minimum 50 frames billed per request
- SAM 3.1 images: **$2.50 per 1,000 images**
- Muse Image: **$0.01 per image**, 150 RPM
- Muse Spark Standard: **$1.25/M input, $4.25/M output**; cached input $0.15/M
- Muse Spark Contributor: **$0.10/M input, $0.20/M output**; cached input $0.002/M
- Muse Wonder: **$4.00/M input, $20.00/M output**
- Web search grounding: **$2.50 per 1,000 queries**, additive to token costs
- ASR hard caps that shape batch design: **10 min max per file, 32 MB max body, 60 min max realtime session with no resume token** — [Speech to text](https://dev.meta.ai/docs/speech-to-text)

### Inferences (arithmetic from the documented unit prices above — these are my calculations, not Meta figures)
- **Audio is effectively free at prototype scale.** $0.18/hour means **1,000 ten-minute recordings = 166.7 hours = ~$30**. Put differently, **$1 buys ~5.5 hours of transcription**, and the entire $150 would buy **~833 hours** of audio if spent on ASR alone.
- **SAM video is the cheapest perception unit.** At $0.20/1,000 frames, sampling 1 frame/second from a 60-second clip (60 frames, above the 50-frame minimum) costs **$0.012 per clip** — so **1,000 video clips ≈ $12**. Sampling 2 fps doubles that. Frame-sampling rate is the dominant cost dial.
- **SAM images cost 12.5x more per unit than video frames** ($2.50 vs $0.20 per 1,000), so routing still-image batches through the video endpoint (≥50 frames per request to avoid the minimum penalty) is materially cheaper where the use case permits.
- **Muse Image: $150 = 15,000 images** at $0.01 — but this is moot for an India-based developer given the geographic prohibition (Q7).
- **Spark text reasoning is the real budget consumer.** At Standard pricing, a request with ~4,000 input tokens and ~1,000 output tokens costs 4,000×$1.25/1M + 1,000×$4.25/1M = **$0.005 + $0.00425 ≈ $0.0093**, so **~16,000 such calls per $150**. On Contributor pricing the same call costs **$0.0006**, i.e. **~250,000 calls per $150** — but at 150 RPM.
- **Illustrative combined pipeline** (per item: 10 min audio transcription + 60-frame SAM pass + one ~4k-in/1k-out Spark reasoning call): ≈ $0.03 (ASR) + $0.012 (SAM) + $0.0093 (Spark Standard) ≈ **$0.051 per item → ~2,900 items for $150**. On Contributor Spark pricing the Spark component drops to ~$0.0006, giving ≈ **$0.043/item → ~3,500 items**. **These figures exclude the undocumented token cost of passing images/video into Spark's context, which could dominate everything above.**
- **The $150 budget is therefore not the limiting factor for a 10-day prototype — engineering time is.** The mono-PCM-only ingest, 10-minute file cap, no-resume-token sessions, turn-level-only timestamps, and one-noun-phrase-per-SAM-call all consume developer days, not dollars.
- **Cost-control levers, ranked:** (1) frame-sampling rate for SAM/video; (2) Contributor tier for Spark if the training trade-off is acceptable (~12x–21x saving); (3) prompt caching (cached input is 8.3x cheaper on Standard, 50x on Contributor); (4) avoiding Muse Wonder unless agentic coding is genuinely required; (5) avoiding search grounding, which at $2.50/1k queries can exceed token costs in search-heavy loops.

### Gaps
- **No documentation found of a $150 free-credit offer, or of any free/trial credits at all.** The premise could not be verified from Meta sources; the final report should not assert that Meta grants $150.
- **How images and video are converted into billable tokens is undocumented** — `dev.meta.ai/docs/models` explicitly leaves this blank, and both `/docs/vision` and a token-counting page could not be retrieved (client errors / not linked in retrieved text). **This is the single largest hole in any cost model for a multimodal workload**, because video-into-context could plausibly dwarf all per-unit perception costs.
- No documented max video length or resolution for Spark/Wonder video input, so per-request input size cannot be bounded.
- SAM's video frame-sampling behaviour (does it process every submitted frame?) is undocumented, so SAM video cost is predictable only if the developer controls sampling client-side.
- Latency is undocumented for every model except ASR (0.16s time-to-final-transcription), so throughput-per-day within 10 days cannot be estimated reliably.
