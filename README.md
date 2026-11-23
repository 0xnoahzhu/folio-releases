# Folio

A Mac app for keeping your personal accounts in plain-text
[Beancount](https://beancount.github.io/) files, with Filio, an AI bookkeeper,
doing the busywork.

![The Folio dashboard](docs/screenshots/dashboard.png)

## Features

- **Tell Filio, or drop in a statement.** Type an expense, or add a bank
  export, PDF or receipt photo, and Filio drafts the entries.
- **Review before anything is written.** Every change is checked with
  `bean-check` and saved as a git commit you can undo.
- **Learns how you book things.** Categories come from your history and your
  choices, so Filio only asks about what's new.
- **Fix many entries at once.** One answer, such as "all MeiTuan orders are
  delivery", updates every matching entry.
- **Remembers what you tell it.** Accounts, people and preferences, per ledger,
  editable in Settings.
- **A whole month at once.** Add many statements together; Folio reads them in
  the background.
- **Private by design.** Your ledger stays in your folder and your git remote,
  optionally encrypted. Names and numbers are hidden before anything reaches a
  model.
- **Any model.** Anthropic, OpenAI, Azure OpenAI, or any OpenAI-compatible
  server, including one on your Mac.
- **Also:** dashboards, transactions, accounts, saved queries, voice input, a
  PIN lock, built-in [Fava](https://beancount.github.io/fava/), and automatic
  updates.

| Chat with Filio | Review every change |
|---|---|
| ![Filio drafting an entry next to the Accounts view](docs/screenshots/chat.png) | ![A change set in Review, showing the diff](docs/screenshots/review.png) |
| **Transactions by month** | **Light and dark** |
| ![Transactions for November](docs/screenshots/transactions.png) | ![The dashboard in dark mode](docs/screenshots/dashboard-dark.png) |

All data in these screenshots is made up.

## Download

For Macs with Apple silicon, macOS 13 or later. Get it from
[Releases](https://github.com/0xnoahzhu/folio-releases/releases).

## Uninstall

Quit Folio (⌘Q), drag it to the Trash, and paste this into Terminal. It removes
everything Folio keeps on your Mac, including your API keys, and the `.folio`
folder in each ledger. Your ledgers themselves are not touched.

```sh
pkill -f "/Folio.app/Contents/Resources/"
D=~/Library/"Application Support"/Folio; C="$(getconf DARWIN_USER_CACHE_DIR)"
[ -f "$D/folio.db" ] && sqlite3 "$D/folio.db" "SELECT path FROM ledgers" | while IFS= read -r L; do [ -n "$L" ] && rm -rf "$L/.folio"; done
rm -rf "$D" ~/Library/Logs/Folio ~/Library/Caches/{folio-ocr,folio-updater} ~/Library/Caches/app.folio.desktop{,.ShipIt,.names,.speech,.auth} ~/Library/HTTPStorages/app.folio.desktop ~/Library/"Saved Application State"/app.folio.desktop.savedState
[ -n "$C" ] && rm -rf "$C"app.folio.desktop{,.helper,.helper.GPU,.helper.Renderer,.helper.Plugin}
find "$TMPDIR" -maxdepth 1 -name 'folio-askpass-*' -exec rm -rf {} + 2>/dev/null
find ~/Library/Logs/DiagnosticReports -maxdepth 1 \( -name 'Folio-20*' -o -name 'Folio Helper*' -o -name 'folio-*-20*' \) -delete 2>/dev/null
defaults delete app.folio.desktop 2>/dev/null; rm -f ~/Library/Preferences/app.folio.desktop.plist; tccutil reset All app.folio.desktop
```

`.folio` sits in the top folder of each ledger and holds Filio's rules for that
ledger (`rules.md`). In a ledger kept in git, commit its removal. Details:
[Uninstall Folio](docs/uninstall.md).

## More

- [Move Folio to another Mac](docs/move-to-another-mac.md)
- [License and open-source software](docs/license.md)

© 2026 Noah Zhu. Free to download and use.
