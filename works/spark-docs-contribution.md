# Как исправить документацию Apache Spark и собрать её локально

> **Учебная работа.** Памятка составлена по публичному руководству Contributing to Spark и файлу `docs/README.md` репозитория apache/spark. В официальную документацию проекта она не входит.

**Для кого:** для человека, который заметил ошибку в документации Spark и хочет предложить исправление.

**Что понадобится:** учётная запись GitHub, Git, Ruby 3 и Python 3.

## Как устроена документация

Документация хранится в Markdown-файлах каталога `docs/` репозитория [apache/spark](https://github.com/apache/spark). Исправление оформляется как pull request. Нужна ли задача в Jira, зависит от размера правки (см. шаг 4).

## Шаг 1. Подготовьте репозиторий (один раз)

1. Создайте форк репозитория apache/spark на GitHub.
2. Склонируйте свой форк и добавьте основной репозиторий как `upstream`:

   ```bash
   git clone https://github.com/<ваш-логин>/spark.git
   cd spark
   git remote add upstream https://github.com/apache/spark.git
   ```

## Шаг 2. Внесите правку

```bash
git fetch upstream
git switch -c docs-my-change upstream/master
```

Отредактируйте нужный файл в каталоге `docs/`.

## Шаг 3. Соберите документацию локально

Если команды `bundle` нет, установите её: `gem install bundler -v 2.4.22`.

```bash
cd docs
bundle install
SKIP_API=1 bundle exec jekyll build   # сборка без API-документации
bundle exec jekyll serve --watch      # просмотр на localhost:4000
```

Готовый сайт появится в каталоге `docs/_site`. Проверьте, что ваша правка отображается корректно.

## Шаг 4. Отправьте изменения

Сначала определите тип правки. По руководству проекта, новое изменение обычно требует задачи в Jira, но тривиальные правки, где понятно и что менять, и как, обходятся без неё. Опечатка относится к таким правкам.

| Тип правки | Задача в Jira | Заголовок pull request |
|------------|---------------|------------------------|
| Опечатка или мелкое исправление | Не нужна | `[MINOR][DOCS] Fix typo in <название страницы>` |
| Содержательное изменение страницы | Нужна | `[SPARK-XXXXX][DOCS] Краткое описание на английском` |

Метка `[MINOR]` ставится вместо `[SPARK-XXXXX]`, а не вместе с ним. Примеры из репозитория: `[MINOR][DOCS] Fix minor typos at nulls_option in Window Functions` и `[SPARK-50787][DOCS] Fix typos and add missing semicolons in sql examples`.

```bash
git add docs/<изменённый-файл>.md
git commit -m "<заголовок по таблице выше>"
git push -u origin docs-my-change
```

Для опечатки то же самое выглядит так:

```bash
git switch -c docs-fix-typo upstream/master
git commit -am "[MINOR][DOCS] Fix typo in <название страницы>"
git push -u origin docs-fix-typo
```

Затем откройте pull request в apache/spark.

В описании ответьте на вопросы шаблона:

- What changes were proposed in this pull request?
- Why are the changes needed?
- Does this PR introduce any user-facing change?
- How was this patch tested?
- Was this patch authored or co-authored using generative AI tooling?

## Если основная ветка ушла вперёд

```bash
git fetch upstream
git rebase upstream/master
```

## Полезные команды

| Команда | Что делает |
|---------|------------|
| `git status` | Показывает изменённые файлы |
| `grep -rn "TODO" docs/*.md` | Ищет недописанные места |
| `cd python/docs && make html` | Собирает документацию PySpark |

## Где узнать больше

- [docs/README.md](https://github.com/apache/spark/blob/master/docs/README.md) — как собрать документацию.
- [Contributing to Spark](https://spark.apache.org/contributing.html) — общие правила участия.
