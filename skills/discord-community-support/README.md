# discord-community-support

Public-Discord support skill for an open-source project: answers questions from
the project's own source tree and docs, and turns bug reports in chat into
**prefilled GitHub issue links the reporter submits themselves**.

## Why it drafts instead of files

A public chat surface with write access to your tracker is a spam amplifier. The
skill is written so the human is in the loop by construction: it composes the
issue, shows it in-channel for correction, and hands back an
`issues/new?template=…` link. For people without a GitHub account it posts a
maintainer-ready draft block instead, attributed to their Discord handle.

Pair it with a risk profile that excludes `git_forge` so the boundary is
enforced, not merely instructed.

## What it needs

| Permission | Used for |
| --- | --- |
| `file_read` | Reading the project's source clone and docs |
| `web_search`, `web_fetch` | Released versions, issues, discussions it can't see locally |

No shell, no writes, no forge access.

## Setting it up

1. Install it, or copy the folder into your skill bundle's directory:

   ```bash
   zeroclaw skills install discord-community-support
   ```

2. Replace `<OWNER>/<REPO>` in `SKILL.md` with your repository — it appears in
   the issue-template URLs and the security-policy link.

3. Check the template filenames match yours (`ls .github/ISSUE_TEMPLATE/`). A
   prefilled link naming a template that doesn't exist silently drops the
   prefill.

4. Give the agent a read-only clone of your repo in `allowed_roots`, refreshed
   on a timer — not a checkout you develop in.

`always: true` keeps the answering rules and the disclosure limits inlined
rather than loaded on demand. On a public surface those limits are the control,
not a style guide; don't turn it off to save tokens.

## Full deployment recipe

This skill is the answering half. The config that contains it — risk profile,
budget ceiling, channel, allowlist — is published separately, along with the
containment lessons that are easy to get wrong (tool exclusions are read from
the risk profile, never the channel; `level = "full"` disables them entirely).

## License

Apache-2.0
