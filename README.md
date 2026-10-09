# LOTGEN

The LOTGEN plugin: its MCP connector and the skills for driving
[LOTGEN](https://lotgen.ai) from Claude, Codex, Grok or Cursor — building lots,
writing shots that render what you meant, fixing a take without re-rendering
it, pricing anything before it costs money, and taking a finished lot to a
client.

LOTGEN generates video on **your own provider keys**, priced in dollars and
seconds rather than credits. These skills are the procedure layer over its MCP
server: what to call, in what order, what refuses and why, and which calls
spend.

## Install

One plugin, four clients. Each installs the same skills and connects the same
MCP server.

**Claude Code**

```
/plugin marketplace add ascendedent/lotgen-skills
/plugin install lotgen@lotgen
```

Then run `/mcp` and sign in.

**Codex**

```
codex plugin marketplace add ascendedent/lotgen-skills
```

Then install LOTGEN from the plugin list and sign in when Codex asks.

**Grok Build**

```
grok plugin marketplace add ascendedent/lotgen-skills
```

Then install LOTGEN from `/marketplace`.

**Cursor**: install LOTGEN from the Cursor Marketplace.

Signing in asks LOTGEN for access to one workspace, and you allow it on a
consent screen. The grant is per client, per person, per workspace, and you can
revoke it from the MCP page in the app at any time.

**As plain skill folders**, for any agent that reads a `SKILL.md`:

```
git clone https://github.com/ascendedent/lotgen-skills
cp -r lotgen-skills/plugins/lotgen/skills/* ~/.claude/skills/
```

You will still need to connect the MCP server yourself. The app's `/mcp` page
shows the URL and the three steps.

## What it connects to

- **One MCP server**, `https://mcp.lotgen.ai/mcp`, over streamable HTTP with
  OAuth 2.0. You sign in as yourself; the package holds no credential.
- **One upload host**, `https://api.lotgen.ai`, only when you ask for files on
  your machine to be added to a product. The `upload_product_assets` tool
  answers a one-time link and a `curl` line per file, and your agent's shell
  posts the file there. Nothing is uploaded unless that line runs.
- **Model providers, from LOTGEN's servers, never from your machine.** A render
  sends its prompt and references to the provider whose key you stored in
  LOTGEN, and is billed to that key.

There are no hooks, no scripts, no commands that run on install and no
telemetry in this plugin. What LOTGEN stores and for how long is in its
[privacy policy](https://lotgen.ai/privacy); use of the service is under its
[terms](https://lotgen.ai/terms). Support: [support@lotgen.ai](mailto:support@lotgen.ai).

## What is here

Twenty-five skills. Thirteen sequence the MCP server's 123 tools — between them
they name every one. Twelve teach the craft: how to write a shot, light it, move
the camera, cast a face that holds, sell a product, write words somebody says out
loud, hook a feed, and frame for where it will be watched.

**Making something**

| Skill | For |
|--|--|
| `lotgen-lot` | The craft. What a lot is, how a format, a vertical, a capture style and a look compose, and how to write a shot that renders what you meant |
| `lotgen-campaign` | The pipeline. Library → mix → dry run → build → quote, across many SKUs, ending at a number a person decides on |
| `lotgen-explainer` | Narrated explainers. The planner does **not** research the topic — this says where your real lines go |
| `lotgen-faceless` | Faceless episodes: one Short for a brand's organic series with nobody on camera. Stills the assembly animates, your lines by beat with their sources, and the disclosure to repeat before posting |
| `lotgen-social` | Photodumps and invented influencers. Identity is a held likeness, not a trained model |
| `lotgen-runway` | Runway's seven recipes. Finished pieces from named inputs, on credits that are never refunded |

**The craft**

| Skill | For |
|--|--|
| `lotgen-prompt` | Writing one shot so it renders what you meant — what the model answers to, and what it ignores |
| `lotgen-models` | Choosing among twelve providers, and what each will silently change about your shot |
| `lotgen-vocabulary` | What the capture styles, verticals and cinema axes actually mean, and which typo is silent |
| `lotgen-continuity` | The three levers that make a lot read as one film, and what a chain costs |
| `lotgen-troubleshoot` | It came back wrong: every silent failure, cheapest check first |

**Writing the shot**

| Skill | For |
|--|--|
| `lotgen-hook` | The first second. What has to be legible in frame one, and why the hook shot is worth rendering three times |
| `lotgen-light` | Describing light so a model renders it — direction, quality, and the practical as the most reliable instruction |
| `lotgen-motion` | Camera and subject movement, why a move needs a reason, and why a slow push has no room in four seconds |
| `lotgen-casting` | Describing a person so they stay the same person. Anchors, wardrobe, and why hair is the most volatile attribute |
| `lotgen-product` | Material, reflection and scale — a model renders plastic unless you say otherwise |
| `lotgen-script` | Words per second as the real unit, so a script is a duration before it is a sentence |
| `lotgen-platform` | Framing for the aspect at plan time, because a 9:16 crop keeps under a third of the frame |

**Getting it right**

| Skill | For |
|--|--|
| `lotgen-refs` | References, opening frames, reference packs and the look that ties a lot together |
| `lotgen-voice` | Voices, narration, lipsync and an explainer's audio. Two of these passes are free |
| `lotgen-post` | Fixing a take without re-rendering it: restyle, erase, clean plate, upscale, reframe |
| `lotgen-cost` | What anything costs before it costs it, and what a provider will silently change |

**Living with it**

| Skill | For |
|--|--|
| `lotgen-assets` | Products, brand kits, and Google Drive in both directions |
| `lotgen-workspace` | Lots and shots: reading before writing, reordering, archiving rather than deleting |
| `lotgen-review` | The delivery. Render, mount, share a review link, read the standing, act on a change request, export |

## Two things worth knowing before you start

**Creating a lot never renders and never spends.** Planning, creating,
attaching references and pricing are all free. Money moves only in the tools
that send work to a model provider: rendering a shot or a lot, a quick shot,
voicing, drafting lines, and the paid post passes such as upscale and restyle.
Each of those is marked destructive in its MCP annotations, so your client asks
before it runs one. `estimate_render` prices a shot without touching a
provider.

**No credential is shipped in this repository.** The MCP config here names the
server and nothing else; you authenticate as yourself. Anything that handed you
a token would be handing you somebody else's workspace.

## Where these come from

They are written and tested inside the LOTGEN codebase, where every tool name
in every skill is held against the live MCP catalogue on each run — a skill that
names a tool which does not exist is worse than no skill, because the agent
reading it goes hunting, fails, and improvises. This repository is published
from there and is not edited directly; issues and corrections are welcome at
[support@lotgen.ai](mailto:support@lotgen.ai).

## Licence

Copyright © Ascended Entertainment LLC. These skills are provided for use with
a LOTGEN workspace; see [LICENSE](LICENSE).
