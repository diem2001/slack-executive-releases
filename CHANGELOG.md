# Changelog

All notable changes to **Slack Executive** are documented here. The format
follows [Keep a Changelog](https://keepachangelog.com/), and the project adheres
to [Semantic Versioning](https://semver.org/).

---

## v0.0.9 — 2026-06-13

### Added
- **Richer Brain cards** — every card now shows its type (color-coded border + label), the channels it came from, decision metadata ("decided … · by …"), and a "confidence: low" hint on single-source extractions; sections are ordered by importance (recency-weighted evidence) with counts and explicit "Show more (+50)" paging.
- **Sharper Ask answers** — questions are first parsed into a search query plus person/channel/time filters ("what did Ben decide about pricing in May?"), with safe fallback to the plain question; the parsing prompt is editable under Settings → Skills.

### Fixed
- **Brain stays responsive during updates** — with a large knowledge base the Brain view no longer freezes while an update runs; progress now updates only the progress panel, and cards render progressively as you scroll.

---

## v0.0.8 — 2026-06-12

### Changed
- **Workspace channel list self-heals** — refreshing the channel directory now hides channels the connected workspace does not actually contain (mis-stamped by the one-time migration backfill); they return automatically when their own workspace is reconnected.
- **Workspace-scoped channels** — after reconnecting to a different Slack workspace, channel pickers, sync, digest, Brain and the open-question lifecycle only see the connected workspace's data; the previous workspace's channels, selection, history and open questions are kept frozen and return on reconnect.
- **Paused Brain in the sidebar** — pausing a Brain update keeps its sidebar row visible with Resume and Discard, instead of vanishing until you visit the Brain view.
- **Reasoning control for thinking models** — LLM profiles get a Reasoning dropdown (model decides / off / low / medium / high) sent as `reasoning_effort`; "off" makes Gemma 4-style thinking models answer directly (measured: 0 instead of ~4,000 hidden tokens per call). The profile editor also shows a recommended Mac + LM Studio setup.
- **Smart sync skip (opt-in)** — Settings → Slack can skip the digest's per-channel catch-up fetch for channels synced within the last X minutes, but only while the realtime connection has been uninterrupted since — any disconnect falls back to a normal fetch.
- **Atlassian redesigned (phases 1–3)** — the validation result groups tasks by person (avatar header, one collapsed conversation strip, overdue-first rows, inline draft review with Send/Edit/Discard) under a workflow funnel (Open → Drafts → Reminded → Replies) with one highlighted next action; the overview becomes a work hub: status line, "Needs you now" aggregation across all lists, a next-step column per list, an add-list dialog, plus an activity feed and automation card.
- **Digest progress as a 4-phase stepper** — the run now shows its phases (Fetch from Slack → Summarize → Overall picture → Check open questions) with AI/no-AI badges, verb sentences ("Fetching new messages from Slack — #channel"), a forward-only segmented bar, and the previously invisible open-question check as its own step; the sidebar indicator shows "Step 2/4 · AI".
- **Embedding model visibility** — LLM profile cards now show the configured embedding model, and the Test button probes it too, reporting "Embedding OK (N dimensions)" or its error separately from the chat model.
- **Human-style reminder links** — reminder DMs now paste the bare Confluence URL like a person would, instead of a formatted "Open the page" link (template and AI-generated reminders alike).

### Fixed
- **7-day digests no longer collapse** — items whose source refs lack the Trigger role are repaired deterministically instead of emptying whole channels, and one bad citation no longer discards the entire executive summary (the narrative survives, only the broken link is dropped).
- **Raw Slack ids healed everywhere** — historical question texts and resolutions ("U05CP… confirmed …") now render display names at read time, and reconcile inputs are healed so new resolutions speak in names.
- **Channel names in sync progress** — the digest progress bar shows "#channel-name" during the fetch phase instead of the raw Slack channel id.
- **Done is an indication, not a result** — a reply classified as "done" now reads "reports done — awaiting Confluence check-off"; only the checked-off task on the Confluence page (read by Validate) counts as completed.
- **Stricter reply classification** — a reply only marks a task complete when it explicitly says the task was done; vague pleasantries and acknowledgments ("worked!", "I know!", emojis) now stay unclear for a human to judge instead of silently closing the task.
- **Readable names in digests and resolved questions** — Slack user ids (`U05CP…`) in message text are now resolved to display names before the AI reads them, so summaries and resolution texts say "Katharina asked …" instead of echoing cryptic ids.

---

## v0.0.7 — 2026-06-12

### Added
- **Onboarding redesigned end-to-end** — welcome screen with a clear method choice, Chrome import with a clean credentials summary (no more layout-breaking masked tokens), a workspace picker when Chrome is signed in to several Slack workspaces, channel selection with the Brain/Digest scopes, the model step shows your real profiles (or creates the first one with the recommended Mac setup), and the finish step asks before running the first digest — every error state now offers concrete ways forward.
- **Background-run visibility** — scheduled digest and nightly Brain runs now show a live sidebar indicator from anywhere in the app, with progress, Cancel (and Pause for the Brain); clicking it opens the same detail panel as a manual run.
- **Resizable channel list** — the divider between the Chats channel list and the messages can be dragged (200–480 px, arrow keys work too, double-click resets); the position survives app restarts.
- **Schedule sanity warnings** — digest run times outside the activity window are flagged right in the editor (live, even against unsaved window edits), with a hint showing the effective window and the Stealth jitter instead of silently skipping the run.
- **Per-channel digest scope** — subscribed channels always feed the Brain; whether each one is also summarized in the digest is now a per-channel switch in Settings → Channels (with bulk on/off). Existing channels stay in the digest; newly subscribed ones start Brain-only.

### Changed
- **Brain evidence opens in-app** — Slack source links in Ask answers, conflicts, and watch alerts now open the internal Chats overlay at the message (like card sources) instead of jumping to the external Slack client; non-Slack links (Confluence) still open externally.

### Fixed
- **Honest Brain parallelism display** — the progress panel now says "N chunks in parallel" (the true unit) and each channel chip shows ×N when it occupies several slots, so the chips always add up to the headline.
- **Visible LLM provider errors** — a provider that responds with an error (wrong model id, no model loaded, bad API key) now aborts digest and Brain runs with a clear message instead of silently producing an empty digest.
- **Thinking models (Gemma 4, DeepSeek-R1, …)** — leaked inline thought blocks from local servers (e.g. LM Studio's default Gemma 4 parser config) are now stripped before any output is used, so digests can't parse a reasoning draft and Slack drafts never contain chain-of-thought.
- **Complete backups** — config export/import now covers the Atlassian connection (incl. token), skill addenda, Brain/digest/appearance settings, and (in file backups) user matches, action lists and the reminder history — a restore reproduces the configured app and never re-reminds anyone.
- **Table action lists** — tasks in Confluence tables (e.g. "What | When | Who") now pick up their assignee and due date from the row's columns (English + German headers, with a fallback scan), so they validate and remind like inline tasks.
- **User matching works again** — assignee matching now resolves emails against the local Slack directory instead of a Slack API call that always failed with `not_allowed_token_type`; "Re-resolve assignees" refreshes the directory first, and unmatched assignees now show their Atlassian name instead of a raw account id.
- **Unremindable sub-line** — tasks without a detectable assignee now read "No assignee detected — no matched Slack user" instead of a dangling "· no matched Slack user".

---

## v0.0.6 — 2026-06-11

### Added
- **Sidebar navigation** — a macOS source-list sidebar replaces the titlebar buttons: grouped views, ⌘1–⌘5 in visual order, Atlassian drill-in entries with a pending badge, and a plain-language connection status ("Live"/"Offline").
- **Command palette** — press ⌘K to jump to any view, open a channel in Chats, regenerate the digest, update the Brain, or check for updates.
- **Onboarding** — new 3-step setup (Connect Slack → Pick channels → Choose AI model) with Chrome import as the recommended path, masked manual entry with a find-your-token guide, a privacy banner, and a first-digest kickoff.
- **Activity window editor** — edit days, start/end time, and timezone directly in Settings → Behavior.
- **Slack reconnect** — an expired Slack session shows a clear reconnect screen instead of scattered error banners.
- **Keyboard list navigation** — arrow keys + Enter open digest items and select channels in Chats.
- **Debug tracing privacy** — enabling full tracing shows a persistent plain-text warning; log files auto-delete after a configurable 3/7/14/30 days.
- **Ask examples** — the Brain's Ask tab offers clickable example questions when empty.
- **Brain card corrections** — Edit, fix details, merge, and split now open a real dialog (the old prompts silently did nothing); merge offers a searchable card picker.
- **Localized timestamps** — dates, times, and "open for N days" labels now follow the app language (English/German) instead of always rendering German formats.
- **Backup encryption** — config exports now use hardened key derivation (Argon2id, 64 MiB) and require a 12+ character passphrase; existing backups still import.

### Changed
- **Digest drilldown** — message details open in a right-hand inspector panel; the view behind stays usable, and "Open in Chats" swaps instead of stacking dialogs.
- **Clearer wording** — friendlier Brain header/Ask/Conflicts copy, plainer Atlassian task badges, and helpful guidance when a Confluence link isn't recognized.
- **Faster Chats and Logs** — user names resolve in one batched query instead of one per message; the Logs view tails files instead of re-reading them whole; the connection indicator updates via push instead of polling.
- **Chats join/leave collapsing** — consecutive join/leave system messages collapse into one expandable summary row.
- **Light mode** — status banners, badges, and labels now adapt to the system appearance instead of showing unreadable text.
- **Faster startup** — the app loads ~47% less code up front; Settings, Brain, Logs, and emoji data now load on first use.
- **Brain schedule** — the nightly Brain extraction time is configurable in Settings → Brain (default 03:00); changes apply immediately.
- **Digest schedule** — daily Executive Digest run times are freely configurable in Settings → Behavior: add or remove any number of time slots (empty = manual only); changes apply live.
- **Cancellable runs** — digest generation gets a Cancel button; the Brain progress panel offers both Pause (keeps a "Paused — X/Y" panel with Resume and Discard) and Cancel; all stop within a second even on a dead network, and the next run resumes where it left off.
- **Native menu bar** — full macOS menu (App/File/Edit/View/Go/Window/Help): ⌘1–⌘5 view switching, ⌘R refreshes the stored digest, ⌘⇧R runs a full regeneration, ⌘K opens the palette; the menu switches language instantly with the app language.
- **Localized error messages** — backup, passphrase, LLM-URL, and Confluence errors now follow the app language in English and German.
- **Appearance switcher** — Settings → General offers System/Light/Dark; applied live including native window chrome, flash-free at startup.
- **Brain reset** — a danger zone in Settings → Brain rebuilds the knowledge base from scratch (cards, history, embeddings, run state); watches and settings stay.
- **Brain integrity** — re-running over old messages can no longer flip active knowledge back to outdated content or stack duplicate hidden cards; hidden cards show why they're hidden ("superseded by …" with date).
- **Safer autopilot validate** — Validate only dispatches the validated list's own due reminders (never another list's or review drafts) and asks for confirmation first.
- **Review queue polish** — edits are always saved before sending, Send all confirms and shows a success panel, failed drafts get Retry, drafts show saved/AI-generated hints.
- **Atlassian user matching** — Settings centers on action-list assignees: unresolved Confluence assignees listed first with a searchable manual Slack match; "Re-resolve assignees" retries matching without touching manual ones.
- **Atlassian polish** — inline validate errors, busy rows, Remove confirmation, validation-age and draft-count badges, filterable summary chips, pill tooltips, overdue highlighting, "Fix matching" shortcut, unified German terminology.
- **Per-task reminder timeline** — each task on the Validation Result screen shows its reminder/reply history as expandable chips with outcome colors; the page-level History panel is gone.
- **Matching fixes from live testing** — matched rows show who they're matched to and can be re-matched via "Change…"; unreachable (deactivated) recipients get an explanatory hint; replies in self-DMs are now detected; dismissed questions no longer linger in the NEW column.
- **Chats performance** — live message updates re-render only the affected conversation.
- **Find (⌘F)** — Edit → Find focuses the search field in Chats and Logs.
- **Digest extras** — the header shows when the next scheduled digest runs; clicking the digest notification opens the Digest view; the sidebar shows an "Offline" indicator when Slack is unreachable.

### Fixed
- **Brain card corrections** — edit, fix details, merge, and split actually work now (the old prompts silently did nothing) via a proper dialog with a card picker for merge.
- **Atlassian badge** — the connected badge no longer shows "?" when the cached identity is missing; it self-refreshes and falls back to the email name.
- **Brain & digest updates** — runs now stop within seconds with a clear "Can't reach the AI model" error when the LLM server is offline, instead of spinning forever at 0 progress.
- **Logs timestamps** — log rows show times in the Mac's local timezone instead of UTC.
- **LLM timing in human units** — every LLM duration (profile timeout setting, test result, digest trace latencies) shows seconds/minutes instead of raw milliseconds.
- **Digest trace retry grouping** — transport retries of the same request render as one call with per-attempt detail rows ("Try 1 — transient · Try 2 — ok") instead of a standalone failed row.
- **Logs detail overlay** — no longer covers the sidebar; it dims only the content area and opens as a right-side sheet.
- **Atlassian screens** — page layout aligned with the rest of the app, back affordance moved top-left, action toolbar tidied; the review-queue screen no longer shows German text in the English UI.
- **Window size** — the window now restores your last size and position deterministically (first launch: 1100×750).

### Fixed
- **Startup** — a failed startup load now shows an error message with a Retry button instead of hanging on the loading screen.

---

## v0.0.5 — 2026-06-11

### Added
- **Live parallel digest progress** — while generating, the progress panel now shows the channels being summarized concurrently as chips ("Summarizing N channels in parallel"), making the parallel work visible.
- **Background automations overview** — each settings area now shows its scheduled background job and cadence (read-only): digest + activity window in Behavior, response check in Atlassian, knowledge update in Brain, update check in Updates.
- **Executive summary** — the digest now opens with a full-width C-level narrative (for a CEO/CIO/CTO audience) that synthesizes the day, above the per-topic and per-channel detail.
- **Open questions on every run** — manually generated digests now track the open-question lifecycle (new / still open / recently resolved) just like scheduled runs, and each open question links straight to its source messages.
- **Jump from open questions to source** — every open-question row now opens its originating Slack messages in the drill-in, the same way topic and channel items do.
- **Open in Chats** — the source drill-in now has an "Open in Chats" button beside "Open in Slack" that opens a resizable in-app overlay on the local archive, scrolled to the message so you can read the surrounding conversation and scroll freely through the channel.
- **Source drill-in is now a resizable overlay** — opening a digest item's sources no longer navigates away; it opens a resizable overlay on top of the digest, closable with Escape, the backdrop, or "Back" (the Chats overlay closes the same way).
- **Hide empty channel boxes** — Settings → Behavior can now hide per-channel digest boxes that have nothing to report, for a more compact overview (shown by default).
- **Tidier noise** — repetitive social chatter (greetings, joins) now collapses into a single counted line instead of one item per person.
- **Readable source timestamps** — the source-messages drill-in shows a local date/time instead of the raw Slack timestamp; click it to copy the underlying message ID.
- **Filter channels to your selection** — the Channels settings list now has a "Selected only" toggle alongside search, to quickly review the channels used for digests and chats.
- **Flush logs** — the Logs view now has a Flush button beside Live to clear the saved log files in one click.
- **Skills** — a new Settings → Skills area lists every AI prompt the app uses (with where it's used), lets you view each default prompt, and add your own per-skill addendum that is appended to the prompt on every call and survives app updates.
- **Manual Brain update with live progress** — the Brain page now has an "Update now" button (also from the empty state) with a live panel showing what the run is doing — reading channels, building roll-ups, indexing, checking changes — like the digest progress, including the channels being analyzed in parallel as chips. The extraction runs in parallel bounded by the LLM profile's concurrency limit, and you can Pause it: it stops promptly and the next update resumes where it left off.
- **Brain cards link in-app + show real names** — a Brain card's sources now open the surrounding conversation in the in-app Chats overlay (no external Slack link), source channels show as `#name` instead of an id, and person cards resolve the Slack user id to the person's name.
- **Backup import now restores (replaces), not merges** — importing a config makes this system match the backup exactly: LLM profiles, channel selection and settings not in the backup are removed (message history is always kept), and a safety backup of your previous configuration is created automatically first, using the same passphrase.

- **Brain settings tab** — Settings → Brain lets you enable the Slack knowledge base, opt in to including direct messages (off by default, with a confirmation naming the active model), see a best-effort local-vs-cloud hint for the active model, and toggle nightly auto-update.
- **Direct messages in the Brain** — with the Brain DM opt-in on, your 1:1 and group DMs are now ingested into the knowledge base alongside channels; off by default, and DMs are never marked read.
- **Nightly knowledge extraction** — the Brain now distills new Slack messages into cited Project, Decision, Action, Fact, Person, and Risk cards each night (or on demand), gated by your Brain settings.
- **Brain view (⌘6)** — browse the Brain's Projects, Decisions, Actions, and Risks in the Overview, plus Glossary and People tabs, each card linking back to its Slack sources, and ask questions to get a cited answer.
- **Supersession** — a newer decision now supersedes an older contradicting one; the Overview shows the current card with a "supersedes" note and the old card struck through.
- **Card correction & trust** — every Brain card now has a `⋯` menu to confirm, edit, fix an action's assignee/date, mark outdated, merge, split, or dismiss it, plus a verification badge (machine / confirmed / corrected / disputed / dismissed). Every correction is logged and fully reversible from a new History tab that lists dismissed and superseded cards with a per-action Undo.
- **Locks, suppressions & answer feedback** — dismissed cards stay dismissed across nightly runs, confirmed/corrected cards are protected from machine rewrites, contradictions surface in a new Conflicts tab to Keep current or Supersede, and thumbs up/down on a cited answer is recorded (a down marks the cited cards disputed).
- **Semantic retrieval foundation** — the Brain now builds embeddings for its knowledge base, with a friendly notice when the active profile can't embed.
- **Overview filters & project roll-ups** — the Brain Overview now has filters for card type, channel vs. DM source, and a date window that compose and update the list (Clear restores everything), and each Project card shows a regenerated roll-up summary of its decisions, actions, and risks.
- **Hybrid Brain Ask** — semantic (meaning-based) search now complements keyword search, so a question phrased differently from the message text still finds the right cited card; each source shows whether it was found by keyword, meaning, or both, low-relevance semantic matches are filtered out, and Ask degrades to keyword-only when embeddings are unavailable.
- **Watch a topic** — watch a topic in the Brain and get an in-app alert (old → new) when a nightly run changes a matching card, with an optional self Slack DM (off by default).
- **Action Lists open-count badge** — each tracked Atlassian list now shows a status badge (amber "N open", green "all complete", neutral until first validated).
- **Validation Result screen** — clicking "View result" on a tracked Atlassian list opens a dedicated screen with the list title, a validated-on/mode/counts line, summary badges, and a per-task status pill (Done / Reminded / Pending review / Sending / Failed / Snoozed / Open / Unremindable); "Open in Confluence" jumps straight to the source page, Re-validate, Check responses, and per-row Retry refresh it in place, and Back returns to the overview.
- **Reminder Review Queue screen** — in Review mode, "Review drafts" now opens a dedicated queue for that list with an "N drafts pending" count, editable draft cards (assignee, open-to-do count, follow-up badge), per-card Send / Discard, and a "Discard all" beside "Send all"; both bulk actions are scoped to the current list, a failed Send stays visible with its reason, and discarding leaves the tasks' reminder state untouched.
- **Remind again** — when a task stays open after its assignee was already reminded, the Validation Result screen now offers "Remind again" per open task and "Remind all again" for the whole list; both reopen a fresh follow-up draft (regenerated wording, covering that person's open tasks) in the Review Queue, so nothing is sent until you click Send. Each action shows progress while it runs and a confirmation pointing to "Review drafts".
- **Reminder history shows the real message** — the per-list history now displays the exact DM that was delivered to each assignee (not just the task titles), so you can see what was actually said.
- **Reply visibility** — when "Check responses" detects an assignee's Slack reply, the task now shows their actual message and its effect (marked complete / snoozed / no change — including replies the model couldn't classify), and the check reports how many replies it evaluated.
- **Decline + deadline risk** — replies are now classified into done / will-do / **declined** / unclear (vacation/busy is read as a deferral with an estimated snooze), and a task whose assignee declined, or deferred past its due date, gets a red **"Risk"** badge so a threatened deadline is obvious. The number of recent messages checked after a reminder is now a configurable "Reply window" (Settings → Atlassian, default 10).
- **Reasoning-model support** — Settings → LLM now has a "Min output tokens" field (default 2048): the provider raises every call's token budget to at least this, so reasoning models (MiniMax-M2, DeepSeek-R1, o-series) — which spend their budget on hidden reasoning before answering — no longer come back empty and silently fail features like reply classification or the Brain.

### Changed
- **Behavior settings save instantly** — the Behavior settings tab now auto-saves every change (mode, activity window, RTM, mark-as-read, expiry) like the rest of Settings; the separate Save button is gone, so the Digest-display and automation cards below it are no longer stranded under it.
- **Tidier Settings tabs** — Behavior now sits right after General, the Channels picker moved into the Slack tab and the Debug section into the General tab, so the settings tab bar no longer overflows.

### Fixed
- **Local-LLM connection failures during digests** — the HTTP client no longer reuses idle keep-alive connections, which a local server (LM Studio) closes under load; this removes the instant "error sending request" failures that left channel summaries empty. (Pair with a higher per-profile timeout for slow local models.)
- **"Check responses" no longer flags a benign workspace mismatch as an error** — a reminder whose assignee isn't in the active Slack workspace (e.g. after switching workspaces / importing a backup) is now reported as skipped, not as an error.
- **Digest parallelism no longer collapses with debug logging on** — with tracing enabled, the per-channel digest summaries now still run in parallel up to the profile's configured concurrency instead of silently one-at-a-time.

- **Executive Digest "Themen" no longer vanishes with weaker/local models** — the cross-channel executive summary stays populated even when the model omits optional item fields, instead of silently collapsing to an empty Themen section and a generic headline.
- **User matching now finds Atlassian accounts** — matching uses the correct Jira Cloud endpoint and works even when Atlassian hides email addresses, instead of marking every user "Kein Atlassian-User"; a lookup failure now surfaces an error rather than silently mislabeling everyone.
- **Digest progress shows instantly on "Neu erstellen"** — the progress panel now appears the moment you click, even with the realtime stream live, instead of staying blank until the first backend update.
- **Atlassian badge keeps the connected account name** — Settings → Atlassian now shows your name after a restart instead of "Verbunden als ?", and clears the cached name when you change credentials or disconnect.

---


## v0.0.4 — TBD

### Added

- **DM read + write capability** — the Slack client can now open a DM with a user, post a plaintext message to it, and read messages back, with the same `BehaviorGate` enforcement, secret-free logging, and DM self-/bot-/subtype-filter on read that the rest of the client already has. Foundation for the Autopilot reminder dispatch (MVP-4701), the AI-response reply detector (MVP-4706), and the opt-in watch-alert self-DM (MVP-4760).
- **User matching** — Settings → Atlassian tab now lists each Slack user with their Atlassian account match status (Matched / No email / No Atlassian user) and persists the snapshot between runs. Click „Run matching" to walk the workspace's Slack users and resolve each email against the Atlassian user search; reopen the tab later to render the saved snapshot without re-running the routine.
- **Action-list validation + reminder dispatch** — a new "Validate" action on every tracked Confluence action list fetches the page's inline tasks, UPSERTs them per-page, matches each assignee against the Slack snapshot, groups the remindable open tasks per (assignee, page) into a single grouped DM, and (in Autopilot mode) dispatches it in the same call. The results panel renders per-task Open/Done, per-assignee Matched / Unremindable-with-reason, and per-grouped-reminder Reminded / Skipped / Pending / Failed-with-manual-Retry pills — all from a single typed snapshot. Failed rows expose a manual Retry that always re-sends immediately (no retry-ladder).
- **Review queue for Atlassian reminders** — in Review dispatch mode, generated reminders now appear as editable drafts in a per-page review queue. Each draft shows the assignee, the covered open tasks, the composed message in an editable textarea, and Send / Send-all / Discard actions. Send reuses the dispatcher's single-row claim + outcome commit (preserving the no-double-send contract); discard leaves task state unchanged and is terminal; Send all dispatches each pending draft with per-draft failure isolation. Re-validating the same page UPSERTs pending drafts and preserves user-edited text.
- **Reply detection for Atlassian reminders** — a 24h sweep + ad-hoc "Check responses now" action reads each sent DM, classifies the assignee's reply via the active LLM, and writes Done / Snoozed-until-date / No-reply on every covered task. A `will_do` with no parseable date defaults to 3 days; the MVP-4701 dedup respects the snooze so no follow-up DM is sent while the window is open.

### Changed

- **Logs view** — the log detail panel is now configurable: pick *Overlay* (full-window, default) or *Side panel* (420px) under Settings → General → Logs. The overlay is *modeless*: the Logs filter bar stays visible and clickable on top of it, Tab navigation flows through the filter buttons, and pressing Escape still closes the panel. The list pauses its live tail while the overlay is open.

### Fixed

- **Auto-update failed to unpack `._Slack Executive.app`** — in-app updates from a previous v0.0.3 install could fail to unpack and leave a stray AppleDouble file next to the app. The updater tarball now strips macOS-only extended attributes that previously leaked into the archive, and a release-pipeline guard aborts the publish if any xattr or AppleDouble member ever shows up in the tarball again.
- **Atlassian tab inputs rendered light-themed in dark mode** — the Site-URL, E-Mail, and API-Token fields in Settings → Atlassian now use the shared theme-aware `Input` component, so they pick up the app's dark background and border colors instead of staying white.
- **Atlassian "Speichern" failed with `invalid args: missing field 'site_url'`** — the `atlassian_save_creds` Tauri command now accepts the camelCase wire format the TypeScript wrapper sends (`siteUrl` / `email` / `token`) instead of silently rejecting it. Saving Atlassian Cloud credentials in Settings → Atlassian now works on the first click. The Rust struct carries `#[serde(rename_all = "camelCase")]` plus snake_case aliases for backward compatibility, and a Rust contract test pins the wire format so the regression cannot silently recur.
- **Digest header width** — on wide windows the Executive Digest header and its divider now span the full content width like the cards below, instead of stopping at a fixed ~768px; the headline text stays capped at `max-w-3xl` for readable line length.

---

## v0.0.3 — 2026-06-07

### Added

- **Auto-expire for open questions** — Settings → Behavior now exposes a "Auto-Ablauf offener Fragen" card with a master toggle and a day-count input. When enabled, the scheduled digest sweep transitions open, automation-managed questions whose `last_activity_at` is older than the configured horizon to `expired`; the horizon ages by the question's last activity (not the last reconciliation check), so repeated checks with no new evidence still expire after N days, and manually-pinned questions are never touched. Default is opt-in at 7 days.
- **Internationalization** — UI chrome and main views (sidebar nav, Digest / Chats / Drilldown / Logs views, startup loader) are now available in German and English. Switch via the new Settings → General → Language selector; the resolver also auto-detects the macOS system locale. Digest notifications are localized on the Rust side. The Settings tabs (Slack, Channels, LLM Profiles, Behavior, Debug, Backup, Updates) and the Onboarding flow are now fully translated, including behavior-mode hints, channel-selection empty/error states, and the import success/failure toasts. The late migrations (the global update reminder banner and the Logs detail panel) are now translated end-to-end, and a `npm run i18n:check` guardrail (with a curated umlaut + denylist scan) prevents new hardcoded German UI strings from regressing in any component file outside the i18n catalogs.
- **Reconciliation** — the scheduled digest now re-checks every still-open tracked question against its anchor thread and the current window, classifying each as still open / resolved (with the answering text) / stale, so questions answered outside the rolling 24h window are correctly marked resolved instead of silently dropped. Touches the storage layer, the pipeline, and a new reconciliation prompt — no UI change in this release.
- **Open-question lifecycle in Digest** — the digest view now shows an "Offene Fragen — Verlauf" section between Themen and Pro Kanal with three sub-cards: newly opened this window, still open across windows (with age), and recently resolved (with the answer). The section is derived deterministically from `tracked_questions` and is additive — the per-channel "Offene Fragen" bucket in each ChannelCard is unchanged.
- **Manual dismiss and reopen of tracked questions** — open and still-open questions can be dismissed with a "Verwerfen" button (the question is pinned to a new "Verworfen" group and protected from re-surfacing by reconciliation or auto-expire) and re-opened from the same panel or from the "Beantwortet" group via a "Wieder öffnen" button. The dismissed state is the source of truth, persists across digest refreshes, and the reopen path re-bumps `last_activity_at` so a just-reopened question is not immediately auto-expired.
- **Tracked questions in the history backup** — the v2 file backup (`include_history`) now carries tracked open questions as a fourth NDJSON table alongside messages, users, and digests; the import summary surfaces the new count ("`N offene Fragen`" / "`N tracked questions`"), and a backup that only carried tracked questions still renders the history block. Restoring a backup never undismisses a question the user has already touched (the local human-decision state wins on conflict) and never moves the `first_seen_at` inactivity clock backward. v1 / pre-MVP-4846 v2 backups import unchanged — the new field is gated by `#[serde(default)]`.
- **Logs-Detail-Panel** — click a log row to open a side panel that renders the `fields` JSON as an interpreted, syntax-highlighted, collapsible tree; object/array nodes are expandable with a chevron and a `N Elemente`/`N Felder` count, and the panel includes a `Kopieren` button that pretty-prints the reconstructed JSON to the clipboard. Truncated, unparseable, and depth-limited values show an inline badge (the marker is the same `…` the backend uses), so the warning is never silently discarded.
- **Max parallel calls in LLM profile settings** — the Settings → LLM-Profile form now exposes a "Max. parallele Aufrufe" / "Max parallel calls" numeric input (1–16) alongside the existing Max context / Temperature / Timeout fields; saved values are persisted and pre-filled when re-opening the profile for editing.
- **Atlassian Cloud connection** — Settings → Atlassian stores the site URL, email, and API token (Keychain-only for the token), validates both Jira and Confluence, and exposes an Autopilot / Review dispatch-mode toggle for downstream Confluence action-list reminders. Token is masked and never crosses the IPC boundary back to the UI.
- **Atlassian section** — a new "Atlassian" navigation area lets the user add and manage Confluence action lists. Lists persist across restarts and open in the browser with one click. The add form accepts a full Confluence URL or a bare page id; a 404 / 403 / unparseable response surfaces the same inline error the user would see anywhere else in the app.

### Changed

- **Digest regeneration** — per-channel summarization now runs concurrently up to the LLM profile's "Max. parallele Aufrufe" / "Max parallel calls" value, so regenerating a digest across many selected channels finishes noticeably faster on multi-channel workspaces. The progress bar advances smoothly with one tick per channel completion, and the saved digests and aggregate output are byte-for-byte identical to the previous sequential behavior (the default of 1 keeps the old single-channel-at-a-time path).
- **Logs** — Slack API debug entries now show up to 16 KiB of the redacted response body with a trailing `…` marker when truncated (was: 500 chars).

### Fixed

- **Auto-update failed to unpack `._Slack Executive.app`** — in-app updates from a v0.0.3 install could fail to unpack and leave a stray AppleDouble file next to the app. The updater tarball now strips macOS-only extended attributes that previously leaked into the archive; affected installs can re-check for updates and pull the now-clean artifact.
- **Changelog rendered as a wall of text in the Updates tab** — Settings → Updates now renders the changelog from `latest.json` as real Markdown (headings, bullet lists, bold runs, paragraph breaks). A bespoke safe renderer escapes any HTML in release notes instead of inserting it; the release manifest now ships multiline Markdown so the renderer has structure to work with.

---

## v0.0.2 — 2026-06-06

### Added

- **In-app auto-update** — the app checks GitHub Releases for a newer signed build (hourly + on demand) and offers to download and install it from within Slack Executive; the Settings → Updates download bar shows a live byte counter (`12,3 MB / 45 MB` with a known size, `12,3 MB` otherwise) instead of an indeterminate „läuft…".
- **Channel Sync** — right-click a channel in the Chats view and choose „Sync" to pull its full history on demand, with a live „Synchronisiere…" indicator, „Abbrechen", and an automatic count refresh when it finishes; synced history is fully browsable — scroll to the top of a channel to lazy-load older messages until the start of the history.
- **Channel search** — filter the channel list by name, both in the Chats view and in Settings → Kanäle; in Settings, ticked channels stay selected while you search and the total count stays visible.
- **Backup with history** — the encrypted file export can now optionally include the message history (messages, users, digests, sync state), so a restore continues where you left off instead of re-syncing.
- **RTM connection in Settings** — Settings → Slack now shows the real-time connection status including the disconnect reason, and offers a reconnect button to restore the connection manually.

### Changed

- **Backup settings** — Settings → Backup has an "Include message history" option (file export only) with a size and privacy note; the import shows the restored history counts.
- **Logs** — Slack API debug entries now show the request parameters and a 500-character response excerpt (secrets redacted).
- **Slack connection status** — Settings → Slack now shows the readable workspace and user name instead of the cryptic `T…`/`U…` IDs (with an ID fallback for older connections).

### Fixed

- **Window dragging (macOS)** — the window can now be moved by dragging the titlebar; previously dragging over the title, RTM dot, and header area did nothing because the required window permission was missing.
- **LLM connection test with reasoning models** — the "Testen" button now works with reasoning models (MiniMax-M2, DeepSeek-R1, and others); truncated responses clearly report that `max_tokens` must be raised instead of "no content".
- **Digest progress** — the live generation progress now stays visible when you switch tabs during a run and come back, instead of disappearing until the digest is ready; during the catch-up sync phase the progress bar shows a step readout (`Rufe Nachrichten ab — Kanal 4/20`) and the aggregate stage no longer flashes a misleading `100%`.
- **Digest evidence links** — clicking a digest item or by-topic entry now always opens the source messages and „In Slack öffnen" link for that exact item, instead of occasionally jumping to a different channel's item when two channels shared an item number.
- **Answered questions in digests** — a question answered in a thread reply is now recognized as resolved and no longer shows up under „Offene Fragen", even when the answer falls just outside the digest's time window.
- **Digest drilldown** — source messages in the drilldown now render with resolved @-mentions, clickable links, and Slack formatting (bold/italic/code/emoji) — like the Chats view, instead of raw tokens.
- **RTM stays connected** — the real-time connection no longer flaps (repeatedly disconnecting "via reconciler") while the app is open outside the activity window; RTM now follows the real-time setting and app visibility, not work hours.

---

## v0.0.1 — 2026-06-05

First tagged release. Apple Silicon (`aarch64-apple-darwin`), signed Developer ID.

### Added

- **Executive digest** — multi-stage LLM pipeline summarizes selected channels into Decisions / Open Questions / FYI / Noise, with a time-range selector (presets + custom Von/Bis picker) and live generation progress.
- **Real-time ingestion** — RTM connection captures new messages live via the modern Slack gateway, with reconnect backfill, thread reconciliation, and an overlap window so connection errors don't drop data. Live status indicator in the header.
- **Chats view** — DB-backed browser for every tracked channel and its threads in a Slack-like layout, with full `mrkdwn` rendering and live refresh.
- **Logs view** — filterable, newest-first tail of the structured app log.
- **Public/private channels** — `#` vs lock icon across the digest, Chats, and Settings.
- **Provider-agnostic LLM** — Anthropic and any OpenAI-compatible endpoint (LM Studio, Ollama, vLLM, OpenRouter), with multiple profiles.
- **LLM debug tracing** — inspect the exact payloads sent to the model and surfaced call failures; toggle in Settings.
- **Encrypted backup** — passphrase-protected (argon2id + AES-256-GCM) full-config export/import as a file.
- **Drilldown** — click a digest item to see its source messages, thread context, and open the message in the Slack web app.
- **Opt-in read-state sync** — `conversations.mark` for low-risk channels, off by default, with a mark history.
- **Integrated macOS titlebar** — traffic lights centered on the header text.
- **Credentials** — `xoxc` token + `d` cookie read from a local browser profile; secrets stored only in the macOS Keychain.

---
