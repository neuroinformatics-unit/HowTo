# How to contribute to this website

## Website structure
The website is structured in three high-level sections, represented by folders in the `docs/source` directory:
- Data Analysis
- Programming
- Open Science

Each section directory (`docs/source/<section>`) may contain several markdown files, with each file corresponding to a specific long-form guide. There are also `docs/source/<section>/index.md` files, which are used to create the tables of contents for each section.

## Adding a long-form guide
To add a new guide, create a new markdown file in the appropriate section directory, and make sure to start it with a level-1 heading. Remember to also add the new file to the table of contents in the corresponding`docs/source/<section>/index.md` file.

## Adding a small tip
If the content you want to add is not long enough to warrant a full guide, for example a small tip or a quick solution to a common problem, you can add it to a `Troubleshooting.md` file in the appropriate section directory. For an example see `docs/source/programming/Troubleshooting.md`. Each small tip should start with a level-2 heading.

> **warning**
>
> Since the website is already named "HowTo", please avoid starting your guides/tips with "How to ...".
> For example, instead of "How to detach a forked repo on GitHub", use "Detach a forked repo on GitHub".

## GitHub workflow
* Clone the GitHub repository, and create your `new_branch`.
* Edit the website and commit your changes to the `new_branch`.
* Push the `new_branch` to GitHub and create a draft pull request. This will automatically trigger a [GitHub action](https://github.com/neuroinformatics-unit/actions/tree/main/build_sphinx_docs) that checks if the website still builds correctly.
* If the checks pass, mark the pull request as ready to review assign someone to review your changes.
* When the reviewer merges your changes into the `main` branch, a different [GitHub action](https://github.com/neuroinformatics-unit/actions/tree/main/deploy_sphinx_docs) will be triggered, which will build the website and publish it to the `gh-pages` branch.
* The updated website should be available at [howto.neuroinformatics.dev](https://howto.neuroinformatics.dev)

> **note**
>
> If you wish to view the website locally, before you push it, you can do so by running the following commands from the root of the repository.
> ```bash
> # First time only
> pip install -r docs/requirements.txt
> sphinx-build docs/source docs/build
>
> # Every time you want to update the local build
> rm -rf docs/build && sphinx-build docs/source docs/build
>```
>You can view the local build at `docs/build/index.html`
