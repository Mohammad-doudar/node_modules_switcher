# Node modules folder switcher

the idea of this is when there is different branches that have different package.json dependencies

## The solution:
### Bash/Zsh
```bash
function switch_modules {
	local from_folder="node_modules_$1";
	local to_folder="node_modules_$2";
	mv node_modules "$to_folder";
	mv "$from_folder" node_modules;
}
```
add the above in either `.zshrc`/`.bashrc` or `.profile`/`.zsh_profile`
NOTE: adding it to `.profile` will make it so that any change on it will take effect on the next sign it, but this approach is the preferred one since the shell is going to only once will read this function while the `rc` method makes it everytime the shell is open it will get parsed.

### Powershell
```powershell
function Switch-Modules {
	param (
		$From,
		$To
	)
	$From_Folder = "node_modules_${From}"
	$To_Folder = "node_modules_${To}"
	Rename-Item -Path "node_modules" -NewName $To_Folder
	Rename-Item -Path $From_Folder -NewName "node_modules"
}
```

add the above function to a file in the following files (if not created you can create the directories):
- Windows: `$HOME\Documents\PowerShell\profile.ps1` - $Home is your current user directory which is normally in `C://Users/<UserName>`
- Mac/linux: `~/.config/powershell/profile.ps1`

Note: profiles in Powershell are executed at runtime of the shell one by one sorted by name meaning `1.ps1` will be runned before `2.ps1` for more about Powershell Profiles consider reading the [Docs](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.5)


## How it works:
when switching to another branch that has the dependency changes, execute the the following `switch_modules <name of current> <name to be backed up>`, so for example if you are going from prod to dev you would do something like this `switch_modules dev prod`, this will make a new folder from the current `node_modules` to `node_modules_prod` then you can run `npm install` on the current directory to install the dev dependency 
then when you switch back to prod you would do some thing like this `switch_modules prod dev` this will renamce current `node_modules` to `node_modules_dev` and then will switch `node_modules_prod` to be `node_modules` meaning you can continue where you left off

## Aliases
you can also take advantage of shell aliases to make it easier to switch back and forth

### Bash/Zsh
examples
```bash
alias switch_to_dev="switch_modules dev prod"
alias switch_to_prod="switch_modules prod dev"
```
when you switched to dev branch then just execute `switch_to_dev` and when you are back on prod just execute `switch_to_prod`

### Powershell
unfortunatly Powershell doesn't seem to support providing args for Aliases, but you can use a function instead to do pretty much the exact same thing:

```powershell
function switch_to_dev {
	Switch-Modules -From dev -To prod
}

function switch_to_prod {
        Switch-Modules -From prod -To dev
}
```

## Advantages:
- Fast
- Easy to understand
- Not a bloatware

## Downsides:
- this approach is saving the two node_modules folders in the project which means double the amount of disk is going to be needed
- This is intended for Unix based systems, meaning it should work fine on Mac and linux, will add Power shell support in the future.
