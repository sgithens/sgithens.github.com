# Useful Commands

## Using Portacle SBCL from Atom 

```lisp
;; Inside the portacle repl
(swank:create-server :port 8099 :dont-close T)
```

After installing `SLIMA` in Atom, run the above in the portacle repl to start up
a new swank server, then set the connection port in the Atom SLIMA preferences to
the port used. After that you can run the `slime:connect` command 


## Infusion

```javascript
fluid.logObjectRenderChars = 8000;
```

## git

```bash
# https://sethrobertson.github.io/GitFixUm/fixup.html#remove_deep
# Removing a branch that was merged in accidentally for a PR (ie. don't edit history on actual shared branches)
git reset --hard @{u}
git rebase -p --onto 6a6bcf622534a69be58d7948c8c5e04055e7df60^ 6a6bcf622534a69be58d7948c8c5e04055e7df60
git rebase -p --onto 9e615a2ddef65590b4b3e1a6e35ce159e0a57041^ 9e615a2ddef65590b4b3e1a6e35ce159e0a57041
git rebase -p --onto 2dba6dad9546dccab704438855f5c35cc8aa9036^ 2dba6dad9546dccab704438855f5c35cc8aa9036
git fetch GPII; git merge GPII/master
git push -f sgithens gpii-228-2018
```

Commiting only certain hunks from a file, with prompts for each hunk:

```bash
# https://stackoverflow.com/questions/1085162/commit-only-part-of-a-file-in-git
git add --patch filename.js
```

## GPII Development

### Grepping for Atkins SR warnings

`egrep -nR 'WARN.*com\.freedomscientific\.jaws' out3.txt`

### Working on Universal and Windows Side by Side

```bash
# Windows and Universal Side by side
$ ls
gpii-universal	windows
# When Changes are made in universal, just rsync back in to windows to avoid having to push all
# the way up to github during work on acceptance tests and what not
$ rsync -avzh gpii-universal/ windows/node_modules/gpii-universal
```

## Debugging gpii with the chrome devtools

Start the windows, universal, or whatever repo with --inspect and look it up from `about://inspect`

```bash
node --inspect gpii.js
# In the chrome bar
about://inspect
# Down the page there should be a link to the running node instance
```

## Simple local webserver

```python
#python2
python -m SimpleHTTPServer 5000
```

## Getting all the dev releases and other info from an npm module
```
npm info --json gpii-express-user
```

## Sending output to both the shell and a file
```bash
# The command here is just simple `rake`
rake 2>&1 | tee out.txt
```

On windows powershell
```powershell
# `npm test` is the command
X:\windows> npm test | Tee-Object -FilePath ./output.txt
```

Powershell regular redirection.
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_redirection

```powershell
# All output to a file
.\script.ps1 *> script.log
# Errors and output to a file
npm test 2>&1 > .\dir.log
```

## Renewing the windows 10 eval VM license

```
# In powershell as administrator
slmgr /rearm
```

## Docker
Run a command in a running container
```bash
docker exec -it 418e3c7099b6 ls a/random/path
```

Rebuild `gpii` repos and push up to hub
```bash
docker build . -t gpii-devpmt
docker tag gpii-devpmt sgithens/gpii-devpmt:latest
docker push docker.io/sgithens/gpii-devpmt:latest
```

## Setting up a local couchdb instance

Simply run the following in a directory where the data should be persisted, or swap out the pwd to set up a number of development and testing images.

```bash
docker run -p 5984:5984 -v $(pwd):/opt/couchdb/data -d couchdb:2.2
```

### Backing up local couch db for dev and migration testing

We can stop couch, cp the data dir as a backup, and restart it.

```bash
# Stop couch
cp -R ./couch-db-data ./couch-db-data-BEFORE-TEST
# Start couch

# To go back to the old version, completely remove the first dir and restore
# the backup
# Stop Couch
rm -fR ./couch-db-data
cp -R ./couch-db-data-BEFORE-TEST ./couch-db-data
# Start Couch
```

## GPII Cloud Stuff

### GCP

#### Running the cluster CouchDB Fauxton locally

```bash
rake couchdb_ui
```

#### Spinning up the user-acounts test cluster

January 3, 2020 12:39 PM

```bash
# Building the user-accounts setup
cd universal
# rm -fR node_modules build
docker build --rm -t sgithens/universal:GPII-2966-$(git rev-parse --short HEAD) .
docker push docker.io/sgithens/universal:GPII-2966-2f365c41

cd ../gpii-devpmt
rm -fR node_modules reports coverage
docker build --rm -t sgithens/gpii-devpmt:user-accounts-$(git rev-parse --short HEAD) .
docker push docker.io/sgithens/gpii-devpmt:user-accounts-e950c2d
```

#### Fire up an alpine container to do debugging sluething in...

in this example testing out the redis service. Some serious Escher nesting of containers.

```bash

rake plain_sh

# Now we're in execube
kubectl run -i -t alpine --image=alpine --restart=Never

# Now we're in the cluster alpine pod
ping preferences.gpii.svc.cluster.local
apk add redis
redis-cli -h redis-master.gpii.svc.cluster.local -a **********
```

#### Force delete / rm a bucket

https://stackoverflow.com/questions/29840033/fast-way-of-deleting-non-empty-google-bucket
```bash

gsutil -m rm -r gs://my-bucket
```

### Amazon Console Login

`https://gpii-net.signin.aws.amazon.com/console`

### Forwarding CouchDB node to localhost
```bash
kubectl -n gpii port-forward couchdb-0 5984
```

On our AWS dev clusters, these secrets are stored in: `aws/modules/deploy/secrets/dev-sgithens.gpii.net-secrets.yml`

### Rebuilding universal for my aws cluster

```bash
# In Universal
# Get rid of node_modules so Docker will rebuild any c deps without taking in artifacts from your potentially different
# laptop architecture
mv node_modules ../univ_node_modules
docker build . -t sgithens/gpii-universal:latest
docker push sgithens/gpii-universal:latest
The push refers to repository [docker.io/sgithens/gpii-universal]
f0906557526b: Pushed
6df29d76c65e: Pushed
d13c20aaa839: Layer already exists
2832e3e1d3a9: Layer already exists
0f0aa102cc1f: Layer already exists
9dfa40a0da3b: Layer already exists
latest: digest: sha256:f9fa332873bc78476a6543e5743c31a144cd19ff57f9be8eae529fedd2165ba9 size: 1580
# Use above digest to update version.yml for flowmanager and preferences

# In gpii-infra/aws/dev
kubectl delete svc/kubernetes
rake
```

## MySQL

```bash
create user username@hostname;
set password for 'username'@'hostname' = PASSWORD('*******');
create schema myschema default character set 'utf8';
grant all privileges on myschema.* to 'username'@'hostname';
show grants for 'username'@'hostname';
```

## Random

Disble Chrome Google Sign in and Sync
https://blog.ideasynthesis.com/2018/09/24/Disable-Google-Chrome-Sign-In-and-Sync/

```bash
defaults write com.google.Chrome SyncDisabled -bool true
defaults write com.google.Chrome RestrictSigninToPattern -string ".*@example.com"
```
