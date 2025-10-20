# TeX Live Dev Container
This is a dev container specification for the latest version of the TeX Live LaTeX distribution. It closely follows the installation instructions from the TeX Live website and picks beginner-friendly default settings.

## Instructions
Copy the `.devcontainer` folder into your LaTeX project and install the Dev Containers extension in VS Code. **Read the following instructions before building the container.**

### Platforms
The configuration assumes you to use a `x86_64` system, which includes most computers, but, e.g., excludes ARM devices like Apple machines. Users of non-`x86_64` systems must adjust line 3 in `.devcontainer/texlive.profile` and lines 38, 45, 46, and 47 in `.devcontainer/Dockerfile`. See TeX Live's website for the list of supported platforms: https://tug.org/texlive/doc.html.

### VS Code Extensions
The dev container adds the LaTeX Workshop and Code Spell Checker extensions for LaTeX functionality and spell checking respectively. You can freely add or remove extensions and their settings by updating `customizations.vscode.extensions` and `customizations.vscode.settings` in `.devcontainer/devcontainer.json`.

### Scheme
The default container installs the full scheme, which is the complete TeX Live distribution with all packages. This is beginner-friendly but inefficient. Users with TeX Live experience should choose a smaller scheme and install required tex packages afterwards.

To switch to the basic scheme and install, e.g., the `latexmk` and `booktabs` packages, update the first line in `.devcontainer/texlive.profile` to
```bash
selected_scheme scheme-basic
```
and line 47 in `.devcontainer/Dockerfile` to
```bash
/home/tl/texlive/${TEXLIVE_YEAR}/bin/x86_64-linux/tlmgr update --all && \
/home/tl/texlive/${TEXLIVE_YEAR}/bin/x86_64-linux/tlmgr install latexmk booktabs
```
Add all the packages that your project requires to that last line.

### Program execution
By default, LaTeX distributions grant a restricted set of packages execution privileges via \write18. If you know that your project does not require this functionality, it is good practice to following the principle of least privilege and disable the feature by adding the following line to `.devcontainer/texlive.profile`:
```bash
instopt_write18_restricted 0
```

## Git
If you want to use Git, append it to line 8 in `.devcontainer/Dockerfile`.
```bash
apt install -y curl perl git
```
