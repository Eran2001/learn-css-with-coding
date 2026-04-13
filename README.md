# CSS and SASS

# Section 3: How CSS Works — A Look Behind the Scenes

---

## 11. Section Intro

**What is this:** A brief overview of what Section 3 covers — understanding how the browser parses and renders CSS.

**Description:** This section goes beyond writing CSS and explains what actually happens under the hood: how the cascade resolves conflicts, how values are computed, how inheritance works, how the browser renders elements visually, and how to architect clean, scalable CSS using BEM.

---

## 12. Three Pillars of Writing Good HTML and CSS (Never Forget Them!)

**What is this:** The three foundational principles that every good CSS developer must internalize.

**Description:** The three pillars are: **Responsive Design** (fluid layouts, media queries, flexible images), **Maintainable and Scalable Code** (clean, reusable, well-organized CSS), and **Web Performance** (less HTTP requests, less code, compressed files). These are guiding principles for every decision made when writing HTML and CSS.

**Examples:**

```css
/* Pillar 1: Responsive — use relative units, not fixed px */
.container {
  width: 90%; /* instead of width: 1200px */
  max-width: 1200px;
}

/* Pillar 2: Maintainable — use meaningful class names */
.btn--primary {
  background-color: #55c57a;
} /* good */
.green-button-big {
  background-color: #55c57a;
} /* bad */

/* Pillar 3: Performance — avoid redundant rules */
/* Reuse classes instead of duplicating declarations */
.text-center {
  text-align: center;
}
```

---

## 13. How CSS Works Behind the Scenes: An Overview

**What is this:** A high-level walkthrough of what happens from loading an HTML file to painting pixels on screen.

**Description:** When the browser loads a page it follows two main steps: **Parse HTML** (builds the DOM) and **Parse CSS** (resolves the cascade, computes values, builds the CSSOM). The DOM and CSSOM are combined into the **Render Tree**, which is then used for the **Visual Formatting Model** (layout), and finally **painted** to screen.

**Examples:**

```
Browser Load Flow:

HTML file
  └─► Parse HTML ──► DOM
  └─► Load CSS
        └─► Parse CSS
              ├─ Resolve Cascade
              ├─ Process Values
              └─► CSSOM
DOM + CSSOM
  └─► Render Tree
        └─► Visual Formatting Model (layout)
              └─► Website rendered on screen
```

---

## 14. How CSS is Parsed, Part 1: The Cascade and Specificity

**What is this:** How the browser decides which CSS rule wins when multiple rules target the same element.

**Description:** The **cascade** combines stylesheets from three sources: author (your CSS), user (browser extensions), and browser (default styles). It resolves conflicts using three criteria in order: **Importance** (`!important` > normal), **Specificity** (inline > IDs > classes > elements), and **Source Order** (last rule wins if specificity is equal). Specificity is calculated as a score: `(inline, IDs, classes/attributes/pseudo-classes, elements/pseudo-elements)`.

**Examples:**

```css
/* Specificity scores shown as (inline, ID, class, element) */

h1 {
  color: blue;
} /* (0, 0, 0, 1) */
.heading {
  color: red;
} /* (0, 0, 1, 0) */
#title {
  color: green;
} /* (0, 1, 0, 0) */

/* #title wins — highest specificity */

/* Chaining selectors increases specificity */
nav#nav div.pull-right .button {
  /* (0, 1, 2, 2) */
  background: orange;
}

a {
  background: blue;
} /* (0, 0, 0, 1) — loses */

/* !important overrides everything — use as last resort */
.btn {
  color: red !important;
}
```

---

## 15. Specificity in Practice

**What is this:** Hands-on application of specificity rules to debug and fix CSS conflicts.

**Description:** In practice, specificity issues arise when styles don't apply as expected. The fix is rarely `!important` — instead, understand which selector is winning and either increase your selector's specificity or restructure your CSS. Overusing IDs in selectors makes CSS hard to override and maintain.

**Examples:**

```css
/* Problem: button color not applying */
.nav .nav__link {
  color: white;
} /* (0, 0, 2, 0) — wins */
.btn {
  color: #55c57a;
} /* (0, 0, 1, 0) — loses */

/* Fix option 1: increase specificity of .btn rule */
.nav .btn {
  color: #55c57a;
} /* (0, 0, 2, 0) — ties, source order decides */

/* Fix option 2: restructure to avoid conflict */
/* Move .btn styles so they come after .nav styles */

/* Lesson: never use !important as a first fix — understand why it's losing */
```

---

## 16. How CSS is Parsed, Part 2: Value Processing

**What is this:** How the browser converts all CSS values (percentages, em, rem, etc.) into final pixel values.

**Description:** CSS values go through six stages: declared → cascaded → specified (defaults applied) → computed (relative values like `em` resolved) → used (final calculations like percentages resolved against layout) → actual (rounded to device pixels). Key rules: `%` on font-size is relative to **parent's font-size**; `%` on width is relative to **parent's width**; `em` on font-size is relative to **parent**; `em` on lengths is relative to **current element's font-size**; `rem` is always relative to the **root font-size**.

**Examples:**

```css
/* Root font-size = 16px (browser default) */

html {
  font-size: 62.5%;
} /* 62.5% of 16px = 10px — easy rem math */

body {
  font-size: 1.6rem;
} /* 1.6 * 10px = 16px */

.section {
  font-size: 1.5rem; /* 15px */
  padding: 3em; /* 3 * 15px = 45px (relative to own font-size) */
  width: 66.6%; /* 66.6% of parent element's width */
}

h1 {
  font-size: 2em; /* 2 * parent font-size */
  margin-bottom: 1.5rem; /* 1.5 * 10px = 15px — predictable */
}
```

---

## 17. How CSS is Parsed, Part 3: Inheritance

**What is this:** How CSS properties flow from parent elements to their children automatically.

**Description:** Inheritance means child elements automatically receive certain property values from their parents without them being explicitly declared. Properties related to **text** (color, font-size, font-family, line-height, etc.) inherit by default. Properties related to **box model** (padding, margin, border, width) do **not** inherit. The `inherit` keyword forces inheritance; `initial` resets to the browser default.

**Examples:**

```css
/* Inherited automatically */
body {
  font-family: "Lato", sans-serif; /* all children inherit this */
  color: #777; /* all children inherit this */
  font-size: 16px;
}

p {
  /* no font-family declared — inherits from body */
  /* no color declared — inherits #777 from body */
}

/* NOT inherited — must be declared explicitly */
.parent {
  padding: 20px;
  border: 1px solid red;
}
.child {
  /* padding and border are NOT inherited */
}

/* Force inheritance */
.child {
  padding: inherit; /* explicitly inherit parent's padding */
}
```

---

## 18. Converting px to rem: An Effective Workflow

**What is this:** A practical technique to use `rem` units throughout your CSS while keeping the math simple.

**Description:** Set the root `font-size` to `62.5%` (which equals `10px` based on the browser's default `16px`). This makes `1rem = 10px`, so converting any px value to rem is trivial (e.g. `24px = 2.4rem`). This approach also respects the user's browser font-size preference — if a user sets their browser to a larger font, everything scales proportionally. Using `px` would override that preference.

**Examples:**

```css
/* The trick: 62.5% of 16px = 10px */
html {
  font-size: 62.5%; /* 1rem = 10px */
}

/* Now px → rem conversion is just dividing by 10 */
h1 {
  font-size: 4.4rem; /* 44px */
  margin-bottom: 3rem; /* 30px */
}

.section {
  padding: 10rem 0; /* 100px top/bottom */
}

/* Why not just set font-size: 10px? */
/* Because px overrides user browser settings.
   62.5% scales with the user's preference. */

/* Global reset using rem */
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

---

## 19. How CSS Renders a Website: The Visual Formatting Model

**What is this:** The algorithm the browser uses to determine the position and size of every element on the page.

**Description:** The **Visual Formatting Model** processes the render tree and determines layout based on: **box model** (content, padding, border, margin), **box type** (block, inline, inline-block), **positioning scheme** (normal flow, floats, absolute/fixed), **stacking context** (z-index), and other factors like image sizes and viewport dimensions. Understanding this model explains why elements behave the way they do in layout.

**Examples:**

```css
/* Box model — default box-sizing: content-box */
.box {
  width: 300px;
  padding: 20px;
  border: 5px solid;
  /* Total rendered width = 300 + 40 + 10 = 350px */
}

/* box-sizing: border-box — width INCLUDES padding + border */
.box {
  box-sizing: border-box;
  width: 300px;
  padding: 20px;
  border: 5px solid;
  /* Total rendered width = 300px (padding/border are inside) */
}

/* Block vs inline vs inline-block */
div {
  display: block;
} /* full width, starts on new line */
span {
  display: inline;
} /* width/height have no effect */
button {
  display: inline-block;
} /* flows inline but respects width/height */

/* Normal flow vs float vs absolute */
.float {
  float: left;
} /* taken out of flow, text wraps around it */
.absolute {
  position: absolute;
} /* fully out of flow */
.relative {
  position: relative;
} /* stays in flow, offset from its normal position */
```

---

## 20. CSS Architecture, Components and BEM

**What is this:** A methodology for structuring CSS to be readable, maintainable, and scalable across large projects.

**Description:** Good CSS architecture means **thinking in components** — small, reusable, self-contained UI pieces. **BEM (Block Element Modifier)** is the naming convention: `block__element--modifier`. **Block** is a standalone component (`.card`), **Element** is a part of that block (`.card__image`), **Modifier** is a variation (`.card--featured`). Keep one component per file, never nest BEM selectors, and organize files with the **7-1 pattern** (7 folders, 1 main file).

**Examples:**

```css
/* BEM naming convention */

/* Block — standalone component */
.recipe-card {
}

/* Element — part of the block, tied to it */
.recipe-card__image {
}
.recipe-card__title {
}
.recipe-card__description {
}

/* Modifier — variation of block or element */
.recipe-card--featured {
}
.recipe-card__title--large {
}

/* Never do this — BEM selectors stay flat */
.recipe-card .recipe-card__image {
} /* wrong */
.recipe-card__image {
} /* correct */
```

```
/* 7-1 Folder Pattern */
sass/
  base/        ← resets, typography, animations
  components/  ← one file per component (.card, .btn, .form)
  layout/      ← header, footer, grid, nav
  pages/       ← page-specific styles
  themes/      ← dark mode etc
  abstracts/   ← variables, mixins, functions (no CSS output)
  vendors/     ← third-party CSS
  main.scss    ← imports everything
```

---

## 21. Implementing BEM in the Natours Project

**What is this:** Applying BEM naming to an existing project to make CSS structure consistent and scalable.

**Description:** This lesson refactors the Natours project HTML and CSS to follow BEM conventions. Every class name is reviewed: blocks get standalone names, elements get the `__` separator, and modifiers get `--`. The goal is that any developer can read a class name and immediately understand what component it belongs to, what role it plays, and whether it's a variation.

**Examples:**

```html
<!-- Before BEM -->
<header class="header">
  <div class="logo-box">
    <img class="logo" src="logo.png" />
  </div>
  <div class="text-box">
    <h1 class="heading-primary">
      <span class="heading-primary-main">Outdoors</span>
      <span class="heading-primary-sub">is where life happens</span>
    </h1>
  </div>
</header>

<!-- After BEM -->
<header class="header">
  <div class="header__logo-box">
    <img class="header__logo" src="logo.png" />
  </div>
  <div class="header__text-box">
    <h1 class="heading-primary">
      <span class="heading-primary__main">Outdoors</span>
      <span class="heading-primary__sub">is where life happens</span>
    </h1>
  </div>
</header>
```

```css
/* Corresponding CSS — flat selectors, no nesting */
.header {
}
.header__logo-box {
}
.header__logo {
}
.header__text-box {
}

.heading-primary {
}
.heading-primary__main {
}
.heading-primary__sub {
}
```

---

# Section 4: Introduction to Sass and NPM

---

## 22. Section Intro

**What is this:** An overview of what Section 4 covers — introducing Sass as a CSS preprocessor and NPM as a tool to manage it.

**Description:** This section teaches Sass (Syntactically Awesome Stylesheets), the most popular CSS preprocessor. It covers Sass core features (variables, nesting, mixins, extends, functions), setting up a real development workflow with NPM, compiling Sass locally, and using live reload to speed up development.

---

## 23. What is Sass?

**What is this:** An introduction to Sass — what it is, why it exists, and what problems it solves.

**Description:** Sass is a CSS preprocessor — you write `.scss` files with extra features (variables, nesting, logic), and Sass compiles them into plain `.css` that browsers understand. Sass does not replace CSS, it extends it. There are two syntaxes: **SCSS** (uses braces and semicolons, like CSS) and the older **Sass** (indentation-based). SCSS is the standard today. Core Sass features: variables, nesting, operators, partials and imports, mixins, functions, extends, and control directives.

**Examples:**

```scss
// Sass adds features that plain CSS lacks

// Variables — define once, reuse everywhere
$color-primary: #55c57a;
$font-size-default: 1.6rem;

// Nesting — mirrors HTML structure
.nav {
  background: $color-primary;

  &__item {         // compiles to .nav__item
    display: inline-block;
  }

  &__link {         // compiles to .nav__link
    color: white;

    &:hover {       // compiles to .nav__link:hover
      color: $color-primary;
    }
  }
}
```

---

## 24. First Steps with Sass: Variables and Nesting

**What is this:** How to use Sass variables to store reusable values and nesting to write cleaner, structured selectors.

**Description:** **Variables** in Sass store colors, font sizes, spacing values — anything you'd otherwise repeat across your CSS. They use the `$` prefix. **Nesting** lets you write child selectors inside their parent, mirroring the HTML hierarchy. The `&` symbol references the current selector, enabling BEM-style class names without repetition. Nesting should not go deeper than 3 levels to avoid overly specific output.

**Examples:**

```scss
// Variables
$color-primary: #55c57a;
$color-primary-dark: #28b485;
$color-grey-dark: #777;
$default-font-size: 1.6rem;

body {
  font-size: $default-font-size;
  color: $color-grey-dark;
}

// Nesting with & (ampersand = parent selector reference)
.header {
  background-color: $color-primary;
  height: 95vh;

  &__logo-box {           // → .header__logo-box
    position: absolute;
    top: 4rem;
    left: 4rem;
  }

  &__logo {               // → .header__logo
    height: 3.5rem;
  }

  &__text-box {           // → .header__text-box
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
}

// & also works for pseudo-classes and pseudo-elements
.btn {
  &:hover {               // → .btn:hover
    transform: translateY(-3px);
  }

  &::after {              // → .btn::after
    content: '';
  }
}
```

---

## 25. First Steps with Sass: Mixins, Extends and Functions

**What is this:** Three Sass tools for reusing chunks of CSS logic — mixins, extends, and built-in functions.

**Description:** **Mixins** are reusable blocks of declarations, optionally accepting arguments — like a function that outputs CSS. **Extends** (`@extend`) share a set of declarations between selectors via a placeholder (`%`); unlike mixins, extend groups selectors together in the compiled output. **Functions** return a value (not a block of CSS) and are used inside property values. Use mixins when you need arguments or complex logic; use extends when selectors are truly variations of the same thing.

**Examples:**

```scss
// Mixin — reusable block, can accept arguments
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

@mixin respond-to($breakpoint) {
  @if $breakpoint == tablet {
    @media (max-width: 768px) { @content; }
  } @else if $breakpoint == phone {
    @media (max-width: 480px) { @content; }
  }
}

.hero {
  @include flex-center;

  @include respond-to(tablet) {
    font-size: 1.4rem;
  }
}

// Extend — shares declarations via placeholder
%btn-base {
  display: inline-block;
  padding: 1rem 2.5rem;
  border-radius: 10rem;
  cursor: pointer;
}

.btn--primary {
  @extend %btn-base;
  background-color: #55c57a;
}

.btn--secondary {
  @extend %btn-base;
  background-color: transparent;
}

// Built-in Sass functions
$color: #55c57a;

.box {
  background: lighten($color, 15%);   // lighter shade
  border-color: darken($color, 10%);  // darker shade
  color: rgba($color, 0.8);           // with opacity
}
```

---

## 26. A Brief Introduction to the Command Line

**What is this:** The minimum command line knowledge needed to work with NPM and Sass in a development workflow.

**Description:** The command line (terminal) is how developers interact with tools like NPM and Sass compilers. You don't need to be an expert — just enough to navigate folders, install packages, and run scripts. Key commands: `ls`/`dir` (list files), `cd` (change directory), `mkdir` (make folder), `touch`/`New-Item` (create file), `npm install`, `npm run`.

**Examples:**

```bash
# Navigate the filesystem
ls                        # list files in current directory (Mac/Linux)
dir                       # list files (Windows)
cd project-folder         # move into a folder
cd ..                     # go up one level
pwd                       # print current directory path

# Create files and folders
mkdir sass                # create a folder named sass
touch style.scss          # create an empty file (Mac/Linux)

# NPM commands used throughout this course
npm init                  # create a package.json in the current folder
npm install sass --save-dev  # install Sass as a dev dependency
npm run compile:sass      # run a custom script from package.json
```

---

## 27. NPM Packages: Let's Install Sass Locally

**What is this:** Setting up NPM in a project and installing the Sass compiler as a local dev dependency.

**Description:** NPM (Node Package Manager) manages third-party tools and libraries. Running `npm init` creates a `package.json` that tracks your project's dependencies and scripts. Installing Sass with `--save-dev` adds it as a dev dependency — meaning it's only needed during development, not in production. The installed package lives in `node_modules/` and should never be committed to git (add it to `.gitignore`).

**Examples:**

```bash
# Step 1: initialize NPM in your project folder
npm init
# (answer the prompts, or use npm init -y to skip them)

# Step 2: install Sass locally as a dev dependency
npm install sass --save-dev

# package.json now contains:
# "devDependencies": {
#   "sass": "^1.x.x"
# }
```

```
# .gitignore — never commit node_modules
node_modules/
```

```json
// package.json after npm init + npm install sass
{
  "name": "natours",
  "version": "1.0.0",
  "devDependencies": {
    "sass": "^1.69.0"
  }
}
```

---

## 28. NPM Scripts: Let's Write and Compile Sass Locally

**What is this:** Writing NPM scripts in `package.json` to compile Sass into CSS with a single terminal command.

**Description:** NPM scripts are shortcuts defined in `package.json` under the `"scripts"` key. They run local binaries from `node_modules/.bin/` without needing global installs. The Sass CLI takes an input `.scss` file and an output `.css` file. Adding `--watch` makes it recompile automatically on every file save. The `--no-source-map` flag is optional — it suppresses the `.css.map` file during development.

**Examples:**

```json
// package.json scripts section
{
  "scripts": {
    "compile:sass": "sass sass/main.scss css/style.css --watch"
  }
}
```

```bash
# Run the script
npm run compile:sass

# Sass watches for changes and recompiles automatically
# Output in terminal:
# Compiled sass/main.scss to css/style.css
# Watching for changes...
```

```
# Recommended folder structure
project/
  css/
    style.css         ← compiled output (never edit manually)
  sass/
    main.scss         ← entry point, imports all partials
    abstracts/
      _variables.scss
      _mixins.scss
    base/
      _base.scss
      _typography.scss
    components/
      _button.scss
  index.html
  package.json
```

---

## 29. The Easiest Way of Automatically Reloading a Page on File Changes

**What is this:** Using `live-server` via NPM to automatically refresh the browser whenever a file changes.

**Description:** `live-server` is a lightweight dev server that watches your project files and refreshes the browser on every save — no manual refreshing needed. Combined with the Sass `--watch` flag, you get a full live-reload workflow: save a `.scss` file → Sass recompiles to CSS → browser refreshes automatically. Both processes run in parallel using two terminal tabs, or combined with a tool like `npm-run-all`.

**Examples:**

```bash
# Install live-server globally (or as a dev dependency)
npm install live-server --save-dev
```

```json
// package.json — two scripts running in parallel
{
  "scripts": {
    "watch:sass": "sass sass/main.scss css/style.css --watch",
    "serve": "live-server",
    "start": "npm-run-all --parallel watch:sass serve"
  }
}
```

```bash
# Install npm-run-all to run scripts in parallel
npm install npm-run-all --save-dev

# Then start both with one command
npm start

# Terminal output:
# [watch:sass] Watching for changes...
# [serve]      Serving at http://127.0.0.1:8080
# → any .scss save recompiles CSS and browser auto-refreshes
```

---

# Section 8: A Quick Introduction to CSS Grid Layouts

---

## 89. Section Intro

**What is this:** An overview of what Section 8 covers — learning CSS Grid from scratch to build two-dimensional layouts.

**Description:** This section introduces CSS Grid, the most powerful layout system in CSS. Unlike Flexbox (one-dimensional), Grid handles both rows and columns simultaneously. The section covers creating grids, placing and spanning items, naming lines and areas, implicit vs explicit grids, alignment, and responsive techniques like `auto-fit`, `auto-fill`, and `minmax()`.

---

## 90. Why CSS Grid: A Whole New Mindset

**What is this:** Understanding the mental shift required to think in Grid — designing layout from the container outward.

**Description:** Before Grid, layouts were hacked together with floats, then Flexbox improved things but remained one-dimensional. CSS Grid introduces a true two-dimensional layout model where you define rows AND columns on a container, then place children into that grid. The mindset shift: stop thinking about each element positioning itself, start thinking about the grid structure as a whole — like a table, but infinitely more flexible.

**Examples:**

```css
/* Old way — float-based layout (hack) */
.col { float: left; width: 33.33%; }
.clearfix::after { content: ''; display: block; clear: both; }

/* Flexbox — one axis at a time */
.container { display: flex; flex-wrap: wrap; }
.item { flex: 0 0 33.33%; }

/* CSS Grid — two-dimensional, built for layout */
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr; /* 3 equal columns */
  grid-template-rows: auto auto;       /* 2 rows */
  gap: 2rem;
}
/* Children just sit in the grid — no float, no flex needed */
```

---

## 91. Quick Setup for This Section

**What is this:** Setting up a minimal HTML/CSS sandbox to experiment with CSS Grid throughout the section.

**Description:** The setup is intentionally simple — a plain HTML file with a `.container` div holding several numbered `.item` divs, and a linked CSS file. This isolated environment makes it easy to see exactly what each Grid property does without noise from a real project. Browser DevTools' Grid inspector (Firefox or Chrome) is invaluable — it overlays the grid lines directly on the page.

**Examples:**

```html
<!-- index.html -->
<div class="container">
  <div class="item item--1">1</div>
  <div class="item item--2">2</div>
  <div class="item item--3">3</div>
  <div class="item item--4">4</div>
  <div class="item item--5">5</div>
  <div class="item item--6">6</div>
</div>
```

```css
/* style.css */
.container {
  background-color: #eee;
  width: 800px;
  margin: 30px auto;
}

.item {
  padding: 20px;
  font-size: 2rem;
  font-family: sans-serif;
  background-color: orangered;
  color: white;
}
```

---

## 92. Creating Our First Grid

**What is this:** Turning a container into a grid and defining its columns and rows with `grid-template-columns` and `grid-template-rows`.

**Description:** `display: grid` activates Grid on a container. `grid-template-columns` defines how many columns and their widths; `grid-template-rows` defines row heights. Values can be fixed (`px`), flexible (`fr`), or auto-sized. The `gap` property (formerly `grid-gap`) adds spacing between tracks. Children automatically flow into cells left-to-right, top-to-bottom.

**Examples:**

```css
.container {
  display: grid;

  /* 3 columns: 150px, auto, 150px */
  grid-template-columns: 150px auto 150px;

  /* 2 rows: 150px tall, then 300px tall */
  grid-template-rows: 150px 300px;

  /* gap between rows and columns */
  gap: 30px;             /* same for row and column */
  row-gap: 30px;         /* rows only */
  column-gap: 30px;      /* columns only */
}

/* repeat() shorthand — avoids repetition */
.container {
  display: grid;
  grid-template-columns: repeat(3, 150px); /* same as: 150px 150px 150px */
  grid-template-rows: repeat(2, 150px);
  gap: 20px;
}
```

---

## 93. Getting Familiar with the fr Unit

**What is this:** Using the `fr` (fraction) unit to create flexible, proportional grid tracks that fill available space.

**Description:** `fr` stands for "fraction of available space." It's Grid-only and distributes leftover space after fixed-size tracks are placed. `1fr 1fr 1fr` creates three equal columns. `1fr 2fr 1fr` makes the middle column twice as wide. `fr` units interact with `repeat()` naturally and are the primary tool for building responsive grids without media queries or percentages.

**Examples:**

```css
/* Fixed vs fr comparison */
.container {
  display: grid;
  width: 800px;
  gap: 20px;
}

/* Fixed — doesn't fill container */
.container { grid-template-columns: 150px 150px 150px; }

/* fr — fills all available space */
.container { grid-template-columns: 1fr 1fr 1fr; }
/* same as: */
.container { grid-template-columns: repeat(3, 1fr); }

/* Mixed: fixed + flexible */
.container { grid-template-columns: 200px 1fr 1fr; }
/* 200px fixed first column, remaining space split equally */

/* Proportional fractions */
.container { grid-template-columns: 1fr 2fr 1fr; }
/* middle column is twice the width of the others */

/* fr with rows */
.container {
  height: 600px;
  grid-template-rows: 1fr 2fr 1fr;
}
```

---

## 94. Positioning Grid Items

**What is this:** Manually placing grid items into specific cells using line numbers.

**Description:** By default items flow automatically into the grid. You can override this with `grid-column` and `grid-row`, which reference **grid lines** (the dividers between tracks). A grid with 3 columns has 4 column lines (1 through 4). `grid-column: 1 / 3` means "start at line 1, end at line 3" — spanning 2 columns. You can also use negative line numbers (counting from the end) and the `span` keyword.

**Examples:**

```css
/* Grid with 3 columns — lines are numbered 1 2 3 4 */
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 150px);
  gap: 20px;
}

/* Place item 1 at column 1, row 1 (default — same as doing nothing) */
.item--1 {
  grid-column: 1 / 2;
  grid-row: 1 / 2;
}

/* Move item 2 to column 3, row 1 */
.item--2 {
  grid-column: 3 / 4;
  grid-row: 1 / 2;
}

/* Place item 3 in the second row, first column */
.item--3 {
  grid-column: 1 / 2;
  grid-row: 2 / 3;
}

/* -1 means the last grid line */
.item--4 {
  grid-column: 1 / -1; /* spans full width */
}
```

---

## 95. Spanning Grid Items

**What is this:** Making a grid item occupy multiple rows or columns using `span`.

**Description:** Instead of specifying start and end line numbers, the `span` keyword tells an item how many tracks to cover. `grid-column: 1 / span 2` means "start at line 1 and span across 2 columns." Spanning lets you build complex layouts — sidebars, featured cards, hero areas — without needing named lines or exact line numbers.

**Examples:**

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 150px);
  gap: 20px;
}

/* Span across 2 columns */
.item--1 {
  grid-column: 1 / span 2;  /* occupies columns 1 and 2 */
}

/* Span across 2 rows */
.item--2 {
  grid-row: 1 / span 2;     /* occupies rows 1 and 2 */
}

/* Full-width item (span all 3 columns) */
.item--6 {
  grid-column: 1 / -1;      /* -1 = last line */
}

/* Span from any starting point */
.item--3 {
  grid-column: 2 / span 2;  /* starts at col 2, spans 2 columns */
  grid-row: 2 / span 2;     /* starts at row 2, spans 2 rows */
}
```

---

## 96. Grid Challenge

**What is this:** A hands-on exercise to practice building a specific grid layout from scratch using the concepts learned so far.

**Description:** A challenge is given: reproduce a target layout by writing only `grid-template-columns`, `grid-template-rows`, and item placement properties. The goal is to reinforce line-based placement and spanning without guidance. Attempting it independently before watching the solution is key to internalizing Grid's logic.

---

## 97. Grid Challenge: A Basic Solution

**What is this:** A walkthrough of one correct solution to the Grid Challenge from lesson 96.

**Description:** This lesson demonstrates how to read a target layout, identify the grid structure (how many columns, how many rows), assign items to their correct positions, and handle spanning. There's rarely one "right" solution — different column/row definitions can produce the same visual result. The focus is on developing a systematic approach: define the grid first, then place the items.

**Examples:**

```css
/* Example solution for a common challenge layout */
.container {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(3, 100px);
  gap: 10px;
}

.item--1 { grid-column: 1 / span 2; }           /* top-left, 2 cols wide */
.item--2 { grid-column: 3 / 4; grid-row: 1 / 3; } /* right side, 2 rows tall */
.item--3 { grid-column: 1 / 2; grid-row: 2 / 4; } /* left side, 2 rows tall */
.item--4 { grid-column: 2 / span 2; grid-row: 2 / 3; }
.item--5 { grid-column: 2 / 4; grid-row: 3 / 4; }
.item--6 { grid-column: 4 / 5; grid-row: 1 / -1; } /* far right, full height */
```

---

## 98. Naming Grid Lines

**What is this:** Giving grid lines meaningful names instead of relying on numeric indices.

**Description:** Grid lines can be named inside `grid-template-columns` and `grid-template-rows` using bracket notation `[name]`. Named lines make placement code self-documenting — instead of `grid-column: 2 / 4` you write `grid-column: content-start / content-end`. A line can have multiple names. Named lines are especially useful in complex layouts where numeric indices become hard to track.

**Examples:**

```css
.container {
  display: grid;

  /* Name lines inline with bracket notation */
  grid-template-columns:
    [sidebar-start] 250px
    [sidebar-end content-start] 1fr
    [content-end];

  grid-template-rows:
    [header-start] 100px
    [header-end main-start] 1fr
    [main-end footer-start] 80px
    [footer-end];

  gap: 20px;
}

/* Place items using names instead of numbers */
.header {
  grid-column: sidebar-start / content-end; /* full width */
  grid-row: header-start / header-end;
}

.sidebar {
  grid-column: sidebar-start / sidebar-end;
  grid-row: main-start / main-end;
}

.content {
  grid-column: content-start / content-end;
  grid-row: main-start / main-end;
}
```

---

## 99. Naming Grid Areas

**What is this:** Defining a visual ASCII-art-like map of your layout using `grid-template-areas`.

**Description:** `grid-template-areas` lets you draw your layout as named regions using strings. Each string represents a row; each word in the string represents a column cell. Assign an item to an area with `grid-area: name`. A `.` represents an empty cell. This approach makes the layout intent immediately readable — the CSS reads like a diagram.

**Examples:**

```css
.container {
  display: grid;
  grid-template-columns: 250px 1fr 1fr;
  grid-template-rows: 80px 1fr 60px;
  gap: 10px;

  grid-template-areas:
    "header  header  header"   /* row 1: header spans all 3 cols */
    "sidebar content content"  /* row 2: sidebar + 2 content cols */
    "footer  footer  footer";  /* row 3: footer spans all 3 cols */
}

/* Assign items to areas */
.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.footer  { grid-area: footer; }

/* Empty cell with dot */
.container {
  grid-template-areas:
    "header header ."
    "sidebar content content"
    ". footer footer";
}
```

---

## 100. Implicit Grids vs. Explicit Grids

**What is this:** Understanding the difference between the grid you define and the grid the browser creates automatically for overflow items.

**Description:** The **explicit grid** is what you define with `grid-template-columns` and `grid-template-rows`. The **implicit grid** is what the browser creates automatically when items overflow the defined tracks. By default, implicit rows are sized to `auto` (fit their content). Use `grid-auto-rows` and `grid-auto-columns` to control implicit track sizes. `grid-auto-flow` controls whether overflow items create new rows (default) or new columns.

**Examples:**

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 150px); /* explicit: 2 rows defined */
  gap: 20px;
}

/* If there are 9 items but only 6 explicit cells,
   a 3rd row is created implicitly — height: auto by default */

/* Control implicit row height */
.container {
  grid-auto-rows: 80px; /* all implicit rows = 80px */
}

/* Change flow direction to columns instead of rows */
.container {
  grid-auto-flow: column;
  grid-auto-columns: 200px; /* implicit columns = 200px wide */
}

/* dense: fill gaps caused by spanning items */
.container {
  grid-auto-flow: row dense;
}
```

---

## 101. Aligning Grid Items

**What is this:** Controlling how grid items are aligned within their individual cells using `align-self` and `justify-self`.

**Description:** Each grid cell is a box, and the item inside it can be positioned within that box. **`justify-items`** aligns items along the **row axis** (horizontal) — set on the container to apply to all items. **`align-items`** aligns items along the **column axis** (vertical). Both default to `stretch` (fill the cell). Individual items can override with `justify-self` and `align-self`. Values: `stretch`, `start`, `end`, `center`.

**Examples:**

```css
/* Applied to container — affects all items */
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 150px);

  align-items: center;    /* vertical: center items in their cells */
  justify-items: center;  /* horizontal: center items in their cells */
}

/* Stretch (default) — item fills the entire cell */
.container { align-items: stretch; justify-items: stretch; }

/* Override per item */
.item--1 {
  align-self: start;    /* top of cell */
  justify-self: end;    /* right of cell */
}

.item--2 {
  align-self: center;
  justify-self: center;
}
```

---

## 102. Aligning Tracks

**What is this:** Controlling how the entire grid (all tracks together) is positioned within the grid container when it doesn't fill it completely.

**Description:** When the total size of all tracks is less than the container size (e.g. fixed-width columns in a wide container), leftover space can be distributed. **`justify-content`** distributes space along the **row axis** (horizontal). **`align-content`** distributes space along the **column axis** (vertical). Values are the same as Flexbox: `start`, `end`, `center`, `space-between`, `space-around`, `space-evenly`. This is about the grid as a whole, not individual items within cells.

**Examples:**

```css
.container {
  display: grid;
  width: 1000px;
  height: 600px;

  /* 3 fixed columns — total: 300px. Container: 1000px. 700px leftover */
  grid-template-columns: repeat(3, 100px);
  grid-template-rows: repeat(2, 100px);
}

/* Distribute leftover horizontal space */
.container { justify-content: center; }        /* grid centered */
.container { justify-content: space-between; } /* spread to edges */
.container { justify-content: space-evenly; }  /* equal gaps everywhere */

/* Distribute leftover vertical space */
.container { align-content: center; }
.container { align-content: space-between; }

/* Note: with fr units, there's no leftover space — fr absorbs it all.
   justify-content and align-content only matter with fixed-size tracks. */
```

---

## 103. Using min-content, max-content and the minmax() Function

**What is this:** Three tools for creating grid tracks that respond intelligently to their content size.

**Description:** **`max-content`** sizes a track as wide as its largest content without wrapping. **`min-content`** sizes a track as narrow as possible while still fitting content (wrapping text at every opportunity). **`minmax(min, max)`** sets a floor and ceiling for a track size — the browser picks the best value between them. These are essential for building grids that adapt to content without hardcoded breakpoints.

**Examples:**

```css
.container {
  display: grid;
  gap: 20px;
}

/* max-content: column is as wide as its widest cell content */
.container {
  grid-template-columns: max-content 1fr 1fr;
}

/* min-content: column is as narrow as possible (content wraps aggressively) */
.container {
  grid-template-columns: min-content 1fr 1fr;
}

/* minmax(): track is at least 150px, at most 1fr */
.container {
  grid-template-columns: repeat(3, minmax(150px, 1fr));
}

/* Practical: sidebar fixed min, flexible max; content fills rest */
.container {
  grid-template-columns: minmax(200px, 300px) 1fr;
}

/* minmax with max-content */
.container {
  grid-template-columns: minmax(min-content, 200px) 1fr 1fr;
  /* first column: no smaller than its content, no larger than 200px */
}
```

---

## 104. Responsive Layouts with auto-fit and auto-fill

**What is this:** Creating automatically responsive grid layouts that reflow columns without any media queries.

**Description:** `auto-fill` and `auto-fit` are used with `repeat()` to let the browser determine how many columns fit based on available space. **`auto-fill`** creates as many tracks as will fit, keeping empty tracks (maintains the grid size). **`auto-fit`** collapses empty tracks to zero, allowing filled tracks to grow and fill the space. Combined with `minmax()`, this produces a fully responsive multi-column layout with a single line of CSS.

**Examples:**

```css
/* auto-fill: empty columns remain, items don't stretch to fill */
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 20px;
}
/* At 800px wide: fits 4 × 200px columns
   At 500px wide: fits 2 × 200px columns — reflows automatically */

/* auto-fit: empty columns collapse, items stretch to fill container */
.container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
}
/* With 3 items in a wide container:
   auto-fill → 3 items + empty ghost columns (items stay at 1fr of total)
   auto-fit  → 3 items expand to fill all available space */

/* The one-liner responsive grid — no media queries needed */
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(25rem, 1fr));
  gap: 3rem;
}
```
