# Pull request descriptions (copy the block you need)

These follow [CONTRIBUTING.md](./CONTRIBUTING.md): target `main`, present-tense summary, testing commands, and (for user-visible changes) a screenshot or recording in the GitHub PR body.

---

## A. Core fix PR — `fix/llamacpp-thread-model-openai-fallback`

**Title (suggestion)**

```
fix(llamacpp): thread model OpenAI fallback and related UI/backend updates
```

**Description (paste into GitHub)**

### Summary

Fixes llamacpp thread / model selection behavior (including OpenAI-style fallback), keeps release signing and related workflow adjustments that belong with this change, and **scopes CI “secrets hardening” out** into a separate follow-up PR so review stays focused (per maintainer feedback).

### What changed

- **LlamaCPP extension & plugin**: server flags / sampling wiring, guest JS types, Rust args and tests; permission alignment where needed for the new commands surface.
- **Web app**: model provider / chat input flow, localized model-selection messaging (`selectModelToStartChatting` and related keys), Security config dialog behavior and test stability (wait for security status before tab actions).
- **Tauri / updater**: small signing-related guards and helpers where they tie into release builds.
- **CI**: only changes that remain vs `main` are signing- or build-related (e.g. `JAN_SIGNING_KEY` on external/release paths, linter/test release builds)—**not** the broad explicit `secrets:` forwarding / Discord `workflow_call` hardening (that is intentionally split).

### How to test

From [CONTRIBUTING.md](./CONTRIBUTING.md):

```bash
yarn test
cd src-tauri && cargo test
```

Manually: start a thread with a local llamacpp model, confirm model selection and chat start behave as expected; open Security settings and use Devices / Logs tabs after status loads.

### Screenshots / recording (PR requirement)

Per **Pull Request Requirements** in CONTRIBUTING: attach a short screen recording or screenshots showing **before/after** for the chat/model selection path if the UI behavior changed visibly (e.g. error copy, model picker, security dialog tabs).

### Checklist

- [ ] PR targets `main`
- [ ] Commits use present tense and are scoped to this fix (no unrelated refactors)
- [ ] Tests updated or added where behavior changed
- [ ] Screenshot or recording linked above for user-visible changes

---

## B. Follow-up CI PR — `ci/explicit-workflow-secrets` (workflow secrets hardening)

**Title (exact)**

```
fix(ci): explicit Discord workflow secrets (workflow_call)
```

**Description (paste into GitHub)**

### Summary

This PR is CI-only and hardens reusable-workflow secret handling for the Discord/README notifier path. It explicitly declares required inputs on the reusable workflow and replaces `secrets: inherit` with explicit forwarding in the related (currently commented) nightly caller stubs.

### What changed

- `.github/workflows/template-noti-discord-and-update-url-readme.yml`
  - Uncomments and explicitly declares `workflow_call.secrets` for:
    - `PAT_SERVICE_ACCOUNT`
    - `DISCORD_WEBHOOK`
  - Both are marked `required: false`.
- `.github/workflows/jan-tauri-build-nightly.yaml`
  - In three commented-out caller job blocks, replaces `secrets: inherit` with explicit forwarding of:
    - `PAT_SERVICE_ACCOUNT`
    - `DISCORD_WEBHOOK`

### How to test

- YAML syntax check in CI / GitHub Actions editor.
- Optional functional check when a caller is active: confirm checkout still uses `PAT_SERVICE_ACCOUNT` and Discord notification still receives `DISCORD_WEBHOOK`.
- Note: no active runtime path is changed by the commented caller-block edits.

### Screenshots / recording

Not applicable (CI-only). N/A called out in the PR body to match CONTRIBUTING intent for non-UI work.

### Checklist

- [ ] PR targets `main`
- [ ] Scope is CI workflow YAML only
- [ ] No unrelated app/backend/Rust changes in this PR
- [ ] PR body matches actual diff (no copied description from other PRs)

### Suggested reply to reviewer

Thanks for the review - agreed on the scope mismatch concern.

I updated the PR description to match the actual diff only:
- explicit `workflow_call.secrets` declaration in `template-noti-discord-and-update-url-readme.yml`
- explicit per-secret forwarding (instead of `secrets: inherit`) in the three commented caller stubs in `jan-tauri-build-nightly.yaml`

No product/runtime code is included in this PR, and there is no active behavior change beyond reusable-workflow secret contract clarity.

---

## Issue links

Replace with real links when you open the PR:

- Closes #____
- Related review: split CI secrets hardening from core llamacpp PR
