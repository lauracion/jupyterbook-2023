# How to contribute to this webpage

This site is run as a JupyterBook. [Jupyterbooks are a way to build publication-quality books and documents from computational content.](https://jupyterbook.org/en/stable/intro.html)

The code for this book is [kept on GitHub](https://github.com/agu-openscience-innovations/jupyterbook-2023). Each presenter in the AGU session has been given a page on this site, [found under the `./speaker_content` directory](https://github.com/agu-openscience-innovations/jupyterbook-2023/tree/main/speaker_content).

## Building the book locally
```{note}
The following assumes some familiarity with Git/GitHub and a working Python environment.
```

1. Create a fork of the repository https://docs.github.com/en/get-started/quickstart/fork-a-repo
2. Clone your new forked repository `git clone <new repo url>` and enter the repository folder `cd jupyterbook-2023`
3. Install the python dependencies `pip install -r requirements.txt`
4. Make desired changes to Jupyterbook content
5. Build the book with `jupyter-book build .`
6. When ready, push your changes to GitHub and open a pull request to the [original repository](https://github.com/agu-openscience-innovations/jupyterbook-2023/pulls)

If you have any difficulties, please feel free to open an issue at https://github.com/agu-openscience-innovations/jupyterbook-2023/issues

## JupyterBook Documentation
Documentation for the JupyterBook project can be found here: https://jupyterbook.org/en/stable/intro.html

```{note}
In particular, pages on [Narrative](https://jupyterbook.org/en/stable/content/index.html#) and [Executable](https://jupyterbook.org/en/stable/content/executable/index.html) content may be interesting
```
