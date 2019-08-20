# Steps to connect jupyter notebook hosted remotely
1. Download anaconda

2. Install anaconda
```bash
$ bash Anaconda3-2019.07-Linux-x86_64.sh
```

3. Generate config file:
```bash
$ jupyter notebook --generate-config
```

4. Generate password \(in IPython\)
```python
from IPython.lib import passwd
passwd()
```
> Enter password: 
> Verify password: 
> Out[2]: 'sha1:0e422dfccef2:84cfbcbb3ef95872fb8e23be3999c123f862d856' 

5. Modify the config file
```bash
$ vim ~/.jupyter/jupyter_notebook_config.py 
```
Change the following config:
```python
c.NotebookApp.ip='*'  # allow all ip to connect
c.NotebookApp.password = u'sha1:0e422dfccef2:84cfbcbb3ef95872fb8e23be3999c123f862d856'  # the passcode generated previously
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888 # bind the listening port
```

6. Launch jupyter notebook at the server

7. SSH to the port by command
```bash
ssh -N -f -L localhost:6000:localhost:8888 username@hostname
```
The command above represent:
```bash
ssh -L <local port>:<remote host>:<remote port> <SSH hostname>
```