# License

Folio is free to download and use. Its source code is not public, except
Folio's ledger engine, which is GPL-2.0 (see below).

© 2026 Noah Zhu. All rights reserved.

## Folio and the GPL

Folio is built on [Beancount](https://github.com/beancount/beancount), which is
licensed under the GNU GPL version 2. Folio follows it like this:

- **Folio's ledger engine is GPL-2.0.** The engine is the small Python program
  inside Folio.app that loads, checks and queries your ledger. It uses
  Beancount directly (in the same process), so it counts as a work based on
  Beancount and is under the same license. Its source code ships inside
  Folio.app as readable Python files, in `Folio.app/Contents/Resources/engine/`.
- **The rest of Folio is a separate program.** The app you see (its window,
  Filio, privacy masking, sync) runs in its own process. It starts the engine,
  git and the other tools as separate programs and talks to them through
  pipes, the way any app runs a command-line tool. It contains no GPL code, so
  the GPL does not extend to it.
- **The bundled tools are included unchanged**, with their license texts.

You can get the complete source code of every GPL program in any Folio release,
including the engine, by opening an issue in the
[folio-releases](https://github.com/0xnoahzhu/folio-releases/issues) repository. This offer is
valid for at least three years after each release.

## Open-source software inside Folio

| Component | License | How Folio uses it | Source |
|---|---|---|---|
| Folio's ledger engine | GPL-2.0 | Separate program | Inside Folio.app |
| [Beancount](https://github.com/beancount/beancount), [beanquery](https://github.com/beancount/beanquery) | GPL-2.0 | Used by the engine | [PyPI](https://pypi.org/project/beancount/#files) |
| [beanprice](https://github.com/beancount/beanprice) | GPL-2.0 | Used by the engine | [PyPI](https://pypi.org/project/beanprice/#files) |
| [Fava](https://github.com/beancount/fava) | MIT | Separate program | [PyPI](https://pypi.org/project/fava/#files) |
| [Git](https://git-scm.com/) | GPL-2.0 | Separate program | [kernel.org](https://www.kernel.org/pub/software/scm/git/) |
| [git-crypt](https://www.agwa.name/projects/git-crypt/) | GPL-3.0 or later | Separate program | [agwa.name](https://www.agwa.name/projects/git-crypt/downloads/) |
| [Python](https://www.python.org/) | PSF-2.0 | Runs the engine | [python.org](https://www.python.org/downloads/source/) |
| [OpenSSL](https://www.openssl.org/) | Apache-2.0 | Inside git-crypt | [GitHub](https://github.com/openssl/openssl/releases) |
| [Electron](https://www.electronjs.org/) (with Chromium and Node.js), [React](https://react.dev/), i18next, Zustand, Zod | MIT (Chromium: BSD and others) | Part of the app | Their project pages |
| [Figtree](https://github.com/erikdkennedy/figtree) and [JetBrains Mono](https://github.com/JetBrains/JetBrainsMono) fonts | OFL-1.1 | Part of the app | Their project pages |

Each release bundles the latest stable version of these tools. In the app,
**Settings → About → Open source licenses** lists the exact versions, their
full license texts and a link to the source code of each one.
