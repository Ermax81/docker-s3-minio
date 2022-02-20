# docker-s3-minio

This repository is a template to implement S3 container using minio/minio image.
A bucket named 'test' will be create and files can be find in storage folder.

How to launch the service
```docker-compose up -d```

How to access the service: you can use any sftp client using the following parameters.
- Hostname: localhost
- Port: 9000
- User: minio
- Password: minio123

How to stop the service
```docker-compose down```

Source: https://github.com/minio/minio/tree/master/docs/orchestration/docker-compose

To add minio server instance, modify nginx.conf :
- upstream minio part
- upstream console part
