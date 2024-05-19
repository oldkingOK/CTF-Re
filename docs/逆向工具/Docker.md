# Docker

```shell
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
docker run --it --net host -v ${PWD}:/root ubuntu:22.04 /bin/sh # 共享网络
```

详细可以使用 `docker run --help` 查看