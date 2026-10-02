# mdr

A package manager for the markdown AI agents read: `SKILL.md`, `AGENTS.md`, `CLAUDE.md`, `llms.txt`, `DESIGN.md` and Cursor rules.

`mdr` installs a skill or another instruction file at an exact version, into the folder your AI agent reads, and writes that version into a lockfile called `mdr.lock`. It is one file with no dependencies, and it needs Node 18 or newer.

Registry, search and audit grades: [markdownregistry.com](https://markdownregistry.com)

## Install

```sh
curl -fsSL https://markdownregistry.com/install.sh | sh
```

Puts `mdr` in `~/.local/bin`. Read it first: [install.sh](https://markdownregistry.com/install.sh) and [mdr.mjs](https://markdownregistry.com/mdr.mjs).

Or run it from npm, where it ships as `modelranch` (with `mdr` as an alias):

```sh
npx modelranch search pdf
npx modelranch add anthropics/skills/pdf        # pinned, recorded in mdr.lock
npx modelranch outdated                         # what moved upstream, with its audit grade
```

## Commands

```
mdr search <query>                    find artifacts
mdr info <owner/repo/name>            versions, audit grade, badge
mdr add <owner/repo/name>[@version]   install pinned, record in mdr.lock
      --agent claude|codex|cursor|opencode   (default claude)   --dir PATH   --force
mdr install                           install every mdr.lock entry at its pinned hash
mdr outdated                          what moved upstream, with the new audit grade
mdr verify [--min-grade A|B|C|D]      CI gate: pins still resolve and meet the grade floor
mdr diff <owner/repo/name> [from] [to]   line diff between versions (default: locked to latest)
mdr update <owner/repo/name>          move the pin to the latest version
```

Run `mdr` with no arguments for the full list.

## Why pin agent markdown

An agent that loads a skill or an `AGENTS.md` by reference reads whatever the file says today. In the registry's corpus, roughly one in five agent markdown files changed within two weeks of being indexed: of 47,724 files watched for at least 14 days, at least 9,487 (19.9%) received a new upstream commit within 14 days. Source: [State of agent markdown, September 2026](https://markdownregistry.com/reports/state-of-agent-markdown-2026-09) (dataset: [dotcomjack/state-of-agent-markdown](https://github.com/dotcomjack/state-of-agent-markdown)).

To keep an agent on the version you reviewed, pin by content hash rather than by path. `mdr add` records the file's SHA-256 in `mdr.lock`, `mdr outdated` reports every pinned artifact whose main file (`SKILL.md` for a skill) changed upstream, and `mdr diff` shows that change before you take it.

## The lockfile

`mdr.lock` lists what you installed and the exact version of each: its id, its label, its SHA-256 fingerprint and, for a public file, the GitHub commit it came from. `mdr install` downloads and checks those exact contents again. It installs them exactly, or it stops with an error.

```json
{
  "version": 1,
  "registry": "https://markdownregistry.com",
  "entries": {
    "anthropics/skills/pdf": {
      "artifact": "art_...",
      "label": "v1.4.2",
      "sha256": "...",
      "commit": "...",
      "path": ".claude/skills/pdf"
    }
  }
}
```

Until you run `mdr update`, your installed copy stays as it is. For a public skill, `mdr outdated` and `mdr diff` compare only the main file; an update also brings the other files from the new version's commit, so read that commit to see changes to scripts or references.

Walk-through with real output: [How to pin an agent skill to an exact version](https://markdownregistry.com/guides/pin-agent-skills).

## Telemetry

`mdr add`, `install` and `update` report anonymous install counts: the artifact id, kind and label, your agent type, the command, the CLI version and a random id kept in `~/.config/mdr/id`, never file contents or paths. `MDR_TELEMETRY=0` turns that off.

## About this repository

This repository holds the files of the published npm package [`modelranch`](https://www.npmjs.com/package/modelranch) (version 2.1.5), so the code here is the code npm ships. `bin/modelranch.mjs` is identical to the file in the npm tarball. The npm build defaults to the `https://modelranch.com` registry origin; the file served by `install.sh` defaults to `https://markdownregistry.com`. Set `MDR_REGISTRY` to choose either.

The npm package also carries `mdr pipeline` commands for [modelranch.com](https://modelranch.com), a deals network for agents. Run `mdr` with no arguments to see them.

## License

All rights reserved; see [LICENSE](LICENSE). `package.json` declares this as `UNLICENSED`. Running it, reading it, and inspecting what it does are all expected and fine. For any other use, ask: jack@dotcomjack.com.

## Links

- [markdownregistry.com](https://markdownregistry.com)
- [The CLI page](https://markdownregistry.com/cli)
- [How to pin an agent skill to an exact version](https://markdownregistry.com/guides/pin-agent-skills)
- [State of agent markdown, September 2026](https://markdownregistry.com/reports/state-of-agent-markdown-2026-09)
