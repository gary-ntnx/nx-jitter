# AOS 7.5 Storage Jitter
Experiment is to read from stargate cache (to avoid NVME device jitter) and measure performance jitter.  In the end it was more useful to run over a long period (e.g. 1 Hour) so that we can monitor and identify periodic jitter.  Some of the periodic jitter is at a frequency of 10's of seconds so running the test for similar lengths of time reports as veriability rather than periodic jitter.

##### AOS 7.5 Default
![7.5 Defaults](https://github.com/gary-ntnx/nx-jitter/blob/994b877a79c3aa2ad2a2ecec4987eeae2ec82cbc/iops/7.5-defaults-IOPS.svg)
##### AOS 7.5 Some Services disabled
Disabled via genesis
```
genesis stop sys_stat_collector curator insights_server alert_manager xtrim insights_data_transfer lazan cluster_health
```
![7.5 Services Disabled](https://github.com/gary-ntnx/nx-jitter/blob/91bcbd9f20bf7e80add06348c40a3f6b4530baa4/iops/7.5-defaults-some-services-stopped-IOPS.svg)
##### AOS 7.5 stargate_alarm_handle=0
Additionally I set `stargate_alarm_handle=0` and restarted stagate
![AOS 7.5 stargate alarm handler 0](https://github.com/gary-ntnx/nx-jitter/blob/962f2f39a647f6f3569ffdd0ec90bc242acfe586/iops/7.5-defaults-some-services-stopped-stargate_alarm_handle-0-IOPS.svg)
