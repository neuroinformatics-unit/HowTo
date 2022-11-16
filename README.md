# How to contribute to this website

## Website structure
The webiste is structured in sections, each one corresponding to a different experimental __modality__, and sub-sections, each corresponding to a different `tool`.

Examples of `tools` that belong in each __modality:__
* __Behaviour:__ `SLEAP`, `DeepLabCut`
* __Electrophysiology:__  `Kilosort`, `Phy`, `SpikeInterface`
* __Imaging:__ `Suite2p`, `CaImAn`
* __Histology:__ `AllenSDK`, `brainreg`, `cellfinder`
* __Programming:__ `Spyder`, `Jupyter`, `VSCode`, `Shell`
  
Each __modality__ is represented by a homonymous markdown file, e.g. `docs/source/Behaviour.md`.
* The H1 heading of each markdown file is the title of the section
* The H2 headings are `tool`-specific sub-sections.
* The H3 headings should provide a succint description of an issue with a particular `tool`
* The text below the corresponding H3 headings should elaborate on the issue and provide a solution.

An dummy example is given below:

```md
# Behaviour

## SLEAP

### SLEAP is slow on Sundays
On Sundays you should be pre-occupied with sleeping, **NOT** SLEAPing
```
  
## Editing the website
* Clone the GitHub repository
```bash
git clone https://github.com/neuroinformatics-unit/troubleshooting.git
```
* Create a new branch
```bash
git checkout -b my_new_branch
```
* Edit the website and commit your changes
```bash
git add .
git commit -m "My new changes"
```
* Push your changes to GitHub
```bash
git push --set-upstream origin my_new_branch
```
* Create a pull request on GitHub. This will automatically trigger a GitHub Action that checks if the website still builds correctly.

* If the checks pass, assign someone to review your changes. The reviewer will merge your changes into the `main` branch, which will trigger a new GitHub Action that will build the website and publish it to the `gh-pages` branch. The website should be available at [troubleshooting.neuroinformatics.dev](https://troubleshooting.neuroinformatics.dev)
