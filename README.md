# Tauri 2 app with CDP enabled

This is a simple Tauri 2 app template that has CDP (Chrome DevTools Protocol) enabled for the Edge WebView2 on Windows.

This enables automated testing of the web view in the Tauri application for example by using Playwright to connect to the CDP port when the Tauri app is running. Unfortunately this way only works on Windows, but it is a very simple setup.

## Prerequisites

See [Tauri prerequisites](https://tauri.app/start/prerequisites/).

This repo is using `pnpm` but `npm` should also work. If you want to use npm instead, just replace the pnpm commands for npm alternatives (e.g. `pnpm tauri dev` -> `npm run tauri dev`).

Prepare dependencies by running `pnpm install`.

## Run in development mode

```shell
pnpm tauri dev
```

## Build

```shell
pnpm tauri build
```

After this (on Windows) you can run the generated executable from: `.\src-tauri\target\release\tauri-cdp.exe`.

## CDP endpoint

A CDP endpoint is exposed at `http://localhost:9222` while the app is running.

### Connecting to the web view with Playwright

From a Playwright test you can connect to the Tauri web view via the exposed CDP endpoint like this:

```ts
import {
  chromium,
  type Browser,
  type BrowserContext,
  type Page
} from '@playwright/test';

const browser: Browser = await chromium.connectOverCDP('http://localhost:9222');
const context: BrowserContext = browser.contexts()[0];
const page: Page = context.pages()[0];
```

## Security note

CDP and the dev tools should NOT be enabled for production builds.

## Relevant configuration

This repository was initialized with the following command:

```shell
pnpm create tauri-app
```

The only relevant changes done after initializing the app template are:

- Enabled dev tools for `tauri` in `Cargo.toml`
- Enabled CDP in `tauri.conf.json`

With Tauri, enabling CDP in this way works only on Windows.

CDP is enabled by setting `--remote-debugging-port=9222` for the [additionalBrowserArgs](https://v2.tauri.app/reference/config/#additionalbrowserargs) option in the window configuration object in `tauri.conf.json`.

Alternatively this could also be done by setting an environment variable for example from Rust code by adding this in the beginning of the `run()` function in `./src/lib.ts`:

```rust
std::env::set_var(
    "WEBVIEW2_ADDITIONAL_BROWSER_ARGUMENTS",
    "--remote-debugging-port=9222"
);
```

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/) + [Tauri](https://marketplace.visualstudio.com/items?itemName=tauri-apps.tauri-vscode) + [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)
