# Test 1.3: Verification of Log Messages

## Logging configuration template

```erlang
logging console disable
logging monitor disable
logging buffered 10000000
logging facility {{ LOGGING_FACILITY }}
logging {{ LOGGING_SERVER }} vrf {{ VRF_NAME }}
logging source-interface {{ LOGGING_SOURCE_INTERFACE }}
```

```erlang
show logging
show logging last 10
```

