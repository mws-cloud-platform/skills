# Провязка зависимостей (references вместо литералов)

## Зачем

generated.tf содержит литералы (`network = "my-net"`, `ref = "vpc/projects/..."`). Terraform не видит связей → нет DAG → destroy удаляет в случайном порядке → 412 FAILED_PRECONDITION у родителей. Провязка даёт DAG → destroy в правильном порядке, в 1 проход.

## Правило: чем вязать

Категория поля определяется по ЗНАЧЕНИЮ в generated.tf (полный путь vs короткое имя):

| Категория поля | Признак в generated.tf | Вязать через | Пример |
|---|---|---|---|
| **ref-поле** | значение = полный путь `x/projects/p/...` | `<res>.metadata.id` | `subnet = mws_vpc_subnet.subnet_a.metadata.id` |
| **поле-имя** | значение = короткое имя | плоский Required-атрибут ресурса | `network = mws_vpc_network.network.network` |
| `.metadata.name` | — | НЕ использовать | deprecated |

Плоские атрибуты имён: `network.network`, `subnet.subnet`, `cluster.cluster_name`, `external_address.external_address`, `egress_nat.egress_nat`, `firewall_rule.firewall_rule`, `disk.disk`, `virtual_machine.virtual_machine`.

Почему не `.metadata.id` в поле-имя: id = полный путь, сервер валидирует path-сегменты regex формата имени → 400 INVALID_ARGUMENT (`must match "^[a-z]([a-z0-9-]{0,61}[a-z0-9])?$"`).

## Контроль

Провязка меняет выражения, НЕ значения. Сразу после правок:
```shell
terraform fmt && terraform plan
```
Ожидание: `0 to add, 0 to change, 0 to destroy` (или No changes). Есть changes → где-то перепутаны id/name — поправить соответствующее поле.

## Конфиг с нуля

Те же правила: ref-поля → `.metadata.id` зависимостей, поля-имена → плоские атрибуты. Ключевые Required-поля ресурса повторяют спеку; при сомнении — `terraform providers schema -json | jq '.provider_schemas["registry.terraform.io/mws-cloud/mws"].resource_schemas.mws_<тип>'` или help cli.

## Если 412 при destroy всё же случился

- В ПОЛНОМ Description ошибки есть `Usages` — API сам называет держателя ресурса
- Часть детей удаляется платформой асинхронно → пауза 2–3 мин и повторить
- Цикл довершения: `until terraform destroy -auto-approve; do sleep 120; done`
