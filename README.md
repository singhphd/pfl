# Publication Facts Label

This repository contains the **Publication Facts Label** web component — a self-contained [Web Component](https://en.wikipedia.org/wiki/Web_Components) that can be integrated into any publishing platform to display transparency information about a scholarly article.

See [singhphd/pflPlugin](https://github.com/singhphd/pflPlugin) for the companion OJS plugin that integrates this component into [Open Journal Systems](https://pkp.sfu.ca/software/ojs/).

---

## Quick Start

1. Clone the repository to your local machine:
   ```
   git clone https://github.com/singhphd/pfl.git
   ```
2. Open `index.html` in your browser.

You can also view the reference implementation [on GitHub Pages](https://pkp.github.io/pfl/).

---

## Integration Guide

### 1. Add the web font

Include the following `<style>` block in your page `<head>` to load the Noto Sans variable font used by the label:

```css
@font-face {
  font-family: "PFL Noto Sans";
  src: url(<path-to-repo>/font/NotoSans-VariableFont_wdthwght.woff2) format("woff2");
  font-weight: 100 900;
}
```

### 2. Add the custom element

Place the `<publication-facts-label>` element wherever you want the label to appear:

```html
<publication-facts-label></publication-facts-label>
```

### 3. Include the JavaScript

Copy the contents of this repository to your server and include `js/pfl.js`:

```html
<script src="<path-to-repo>/js/pfl.js"></script>
```

### 4. Configure and initialise

After the DOM is ready, set the `data` property on the element to provide article and journal data. The component then renders automatically.

```js
window.addEventListener("DOMContentLoaded", () => {
  const pfl = document.querySelector("publication-facts-label");

  const pflData = {
    baseUrl: "<path-to-repo>",  // used to resolve locale files and images
    locale: "en",               // BCP 47 language tag (e.g. "fr", "pt_BR")
    labels: {},                 // populated from locale file — see below
    values: {
      // ── Per-article values ──────────────────────────────────────────────

      // Number of peer reviewers for this article (string)
      pflReviewerCount: "2",

      // Data availability statement: "YES", "NO", or "NA" (not applicable)
      pflDataAvailabilityValue: "YES",
      // URL to the data availability statement on the page (can be a hash)
      pflDataAvailabilityValueUrl: "#data-availability",

      // External funding: "YES", "NO", or "NA"
      pflFundersValue: "YES",
      // URL to the funding section on the page
      pflFundersValueUrl: "#funding",

      // Competing interests: "YES", "NO", or "NA"
      pflCompetingInterestsValue: "YES",
      // URL to the competing interests section
      pflCompetingInterestsValueUrl: "#competing-interests",

      // Acceptance rate for this journal (string, e.g. "15%")
      pflAcceptedPercent: "15%",

      // Days from initial submission to first online publication (string)
      pflDaysToPublication: "180",

      // ── Journal/repository-level values ─────────────────────────────────

      // Comparable journals: average reviewers per article
      pflReviewerCountClass: "2.5",
      // Comparable journals: % with data availability statement
      pflDataAvailabilityPercentClass: "40%",
      // Comparable journals: % with funding information
      pflNumHaveFundersClass: "20%",
      // Comparable journals: % with competing interest statements
      pflCompetingInterestsPercentClass: "25%",
      // Comparable journals: acceptance rate (string, e.g. "18%")
      pflNumAcceptedClass: "18%",
      // Comparable journals: avg days submission → publication
      pflDaysToPublicationClass: "240",

      // ── Index list ───────────────────────────────────────────────────────

      // Array of indexing databases for this journal
      pflIndexList: [
        {
          url: "https://doaj.org/toc/your-issn",
          name: "DOAJ",
          description: "Directory of Open Access Journals",
        },
        // add more entries as needed
      ],

      // ── Editorial board ──────────────────────────────────────────────────

      // URL to the editorial board / masthead page
      editorialTeamUrl: "/about/editorialTeam",
      // Set true when the platform surfaces verified ORCID iDs for editors
      editorialTeamOrcidSupported: false,

      // ── Society & publisher ──────────────────────────────────────────────

      // Name and URL of the sponsoring academic society (omit if none)
      pflAcademicSociety: "Example Society",
      pflAcademicSocietyUrl: "https://example-society.org",

      // Journal publisher name and homepage URL
      pflPublisherName: "Example Publisher",
      pflPublisherUrl: "https://example-publisher.com",

      // ── Info link ────────────────────────────────────────────────────────

      // URL to the "about Publication Facts" information page
      pflInfoUrl: "https://pkp.sfu.ca/information-on-pfl/",
    },
  };

  // Load locale strings, falling back to English
  const localeUrl  = pflData.baseUrl + "/locale/" + pflData.locale + ".json";
  const fallbackUrl = pflData.baseUrl + "/locale/en.json";

  function applyPflLabels(labels) {
    document.querySelector("publication-facts-label").data =
      Object.assign({}, pflData, { labels });
  }

  fetch(localeUrl)
    .then((r) => { if (!r.ok) throw new Error(); return r.json(); })
    .then(applyPflLabels)
    .catch(() =>
      fetch(fallbackUrl)
        .then((r) => { if (!r.ok) throw new Error(); return r.json(); })
        .then(applyPflLabels)
        .catch((err) => console.warn("PFL: failed to load translations", err))
    );
});
```

---

## Localisation

Locale strings live in `locale/<lang>.json` (e.g. `locale/fr.json`). Currently supported languages:

| Code   | Language            |
|--------|---------------------|
| `bg`   | Bulgarian           |
| `cs`   | Czech               |
| `da`   | Danish              |
| `en`   | English (default)   |
| `es`   | Spanish             |
| `eu`   | Basque              |
| `fi`   | Finnish             |
| `fr`   | French              |
| `hu`   | Hungarian           |
| `id`   | Indonesian          |
| `it`   | Italian             |
| `mk`   | Macedonian          |
| `pt`   | Portuguese          |
| `pt_BR`| Portuguese (Brazil) |
| `sk`   | Slovak              |
| `sl`   | Slovenian           |
| `tr`   | Turkish             |
| `uk`   | Ukrainian           |

To add a new language, create `locale/<lang>.json` using `locale/en.json` as the template and translate all values. The `editorAndBoard` entry may contain a single `<a>` element (with `data-value="editorialTeamUrl"` and `data-attribute="href"`) positioned anywhere in the string to accommodate different word orders.

---

## Directory Layout

```
pfl/
├── font/          Variable Noto Sans web font (.woff2)
├── img/           SVG icons used by the component
├── js/
│   └── pfl.js    Web component source (single file, no build step)
├── locale/        Translation JSON files
└── index.html    Reference implementation & technical specification
```

---

## Contributing to Statistics

When fetching the shared statistics file, you can contribute your repository's aggregated data upstream. See the *Contributing to Statistics* section in `index.html` (or the live spec at <https://pkp.github.io/pfl/>) for the full list of URL parameters.
