# InEnv

A simple utility to manage multiple virtual python environments and activate them when you need it 


### Install

```sh
$ pip install inenv
```

### Usage

Create a config file called `inenv.ini` in your project root directory:

```yml
[webproject]
deps: file:requirements.txt
    
[service]
deps: requests==1.4 file:subproject/app/requirements.txt
```

Example Usage (in any directory of the project):

```sh
# Initialize inenv
inenv init webproject
# Note this will tell you to add a file to source in bash, if you want to switch envs in your shell, do this.  

# Switches your current env to webproject
inenv webproject python 
 
inenv service
pip freeze

# Runs `python manage.py syncdb` in the webproject venv
# Use posix style -- to pass all args
inenv webproject -- python manage.py syncdb --hello 
```

Python Lib

```python
from inenv.venv import VirtualEnv
    
venv = VirtualEnv('webproject', '/User/.virtualenvs')
venv.create_if_dne()
venv.delete()
venv.create_if_dne()
    
venv.install_requirements_file('requirements.txt')
venv.install_deps(['requests==2.0'])
    
venv.activate()
subprocess.check_output(['pip','freeze'])
venv.deactivate()
subprocess.check_output(['pip','freeze'])
    
venv.run(['pip','freeze'])
    
with  VirtualEnv('webproject', '/User/.virtualenvs') as venv:
    subprocess.check_output('python --version')
        
    
from inenv.inenv import InenvManager
    
manager = InenvManager("/project/inenv.ini") # file optional will look for one
venv = manager.get_prepped_venv('webproject')
# Sase as VirtualEnv above
```

Fun (don't do this; it's unreliable and dangerous): 

```python
from inenv.venv import VirtualEnv

try:
   import django
except ImportError:
   print "couldn't import django"

test_venv = VirtualEnv('test1', '/Users/pnegahdar/.venvs')
test_venv.create_if_dne()
test_venv.install_deps(['django==1.7'])

test_venv2 = VirtualEnv('test2', '/Users/pnegahdar/.venvs')
test_venv2.create_if_dne()
test_venv2.install_deps(['django==1.8'])

with test_venv:
   import django
   print django.get_version() # 1.7

with test_venv2:
   reload(django)
   print django.get_version() # 1.8
```
