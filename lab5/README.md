# Lab 5 - Instructions

## Steps

Prerequisites
1. Complete Unit 5 Moodle quiz first.
2. Install EconML package with `pip install econml` or `pip install --user econml`.
3. Implement $\epsilon_{ATE}$ and $\epsilon_{PEHE}$ evaluation metrics. See lecture slides for help. You should have done this already as part of the Moodle quiz. You can re-use your quiz answers here. Have the code for the metrics ready to use in your notebook or python script.

Data
1. Load the IHDP dataset (see bottom of the page for a description).
2. Drop the 'ycf' column.
3. Separate the data into X, T, Y and ITE.
4. Split the data into training and testing (80/20 ratio is fine).

Modelling
1. Estimate ATEs and ITEs with 3 different types of causal models: S-Learner, IPSW and X-Learner. Train the model on the training data and make predictions on the test set. See lecture slides for help. You are free to use any regressors and classifiers you like (e.g. Random Forests). To merge X and T, you can use `np.hstack` or `np.concatenate`. When predicting outcomes, `np.zeros` and `np.ones` might be useful.
    - **S-Learner**. Training input: [X,T], training target: Y. Predict $Y_0$ and $Y_1$. Compute ITEs.
    - **IPSW**. You will need to compute *propensity score weights* with a classifier first. See the function inside the [ipsw.py](ipsw.py) file. Use it to get the weights. Training and prediction is similar to a) but you will need to pass the weights as sample weights to the fit function.
    - **X-Learner**. Train on X, T and Y data. Use the `effect` function to predict ITEs. See the docs [here](https://econml.azurewebsites.net/_autosummary/econml.metalearners.XLearner.html#econml.metalearners.XLearner).

Evaluation
1. Calculate $\epsilon_{ATE}$ and $\epsilon_{PEHE}$ metrics on the test set for all 3 models based on your predictions and true effects that can be accessed in the dataset.
2. Compare obtained metric values.
    - Which estimator performs the best with respect to $\epsilon_{ATE}$?
    - Which estimator performs the best with respect to $\epsilon_{PEHE}$?
    - Is the same estimator winning with respect to both metrics? Or are they two different estimators?

Model selection
1. Perform hyperparameter tuning using [GridSearchCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html#sklearn.model_selection.GridSearchCV) for one estimator of your choice. That is, from S-Learner, IPSW and X-Learner, pick one.
    - S-Learner/IPSW. This is the same as for any standard regressor. See examples on how to use GridSearchCV in sklearn's docs.
    - X-Learner. When creating the `XLearner` object, pass GridSearchCV objects instead of regressors. Example: `XLearner(models=GridSearchCV(...))`. For more information, see [link](https://econml.azurewebsites.net/spec/estimation/dml.html#usage-faqs) and search for "How do I select the hyperparameters of the first stage models?".
2. Obtain the metrics again for the tuned model.
3. Did hyperparameter tuning help improve the metrics?

Optional
- Experiment with other CATE estimators (see [here](https://econml.azurewebsites.net/reference.html#cate-estimators)).
- Try to improve predictions (decrease metrics).


## Dataset description

The Infant Health Development Program (IHDP) dataset was collected to investigate the effect of high-quality childcare and home visits on the future cognitive test score of low-birth-weight, premature infants. The dataset contains 25 features, including measurements about the child (e.g., child-birth weight, head circumference, weeks born preterm, birth order, first born, neonatal health index, sex...) and information about the mother at the time she gave birth (e.g., age, marital status, educational attainment, whether she worked) and her behaviours during the pregnancy (e.g., whether she smoked cigarettes, drank alcohol, took drugs...). These are background variables X. The treatment variable (t) indicates whether a family was part of the control (i.e., t = 0, no support was provided) or the treatment (i.e., t = 1, support was provided) group. The outcome column records the cognitive test score for the child. The dataset was introduced in [[1]](https://doi.org/10.1198/jcgs.2010.08162) based on a clinical trial [[2]](https://pubmed.ncbi.nlm.nih.gov/1538279/). We use a semi-synthetic version of the data, where the outcomes (both factual and counterfactual) are simulated (with some added random noise) based on real pre-treatment covariates. For this reason, the dataset also includes true (noiseless) individualised effects per each data unit, which are better suited for performance evaluation than the outcomes due to lack of noise. The use of counterfactuals/true effects is forbidden in the training stage (evaluation only).

You can find the dataset in the [ihdp](ihdp.csv) CSV file.

Column names explanation:
- x1-x25: background variables X
- t: treatment variable T
- yf: observed (factual) outcome Y
- ycf: counterfactual outcome (should be dropped)
- ite: true ITEs for evaluation purposes