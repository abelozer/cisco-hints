# Обновление IOS XR 4.3.4 до версии 5.3.4

## Подготовительные работы

Подготовительные работы проводятся без перерыва сервиса на следующих устройствах:

- ASR-CORE-1, 10.40.10.105
- ASR-CORE-2, 10.40.10.109

Необходимо заранее скачать и поместить на FTP сервер, доступный с каждого из обновляемых устройств, следующие пакеты:

- asr9k-k9sec-px-5.3.4
- asr9k-mcast-px-5.3.4
- asr9k-mgbl-px-5.3.4
- asr9k-mini-px-5.3.4
- asr9k-mpls-px-5.3.4
- asr9k-fpd-px-5.3.4
- asr9k-px-5.3.4.sp1.tar

### Проверка config-register 0x2102

На обоих устройствах установлено правильное значение config-register.

### Проверка необходимых SMU

- CSCut30136
- CSCul58246

На обоих устройствах установлены необходимые SMU.

### Проверка fpd auto-upgrade

```cisco
admin show running-config
```

На обоих устройствах автоматическое обновление fpd включено.

### Удаление неактивных пакетов

```cisco
admin# install remove inactive
```

### Добавление новых пакетов

```cisco
admin install add source ftp://address/path asr9k-k9sec-px-5.3.4 asr9k-mcast-px-5.3.4 asr9k-mgbl-px-5.3.4 asr9k-mini-px-5.3.4 asr9k-mpls-px-5.3.4 asr9k-fpd-px-4.3.4 asr9k-px-5.3.4.sp1.tar
```

Перед добавление новых пакетов необходимо убедиться в том, что на disk0 достаточно свободного места. Если места недостаточно, потребуется удаление пакетов. Прежде всего удаляется пакет fpd.

```cisco
admin# install deactivate disk0:asr9k-fpd-px-4.3.4
admin# install commit
admin# install remove disk0:asr9k-fpd-px-4.3.4
```

> **Note** IOS XR 4.3.4 поддерживает установку с disk1, но дефект [CSCva62399](https://bst.cloudapps.cisco.com/bugsearch/bug/CSCva62399) не позволяет ее использовать.

### Проверяем добавленные пакеты

```cisco
admin# show install inactive
```

## Активная фаза работ

> **Warning** Во время активной фазы работ происходит активация IOS XR версии 5.3.4 с перезагрузкой всего устройства.

### Активация добавленных пакетов

```cisco
admin# install activate disk0:asr9k-*-5.3.4 disk0:asr9k-px-5.3.4.sp1-1.0.0
```

### Подтверждение установки

```cisco
admin# install commit
```
