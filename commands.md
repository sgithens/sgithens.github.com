# Useful Commands

## GPII Cloud Stuff

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

## Random

Disble Chrome Google Sign in and Sync
https://blog.ideasynthesis.com/2018/09/24/Disable-Google-Chrome-Sign-In-and-Sync/

```bash
defaults write com.google.Chrome SyncDisabled -bool true
defaults write com.google.Chrome RestrictSigninToPattern -string ".*@example.com"
```
