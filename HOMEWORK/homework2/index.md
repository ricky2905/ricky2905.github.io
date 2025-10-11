# What is a Dataset

A **dataset** is a structured collection of data, usually organized so that each record (row) corresponds to a single observation and each column corresponds to a variable or attribute describing the observation.  
In research and analysis contexts, a dataset provides the empirical basis for inference, modeling, and visualization [1].

### Formal (concise) definition
> “A collection of data, usually represented in tabular form, where rows correspond to individual entities or observations and columns correspond to variables or attributes describing those entities.” [1]

---

## Key Components

- **Observations / records / instances** — each row is one case (e.g., one log entry or one patient).  
- **Variables / features** — columns that can be quantitative (numeric) or qualitative (categorical).  
- **Values** — the individual cells in the table.  
- **Metadata** — data about the data (field descriptions, units, coding schemes, provenance).  

When documenting a dataset, include a short **data dictionary** listing each field, its type, and allowed values — this is essential for reproducible analysis and downstream statistical tests [6].

---

## Dataset Types & Data Quality

### By Structure
- **Structured** — relational tables, CSVs. *(Example: firewall logs)*  
- **Semi-structured** — JSON, XML, log files with fields *(common in telemetry)*.  
- **Unstructured** — free text, images, audio.

### By Measurement
- **Quantitative** — numeric variables (continuous or discrete).  
- **Qualitative** — categorical variables (nominal or ordinal).

### Quality Checklist (recommended)
- Total number of records `n`  
- Percent missing per field  
- Data formats *(timestamps in ISO 8601 recommended)*  
- Known filters / sampling used during collection  
- Unit tests or validation scripts for field ranges / regexes  

Quality metadata is required to validate assumptions for many statistical tests (for example, contingency-table χ² requires adequate counts per cell) [6].

---

## CSV Example & Aggregation

### Example: `logs_sample.csv`

```csv
timestamp,src_ip,dst_ip,src_port,dst_port,protocol,action,threat_type,bytes,response_time
2025-10-01T08:12:23Z,192.0.2.10,198.51.100.5,52344,443,TCP,ACCEPT,none,1240,23
2025-10-01T08:12:27Z,203.0.113.9,198.51.100.5,33421,22,TCP,DROP,ssh-bruteforce,0,-
2025-10-01T08:13:01Z,198.51.100.20,198.51.100.5,445,49152,TCP,DROP,ddos,0,-
```

### Aggregated View (example frequency table)
| Attack Type           | Observed Frequency |
| ---------------------: | -----------------: |
| Malware                | 120 |
| Phishing               | 80 |
| DDoS                   | 50 |
| Known Vulnerabilities  | 40 |

---

## Distributions: Core Concepts

A **distribution** describes how values of a variable (or set of variables) are spread through the data.  
Understanding distributional shape is essential for inference, anomaly detection, and model selection.

### Classification by Dimensionality
- **Univariate distribution** — distribution of a single variable (histogram, PMF/PDF).  
- **Bivariate distribution** — joint distribution of two variables.  
- **Multivariate distribution** — three or more variables.

---

## SQL Examples

### Univariate Distribution (SQL)

```sql
SELECT
  age,
  COUNT(*) AS frequency
FROM
  People
GROUP BY
  age
ORDER BY
  age;
```

### Bivariate Distribution (SQL)

```sql
SELECT
  age,
  gender,
  COUNT(*) AS frequency
FROM
  People
GROUP BY
  age, gender
ORDER BY
  age, gender;
```

---

## Using Distributions to Attack the Caesar Cipher (Statistical Approach)

Educational demo: Caesar encryption, brute-force decryption, and automatic estimator using χ² comparison against a reference frequency table.

### JavaScript Functions

```js
// --- Reference frequencies (example: English A..Z) ---
const freqEn = {
  A:8.17,B:1.49,C:2.78,D:4.25,E:12.70,F:2.23,
  G:2.02,H:6.09,I:6.97,J:0.15,K:0.77,L:4.03,
  M:2.41,N:6.75,O:7.51,P:1.93,Q:0.10,R:5.99,
  S:6.33,T:9.06,U:2.76,V:0.98,W:2.36,X:0.15,Y:1.97,Z:0.07
};

// Caesar shift function and chi-squared decoder...
```

---

## 🔹 Widget interattivo: Caesar demo

Qui viene incluso il widget HTML interattivo del progetto:

```html
<!-- Inclusione del widget interattivo -->
<iframe src="assets/script/widget-cesaer.hmtl" title="Caesar cipher widget" width="100%" height="450" style="border:1px solid #ddd;border-radius:8px;"></iframe>
```

---

## References

1. [Dataset (Wikipedia)](https://en.wikipedia.org/wiki/Dataset)  
2. Statistical analysis and χ² test fundamentals.  
3. Classical cipher cryptanalysis references.  
4. Frequency analysis methods in cryptography.  
5. Cross-language effects in statistical decoding.  
6. Data quality and reproducibility guidelines.

---

## Immagine finale

![Script](assets/images/script.png)
