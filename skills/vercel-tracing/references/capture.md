# Targeted capture

Use this reference to reproduce a request or browser flow without changing continuous collection rules. Verify [collection prerequisites](collection.md); framework and custom spans still need deployed instrumentation.

## Capture a request

Target the intended deployment explicitly, using its team and a directory linked to the same project:

```bash
vercel traces create /api/status --deployment https://my-app-abc123.vercel.app --scope my-team
```

`vercel traces create` is an alias for `vercel curl --trace`:

```bash
vercel curl --trace /api/status --deployment https://my-app-abc123.vercel.app --scope my-team --json
```

Replace the path and deployment URL with the affected endpoint. The CLI adds a protection bypass for protected deployments automatically; pass `--protection-bypass <secret>` to use a specific secret. For other protected-deployment access: `⤳ skill: access-protected-vercel-deployment`. Without `--deployment`, the CLI selects the linked project's deployment, preferring production. Production capture prompts for confirmation; `--yes` skips that prompt and is required for noninteractive production capture.

Pass curl options after `--`, for example when reproducing an authorized POST:

```bash
vercel traces create /api/test --deployment https://my-app-abc123.vercel.app --scope my-team -- --request POST --header 'Content-Type: application/json' --data '{"name":"John"}'
```

The command prints a request ID and a suggested command to inspect it. With `--json`, it emits one object with `response` and `requestId`, not search JSON Lines. Extract `.requestId` and [inspect it](inspection.md) in the deployment's project and team; if the result is not the captured request, see [troubleshooting](troubleshooting.md#inspect-returns-the-wrong-trace). The JSON `response` holds the body only; read the status from the trace's `http.status_code`. See [CLI capture](https://vercel.com/docs/cli/traces#create) and [curl tracing options](https://vercel.com/docs/cli/curl#trace).

## Capture a browser flow

Use a preview or production deployment with the Vercel Toolbar enabled:

1. Open **Tracing** in the Toolbar. Select **Run Page Trace** for one page load, or **Start Tracing Session** for a flow; the page reloads to activate capture.
2. Reproduce the flow. Open the completed page trace or **View Session Traces** to inspect captured requests.
3. Select **Stop Tracing Session** when finished. Sessions stay active until stopped or browser cookies are cleared.

See [browser capture instructions](https://vercel.com/docs/tracing/session-tracing). Use [inspection](inspection.md) to choose a trace view.
