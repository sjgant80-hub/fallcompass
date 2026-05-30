# ◊ fallcompass · sovereign LLM cascade

**Drop-in router for 8 LLM providers. First success wins. If Anthropic gates · the cascade keeps your tool alive.**

[**Live landing**](https://sjgant80-hub.github.io/fallcompass/) · [Shim source](./fallcompass-shim.js) · MIT · Part of [AI Native Solutions](https://www.ai-nativesolutions.com) · ◊·κ=1

---

## 60-second install

```html
<script src="https://sjgant80-hub.github.io/fallcompass/fallcompass-shim.js"></script>
<script>
  const r = await FallCompass.chat({ messages: [{ role: 'user', content: 'hi' }] });
  console.log(r.provider, r.ms+'ms', r.reply);
</script>
```

That's it. Your tool now has 8-provider failover.

## The 8 providers (in default cascade order)

| | Provider | Free? | Needs | Notes |
|---|---|---|---|---|
| 1 | **ollama** | ✓ | local server on `127.0.0.1:11434` | your hardware, your model |
| 2 | **fallcore** | ✓ | self-hosted at `127.0.0.1:8787` | Anthropic-compatible proxy |
| 3 | anthropic | – | API key | Claude Haiku 4.5 default |
| 4 | openrouter | partial | API key | has free `:free` models |
| 5 | gemini | – | API key | generous free tier |
| 6 | openai | – | API key | GPT-4o-mini default |
| 7 | mistral | – | API key | EU jurisdiction |
| 8 | **webllm** | ✓ | WebGPU browser | in-page inference, zero network |

The cascade tries them in this order. **First success wins.** If one rate-limits, errors, or returns 4xx/5xx, falls through to the next.

## Override the order per request

```js
FallCompass.chat({
  messages: [...],
  preferredOrder: ['webllm','ollama','anthropic']   // tighter cascade
});
```

## Set keys (stored in localStorage, never leave the device)

```js
FallCompass.setKey('anthropic', 'sk-ant-...');
FallCompass.setKey('openrouter', 'sk-or-...');
```

## Probe what's reachable

```js
const reachable = await FallCompass.probe();
// { ollama: true, anthropic: false, gemini: true, webllm: true, ... }
```

## Listen for cascade events on the fall-signal mesh

```js
new BroadcastChannel('fall-signal').onmessage = e => {
  if (e.data.kind === 'fallcompass:success') console.log('won by', e.data.provider, e.data.ms+'ms');
  if (e.data.kind === 'fallcompass:fail') console.log('skipped', e.data.provider, e.data.error);
};
```

---

## Why this exists · the defensive sovereign play

Every AI app today is one ToS update away from death.

| Threat | fallcompass response |
|---|---|
| Anthropic gates subscription agents | Cascade falls through to OpenRouter / Gemini / Mistral / local |
| OpenAI deprecates GPT-4 mid-product | Auto-routes around the dead endpoint, no code change |
| Country-level block | 4xx detection → next provider |
| Provider rate-limits your key | Falls through silently to next |
| Internet down at airport | WebLLM in-browser answers (slowly, but answers) |
| Compliance forbids cloud LLMs | Set `preferredOrder: ['ollama','fallcore']` — all on-prem |

**This is the model-gating defense.** Combined with [fallmirror](https://github.com/sjgant80-hub/fallmirror) (host gating) and [fallvault](https://github.com/sjgant80-hub/fallvault) (account gating), every estate tool inherits sovereignty by default.

---

## Estate ripple

Every sovereign tool in the AI Native Solutions estate that uses an LLM gets this one-line injection:

```html
<script src="https://sjgant80-hub.github.io/fallcompass/fallcompass-shim.js"></script>
```

Tools currently using LLM directly that benefit immediately:
- ShadowCompass · was Anthropic + WebLLM only → now 8-wide
- FallPost · was BYOK Anthropic/OpenAI/Gemini → now 8-wide
- FallLearn · ditto
- CopyGate · ditto
- FallAccount Trades · was Anthropic vision-only → now 8-wide for text
- The AI Native Solutions hub · "ask the estate" feature → now 8-wide

The ripple is a one-line insertion + verify-before-list per tool.

---

## License

MIT. Fork it, vendor-copy it, modify it.

Built by [AI Native Solutions](https://www.ai-nativesolutions.com). Part of the sovereign estate of 60+ tools. Same philosophy: single-file, you own them outright, no SaaS rent.

◊·κ=1 · prime 379 · v20.1 · φ=1.618 · κ=0.618 · the cascade compounds
