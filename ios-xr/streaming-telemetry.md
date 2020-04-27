# Streaming Telemetry

## Configuration Template

```erlang
telemetry model-driven
 destination-group {{ DG_NAME }}
  vrf {{ VRF_NAME }}
  address-family ipv4 {{ DST_IP }} port {{ DST_PORT ))
   encoding {{ TELEMETRY_ENCODING }}
   protocol {{ TELEMETRY_PROTOCOL }}
  !
 !
 sensor-group {{ SENSOR_NAME }}
  sensor-path {{ SENSOR_PATH }}
 !
 subscription {{ SUBSCRIPTION_NAME }}
  sensor-group-id {{ SENSOR_NAME }} sample-interval {{ SAMPLE_INTERVAL }}
  destination-id {{ DG_NAME }}
 !
!
```

## Getting the Right YANG Model

```bash
RP/0/RP0/CPU0:NCS540-02#schema-describe sho int te0/0/0/23
Mon Mar 30 09:31:48.974 UTC
Action: get
Path:   RootOper.Interfaces.Interface
```

The thing you do afterwords is not obvious:

```erlang
RP/0/RP0/CPU0:NCS540-02#run m2mcon
Mon Mar 30 09:31:54.388 UTC
Enter 'help' or '?' for help
m2mcon> get RootOper.Interfaces.Interface({'InterfaceName': 'TenGigE0/0/0/23'})
Result:
["RootOper.Interfaces.Interface({'InterfaceName': 'TenGigE0/0/0/23'})",
 {'ARPInformation': None,
  'Bandwidth': 10000000,
  'Bandwidth64Bit': 10000000,
  'BurnedInAddress': {'Address': '00:32:17:7d:c4:5c'},
  'CRCLength': None,
  'CarrierDelay': {'CarrierDelayDown': 0, 'CarrierDelayUp': 10},
  'DampeningInformation': None,
  'DataRates': {'Bandwidth': 10000000,
                'InputDataRate': 0,
                'InputLoad': 0,
                'InputPacketRate': 0,
                'LoadInterval': 9,
                'OutputDataRate': 0,
                'OutputLoad': 0,
                'OutputPacketRate': 0,
                'PeakInputDataRate': 0,
                'PeakInputPacketRate': 0,
                'PeakOutputDataRate': 0,
                'PeakOutputPacketRate': 0,
                'Reliability': 255},
  'Description': '"dd=QFX5100-02;di=xe-0/0/34;rem=QINQ 1-999"',
  'Duplexity': 'IM_ATTR_DUPLEX_FULL',
  'Encapsulation': 'ether',
  'EncapsulationInformation': None,
  'EncapsulationTypeString': 'ARPA',
  'FastShutdown': False,
  'HardwareTypeString': 'TenGigE',
  'IPInformation': None,
  'IfIndex': 43,
  'InFlowControl': 'IM_ATTR_FLOW_CONTROL_OFF',
  'InterfaceHandle': 'TenGigE0/0/0/23',
  'InterfaceStatistics': {'FullInterfaceStats': {'Applique': 0,
                                                 'AvailabilityFlag': 0,
                                                 'BroadcastPacketsReceived': 435,
                                                 'BroadcastPacketsSent': 4105,
                                                 'BytesReceived': 3117310,
                                                 'BytesSent': 4249789,
                                                 'CRCErrors': 0,
                                                 'CarrierTransitions': 1,
                                                 'FramingErrorsReceived': 0,
                                                 'GiantPacketsReceived': 0,
                                                 'InputAborts': 0,
                                                 'InputDrops': 0,
                                                 'InputErrors': 0,
                                                 'InputIgnoredPackets': 0,
                                                 'InputOverruns': 0,
                                                 'InputQueueDrops': 0,
                                                 'LastDataTime': 1585560830,
                                                 'LastDiscontinuityTime': 1585319990,
                                                 'MulticastPacketsReceived': 8997,
                                                 'MulticastPacketsSent': 17904,
                                                 'OutputBufferFailures': 0,
                                                 'OutputBuffersSwappedOut': 0,
                                                 'OutputDrops': 0,
                                                 'OutputErrors': 0,
                                                 'OutputQueueDrops': 0,
                                                 'OutputUnderruns': 0,
                                                 'PacketsReceived': 10431,
                                                 'PacketsSent': 27015,
                                                 'ParityPacketsReceived': 0,
                                                 'Resets': 0,
                                                 'RuntPacketsReceived': 0,
                                                 'SecondsSinceLastClearCounters': 0,
                                                 'SecondsSincePacketReceived': 0,
                                                 'SecondsSincePacketSent': 0,
                                                 'ThrottledPacketsReceived': 0,
                                                 'UnknownProtocolPacketsReceived': 0},
                          'StatsType': 'Full'},
  'InterfaceType': 'IFT_TENGETHERNET',
  'InterfaceTypeInformation': None,
  'IsDampeningEnabled': False,
  'IsDataInverted': None,
  'IsIntfLogical': False,
  'IsL2Looped': False,
  'IsL2TransportEnabled': False,
  'IsMaintenanceEnabled': None,
  'IsScrambleEnabled': None,
  'Keepalive': None,
  'L2InterfaceStatistics': None,
  'LastStateTransitionTime': 240893,
  'LineState': 'IM_STATE_UP',
  'LinkType': 'IM_ATTR_LINK_TYPE_FORCE',
  'LoopbackConfiguration': 'NoLoopback',
  'MACAddress': {'Address': '00:32:17:7d:c4:5c'},
  'MTU': 9200,
  'MaxBandwidth': 10000000,
  'MaxBandwidth64Bit': 10000000,
  'MediaType': 'IM_ATTR_MEDIA_10GBASE_SR',
  'OutFlowControl': 'IM_ATTR_FLOW_CONTROL_OFF',
  'ParentInterfaceName': None,
  'Speed': 10000000,
  'State': 'IM_STATE_UP',
  'StateTransitionCount': 1,
  'TransportMode': None}]
```

## Getting Sample Telemetry

Nobody knows it but you can get a sample telemetry output using CLI commands in IOS XR. Use `run mdt_exec` then specify a sensor path with `-s` key and define interval with `-c` key.

```text
RP/0/RP0/CPU0:ios# run mdt_exec -s Cisco-IOS-XR-ipv4-bgp-oper:bgp/instances/instance/instance-active/default-vrf/neighbors/neighbor -c 10000
```

```javascript
{
    "node_id_str": "iosxrv9000-1",
    "subscription_id_str": "app_TEST_200000001",
    "encoding_path": "Cisco-IOS-XR-ipv4-bgp-oper:bgp/instances/instance/instance-active/default-vrf/neighbors/neighbor",
    "collection_id": 1598,
    "collection_start_time": 1544519534493,
    "msg_timestamp": 1544519534504,
    "data_json": [{
        "timestamp": 1544519534501,
        "keys": {
            "instance-name": "default",
            "neighbor-address": "192.168.0.2"
        },
        "content": {
            "speaker-id": 0,
            "description": "iBGP peer iosxrv9000-2",
            "local-as": 1,
            "remote-as": 1,
            "has-internal-link": true,
            "is-external-neighbor-not-directly-connected": false,
            "messages-received": 1127,
            "messages-sent": 1127,
            "update-messages-in": 2,
            "update-messages-out": 2,
            "messages-queued-in": 0,
            "messages-queued-out": 0,
            "connection-established-time": 67405,
            "connection-state": "bgp-st-estab",
            "previous-connection-state": 3,
            "connection-admin-status": 0,
            "open-check-error-code": "none",
            "connection-local-address": {
                "afi": "ipv4",
                "ipv4-address": "192.168.0.1"
            },
            "is-local-address-configured": false,
            "connection-local-port": 55050,
            "connection-remote-address": {
                "afi": "ipv4",
                "ipv4-address": "192.168.0.2"
            },
            "connection-remote-port": 179,
            "neighbor-interface-handle": 0,
            "reset-notification-sent": false,
            "is-administratively-shut-down": false,
            "is-neighbor-max-prefix-shutdown": false,
            "is-out-of-memory-shutdown": false,
            "is-out-of-memory-forced-up": false,
            "is-ebgp-peer-as-league": false,
            "is-ebgp-peer-common-admin": false,
            "ttl-security-enabled": false,
            "suppress4-byte-as": false,
            "bfd-session-state": "bgp-bfd-state-none",
            "bfd-session-created-state": "bgp-bfd-state-none",
            "bfd-session-enable-mode": "bgp-bfd-enable-mode-disable",
            "bfd-minintervalval": 333,
            "bfd-multiplierval": 3,
            "bfd-state-ts": 1544452122865431850,
            "router-id": "192.168.0.2",
            "negotiated-protocol-version": 4,
            "ebgp-time-to-live": 1,
            "is-ebgp-multihop-bgpmpls-forwarding-disabled": false,
            "tcpmss": 0,
            "msg-log-in": 3,
            "msg-log-out": 3,
            "neighbor-local-as": 0,
            "local-as-no-prepend": false,
            "is-capability-negotiation-suppressed": false,
            "is-capability-negotiation-performed": true,
            "is-route-refresh-capability-received": true,
            "is-route-refresh-old-capability-received": true,
            "is-gr-aware": false,
            "is4-byte-as-capability-received": true,
            "is4-byte-as-capability-sent": true,
            "multi-protocol-capability-received": true,
            "hold-time": 180,
            "keep-alive-time": 60,
            "configured-hold-time": 180,
            "configured-keepalive": 60,
            "configured-min-acc-hold-time": 3,
            "min-advertise-interval": 0,
            "min-advertise-interval-msecs": 0,
            "min-origination-interval": 0,
            "connect-retry-interval": 30,
            "time-since-last-update": 67400,
            "time-since-last-read": 20,
            "time-since-last-read-reset": 0,
            "time-last-cb": 1544519513937719479,
            "time-last-cb-reset": 0,
            "time-last-fb": 0,
            "count-last-write": 2253,
            "time-since-last-write": 25,
            "attempted-last-write-bytes": 19,
            "actual-last-write-bytes": 19,
            "time-since-second-last-write": 85,
            "attempted-second-last-write-bytes": 19,
            "actual-second-last-write-bytes": 19,
            "time-since-last-write-reset": 0,
            "attempted-last-write-reset-bytes": 0,
            "actual-last-write-reset-bytes": 0,
            "time-since-second-last-write-reset": 0,
            "attempted-second-last-write-reset-bytes": 0,
            "actual-second-last-write-reset-bytes": 0,
            "last-write-event": 0,
            "second-last-write-event": 0,
            "last-k-aexpiry-reset": 0,
            "second-last-k-aexpiry-reset": 0,
            "last-k-anotsent-reset": 0,
            "last-k-aerror-reset": 0,
            "last-k-astart-reset": 0,
            "second-last-k-astart-reset": 0,
            "connection-up-count": 1,
            "connection-down-count": 0,
            "time-since-connection-last-dropped": 0,
            "reset-reason": "bgp-none",
            "peer-reset-reason": "bgp-peer-reset-reason-none",
            "peer-error-code": 0,
            "last-notify-error-code": 0,
            "last-notify-error-subcode": 0,
            "send-notification-info": {
                "time-since-last-notification": 0,
                "notification-error-code": 0,
                "notification-error-subcode": 0,
                "last-notification-data": []
            },
            "received-notification-info": {
                "time-since-last-notification": 0,
                "notification-error-code": 0,
                "notification-error-subcode": 0,
                "last-notification-data": []
            },
            "error-notifies-received": 0,
            "error-notifies-sent": 0,
            "remote-as-number": 1,
            "dmz-link-bandwidth": 0,
            "ebgp-recv-dmz": false,
            "ebgp-send-dmz-mode": "bgp-ebgp-send-dmz-disable",
            "tos-type": 0,
            "tos-value": 6,
            "performance-statistics": {
                "read-throttles": 0,
                "low-throttled-read": 0,
                "high-throttled-read": 0,
                "time-since-last-throttled-read": 20,
                "read-calls-count": 1126,
                "read-messages-count": 1127,
                "data-bytes-read": 21510,
                "io-read-time": 75,
                "write-calls-count": 1126,
                "data-bytes-written": 21510,
                "io-write-time": 921,
                "last-sent-seq-no": 21510,
                "write-subgroup-calls-count": 2250,
                "write-subgroup-messages-count": 2,
                "subgroup-list-time": 0,
                "write-queue-calls-count": 2257,
                "write-queue-messages-count": 2,
                "write-queue-time": 0,
                "inbound-update-messages": 2,
                "inbound-update-messages-time": 0,
                "maximum-read-size": 85,
                "actives": 1,
                "failed-post-actives": 0,
                "passives": 0,
                "rejected-passives": 0,
                "active-collision": 0,
                "passive-collision": 0,
                "control-to-read-thread-trigger": 0,
                "control-to-write-thread-trigger": 0,
                "network-status": 0,
                "reset-flags": 0,
                "nbr-flags": 1076953136,
                "nbr-fd": 488,
                "reset-retries": 0,
                "sync-flags": 0,
                "nsr-oper-down-count": 0,
                "last-nsr-scoped-sync": 0
            },
            "af-data": [{
                "value": {
                    "af-name": "ipv4",
                    "is-neighbor-route-reflector-client": false,
                    "is-legacy-pe-rt": false,
                    "is-neighbor-af-capable": true,
                    "is-soft-reconfiguration-inbound-allowed": false,
                    "is-use-soft-reconfiguration-always-on": false,
                    "remove-private-as-from-updates": false,
                    "remove-private-as-entire-aspath-from-updates": false,
                    "remove-private-as-from-inbound-updates": false,
                    "remove-private-as-entire-aspath-from-inbound-updates": false,
                    "flowspec-validation-d-isable": false,
                    "flowspec-redirect-validation-d-isable": false,
                    "orr-group-name": "",
                    "orr-group-index": 0,
                    "is-orr-root-address-configured": false,
                    "advertise-afi": false,
                    "advertise-afi-reorg": false,
                    "advertise-afi-disable": false,
                    "encapsulation-type": 0,
                    "advertise-rt-type": 0,
                    "advertise-afi-def-vrf-imp-disable": false,
                    "advertise-evp-nv4afi-def-vrf-imp-disable": false,
                    "advertise-evp-nv6afi-def-vrf-imp-disable": false,
                    "advertise-afi-vrf-re-imp-disable": false,
                    "advertise-evp-nv4afi-vrf-re-imp-disable": false,
                    "advertise-evp-nv6afi-vrf-re-imp-disable": false,
                    "advertise-afi-eo-r-ready": false,
                    "always-use-next-hop-local": false,
                    "sent-community-to-neighbor": false,
                    "sent-gshut-community-to-neighbor": false,
                    "sent-extended-community-to-neighbor": false,
                    "neighbor-default-originate": false,
                    "is-orf-sent": false,
                    "is-update-deferred": false,
                    "is-orf-send-scheduled": false,
                    "update-group-number": 2,
                    "filter-group-index": 1,
                    "is-update-throttled": false,
                    "is-update-leaving": false,
                    "vpn-update-gen-enabled": false,
                    "vpn-update-gen-trigger-enabled": false,
                    "is-addpath-send-operational": false,
                    "is-addpath-receive-operational": false,
                    "neighbor-version": 4,
                    "weight": 0,
                    "max-prefix-limit": 1048576,
                    "use-max-prefix-warning-only": false,
                    "max-prefix-discard-extra-paths": false,
                    "max-prefix-exceed-discard-paths": false,
                    "max-prefix-threshold-percent": 75,
                    "max-prefix-restart-time": 0,
                    "prefixes-accepted": 1,
                    "prefixes-synced": 0,
                    "prefixes-withdrawn-not-found": 0,
                    "prefixes-denied": 0,
                    "prefixes-denied-no-policy": 0,
                    "prefixes-denied-rt-permit": 0,
                    "prefixes-denied-orf-policy": 0,
                    "prefixes-denied-policy": 0,
                    "number-of-bestpaths": 1,
                    "number-of-best-externalpaths": 0,
                    "prefixes-advertised": 1,
                    "prefixes-be-advertised": 0,
                    "prefixes-suppressed": 0,
                    "prefixes-withdrawn": 0,
                    "is-peer-orf-capable": false,
                    "is-advertised-orf-send": false,
                    "is-received-orf-send-capable": false,
                    "is-advertised-orf-receive": false,
                    "is-received-orf-receive-capable": false,
                    "is-advertised-graceful-restart": false,
                    "is-graceful-restart-state-flag": false,
                    "is-received-graceful-restart-capable": false,
                    "is-add-path-send-capability-advertised": false,
                    "is-add-path-send-capability-received": false,
                    "is-add-path-receive-capability-advertised": false,
                    "is-add-path-receive-capability-received": false,
                    "is-ext-nh-encoding-capability-received": true,
                    "is-ext-nh-encoding-capability-sent": true,
                    "restart-time": 0,
                    "local-restart-time": 120,
                    "stale-path-timeout": 360,
                    "rib-purge-timeout-value": 600,
                    "neighbor-preserved-forwarding-state": false,
                    "long-lived-graceful-restart-stale-time-configured": false,
                    "long-lived-graceful-restart-stale-time-sent": 0,
                    "long-lived-graceful-restart-stale-time-accept": 0,
                    "long-lived-graceful-restart-capability-received": false,
                    "long-lived-graceful-restart-stale-time-received": 0,
                    "neighbor-preserved-long-lived-forwarding-state": false,
                    "neighbor-long-lived-graceful-restart-capable": false,
                    "neighbor-long-lived-graceful-restart-time-remaining": 0,
                    "route-refreshes-received": 0,
                    "route-refreshes-sent": 0,
                    "refresh-target-version": 0,
                    "refresh-version": 0,
                    "refresh-acked-version": 0,
                    "is-prefix-orf-present": false,
                    "orf-entries-received": 0,
                    "is-default-originate-sent": false,
                    "route-policy-prefix-orf": "",
                    "route-policy-in": "",
                    "route-policy-out": "",
                    "route-policy-default-originate": "",
                    "is-neighbor-ebgp-without-inbound-policy": false,
                    "is-neighbor-ebgp-without-outbound-policy": false,
                    "is-as-override-set": false,
                    "is-allow-as-in-set": false,
                    "allow-as-in-count": 0,
                    "address-family-long-lived-time": 0,
                    "eo-r-received-in-read-only": true,
                    "acked-version": 4,
                    "synced-acked-version": 0,
                    "outstanding-version": 0,
                    "outstanding-refresh-version": 0,
                    "outstanding-version-max": 1,
                    "neighbor-af-performance-statistics": {
                        "sub-group-pending-message-count": 0,
                        "processed-messages": 2,
                        "sent-messages": 2,
                        "split-horizon-update-transmit": 0,
                        "split-horizon-update-blocked": 0,
                        "split-horizon-withdraw-transmit": 0,
                        "split-horizon-withdraw-blocked": 0
                    },
                    "is-aigp-set": true,
                    "is-rt-present": false,
                    "extended-community": [],
                    "is-rt-present-standby": false,
                    "extended-community-standby": [],
                    "accept-own-enabled": false,
                    "selective-multipath-eligible": false,
                    "afrpki-disable": false,
                    "afrpki-use-validity": false,
                    "afrpki-allow-invalid": false,
                    "afrpki-signal-ibgp": false,
                    "is-advertise-permanent-network": false,
                    "is-send-mcast-attr": true,
                    "import-stitching": false,
                    "import-reoriginate": false,
                    "import-reoriginate-stitching": false,
                    "advertise-v4-flags": 0,
                    "advertise-v6-flags": 0,
                    "advertise-local-labeled-route-unicast": true,
                    "prefixes-denied-non-cumulative": 0,
                    "enable-label-stack": false
                }
            }, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}],
            "tcp-session-open-mode": "bgp-tcp-mode-type-either",
            "vrf-name": "default",
            "standby-rp": false,
            "nsr-enabled": true,
            "graceful-restart-enabled-nbr": false,
            "gr-restart-time": 120,
            "gr-stale-path-time": 360,
            "fssn-offset": 0,
            "fpbsn-offset": 0,
            "last-ackd-seq-no": 21491,
            "bytes-written": 211,
            "bytes-read": 21510,
            "socket-read-bytes": 21510,
            "is-read-disabled": false,
            "update-bytes-read": 85,
            "nsr-state": "bgp-nbr-nsr-st-none",
            "is-passive-close": true,
            "nbr-enforce-first-as": true,
            "active-bmp-servers": 0,
            "nbr-cluster-id": 0,
            "nbr-in-cluster": 0,
            "ignore-connected": false,
            "internal-vpn-client": false,
            "io-armed": false,
            "read-armed": true,
            "write-armed": true,
            "message-statistics": {
                "open": {
                    "tx": {
                        "count": 1,
                        "last-time-spec": {
                            "seconds": 1544452126,
                            "nanoseconds": 531684284
                        }
                    },
                    "rx": {
                        "count": 1,
                        "last-time-spec": {
                            "seconds": 1544452128,
                            "nanoseconds": 541118865
                        }
                    }
                },
                "notification": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                },
                "update": {
                    "tx": {
                        "count": 2,
                        "last-time-spec": {
                            "seconds": 1544452248,
                            "nanoseconds": 565159651
                        }
                    },
                    "rx": {
                        "count": 2,
                        "last-time-spec": {
                            "seconds": 1544452133,
                            "nanoseconds": 547431435
                        }
                    }
                },
                "keepalive": {
                    "tx": {
                        "count": 1124,
                        "last-time-spec": {
                            "seconds": 1544519508,
                            "nanoseconds": 898479932
                        }
                    },
                    "rx": {
                        "count": 1124,
                        "last-time-spec": {
                            "seconds": 1544519513,
                            "nanoseconds": 937602174
                        }
                    }
                },
                "route-refresh": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                },
                "total": {
                    "tx": {
                        "count": 1127,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 1127,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                }
            },
            "discard-data-bytes": 0,
            "local-as-replace-as": false,
            "local-as-dual-as": false,
            "local-as-dual-as-mode-native": false,
            "egress-peer-engineering-enabled": false,
            "tcp-init-sync-time-spec": {
                "seconds": 0,
                "nanoseconds": 0
            },
            "tcp-init-sync-phase-two-time-spec": {
                "seconds": 0,
                "nanoseconds": 0
            },
            "tcp-init-sync-done-time-spec": {
                "seconds": 0,
                "nanoseconds": 0
            },
            "discard-as4-path": 0,
            "rpki-disable": false,
            "rpki-use-validity": false,
            "rpki-allow-invalid": false,
            "rpki-signal-ibgp": false,
            "graceful-maintenance": {
                "gshut-exists": false,
                "gshut-local-active": false,
                "gshut-active": false,
                "gshut-locpref-set": false,
                "gshut-locpref": 0,
                "gshut-prepends": 0
            },
            "dynamic-neighbor": false
        }
    }, {
        "timestamp": 1544519534501,
        "keys": {
            "instance-name": "default",
            "neighbor-address": "192.168.0.3"
        },
        "content": {
            "speaker-id": 0,
            "description": "",
            "local-as": 1,
            "remote-as": 1,
            "has-internal-link": true,
            "is-external-neighbor-not-directly-connected": false,
            "messages-received": 0,
            "messages-sent": 0,
            "update-messages-in": 0,
            "update-messages-out": 0,
            "messages-queued-in": 0,
            "messages-queued-out": 0,
            "connection-established-time": 0,
            "connection-state": "bgp-st-idle",
            "previous-connection-state": 1,
            "connection-admin-status": 0,
            "open-check-error-code": "no-best-local-address",
            "connection-local-address": {
                "afi": "ipv4",
                "ipv4-address": "0.0.0.0"
            },
            "is-local-address-configured": false,
            "connection-local-port": 0,
            "connection-remote-address": {
                "afi": "ipv4",
                "ipv4-address": "192.168.0.3"
            },
            "connection-remote-port": 0,
            "neighbor-interface-handle": 0,
            "reset-notification-sent": false,
            "is-administratively-shut-down": false,
            "is-neighbor-max-prefix-shutdown": false,
            "is-out-of-memory-shutdown": false,
            "is-out-of-memory-forced-up": false,
            "is-ebgp-peer-as-league": false,
            "is-ebgp-peer-common-admin": false,
            "ttl-security-enabled": false,
            "suppress4-byte-as": false,
            "bfd-session-state": "bgp-bfd-state-none",
            "bfd-session-created-state": "bgp-bfd-state-none",
            "bfd-session-enable-mode": "bgp-bfd-enable-mode-disable",
            "bfd-minintervalval": 333,
            "bfd-multiplierval": 3,
            "bfd-state-ts": 1544452122866101275,
            "router-id": "0.0.0.0",
            "negotiated-protocol-version": 4,
            "ebgp-time-to-live": 1,
            "is-ebgp-multihop-bgpmpls-forwarding-disabled": false,
            "tcpmss": 0,
            "msg-log-in": 3,
            "msg-log-out": 3,
            "neighbor-local-as": 0,
            "local-as-no-prepend": false,
            "is-capability-negotiation-suppressed": false,
            "is-capability-negotiation-performed": false,
            "is-route-refresh-capability-received": false,
            "is-route-refresh-old-capability-received": false,
            "is-gr-aware": false,
            "is4-byte-as-capability-received": false,
            "is4-byte-as-capability-sent": false,
            "multi-protocol-capability-received": false,
            "hold-time": 180,
            "keep-alive-time": 60,
            "configured-hold-time": 180,
            "configured-keepalive": 60,
            "configured-min-acc-hold-time": 3,
            "min-advertise-interval": 0,
            "min-advertise-interval-msecs": 0,
            "min-origination-interval": 0,
            "connect-retry-interval": 30,
            "time-since-last-update": 0,
            "time-since-last-read": 0,
            "time-since-last-read-reset": 0,
            "time-last-cb": 0,
            "time-last-cb-reset": 0,
            "time-last-fb": 0,
            "count-last-write": 0,
            "time-since-last-write": 0,
            "attempted-last-write-bytes": 0,
            "actual-last-write-bytes": 0,
            "time-since-second-last-write": 0,
            "attempted-second-last-write-bytes": 0,
            "actual-second-last-write-bytes": 0,
            "time-since-last-write-reset": 0,
            "attempted-last-write-reset-bytes": 0,
            "actual-last-write-reset-bytes": 0,
            "time-since-second-last-write-reset": 0,
            "attempted-second-last-write-reset-bytes": 0,
            "actual-second-last-write-reset-bytes": 0,
            "last-write-event": 0,
            "second-last-write-event": 0,
            "last-k-aexpiry-reset": 0,
            "second-last-k-aexpiry-reset": 0,
            "last-k-anotsent-reset": 0,
            "last-k-aerror-reset": 0,
            "last-k-astart-reset": 0,
            "second-last-k-astart-reset": 0,
            "connection-up-count": 0,
            "connection-down-count": 0,
            "time-since-connection-last-dropped": 0,
            "reset-reason": "bgp-none",
            "peer-reset-reason": "bgp-peer-reset-reason-none",
            "peer-error-code": 0,
            "last-notify-error-code": 0,
            "last-notify-error-subcode": 0,
            "send-notification-info": {
                "time-since-last-notification": 0,
                "notification-error-code": 0,
                "notification-error-subcode": 0,
                "last-notification-data": []
            },
            "received-notification-info": {
                "time-since-last-notification": 0,
                "notification-error-code": 0,
                "notification-error-subcode": 0,
                "last-notification-data": []
            },
            "error-notifies-received": 0,
            "error-notifies-sent": 0,
            "remote-as-number": 1,
            "dmz-link-bandwidth": 0,
            "ebgp-recv-dmz": false,
            "ebgp-send-dmz-mode": "bgp-ebgp-send-dmz-disable",
            "tos-type": 0,
            "tos-value": 6,
            "performance-statistics": {
                "read-throttles": 0,
                "low-throttled-read": 0,
                "high-throttled-read": 0,
                "time-since-last-throttled-read": 0,
                "read-calls-count": 0,
                "read-messages-count": 0,
                "data-bytes-read": 0,
                "io-read-time": 0,
                "write-calls-count": 0,
                "data-bytes-written": 0,
                "io-write-time": 0,
                "last-sent-seq-no": 0,
                "write-subgroup-calls-count": 0,
                "write-subgroup-messages-count": 0,
                "subgroup-list-time": 0,
                "write-queue-calls-count": 0,
                "write-queue-messages-count": 0,
                "write-queue-time": 0,
                "inbound-update-messages": 0,
                "inbound-update-messages-time": 0,
                "maximum-read-size": 0,
                "actives": 0,
                "failed-post-actives": 0,
                "passives": 0,
                "rejected-passives": 0,
                "active-collision": 0,
                "passive-collision": 0,
                "control-to-read-thread-trigger": 0,
                "control-to-write-thread-trigger": 0,
                "network-status": 0,
                "reset-flags": 0,
                "nbr-flags": 0,
                "nbr-fd": -1,
                "reset-retries": 0,
                "sync-flags": 0,
                "nsr-oper-down-count": 0,
                "last-nsr-scoped-sync": 0
            },
            "af-data": [{
                "value": {
                    "af-name": "ipv4",
                    "is-neighbor-route-reflector-client": false,
                    "is-legacy-pe-rt": false,
                    "is-neighbor-af-capable": false,
                    "is-soft-reconfiguration-inbound-allowed": false,
                    "is-use-soft-reconfiguration-always-on": false,
                    "remove-private-as-from-updates": false,
                    "remove-private-as-entire-aspath-from-updates": false,
                    "remove-private-as-from-inbound-updates": false,
                    "remove-private-as-entire-aspath-from-inbound-updates": false,
                    "flowspec-validation-d-isable": false,
                    "flowspec-redirect-validation-d-isable": false,
                    "orr-group-name": "",
                    "orr-group-index": 0,
                    "is-orr-root-address-configured": false,
                    "advertise-afi": false,
                    "advertise-afi-reorg": false,
                    "advertise-afi-disable": false,
                    "encapsulation-type": 0,
                    "advertise-rt-type": 0,
                    "advertise-afi-def-vrf-imp-disable": false,
                    "advertise-evp-nv4afi-def-vrf-imp-disable": false,
                    "advertise-evp-nv6afi-def-vrf-imp-disable": false,
                    "advertise-afi-vrf-re-imp-disable": false,
                    "advertise-evp-nv4afi-vrf-re-imp-disable": false,
                    "advertise-evp-nv6afi-vrf-re-imp-disable": false,
                    "advertise-afi-eo-r-ready": false,
                    "always-use-next-hop-local": false,
                    "sent-community-to-neighbor": false,
                    "sent-gshut-community-to-neighbor": false,
                    "sent-extended-community-to-neighbor": false,
                    "neighbor-default-originate": false,
                    "is-orf-sent": false,
                    "is-update-deferred": false,
                    "is-orf-send-scheduled": false,
                    "update-group-number": 1,
                    "filter-group-index": 0,
                    "is-update-throttled": false,
                    "is-update-leaving": false,
                    "vpn-update-gen-enabled": false,
                    "vpn-update-gen-trigger-enabled": false,
                    "is-addpath-send-operational": false,
                    "is-addpath-receive-operational": false,
                    "neighbor-version": 0,
                    "weight": 0,
                    "max-prefix-limit": 1048576,
                    "use-max-prefix-warning-only": false,
                    "max-prefix-discard-extra-paths": false,
                    "max-prefix-exceed-discard-paths": false,
                    "max-prefix-threshold-percent": 75,
                    "max-prefix-restart-time": 0,
                    "prefixes-accepted": 0,
                    "prefixes-synced": 0,
                    "prefixes-withdrawn-not-found": 0,
                    "prefixes-denied": 0,
                    "prefixes-denied-no-policy": 0,
                    "prefixes-denied-rt-permit": 0,
                    "prefixes-denied-orf-policy": 0,
                    "prefixes-denied-policy": 0,
                    "number-of-bestpaths": 0,
                    "number-of-best-externalpaths": 0,
                    "prefixes-advertised": 0,
                    "prefixes-be-advertised": 0,
                    "prefixes-suppressed": 0,
                    "prefixes-withdrawn": 0,
                    "is-peer-orf-capable": false,
                    "is-advertised-orf-send": false,
                    "is-received-orf-send-capable": false,
                    "is-advertised-orf-receive": false,
                    "is-received-orf-receive-capable": false,
                    "is-advertised-graceful-restart": false,
                    "is-graceful-restart-state-flag": false,
                    "is-received-graceful-restart-capable": false,
                    "is-add-path-send-capability-advertised": false,
                    "is-add-path-send-capability-received": false,
                    "is-add-path-receive-capability-advertised": false,
                    "is-add-path-receive-capability-received": false,
                    "is-ext-nh-encoding-capability-received": false,
                    "is-ext-nh-encoding-capability-sent": false,
                    "restart-time": 0,
                    "local-restart-time": 120,
                    "stale-path-timeout": 360,
                    "rib-purge-timeout-value": 600,
                    "neighbor-preserved-forwarding-state": false,
                    "long-lived-graceful-restart-stale-time-configured": false,
                    "long-lived-graceful-restart-stale-time-sent": 0,
                    "long-lived-graceful-restart-stale-time-accept": 0,
                    "long-lived-graceful-restart-capability-received": false,
                    "long-lived-graceful-restart-stale-time-received": 0,
                    "neighbor-preserved-long-lived-forwarding-state": false,
                    "neighbor-long-lived-graceful-restart-capable": false,
                    "neighbor-long-lived-graceful-restart-time-remaining": 0,
                    "route-refreshes-received": 0,
                    "route-refreshes-sent": 0,
                    "refresh-target-version": 0,
                    "refresh-version": 0,
                    "refresh-acked-version": 0,
                    "is-prefix-orf-present": false,
                    "orf-entries-received": 0,
                    "is-default-originate-sent": false,
                    "route-policy-prefix-orf": "",
                    "route-policy-in": "",
                    "route-policy-out": "",
                    "route-policy-default-originate": "",
                    "is-neighbor-ebgp-without-inbound-policy": false,
                    "is-neighbor-ebgp-without-outbound-policy": false,
                    "is-as-override-set": false,
                    "is-allow-as-in-set": false,
                    "allow-as-in-count": 0,
                    "address-family-long-lived-time": 0,
                    "eo-r-received-in-read-only": false,
                    "acked-version": 1,
                    "synced-acked-version": 0,
                    "outstanding-version": 0,
                    "outstanding-refresh-version": 0,
                    "outstanding-version-max": 0,
                    "neighbor-af-performance-statistics": {
                        "sub-group-pending-message-count": 0,
                        "processed-messages": 0,
                        "sent-messages": 0,
                        "split-horizon-update-transmit": 0,
                        "split-horizon-update-blocked": 0,
                        "split-horizon-withdraw-transmit": 0,
                        "split-horizon-withdraw-blocked": 0
                    },
                    "is-aigp-set": true,
                    "is-rt-present": false,
                    "extended-community": [],
                    "is-rt-present-standby": false,
                    "extended-community-standby": [],
                    "accept-own-enabled": false,
                    "selective-multipath-eligible": false,
                    "afrpki-disable": false,
                    "afrpki-use-validity": false,
                    "afrpki-allow-invalid": false,
                    "afrpki-signal-ibgp": false,
                    "is-advertise-permanent-network": false,
                    "is-send-mcast-attr": true,
                    "import-stitching": false,
                    "import-reoriginate": false,
                    "import-reoriginate-stitching": false,
                    "advertise-v4-flags": 0,
                    "advertise-v6-flags": 0,
                    "advertise-local-labeled-route-unicast": true,
                    "prefixes-denied-non-cumulative": 0,
                    "enable-label-stack": false
                }
            }, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}],
            "tcp-session-open-mode": "bgp-tcp-mode-type-either",
            "vrf-name": "default",
            "standby-rp": false,
            "nsr-enabled": true,
            "graceful-restart-enabled-nbr": false,
            "gr-restart-time": 120,
            "gr-stale-path-time": 360,
            "fssn-offset": 0,
            "fpbsn-offset": 0,
            "last-ackd-seq-no": 0,
            "bytes-written": 0,
            "bytes-read": 0,
            "socket-read-bytes": 0,
            "is-read-disabled": false,
            "update-bytes-read": 0,
            "nsr-state": "bgp-nbr-nsr-st-none",
            "is-passive-close": false,
            "nbr-enforce-first-as": true,
            "active-bmp-servers": 0,
            "nbr-cluster-id": 0,
            "nbr-in-cluster": 0,
            "ignore-connected": false,
            "internal-vpn-client": false,
            "io-armed": false,
            "read-armed": false,
            "write-armed": false,
            "message-statistics": {
                "open": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                },
                "notification": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                },
                "update": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                },
                "keepalive": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                },
                "route-refresh": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                },
                "total": {
                    "tx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    },
                    "rx": {
                        "count": 0,
                        "last-time-spec": {
                            "seconds": 0,
                            "nanoseconds": 0
                        }
                    }
                }
            },
            "discard-data-bytes": 0,
            "local-as-replace-as": false,
            "local-as-dual-as": false,
            "local-as-dual-as-mode-native": false,
            "egress-peer-engineering-enabled": false,
            "tcp-init-sync-time-spec": {
                "seconds": 0,
                "nanoseconds": 0
            },
            "tcp-init-sync-phase-two-time-spec": {
                "seconds": 0,
                "nanoseconds": 0
            },
            "tcp-init-sync-done-time-spec": {
                "seconds": 0,
                "nanoseconds": 0
            },
            "discard-as4-path": 0,
            "rpki-disable": false,
            "rpki-use-validity": false,
            "rpki-allow-invalid": false,
            "rpki-signal-ibgp": false,
            "graceful-maintenance": {
                "gshut-exists": false,
                "gshut-local-active": false,
                "gshut-active": false,
                "gshut-locpref-set": false,
                "gshut-locpref": 0,
                "gshut-prepends": 0
            },
            "dynamic-neighbor": false
        }
    }],
    "collection_end_time": 1544519534504
}
```

Another example:

```erlang
RP/0/RP0/CPU0:NCS540-02#run mdt_exec -s Cisco-IOS-XR-infra-statsd-oper:infra-statistics/interfaces/interface/latest/generic-counters -c 10000
Mon Mar 30 13:26:38.811 UTC
Enter any key to exit...
 Sub_id 200000001, flag 0, len 0
 Sub_id 200000001, flag 4, len 43268
--------
<snip>
```

## metrics.json

Corresponding metrics.json

```javascript
[
    {
        "basepath": "Cisco-IOS-XR-ipv4-bgp-oper:bgp/instances/instance/instance-active/default-vrf/neighbors/neighbor",
        "spec": {
            "fields": [
                       {"name": "neighbor-address", "tag": true},
                       {"name": "connection-state"},
                       {"name": "remote-as"},
                       {"name": "af-data",
                        "fields": [
                            {"name": "value",
                             "fields": [{"name": "af-name", "tag": true},
                                        {"name": "prefixes-accepted"},
                                        {"name": "prefixes-denied"},
                                        {"name": "prefixes-advertised"}
                                       ]
                            }
                        ]
                       }
            ]
        }
    }
]
```

## Telemetry Software Stack

### Docker Network

```bash
docker network create influxdb
```

### InfluxDB

```bash
docker run -it -d --name=influxdb \
      --net=influxdb \
      -p 8083:8083 \
      -p 8086:8086 \
      influxdb
```

### **InfluxDB Logs**

```bash
docker logs -f influxdb
```

```bash
docker exec -it influxdb influx
```

### Telegraf

```bash
docker run -d --name=telegraf \
      --net=influxdb \
      -p 57000:57000 \
      -v ~/telegraf.conf:/etc/telegraf/telegraf.conf:ro \
      telegraf
```

```erlang
# telegraf.conf
# Global Agent Configuration
[agent]
hostname = "telemetry-container"
flush_interval = "15s"
interval = "15s"

# gRPC Dial-Out Telemetry Listener
[[inputs.cisco_telemetry_mdt]]
transport = "grpc"
service_address = ":57000"

# Output Plugin InfluxDB
[[outputs.influxdb]]
database = "telegraf"
urls = [ "http://influxdb:8086" ]
username = "telegraf"
password = "telegraf"
```

```bash
docker logs -f telegraf
```

### Grafana

```bash
docker run -d --name=grafana \
      --net=influxdb \
      -p 3000:3000 \
      grafana/grafana
```

