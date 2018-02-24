## uninstall libraries installed by running setup.py

You need to remove all files manually, and also undo any other stuff that installation did manually.
If you don't know the list of all files, you can reinstall it with the --record option, and take a look at the list this produces.

To record a list of installed files, you can use:
```
python setup.py install --record files.txt
```
Once you want to uninstall you can use xargs to do the removal:

```
cat files.txt | xargs rm -rf
```

Or if you're running Windows, use Powershell:

```
Get-Content files.txt | ForEach-Object {Remove-Item $_ -Recurse -Force}
```
