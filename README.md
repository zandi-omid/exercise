# Ex-06: Classifying Spam Emails

## Objective:

Apply and evaluate different classification models to predict high-risk credit individuals using financial traits, focusing on understanding model performance through various evaluation metrics.

**Prerequisites:** Ensure you have Python, Jupyter Notebook, and the required libraries (**`pandas`**, **`numpy`**, **`scikit-learn`**, **`matplotlib`**, **`seaborn`**, **`mord`**) installed. The dataset **`spam.csv`** should be available in the **`data`** directory.

## Dataset:

The data this week comes from Vincent Arel-Bundock's Rdatasets package(<https://vincentarelbundock.github.io/Rdatasets/index.html>).

> Rdatasets is a collection of 2246 datasets which were originally distributed alongside the statistical software environment R and some of its add-on packages. The goal is to make these data more broadly accessible for teaching and statistical software development.

We're working with the [spam email](https://vincentarelbundock.github.io/Rdatasets/doc/DAAG/spam7.html) dataset. This is a subset of the [spam e-mail database](https://search.r-project.org/CRAN/refmans/kernlab/html/spam.html).

This is a dataset collected at Hewlett-Packard Labs by Mark Hopkins, Erik Reeber, George Forman, and Jaap Suermondt and shared with the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/94/spambase). The dataset classifies 4601 e-mails as spam or non-spam, with additional variables indicating the frequency of certain words and characters in the e-mail.

### Metadata

| Variable  | Class     | Description                                                              |
|-----------|-----------|--------------------------------------------------------------------------|
| `crl.tot` | double    | Total length of uninterrupted sequences of capitals                      |
| `dollar`  | double    | Occurrences of the dollar sign, as percent of total number of characters |
| `bang`    | double    | Occurrences of `!`, as percent of total number of characters             |
| `money`   | double    | Occurrences of `money`, as percent of total number of characters         |
| `n000`    | double    | Occurrences of the string `000`, as percent of total number of words     |
| `make`    | double    | Occurrences of `make`, as a percent of total number of words             |
| `yesno`   | character | Outcome variable, a factor with levels `n` not spam, `y` spam            |

(Source: [TidyTuesday](https://github.com/rfordatascience/tidytuesday/blob/master/data/2023/2023-08-15/readme.md))

## Question:

Can we predict whether an email is Spam or not using decision tree classification?

## **Step 1: Setup and Data Preprocessing**

-   Start by importing the necessary libraries and load the **`spam.csv`** dataset.

-   Preprocess the data by encoding categorical variables, defining features and target, and splitting the data into training and testing sets. Finally, apply PCA to reduce dimensionality.

    ```{python}
    # Import libraries
    import pandas as pd
    import numpy as np
    from sklearn.preprocessing import LabelEncoder
    from sklearn.decomposition import PCA
    from sklearn.model_selection import train_test_split

    # Load the dataset
    spam = pd.read_csv("data/spam.csv")

    # Encode categorical variables
    categorical_columns = spam.select_dtypes(include = ['object', 'category']).columns.tolist()
    label_encoders = {col: LabelEncoder() for col in categorical_columns}
    for col in categorical_columns:
        spam[col] = label_encoders[col].fit_transform(spam[col])

    # Define features and target
    X = spam.drop('yesno', axis = 1)
    y = spam['yesno']

    # Split the data
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 42)

    # Reduce dimensionality
    pca = PCA(n_components = 2)
    X_train_pca = pca.fit_transform(X_train)
    X_test_pca = pca.transform(X_test)
    ```

## **Step 2: Model Training and Decision Boundary Visualization**

-   Train a Decision Tree classifier on the PCA-transformed training data.

-   Implement and use the **`decisionplot`** function to visualize the decision boundary of your trained model.

```{python}
#| eval: false
from sklearn.tree import DecisionTreeClassifier
import matplotlib.pyplot as plt

# Train Decision Tree
dtree = DecisionTreeClassifier()
dtree.fit(X_train_pca, y_train)

# Implement the decisionplot function (as provided in the lecture content)
# Add the decisionplot function here

# Visualize decision boundary
decisionplot(dtree, pd.DataFrame(X_train_pca, columns = ['PC1', 'PC2']), y_train)

```

## **Step 3: Model Evaluation**

-   Evaluate your model using accuracy, precision, recall, F1 score, and AUC-ROC metrics.

```{python}
#| eval: false
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, roc_auc_score, roc_curve, auc
from sklearn.preprocessing import label_binarize

# Predictions
predictions = dtree.predict(X_test_pca)

# Evaluate metrics
accuracy = accuracy_score(y_test, predictions)
precision = precision_score(y_test, predictions, average = 'weighted')
recall = recall_score(y_test, predictions, average = 'weighted')
f1 = f1_score(y_test, predictions, average = 'weighted')

# Display results
print(f"Accuracy: {accuracy:.2f}")
print(f"Precision: {precision:.2f}")
print(f"Recall: {recall:.2f}")
print(f"F1 Score: {f1:.2f}")

# For AUC-ROC, binarize the output and calculate AUC-ROC for each class
# Add the necessary code for AUC-ROC calculation here (refer to lecture content)
```

## **Assignment:**

-   Implement the missing parts of the code: the **`decisionplot`** function and AUC-ROC calculation.

-   Discuss the results among your peers. Consider the following:

    -   Which metric is most informative for this problem and why?

    -   How does the decision boundary visualization help in understanding the model's performance?

    -   Reflect on the impact of PCA on model performance and decision boundary.

## **Submission:**

-   Submit your Jupyter Notebook via GitHub with implemented code and a brief summary of your discussion findings regarding model evaluation and the impact of PCA.
