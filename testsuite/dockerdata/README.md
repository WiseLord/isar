# Creating image

```
export version="1.0"
cd <isar_dir>/testsuite/dockerdata
docker build -t ghcr.io/ilbers/docker-isar:${version} .
```

# Pushing the image to docker hub

- Configure github token (classic) with `write:packages` permissions.

- Use it for uploading docker image:

```
docker push ghcr.io/ilbers/docker-isar:${version}
```

- Make the uploaded package public 
