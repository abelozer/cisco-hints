---
description: Page describes how to completely remove licenses from ASR 9000 box.
---

# ASR9k: How-to Remove Licences

## 1. Remove license\_opid.puf from both RSP

```text
delete nvram:/license_opid.puf loc 0/RSP0/CPU0
delete nvram:/license_opid.puf loc 0/RSP1/CPU0
```

## 2. Remove files

Remove license storage and licmgr temporary files from `disk0:` on both RSP

```bash
run attach 0/RSP0/CPU0
cd /disk0:
rm -R license
rm /tmp/chkpt_licmgr*
exit

run attach 0/RSP1/CPU0
cd /disk0:
rm -R license
rm /tmp/chkpt_licmgr*
exit
```

## 3. Restart processes

Restart processes licmgr and plat\_license\_udi\_mgr

```text
process restart plat_license_udi_mgr
process restart licmgr
```

## 4. Remove license config

Remove license config from admin configuration, clear operational log

```text
(admin)# clear license
(admin)# clear license log operational
(admin-config)# no license [...]
```

## 5. Check the result

Check the result and proceed with new licenses installation

```text
(admin)#show license udi

Local Chassis UDI Information:
PID : ASR-9010
S/N : {{ SOME_SERIAL_NUMBER }}
Operation ID: 0
```

