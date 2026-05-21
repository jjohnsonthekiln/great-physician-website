# `/.well-known/` — Universal Links & Android App Links

These files let `https://thegreatphysician.ai/join/<token>` open the
Great Physician app directly when a user with the app installed taps the
link. They are served by Netlify at:

- https://thegreatphysician.ai/.well-known/apple-app-site-association
- https://thegreatphysician.ai/.well-known/assetlinks.json

The `_headers` file in the site root sets the correct
`Content-Type: application/json` on the AASA file (which has no
extension, so Netlify would otherwise serve it as octet-stream).

## Placeholders to fill in before this works

### `apple-app-site-association`

Replace `REPLACE_APPLE_TEAM_ID` with the 10-character team ID from
Apple Developer (developer.apple.com → Membership Details → Team ID).

Final value should look like `ABC123XYZ9.com.jeffthekiln.GreatPhysician`.

### `assetlinks.json`

Replace `REPLACE_ANDROID_RELEASE_SHA256_FINGERPRINT` with the SHA-256
fingerprint of the **release** signing certificate (the one EAS uses to
sign Play Store builds). Get it with:

```
eas credentials
```

Pick Android → production → "Show keystore credentials" — the fingerprint
is listed there. Format must be uppercase hex with colons every two
characters, e.g. `14:6D:E9:83:...`.

If you ever rotate the signing key, update this file with the new
fingerprint and redeploy the site.

## Validation

After filling in placeholders and deploying:

- iOS AASA validator: https://branch.io/resources/aasa-validator/
- Android validator: https://developers.google.com/digital-asset-links/tools/generator

## Required app build

iOS Universal Links require the `associatedDomains` entitlement, which
ships only with a fresh EAS build (development builds and Expo Go won't
honor it). After updating the Team ID:

1. Push these changes (so the AASA is live)
2. Run `eas build --platform ios --profile production`
3. Submit to TestFlight or App Store; Apple's CDN needs ~24h to refresh
   the AASA cache after first deploy
