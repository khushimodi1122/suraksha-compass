# Suraksha Compass

A cross-platform web app that suggests a life insurance policy type to customers in India, based on a 37-question profile. All amounts are in INR with Indian grouping (₹12,00,000, lakh and crore).

## Run it

It is a single static file with no build step and no backend. It runs in any modern browser on Windows, macOS, Linux, Android and iOS.

```bash
python3 -m http.server 8765
```

Then open http://localhost:8765. Opening `index.html` directly also works.

## What it does

1. **Questionnaire, 7 sections.** It has all 30 questions from the brief, plus 7 added ones (marked "Added" in the UI): city tier, existing savings/investments, family medical history, health insurance, planned retirement age, tax-saving preference and premium payment mode. Follow-up questions such as "how much cover" and "loan type" only appear when they apply. Answers are stored in the browser's `localStorage`.
2. **Live suggestion panel.** Fit scores for the four policies update as the customer answers.
3. **Report.**
   - The suggested policy, or a combination such as Term + Endowment.
   - A needs-based estimate of how much cover the family needs, with a line-by-line breakdown.
   - A capped amount, based on how much cover insurers usually allow as a multiple of income.
   - Indicative premiums and how to split the stated budget between policies.
   - Reasons for the suggestion and reasons the other policies were not chosen.
   - Suggested riders, a checklist for before buying, notes on tax (80C / 10(10D)) and the data sources used.

## Policy catalogue

`POLICIES` in `index.html` is pre-filled with Term, Whole life, Endowment and Money-back. Add an entry there, and scoring rules in `analyse()`, to support more products such as ULIPs or pension plans.

## Open data source

The app uses the [World Bank Open Data API](https://datahelpdesk.worldbank.org/knowledgebase/articles/889392). It is free, needs no key and is licensed CC BY 4.0. These India indicators are fetched at runtime:

| Indicator | Used for |
|---|---|
| `FP.CPI.TOTL.ZG` CPI inflation (5-year average) | Growing future income needs, education and marriage costs; real value of cover |
| `SP.DYN.LE00.IN` / `.MA.IN` / `.FE.IN` life expectancy | Comparing term end age with life expectancy (whole-life case) |
| `FR.INR.LEND` lending rate | Context for the assumed 7% return on the payout |

Some World Bank CDN responses omit CORS headers, so the app requests the API's JSONP format and falls back to `fetch`. If both fail (offline, or a sandbox that blocks the request), it uses a bundled snapshot taken on 6 Oct 2026. The header chip shows which source is active.

Other open Indian sources worth adding later:
- [data.gov.in](https://data.gov.in): Open Government Data Platform. It needs a free API key and has insurance and CPI datasets.
- IRDAI Annual Report: insurer-wise claim settlement ratios, so you can recommend specific insurers as well as policy types.

## Assumptions (edit in the constants block)

- **Rates:**
  - 7% return on the payout.
  - Education inflation = CPI + 4 points.
  - The family needs 70% of the income replaced until retirement.
- **City-tier costs:** education, marriage and parent-support costs vary by city tier (`COSTS`).
- **Premium tables:** `TERM_RATE` and `WHOLE_RATE`, and the endowment and money-back factors, are market-typical approximations. They are not quotes.
- **Health answers:** these only adjust indicative premiums and suggest riders. They never decide eligibility. The app says clearly that the insurer's underwriting decides.

## Disclaimer

This is an educational, preliminary suggestion. It is not an offer of insurance, a quote, an underwriting decision or financial advice.
