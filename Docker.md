some command:

```
# pull
docker pull xxx

# run with background
docker run -dti -p 80:80 -p 81:81 image_id

# in container bash
docker exec -it container_id bash

# copy resource in container
docker cp host_path container_id:container_path

docker stop/start

docker ps [-a]

docker rm container_id

```
