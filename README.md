# AIOps Monitoring and Event Processing

## Task 1: Project Overview

This project monitors a synthetic `payment-service` that produces timestamped
metrics and log information. The operational problem is identifying slow payment
requests, high resource utilisation, and service or database timeout errors before
they affect continued payment processing.

AIOps is used in this assessment to analyse the service's operational data,
identify abnormal behaviour, generate anomaly events, and pass those events
through a lightweight producer, topic, consumer, and downstream processing flow.
The simulation uses Python and in-memory components rather than external Kafka or
Airflow infrastructure.

### Repository Components

- `data/service_data.json`: synthetic operational data for the payment service.
- `src/anomaly_detector.py`: checks metrics and log information for anomalies.
- `src/event_producer.py`: publishes anomaly events.
- `src/event_topic.py`: provides the in-memory event topic.
- `src/event_consumer.py`: receives events from the topic.
- `src/aiops_pipeline.py`: coordinates data loading, detection, event production,
  and event consumption.
- `tests/`: validation tests for calculations, anomaly detection, and event flow.

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

## Task 2: Operational Data Analysis

The operational data is stored in `data/service_data.json` and contains 10
timestamped observations for the `payment-service`.

Metric fields:

- `response_time_ms`: payment request response time.
- `cpu_percent`: CPU utilisation.
- `memory_percent`: memory utilisation.


Log fields:

- `log_level`: log severity, such as `INFO` or `ERROR`.
- `message`: description of the service event.


The `timestamp` field records when each observation occurred. The timestamps are
in ISO format and occur at one-minute intervals from
`2026-09-20T10:00:00` through `2026-09-20T10:09:00`.
The observations from `10:00` through `10:04` and `10:07` through `10:09`
represent normal behaviour. They have `INFO` logs, response times from 120 ms
to 150 ms, CPU utilisation from 42% to 50%, and memory utilisation from 51% to
57%.

The observations at `10:05` and `10:06` represent unusual behaviour. Both have
`ERROR` logs and response times above 500 ms. The `10:06` record also has high
CPU utilisation at 94% and high memory utilisation at 91%. The messages describe
a payment service timeout and a database connection timeout.

