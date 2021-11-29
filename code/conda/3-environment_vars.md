```
> conda activate test-env
> conda env config vars list
>
> conda env config vars set my_var='hello'
> # need to reactivate environment
> conda activate test-env
> conda env config vars list
> conda env config vars unset my_var
> conda activate
```