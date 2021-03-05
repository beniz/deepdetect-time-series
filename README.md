<p align="left"><img src="https://www.deepdetect.com/img/icons/menu/sidebar/deepdetect.svg" alt="DeepDetect Logo" width="25%" /></p>

<h1 align="center">SoTA Time Series Forecasting Models Using N-BEATS & Transformers</h1>

<h2 align="center">Incredibly accurate time series forecasting models</h2>

Use quick and very powerful modeling of time series for a variety of applications, including **forecasting** and **anomaly detection**.

<p align="center"><img src="https://www.deepdetect.com/img/ts/nasa_a_4_anomaly_detection1.png" alt="Anomaly Detection" width="35%" /></p>

## Main features

- Fast training CPU / GPU

- Lightweight networks for embedded devices

- Higher accuracy than recurrent networks

- Multivariate time series with up to 800 signals as inputs
- Fully configurable backcast and forecast
- Open Source under permissive licenses

## Quickstart

### Requirements

- Install the Python tooling

```bash
pip install "git+https://github.com/jolibrain/dd_widgets"
pip install "git+https://github.com/jolibrain/deepdetect.git#egg=dd_client&subdirectory=clients/python"
```

- Run the DeepDetect Server docker container

```
docker pull jolibrain/deepdetect_gpu
docker run -d -p 8080:8080 jolibrain/deepdetect_gpu
```
Replace `_gpu` with `_cpu` for machines without GPU.

### Setup steps

```python
datadir = "/path/to/nasa/A-4_f1"
model_name = "nbeats_nasa_a4"

nbeats = NBEATS(
    sname = "nbeats_timeseries",
    host = "127.0.0.1",
    port = 8080,
        
    models_dir = "/path/to/models/",
    models = [model_name],
    
    # all columns of the csv
    columns = ["0"],
    # NBEATS predicts the future from the past: target columns are also input columns
    target_cols = ["0"],
    # ignored columns. The model won't predict the future of these signals
    ignored_cols = [],
    
    # Model parameters
    # NBEATS hyperparameters
    # 2 x generic stack with theta = 128, 5 blocks per stacks, hidden unit size of 128 everywhere
    # more details here https://www.deepdetect.com/server/docs/api/#create-a-service and in NBEATS paper
    template_params = ["g128", "g128", "b5", "h128"],
    # number of past steps taken as input
    backcast = 200,
    # number of future steps predicted
    forecast = 1,

    # Training parameters
    gpuid = 0,
    batch_size = 100,
    iter_size = 1,
    iterations = 100000,
    
    # predictions are cached in this directory to save time
    output_dir = "/path/to/predictions/",
    # root directory of the prediction data
    datadir = datadir,
    # ts.get_datafiles retrieves all csv files from a directory
    datafiles = ts.get_datafiles(datadir)
)

```

### Optional: take a look at the data

Now that the model has been setup, the DeepDetect time series widget allows the target signal to be visualized with:

```python
nbeats.dataset_ui()
```

<div class="row">
<figure>
<img src="https://www.deepdetect.com/img/blog/09_nasa_a4_f1_1_signal_viz_ui.png">
<figcaption>Visualization of the train set.</figcaption>
</figure>
</div>


### Model training

DeepDetect makes the training step easy and robust. Here to train the model with train dataset located at `datadir + "
/train"` and test dataset located at `datadir + "/test"`:

```python
train_data = [datadir + "/train", datadir + "/test"]
# create a service with name `nbeats.sname` and model `model_name`
nbeats.create_service(model_name)
# train service `nbeats.sname` on data `train_data`
nbeats.train(train_data)
```

This start training and then returns immediately, while the training job runs in the background. Expect between 30 min
utes and one hour for this job, depending on your GPU.

Training metrics can be monitored with the DeepDetect Platform, or with the DeepDetect Server API.

<div class="row">
<div class="col">
<figure>
<img src="https://www.deepdetect.com/img/blog/11_nasa_a4_training_loss.png">
<figcaption>Training loss with NBEATS on NASA a4.</figcaption>
</figure>
</div>
<div class="col">
<figure>
<img src="https://www.deepdetect.com/img/blog/11_nasa_a4_training_metrics.png">
<figcaption>Test metrics with NBEATS on NASA a4.</figcaption>
</figure>
</div>
</div>
