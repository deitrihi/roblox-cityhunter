# Dummy roaming checklist

- [x] Inspect the live Roblox Studio dummy and city map implementation.
- [x] Add roaming plus idle behavior to City dummies without changing unrelated map flow.
- [x] Verify dummies move, pause, and still respawn correctly.
- [x] Record any documentation risk or follow-up needed for PROJECT.md.

# PC controls hint checklist

- [x] Inspect the existing mobile controls UI and gun HUD visibility flow.
- [x] Add a PC-only controls hint panel in the mobile controls area.
- [x] Verify the hint appears on PC during City/gun HUD and stays hidden on mobile or when HUD is hidden.

# PC controls hint resize checklist

- [x] Inspect current PC controls hint dimensions.
- [x] Scale the PC controls hint panel and contents by 1.5x.
- [x] Verify the resized hint frame dimensions in Play mode.

# Localization checklist

- [x] Inventory visible UI strings across Studio scripts.
- [x] Add a shared locale module with keys and translations.
- [x] Apply locale lookup to client HUD text and server-created world UI text.
- [x] Verify visible text resolves through the locale layer.

# Dash action checklist

- [x] Replace sprint stamina behavior with one-shot dash movement.
- [x] Update PC/mobile action labels from sprint/run to dash.
- [x] Verify dash consumes stamina, moves the character briefly, and clears its active state.

# Dash terminology checklist

- [x] Change visible roll/구르기 labels to dash/대시.
- [x] Publish dash state compatibility attribute while preserving existing roll flag.
- [x] Update PROJECT.md and notes to use dash terminology.
