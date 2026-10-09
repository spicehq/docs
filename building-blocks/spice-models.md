---
description: Spice Machine Learning (ML) Models
icon: brain-circuit
---

# ML Models

{% hint style="info" %}
Spice Models are in beta for Design Partners. Get in touch for more info.
{% endhint %}

Spice Models enable the training and use of ML models natively on the Spice platform.

The platform currently supports time-series forecasting models, with other categories of models planned.

Hosted models have first-class access to co-located data for training and inferencing including: [Spice managed datasets](/broken/pages/JnKPswDs1OXhUD2OxR0K), [user managed datasets](../cloud/portal/external-data-sources.md), and [custom datasets and views](../cloud/portal/datasets-and-views.md). Additionally, [Spice Firecache](../features/federated-sql-query.md) can be leveraged to train and infer up to 10x faster.

### Defining a Model

Models are defined using a [model manifest](/broken/pages/kBNlZsrTXSmL6zaiY8bm) YAML file. Model details such as data requirements, architecture, training parameters, and other important hyperparameters are defined in the `model.yaml.`

Add a `model.yaml` file to the repository path `/models/[model_name]/model.yaml` of a [GitHub connected Spice app](../cloud/portal/apps/connect-github.md), replacing `[model_name]` with the desired model name.

Refer to the [Models YAML specification](/broken/pages/kBNlZsrTXSmL6zaiY8bm) for all available configuration options.

For example model manifests, see the [models samples repo](https://github.com/spiceai/samples/tree/trunk/.spice/models).

## Training a Model

In the [spice.ai Portal](https://spice.ai), navigate to the **Models** tab of the Spice app.

`model.yaml` files committed to the connected repository will be automatically detected and imported as Spice Models.

<figure><img src="../.gitbook/assets/CleanShot 2024-01-30 at 10.49.30@2x.png" alt=""><figcaption><p>Spice Models defined in <code>model.yaml</code> files automatically detected and imported in the Portal.</p></figcaption></figure>

Navigating to a specific Model will show detailed information as defined in the `model.yaml`.

A training run can be started using the **Train** button.

<figure><img src="../.gitbook/assets/CleanShot 2024-01-30 at 10.51.58@2x.png" alt=""><figcaption><p>Details for a specific Model.</p></figcaption></figure>

**Training runs** in progress will be shown and updated, along with historical training runs.

<figure><img src="../.gitbook/assets/CleanShot 2024-01-30 at 10.53.08@2x.png" alt=""><figcaption><p>A model training with status "Running".</p></figcaption></figure>

The **Training Status** will be updated to `Complete` for successfully completed training runs. Details and the Training Report, are available on the **Training Run** page.

<figure><img src="../.gitbook/assets/CleanShot 2024-01-30 at 10.55.32@2x.png" alt=""><figcaption><p>A successfully completed Model training run with status "Complete".</p></figcaption></figure>

### Running Model Predictions

{% hint style="info" %}
Spice Models (beta) currently supports **time-series forecasting**.&#x20;

Additional categories of data science and machine learning are on our roadmap.
{% endhint %}

A successfully trained model can be used to make predictions.

The lookback data (inferencing data) is automatically provided by the platform and wired up to the inference, enabling a prediction to be made using a simple API call.

### AI Predictions in the Playground

Navigate to **AI Predictions** in the **Playground**.

Successfully trained models will be available for selection from the model selector drop down on the right.

Clicking **Predict** will demonstrate calling the predictions API using lookback data within the Spice platform. A graph of the predicted value(s) along with the lookback data will be displayed.

<figure><img src="../.gitbook/assets/CleanShot 2024-01-30 at 11.02.00@2x.png" alt=""><figcaption><p>The AI Predictions playground.</p></figcaption></figure>

### Predictions by API

The **Training Runs** page provides training details including a copyable `curl` command to make a prediction from the command line.

For details on the API, see [Prediction Documentation](/broken/pages/2C09l89bwNrM5jRF0xSx).&#x20;

<figure><img src="../.gitbook/assets/CleanShot 2024-01-30 at 11.00.34@2x.png" alt=""><figcaption><p>Detailed training run page with the predictions API</p></figcaption></figure>
