---
jupytext:
    text_representation:
        extension: .md
        format_name: myst
kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# Example Markdown page
The source for this page can be [found here](https://github.com/agu-openscience-innovations/jupyterbook-2023/example/example.md)

This is some example text. Text can be **bold** or *italic*. You can add [links](https://github.com)

```{note}
You can create note blocks
```

You can create static code blocksß:

```python
import numpy as np
import matplotlib.pyplot as plt
x = np.arange(0, 10, 0.1)

print(x)

plt.plot(x)
```

Or also automatically display the output:
```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt
x = np.arange(0, 10, 0.1)

print(x)

plt.plot(x)
```