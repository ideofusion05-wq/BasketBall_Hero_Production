# BasketBall Hero — WebGL (production)

Unity WebGL build of BasketBall Hero, hosted via GitHub Pages. This is the
**production** deployment: it talks to `https://api.g-b.store`. The staging
deployment lives in `ideofusion05-wq/BasketBall_Hero`.

- **Orientation:** landscape. The canvas is always letterboxed to 16:9, so the
  composition is identical on every screen. On touch devices held in portrait a
  "rotate your device" overlay covers the game.
- **Template:** `BasketBallHeroLandscape` (lives in the Unity project under
  `Assets/WebGLTemplates/`).

## The two halves must agree on a host

The build reads `/play/games/{gameId}/context` from two places, and they pick
their host independently:

| Half | What it does | How it picks the host |
|---|---|---|
| C# (`GameBullPartnerAssets`) | the `ball` and `court` custom assets | `GAMEBULL_PRODUCTION` scripting define |
| Template JS (`loadPlatformIcon`) | the loading screen icon | `DEFAULT_API_BASE` in `index.html` |

If they disagree, the bootToken minted by one host is presented to the other,
`/context` answers 401, and the affected half silently falls back to its
built-in artwork. Nothing breaks loudly — which is exactly why it is easy to
miss. For this deployment **both must be production**:

- Player Settings → Other Settings → Scripting Define Symbols (WebGL) contains
  `GAMEBULL_PRODUCTION`
- `index.html` has `DEFAULT_API_BASE = "https://api.g-b.store"`

## Build settings this deployment depends on

- **Compression Format:** Gzip or Brotli
- **Decompression Fallback:** **ON**. GitHub Pages cannot set
  `Content-Encoding`, so without the fallback the browser fails with
  `Unable to parse Build/...` The `.unityweb` extension on the files in `Build/`
  is the sign it is enabled.
- **Run In Background:** ON. Embedded in the platform's iframe the canvas loses
  focus readily, and every focus loss would otherwise pause the round.

## Partner-swappable artwork

Two images can be replaced from the GameBull console, matched by keyword against
`customization.assets[]`:

| Keyword | Replaces | Size |
|---|---|---|
| `ball` | the basketball's texture | 900 × 542 |
| `court` | the court floor's texture | 1254 × 1350 |

The loading screen wallpaper (`TemplateData/loading-background.jpg`) ships with
the build and is **not** swappable — changing it means a new build.
