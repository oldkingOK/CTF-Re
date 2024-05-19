# Pwntools

```shell
pip install pwntools
```

或者使用docker

```shell
docker run -d \
	--rm \
	-h ${ctf_name} \
	--name ${ctf_name} \
	-v $(pwd)/${ctf_name}:/ctf/work \
	-p 23946:23946 \
	--cap-add=SYS_PTRACE \
	skysider/pwndocker
```

```shell
 # 目录作为名称
$ctf_name = Split-Path -Leaf (Get-Location);docker run -d --rm -h ${ctf_name} --name ${ctf_name} -v ${pwd}:/ctf/work -v "D:\Program Files\IDA_Pro_v8.3_Portable\dbgsrv:/root/dbgsrv" -p 23946:23946 --cap-add=SYS_PTRACE skysider/pwndocker
```