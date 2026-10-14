# Подготовка окружения и базовый цикл

## Установка Terraform

Бинарный файл с [официального сайта](https://developer.hashicorp.com/terraform/install); если официальный сайт недоступен — зеркало MWS `https://hc-mirror.mws.cloud`. Либо пакетный менеджер: `brew install hashicorp/tap/terraform` / `snap install terraform --classic` / `choco install terraform`.

## Mirror провайдера (обязательно)

`~/.terraformrc`:
```hcl
provider_installation {
  network_mirror {
    url = "https://storage.mwsapis.ru/mws-terraform/"
    include = ["registry.terraform.io/mws-cloud/*", "registry.opentofu.org/mws-cloud/*"]
  }
  direct {
    exclude = ["registry.terraform.io/mws-cloud/*", "registry.opentofu.org/mws-cloud/*"]
  }
}
```
Без mirror `terraform init` падает с `Invalid provider registry host`.

## Аутентификация

Основной способ — авторизованный ключ сервисного аккаунта (SA с ролью `editor` или выше на проект):

- Если у пользователя нет SA или ключа — предложи создать их (можно через mws cli) и дождись явного подтверждения
- Ключ подключается одним из способов:
  - env: `export MWS_SERVICE_ACCOUNT_AUTHORIZED_KEY_PATH=<путь к файлу ключа>`
  - атрибут `service_account_authorized_key_path` в provider-блоке

### Установка MWS CLI

Утилита MWS CLI — удобный способ создать авторизационный ключ или токен. Если у пользователя она не установлена, предложи одно из двух:
1. Скилл `mws-cli` (https://github.com/mws-cloud-platform/skills, README → Installation) — умеет и установить утилиту, и полноценно с ней работать; предпочтительный путь
2. Напрямую бинарь:
  - Linux/macOS: `curl -sSL https://storage.mwsapis.ru/mws-cli/install.sh | bash`
  - Windows (PowerShell): `iex (New-Object System.Net.WebClient).DownloadString('https://storage.mwsapis.ru/mws-cli/install.ps1')`

### Создание авторизованного ключа

Провайдер ждёт JSON `{keyId, algorithm, privateKey}`, где `keyId` — БЕЗ префикса `iam/` (валидация: `^projects/.../serviceAccounts/.../authorizedKeys/...$`). Ответ `mws iam authorized-key create` содержит полный `metadata.id` и `status.privateKey` — собирай файл одной командой:

```bash
KEYFILE="$(mktemp)"
mws iam authorized-key create <имя> --service-account <sa> --key-algorithm ES256 --active -f json | \
  jq '{keyId: (.metadata.id | sub("^iam/"; "")), algorithm: "ES256", privateKey: .status.privateKey}' > "$KEYFILE"
export MWS_SERVICE_ACCOUNT_AUTHORIZED_KEY_PATH="$KEYFILE"
```

После завершения работы (в конце сессии):
```bash
rm -f "$MWS_SERVICE_ACCOUNT_AUTHORIZED_KEY_PATH"
mws iam authorized-key delete <имя> --service-account <sa>
```

Альтернатива для коротких тестовых операций — токен: `export MWS_TOKEN=<токен>` (живёт ~5 минут для OAuth, ~12 часов для SA). Для создания токена удобно использовать команду `mws iam create-token`

## Конфигурация провайдера

```hcl
terraform {
  required_providers {
    mws = { 
      source = "mws-cloud/mws"
      # version = "..."
    }
  }
  required_version = ">= 1.11"
}

provider "mws" {
  project = "<имя проекта>"
  zone    = "ru-central1-a"
}
```

## Хранение состояния

До первого `terraform init` уточни у пользователя, где хранить состояние:
- локально — по умолчанию, достаточно для разовых и тестовых конфигов;
- Object Storage (S3-бэкенд) — для инфраструктуры, которая живёт дольше эксперимента: совместная работа, CI/CD, состояние переживает машину.

Для S3-бэкенда следуй инструкции: https://mws.ru/docs/cloud-platform/terraform/general/terraform-states-storage.html

## Базовый цикл

```shell
terraform fmt          # после любых ручных правок .tf
terraform validate     # после любых ручных правок .tf
terraform plan         # сверка конфига и реальности
terraform apply -auto-approve
terraform destroy -auto-approve
```

Порядок работ с зависимостями и поведение destroy при ошибках — см. wiring.md.
