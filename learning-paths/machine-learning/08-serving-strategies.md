​​
Machine learning models can not be deployed and forgotten, as new data may imply a need to check for model drift and automatically update it. Traceability of models in production becomes imperative. There are many ways to serve ML models to end users today, and even though new ways keep popping up as time passes, many questions remain. Firstly, how do we pick the appropriate serving approach and, secondly, how can we execute it as fast and efficiently as possible?

## The 3 Kinds of Methods

### Batch

Batch prediction is useful when you want to generate predictions for a set of observations all at once, and then take action on a certain percentage or number of the observations.

### Online

Online prediction scenarios are for cases when you want to generate predictions on a one-by-one basis for each example independent of the other examples, in a low-latency environment.

### Streaming

Streaming prediction is when data is generated every second from thousands of data sources and is required to be processed and analysed as soon as possible. For example anomaly detection in sensor data and predictive maintenance for industrial equipment, demand real-time predictions from machine learning models.

## Deployment Strategies

### Embedded Models

Deploy models within:

- Website
  - Prototyping stage
  - Model won't change or be retrained often
  - Not a core component of the website
  - Minimise external dependencies
  - Relatively simple and not complex website
- Mobile app
  - Pros:
    - Increased privacy
    - Offline inference
    - Low latency
    - Energy efficiency
    - Compliance with regulatory requirements
    - Bandwidth saving
  - Cons
    - Limited computational resources
    - Storage constraints
    - Limited model interactivity
    - Compatibility issues
    - Model maintenance and updates challenges
    - Training limitations
- Edge device
  - Pros:
    - Reduced network dependency
    - Distributed processing
    - Edge-to-Edge communication
    - Autonomous operation
    - Reduced cloud costs
    - Low latency at the edge
  - Cons:
    - Scalability concerns
    - Environmental factors
    - Limited processing power
    - Connectivity issues
    - Resource constraints
    - Heterogeneous Hardware

### Model as a Service

TBA...

Within Dedicated hardware, serverless, as pipelines ...

### Model as Data

Niche deployment strategy where the models are queried as if they were items in a database. For example [PostgresML](https://postgresml.org/) a complete MLOps platform in a PostgreSQL extension. Build simpler, faster and more scalable models right inside your database.

---

#ml #mlops #automl
