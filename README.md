= meshconv

Standalone distribution of meshconv, extracted from Sofa ( https://github.com/sofa-framework/sofa )
using git filter-repo tool ( https://github.com/newren/git-filter-repo )

The following command has been used to create the inital version of the repository

```lang=bash
$ git-filter-repo --source Sofa --target meshconv --path applications/projects/meshconv --path-rename 'applications/projects/meshconv:meshconv' --path extlibs/miniFlowVR --path-rename 'extlibs/miniFlowVR:miniFlowVR' --preserve-commit-hashes --no-ff
```
