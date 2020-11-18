# Minio 对象存储使用

## 安装指南

- [请移步到此处](http://docs.minio.org.cn/docs/)

## 启动服务并使用

```bash
    # 服务启动
    MINIO_ACCESS_KEY=admin MINIO_SECRET_KEY=as123456 minio server /mnt/minio/

    # 注册客户端
    mc alias set myminio http://192.168.201.40:9000 admin as123456 --api s3v4
```
