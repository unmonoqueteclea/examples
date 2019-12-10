# AutoML Script

These WhizzML scripts will let the user run a **fully automated
Machine Learning pipeline** in BigML.

![BigML AutoML Steps](../res/steps.png)

From a train, validation and test datasets, the following tasks will be
automatically done:

-  `Unsupervised Models Generation`: Creating the following
  **unsupervised models**: `Cluster`, `Anomaly Detector`, `Association
  Discovery` (with leverage and lift as search_strategy), `PCA` and
  `Topic Model` (for datasets that contain text fields).
-  `Feature Generation`: Using the unsupervised models created
  previously to append automatically generated new features to all the
  user given datasets.
- `Feature Selection`: Reducing, automatically,
  the number of fields of the datasets using the **Recursive Feature
  Eliminination** algorithm.
-  `Model Selection` Using OptiML to find the best models and using
  the top 3 models to create a `Fusion` model to predict all the test
  dataset instances. If a validation dataset is given, this script
  will return an evaluation of the final model too.


The **inputs** for the script are:

* `train-source`: (source-id) Initial source id (it will be splitted
  for train/evaluation) If no train source is provided, the script
  will expect an `automl-execution` to be provided and will use its
  models as starting point.
* `test-source`: (source-id) Test source id. To use AutoML with new
  production data. If empty, no output-dataset is returned.
* `objective-name`: (string) Objective field name. `Car+1_has_errors`
  by default
* `automl-exection`: (execution-id) Previous execution of this script, to
  reuse created executions and models,
  e.g. execution/5d272205eba31d61920005cd. Either `train-dataset` or
  `automl-execution` should be provided for the script to work. When
  both are provided, the `automl-execution` argument is discarded and
  new resources are generated from the training data.
* `configuration-params`: (map) Execution configuration
  parameters. They will be overwritten if automl-execution is
  given. The following values must be provided:
  * `excluded-fields`: (list) List of fields that will be excluded
  from any dataset before any other process starts. e.g. ["bmi",
  "age"]. Empty by default.
  * `excluded-models`: (list) List of unsupervised models that won't
  be created nor reused during feature generation. e.g. ["anomaly",
  "cluster"]. Possible values are: association, cluster, anomaly, pca
  or topicmodel. Empty by default
  * `pca-variance-threshold`: (number) The PCA projection uses the
  minimum number of components such that the cumulative explained
  variance is greater than the given threshold. Values from 0 to 1.
  Default value is 1 (all the components will be used)
  * `max-rules`: (integer) Maximum number of association rules
    that should be included in the extended datasets. Default value
    is 20. The final number of rules added to the dataset can be lower
    than this value if there aren't enough rules in the created
    association discovery models or if the same rules appear on more
    than one association discovery model.
  * `balance-objective`: (boolean) Whether to balance classes
    proportionally to their category counts or not. True by default

**WARNING** To avoid confusion, `configuration-params` are always
overwritten by the corresponding input in `automl-execution` if this
is given.

**WARNING** All the fields that appear as `non-preferred` in train,
validation or test datasets will be considered `non-preferred` in all
the resources created by AutoML. It doesn't matter in which dataset
(train, validation or test) they are set as non-preferred fields.

The **outputs** for the script are:
* `output-dataset`: (dataset-id) Dataset with final predictions for the test dataset
* `output-evaluation`: (evaluation-id) Evaluation of the `Fusion`
  model using the `validation-dataset`
* `output-fusion`: (fusion-id) Output fusion model
* `selected-fields`: (list) Selected important field names
* `unsupervised-models`: (list) List of unsupervised models created
  from train-dataset

## Usage
There are two different ways of using this script:

### From a train (and optionally test) source
In this case, the expected inputs for the script are the
`train-source`, the `objective-name` and (optionally) the
`test-source`, with no `autml-execution` input.

The script will run the fully **automated Machine Learning pipeline**
and it will return, at the end of the process, the **output-dataset**
with the final predictions for the test dataset using a `Fusion` with
the best models from the created `OptiML`.

The script will also return an `evaluation` of the final `Fusion`
model with a `validation-dataset` obtained from the train dataset.

### From a test source and a previous execution id
If we want to predict new data using the same models created by a
previously executed `automl` script, you can use the `automl-execution`
parameter (no `train-source` parameter needed) associated to that
previous execution and provide the test source ID in the
`test-source` parameter.

In this case, some steps of the process will be bypassed. The script
won't generate neither a new `OptiML`, unsupervised model nor a
**Recursive Feature Selection** execution.
