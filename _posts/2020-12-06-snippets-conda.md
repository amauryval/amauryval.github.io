---
title: "Snippets - conda"
last_modified_at: 2020-12-06T10:37:45
categories:
  - Blog
tags:
  - Conda
  - Python
  - Snippets
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---

Some Anaconda commands snippets


# Conda environment list
```
conda info --envs
```

# Installed packages list on current environment
```
conda list
```

# Installed packages list on current environment
```
conda list
```

# Environment creation
```
conda create -y --name MY_ENV python=PYTHON_VERS
```

# Add channel
```
conda config --add channels CHANNEL_NAME
```

# Environment managment

## Creation

### A new environement
```
conda create --yes --name MY_ENV python=PYTHON_VERS
```

### From an environment.yml file
```
conda env create -f environment.yml
```

### From an requirements.txt file
```
conda install --yes --file requirements.txt
```

## Export

### To an environment.yml file
```
conda env export > environment.yml
```

### To an requirements.txt file
```
conda list -e export > requirements.txt
```

## Misc

### Clone an existing environment
```
conda create -y --clone MY_ENV --name MY_CLONED_ENV
```

### Remove an environment
```
conda env remove -n MY_ENV
```
