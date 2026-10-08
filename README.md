# Open Stock

A backend-free industry explorer for GitHub Pages, built with a single HTML
page, inline CSS, vanilla JavaScript, and nine local JSON files. There are no
dependencies, API keys, external fonts, or build steps.

## License

This project is licensed under the [MIT License](./LICENSE). The included
company examples and industry descriptions are mock educational content; names
and trademarks remain the property of their respective owners. See the license
for warranty and liability terms.

## Publish on GitHub Pages

1. Upload `index.html`, this README, and the entire `data` directory to your
   repository, preserving file names and capitalization.
2. In **Settings > Pages**, choose **Deploy from a branch**.
3. Select your publishing branch and **/(root)**, then save.
4. Open the published Pages URL when deployment completes.

All data URLs are relative, so both user/organization Pages sites and project
sites hosted under a repository subpath work without changing the code.

## Local preview

Serve this directory with an HTTP static server (for example, VS Code Live
Server), then open its local URL. Do not double-click `index.html`: browsers
restrict `fetch()` from `file:` URLs, and the app displays an explanatory error.

## Data and behavior

- Default: Traditional Chinese (`zh-TW`), Taiwan, Information Technology,
  investment characteristics and representative companies.
- Language follows the selected market: Taiwan uses Traditional Chinese
  (`zh-TW`), US uses English (`en`), and Japan uses Japanese (`ja`). There is no
  separate language selector. Market changes translate labels, company names,
  characteristics, errors, table headers, and the document title.
- Markets: Taiwan, US, Japan, each with two industry datasets.
- The sticky header places the Open Stock logo/title on the left and the
  country control on the right. The country label is `Stock Country`,
  `股票市場`, or `株の売り場`; options are ordered US, Japan, Taiwan and localized.
  The English US option uses the requested wording `United State`.
- The horizontal, scrollable chip bar shows all eleven requested sectors in
  every market. Solid chips have a dataset; dashed chips show a localized
  "no dataset" message without fetching a nonexistent file or substituting
  another sector. Japan's original Electric Appliances and Consumer chips are
  appended so both original datasets remain accessible. They are not
  reclassified as GICS sectors. All original JSON files remain unchanged.
- Changing market selects its first industry. Changing either market or industry
  restores the representative view.
- Each dataset has four localized characteristics, three representative
  companies with API reference metadata, and six companies in its full list.
- "Full list" means every company in that mock dataset, **not** an exhaustive
  exchange listing. Company identities and tickers are realistic examples, not
  a live or certified listing directory. Japan Consumer is a thematic grouping
  across official JPX industries.
- API names in the static company cards are the original reference metadata,
  not necessarily the source used for a fetched quote. The quote itself displays
  its actual provider, currency, and source date/time separately.
  Educational material is not investment advice.
- JSON loads are cached during the session. Failed requests show a localized
  error with retry; requests time out after 15 seconds. Stale requests cannot
  overwrite a newer market or industry selection.
- The layout adapts to mobile, with native selectors, keyboard-accessible
  industry buttons, focus management, semantic tables, and loading announcements.

## Fetch Live Data

The localized **Fetch Live Data** button fetches prices for every company in the
selected dataset and updates both representative cards and the expanded table.
Results exist only in browser memory. No JSON is written, no API keys are
embedded, and no backend, third-party proxy, or paid service is used.

| Market/exchange | Free price endpoint | Meaning |
| --- | --- | --- |
| Taiwan / TWSE | `https://openapi.twse.com.tw/v1/exchangeReport/STOCK_DAY_ALL` | Latest trading-day closing price, TWD |
| Taiwan / TPEx | `https://www.tpex.org.tw/openapi/v1/tpex_mainboard_daily_close_quotes` | Latest trading-day closing price, TWD |
| US | `https://query1.finance.yahoo.com/v8/finance/chart/<ticker>?interval=1d&range=1d` | Unofficial Yahoo market price, USD |
| Japan | Same Yahoo endpoint with `<ticker>.T` | Unofficial Yahoo market price, JPY |

TWSE/TPEx prices are **daily closes, not streaming intraday quotes**. The
source's ROC or Gregorian date is displayed; retrieval time is shown separately.
Yahoo prices may be delayed. Yahoo requests are sequential, not a parallel
burst, and the endpoint is unofficial and may change or restrict access.

Browser access depends on each provider's CORS policy, availability, and rate
limits. During local browser verification, **all three price hosts rejected
cross-origin requests with missing `Access-Control-Allow-Origin` headers**.
An HTTP client being able to fetch an endpoint does not establish that a browser
can fetch it. Live retrieval cannot be guaranteed on GitHub Pages under the
no-backend constraint; the button attempts direct requests and reports failures.
No CORS bypass is attempted. Failed or
partial requests are explicitly reported in the selected language with the
affected provider/tickers; missing quotes are never replaced with mock prices.
The same button retries requests, with a 15-second timeout per request.
Market/industry changes cancel and invalidate pending quote requests and clear
old quotes. Representative/full-list view changes preserve fetched prices.

**SEC EDGAR** is a free filings/fundamentals API, not a quote source; its
`data.sec.gov` APIs do not support browser CORS requests.
**EDINET v2** is a filings API that requires a registered API key.
Neither is called by this credential-free browser app. Fetching their filings
would require relaxing the no-backend/no-credentials constraints. Their original
JSON reference metadata is preserved. Alpha Vantage and J-Quants, also mentioned
in the original JSON metadata, are **not called**.

## Editing the datasets

`data/languages.json` lists the three supported languages.
`data/markets.json` defines localized market names.
`data/industries.json` maps each market ID to its industries and source metadata.
The app loads each industry's content from
`data/industry_data/<market>/<industry>.json`.

Keep localized values for all three languages, string tickers, unique
exchange/ticker pairs, and representative companies included in `allCompanies`.
Each representative also needs `api.price` and `api.fundamental`.
The app validates these requirements and displays an error for malformed data.
To populate a dashed industry, add its matching ID to `data/industries.json`
and create its matching JSON file. The navigation IDs are `IT`, `Financials`,
`Healthcare`, `Energy`, `Industrials`, `ConsumerDiscretionary`, `ConsumerStaples`,
`CommunicationServices`, `Materials`, `RealEstate`, and `Utilities`.
To add an additional supported-market industry, add its metadata and matching
JSON file; its chip is appended after the eleven standard sectors.
When adding languages or changing dataset totals, also update the translations
in `index.html`.
