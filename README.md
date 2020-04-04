# MonitorDotNetCoreWebApiUsingPrometheus

Monitor dotnet core web api using prometheus

- Install the below packages

        <PackageReference Include="App.Metrics.AspNetCore" Version="3.2.0" />
        <PackageReference Include="App.Metrics.AspNetCore.Endpoints" Version="3.2.0" />
        <PackageReference Include="App.Metrics.AspNetCore.Tracking" Version="3.2.0" />
        <PackageReference Include="App.Metrics.Formatters.Prometheus" Version="3.2.0" />

- Add the below code in Program.cs class to support prometheus

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

- Add a new job in prometheus.yml

        - job_name: 'SampleWebAPi'
            metrics_path: /metrics-text
            static_configs:
            - targets: ['<ip:port>']

* Restart prometheus & then check targets page

        http://localhost:9090/targets

# Reference

- [Prometheus - Installation](https://www.youtube.com/watch?v=EDCnqnBkecw)
- [Prometheus and AppMetrics in .NET Core](https://www.youtube.com/watch?v=sM7D8biBf4k)
- [How to setup AlertManager in Prometheus](https://www.youtube.com/watch?v=GiaYg19-OTM)
