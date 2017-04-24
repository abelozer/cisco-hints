# Low disk0 space during upgrade

If you are running low space on disk0 of RSP you will not be able to upgrade system. There are  at least four options to resolve this.

## Remove inactive packages

First of all you don't need inactive packages.


```cisco
admin# show install inactive
admin# install remove inactive
```

## Remove FPD pie

System needs FPD pie only during first line card boot with new version to upgrace microcode. Later this package only consumes space.

```cisco
admin# install deactivate disk0:asr9k-fpd-px-4.3.4
admin# install commit
admin# install remove disk0:asr9k-fpd-px-4.3.4
```

## Flexible disk installs

Starting 4.3.4 IOS XR supports [flexible disk installs](http://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r4-3/system_management/configuration/guide/b_sysman_cg43asr9k/b_sysman_cg43asr9k_chapter_0100.html#concept_4AD30BCA80FD484DAFCE08362851CC0D). It meens you can use disk1 as boot media. I would not recommend this approach on releases lower than 5.1.3 because of bug [CSCva62399](https://bst.cloudapps.cisco.com/bugsearch/bug/CSCva62399). System will still count free space on disk0 by mistake. On 5.1.3 use SMU or Service Pack 10.

```cisco
RP/0/RSP0/CPU0:asr9k#Error:    The install operation failed due to a system error.
Error:
Error:    ERROR: Node responded with an error when completing the check of disk space. (Details: 'Install Helper' detected the 'fatal' condition 'stat() failed': No such file or directory)
Error:    AFFECTED NODE(S): 0/RSP0/CPU0
Error:    TECH SUPPORT:
Error:     Please collect diagnostic information using '(admin) show tech-support install'
Install operation 9 failed at 15:33:40 MSK Sun Apr 16 2017.
```

## Turboboot

Option of last resort is [turboboot](https://supportforums.cisco.com/document/123576/asr9000xr-understanding-turboboot-and-initial-system-bring).
