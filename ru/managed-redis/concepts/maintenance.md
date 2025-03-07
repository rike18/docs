# Техническое обслуживание в {{ mrd-name }}

Под техническим обслуживанием понимается:

* автоматическая установка обновлений и исправлений СУБД для хостов (в т. ч. для выключенных кластеров);
* изменение класса хостов и размера хранилища;
* другие сервисные работы.

Изменение мажорной версии СУБД не включено в техническое обслуживание. Подробнее о переходе между мажорными версиями см. в разделе [{#T}](../operations/cluster-version-update.md).

## Окно обслуживания {#maintenance-window}

Предпочтительное время технического обслуживания можно задать при [создании кластера](../operations/cluster-create.md) или [изменении его настроек](../operations/update.md):

* **Произвольное время** (по умолчанию) — разрешает проведение технического обслуживания в любое время.
* **По расписанию** — укажите предпочтительное время начала обслуживания: нужные день недели и час дня по UTC. Например, можно выбрать время, когда кластер наименее загружен.

## Порядок обслуживания {#maintenance-order}

Порядок технического обслуживания кластеров {{ mrd-name }} определяется количеством хостов и наличием [шардов](sharding.md):

* В нешардированных кластерах из одного хоста техническое обслуживание проходит хост-мастер. Поэтому если во время технического обслуживания потребуется перезагрузка мастера, такой кластер станет недоступным.
* Если нешардированный кластер состоит из нескольких хостов, техническое обслуживание проводится в следующем порядке:

    1. [Хосты-реплики](replication.md) последовательно проходят техническое обслуживание. Порядок хостов в очереди определяется случайным образом. Если во время технического обслуживания потребуется перезагрузка реплики, она станет недоступной на это время.
    1. Хост-мастер проходит техническое обслуживание. Если во время технического обслуживания потребуется перезагрузка мастера и он станет недоступным, его роль возьмет на себя одна из реплик.

* В шардированных кластерах техническое обслуживание проходит пошардово в порядке возрастания номера шарда. Технического обслуживания хостов происходит так же как и в нешардированных кластерах.
