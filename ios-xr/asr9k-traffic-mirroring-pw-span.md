# Настройка traffic mirroring на ASR 9000

## Настройка local site

Настройка узла, где находится источник..
### 1. Настраиваем monitor-session

```
monitor-session {{ MONITOR_SESSION_NAME }}
 destination pseudowire
```

В качестве destination используется ключевое слово pseudowire.  Далее эта сессия применяется к attachment circuit и специальному PW для зеркалируемого трафика.


### 2. Настройка PW для зеркалируемого трафика

Настраивается специальный PW, в который будет отправляться зеркалируемый трафик. В этом xconnect p2p нет attachment circuit.

```
l2vpn
 xconnect group {{ XC_GROUP_NAME }}
  p2p {{ P2P-NAME }}
   monitor-session {{ MONITOR_SESSION_NAME }}
   neighbor ipv4 {{ NEIGHBOR_ADDRESS }} pw-id {{ PW_ID }}
```

### 3. Настройка monitor-session на attachment circuit

Команда добавляется на интерфейс, куда подключается сервис, который необходимо зеркалировать.

```
interface {{ UNI_IFACE }} l2transport
 monitor-session {{ MONITOR_SESSION_NAME }} [direction {rx-only | tx-only}]
```

Опционально указывается направление трафика. По умолчанию зеркалируются RX и TX.

## Настройка remote site

На удаленной стороне настраивается обычный PW.

## Проверка

- show monitor-session [ session-name ] status
- show monitor-session [ session-name ] status detail
- show monitor-session [ session-name ] status error
- show monitor-session counters
