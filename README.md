# QuietDigest

**Less scrolling. Original words.** A private, browser-local reading shortcut
for handoffs, meeting notes, and threads you keep putting off.

New project started October 23, 2026, inside Hacktoberfest Week 3's
**Slow and Steady** window. It is not a resubmission of ScopeMatch.

## Try it

**[Open the live demo](https://rahulatrkm.github.io/quietdigest-hf26/)**

Serve this folder with an existing static HTTP server, for example:

```powershell
python -m http.server 8767 --bind 127.0.0.1
```

Open `http://127.0.0.1:8767`. No build, account, API key, package installation,
or hosted inference service is required. A recent browser supporting WebAssembly
and `Intl.Segmenter` is needed.

1. Paste 40-16,000 characters or open a UTF-8 text file, up to 48 KB.
2. Choose decisions/next steps, risks/blockers, or a general overview.
3. Request a three-, five-, or seven-quote digest.
4. Review original source sentences before downloading the plain-text digest.

## What the AI does

The actual quantized `Xenova/all-MiniLM-L6-v2` model runs locally through
Transformers.js 3.8.1 and ONNX Runtime Web. Model revision:
`751bff37182d3f1213fa05d7196b954e230abad9`.

Each source sentence and the selected focus become normalized 384-dimensional
embeddings. Greedy selection ranks a candidate by:

```text
0.8 * cosine(candidate, focus) - 0.2 * highest cosine(candidate, selected quotes)
```

This rewards relevance while discouraging repetition. Selected quotes are
displayed in source order, unchanged, with source and line references.
Scores are similarities, not probabilities or confidence.

This is **extractive selection**, not generative summarization, reliable
requirements analysis, or a complete record. A useful outlier, crucial context,
or a negation can be missed. Do not automate decisions or replies from this
prototype.

## Honest bounds

- 3-80 distinct sentences; identical text ignoring case/whitespace is deduplicated.
- Fragments shorter than 12 characters are omitted and their count is displayed.
- Actual tokenizer output is checked before inference. A sentence longer than
  256 tokens is rejected instead of silently truncated.
- Invalid settings, malformed embeddings, unsupported browsers, and asset
  failures produce visible errors; there are no fake AI fallback results.
- Changing the input, focus, or digest size invalidates the earlier result.
- The digest may contain fewer than the requested number of quotes when fewer
  distinct sentences exist; the exact scope and limitation are displayed.

## Privacy and cost

Notes stay in tab memory, not local storage, analytics, or a model API.
Opening a local file never uploads it. Explicitly downloading a digest creates
a file on the user's device containing the selected quotes.

The first analysis downloads public library, runtime, and model assets from
jsDelivr and Hugging Face, often tens of MB. Those providers see ordinary
download metadata. A cold start needs connectivity; after model load, inference
can run in the open tab offline. A fresh reload is not an offline guarantee.

No paid AI API, credit card, trial, auto-renewing service, database, or cloud
deployment is part of this app. Audit and self-host the public dependencies if
your organization requires tighter supply-chain controls.

## Verified browser behavior

These checks used the actual pinned model, not mocked embeddings:

| Check | Observed result |
| --- | --- |
| Default sample | Exactly three unchanged quotes with valid source references, from ten distinct sentences |
| Risk focus, after putting the loaded tab offline | Real local inference selected two actual blocker/risk sentences among its three quotes |
| Tokenizer-measured sentence lengths | 256 tokens accepted; 257 rejected explicitly with no digest |
| Distinct sentence count | 80 accepted; 81 rejected |
| Character minimum | 40 accepted; 39 rejected |
| Invalid UTF-8 or oversized files | Explicit error; existing notes preserved and stale digest invalidated |
| Exact repeats and short fragments | Counts displayed; requested seven quotes honestly limited to three available |
| Export payload | Plain-text blob contained original quotes, source references, model revision, and the human-review warning |
| Blocked public library download | Visible failure; no replacement summary; normal inference recovered after reload |
| Unsupported focus mode | Explicit rejection, not a silent default |
| HTML-like source text | Preserved as literal text, with no generated image/markup |
| Actual 390px / 1440px viewports | One mobile column without horizontal overflow / two desktop columns |

Warm-model inference took about 0.14-0.20 seconds on the validation machine.
That excludes model download/initialization and is not a performance guarantee.
The export payload was checked directly; browser download handling may vary.

## Attribution

Original app: MIT, copyright 2026 Rahul.

- [Model](https://huggingface.co/Xenova/all-MiniLM-L6-v2): Apache-2.0, derived
  from [sentence-transformers/all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2).
- [Transformers.js](https://github.com/huggingface/transformers.js): Apache-2.0.
- [ONNX Runtime](https://github.com/microsoft/onnxruntime): MIT.

The public-library/model loading approach was previously exercised in
[ScopeMatch](https://github.com/rahulatrkm/scopematch-hacktoberfest-2026).
QuietDigest is a new, separately implemented extractive reading tool with
sentence segmentation, real tokenizer bounds, focus selection, novelty ranking,
traceable quotes, and local-file input, not a renamed service matcher.

An AI assistant using Copilot SDK in VS Code assisted with implementation and
validation. No private agent session, customer data, testimonials, revenue, or
prize-win claims are included.
