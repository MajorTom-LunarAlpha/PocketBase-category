# PocketBase Category Add-on

A userscript that adds custom category management to the PocketBase admin sidebar.  
Organize your collections into collapsible, draggable categories for a cleaner and more productive workflow.

This script was developed half by me and half by AI. I didn’t bother to set the styles myself or fine-tune some of the mutators, so some stuff may be a bit broken.

## Features

- **Create custom categories** in the sidebar
- **Drag and drop collections** into categories
- **Collapse/expand** categories for better organization
- **Remove categories** easily
- **Persistent:** Categories are saved in your browser’s localStorage
- **Works on:** `/collections`, `/logs`, and `/settings` pages

## Installation

1. Install a userscript manager like [Violentmonkey](https://violentmonkey.github.io/) or [Tampermonkey](https://www.tampermonkey.net/).
2. Add this script to your userscript manager.
3. The script will automatically activate on PocketBase admin URLs.

## Usage

- Click the **"New category"** button at the bottom of the sidebar to create a category.
- **Drag collections** from the sidebar into your categories.
- **Expand/collapse** categories by clicking their header.
- **Remove** a category by clicking the ✖ icon.
- Collections can be moved between categories or removed from categories by dragging them out.

## Technical Details

- **Persistence:** Categories and their contents are stored in `localStorage` under the key `pb_customCategories`.
- **No server-side changes:** All data is local to your browser.

## Compatibility

- Tested with PocketBase admin UI.
- Should work with most recent browsers and PocketBase versions.

## Issues

- Sometimes the drag and drop stops working.

## Authors

- Major Tom & GPT 4.1
