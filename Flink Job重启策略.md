## 每天一道数据开发面试题

### Flink Job重启策略

- Flink支持不同的重启策略，以在故障发生时控制作业如何重启

- 集群在启动时会伴随一个默认的重启策略，在没有定义具体重启策略时会使用该默认策略。

- 如果在工作提交时指定了一个重启策略，该策略会覆盖集群的默认策略默认的重启策略可以通过 Flink 的配置文件 flink-conf.yaml 指定。配置参数 restart-strategy 定义了哪个策略被使用。

- 常用的重启：

  1.策略固定间隔 (Fixed delay) 2.失败率 (Failure rate) 3.无重启 (No restart)

- 如果没有启用 checkpointing，则使用无重启 (no restart) 策略。如果启用了 checkpointing，但没有配置重启策略，则使用固定间隔 (fixed-delay) 策略

- 重启策略可以在flink-conf.yaml中配置，表示全局的配置。也可以在应用代码中动态指定，会覆盖全局配置