---
name: mws-terraform
description: "Работа с Terraform в облаке MWS Cloud Platform (провайдер terraform-provider-mws). Следует использовать данный скилл для любых задач, связанных с terraform и ресурсами MWS: конфигурация, импорт, изменение, удаление."
---

# MWS Terraform

Terraform с провайдером terraform-provider-mws для облака MWS Cloud Platform.

Документация (обзор, troubleshooting, FAQ): https://mws.ru/docs/cloud-platform/terraform/general/whatis-terraform.html
Исходники провайдера: https://github.com/mws-cloud-platform/terraform-provider-mws

## Когда использовать

1. Подготовка окружения
2. Изучение схемы провайдера
3. Создание конфигурации
4. Импорт существующих ресурсов
5. Применение конфигурации (plan / apply / destroy)

## Как использовать

1. Получи запрос от пользователя
2. Проверь запрос по категориям ниже
3. Открой соответствующий файл в `references/` и действуй по нему

| Категория | Файл |
|---|---|
| Подготовка окружения, базовый цикл plan/apply/destroy | [references/setup.md](references/setup.md) |
| Изучение схемы провайдера | [references/schema.md](references/schema.md) |
| Импорт ресурсов, generate-config-out | [references/import.md](references/import.md) |
| Создание конфигурации, провязка зависимостей | [references/wiring.md](references/wiring.md) |

## Правила

1. Мутирующие операции (apply, destroy, import) — ТОЛЬКО с явного подтверждения пользователя: «примени/удали/импортируй» — это запрос, а не подтверждение. Перед мутациями показывай план (`terraform plan`) изменений
2. Секреты — предпочтительно env (MWS_TOKEN и пр.); для длинноживущих — отдельный gitignored-файл `secret.tfvars`, передавать явно `-var-file=secret.tfvars`. Файлы `*.auto.tfvars` для секретов запрещён
3. Аутентификация — см. setup.md (ключ SA — основной способ; при работе через токен выпускай свежий перед каждым блоком terraform-команд, env не переживает перезапуск shell)
4. После ручных правок .tf — `terraform fmt` и `terraform validate`
