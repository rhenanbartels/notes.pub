## Dump

```
pg_dump \
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
