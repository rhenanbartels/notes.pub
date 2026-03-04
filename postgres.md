## Dump

```
pg_dump \
  --verbose \
  --file {filename}.tar \
  --format=tar \
  --no-acl \
  --no-owner \
  --no-privileges \
  -U {username} \
  -h {host} \
  -p {port} \
  {db-name}
```

## Restore

```
pg_restore \
  --verbose \
  --format=tar \
  --no-acl \
  --no-owner \
  --no-privileges \
  -U {username} \
  -h {host} \
  -p {port} \
  -d {db-name} \
  {same-filename-as-dump}.tar
```
