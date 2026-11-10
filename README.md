# Gallery

SEO and AI-search tools by Jason McGovern, built as Google Colab notebooks. Each one runs in the browser: open it in Colab, fill in the config cell, and run the cells top to bottom.

Nothing in these notebooks is stored with a key. Wherever a tool needs an API key, it asks for it at runtime or reads it from a file you upload to the Colab session.

| Tool | What it does | Needs |
|---|---|---|
| [Technical SEO + AI-readiness audit](#technical-seo--ai-readiness-audit) | Crawls a site and scores traditional SEO and AI-search readiness, exported to Excel | Optional PageSpeed API key |
| [Keyword-to-content match analyzer](#keyword-to-content-match-analyzer) | Decides, per keyword, whether an existing page should target it, needs optimizing, or whether new content is needed | OpenAI API key |
| [AI brand sentiment](#ai-brand-sentiment) | Measures how positively ChatGPT talks about a brand, tracked over time | OpenAI API key |
| [AI brand visibility](#ai-brand-visibility) | Asks ChatGPT unbranded research questions for a keyword and counts which brands it recommends | OpenAI API key |
| [Alt text generator](#alt-text-generator) | Writes SEO-friendly alt text at scale with GPT-4o Vision plus page context | OpenAI API key |
| [SEMrush competitor comparison](#semrush-competitor-comparison) | Merges SEMrush organic-position exports from several competitors into one keyword-by-brand table | SEMrush exports |

---

## Technical SEO + AI-readiness audit
`socsDeathStar_updatedv3_26.ipynb`

Crawls a site (requests-based, so no browser needed), runs on-page SEO checks, and scores each page on AI-search readiness: structured data, headings, answer-style content, citation-worthiness, freshness, chunking and bot accessibility. Optionally samples Core Web Vitals through PageSpeed Insights and folds in a Screaming Frog export. Results go to a multi-tab Excel file.

**Setup** (the form in Cell 3):
- `AnalyzedDomain`, `XMLSitemap`: the site to audit
- `BrandKeywords`, `UnbrandedKeywords`: comma-separated, used for relevance checks
- `MaxPages`, `CrawlDelay`: keep the delay polite on sites you don't own
- `PageSpeedAPIKey`: optional. Leave it blank to skip PageSpeed. [Get a free key](https://developers.google.com/speed/docs/insights/v5/get-started)
- `EmailAddress`: optional. To email the report, also set `sender_email` and a Gmail **app password** in `send_email_report()` (Cell 4). Don't commit those back anywhere.

## Keyword-to-content match analyzer
`keywordContentMatchAnalyzer_V4.ipynb`

For each keyword, recommends **target an existing page**, **optimize a page**, or **create new content**. Keywords are classified by intent first and clustered *within* intent, so "CPQ" and "CPQ pricing" never merge. Each cluster is then scored against your best-matching URL on three axes: semantic match (embeddings), literal match (title/H1/URL, weighted by position) and intent/specificity match (GPT).

**Inputs:** a file of your site's URLs and a keyword file (optional `Search_Volume` and `Keyword_Difficulty` columns are used when present). **Key:** `openai_api_key.txt` uploaded to the session, or pasted when prompted. **Tuning:** add vertical-specific few-shot examples to `FEWSHOT_EXAMPLES_CUSTOM` and `KEYWORD_INTENT_FEWSHOT_CUSTOM`. **Output:** `keywordContentMatchAnalysis.xlsx`.

## AI brand sentiment
`hubSpotImpersonator_brandSentiment.ipynb`

Asks ChatGPT a mix of fixed, brand-agnostic questions (comparable across brands and runs) and generated, brand-specific ones, with optional web search. A separate judge model scores each answer from −2 to +2, tags aspects (price, quality, service, trust, results) and pulls supporting quotes. Reports a 0–100 Sentiment Index, a brand-safe rate, breakdowns by intent and aspect, and an optional themes summary. Re-upload past `*_responses.csv` exports to track a brand over time or compare brands.

**Setup:** upload `openai_api_key.txt`, then set `BRAND`, `CATEGORY` and the optional market fields in cell 3. Cell 4 estimates cost before anything runs. Update the pricing table in cell 2 if OpenAI's prices change.

## AI brand visibility
`hubSpotImpersonator.ipynb`

Generates research-phase questions for a keyword that don't name any brand (the kind a buyer would ask), runs them through ChatGPT, then extracts which real brands the answers mention and how often. A quick read on who AI assistants recommend in a category.

**Setup:** set `OPENAI_API_KEY` in the configuration cell (don't save the notebook with it filled in), then set `keyword` in the main cell.

## Alt text generator
`altTextGenerator_v3.ipynb`

Generates alt attributes with GPT-4o Vision, using each image plus context crawled from its page and an optional keyword list (keywords are used only when they fit what's visible in the image). Includes a 5-row test run before the full run.

**Input CSV columns:** `image_url`, `page_url`. **Key:** pasted into a hidden field or uploaded as a `.txt` file. It's held in memory for the session only. **Cost control:** `IMAGE_DETAIL = "low"` costs about $0.002 per image.

## SEMrush competitor comparison
`semRushCompetitorComparison_updated.ipynb`

Upload one SEMrush organic-positions CSV per competitor. The brand name is taken from each filename. Produces `combined_rankings.csv` showing how each brand ranks for every shared keyword, dropping keywords that fewer than `MIN_BRAND_COUNT` brands rank for.

---

## License

MIT. See [LICENSE](LICENSE).
