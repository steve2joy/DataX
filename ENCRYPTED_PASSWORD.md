# 使用加密密码配置说明

本文说明如何在 DataX 任务配置中使用加密后的数据库密码，并保持原有配置结构不变。

## 前提条件

1. 在 DataX 安装目录下准备密钥配置文件：`$DATAX_HOME/conf/.secret.properties`。
2. 在作业 JSON 中设置 `job.setting.keyVersion`，用于指定密钥版本。

> DataX 会根据 `job.setting.keyVersion` 读取 `.secret.properties` 中对应的密钥配置，并对 `password` 字段进行解密。

## 密文格式

请将密码值包裹为 `ENC(...)` 格式，例如：

```json
{
  "job": {
    "setting": {
      "keyVersion": "yourKeyVersion"
    },
    "content": [
      {
        "reader": {
          "name": "mysqlreader",
          "parameter": {
            "username": "yourUser",
            "password": "ENC(密文内容)",
            "connection": [
              {
                "jdbcUrl": "jdbc:mysql://127.0.0.1:3306/db",
                "table": ["table_name"]
              }
            ]
          }
        },
        "writer": {
          "name": "mysqlwriter",
          "parameter": {
            "username": "yourUser",
            "password": "ENC(密文内容)",
            "connection": [
              {
                "jdbcUrl": "jdbc:mysql://127.0.0.1:3306/db",
                "table": ["table_name"]
              }
            ]
          }
        }
      }
    ]
  }
}
```

> 仅需加密 `password` 字段，其他字段保持原样。

## 密钥配置说明

在 `.secret.properties` 中配置密钥版本与密钥内容，示例：

```properties
# 版本号
current.keyVersion=yourKeyVersion

# RSA 公私钥对（示例为占位内容）
current.publicKey=BASE64_PUBLIC_KEY
current.privateKey=BASE64_PRIVATE_KEY
```

或使用 3DES 密钥：

```properties
current.service.username=yourKeyVersion
current.service.password=YOUR_3DES_KEY
```

> 实际配置请按现有 DataX 密钥管理方式填写。

## 生成密文的建议

- 使用 DataX 现有密钥体系对应的算法（RSA 或 3DES）生成密文。
- 将生成的密文放入 `ENC(...)` 中写入配置。

## 注意事项

- 未设置 `job.setting.keyVersion` 时，DataX 不会进行解密。
- `ENC(...)` 仅用于 `password` 字段，其它字段不会被解密处理。
