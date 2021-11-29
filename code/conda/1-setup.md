Install miniconda
- DO NOT ADD MINICONDA TO PATH VARIABLE

Add miniconda to path using powershell:

```
function CondaOn {
    $env:PATH += ';C:\Users\371753\Miniconda3\Scripts\'
}

function CondaOff {
    $env:PATH = $env:PATH.replace(';C:\Users\371753\Miniconda3\Scripts\', '')
}
```

Now we can turn conda on, and use conda commands from the powershell. 

```
> conda init powershell
```

After this has completed, this will have been added to the bottom of the profile:

```
#region conda initialize
# !! Contents within this block are managed by 'conda init' !!
(& "C:\Users\371753\Miniconda3\Scripts\conda.exe" "shell.powershell" "hook") | Out-String | Invoke-Expression
#endregion
```

Move this into a function that we can activate in a scenario that we want to use conda:

```
function CondaOn {
    #region conda initialize
    # !! Contents within this block are managed by 'conda init' !!
    (& "C:\Users\371753\Miniconda3\Scripts\conda.exe" "shell.powershell" "hook") | Out-String | Invoke-Expression
    #endregion
}
```

Now we can activate it in a fresh powershell with `> CondaOn`