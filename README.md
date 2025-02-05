# Node modules folder switcher

the idea of this is when there is different branches that have different package.json dependencies

## The solution:
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

## How it works:
when switching to another branch that has the dependency changes, execute the the following `switch_modules <name of current> <name to be backed up>`, so for example if you are going from prod to dev you would do something like this `switch_modules dev prod`, this will make a new folder from the current `node_modules` to `node_modules_prod` then you can run `npm install` on the current directory to install the dev dependency 
then when you switch back to prod you would do some thing like this `switch_modules prod dev` this will renamce current `node_modules` to `node_modules_dev` and then will switch `node_modules_prod` to be `node_modules` meaning you can continue where you left off

## Aliases
you can also take advantage of shell aliases to make it easier to switch back and forth

examples
```bash
alias switch_to_dev="switch_modules dev prod"
alias switch_to_prod="switch_modules prod dev"
```
when you switched to dev branch then just execute `switch_to_dev` and when you are back on prod just execute `switch_to_prod`

## Advantages:
- Fast
- Easy to understand
- Not a bloatware

## Downsides:
- this approach is saving the two node_modules folders in the project which means double the amount of disk is going to be needed
- This is intended for Unix based systems, meaning it should work fine on Mac and linux, will add Power shell support in the future.
