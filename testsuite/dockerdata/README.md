# Creating image

```
export version="4.4"
cd <isar_dir>/testsuite/dockerdata
sed "s/:<version>/:${version}/" Dockerfile | docker build -t ghcr.io/ilbers/docker-isar:${version} -
```

# Pushing the image to docker hub

- Configure github token (classic) with `write:packages` permissions.

- Use it for uploading docker image:

```
export version="4.4"
docker push ghcr.io/ilbers/docker-isar:${version}
```

- Make the uploaded package public
