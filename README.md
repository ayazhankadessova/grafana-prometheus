## Monitoring using Grafana Dashboards for Prometheus

`Site Reliability Engineering(SRE)`, `Grafana`, `Prometheus`, `Monitoring`

## Overview 

This project entailed forming a monitoring dashboard in Grafana using Prometheus as a data source. 

The dashboard composed of three distinct panels: 

1. **A time-series chart**: 95th percentile request latency for every path and method.
2. **A gauge display**: CPU Usage for three different demo services.
3. **A data table**: Top 3 request rates that demo service is getting grouped by path and method lablels.

All data was accessed via the PromLabs' public [Prometheus server](https://demo.promlabs.com/).

## What I Learned

- Setting Up Grafana
- Creating a Prometheus data source in Grafana
- Creating a Dashboard
- Creating different panels
- Building PromQL queries
- Transforming data
- Organizing Panels

## Link & Demo

- Check out my Interactive [Grafana Dashboard Snapshot](https://snapshots.raintank.io/dashboard/snapshot/Q2M0jFiQ8qr5lw1ExZzeFiqKk115eFK1).

*A dashboard snapshot shares an interactive dashboard publicly. Grafana strips sensitive data such as queries (metric, template and annotation) and panel links, leaving only the visible metric data and series names embedded in the dashboard. Dashboard snapshots can be accessed by anyone with the link.*

- Demo:
![ayazhan-grafana-dashboard](https://github.com/ayazhankadessova/grafana-prometheus/assets/86869537/5c969550-a5df-477b-b87d-97f8992fc15c)

## Import my Grafana Dashboard

1. Save `Http_Cpu-1692340715847.json`

2. Login to Grafana

3. Add Prometheus Data Source named `Prometheus-HTTP-CPU` with Prometheus Server URL: https://demo.promlabs.com

4. Import Dashboard

5. Upload `Http_Cpu-1692340715847.json`.

## Steps to Create a Simple Grafana Dashboard for Prometheus

## 1. Install Grafana

* MacOS:
```
brew install grafana
```

## 2. Run Grafana:

- Run Grafana Using Docker
```
docker run -p 3000:30000 --name=grafana-storage:/var/lib/grafana grafana/grafana
```

- Running Grafana Using Pre-Built Binaries 

* Install from [Grafana.com[(https://grafana.com)
```
brew services start grafana
```
```
http://localhost:3000/?orgId=1
```

## 3. Log Into Grafana

- admin , admin

*To query and visualize data from a Prometheus-compatible backend in a Grafana dashboard, we will first need to configure a Prometheus data source in Grafana.*

## 4. Add Prometheus as a Data Source Type

- Change Data Source name + make it default

- Prometheus Server URL: https://demo.promlabs.com/

- This points the data source at the public PromLabs demo Prometheus server.

*Now Grafana is all set up to send promql queries to Prometheus.*

## 5. Create a Dashboard

## 5a. Time Series Visualization

1. Dashboard -> new
2. Create a panel -> Select Data Source
3. Edit promql by selecting metric
4. Make a query with build & with code for the last 30 mins.

> histogram_quantile(0.95, sum by(le, method, path) (rate(demo_api_request_duration_seconds_bucket[$__rate_interval])))

5. Change the legend & play with options
- Verbose -> Custom
* Legend v1: {{label_name}} : {method="GET", path="/api/foo"}
* Legend v2: {{path}}-{{method}} : /api/foo-GET

6. Configure unit
* seconds -> check y-axis, which now shows ms

## 5b. Gauge Visualization

1. Enter a PromQL query that shows us CPU usage for three different demo services.

- Query:

```
sum by(instance) (rate(demo_cpu_usage_seconds_total{mode!="idle"}[5m])) / on(instance) demo_num_cpus
```

2. Set min and max: 0 and 1
3. Change unit to percentage (0-1)
4. Color code using thresholds:

- 80%+ CPU usage is high -> red 0.8
- Others are green

*Now we can see where the red region would begin.*

## 5c. Table Visualization

1. Enter a PromQL query that Selects Top-3 Requests rates that our demo service is getting in the range vector, grouped by path and method labels.

- Query:

```
topk(3, sum by(path, method) (rate(demo_api_request_duration_seconds_count[5m])))
```

- We see different values over time for a single selected time series. We can select which time series to show, but we would actually like to see all time series at once and only the latest value for each one.

2. Change Format Option: Time Series -> Table.

3. Change Query Type: Range -> Instant. (Only fetches one latest value for each series)

4. Remove the TimeStamp since it always will be the latest value.

- Method #1:

* Transform -> Organize Fields -> Hide `Time`.

- Method #2:

* Scroll all the way down in the panel options -> `Add Field Override` -> `Fields with Type` -> `Time` -> `Add Override Property` -> `Hide in Table` -> `Enable Override`

5. Choose unit for Panel

- RPS (requests per second)

## 6. Organize Panels within a Dashboard into Thematic Rows

1. Group two HTTP stats panels into one row and the CPU stats into another.

- Add Panel -> Add a new row -> Rename the row -> HTTP stats
- Add Panel -> Add a new row -> Rename the row -> CPU stats

2. Grab each panel by its title bar and drag it into the right row.

3. You can collapse the rows by clicking on them.

4. You can also drag them around using the right hand side handles to reorder them on the dashboard.

## 7. Save the dashboard

- Give it a name -> Hit `Save`
  
## 8. Export `.json` for backup.


