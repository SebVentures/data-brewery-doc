Operate
=======

In any data management process, it is important to get a sense of what is going on especially since it's operated in the cloud.


Current job status
------------------

The first thing you want to know is the current status of jobs running. For that, you can use the `/job/running` API as below.


```bash
curl -H 'Accept : text/plain' -u token:<token> https://etl.dataintoresults.com/api/v0/job/running
```

In this case, we can see that two of our jobs are running (module 901 and 902), as modules are composed of many table, we give you the exact step. You have as ell the running_time column that tell you since how long the job is running (in seconds)

```
event_log_id, module, event, details, start_timestamp, running_time
40, factory, runModule, {"module":"902","step":"Sourcing for m902_google_analytics.sessions"}, 2017-04-01 18:11:11.051761, 4
34, factory, runModule, {"module":"901","step":"Sourcing for m901_prestashop.ps_guest"}, 2017-04-01 18:10:34.327918, 41
```


Finished and failed jobs
------------------------

You migt as well get more information about previous jobs. Did the night job finished well? That wat the `/job/history` is all about. It gives you information about previous API call (seccessful or not).


```bash
curl -H 'Accept : text/plain' -u token:<token> https://etl.dataintoresults.com/api/v0/job/history
```

With this command, we can see thatmost previous call have succeded and tat the module 901 takes around 60-100 seconds to complete and te 902 less than 10 seconds. There is also some failed jobs (status = FAILURE) where we tried to request data from a datastore that doesn't exists.

```
event_log_id, module, event, status, details, issue, start_timestamp, end_timestamp, running_time
40, factory, runModule, SUCCESS, {"module":"902","step":"Sourcing for m902_google_analytics.sessions"}, {}, 2017-04-01 18:11:11.051761, 2017-04-01 18:11:17.025416, 6
34, factory, runModule, SUCCESS, {"module":"901","step":"Sourcing for m901_prestashop.ps_zone_shop"}, {}, 2017-04-01 18:10:34.327918, 2017-04-01 18:11:39.108221, 65
32, factory, runModule, SUCCESS, {"module":"401","step":"Sourcing for visits"}, {}, 2017-04-01 18:06:32.443286, 2017-04-01 18:06:34.308409, 2
30, factory, runModule, SUCCESS, {"module":"902","step":"Sourcing for m902_google_analytics.sessions"}, {}, 2017-04-01 18:06:25.993908, 2017-04-01 18:06:32.208991, 6
27, factory, runModule, SUCCESS, {"module":"901","step":"Sourcing for m901_prestashop.ps_zone_shop"}, {}, 2017-04-01 18:04:54.944928, 2017-04-01 18:06:25.75413, 91
26, factory, runModule, SUCCESS, {"module":"401","step":"Sourcing for visits"}, {}, 2017-04-01 18:01:17.593462, 2017-04-01 18:01:25.191793, 8
19, factory, runModule, SUCCESS, {"module":"401","step":"Sourcing for visits"}, {}, 2017-03-29 16:34:34.760818, 2017-03-29 16:34:40.558077, 6
15, factory, runModule, SUCCESS, {"module":"401","step":"init"}, {}, 2017-03-29 16:18:38.208954, 2017-03-29 16:18:40.288993, 2
11, factory, runModule, SUCCESS, {"module":"901","step":"Sourcing for m901_prestashop.ps_zone_shop"}, {}, 2017-03-29 16:15:36.822885, 2017-03-29 16:17:10.403274, 94
10, factory, runModule, SUCCESS, {"module":"902","step":"Sourcing for m902_google_analytics.sessions"}, {}, 2017-03-29 16:15:20.517514, 2017-03-29 16:15:25.789068, 5
9, factory, runModule, FAILURE, {"module":"902","step":"Sourcing for m902_google_analytics.sessions"}, {"issue":"ERROR: schema \"m902_google_analytics\" does not exist\n  Position: 14"}, 2017-03-29 16:14:36.858743, 2017-03-29 16:14:38.676609, 2
8, factory, postDataWarehouse, SUCCESS, {}, {}, 2017-03-29 16:14:30.136409, 2017-03-29 16:14:30.317431, 0
5, factory, runModule, SUCCESS, {"module":"902","step":"Sourcing for m902_google_analytics.sessions"}, {}, 2017-03-29 16:06:39.170205, 2017-03-29 16:06:54.147338, 15
4, factory, data, SUCCESS, {"step":"Get data from google_analytics.sessions (100 rows max)"}, {}, 2017-03-29 16:03:35.524115, 2017-03-29 16:03:39.869872, 4
3, factory, data, SUCCESS, {"step":"Get data from google_analytics.sessions (100 rows max)"}, {}, 2017-03-29 16:03:18.238442, 2017-03-29 16:03:27.332388, 9
2, factory, data, FAILURE, {"step":"Get data from ga.sessions (100 rows max)"}, {"issue":"No datastore like \"ga\"."}, 2017-03-29 16:03:04.038809, 2017-03-29 16:03:04.163863, 0
1, factory, postDataWarehouse, SUCCESS, {}, {}, 2017-03-29 16:00:56.003035, 2017-03-29 16:00:59.168067, 3
```
