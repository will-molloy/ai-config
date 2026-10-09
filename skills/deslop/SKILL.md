---
name: deslop
description: |
  Rewrite agent-written prose in plain words - invented terms, borrowed jargon and vague phrasing in
  code comments, docs, commit messages, PR descriptions and tickets.
  Use when asked to "fix ai slop", "fix the jargon" or "make this plain English".
allowed-tools:
  - Bash
  - Read
  - Edit
  - Grep
---

# Deslop

Rewrites prose the agent wrote so a reader understands it without decoding.
The main target is words the agent made up or borrowed. Each one reads as a real term, but the reader can't look it up anywhere.
The "Response style" and "Code comments" sections of the global instructions (`CLAUDE.md` or `AGENTS.md`) still apply.
This skill adds a check for terms and the steps to fix them.

## Technical writing is not creative writing

Technical prose uses a ubiquitous language. Each concept has one name, the name the code, schema and team already use, and the prose uses that exact name every time.

- Repeat the name. Repetition is correct, and a synonym makes the reader wonder whether it's a different thing.
- Never vary a word for style, paraphrase a defined name, or coin a fresh label for something that already has one.
- Match the exact form of the name, including its spelling, casing and qualifiers. If the code says `bank rule`, don't write "rule", "matching rule" and "bank rule" in turn.
- Reach for vivid or clever wording only when there's no defined term and plain words can't say it. Then cash it out straight away.

## Inputs

- A path, commit range, PR or ticket. If none is given, use the current branch's changes against its base branch.

## Rules

- Change prose only: comments, docstrings, docs, commit messages, PR descriptions and ticket text. Never change code behaviour.
- Preserve the user's own wording. Only rewrite text the agent wrote.
- Keep the meaning exact. A plainer sentence that says something different is worse than the original.
- Leave file changes uncommitted.
- Never edit a pushed commit message or a posted comment without asking. Propose the new text instead.

## Steps

1. Collect the agent-written prose in scope.
2. List the concepts the prose refers to, and the words it uses for each. Every concept should have exactly one.
3. Check every term that isn't plain everyday English against the term test below.
4. Rewrite each term that fails, and each phrasing problem in the checklist.
5. Re-read each rewrite next to its code or context to confirm it's still accurate.

## Term test

A term passes if a reader can find what it means in one of these places:

- the code, as a name for a type, function, variable, file or config key
- the schema, API or vendor docs, using the vendor's own name for the thing
- the ticket, design doc or team glossary
- standard domain vocabulary, such as the accounting or protocol term

Search for the term before deciding. A term that appears only in the agent's own writing fails.
Replace it with the defined name, or describe the thing in plain words if nothing defines it.
For example, "the sync shim" becomes "`LegacyAdapter`" when the code names it, or "the code that converts old responses to the new format" when nothing does.
When nothing defines a concept the prose keeps referring to, use one description consistently, and say in the output that the concept needs a name in the code or glossary.

## Checklist

- **Invented labels.** A made-up noun for something the code or vendor already names.
- **Borrowed jargon.** A term from another field or codebase that this one never uses.
- **Vague verbs.** Verbs that hide the mechanism, such as "hands out", "handles", "deals with" or "takes care of". Say what actually happens.
- **Metaphors left open.** A figure the reader has to translate. Say the literal thing.
- **Stacked compression.** Several packed noun phrases in one clause. Unpack it into a plain sentence with real verbs.
- **Synonyms for variety.** The same concept under different words in one piece of writing. Pick the defined name and use it every time.
- **One word for two things.** A single name used for two different concepts. Give each its own defined name.
- **Prose tics** from the global response style, such as colon-hinged sentences, "not X but Y" and verbless fragments.

## Output

- Each change, as the old wording and the new, grouped by file.
- Proposed text for anything this skill must not edit, such as pushed commit messages or posted comments.
- Terms left alone because they pass the test, only where the call was close.
- Concepts that need a name in the code or glossary.
