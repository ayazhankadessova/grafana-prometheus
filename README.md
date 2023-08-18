Grafana DashBoard with 3 Panels, showing a chart, a gauge, and a panel and we organized our panels inti rows.

## 1. Install Grafana

-via docker

```
docker run -p 3000:30000 --name=grafana-storage:/var/lib/grafana grafana/grafana
```

## 2. Via Grafana Binaries

- Install from here

```
brew services start grafana
```

```
http://localhost:3000/?orgId=1
```

## 3. Add Prometheus as a Data Source Type

- Change DS name + make it default

- https://demo.promlabs.com/

- Now Grafana is all set up to send promql queries to Prometheus

## 4. Create a Dashboard

## 4a. Time Series Visualization

- Dashboard -> new
- Create a panel -> Select Data Source
- Edit promql by selecting metric

## Make a query with build & with code for the last 30 mins

> histogram_quantile(0.95, sum by(le, method, path) (rate(demo_api_request_duration_seconds_bucket[$__rate_interval])))

## 6. Change the legend & play with options

- Verbose -> Custom
- Legend v1: {{label_name}} : {method="GET", path="/api/foo"}
- Legend v2: {{path}}-{{method}} : /api/foo-GET

## 7. Configure unit

- seconds -> check y-axis, which now shows ms
