## Step 01: Verify if ssh keypair is present on Dardel
```
ls ~/.ssh/id_rsa.pub
# If the file is not present, then you can generate your ssh keypair by issuing follwing (accept default answers)
ssh-keygen
```
## Step 02: Copy public key to Github.
```
On Dardel issue;
cat ~/.ssh/id_rsa.pub
```
- Now login to Github account and go to Settings -> SSH and GPG Keys -> New SSH Key
- Paste contents of your public key from Dardel in Key field and in title you can write "Dardel", so that you remember later on that this key was from Dardel.

## Step 03: On Dardel, do following;
```
git config --global user.name "My Name"
git config --global user.email "mygitemail@host.com"
```
- Verify config by issuing `git config --list` and press 'q' to quit.
- Now login to github account from Dardel by issuing following;
```
ssh -T git@github.com
```
You should get a response as following, if not, then please revist previous steps.
```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```
## Step 04: Now you can clone private/public repo from your github account
```
git clone git@github.com:username/myprivaterepo.git
```
## Step 05: To Push changes to the cloned repo
```
cd myprivaterepo
# Make changes
touch anewfile
git add .
git status
git commit -m "A new file"
git push
```
