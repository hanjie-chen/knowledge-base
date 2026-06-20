# Repository Guide

## Read First

Before creating, moving, or modifying content:

1. Read the project root `README.md`.
2. Read the nearest `README.md` on the target path, if one exists below the project root.

## When Writing or Revising Articles

- Store the article title in the YAML front matter `Title` field; do not add a Markdown `#` heading only to repeat it.
- In article bodies, `#` headings are normal top-level sections and may appear multiple times.
- Use only `#`, `##`, and `###` heading levels in article bodies.
- Prefer English for Markdown `#`, `##`, and `###` headings whenever practical.
- Headings should stay short and descriptive.
- Body content can be Chinese, English, or mixed, depending on what explains the topic most clearly.
- If a topic is naturally expressed in Chinese, or the user explicitly wants Chinese headings, Chinese headings are acceptable.
- Organize articles in a clear, progressive order. Start with the necessary foundation, then build forward step by step; move side notes or less central details later so they do not interrupt the main flow.

## When Placing or Naming Articles

- Treat directory paths as taxonomy: choose the path by subject area, with each path segment representing a narrower topic classification.
- Choose concise English file names using lowercase letters and hyphens (`-`) between words.
- Keep file names short. Let the directory path carry as much context as possible.
- Avoid repeating parent directory information in the file name.

For example, prefer `code/python/package/flask/basic.md` over `code/python/package/flask/python-flask-basic.md`.
