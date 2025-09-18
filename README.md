# TAP Website Search using Google Search Console


## Site Search Tracking – Current Behavior Analysis

This document describes the current behavior of the **site search implementation** on [taptogo.net](https://www.taptogo.net), focusing on how GA4 and GTM are handling search queries.

---

## 1. GA4 Data Stream Configuration
- **Enhanced Measurement → Site Search** is enabled.
- **Search Term Query Parameters** configured:

- GA4 successfully reads search terms when passed in the URL as `?q=<term>`.

---

## 2. Search Flow Observed

### Initial Search
- User enters search term in the small search widget.
- Example URL after searching **"Parking"**:
- Behavior:
- The page loads with `?q=parking`.
- GA4 records a **view_search_results** event with `search_term = parking`.

### Subsequent Searches (On the Same Page)
- User enters another search term (e.g., **"tips"**) into the widget without leaving the page.
- URL updates dynamically:
- Behavior:
- The original query `?q=parking` remains in the query string.
- The new search term is only reflected in the hash fragment (`#gsc.q=tips`).
- GA4 does **not** capture `gsc.q` values because:
  - GA4 only parses query parameters (before `#`).
  - URL fragments (`#…`) are ignored by GA4’s built-in site search feature.

---

## 3. Impact on Analytics
- **Blank Search Terms**
- When the search page loads without a valid `?q` value, GA4 still fires `view_search_results`, producing blanks in reports.
- **Duplicate Parameters**
- URLs contain both `q` and `gsc.q`, but GA4 only reads `q`.
- **Missed Searches**
- Any searches made *after the first page load* are lost because only the fragment (`gsc.q`) updates, not the query string.

---

## 4. Summary of Issues
1. **First search works** → GA4 captures `?q=<term>`.
2. **Subsequent searches fail** → GA4 ignores `#gsc.q=<term>`.
3. **Blank values in GA4** → Triggered by page loads without `?q=`.
4. **Analytics distortion** → Many searches appear missing or empty.

---

## 5. Next Steps (to be addressed separately)
- Capture `gsc.q` values via GTM (fragment parsing + History Change trigger).
- Decide whether to override the `search_term` parameter or create a new one for widget searches.
- Filter out blank values in GA4 reports for cleaner insights.

---
