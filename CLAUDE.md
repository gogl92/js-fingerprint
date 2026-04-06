# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

This is a **vanilla JS sample app** demonstrating the `@digitalpersona/fingerprint` SDK. It lives inside the SDK monorepo — the fingerprint SDK dependency resolves to `../..` (the monorepo root), and `eslint.config.js` extends `../../eslint.config`.

## Commands

```bash
npm run build   # Clean dist/ and copy all files into it
npm start       # Build and serve with http-server (opens browser, no cache)
npm run prepare # Rebuild scripts/ from node_modules (run after SDK or websdk changes)
```

No test suite exists in this sample app.

## Architecture

This is a **build-less-capable** app: for quick dev, `index.html` loads both SDKs directly from the unpkg CDN (no local build needed). For production/local SDK testing, the build pipeline copies pre-built SDK files from `node_modules` into `scripts/` and deploys everything to `dist/`.

### SDK integration pattern

Both SDKs are loaded as global scripts (not ES modules). TypeScript typings are pulled in via triple-slash `/// <reference types="..." />` directives at the top of `index.js`. The `Fingerprint` global (from `@digitalpersona/fingerprint`) and `WebSdk` global (from `@digitalpersona/websdk`) are the entry points.

### Template-based rendering

The HTML uses `<template>` elements with a custom `<list>` element. `addItem()` in `index.js` clones a template, then recursively walks its children using the `name` attribute as a dot-notation JSON path (`setData()`) to populate values into controls, media elements, or text nodes.

### Event flow

`Fingerprint.WebApi` emits events (device connect/disconnect, acquisition start/stop, samples acquired, quality reported, errors). All event handlers are bound in `index.js` at startup. Captured fingerprint samples are base64-decoded PNG images displayed in a `<details>` list.
