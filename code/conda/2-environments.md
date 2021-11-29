### Create/Activate/Delete

```
(base) > conda create -n test_env
(base) > conda activate test_env
(test_env) > conda deactivate
(base) > conda remove -n test_env --all
```

### Env Infos

```
> # this will show where environments are saved as well
(base) > conda info --envs
>
> # show packages installed in activated env
(test_env) > conda list
>
```

### Create from an environment.yml file:

```
(base) > conda env create -f environment.yml
(base) > conda env create --name envname --file=environments.yml
```
