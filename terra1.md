# Решения  
1. Задача 0  
[Скрин решения](https://dropmefiles.com/nfgld)  
2. Задача 1.2  
каталог personal.auto.tfvars
3. Задача 1.3  
$0$Fbf9yXYJhmV1OFaooeBT2edXv.NDCv1rWUNomTBRm1psrWVrQTNFi
4. Задача 1.4  
Ошибки  
resource "docker_image" "nginx" {  # Нет второго блока, (uniq_name) уникального названия, задается самостоятельно, распространяется на текущий проект.  
resource "docker_container" "nginx" {  # Имя может начинаться с буквы или подчеркивания _ и может содержать только буквы, цифры, подчеркивания и тире.  
5. Задача 1.5  
[Скрин решения](https://dropmefiles.com/bxSDl)  
[Скрин решения](https://dropmefiles.com/7q4oo)  
6. Задача 1.6  
[Скрин решения](https://dropmefiles.com/42r3S)  
Опасность в том, что если применить авто-одобрение, то эти изменения будут автоматически приняты и добавлены в сборку. Это может привести к неожиданному изменению кода и возможному нарушению работы приложения. Не стоит использовать в ответственных местах. Удобно использовать при обучении, локальном тестировании сборок.  
7. Задача 1.7  
[Скрин решения](https://dropmefiles.com/iqw70)  
Образ не удаляется потому что в конфигурационном файле задан аргумент: keep_locally – true, если его сменить на false, то при уничтожении ресурсов с помощью terraform образы тоже удалятся.
keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation.
