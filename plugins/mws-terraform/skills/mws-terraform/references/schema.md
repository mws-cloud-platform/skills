# Изучение схемы провайдера

## Источники

1. `terraform providers schema -json | jq '.provider_schemas["registry.terraform.io/mws-cloud/mws"]'` — точная схема установленной версии; один ресурс: `terraform providers schema -json | jq '.provider_schemas["registry.terraform.io/mws-cloud/mws"].resource_schemas.mws_<тип>'`
2. Документация провайдера на registry
3. OpenAPI-спека MWS — источник истины: ресурсы провайдера генерируются из неё

## Устройство ресурсов (следствие генерации из спеки)

- id ресурса = полный путь (`vpc/projects/<proj>/networks/<name>`)
- Атрибуты повторяют спеку; Required-поля верхнего уровня (`network`, `subnet`, `cluster_name`, ...) — это path-сегменты, ожидают короткие имена
- Поля, ссылающиеся на другие ресурсы (`ref`, `subnet` у address, `cluster_name` и т.п.), принимают один из двух видов значений: полный путь (ref-поля) или короткое имя (поля-имена) — категории и правило провязки в wiring.md
- Сервисные префиксы: `vpc`, `mk8s`, `compute`, `iam`, `mpostgres`, `mkafka`, `kms`, ...

## Data sources

Для чтения существующих ресурсов без управления ими: образы, типы ВМ, сети, существующие кластеры и т.п.

```hcl
data "mws_compute_image" "image" {
  image   = "mws-ubuntu-2404-lts-v20260916"
  project = "mws-ubuntu"
}
```

Ссылки на data sources — по тем же правилам, что и на ресурсы: `data.mws_compute_image.image.metadata.id` (ref-поля).

Полный список — `terraform providers schema -json`, раздел `data_source_schemas` (ключи через jq `| keys`).

## Подбор ресурса

Задача → тип ресурса: сети/подсети/адреса/egress-nat/firewall → `mws_vpc_*`; кластеры/node groups → `mws_mk8s_*`; диски/ВМ → `mws_compute_*`. Точный список — schema или документация.
