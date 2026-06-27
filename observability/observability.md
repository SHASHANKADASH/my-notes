# What is observability?
- Observability is the ability to understand and react to the state of an IT system or app based on that data that it generates.
# How is it different from monitoring?
- Monitoring is the process of collecting data and generating reports on different metrics that define system health. (eg. CPU usage).
- It helps to understand when and what component of a system.
- For example collecting system health metric of multiple services can tell us when and which service has gone down. 
- Based on this we can create alerts and take actions.
- Observability on the other hand provides us deeper insights to the applications and helps us to understand why it happened and how to fix it.
- There are multiple tools that observability provides which we can use to gain deeper understanding of application behaviour.
### Three pillars of observability
1. **Metrics**: Metrics are numeric representation of data measured over time. For example CPU and memory utilization or request throughput.
2. **Logs**: Detailed, text based records of what application was doing at that particular time. Each log can have a timestamp, thread, package, span_id, trace_id etc. information in the logs.
3. **Traces**: Traces represent the end to end life cycle of a single request or transaction as it flow through a system (or systems). Each event in that request is called a *span*.
