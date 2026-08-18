# Claude Code Analysis — Power BI report

A Power BI report over **your own** Claude Code usage data.

It reads the `.claude` folder in your user profile and turns it into four pages: prompt and project
activity, session and token usage with estimated cost, orchestration (subagents, teams, tasks), and
your environment (plugins, marketplaces, plans, live sessions).

**Bring your own data.** This kit ships with **no data in it at all**. Nothing is uploaded anywhere
— every query reads local files on your machine, and the report only ever shows your own activity.

---

## Setup

**You need:** Windows, and [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free).

1. Open `Claude Code Analysis.pbit`.
2. Power BI Desktop prompts you for one parameter, **ClaudeHomePath**. Enter the full path to your
   `.claude` folder — usually:

   ```
   C:\Users\yourname\.claude
   ```

   To find it, open a terminal and run `echo %USERPROFILE%\.claude`.
3. Click **Load**. First load takes a few minutes if you have a lot of session history.

That is the whole setup. There is no sign-in, no gateway, no cloud dataset.

### If a table comes back empty

That is expected and fine. The report reads about a dozen different subfolders of `.claude`, and
most people do not have all of them. A folder you do not have simply loads as an empty table rather
than breaking the refresh.

---

## What it reads

Everything comes from the one `ClaudeHomePath` parameter:

| Source | Used for |
|---|---|
| `history.jsonl` | Prompt history, projects, session ids |
| `projects\**\*.jsonl` | Session events, token counts, tool calls |
| `file-history\` | File edit events |
| `tasks\`, `teams\` | Subagent tasks, team and member activity |
| `plans\` | Plan documents |
| `plugins\`, `session-env\` | Installed plugins and marketplaces |
| `sessions\`, `ide\`, `shell-snapshots\` | Live sessions, IDE connections, shell snapshots |
| `settings.json` | Theme, effort level, update channel, default permission mode |

**On `settings.json`:** the model deliberately imports only five fields from it — theme, effort
level, auto-update channel, default permission mode, and one experimental feature flag. Everything
else is filtered out **before** it reaches the model, because `settings.json` commonly holds API
keys and tokens (under `mcpServers` and `env`). Those values are never loaded, so they cannot end
up in the model, in an export, or in a screenshot.

---

## Known Limitations

- **Windows only.** Power BI Desktop itself runs only on Windows 10 / Windows Server 2016 or later.
  There is no macOS or Linux build, so this kit cannot run there. A Windows VM works, and Windows on
  ARM is supported.

- **WSL users: the default path will be wrong.** If you run Claude Code inside WSL, your `.claude`
  folder is not in your Windows user profile, so the path in Setup above will not find it. It lives
  in the WSL filesystem — typically reachable from Windows at
  `\\wsl.localhost\<distro>\home\<you>\.claude`. **We have not tested whether Power BI can load from
  that location**, so treat it as something to try, not a supported configuration. If it does not
  work, copy the folder to a Windows path and point the parameter there — that always works.

- **The `.pbip` variant needs a preview toggle.** The `.pbit` is the supported path and needs
  nothing special. The `.pbip` project is included for people who want to fork the model, but PBIP
  is still an official *preview* feature — you have to enable it under
  *File → Options → Preview features* before Power BI Desktop will open it.

- **One custom visual.** The prompt search box on the Usage page is the *Text Filter* visual from
  AppSource. Power BI Desktop offers to download it on first open. If your organization blocks
  custom visuals, that one box will not render — the rest of the report is unaffected.

- **Estimated cost is an estimate.** Token costs are computed from a hardcoded table of public API
  prices. If you are on a subscription plan, or prices have changed, the figures will not match what
  you were actually billed. Treat them as relative, not as an invoice.

- **If you fork the `.pbip`, do not commit `.pbi/`.** Power BI Desktop regenerates that folder every
  time it opens the project, and it contains `cache.abf` — a full copy of every row you have loaded,
  including your prompt text — plus your username. The included `.gitignore` already excludes it.
  Deleting it once is not enough; it comes back on the next open.

---

## Support

**None.** This is published as-is, for free, with no warranty and no support. It is not affiliated
with or endorsed by Anthropic or Microsoft. Issues and pull requests may not be answered. If it does
not work for you, you are welcome to fork it and change it. If you send a patch, you are offering it
under the same MIT terms.

The `.claude` folder format is not a documented, stable API — it can change at any time without
notice, and when it does, parts of this report will silently go empty or break.

---

## License

**MIT** — see [`LICENSE`](LICENSE). Copyright (c) 2026 byggkpi.no. You are free to use, modify and
redistribute this kit, commercially or not; keep the copyright notice and the license text with any
copy you pass on.

**Not everything here is ours.** Some of this project was generated by Power BI Desktop or ships
with it rather than being written for this kit — the Microsoft base theme at
`Claude Code Analysis.Report\StaticResources\SharedResources\BaseThemes\CY26SU04.json` is the
largest single example, and the auto-generated date tables, the `en-US` linguistic schema and the
`.pbip` / `.pbir` / `.pbism` / `.platform` scaffolding files are the same kind of thing. Those parts
stay Microsoft's, on Microsoft's terms; the MIT grant above covers our own work. Power BI Desktop
rewrites this tree every time the project is opened, so this describes a class of files, not a
fixed list. The *Text Filter* visual referenced on the Usage page is a third-party AppSource visual
and is not included here — Power BI Desktop downloads it on first open.
