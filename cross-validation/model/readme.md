# Model's k-fold cross-validation

The objective of this script is to perform a k-fold cross validation of a
model built from a dataset. The algorithm:

- Divides the dataset in k parts
- Holds out the data in one of the parts and builds a model with the rest of
  data
- Evaluates the model with the hold out data
- The second and third steps are repeated with each of the k parts, so that
  k evaluations are generated
- Finally, the evaluation metrics are averaged to provide the cross-validation
  metrics.

The **inputs** for the script are:

* `dataset-id`: (dataset-id) Dataset ID for the training data
* `k-folds`: (integer) Number of folds to split the dataset into (optional, default=5)
* `objective-id`: (string) ID of the objective field for the model (optional)
* `missing-splits`: (boolean) Model's missing_splits flag (optional, default=false)
* `stat-pruning`: (boolean) Model's statistical pruning flag (optional, default=false)
* `balance-objective`: (boolean) Model's balance objective flag (optional, default=false)
* `weight-field`: (string) ID of the field used in the model as weight field (optional, default="")
* `objective-weights`: (list) List of objective weights for the model (optional, default=[])
* `node-threshold`: (integer) Maximum number of nodes in the model (optional, default=-1)
* `delete-resources?`: (boolean) Whether to delete intermediate resources (optional, default=true)
* `stratified?`: (boolean) Whether to stratify the k-folds across classes (optional, default=false)

as you can see, most of the inputs are optional. They default to the defaults
in the platform. The `objective-id` will also be inferred from the one in
the dataset if it is not provided.

# Using the model's k-fold cross-validation script

One just needs to call

```
(model-cross-validation dataset-id
                        k-folds
                        objective-id
                        missing-splits
                        stat-pruning
                        balance-objective
                        weight-field
                        objective-weights
                        node-threshold
                        delete-resources?
                        stratified?)
```

using the previously described inputs.

The **output** of the script will be an `evaluation ID`. This evaluation is a
cross-validation, meaning that its metrics are averages of the k evaluations
created in the cross-validation process.
