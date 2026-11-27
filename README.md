# Identity

**A searchable, bilingual directory of people and their stories.**

Identity is a lightweight, responsive website hosted with GitHub Pages. It reads public Markdown profiles from this repository, then lets visitors search by a person's name, Bangla or English name, nickname, aliases, quotations, and other details.

- **Website:** <https://alian69.github.io/identity/>
- **Repository:** <https://github.com/alian69/identity>
- **Contact:** [nursifat@proton.me](mailto:nursifat@proton.me)

> **Important:** This is a public project. Anything committed to this repository—including profile text and uploaded images—can be viewed and copied by other people. Only publish material that is appropriate to make public and that you have permission to use.

## Contents

- [Features](#features)
- [How the project works](#how-the-project-works)
- [Repository structure](#repository-structure)
- [Add a person](#add-a-person)
- [Add images](#add-images)
- [Markdown profile format](#markdown-profile-format)
- [Search behaviour](#search-behaviour)
- [Languages and themes](#languages-and-themes)
- [Reports and feedback](#reports-and-feedback)
- [Privacy and responsible publishing](#privacy-and-responsible-publishing)
- [Deployment](#deployment)
- [Customisation](#customisation)
- [License](#license)

## Features

- **Markdown-powered profiles:** Each person has an individual `.md` file. No database or build step is required.
- **English and Bangla interface:** Visitors can switch the website interface between English and বাংলা.
- **Search across profile content:** Search checks filenames, names, aliases, nicknames, metadata, quotations, and written profile details.
- **Typo-tolerant matching:** The client-side search uses normalized text, token similarity, and edit-distance matching to find close spellings and suggest similar profiles.
- **Two themes:** Dark is the default; light is also available. Theme and language preferences are remembered in the visitor's browser.
- **Responsive layout:** The page adapts to phones, tablets, and desktop screens.
- **Animated interface:** Page content and search results use the same upward fade-in motion in both themes. Reduced-motion preferences are respected.
- **Persistent header:** The logo and search, language, and menu controls remain available while navigating profiles and policy pages.
- **Separate image files:** Profile photographs and other images are stored as normal repository assets and referenced from Markdown.
- **No account system:** Visitors do not need to register or sign in.

## How the project works

Identity is a static front-end. When the page opens, the browser requests the repository's Git tree from the GitHub API. It then finds Markdown files inside `people/`, downloads those files from GitHub's raw-content host, and builds an in-memory search index.

When a visitor searches, matching happens in their browser. Selecting a result opens a profile view without leaving the website. The browser does not submit search terms to a separate Identity server.

The search implementation is deliberately dependency-free: the website does not require a framework, package installation, database, API key, or build command. GitHub's public API and raw-content service are required to load the profile collection, so the first load needs an internet connection.

## Repository structure

```text
identity/
├── index.html                       # Website, styles, and JavaScript
├── README.md                        # Project documentation
├── license.txt                      # Project usage and copyright notice
├── icon.png                         # Browser and device icon
├── in-website-logo-dark-theme.png   # Dark-theme header logo
├── in-website-logo-light-theme.png  # Light-theme header logo
├── people/
│   ├── .gitkeep
│   ├── person-name.md                # One profile per Markdown file
│   └── another-person.md
├── assets/
│   └── people/
│       ├── .gitkeep
│       └── person-name.jpg           # Images are stored separately
└── templates/
    └── identity-template.md          # Blank profile template
```

The website scans `.md` files under `people/`, including subfolders. It intentionally ignores the template and the repository's README. Markdown files elsewhere in the repository are not treated as person profiles.

## Add a person

1. Open the `people/` folder in the repository.
2. Create a new Markdown file. Use a simple filename such as `person-name.md` or `person-name-bangla.md`.
3. Copy the contents of [`templates/identity-template.md`](templates/identity-template.md) into the new file.
4. Fill in the front matter and the sections below it. Add both English and Bangla names, aliases, and search terms whenever you have accurate equivalents.
5. Commit the file to the `main` branch.
6. Open the website and search for the new profile. A page refresh loads the updated repository tree and files.

**Example path:** `people/your-person.md`

The filename is used as a fallback name only when `name_en` is empty. Use a clear, unique filename and avoid putting sensitive details in filenames.

## Add images

Keep images separate from Markdown files. This makes each image easier to replace, reuse, and manage.

1. Open `assets/people/` in the repository.
2. Upload an image you own or have permission to publish, for example `portrait.jpg`.
3. In a profile stored directly inside `people/`, set the front-matter `image` value to `../assets/people/portrait.jpg`.
4. To add more than one image, write normal Markdown image references in the profile body:

   ```md
   ![Portrait](../assets/people/portrait.jpg)
   ![Another photograph](../assets/people/another-photo.jpg)
   ```

The image path is relative to the Markdown file. If a profile is stored in a subfolder, adjust the number of `../` segments accordingly. For example, `people/history/person.md` needs `../../assets/people/portrait.jpg` to reach the top-level image folder.

Use descriptive filenames and alt text. Compress large images before uploading so the page loads quickly on mobile connections. A public image URL is not proof that you have permission to reuse the image.

## Markdown profile format

The template uses a small YAML-style front matter block at the start of each file. Keep each front-matter value on one line. The website understands these fields:

| Field | Purpose | Example |
|---|---|---|
| `name_en` | Name shown in the English interface | `"Ada Lovelace"` |
| `name_bn` | Name shown in the Bangla interface | `"অ্যাডা লাভলেস"` |
| `aliases` | Alternative names, separated by semicolons | `"Augusta Ada King; Ada King"` |
| `nickname` | Common nickname | `"Ada"` |
| `date_of_birth` | Date of birth | `"10 December 1815"` |
| `date_of_death` | Date of death, if applicable | `"27 November 1852"` |
| `nationality` | Nationality | `"British"` |
| `occupation` | Occupation or role | `"Mathematician and writer"` |
| `summary` | One-line profile introduction | `"A mathematician and early computing pioneer."` |
| `famous_line` | A short, accurately attributed quotation | `"A short quotation"` |
| `image` | Main image path, relative to the Markdown file | `"../assets/people/ada.jpg"` |
| `search_terms` | Extra searchable words in either language | `"computing; গণিত; analytical engine"` |

A profile can also contain regular Markdown below the closing `---` line. Use headings, paragraphs, lists, blockquotes, links, and images. Suggested sections include:

```md
## Biography

Write a concise, factual biography.

## Work and achievements

- Achievement one
- Achievement two

## Famous lines

> Add a short quotation only when it is correctly attributed.

## Sources

- [Source title](https://example.com)
```

The profile body is displayed in the language in which you wrote it. The language switch translates the website interface; it does not automatically translate the facts or biography. To make a profile searchable in both languages, add accurate Bangla and English names, aliases, and keywords to its front matter and body.

## Search behaviour

Search uses more than the Markdown filename. It considers:

- English and Bangla names;
- aliases and nicknames;
- metadata such as occupation, nationality, and dates;
- `search_terms` and the full Markdown body;
- close spellings and similar words.

Exact name matches are ranked above broad text matches. Typo tolerance is heuristic, not a guarantee: unusual spellings, very short queries, and mixed-language transliterations may need additional aliases or search terms in the profile. The search does not call a translation service. Add both scripts to the profile when you need cross-language matching.

## Languages and themes

- The interface supports English and Bangla.
- Dark theme is selected by default.
- Visitors can change the theme and language from the menu. The language can also be changed from the language control in the header.
- Preferences are stored locally in the visitor's browser; they are not written to the GitHub repository.
- The same motion design is used in both themes. Visitors who enable reduced motion on their device receive reduced animation.

## Reports and feedback

The menu contains **Report a problem** and **Send feedback** forms. Submitting either form opens the visitor's configured email application with a draft addressed to [nursifat@proton.me](mailto:nursifat@proton.me).

This is a static website, so it cannot send an email silently or deliver a message without an email service. The visitor must review the draft and press **Send**. The form does not upload the message to an Identity server.

## Privacy and responsible publishing

This repository and GitHub Pages site are public. Treat all committed profile files, images, and filenames as information that anyone may read, download, copy, or archive.

- Prefer public figures and information that is already appropriately public and verifiable.
- Do not publish private phone numbers, home or school addresses, account details, private conversations, financial information, health information, or other sensitive personal data.
- Do not publish a private individual's profile or photograph without their informed permission.
- Do not publish profiles, photographs, or identifying details about children or teenagers without the permissions required by applicable law and their parent or guardian.
- Use reliable sources, distinguish facts from opinions, and correct errors when discovered.
- Use only photographs and other media that you created, are licensed to use, are in the public domain, or have permission to publish. Give credit where required.

The website itself does not provide user accounts or an Identity-owned submission database. It requests public repository files from GitHub, stores theme and language preferences in browser local storage, and opens an email draft for reports. See the **Privacy Policy** inside the website for more detail. GitHub's own services and any external websites have separate privacy terms.

## Deployment

This project is designed for GitHub Pages from the repository root:

1. Open the repository's **Settings → Pages**.
2. Under **Build and deployment**, choose **Deploy from a branch**.
3. Select the `main` branch and the `/ (root)` folder, then save.
4. Keep `index.html` in the repository root.
5. Visit <https://alian69.github.io/identity/> after GitHub Pages finishes publishing the commit.

No build step or dependency installation is needed. If the site is already published, new commits to `main` update the website after Pages deploys them.

## Customisation

The website is intentionally kept in one `index.html` file. Its CSS and JavaScript are embedded in that file, so the project can be hosted directly by GitHub Pages without a compiler.

- Theme colours and layout variables are near the beginning of the `<style>` block.
- English and Bangla interface strings and policy text are in the `translations` object in the `<script>` block.
- Repository owner, repository name, branch, asset URLs, and contact email are defined near the beginning of the script.
- Header logos and the browser icon are image files in the repository root.

When changing the repository name or branch, update the corresponding configuration values and any absolute GitHub Pages asset links in `index.html` and this README.

## License

The project has a custom **All Rights Reserved** notice in [`license.txt`](license.txt). It is not an open-source licence and does not grant permission to copy, modify, redistribute, or commercially reuse the project. Third-party profile text, photographs, logos, and other contributed material may have separate rights and licence terms; this project's notice does not override those rights.
