# Pumpkin MC AI skill

Cursor agent skill for [Pumpkin](https://github.com/Pumpkin-MC/Pumpkin), the Rust Minecraft server.

The skill is [`skills/pumpkin-mc/SKILL.md`](skills/pumpkin-mc/SKILL.md). It stays short. It routes an agent to the English guides in [Pumpkin-Docs](https://github.com/Pumpkin-MC/Pumpkin-Docs) (`docs/en/` only). It does not copy those guides.

A daily Cursor automation opens a pull request only when procedure changes: Wasm build target, entry paths, plugin load rules, or the folder map. Wording, translations, and new tutorial pages do not update the skill. When a guide and the `pumpkin-plugin-api` crate disagree, the crate wins.
