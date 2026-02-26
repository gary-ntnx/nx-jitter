# AOS 7.5 Storage Jitter
Experiment is to read from stargate cache (to avoid NVME device jitter) and measure performance jitter.  In the end it was more useful to run over a long period (e.g. 1 Hour) so that we can monitor and identify periodic jitter.  Some of the periodic jitter is at a frequency of 10's of seconds so running the test for similar lengths of time reports as veriability rather than periodic jitter.

Identified two sources of variability
* [Services interference jitter](#services-jitter)
* [EPOLL Skew](#epoll-skew)

### Services Jitter
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
### EPOLL Skew
On my cluster nodes - there are 8 EPOLL threads - and by coincidence my test has 8 vdisks.  When we observe stargate we see that not all EPOLLs are always in use - in other words two EPOLL threads may be 
running at 70-80% while other threads are idle.
#### gflags
There are some gflags which control the dynamic rebalancing
###### load balancing defaults
```
epoll_load_balance_high_idle_threshold_percent : 40  (Less than 60% busy)
    If an epoll driver is more than this percent idle, it is considered a destination candidate by the load balancer. 
epoll_load_balance_low_idle_threshold_percent : 15 (More than 85% busy)
   If an epoll driver is less than this percent idle, it is considered a source candidate by the load balancer. 
```
###### messages
`stargate.INFO` shows messages like these:
```
I20260226 16:37:25.566675Z 3750024 tcp_connection.cc:1053] Epoll load balancer: Migrating tcp connection with fd 977 from epoll_1 to epoll_6
I20260226 16:37:25.566699Z 3750024 tcp_connection.cc:1103] fd 977 removed from epoll driver epoll_1. It will be migrated to epoll driver epoll_6
```
####
Attempt to improve convergence and balancing twiddling the gflags
##### gflag changes
Close ratio.
```
epoll_load_balance_high_idle_threshold_percent : int32 : 40 :: NP :: 45
epoll_load_balance_low_idle_threshold_percent : int32 : 15 :: NP :: 50
```
#### Default EPOLL Balancing IOPS
![Default EPOLL load-balancing](https://github.com/gary-ntnx/nx-jitter/blob/24fc0b3bb2a492a638f5b3cc392754f0acdcdb76/iops/run6-epoll-default-frodo-4.svg)

#### Close-ratio EPOLL Balancing IOPS
![Close-Ratio EPOLL load-balancing](https://github.com/gary-ntnx/nx-jitter/blob/24fc0b3bb2a492a638f5b3cc392754f0acdcdb76/iops/run5-epoll-converge-frodo-4.svg)
