# vault_example

## Полные инструкции:
- set-up: https://developer.hashicorp.com/vault/tutorials/get-started/setup

## Шаги выполнения:
1. Установка vault: https://developer.hashicorp.com/vault/downloads#windows
2. Запуск vault  (в той директории, в которой находится vault.exe, либо добавить его в PATH под имя vault и использовать его отовсюду)
   ```
    ./vault server -dev -dev-root-token-id root
   ```
3. Добавляем путь до вольта в локальный DNS:
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

