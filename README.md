Supported by [Nikola](https://getnikola.com/)

## new
```sh
python3 -m venv .venv
.venv/bin/python -m pip install -U pip setuptools wheel
.venv/bin/python -m pip install -U "Nikola[extras]"
```

## start using
```sh
source bin/activate
env EDITOR=vim nikola new_post -e -f markdown
```