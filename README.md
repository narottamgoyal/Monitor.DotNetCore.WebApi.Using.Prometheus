# MonitorDotNetCoreWebApiUsingPrometheus

Monitor dotnet core web api using prometheus

- Install the below [packages](https://github.com/narottamgoyal/MonitorDotNetCoreWebApiUsingPrometheus/blob/master/SampleWebApi/SampleWebApi.csproj)

        <PackageReference Include="App.Metrics.AspNetCore" Version="3.2.0" />
        <PackageReference Include="App.Metrics.AspNetCore.Endpoints" Version="3.2.0" />
        <PackageReference Include="App.Metrics.AspNetCore.Tracking" Version="3.2.0" />
        <PackageReference Include="App.Metrics.Formatters.Prometheus" Version="3.2.0" />

- Add the below code in [Program.cs](https://github.com/narottamgoyal/MonitorDotNetCoreWebApiUsingPrometheus/blob/master/SampleWebApi/Program.cs) class to support prometheus

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
            .UseMetricsWebTracking()
            .UseMetrics(option =>
            {
                option.EndpointOptions = endpointOptions =>
                {
                    endpointOptions.MetricsTextEndpointOutputFormatter = new MetricsPrometheusTextOutputFormatter();
                    endpointOptions.MetricsEndpointOutputFormatter = new MetricsPrometheusProtobufOutputFormatter();
                    endpointOptions.EnvironmentInfoEndpointEnabled = false;
                };
            })
            .UseStartup<Startup>();

- Make sure these below endpoints are working after making all the above changes

        http://<ip:port>/metrics
        http://<ip:port>/metrics-text

- Add a new job in [prometheus.yml](https://github.com/narottamgoyal/MonitorDotNetCoreWebApiUsingPrometheus/blob/master/prometheus.yml)

        - job_name: 'SampleWebAPi'
            metrics_path: /metrics-text
            static_configs:
            - targets: ['<ip:port>']

- Restart prometheus & then check targets page

        http://localhost:9090/targets

# Configure Prometheus as a Windows Service using [NSSM](https://nssm.cc/download)

- Stop prometheus.exe if is running
- Download [NSSM](https://nssm.cc/download)
- From the zip file, copy win64/nssm.exe into prometheus exe folder location
- Run the command prompt from prometheus exe folder location
- **nssm install prometheus <"prometheus exe full path">**
- **nssm start prometheus**
- NSSM basic commonds
  - nssm install <service-name>
  - nssm start <service-name>
  - nssm restart <service-name>
  - nssm stop <service-name>
  - nssm status <service-name>

# Alert Manager

- [Install Alert manager](https://github.com/prometheus/alertmanager/releases)
- Create [alertmanager.yml](https://github.com/narottamgoyal/Monitor.DotNetCore.WebApi.Using.Prometheus/blob/master/alertmanager.yml) in Alert manager folder for email settings
- Create [alert_rules.yml](https://github.com/narottamgoyal/Monitor.DotNetCore.WebApi.Using.Prometheus/blob/master/alert_rules.yml) in prometheus folder and update in [prometheus.yml](https://github.com/narottamgoyal/Monitor.DotNetCore.WebApi.Using.Prometheus/blob/master/prometheus.yml)
- Sample Alert email screenshot

  ![image](https://user-images.githubusercontent.com/2716202/78682081-142fbd80-78dd-11ea-93ad-227025d74cc7.png)

# Reference

- [Prometheus - Installation](https://www.youtube.com/watch?v=EDCnqnBkecw)
- [Prometheus and AppMetrics in .NET Core](https://www.youtube.com/watch?v=sM7D8biBf4k)
- [How to setup AlertManager in Prometheus](https://www.youtube.com/watch?v=GiaYg19-OTM)
- [Prometheus-Tutorial](https://github.com/vipin-k/Prometheus-Tutorial)
- [Notification_examples](https://prometheus.io/docs/alerting/notification_examples/)
- [Setting-up-alertmanager](https://daenney.github.io/2018/04/21/setting-up-alertmanager)
- [Run Prometheus as service using NSSM](https://mclarenappliedtechnologies.zendesk.com/hc/en-us/articles/360008182953-Setting-up-Infrastructure-Monitoring-with-Prometheus-and-Grafana)
