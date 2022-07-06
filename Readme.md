
# rclone mount : acces in read-only mode to my data on oscar

mounting my s3 repo :

```console
nohup rclone mount --dir-cache-time=2m \
  --fast-list \
  --s3-chunk-size=1G \
  --s3-upload-concurrency=8 \
  --read-only \
  remote: /path/to/data/s3/ 1> rclone-mount.log 2> rclone-mount.error &
```
