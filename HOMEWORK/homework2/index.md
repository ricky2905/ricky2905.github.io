# Datasets in Statistics and Cybersecurity

Datasets are fundamental for analyzing and modeling behaviors in cybersecurity. They form the basis for detecting anomalies, training predictive models, and evaluating intrusion detection systems [1].

---

## Types of Datasets

### 1. **Structured**
Data organized in tables with rows and columns, such as relational databases.  
**Example:** access logs, user tables [2].

### 2. **Unstructured**
Data without a fixed schema, such as text, images, or video. They require advanced processing techniques, like NLP or computer vision [3].

### 3. **Semi-Structured**
Data with partial structure, such as JSON or XML files. They contain labels or metadata that facilitate analysis [2].

---

## Example: Tabular Dataset from KDD Cup '99

The **KDD Cup '99** dataset is widely used for intrusion detection. It contains simulated network traffic information with 41 variables and various attack labels [6].  
Here is a simplified tabular representation:

| Duration | Protocol | Service | SrcBytes | DstBytes | Label  |
| -------- | -------- | ------- | -------- | -------- | ------ |
| 0        | tcp      | http    | 181      | 5450     | normal |
| 0        | udp      | domain  | 105      | 146      | normal |
| 0        | tcp      | ftp     | 239      | 486      | attack |

This structure allows the application of statistical and machine learning techniques to identify anomalous behaviors.

---

## Dataset Management

Proper dataset management is crucial for reliable and replicable results. Main steps include:

* **Data cleaning**: removing missing, duplicate, or erroneous values.
* **Normalization and standardization**: aligning variable scales to avoid bias in models [4].
* **Anonymization and privacy**: protecting sensitive data, essential in cybersecurity [5].
* **Feature selection**: choosing the most relevant features to improve model performance and reduce computational complexity [2].

---

## Data Distribution Concepts

**Data distribution** describes how values of a variable or set of variables are spread. Understanding distribution is essential to:

* Choose appropriate statistical tests
* Identify outliers and anomalies
* Improve predictive modeling [4]

### Types of Distribution

* **Univariate** — examines one variable at a time, analyzing mean, median, mode, variance, skewness, and kurtosis [4].  
* **Bivariate** — examines the relationship between two variables, using scatter plots, contingency tables, and correlations [4].  
* **Multivariate** — involves multiple variables simultaneously, used in PCA, clustering, and complex predictive models [4].

### Other Relevant Concepts

* **Probability distributions**: Normal, Poisson, Binomial — useful for modeling events like access attempts or attacks [4].  
* **Outliers**: extreme values that may indicate anomalies or intrusions [1][5].  
* **Patterns and correlations**: identifying dependencies between variables can reveal abnormal behaviors or vulnerabilities [2][4].

---

## Final Considerations

Proper analysis of datasets and distributions is essential in cybersecurity for identifying threats and optimizing defense systems.  
The combined use of structured, unstructured, and semi-structured datasets, along with rigorous data management, allows the development of robust and reliable statistical and machine learning models [1][6].

---

## References

[1] Provost, F., & Fawcett, T. (2013). *Data Science for Business*. O'Reilly Media.  
[2] Han, J., Pei, J., & Kamber, M. (2011). *Data Mining: Concepts and Techniques*. Elsevier.  
[3] Jurafsky, D., & Martin, J. H. (2020). *Speech and Language Processing*. Pearson.  
[4] Montgomery, D. C., & Runger, G. C. (2018). *Applied Statistics and Probability for Engineers*. Wiley.  
[5] Goodfellow, I., Bengio, Y., & Courville, A. (2016). *Deep Learning*. MIT Press.  
[6] Tavallaee, M., et al. (2009). *A detailed analysis of the KDD CUP 99 data set*. In *IEEE Symposium on Computational Intelligence for Security and Defense Applications*.

---

## Univariate and Bivariate Distribution on a Dataset

Here is an example based on a simple database created with Access. Using two basic SQL queries, we can calculate both the univariate and bivariate distributions.  
For the univariate distribution, we consider the **Age** variable, while for the bivariate distribution we examine **Age** and **Height**.

![Dati](/assets/images/dati.png "Esempio di dati tabellari")

### Univariate Distribution on Age
![Eta](/assets/images/Dist_univariata.png "Distribuzione Univariata")

**SQL Code:**
![EtaSQL](/assets/images/Dist_univariata_sql.png "Distribuzione Univariata SQL")

### Bivariate Distribution on Age and Height
![EtaAltezza](/assets/images/Dist_bivariata.png "Distribuzione Bivariata")

**SQL Code:**
![EtaAltezzaSQL](/assets/images/Dist_bivariata_sql.png "Distribuzione Bivariata SQL")

---

## Using Distribution to Break a Caesar Cipher

This section introduces a simple web-based tool and an explanation for working with the **Caesar cipher (shift = 3)**.  
The tool allows users to:

* Encrypt text
* Attempt decryption by trying all 25 possible shifts (brute-force)
* Automatically estimate the correct shift using letter frequency analysis (chi-squared test)

The frequency-based approach provides a statistical guess for the most likely shift (for longer texts), while brute-force remains reliable for shorter ones.

---

## Main Goals of the Tool

### Caesar Cipher Generation
* Encrypts plaintext with a user-selected shift.
* Supports accent mapping before encryption.

### Attempted Decryption of All Shifts
* Performs brute-force over all 26 possible Caesar cipher shifts.

### Scoring and Ranking of Candidates
Calculates scores based on:
* Hamming distance (letter order)
* Chi-squared statistic (χ²) for frequencies
* Presence of common Italian words (wordScore)
* Presence of common bigrams (bigramScore)

### Results Display
* Shows all candidate decryptions in a sortable table.
* Highlights the top N candidates.
* Allows selection of a candidate to view a preview and detailed scores.

### User Utilities
* Copy ciphertext to clipboard.
* Automatically handle accents and capitalization.

---

<iframe src="ricky2905.github.io/tools/caesar_tool.html" width="100%" height="400px"></iframe>
---

# Overview of the Caesar Cipher with Statistical Analysis

The Caesar cipher is a **monoalphabetic substitution cipher** that shifts each letter of the plaintext by a fixed number of positions.  
For example, with a shift of 3:

```
Plaintext:  ABCDEFGHIJKLMNOPQRSTUVWXYZ
Ciphertext: DEFGHIJKLMNOPQRSTUVWXYZABC
```

### Encryption/Decryption Function

```javascript
function caesarShift(text, shift){
  return text.split('').map(ch => {
    const code = ch.charCodeAt(0);
    if(code >= 65 && code <= 90) return String.fromCharCode(((code - 65 + shift) % 26) + 65);
    if(code >= 97 && code <= 122) return String.fromCharCode(((code - 97 + shift) % 26) + 97);
    return ch;
  }).join('');
}
```

### Brute-force Decryption

Since there are only 25 possible shifts, the Caesar cipher can easily be broken:

```javascript
for(let shift = 1; shift < 26; shift++){
  results.push({shift, text: caesarShift(ciphertext, shift)});
}
```

### Statistical / Distribution-based Decryption

The advanced part of the code uses **letter and bigram frequency distributions** to estimate the most probable shift automatically.

---

## Metrics Explained

| Metric | Description | Weight |
| ------- | ------------ | ------- |
| **Word%** | Fraction of valid Italian words | 0.45 |
| **Hamming** | Letter rank similarity | 0.25 |
| **Chi²** | Statistical deviation from expected frequencies | 0.15 |
| **Bigram%** | Common bigram presence | 0.15 |

---

## Cryptographic Insights

The Caesar cipher is weak because of its **small keyspace** (25 shifts) and **predictable frequency patterns**.  
Statistical analysis quickly reveals the shift.

---

## Conclusion

This implementation bridges **cryptography** and **statistics**, showing how data distributions can reveal hidden information.  
The **combined score metric** elegantly integrates multiple statistical indicators to automatically prioritize the most probable plaintext.

![Script](/assets/images/script.png "Code")
