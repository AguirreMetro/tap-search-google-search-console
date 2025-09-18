# TAP Website Search using Google Search Console

## Site Search Tracking – Current Behavior Analysis

This document describes the current behavior of the **site search implementation** on [taptogo.net](https://www.taptogo.net), focusing on how GA4 and GTM are handling search queries.

---

## 1. GA4 Data Stream Configuration
- **Enhanced Measurement → Site Search** is enabled.
- **Search Term Query Parameters** configured: `q, s, search, query, keyword`
- GA4 successfully reads search terms when passed in the URL as `?q=<term>`.

---

## 2. Search Flow Observed

### Initial Search
- User enters a search term in the navigation menu widget.
- Example: search for **"Parking"** https://www.taptogo.net/TapSearchResults?q=parking#gsc.tab=0&gsc.q=parking&gsc.page=1
- Behavior:
- The page loads with `?q=parking`.
- GA4 records a **view_search_results** event with `search_term = parking`.

### Subsequent Searches (On the Same Page)
- User enters another search term (e.g., **"balance"**) in the TapSearchResults page search bar.
- Example URL: https://www.taptogo.net/TapSearchResults?q=parking#gsc.tab=0&gsc.q=balance&gsc.sort=
- Behavior:
- The original query `?q=parking` remains in the query string.
- The new search term appears only in the hash fragment (`#gsc.q=balance`).
- GA4 does **not** capture `gsc.q` values because:
  - GA4 only parses query parameters before `#`.
  - URL fragments (`#…`) are ignored by GA4’s built-in site search feature.

---

## 3. Issue
- GA4 cannot properly track **subsequent searches** because the `q` parameter never updates.
- This leads to:
- Blank search terms when the page loads without `?q`.
- Duplicate parameters in the URL (`q` vs `gsc.q`).
- Missed search events when only the fragment changes.

---

## 4. Solution
The search experience should consistently update the query parameter (`q`) instead of relying only on the hash.

**Proposed behavior:**
1. User searches via the navigation search bar → redirected to TapSearchResults page with: https://www.taptogo.net/TapSearchResults?q=parking
2. On the TapSearchResults page, any additional searches (e.g., "balance") should also reload the page with: https://www.taptogo.net/TapSearchResults?q=balance
3. 3. This ensures GA4’s site search tracking always captures the latest query via the `q` parameter.

---

## 5. Impact on Analytics
- **Blank Search Terms**: Reduced by eliminating page loads without `q`.
- **Duplicate Parameters**: Simplified URLs with only `q` for consistency.
- **Missed Searches**: GA4 will capture every search, not just the first.

---

## 6. Summary of Issues
1. **First search works** → GA4 captures `?q=<term>`.
2. **Subsequent searches fail** → GA4 ignores `#gsc.q=<term>`.
3. **Blank values** → Triggered by page loads without `?q`.
4. **Analytics distortion** → Many searches appear missing or empty.

---

## 7. Next Steps
- Evaluate feasibility of updating the search widget/page to use `?q=` consistently.
- If not feasible, implement a GTM workaround to parse `gsc.q` from the hash and push it to GA4 as the `search_term`.
- Test changes in GA4 DebugView to ensure accuracy before rollout.


