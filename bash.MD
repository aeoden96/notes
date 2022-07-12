```
apt-get install $(grep -vE "^\s*#" requirements.ubuntu.txt | tr "\n" " ")
```
```
export PYTHONPATH="${PYTHONPATH}:/new/path/" 


remove local commit (but keep changes)
git reset HEAD^

reset to last commit 
git checkout .

```

You can do the following to change packages from dependencies to explict with:


```
sudo pacman -D --asexplicit <package-name>
Or the other way around and mark something as a dependency:

sudo pacman -D --asdeps <package-name>
```


