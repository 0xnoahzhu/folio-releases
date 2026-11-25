# Folio is the app. Filio is its agent.

**Folio** is the app. **Filio** is its AI bookkeeper and mascot, the little
bean on the chat box: you talk to Filio, and Filio keeps your books in Folio.

Folio is a Mac app for keeping your personal accounts in plain-text
[Beancount](https://beancount.github.io/) files, with Filio doing the busywork.

![The Folio dashboard](docs/screenshots/dashboard.png)

## Features

- **Tell Filio, or drop in a statement.** Type an expense, or add a bank
  export, PDF or receipt photo, and Filio drafts the entries.
- **Review before anything is written.** Every change is checked with
  `bean-check` and saved as a git commit you can undo.
- **Filio learns how you book things.** Categories come from your history and your
  choices, so Filio only asks about what's new.
- **Fix many entries at once.** One answer, such as "all MeiTuan orders are
  delivery", updates every matching entry.
- **Filio remembers what you tell it.** Accounts, people and preferences, per ledger,
  editable in Settings.
- **A whole month at once.** Add many statements together; Filio reads them in
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

## More

- [Move Folio to another Mac](docs/move-to-another-mac.md)
- [Uninstall Folio](docs/uninstall.md)
- [License and open-source software](docs/license.md)

© 2026 Noah Zhu. Free to download and use.
