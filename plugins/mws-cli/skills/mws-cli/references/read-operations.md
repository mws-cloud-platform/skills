# Читающие операции

- Для получения конкретного ресурса, используй команду `mws <service> <resource> get <id>` с необходимыми аргументами и флагами, например:

```shell
mws compute disk get test-disk --profile main --project inter1

mws iam service-account get editor-sa

mws vpc subnet get my-subnet --network nw-ic-21
```

- Для получения списка ресурсов, используй команду `mws <service> <resource> list` с необходимыми аргументами и флагами, например:

```shell
mws mk8s cluster list

mws vpc network list

mws vpc one-to-one-nat list --network n-buga2

mws compute vm list --project sandbox
```
