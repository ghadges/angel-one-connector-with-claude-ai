# Building the bundle

The `.mcpb` file is not committed to this repo — it is built from source and published as a
GitHub Release asset.

## Prerequisites

```bash
npm install -g @anthropic-ai/mcpb
```

## Build

```bash
cd server
npm install --omit=dev
cd ..
mcpb validate manifest.json
mcpb pack . angel-one-connector.mcpb
```

Output: `angel-one-connector.mcpb` (~3.4 MB).

## What gets bundled

`server/package.json` pins [`angel-one-mcp`](https://github.com/ameernoufil/angel-one-mcp) to an
exact version — no `^` or `~`. This is deliberate.

The bundled server receives the user's MPIN and TOTP secret on every run. A floating version
range would let a future release change what runs on their machine without anyone reviewing it.
To take a new upstream version, edit the exact version in `server/package.json`, rebuild, test,
and cut a new release.

## Verifying a build

Before releasing, confirm the safety limits are actually enforced. From `server/`:

```bash
node --input-type=module -e '
const {loadConfig} = await import("./node_modules/angel-one-mcp/build/config.js");
const {validateOrderLimits} = await import("./node_modules/angel-one-mcp/build/guards.js");
Object.assign(process.env, {
  ANGEL_API_KEY:"k", ANGEL_CLIENT_ID:"A1", ANGEL_PASSWORD:"1", ANGEL_TOTP_SECRET:"JBSWY3DPEHPK3PXP",
  SOFT_MAX_ORDER_QTY:"1", HARD_MAX_ORDER_QTY:"1",
  SOFT_MAX_ORDER_VALUE:"1000", HARD_MAX_ORDER_VALUE:"1000",
});
const c = loadConfig();
console.log("qty=5 force=true  ->", validateOrderLimits(c,5,100,true).allowed ? "FAIL" : "blocked");
console.log("val=5000 force=true ->", validateOrderLimits(c,1,5000,true).allowed ? "FAIL" : "blocked");
console.log("qty=1 val=500     ->", validateOrderLimits(c,1,500,false).allowed ? "allowed" : "FAIL");
'
```

Expected: `blocked`, `blocked`, `allowed`. Anything else means the limits are not reaching the
server and the build must not be released.

## Release checklist

- [ ] `mcpb validate manifest.json` passes
- [ ] Limit-enforcement check above gives the expected output
- [ ] Installed and tested on a real Claude Desktop
- [ ] Confirmed credentials are **not** written in plain text to `claude_desktop_config.json`
- [ ] `version` in `manifest.json` bumped
- [ ] Upload the `.mcpb` as the release asset
