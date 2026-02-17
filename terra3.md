# Домашняя работа 03: Управляющие конструкции Terraform

## Задание 1

![security group](images/security_group.png)

## Задание 2

Сделано:
1. Создан `count-vm.tf` с двумя одинаковыми ВМ `web-1`, `web-2` через `count`.
2. Создан `for_each-vm.tf` с двумя ВМ БД `main` и `replica` через `for_each` по переменной:
   - `variable "each_vm" { type = list(object({ vm_name=string, cpu=number, ram=number, disk_volume=number })) }`
3. ВМ `web-*` создаются после `main/replica` через `depends_on`.
4. Для `metadata.ssh-keys` используется local с `file(pathexpand("~/.ssh/id_rsa.pub"))` и fallback-ключом.
5. Группа безопасности из задания 1 назначена web-ВМ.

## Задание 3

Сделано в `disk_vm.tf`:
1. Созданы 3 одинаковых диска по 1 ГБ через `yandex_compute_disk` + `count`.
2. Создана одиночная ВМ `storage`.
3. Подключение дисков к `storage` сделано через `dynamic "secondary_disk"` + `for_each`.

## Задание 4

Сделано:
1. В `ansible.tf` добавлена генерация inventory через `templatefile`.
2. Создан шаблон `hosts.tftpl`.
3. Inventory динамический и содержит 3 группы: `webservers`, `databases`, `storage`.
4. Для каждой ВМ добавлены `ansible_host` и `fqdn`.

Фактический `hosts.ini`:

![hosts.ini](images/hosts.png)

## Задание 5*

Добавлен output `vms_count_for_each` (итерация по ресурсам `count` и `for_each`):

![output](images/output.png)

## Задание 6*

Добавлен `null_resource` с `local-exec` в `ansible.tf`:
- `ansible-playbook` запускается только при `run_ansible_playbook = true`.
- По умолчанию `false`, чтобы не ломать `terraform apply` в окружении без ansible.

## Задание 7*

Выражение для удаления 3-го элемента (`index = 2`) из `subnet_ids` и `subnet_zones`:

```hcl
merge(
  { network_id = local.vpc.network_id },
  {
    subnet_ids   = concat(slice(local.vpc.subnet_ids, 0, 2), slice(local.vpc.subnet_ids, 3, length(local.vpc.subnet_ids)))
    subnet_zones = concat(slice(local.vpc.subnet_zones, 0, 2), slice(local.vpc.subnet_zones, 3, length(local.vpc.subnet_zones)))
  }
)
```

## Задание 8*

Ошибка в шаблоне:
- отсутствует закрывающая `]` после `nat_ip_address`;
- у ключа `platform_id ` лишний пробел в имени ключа.

Корректный фрагмент:

```hcl
${i["name"]} ansible_host=${i["network_interface"][0]["nat_ip_address"]} platform_id=${i["platform_id"]}
```

## Задание 9*

1. Список от `rc01` до `rc99`:

```hcl
[for n in range(1, 100) : format("rc%02d", n)]
```

2. Список от `rc01` до `rc96`, исключая окончания `0,7,8,9`, кроме `rc19`:

```hcl
[for n in range(1, 97) : format("rc%02d", n) if contains([1, 2, 3, 4, 5, 6], n % 10) || n == 19]
```
