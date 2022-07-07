
# rclone mount

acces in read-only mode to my data on oscar:

```console
$ rclone config # setup URL and credentials
$ rclone listremotes
$ nohup rclone mount --dir-cache-time=2m \
  --fast-list \
  --s3-chunk-size=1G \
  --s3-upload-concurrency=8 \
  --read-only \
  remote: /path/to/data/s3/ 1> rclone-mount.log 2> rclone-mount.error &
```

# install miniconda3

```console
$ wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.12.0-Linux-x86_64.sh
$ bash Miniconda3-py39_4.12.0-Linux-x86_64.sh
$ conda -V
$ conda add -c bioconda
$ conda add -c conda-forge
```