# Мутирующие операции

## Создание ресурса

Для создания ресурса, используй команду `mws <service> <resource> create <id>` с необходимыми аргументами и флагами, например:

```shell
mws compute disk create diskst --disk-type nbs-pl2 --iops 1000 --size 10GB --zone ru-central1-a

mws vpc subnet create sub1 \
  --network net2 \
  --cidr 192.168.0.0/16

mws kms crypto-key create some-key \
  --default-algorithm AES_256_GCM \
  --enabled \
  --rotation-enabled \
  --rotation-interval-days 2
```

## Изменение ресурса

Для изменения ресурса, используй команду `mws <service> <resource> update <id>` с необходимыми аргументами и флагами, например:

```shell
mws kms crypto-key update deprecatedkey --enabled=false

mws mpostgres database update users --cluster master12 --deletion-protection

mws compute vm update craft-vm --vm-type gen-2-4 --hardware-power ON
```

Некоторые ресурсы создаются и обновляются через особые команды, используй help для получения корректной команды.
Например, ротация ключа:

```shell
mws kms crypto-key rotate <идентификатор ключа> 
```

## Удаление ресурса

Для удаления ресурса, используй команду `mws <service> <resource> delete <id>` с необходимыми аргументами и флагами, например:

```shell
mws secretmanager secret delete <идентификатор секрета>

mws compute vm delete my-vm

mws vpc subnet delete aasn4 --network x-network
```

## Флаги

В командах `create` и `update` флаги с типом `objectArray` передавай в формате `yaml`, например:

```shell
mws mpostgres cluster create gigcluster \
  --version "17" \
  --active \
  --endpoints '
    name: first
    network: projects/sandbox-project/networks/test-network
    primaryAddresses:
      - spec:
          subnet: projects/sandbox-project/networks/test-network/subnets/sub3
  ' \
  --instance-template-vm-type vmTypes/gen-2-8 \
  --instance-template-disk-type NETWORK_STANDARD_SSD \
  --instance-template-disk-size 20GB \
  --instance-template-disk-iops 1000 \
  --instances '
    count: 1
    zone: ru-central1-b
  '
```
