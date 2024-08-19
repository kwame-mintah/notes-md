# Serving Strategies

​​
Machine learning models can not be deployed and forgotten, as new data may imply a need to check for model drift and automatically update it. Traceability of models in production becomes imperative. There are many ways to serve ML models to end users today.

## The 3 Kinds of Methods

### Batch

Batch prediction is useful when you want to generate predictions for a set of observations all at once, and then take action on a certain percentage or number of the observations.

### Online

Online prediction scenarios are for cases when you want to generate predictions on a one-by-one basis for each example independent of the other examples, in a low-latency environment.

### Streaming

Streaming prediction is when data is generated every second from thousands of data sources and is required to be processed and analysed as soon as possible. For example anomaly detection in sensor data and predictive maintenance for industrial equipment, demand real-time predictions from machine learning models.

## Deployment Strategies

### Embedded Models

Embedded machine learning is a type of edge computing: running algorithms on end-user computational resources rather than a central data centre (the cloud) and can offer a few key advantages compared to cloud-based processing:

- Speed: Without a round-trip to a server for predictions, model inputs and outputs can be provided much more quickly.
- Connectivity: An internet connection is not required for embedded machine learning. This means you can deploy your model somewhere without having to have an internet network set up (i.e. in a vast field to analyse crops).
- Privacy: All data processing happens on a device directly where a user is present, meaning the input data received stays locally.

#### Website

- Prototyping stage
- Model won't change or be retrained often
- Not a core component of the website
- Minimize external dependencies
- Relatively simple and not complex website

#### Mobile app

| Pros                                    | Cons                                     |
| --------------------------------------- | ---------------------------------------- |
| Increased privacy                       | Limited computational resources          |
| Offline inference                       | Storage constraints                      |
| Low latency                             | Limited model interactivity              |
| Energy efficiency                       | Compatibility issues                     |
| Compliance with regulatory requirements | Model maintenance and updates challenges |
| Bandwidth saving                        | Training limitations                     |

#### Edge device

| Pros                       | Cons                     |
| -------------------------- | ------------------------ |
| Reduced network dependency | Scalability concerns     |
| Distributed processing     | Environmental factors    |
| Edge-to-Edge communication | Limited processing power |
| Autonomous operation       | Connectivity issues      |
| Reduced cloud costs        | Resource constraints     |
| Low latency at the edge    | Heterogeneous Hardware   |

### Model as a Service

Model as a Service (MaaS) is a cloud-based service that enables businesses to develop, deploy, and manage machine learning models quickly and efficiently. Offering access to machine learning models that can be used to develop AI applications and services. Enabling businesses to quickly and easily deploy pre-trained machine learning models that are ready to use.

| Pros                            | Cons                           |
| ------------------------------- | ------------------------------ |
| Improved testing and debugging  | Increased complexity           |
| Flexibility in technology stack | Dependency management          |
| Enhanced team collaboration     | Communication overhead         |
| Improved resource utilization   | Increased resource consumption |
| Scalability                     | Security concerns              |

### Model as Data

Niche deployment strategy where the models are queried as if they were items in a database. For example [PostgresML](https://postgresml.org/) a complete MLOps platform in a PostgreSQL extension. Build simpler, faster and more scalable models right inside your database.

---

#ml #mlops #automl
