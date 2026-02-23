# AOS 7.5 Storage Jitter
Experiment is to read from stargate cache (to avoid NVME device jitter) and measure performance jitter.  In the end it was more useful to run over a long period (e.g. 1 Hour) so that we can monitor and identify periodic jitter.  Some of the periodic jitter is at a frequency of 10's of seconds so running the test for similar lengths of time reports as veriability rather than periodic jitter.

##### AOS 7.5 Default
