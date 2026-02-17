# STAT: Precision, Recall, Sensitivity, Specificity, FPR, and FDR.

> Reference:     
> [1] https://scikit-learn.org/stable/auto_examples/model_selection/plot_precision_recall.html.     
> [2] https://cran.r-project.org/web/packages/precrec/vignettes/introduction.html.  


Here is a confusion matrix at a certain threshold $\tau$:  

|           |          | Actual    |          |
|-----------|----------|-----------|----------|
| Predicted |          | Positive  | Negative |
|           | Positive | [$T_p$]   | [$F_n$]  |
|           | Negative | [$F_p$]   | [$T_n$]  |

## Introduce Precision and Recall 

- **Precision-recall** is a useful measure of success of prediction when the **classes are very imbalanced**.    

- **Precision (Positive Predictive Value)** is a measure of **result relevancy**; 
  - defined as the number of true positive ($T_p$) over the number of predicted positive ($T_p + F_p$). 

```math
\text{Precision} = \frac{T_p}{T_p + F_p}
```

- **Recall (Sensitivity, True Positive Rate)** is a measure of **how many truly relevant results are returned**;
  - defined as the number of true positive ($T_p$) over the number of actual positive ($T_p + F_n$).
  - A concept related to "**power**", i.e., 1 - Type II error ($1-\beta$). 

```math
\text{Recall} = \frac{T_p}{T_p + F_n}
```

- These two quantities are related to the $F_1$ **score** defined as the *harmonic mean of precision and recall*.
```math
F_1 = 2 \times \frac{\text{Precision} \times \text{Recall} }{\text{Precision} + \text{Recall}}
```

- The precision may not decrease with recall. 

- The definition of precision shows that lowering the threshold of a classifier may increase the denominator. 
If the threshold was previously set too high and now is lowered down, the new results may all be true positives, which will increase precision. 
If the threshold was about right or too low, further lowering the threshold will introduce false positives, decreasing precision.

- The definition of recall shows that its denominator, the number of actual positive, does not depend on the classifier threshold. 
This means that lowering the classifier threshold may increase recall by increasing the number of results returned. 
It is also possible that lowering the threshold may leave recall unchanged, while the precision fluctuates. 

- Precision-recall curves are typically used in binary classification to study the output of a classifier.  



## Draw the Precision-Recall Curve and Compute AU-PRC in R

The r-package we use is called `precrec`.   

Here is an instruction of this package: [2] https://cran.r-project.org/web/packages/precrec/vignettes/introduction.html.  

<!-- The `evalmod` function calculates ROC and Precision-Recall curves and returns an S3 object. 

```{r, eval=F}
library(precrec}

# Load a test dataset
data(P10N10)

# Calculate ROC and Precision-Recall curves
sscurves <- evalmod(scores = P10N10$scores, labels = P10N10$labels)
```

The `precrec` package proves nine S3 generics for the S3 object created by the `evalmod` function.

 -->
 
 Please study the website carefully (hhhh)



## In Addition: Introduce Sensitivity, Specificity

- **Sensitivity (True Positive Rate, Recall)**: 
  - the ability of a test to correctly identify individuals with the disease. 

```math
\text{Sensitivity} = \frac{T_p}{T_p + F_n},  
```

- **Specificity (True Negative Rate)**: 
  - How well does the model identify actual negatives? 

```math
\text{Specificity} = \frac{T_n}{T_n + F_p} 
```

- Sensitivity & Specificity: often used together in medical diagnostics where identifying true positives (sensitivity) and true negatives (specificity) are both important for patient health. 


## False Positive Rate and False Discovery Rate

- **False Positive Rate (FPR)**:  
  - Definition: $F_p / (F_p + T_n)$, which equals $1-\text{specificity(TNR)}$. 
  - Focuses on the "true negative" population.
  - "Of all true negatives, how many were wrongly flagged?"
  - A concept related to **Type I error**.

- **False Discovery Rate (FDR)**:
  - Definition: $F_p / (F_p + T_p)$, which equals $1 - \text{precision}$. 
  - Focuses on the "positive result" population.  
  - "Of all rejected nulls, how many are actually null?" 

