---

#  Datasets in Statistics and Cybersecurity

Datasets are fundamental for analyzing and modeling behaviors in cybersecurity. They form the basis for detecting anomalies, training predictive models, and evaluating intrusion detection systems [1].

## Types of Datasets

### 1. **Structured**

Data organized in tables with rows and columns, such as relational databases. Example: access logs, user tables [2].

### 2. **Unstructured**

Data without a fixed schema, such as text, images, or video. They require advanced processing techniques, like NLP or computer vision [3].

### 3. **Semi-Structured**

Data with partial structure, such as JSON or XML files. They contain labels or metadata that facilitate analysis [2].

## Example: Tabular Dataset from KDD Cup '99

The **KDD Cup '99** dataset is widely used for intrusion detection. It contains simulated network traffic information with 41 variables and various attack labels [6]. Here is a simplified tabular representation:

| Duration | Protocol | Service | SrcBytes | DstBytes | Label  |
| -------- | -------- | ------- | -------- | -------- | ------ |
| 0        | tcp      | http    | 181      | 5450     | normal |
| 0        | udp      | domain  | 105      | 146      | normal |
| 0        | tcp      | ftp     | 239      | 486      | attack |

This structure allows the application of statistical and machine learning techniques to identify anomalous behaviors.

## Dataset Management

Proper dataset management is crucial for reliable and replicable results. Main steps include:

* **Data cleaning**: removing missing, duplicate, or erroneous values.
* **Normalization and standardization**: aligning variable scales to avoid bias in models [4].
* **Anonymization and privacy**: protecting sensitive data, essential in cybersecurity [5].
* **Feature selection**: choosing the most relevant features to improve model performance and reduce computational complexity [2].

## Data Distribution Concepts

**Data distribution** describes how values of a variable or set of variables are spread. Understanding distribution is essential to:

* Choose appropriate statistical tests
* Identify outliers and anomalies
* Improve predictive modeling [4]

### Types of Distribution

* **Univariate**: examines one variable at a time, analyzing mean, median, mode, variance, skewness, and kurtosis [4].
* **Bivariate**: examines the relationship between two variables, using scatter plots, contingency tables, and correlations [4].
* **Multivariate**: involves multiple variables simultaneously, used in PCA, clustering, and complex predictive models [4].

### Other Relevant Concepts

* **Probability distributions**: Normal, Poisson, Binomial, useful for modeling events like access attempts or attacks [4].
* **Outliers**: extreme values that may indicate anomalies or intrusions [1][5].
* **Patterns and correlations**: identifying dependencies between variables can reveal abnormal behaviors or vulnerabilities [2][4].

## Final Considerations

Proper analysis of datasets and distributions is essential in cybersecurity for identifying threats and optimizing defense systems. The combined use of structured, unstructured, and semi-structured datasets, along with rigorous data management, allows the development of robust and reliable statistical and machine learning models [1][6].

## References

[1] Provost, F., & Fawcett, T. (2013). *Data Science for Business*. O'Reilly Media.

[2] Han, J., Pei, J., & Kamber, M. (2011). *Data Mining: Concepts and Techniques*. Elsevier.

[3] Jurafsky, D., & Martin, J. H. (2020). *Speech and Language Processing*. Pearson.

[4] Montgomery, D. C., & Runger, G. C. (2018). *Applied Statistics and Probability for Engineers*. Wiley.

[5] Goodfellow, I., Bengio, Y., & Courville, A. (2016). *Deep Learning*. MIT Press.

[6] Tavallaee, M., et al. (2009). *A detailed analysis of the KDD CUP 99 data set*. In *IEEE Symposium on Computational Intelligence for Security and Defense Applications*.

## Univariate and bivariate distribution on a dataset
Here is an example based on a simple database created with Access. Using two basic SQL queries, we can calculate both the univariate and bivariate distributions. For the univariate distribution, we consider the age variable, while for the bivariate distribution we examine age and Height.



