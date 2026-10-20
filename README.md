# BapeQuests

Quest plugin for Bape Network. It has a 54 slot GUI (orange/purple glass border, black background), smallcaps text,
progress bars (■□), status lines, a Halloween event category, command/money rewards and English/Romanian
language per player.

## Features
- Main menu: profile head (slot 4), Daily (11), Combat (13), Mining (15), Halloween Event (31), Close (49).
- Each category opens its own menu with quest cards (Quest Info, Progress, Rewards, Status), Back (45) and Close (49).
- Quest types: `KILL_MOB`, `BREAK_BLOCK`, `FISH_ITEM`, `COLLECT_ITEM`, `PLAYER_KILL`.
- Status: CLICK TO CLAIM / IN PROGRESS / LOCKED / CLAIMED / COOLDOWN (repeatable quests, for example daily ones).
- Rewards: console commands, plus optional money through Vault.
- Languages: every player picks English or Romanian with `/quests lang set <eng|ro>`. The GUI, messages, titles,
  action bar and quest texts are all shown in that language. The choice is saved in `playerdata.yml`.
- Anti-exploit: blocks placed and items dropped in survival do not count, spawner mobs can be excluded.
  Blocks placed or items dropped in creative do count when broken or picked up later in survival.
- Progress is kept in memory and saved to `playerdata.yml` asynchronously.
- Optional PlaceholderAPI expansion.

## Requirements
- Paper 1.21.11 (built against paper-api 1.21.11-R0.1-SNAPSHOT, `api-version: 1.21`), Java 21.
- Optional: Vault 1.7.1 + an economy plugin (only needed for `rewards.money`), PlaceholderAPI 2.11.6 or newer.
- The example rewards use `eco give` (EssentialsX), `crate give` and `candies give`. Replace them with commands
  that exist on your server.

## Build
Run `gradle build` in the project folder (Gradle Kotlin DSL, JDK 21). The jar is `build/libs/BapeQuests.jar`.

## Installation
1. Put `BapeQuests.jar` in the server's `plugins/` folder.
2. Start the server. The plugin creates `plugins/BapeQuests/config.yml`, `quests.yml`, `lang/en.yml` and
   `lang/ro.yml`. `playerdata.yml` is created on the first save.
3. Edit the files and run `/quests reload`.

Updating from an older version: the texts moved from `config.yml` into `lang/en.yml` and `lang/ro.yml`. Delete the
old `config.yml` (or copy your changes over) so the new one is generated. Keys missing from a lang file are taken
from the copy inside the jar.

## Commands and permissions
| Command | Permission | Description |
|---|---|---|
| `/quests`, `/quest` or `/misiuni` | `bapequests.use` (default: everyone) | Opens the Quest GUI |
| `/quests lang` | `bapequests.use` | Shows your current language |
| `/quests lang set <eng\|ro>` | `bapequests.use` | Sets your language (`eng`/`en`/`english` or `ro`/`romana`) |
| `/quests reload` | `bapequests.admin` (default: op) | Reloads config.yml, quests.yml and the lang files |
| `/quests reset <player> <quest_id\|all>` | `bapequests.admin` | Resets a player's progress (the language stays) |

## config.yml (defaults)
| Option | Default | Description |
|---|---|---|
| `prefix` | `&#FF6B00&lBAPE &#8A2BE2&lQuests &8» ` (smallcaps) | Chat prefix, same for both languages |
| `default-language` | `en` | Language for players who never used `/quests lang set` (`en` or `ro`) |
| `settings.auto-claim` | `false` | Give the rewards automatically on completion |
| `settings.progress-actionbar` | `true` | Action bar with the progress |
| `settings.disabled-worlds` | `[]` | Worlds without quest progress |
| `settings.ignore-creative` | `true` | No progress while in creative |
| `anti-exploit.ignore-placed-blocks` | `true` | Blocks placed in survival do not count (tracked until restart) |
| `anti-exploit.ignore-player-dropped-items` | `true` | Items dropped in survival do not count for COLLECT_ITEM |
| `anti-exploit.ignore-spawner-mobs` | `false` | Spawner mobs do not count |
| `anti-exploit.count-creative-placed` | `true` | Blocks placed or items dropped in creative count when broken or picked up in survival |
| `data.autosave-seconds` | `300` | Async save interval (minimum 30) |
| `sounds.complete` | `UI_TOAST_CHALLENGE_COMPLETE 1.0 1.0` | Played on completion |
| `sounds.claim` | `ENTITY_PLAYER_LEVELUP 1.0 1.2` | Played on claim |
| `sounds.click` / `sounds.error` | `UI_BUTTON_CLICK 0.6 1.0` / `ENTITY_VILLAGER_NO 0.8 1.0` | GUI sounds |
| `titles.complete.enabled` | `true` | Title on completion |
| `titles.complete.fade-in` / `stay` / `fade-out` | `10` / `50` / `15` | Title timings in ticks |
| `progress-bar.length` | `10` | Number of symbols |
| `progress-bar.filled-symbol` / `empty-symbol` | `■` / `□` | Bar symbols |
| `progress-bar.filled-color` / `empty-color` | `&#00FF88` / `&#555555` | Bar colors |
| `gui.border.primary` / `secondary` / `filler` | `ORANGE_` / `PURPLE_` / `BLACK_STAINED_GLASS_PANE` | Border and background |
| `gui.close.material` / `gui.back.material` / `gui.empty.material` | `BARRIER` / `ARROW` / `GRAY_DYE` | Button items |
| `categories.<KEY>.enabled` | `true` | `false` = the category shows as LOCKED (for example after Halloween) |
| `categories.<KEY>.slot` | 11 / 13 / 15 / 31 | Slot in the main menu |
| `categories.<KEY>.material` | `HAY_BLOCK` / `NETHERITE_SWORD` / `DIAMOND_PICKAXE` / `JACK_O_LANTERN` | Icon |

Colors: `&a`, `&l`, `&#RRGGBB` and `&#RRGGBB;`. Sounds: `NAME [volume] [pitch]` or a key like `minecraft:ui.button.click`.

## Language files (lang/en.yml, lang/ro.yml)
Both files have the same keys:
- `language-name`: name shown in `/quests lang` messages (`English` / `Romana`).
- `messages.*`: every chat message, including `lang-set`, `lang-current`, `lang-usage`, `lang-invalid` and
  `progress-actionbar`.
- `status.*`: `claimable`, `in-progress`, `locked`, `claimed`, `cooldown` (`%time%`).
- `titles.complete.title` / `subtitle`: completion title (`%quest%`).
- `gui.*`: `main-title`, `category-title` (`%category%`), `profile`, `close`, `back`, `empty`,
  `category-locked-lore` and `quest-lore` (the template of every quest card).
- `categories.<KEY>.name` / `title` / `lore`: texts of each category (`%completed%`, `%total%`, `%claimable%`).

Quest lore placeholders: `%description%`, `%target%`, `%amount%`, `%progress%`, `%percent%`, `%bar%`, `%rewards%`,
`%status%`, `%name%`, `%money%`. To add a new category, add it in `config.yml` under `categories` and add its texts
in both lang files.

## quests.yml
```yaml
quests:
  pumpkin_hunter:
    name: "&#FF6B00&lPumpkin Hunter"
    category: HALLOWEEN
    icon: JACK_O_LANTERN
    slot: 21            # optional (10-16, 19-25, 28-34, 37-43), otherwise placed automatically
    type: BREAK_BLOCK
    target: PUMPKIN     # several: "DIAMOND_ORE, DEEPSLATE_DIAMOND_ORE"; ANY = all; PLAYER for PLAYER_KILL
    amount: 64
    permission: ""      # optional, the quest is LOCKED without it
    cooldown: 0         # seconds, more than 0 = repeatable (86400 = daily)
    description: ["&7Break pumpkins around the world to complete this task."]
    rewards:
      money: 0          # optional, paid through Vault
      commands: ["eco give %player% 5000", "crate give %player% halloween 1"]
      display: ["+$5,000 Money", "1x Halloween Key"]
    claim-message: "&#FF6B00&l[HALLOWEEN] &fQuest completed! You claimed your rewards."
    complete-sound: ""  # optional
    translations:       # optional, used for players with Romanian selected
      ro:
        name: "&#FF6B00&lVanator de Dovleci"
        description: ["&7Sparge dovleci prin lume ca sa termini acest quest."]
        rewards-display: ["+$5,000 Bani", "1x Cheie Halloween"]
        claim-message: "&#FF6B00&l[HALLOWEEN] &fQuest terminat! Ai primit recompensele."
```
The base `name`, `description`, `display` and `claim-message` are the English texts. Every field under
`translations.ro` is optional; a missing one falls back to the base text.

Included quests (with Romanian translations): Pumpkin Hunter, Soul Collector (Halloween), Diamond Miner,
Stone Breaker (Mining), Monster Slayer, Bounty Hunter (Combat), Fisherman, Lumberjack, Daily Hunter (Daily,
24h cooldown). A category can show at most 28 quests. Command placeholders: `%player%`, `%uuid%`, `%quest%`.

## playerdata.yml
```yaml
players:
  <uuid>:
    name: Steve
    lang: ro
    quests:
      pumpkin_hunter: { progress: 32, claimed: false, claimed-at: 0 }
```

## PlaceholderAPI
`%bapequests_claimed%`, `%bapequests_total%`, `%bapequests_claimable%`,
`%bapequests_progress_<id>%`, `%bapequests_amount_<id>%`, `%bapequests_percent_<id>%`, `%bapequests_status_<id>%`.

Note: emoji that are not in the Minecraft font (like 🌾 or 🎃) may show as a box on some clients. You can change
them in the lang files.
