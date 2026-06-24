<!--
Thanks for contributing a workflow!

Quick checklist — please tick where applicable:
-->

## Summary

One-line description of the workflow you're adding (or change you're making).

## Checklist

- [ ] New MDX file lives at `workflows/<slug>.mdx`
- [ ] Frontmatter has `title`, `tagline`, `tier`, `canonical_url` (the four required fields)
- [ ] `tier` is one of `snippet`, `framework`, `tool`
- [ ] `setup_command` is the actual install + run incantation a reader can copy
- [ ] `when_to_use` / `when_not_to_use` are filled in
- [ ] At least one entry in `sources`
- [ ] If editorial mode: ~200 words of original prose with attribution, code block with the recipe, bold inline `Use for` / `Avoid in`, ends with `Canonical: <url>`
- [ ] If reference mode (vendor docs): MDX body is empty
- [ ] Validator passes locally: `npm run lint`

## Notes for the reviewer

(Optional — anything special about this workflow, edge cases, design decisions.)
