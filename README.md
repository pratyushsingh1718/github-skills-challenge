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

