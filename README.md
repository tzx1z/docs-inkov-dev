# docs-inkov-dev

Корень сайта документации https://docs.inkov.dev/: страница со списком проектов.

Документация каждого проекта собирается в его собственном репозитории и подключена
в Read the Docs как subproject этого проекта. Адрес проекта - `https://docs.inkov.dev/projects/<alias>/`.

| Проект | Репозиторий | Адрес |
|---|---|---|
| termisations | [tzx1z/termisations](https://github.com/tzx1z/termisations) | https://docs.inkov.dev/projects/termisations/ |
| XMPP Server Guides | [tzx1z/xmpp-server-guides](https://github.com/tzx1z/xmpp-server-guides) | https://docs.inkov.dev/projects/xmpp-server-guides/ |

## Структура

```
docs/index.md           главная страница: список проектов
docs/robots.txt         robots.txt домена, ссылки на sitemap всех проектов
mkdocs.yml              конфигурация MkDocs
requirements-docs.txt   зависимости сборки, версии закреплены
.readthedocs.yaml       конфигурация сборки на Read the Docs
```

## Локальная сборка

```bash
python3 -m venv .venv && . .venv/bin/activate
pip install -r requirements-docs.txt
mkdocs build --strict   # та же проверка, что fail_on_warning на Read the Docs
mkdocs serve            # http://127.0.0.1:8000/
```

## Настройки Read the Docs

Настройки хранятся в панели https://app.readthedocs.org, не в репозитории.

- Проект `docs-inkov-dev`, URL versioning scheme - `Single version without translations`.
- Settings -> Domains: `docs.inkov.dev`, Canonical. Custom domain можно привязать
  только к этому проекту: subprojects всегда открываются на домене основного проекта.
- Settings -> Subprojects, проект и alias:
  - `termisations`, alias `termisations`;
  - `xmpp-server-guides`, alias `xmpp-server-guides`.
- Settings -> Redirects: адреса, которые открывались от корня домена до перехода на subprojects.

  | Type | From URL | To URL |
  |---|---|---|
  | Exact redirect | `/GUIDE_XMPP_*` | `/projects/xmpp-server-guides/GUIDE_XMPP_:splat` |
  | Exact redirect | `/README.en/` | `/projects/xmpp-server-guides/README.en/` |

DNS зоны `inkov.dev` в Cloudflare: `docs` CNAME `readthedocs.io`, Proxy status - DNS only.
При включенном прокси Cloudflare отдает ошибку 1014.

## Добавление проекта

1. В репозитории проекта:
   - `site_url: !ENV [READTHEDOCS_CANONICAL_URL, "https://docs.inkov.dev/projects/<alias>/"]` в `mkdocs.yml`;
   - `.readthedocs.yaml` по образцу этого репозитория.
2. Импортировать репозиторий в Read the Docs, URL versioning scheme -
   `Single version without translations`. Custom domain проекту не добавлять.
3. В проекте `docs-inkov-dev`: Settings -> Subprojects -> Add subproject, alias `<alias>`.
   Alias после публикации не менять: от него зависят все адреса проекта.
4. В этом репозитории:
   - строка проекта в таблицах `docs/index.md` (RU и EN) и в таблице выше;
   - строка `Sitemap: https://docs.inkov.dev/projects/<alias>/sitemap.xml` в `docs/robots.txt`.

Ссылки на subprojects в `docs/index.md` пишутся полными URL с `https://`.
Относительную ссылку `projects/<alias>/` MkDocs считает битой, и строгая сборка завершается ошибкой.
