## Задание 1

![curl](images/curl.png)
![vms](images/vms.png)

### Что было исправлено

1. `platform_id = "standart-v4"`:
- ошибка в названии платформы (опечатка `standart` вместо `standard`) и невалидная комбинация с `core_fraction = 5` для `standard-v3`.
- итог: для web-ВМ задана платформа `standard-v1`, где `core_fraction = 5` поддерживается.

2. SSH-переменная:
- приведено к требуемому имени `vms_ssh_public_root_key` (публичный ключ).

3. Подключение сервисного ключа:
- провайдер переведен на переменную `service_account_key_file` + `pathexpand(...)`.

### Для чего `preemptible = true` и `core_fraction = 5`

- `preemptible = true` снижает стоимость ВМ, полезно для учебных/временных стендов.
- `core_fraction = 5` сильно уменьшает гарантированную долю CPU и цену, подходит для легкой нагрузки в обучении.

## Задание 2

- Все хардкод-значения для web-ВМ и data image вынесены в переменные с префиксом `vm_web_`.
- Добавлены типы и `default`.

## Задание 3

- Создан файл `02/src/vms_platform.tf`.
- Вынесены переменные web-ВМ и добавлены переменные db-ВМ (`vm_db_*`).
- Добавлена вторая ВМ `netology-develop-platform-db` в зоне `ru-central1-b` с ресурсами `cores=2`, `memory=2`, `core_fraction=20`.

## Задание 4

В `02/src/outputs.tf` объявлен один output `vms_info` со структурой:
- `instance_name`
- `external_ip`
- `fqdn`

Фактический вывод `terraform output`:

```text
vms_info = {
  "db" = {
    "external_ip" = "158.160.5.192"
    "fqdn" = "epdo6r48arcoumjjdvnr.auto.internal"
    "instance_name" = "netology-develop-platform-db"
  }
  "web" = {
    "external_ip" = "46.21.244.52"
    "fqdn" = "fhm97tcmtagkv4uhsc2d.auto.internal"
    "instance_name" = "netology-develop-platform-web"
  }
}
```

## Задание 5

- В `02/src/locals.tf` создан один `locals` блок с интерполяцией имен обеих ВМ из нескольких переменных.
- В ресурсах ВМ используются `local.vm_web_name` и `local.vm_db_name`.

## Задание 6

- Введена единая map(object)-переменная `vms_resources` для обеих ВМ.
- Введена общая map-переменная `metadata`.
- Неиспользуемые старые переменные ресурсов закомментированы.
- `terraform plan` после apply: `No changes`.

## Задание 7*

Команды и вывод из `terraform console`:

1. Второй элемент `test_list`:
```text
local.test_list[1]
"staging"
```

2. Длина `test_list`:
```text
length(local.test_list)
3
```

3. Значение ключа `admin` из `test_map`:
```text
local.test_map["admin"]
"John"
```

4. Интерполяция:
```text
"${local.test_map[keys(local.test_map)[0]]} is ${keys(local.test_map)[0]} for production server based on OS ${local.servers.production.image} with ${local.servers.production.cpu} vcpu, ${local.servers.production.ram} ram and ${length(local.servers.production.disks)} virtual disks"
"John is admin for production server based on OS ubuntu-20-04 with 10 vcpu, 40 ram and 4 virtual disks"
```

## Задание 8*

Переменная `test` добавлена в `02/src/variables.tf`:

```text
type(var.test)
list(map(
    tuple([
        string,
        string,
    ]),
))
```

Выражение для получения строки ssh из первого элемента:

```text
var.test[0]["dev1"][0]
"ssh -o 'StrictHostKeyChecking=no' ubuntu@62.84.124.117"
```

## Задание 9*

- Настроен NAT Gateway:
  - `yandex_vpc_gateway.nat_gateway`
  - `yandex_vpc_route_table.develop`
  - маршрутизация `0.0.0.0/0` через gateway.
- Обе подсети привязаны к route table.
- Добавлен флаг `vm_use_public_ip` для отключения внешних IP (`nat = false`) при проверке сценария NAT-only.
