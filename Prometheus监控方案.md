#  Prometheus是一种开源的监控解决方案，采用Go语言编写的。它通过HTTP协议周期性地从指定的目标中获取数据，并把数据存储在本地存储中。Prometheus可以查询和分析这些数据，并且提供仪表板和报警功能。Prometheus的主要优点包括:

# • 多维度数据模型: Prometheus的数据模型非常灵活，可以用多种不同的维度来描述不同的资源、行为和指标。
# • 支持多种API: Prometheus提供了多种API，包括标准的查询语言和REST接口。
# • 高效的数据存储: Prometheus使用自主设计的本地存储引擎，支持高效的时间序列数据存储和查询。
# • 开放性: Prometheus是一种开放的平台，可以通过插件来扩展其功能，也可以与其他工具进行集成。

```golang
package main

import (
	"log"
	"net/http"

	"github.com/prometheus/client_golang/prometheus"
	"github.com/prometheus/client_golang/prometheus/promhttp"
)

type metrics struct {
	cpuTemp    prometheus.Gauge
	hdFailures *prometheus.CounterVec
}

func NewMetrics(reg prometheus.Registerer) *metrics {
	m := &metrics{
		cpuTemp: prometheus.NewGauge(prometheus.GaugeOpts{
			Name: "cpu_temperature_celsius",
			Help: "Current temperature of the CPU.",
		}),
		hdFailures: prometheus.NewCounterVec(
			prometheus.CounterOpts{
				Name: "hd_errors_total",
				Help: "Number of hard-disk errors.",
			},
			[]string{"device"},
		),
	}
	reg.MustRegister(m.cpuTemp)
	reg.MustRegister(m.hdFailures)
	return m
}

func main() {
	// Create a non-global registry.
	reg := prometheus.NewRegistry()

	// Create new metrics and register them using the custom registry.
	m := NewMetrics(reg)
	// Set values for the new created metrics.
	m.cpuTemp.Set(65.3)
	m.hdFailures.With(prometheus.Labels{"device": "/dev/sda"}).Inc()

	// Expose metrics and custom registry via an HTTP server
	// using the HandleFor function. "/metrics" is the usual endpoint for that.
	http.Handle("/metrics", promhttp.HandlerFor(reg, promhttp.HandlerOpts{Registry: reg}))
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```
