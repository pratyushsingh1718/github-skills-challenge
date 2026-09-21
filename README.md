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


## Task 3: Anomaly Detection Findings

The provided `AnomalyDetector` processed all 10 operational records.

It detected anomalies at:

- `2026-09-20T10:05:00`: response time was 610 ms, above the 500 ms threshold.
- `2026-09-20T10:06:00`: response time was 640 ms, CPU was 94%, and memory was
  91%, exceeding the configured thresholds.

The normal observations were not flagged because their metrics remained below the
configured thresholds.

The supplied records contain `ERROR` log events at `10:05` and `10:06`.
However, the current detector checks for `WARNING`, so it does not add an
`Error log detected` reason. This is a detection limitation and will be corrected
during Task 5.

The detector output includes the timestamp, service, anomaly type, detection
reasons, and original source record, making it possible to understand why a
record was flagged.

## Task 4: AIOps Event Flow

The event flow uses the provided architecture:

- `Event`: the anomaly message created by `AnomalyDetector`.
- `EventProducer`: publishes the generated anomaly event.
- `EventTopic`: stores events in the in-memory topic.
- `EventConsumer`: receives events from the topic.
- Downstream AIOps output: displays the consumed anomaly event.

The detector generated anomaly events and passed them to the producer. The
producer published the events, but the consumer received zero events because the
producer uses `service-events` while the consumer uses a separate
`anomaly-events` topic. Therefore, the events did not reach downstream AIOps
processing. This topic mismatch will be corrected in Task 5.

## Task 5: Workflow Troubleshooting and Corrections

Two workflow issues were identified.

1. **Anomaly detector:** `src/anomaly_detector.py` checked for the log level
   `WARNING`, but the supplied data uses `ERROR`. As a result, the concerning
   error logs were not included in the anomaly reasons. The condition was changed
   to detect `ERROR`.

2. **Event topic wiring:** `src/aiops_pipeline.py` created separate topics for
   the producer and consumer. The producer published to `service-events`, while
   the consumer read from `anomaly-events`. The pipeline was corrected so both
   components use the same `anomaly-events` topic.

After the corrections, the pipeline processed 10 records, detected 2 anomalies,
and the consumer received 2 events. The anomaly reasons now include the relevant
ERROR log information.

## Task 6: End-to-End Pipeline Execution

The corrected pipeline completed the full workflow:

`Operational Data -> Anomaly Detection -> Event -> Producer -> Topic -> Consumer -> AIOps Output`

The pipeline processed 10 operational records and detected 2 anomalous
observations. Two anomaly events were generated and published to the shared
`anomaly-events` topic. The consumer received and processed both events
successfully.

The final AIOps output represented these operational issues:

- `2026-09-20T10:05:00`: Payment service timeout, with high response time and an
  ERROR log.
- `2026-09-20T10:06:00`: Database connection timeout, with high response time,
  high CPU, high memory, and an ERROR log.

## Task 7: README Documentation and Reproduction

This README documents the complete assessment scenario and reproduction evidence:

1. The AIOps scenario monitors a synthetic `payment-service` for slow requests,
  resource saturation, and timeout errors.
2. The operational data contains timestamped response-time, CPU, memory, log
  level, and log message fields.
3. Normal and unusual metric and log observations are described in Task 2.
4. Anomaly detection results and detection reasons are recorded in Task 3.
5. The producer, topic, consumer, event, and downstream flow are described in
  Task 4.
6. The final workflow result is recorded in Task 6.
7. The detector and topic-wiring issues and their corrections are recorded in
  Task 5.
8. A limitation is the use of fixed thresholds; adaptive or rolling-window
  baselines could improve detection for changing workloads.
9. The commands below reproduce the demonstration without external
  infrastructure.

Reproduction commands:

```bash
python3 -m pip install -r requirements.txt
PYTHONPATH=.:src python3 -m pytest -q
python3 src/aiops_pipeline.py
```

Expected final pipeline result:

```text
Records processed: 10
Anomalies detected: 2
Events consumed: 2
```

## Task 8: Validation Record

The provided validation suite was run with:

```bash
PYTHONPATH=.:src python3 -m pytest -q
```

Result: `8 passed`.

The validation and final pipeline execution confirmed that operational data can
be loaded, anomalies can be detected, anomaly events can be generated and
published, the shared topic can deliver events to the consumer, and the final
AIOps output completes successfully.
