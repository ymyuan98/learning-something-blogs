# STAT: Precision-Recall and its Application in R

> Reference:     
> [1] https://scikit-learn.org/stable/auto_examples/model_selection/plot_precision_recall.html.     
> [2] https://cran.r-project.org/web/packages/precrec/vignettes/introduction.html.  


## Introduce Precision and Recall 

Before we introduce the precision and recall, here is a confusion matrix at a certain threshold $\tau$:  
|        |          | Predicted |          |
|--------|----------|-----------|----------|
| Actual |          | Negative  | Positive |
|        | Negative | [$T_n$]   | [$F_p$]  |
|        | Positive | [$F_n$]   | [$T_p$]  |

- Precision-recall is a useful measure of success of prediction when the classes are very imbalanced.    

- **Precision** is a measure of **result relevancy**, while **recall** is a measure of **how many truly relevant results are returned**. 
Precision is defined as the number of true positive ($T_p$) over the number of predicted positive ($T_p + F_p$).
Recall is defined as the number of true positive ($T_p$) over the number of actual positive ($T_p + F_n$). 
```math
\text{Precision} = \frac{T_p}{T_p + F_p},  
\text{Recall} = \frac{T_p}{T_p + F_n}
```

- These two quantities are related to the $F_1$ score defined as the harmonic mean of precision and recall.
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

```{r}
library(precrec}

# Load a test dataset
data(P10N10)

# Calculate ROC and Precision-Recall curves
sscurves <- evalmod(scores = P10N10$scores, labels = P10N10$labels)
```

The `precrec` package proves nine S3 generics for the S3 object created by the `evalmod` function.

 -->
 
 Please study the website carefully (hhhh)
