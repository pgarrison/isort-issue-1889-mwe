# Reproducing isort issue #1889
Log:

1. `pre-commit` cache is empty
```console
$ ls ~/.cache/pre-commit
ls: cannot access '/home/philip.garrison/.cache/pre-commit': No such file or directory
```

2. Clone this repo
```console
$ git clone git@github.com:pgarrison/isort-issue-1889-mwe.git
Cloning into 'isort-issue-1889-mwe'...
remote: Enumerating objects: 13, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 13 (delta 0), reused 10 (delta 0), pack-reused 0
Receiving objects: 100% (13/13), 5.10 KiB | 5.10 MiB/s, done.
$ cd isort-issue-1889-mwe
```

3. System configuration is recent
```console
$ python --version
Python 3.10.6
$ pip --version
pip 22.2.2 from /home/philip.garrison/.pyenv/versions/3.10.6/lib/python3.10/site-packages/pip (python 3.10)
$ pipenv --version
pipenv, version 2022.9.8
```

4. `pipenv` virtual environment is clean. Install the dependencies.
```console
$ pipenv --venv
No virtualenv has been created for this project(/home/philip.garrison/workspace/aics/isort-issue-1889-mwe) yet!
Aborted!

$ pipenv sync
Creating a virtualenv for this project...
Pipfile: /home/philip.garrison/workspace/aics/isort-issue-1889-mwe/Pipfile
Using /home/philip.garrison/.pyenv/versions/3.10.6/bin/python3 (3.10.6) to create virtualenv...
⠴ Creating virtual environment...created virtual environment CPython3.10.6.final.0-64 in 173ms
  creator CPython3Posix(dest=/home/philip.garrison/.local/share/virtualenvs/isort-issue-1889-mwe-rmCrJBLR, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/philip.garrison/.local/share/virtualenv)
    added seed packages: pip==22.2.2, setuptools==65.1.1, wheel==0.37.1
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator

✔ Successfully created virtual environment! 
Virtualenv location: /home/philip.garrison/.local/share/virtualenvs/isort-issue-1889-mwe-rmCrJBLR
Installing dependencies from Pipfile.lock (043a9b)...
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
All dependencies are now up-to-date!
$ pipenv shell
Launching subshell in virtual environment...
$  . /home/philip.garrison/.local/share/virtualenvs/isort-issue-1889-mwe-rmCrJBLR/bin/activate

$ pre-commit install
pre-commit installed at .git/hooks/pre-commit
```

5. This `pre-commit` command finds an error
```console
$ pre-commit run isort --all-files
[INFO] Initializing environment for https://github.com/pycqa/isort.
[INFO] Installing environment for https://github.com/pycqa/isort.
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
isort (python)...........................................................Failed
- hook id: isort
- files were modified by this hook

Fixing /home/philip.garrison/workspace/aics/isort-issue-1889-mwe/main.py

$ git diff
diff --git a/main.py b/main.py
index 366ffca..5b45831 100644
--- a/main.py
+++ b/main.py
@@ -1,2 +1,2 @@
-import time
 from dataclasses import dataclass
+import time

$ git add .
```

6. This pre-commit hook disagrees with the previous one
```console
$ git commit -m "fix pre-commit run isort --all-files"
isort (python)...........................................................Failed
- hook id: isort
- files were modified by this hook

Fixing /home/philip.garrison/workspace/aics/isort-issue-1889-mwe/main.py

$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   main.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   main.py

$ git diff
diff --git a/main.py b/main.py
index 5b45831..366ffca 100644
--- a/main.py
+++ b/main.py
@@ -1,2 +1,2 @@
-from dataclasses import dataclass
 import time
+from dataclasses import dataclass
```
