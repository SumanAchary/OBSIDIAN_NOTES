<hr>


```
pyenv install <version>
SAVE
.pyenv/versions/3.6.8/bin/python -m venv /home/suman/Projects/gravity/SAVE/save_venv
.pyenv/versions/3.6.9/bin/python -m venv /home/suman/Projects/fos/reimagine/reim_venv

.pyenv/versions/3.6.8/bin/python -m venv /home/suman/Projects/SAVE/save_venv

```


# PIP SYNC

```
We will be using pip-tools to pin down our dependencies. All explicit requirements will be added to the `requirements.in` file. Once added, you can run the pip-compile command which will auto generate the `requirements.txt` file required for the environment.

> In development environment, you should run the following command for installing the requirements `pip-sync requirements.txt dev-requirements.txt`

> Adding a new dependency  
> Dev - Add package to `dev-requirements.in`. Run `pip-compile dev-requirements.in`  
> Common - Add package to `requirements.in` . Run `pip-compile`

Run pip-sync only within virtual environments. Ensure you have pip-tools installed first using  
  
`$ source venv/bin/activate (venv)$ pip install -U pip-tools (venv)$ pip-sync`
```
