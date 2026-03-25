# Personal Website Template

A minimal, single-page academic website built with plain Markdown and [Quarto](https://quarto.org/).

## Structure

```
index.md              → main page (bio, research, blog links)
blog/*.md             → blog posts
projects/*.md         → research project pages
style.css             → styling
_quarto.yml           → render config
```

## Usage

1. Install [Quarto](https://quarto.org/docs/get-started/)
2. Edit the `.md` files with your content
3. Run `quarto render` to build the site into `_site/`
4. Run `quarto preview` to preview locally

## Deploy on GitHub Pages

1. In `_quarto.yml`, change `output-dir` to `docs`
2. Push to GitHub
3. Go to repo Settings → Pages → Source: "Deploy from a branch", branch `main`, folder `/docs`

## Screenshot 

<img width="916" height="779" alt="image" src="https://github.com/user-attachments/assets/63ab6af2-8cdd-4f38-8ed0-ea107599046a" />
