# Test 8.5: Syslog functionality

```erlang
logging console disable
logging monitor disable
logging buffered 10000000
logging facility {{ SYSLOG_FACILITY }}
logging {{ SYSLOG_SERVER }} vrf {{ VRF_NAME }}
logging source-interface {{ SOURCE_INTERFACE }}
```

