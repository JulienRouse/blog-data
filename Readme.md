

[![CircleCI](https://circleci.com/gh/JulienRouse/blog-data.svg?style=svg)](https://circleci.com/gh/JulienRouse/blog-data)

Personal website up at https://www.julienrouse.com 

With circleci, push the result to the repo [JulienRouse/JulienRouse.github.io](https://github.com/JulienRouse/JulienRouse.github.io) on each commit. (unless commit message contains "[ci skip]")

## setup

Because this use submodule, you can checkout the submodules with

```
git submodule init
git submodule update
```

(or maybe `git clone --recurse-submodules <repo>` if you did not do the `clone` yet)
