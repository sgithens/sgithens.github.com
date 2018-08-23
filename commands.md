# Useful Commands

## GPII Cloud Stuff

### Amazon Console Login

`https://gpii-net.signin.aws.amazon.com/console`

### Forwarding CouchDB node to localhost
```bash
kubectl -n gpii port-forward couchdb-0 5984
```

On our AWS dev clusters, these secrets are stored in: `aws/modules/deploy/secrets/dev-sgithens.gpii.net-secrets.yml`
