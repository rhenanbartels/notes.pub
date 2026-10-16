## Log requests
sudo watch -n1 'docker logs --since 1m <<container>> 2>&1 | grep -oE "\"(GET|POST|PUT|PATCH|DELETE|WebSocket) [^ ?\"]+" | sort | uniq -c | sort -rn | head -15'

## Container stats
sudo docker stats <<container>>
