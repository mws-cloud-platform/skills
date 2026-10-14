# Импорт существующих ресурсов в terraform

## Подготовка

Окружение и аутентификация — см. setup.md.

## imports.tf

Один import-блок на ресурс. id = полный путь ресурса:
```hcl
import {
  to = mws_vpc_network.network
  id = "vpc/projects/my-proj/networks/my-net"
}

import {
  to = mws_mk8s_cluster.cluster
  id = "mk8s/projects/my-proj/clusters/my-cluster"
}
```

Полные id ресурсов — из вывода `mws <service> <resource> list` / `get`.

## Генерация конфигурации

```shell
terraform init
terraform plan -generate-config-out=generated.tf
```

## Правки после генерации (обязательные)

1. **Ложное пересоздание (forces replacement)**: если после импорта plan требует пересоздать ресурс из-за одного опционального поля — сверь фактическое значение со state (`terraform state show <ресурс>`) и выставь в конфиге именно его (в т.ч. `null`, если в state null). generate-config-out пишет поля не в том виде, в котором сервер их хранит.
2. **Провязка зависимостей** — см. wiring.md. Без неё destroy ломается 412.
3. `terraform fmt`

## Проверка

```shell
terraform apply -auto-approve   # импортирует в state
terraform plan                  # → No changes
```
