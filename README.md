# Методичка по работе с HashiCorp Vault

*Замечание*: Все инструкции приведены для Windows.

## Полные инструкции:
- set-up: https://developer.hashicorp.com/vault/tutorials/get-started/setup
- ui: https://developer.hashicorp.com/vault/tutorials/get-started/learn-ui
- http-api: https://developer.hashicorp.com/vault/tutorials/get-started/learn-http-api

## Vault set-up
1. Установка vault: https://developer.hashicorp.com/vault/downloads#windows
2. Запуск vault  (в той директории, в которой находится vault.exe, либо добавить его в PATH под имя vault и использовать его отовсюду)
   ```
    ./vault server -dev -dev-root-token-id root
   ```
3. Добавляем путь до вольта в переменные среды:
   ```
    $env:VAULT_ADDR="http://127.0.0.1:8200"
   ```
4. Проверяем статус сервера:
   ```
    ./vault status
   ```
   Вывод:
   ```
   Key             Value
   ---             -----
   Seal Type       shamir
   Initialized     true
   Sealed          false
   Total Shares    1
   Threshold       1
   Version         1.18.3
   Build Date      2024-12-16T14:00:53Z
   Storage Type    inmem
   Cluster Name    vault-cluster-5d7649b0
   Cluster ID      f1c131fc-806f-71ca-d691-1194721bcded
   HA Enabled      false
   ```
5. Авторизация как developer:
   ```
   ./vault login root
   ```
   Вывод:
   ```
   Success! You are now authenticated. The token information displayed below
   is already stored in the token helper. You do NOT need to run "vault login"
   again. Future Vault requests will automatically use this token.
  
   Key                  Value
   ---                  -----
   token                root
   token_accessor       XmLpIclumxqVuaR8Oyq9mJj4
   token_duration       ∞
   token_renewable      false
   token_policies       ["root"]
   identity_policies    []
   policies             ["root"]
   ```
   
## Vault UI

Доступен по адресу `http://127.0.0.1:8200`. Вводим `root` в поле token.

![image](https://github.com/user-attachments/assets/926a387a-ea0e-4671-8701-e33281da955e)

1. Само хранилище секретов
   
   ![image](https://github.com/user-attachments/assets/85fbc43f-9ff3-41e3-855c-2f6733e22952)

2. Создадим в нашем хранилище директорию secret1и положим в нее 3 пары ключ-значение:

   ![image](https://github.com/user-attachments/assets/4ab9a999-0c6a-4bd6-8285-5ebe7de1f237)

3. Попробуем получить эти токены (см. секцию про использование Vault HTTP API)

## Vault HTTP API

### GET
Пошлем запрос на получение секрета `secret1`:

```
    curl.exe -X GET -H "X-Vault-Token: root" http://127.0.0.1:8200/v1/secret/data/secret1
```
Вывод:
```
{
   "request_id" :"4460c22c-c4f8-21d5-4655-f04f4bb16ba5",
   "lease_id": "",
   "renewable": false,
   "lease_duration": 0,
   "data":
   {
      "data":
      {
         "my_key_1": "my_secret_1",
         "my_key_2": "my_secret_2",
         "my_key_3": "my_secret_3"
      },
      "metadata":
      {
         "created_time": "2025-01-31T09:14:33.6437983Z",
         "custom_metadata": null,
         "deletion_time": "",
         "destroyed": false,
         "version": 1
      }
   },
   "wrap_info": null,
   "warnings": null,
   "auth":   null,
   "mount_type": "kv"
}
```

### POST

Положим новый токен "my_secret_token" в секрет `secret3` по ключу `token_id`. **Обязательно**: посылаемый json на корневом уровне должен иметь поле `data`:
```json
{
  "data": {
    "token_id": "my_secret_token"
  }
}
```
Итак, выполним запрос:
```
curl.exe -X POST -H "X-Vault-Token: root" -H "Content-Type: application/json" -d '{\"data\":{\"token_id\":\"my_secret_token\"}}' http://127.0.0.1:8200/v1/secret/data/secret3
```
Вывод:
```
{
   "request_id": "7ecff528-2e80-8a3b-a7a5-fee77559df57",
   "lease_id": "",
   "renewable": false,
   "lease_duration": 0,
   "data":
   {
      "created_time": "2025-01-31T12:34:19.8070105Z",
      "custom_metadata": null,
      "deletion_time": "",
      "destroyed": false,
      "version": 1
   },
   "wrap_info": null,
   "warnings": null,
   "auth": null,
   "mount_type": "kv"
}
```

#### Ошибки

1. Если послать не json, то будет ошибка:
   ```
   curl.exe -X POST -H "X-Vault-Token: root" -H "Content-Type: text/plain" -d 'my_secret_token' http://127.0.0.1:8200/v1/secret/data/my_secret_token_id
   ```
   Вывод:
   ```
   {"errors":["error parsing JSON"]}
   ```
2. Если послать json без поля data в корне, то будет ошибка:
   ```
   curl.exe -X POST -H "X-Vault-Token: root" -H "Content-Type: application/json" -d '{\"token_id\":\"my_secret_token\"}' http://127.0.0.1:8200/v1/secret/data/secret3
   ```
   Вывод:
   ```
   {"errors":["no data provided"]}
   ```
