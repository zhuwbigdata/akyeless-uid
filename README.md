# akyeless-uid

## Create auth method for UID
```
$ akeyless auth-method create universal-identity \
--name /devops/uid_auth \
--ttl 60 \
--profile devopsapi
```

## Associate auth method with access role
```
$ akeyless get-role "devops/devops_Engineer" --profile devopsapi
$ akeyless assoc-role-am -r "devops/devops_Engineer" -a "/devops/uid_auth" --profile devopsapi
```

## Generate UID token
```
UID_TOKEN=$(akeyless uid-generate-token --auth-method-name /devops/uid_auth --profile devopsapi --json --jq-expression .token)

## Validate 
https://docs.akeyless.io/reference/validatetoken
```
{
  "is_valid": true,
  "expiration": "2025-03-28 21:58:57 UTC",
  "ttl": 60,
  "last_rotate": "2025-03-28 20:58:57 UTC"
}
```

## Rotate with local file - once rotated, the previous no longer valid
```
$ echo $UID_ID > /tmp/token

$ akeyless uid-rotate-token -i /tmp/token -o /tmp/token
ROTATED TOKEN: [u-AQAAADwAAABpDufwKoo4K+r4all/r9ILtG640hcYLjNncGhlNHp2a3p2ZHVt]

$cat /tmp/token 
u-AQAAADwAAABpDufwKoo4K+r4all/r9ILtG640hcYLjNncGhlNHp2a3p2ZHVt


```

## Revoke self
```
$ akeyless get-secret-value -n /devops/static-secrets/data_gov_api_key --uid-token $(cat /tmp/token)

$ akeyless uid-revoke-token --uid-token $(cat /tmp/token) --revoke-token $(cat /tmp/token) --revoke-type revokeSelf

$ akeyless uid-revoke-token -h
Revoke token using Akeyless Universal Identity
Options:
  -r, --revoke-type               *revokeSelf/revokeAll (delete only this token/this token and his children)
```




