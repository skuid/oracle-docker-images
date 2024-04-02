# Building

Download the following installation files from http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html:
* `LINUX.ARM64_1919000_db_home.zip`
* `LINUX.X64_193000_db_home.zip`

Purge docker build files, or it'll likely run out of room: `docker builder prune -f`.

First build for arm64 (Apple Silicon). Move the ARM64 zipfile into the 19.3.0 directory, then run
```
./buildContainerImage.sh -v 19.3.0 -i -t quay.io/skuid/oracle:19.3.0-ee-aarch64 -e -a
```

Go make some coffee. The image must be pushed for `docker manifest create` to do its thing later:
```
docker push quay.io/skuid/oracle:19.3.0-ee-aarch64
```

Now build for x64. Move the X64 zipfile into the 19.3.0 directory, and move the ARM64 file out. You will probably need to purge build files again, `docker builder prune -f`.
```
./buildContainerImage.sh -v 19.3.0 -i -t quay.io/skuid/oracle:19.3.0-ee-amd64 -e
docker push quay.io/skuid/oracle:19.3.0-ee-amd64
```

Now create the multiarch manifest:
```
docker manifest create --insecure quay.io/skuid/oracle:19.3.0-ee --amend quay.io/skuid/oracle:19.3.0-ee-x64 --amend quay.io/skuid/oracle:19.3.0-ee-aarch64
docker manifest push quay.io/skuid/oracle:19.3.0-ee
```

Delete the intermediate x64 and aarch64 images to reclaim space, then take a break and let your CPU cool off.

