Record down the steps of using github:
1. Create github repository
2. Initialise the local repo with command:
<p>git init</p>
3. Add remote origin for local repo with command:
<p>git remote add origin <url/ssh link></p>
4. Sync remote origin to local with command:
<p>git pull <upstream></p>
examples of upstream: origin master
5. when added the changes of the codes, use follow command:
<p>git add .</p>
<p>git commit -m ''</p>
The first line is to add all the changed files to staged
The second line is to commit all the changes
5. The last step is to push the changes to remote with following command:
<p>git push -u <upstream><branch></p>

Tips for some errors:
The authenticity of host 'github.com (192.30.255.112)' can't be established
The cause of this error is missing a known_host file. The SSH key is supposed to have 3 files, and now is only two files, the solution for this is to input 'yes' to allow the suthenticity.

[rejected] master -> master (fetch first)
[rejected] master -> master (non-fast forward)
The reason for the error is because remote repo is not sync to local, i.e. the repo is not pulled before commit the changes. 
Few solutions for this:
Solultion A:
If it is confirmed that the remote origin could be overwritten, then the following command will force the push:
<p>git push -uf <upstream><branch></p>

Solution B:
Otherwise, sync remote files to lcoal and merge the changes, then push the changes:
<p>git fetch</p>
<p>git merge</p>
The two commands above are equivalent to:
<p>git pull</p>
After this, another error may pop as show below:
![error1](https://img-blog.csdn.net/20170216141449767)
Below two params need to specify:
<p>remote = origin</p>
<p>merge = refs/heads/master</p>
To set up these two params, you can either update config file or use the following command:
<p>git config branch.master.remote origin</p>
<p>git confit branch.master.merge refs/heads/master</p>