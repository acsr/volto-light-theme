# How to create a project storybook for a customized VLT theme in a project

The Storybook in the VLT docs is created from the ".stories.tsx" files in the VLT package:
https://volto-light-theme.readthedocs.io/storybook/?path=/docs/primitives-summary--docs

We explain how a project Storybook is generated reusing the .stories.tsx from VLT.

You learn how to change mock texts live in the browser (without dev tools) for trying out variants and edge cases.

### Goal of the Howto

Simplify development cycles and improve customer documentation for digital brandbooks etc. by lowering the bar to use existing tooling.

### Helpers

You can list the Storybook source files in the `volto-light-theme` repository like this:

``` shell
tree -a -P "*.storybook|*.stories.tsx" --matchdirs --prune ./frontend
```
output:
``` shell
./frontend
├── .storybook
│   ├── main.js
│   └── preview.jsx
└── packages
    └── volto-light-theme
        └── src
            ├── components
            │   ├── Summary
            │   │   └── Summary.stories.tsx
            │   └── Widgets
            │       └── ColorSwatch.stories.tsx
            ├── primitives
            │   └── Card
            │       └── Card.stories.tsx
            └── stories
                ├── grid.teaser.stories.tsx
                ├── listing.stories.tsx
                └── teaser.stories.tsx

11 directories, 8 files
```
### Building the docs

You can build the regular docs using:

``` shell
make docs-build 
```

### Change "Page" Title in Teaser Story:

Look for the file at: `frontend/packages/volto-light-theme/src/components/Blocks/Teaser/Teaser.stories.jsx`

Inside that file is an args object with fake content data — look for something like:

``` json
jsxexport const Default = {
  args: {
    data: {
      title: 'Page',          // ← THIS THE ROCK TO CHANGE
      description: 'Lorem ipsum...',
      href: { ... },
    },
  },
};
```

Change 'Page' to whatever phrase you want → commit → ReadTheDocs rebuild → new text appear in Storybook doc.

### Storybook config

The build command in `.readthedocs.yaml` first runs `make -C "./docs/" rtd-build` for the main docs, then separately builds Storybook with `pnpm run storybook-build`. Both outputs get combined under ReadTheDocs.

The Storybook itself is configured inside: `frontend/packages/volto-light-theme/.storybook/main.js`

That file tell Storybook: "go look for `*.stories.jsx` files in `src/components/`."


## Finally running solution

`.readthedocs.yaml` (irrelevant)

``` yaml
...
# Install uv, build the docs
build:
  ...
  jobs:
    ...
    build:
      html:
        - READTHEDOCS_OUTPUT="/Users/astro/github/Plone.VoltoLightTheme/volto-light-theme-8.x/volto-light-theme/docs/_build"
  ...
```

`docs/Makefile`  (irrelevant)

``` txt
...
READTHEDOCS_OUTPUT="/Users/astro/github/Plone.VoltoLightTheme/volto-light-theme-8.x/volto-light-theme/docs/_build"
...
```

set Environment Variable
`READTHEDOCS_OUTPUT="/Users/astro/github/Plone.VoltoLightTheme/volto-light-theme-8.x/volto-light-theme/docs/_build"`


``` shell
make install
```

start manually

``` shell
(cd frontend && pnpm run storybook-build -o $READTHEDOCS_OUTPUT/html/storybook)
```