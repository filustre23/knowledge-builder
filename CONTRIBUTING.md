# Contributing

Anyone can propose a module or a lesson.

## Propose an idea

Open an issue with one of the forms:

- **[Propose a module](https://github.com/filustre23/knowledge-builder/issues/new?template=propose-module.yml):** a new topic to learn on Halcyon Health's data
- **[Propose a lesson](https://github.com/filustre23/knowledge-builder/issues/new?template=propose-lesson.yml):** a new lesson, or a change to an existing one

Rough ideas are welcome. An outline and a sentence on why it's worth learning on enterprise data is enough to start
the conversation.

## Write it

Once a proposal is agreed, open a pull request.

- **New module:** add `modules/<module-name>/README.md` with the audience, goals and a lesson index.
- **New lesson:** add `modules/<module>/lessons/NN-<slug>.md` using the lesson format in [CLAUDE.md](CLAUDE.md#lesson-file-format),
  plus a verification query in `modules/<module>/checks/NN-<slug>.sql`.

## Rules for contributions

- **Synthetic data only:** no PHI, no real people, no real company data.
- **No secrets** or machine-specific paths.
- **Link and summarize** external sources; don't copy them.
- **Every exercise must be answerable from the company's data** and checkable by a query.
