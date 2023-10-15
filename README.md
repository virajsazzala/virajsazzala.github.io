# virajsazzala.github.io

The site is built using [Zola](https://www.getzola.org/) and the [Terminimal](https://github.com/pawelgrzybek/terminimal-zola-theme) theme.

## Repository Structure

- `main` - Contains the Zola site source code 
- `gh-pages` - Contains the built static site output for GitHub Pages

## Building the Site

To build the site:

1. Clone the repo
2. Run `zola build` to generate the static site output in `public/`
3. Commit the `public/` folder to the `gh-pages` branch

The `gh-pages` branch is configured to be served by GitHub Pages.

## Customizing the Site

The main customization points are:

- `config.toml` - Site configuration like title and base URL
- `content/*.md` - Markdown content for pages 

## Deploying Updates

To deploy changes:

1. Make edits to the `main` branch
2. Run `zola build` to rebuild the site
3. Commit the updated `public/` folder to `gh-pages`
4. Changes will go live on GitHub Pages within a few minutes

Let me know if you have any other questions!