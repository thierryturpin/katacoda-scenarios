# Step 4

Clone the repository, credentials will be shared via Teams chat  
```
git clone https://github.com/MicropoleBelgium/WUK.git
```{{execute}}

```
cd WUK
git branch -a
git checkout origin/qual
```{{execute}}

Provide your user name int the prompt
```
read myuser
```{{execute}}

```
echo "you have set: $myuser for username"
```{{execute}}

Set your git user name and e-mail 
```
git config --global user.email $myuser@micropole.be
git config --global user.name $myuser
```{{execute}}

Create a new branch from the currrent qual branch   
```
git checkout -b qual-$myuser
```{{execute}}

Create a copy of the reference script for your user and add it to git  
```
cp sparkscripts/csv_to_parquet_ref.py sparkscripts/csv_to_parquet_$myuser.py
git add sparkscripts/csv_to_parquet_$myuser.py
```{{execute}}

Edit your spark script. This can be done via the file tree.  

Edit the funtcion: `persist_results` so that the variable `parquet_file` includes your name like above  

Commit your changes:
```
git commit -m "Updated my script csv_to_parquet_$myuser.py so that s3 file include my name"
```{{execute}}

Push your changes:
```
git push --set-upstream origin qual-$myuser
```{{execute}}

Create a pull request  
