# How to clear all licenses in ASR9k

## 1) Remove license_opid.puf from both RSP

    delete nvram:/license_opid.puf loc 0/RSP0/CPU0
    delete nvram:/license_opid.puf loc 0/RSP1/CPU0

## 2) Remove files

Remove license storage and licmgr temporary files from `disk0:` on both RSP

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

## 3) Restart processes

Restart processes licmgr and plat_license_udi_mgr

    process restart plat_license_udi_mgr
    process restart licmgr

## 4) Remove license config

Remove license config from admin configuration, clear operational log

    (admin)# clear license
    (admin)# clear license log operational
    (admin-config)# no license [...]

## 5) Check the result

Check the result and proceed with new licenses installation

    (admin)#show license udi

    Local Chassis UDI Information:
    PID : ASR-9010
    S/N : {{ SOME_SERIAL_NUMBER }}
    Operation ID: 0
