# Домашнее задание к занятию "8.4 Работа с Roles"

## Подготовка к выполнению
1. Создайте два пустых публичных репозитория в любом своём проекте: [vector-role](https://github.com/Ingvar78/vector-role.git) и [lighthouse-role](https://github.com/Ingvar78/lighthouse-role.git).
2. Добавьте публичную часть своего ключа к своему профилю в github.

## Основная часть

Наша основная цель - разбить наш playbook на отдельные roles. Задача: сделать roles для clickhouse, vector и lighthouse и написать playbook для использования этих ролей. Ожидаемый результат: существуют три ваших репозитория: два с roles и один с playbook.

1. Создать в старой версии playbook файл `requirements.yml` и заполнить его следующим содержимым:

   ```yaml
   ---
     - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
       scm: git
       version: "1.11.0"
       name: clickhouse 
   ```

2. При помощи `ansible-galaxy` скачать себе эту роль.

```bash
ansible-galaxy install -r requirements.yml -p roles
```
3. Создать новый каталог с ролью при помощи `ansible-galaxy role init vector-role`.
4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. 
5. Перенести нужные шаблоны конфигов в `templates`.
6. Описать в `README.md` обе роли и их параметры.
7. Повторите шаги 3-6 для lighthouse. Помните, что одна роль должна настраивать один продукт.

8. Выложите все roles в репозитории. Проставьте тэги, используя семантическую нумерацию Добавьте roles в `requirements.yml` в playbook.
9. Переработайте playbook на использование roles. Не забудьте про зависимости lighthouse и возможности совмещения `roles` с `tasks`.
10. Выложите playbook в репозиторий.
11. В ответ приведите ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.


---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
Структура проекта:

```
clickhouse-role/
├── group_vars			# Директория с переменными для группы, в данном данном случае общая для всех групп
│   └── all.yml			# 
├── inventory			# инвентори для stage и prod
│   ├── prod.yml
│   └── stage.yml
├── README.md			# Описание проекта, текущий файл
├── requirements.yml		# Файл с указанием используемых ролей
├── roles			# Директория с ролями
│   ├── clickhouse		# Роль [Clickhouse](git@github.com:AlexeySetevoi/ansible-clickhouse.git)
│   ├── lighthouse-role		# Роль [Lighthouse](https://github.com/Ingvar78/lighthouse-role.git)
│   ├── nginx-role		# Роль [Nginx](https://github.com/Ingvar78/nginx-role.git)
│   └── vector-role		# Роль [Vector](https://github.com/Ingvar78/vector-role.git)
├── site.yml			# Основной playbook
└── templates			# подготовленные темплейты для nginx и lighthouse
    ├── lighthouse.conf.j2
    └── nginx.conf.j2
```

### Порядок выполнения playbook

<details>
     <summary>Решение Задачи *</summary>
    <br>


```bash
iva@c9:~/Documents/08-04/clickhouse-role  (main *)$ ansible-galaxy install -r requirements.yml -p roles --force
Starting galaxy role install process
- extracting clickhouse to /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse
- clickhouse (1.11.0) was installed successfully
- extracting vector-role to /home/iva/Documents/08-04/clickhouse-role/roles/vector-role
- vector-role (1.0.2) was installed successfully
- extracting lighthouse-role to /home/iva/Documents/08-04/clickhouse-role/roles/lighthouse-role
- lighthouse-role (1.0.0) was installed successfully
- extracting nginx-role to /home/iva/Documents/08-04/clickhouse-role/roles/nginx-role
- nginx-role (1.0.5) was installed successfully
iva@c9:~/Documents/08-04/clickhouse-role  (main *)$ ansible-playbook -i inventory/stage.yml site.yml 

PLAY [Assert clickhouse role] ***********************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : Include OS Family Specific Variables] ********************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
included: /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse/tasks/precheck.yml for c8-clickhouse

TASK [clickhouse : Requirements check | Checking sse4_2 support] ************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : Requirements check | Not supported distribution && release] **********************************************************************************************************************
skipping: [c8-clickhouse]

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
included: /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse/tasks/params.yml for c8-clickhouse

TASK [clickhouse : Set clickhouse_service_enable] ***************************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : Set clickhouse_service_ensure] ***************************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
included: /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse/tasks/install/dnf.yml for c8-clickhouse

TASK [clickhouse : Install by YUM | Ensure clickhouse repo GPG key imported] ************************************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Install by YUM | Ensure clickhouse repo installed] *******************************************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Install by YUM | Ensure clickhouse package installed (latest)] *******************************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Install by YUM | Ensure clickhouse package installed (version latest)] ***********************************************************************************************************
skipping: [c8-clickhouse]

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
included: /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse/tasks/configure/sys.yml for c8-clickhouse

TASK [clickhouse : Check clickhouse config, data and logs] ******************************************************************************************************************************************
ok: [c8-clickhouse] => (item=/var/log/clickhouse-server)
changed: [c8-clickhouse] => (item=/etc/clickhouse-server)
changed: [c8-clickhouse] => (item=/var/lib/clickhouse/tmp/)
changed: [c8-clickhouse] => (item=/var/lib/clickhouse/)

TASK [clickhouse : Config | Create config.d folder] *************************************************************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Config | Create users.d folder] **************************************************************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Config | Generate system config] *************************************************************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Config | Generate users config] **************************************************************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Config | Generate remote_servers config] *****************************************************************************************************************************************
skipping: [c8-clickhouse]

TASK [clickhouse : Config | Generate macros config] *************************************************************************************************************************************************
skipping: [c8-clickhouse]

TASK [clickhouse : Config | Generate zookeeper servers config] **************************************************************************************************************************************
skipping: [c8-clickhouse]

TASK [clickhouse : Config | Fix interserver_http_port and intersever_https_port collision] **********************************************************************************************************
skipping: [c8-clickhouse]

TASK [clickhouse : Notify Handlers Now] *************************************************************************************************************************************************************

RUNNING HANDLER [clickhouse : Restart Clickhouse Service] *******************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
included: /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse/tasks/service.yml for c8-clickhouse

TASK [clickhouse : Ensure clickhouse-server.service is enabled: True and state: restarted] **********************************************************************************************************
changed: [c8-clickhouse]

TASK [clickhouse : Wait for Clickhouse Server to Become Ready] **************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
included: /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse/tasks/configure/db.yml for c8-clickhouse

TASK [clickhouse : Set ClickHose Connection String] *************************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : Gather list of existing databases] ***********************************************************************************************************************************************
ok: [c8-clickhouse]

TASK [clickhouse : Config | Delete database config] *************************************************************************************************************************************************

TASK [clickhouse : Config | Create database config] *************************************************************************************************************************************************

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
included: /home/iva/Documents/08-04/clickhouse-role/roles/clickhouse/tasks/configure/dict.yml for c8-clickhouse

TASK [clickhouse : Config | Generate dictionary config] *********************************************************************************************************************************************
skipping: [c8-clickhouse]

TASK [clickhouse : include_tasks] *******************************************************************************************************************************************************************
skipping: [c8-clickhouse]

TASK [Clickhouse | Create database] *****************************************************************************************************************************************************************
changed: [c8-clickhouse]

TASK [Clickhouse | Create log table] ****************************************************************************************************************************************************************
changed: [c8-clickhouse]

PLAY [Assert lighthouse role] ***********************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************
ok: [c8-lighthouse]

TASK [nginx-role : Nginx | Install eper-release] ****************************************************************************************************************************************************
changed: [c8-lighthouse]

TASK [nginx-role : Nginx | Install Nginx] ***********************************************************************************************************************************************************
changed: [c8-lighthouse]

TASK [nginx-role : Nginx | Create general config] ***************************************************************************************************************************************************
changed: [c8-lighthouse]

TASK [nginx-role : install git] *********************************************************************************************************************************************************************
changed: [c8-lighthouse]

TASK [lighthouse-role : LightHouse | Get from git repo] *********************************************************************************************************************************************
changed: [c8-lighthouse]

TASK [lighthouse-role : LightHouse | LightHouse nginx config] ***************************************************************************************************************************************
changed: [c8-lighthouse]

RUNNING HANDLER [nginx-role : Start nginx] **********************************************************************************************************************************************************
changed: [c8-lighthouse]

RUNNING HANDLER [nginx-role : Reload nginx] *********************************************************************************************************************************************************
changed: [c8-lighthouse]

RUNNING HANDLER [lighthouse-role : Reload Nginx] ****************************************************************************************************************************************************
changed: [c8-lighthouse]

PLAY [Assert vector role] ***************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************
ok: [c8-vector]

TASK [vector-role : Vector | Add clickhouse addresses to /etc/hosts] ********************************************************************************************************************************
changed: [c8-vector] => (item=c8-clickhouse)

TASK [vector-role : Vector | Get vector distrib] ****************************************************************************************************************************************************
changed: [c8-vector]

TASK [vector-role : Vector | Install vector rpm] ****************************************************************************************************************************************************
changed: [c8-vector]

TASK [vector-role : Add vector user to systemd-journal group] ***************************************************************************************************************************************
ok: [c8-vector]

TASK [vector-role : Vector | Update vector config] **************************************************************************************************************************************************
changed: [c8-vector]

TASK [vector-role : Vector | Create vector config] **************************************************************************************************************************************************
changed: [c8-vector]

TASK [vector-role : Replace host ip] ****************************************************************************************************************************************************************
changed: [c8-vector] => (item=c8-clickhouse)

RUNNING HANDLER [vector-role : Start vector service] ************************************************************************************************************************************************
changed: [c8-vector]

PLAY RECAP ******************************************************************************************************************************************************************************************
c8-clickhouse              : ok=27   changed=11   unreachable=0    failed=0    skipped=10   rescued=0    ignored=0   
c8-lighthouse              : ok=10   changed=9    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
c8-vector                  : ok=9    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

iva@c9:~/Documents/08-04/clickhouse-role  (main *)$ 

```

</detail>
---
