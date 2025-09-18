# TAP Website Search using Google Search Console


## Current Behavior

1. **sfdcIFrameOrigin Parameter**
   - Value: `null`
   - Purpose: Normally tracks the origin domain of an iframe embedding Salesforce content.
   - Behavior: Since the value is `null`, Salesforce does not recognize or record a valid iframe origin.

2. **refURL Parameter**
   - Value: `https`
   - Purpose: Typically holds the return URL (encoded) that Salesforce should redirect back to after login or iframe load.
   - Behavior: Only contains the string `https` instead of a full URL (e.g., `https%3A%2F%2Fexample.com/path`).
   - Effect: The system does not have a complete return path, which may cause redirect issues or incomplete navigation.

## Contexts Where This Appears
- **Salesforce Communities / Experience Cloud**  
  Occurs when a page or search redirects but does not capture the correct referring URL.
- **Embedded Visualforce or LWC Pages**  
  Happens if an embedded page does not provide the correct iframe origin.
- **Login / Redirect Flows**  
  The `refURL` should capture the requested page to return after authentication. Truncation prevents correct return behavior.

## Summary
- Salesforce is attempting to capture redirect metadata.
- `sfdcIFrameOrigin` is missing (null).
- `refURL` is incomplete (only "https").
- These conditions suggest misconfigured or truncated redirect handling, leading to broken or incomplete navigation.
