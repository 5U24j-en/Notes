
Classification types
-  [[Binary Classification]]
-  [[Multi-class Classification]] 


**Classification :** 
		Identifying the category or class from the input features.
			Example:  Assuming that the number of hours ( input variable ) predicts if the student pass or fail. ( less than 3 hours fail, more than 5 hours pass )
			
![[Pasted image 20251122210049.png]]


Linear Regression -> $\hat{y}=\beta_0+\beta_1x_i$  

We need a categorical output instead of numerical $\hat{y}$ .

To convert numerical output into categories:
- Take a rule - based approach
-  Statistical approach

![[Pasted image 20251123105449.png]]

In Logistic Regression you are passing the linear regression output to the Sigmoid/Logistic regression function to get the categories of statistical significance.

### Sigmoid Function 

A Sigmoid function converts a real number between $-\infty$ to $\infty$   to a probability value of range 0 - 1.

$$\sigma= \frac{1}{1+e^{-x}}$$
$x$ = Input Value

In Logistic Regression we pass the linear regression line ( which gives us the "y") as input ( as we need to categorize the "y" predicted values ), and the out put we get is .

![[Pasted image 20251125155505.png]]

Default threshold can be 0.5

![[Pasted image 20251125155608.png]]

Output Probability :

$$p=\frac{1}{1+e^{-(\beta_0+\beta X)}}$$

$\beta_0+\beta X$  = Linear Regression  

### Loss function for Logistic Regression Model

$$L=p^{y} * (1-p)^{(1-y)}$$

L is the Likelihood of being correct

y is the actual value -- > 1 or 0
p is probability of the predicted output
We need to maximize this for all the data points

![[Pasted image 20251210163402.png]]


### Accuracy

$\frac{Correct \; Classified \; Classes}{Total \; number\;  of \; data \; Points}$


Example:
![[Pasted image 20251125174643.png]]

4 predictions are correct

Accuracy = 4/5 = 0.8

### Confusion Matrix

Suppose we have  a dataset with 100 patients, of which 5 patients have terminal illness.

![[Pasted image 20251125175306.png]]


Out of all patients with the terminal disease, how many of them has the model correctly diagnosed.

$$\frac{5}{5+3}=0.625 ---> Accuracy \ only \  60$$

![[Pasted image 20251125175607.png]]

Mis classifications Counts:

- True Positive ( TP ): Positive correctly as postive
-  True Negative ( TN ): Negative correctly as negative 
-  False Positive ( FP ): Negative incorrectly as Positive
-  False Negative ( FN ): Positive incorrectly as Negative

Example Terminal Illness:

![[Pasted image 20251125180415.png]]

### Precision:

Among Positives which are actually positive

$$Precision = \frac{TP}{TP+FP}$$

![[Pasted image 20251125180541.png]]

Using Example:
	5/5+1 = 0.83

Precision = 0.83

### Positive Recall or Sensitivity

Among actual positives how many are correctly predicted.

$$Sensitivity = \frac{TP}{TP+FN} = 0.624$$

### Negative Recall OR Specificity

Among actual Negatives. how many are correctly predicted

$$Specificity = \frac{TN}{TN+FP} = 0.916$$

### F-1 Score

![[Pasted image 20251125181045.png]]

### Identifying Cutoff

![[Pasted image 20251125183338.png]]


Multi class Classification

![[Pasted image 20251130192803.png]]


Precision Recall Curve

ROC   ( Receiver Operating Characteristic Curve )and AUC  ( Area Under the Curve)


![[Pasted image 20251212184838.png]]


- AUC helps us to compare ROC of multiple curves
- If AUC for 1 curve is greater than the other, then the 


![[Pasted image 20251212190753.png]]

![[Pasted image 20251212190811.png]]

- We can also use Precision instead of False Positive Rate
![[Pasted image 20251212190958.png]]


-  Conclusion
	- ROC curves makes it easy to identify the best threshold for making a decision
	- ![[Pasted image 20251212191203.png]]
	
	- The AUC helps us to identify which classification method is the best 
	- ![[Pasted image 20251212191221.png]]

## Core difference ( Standardization )

- `scaler.fit_transform(X_train)`
    
    - First **learns** the scaling parameters from `X_train` (e.g., mean and standard deviation for `StandardScaler`).
        
    - Then **applies** that scaling to `X_train` in one combined step.
        
    - Equivalent to `scaler.fit(X_train); scaler.transform(X_train)`.
        
- `scaler.transform(X_test)`
    
    - Only **applies** the scaling **already learned** from the previous `fit`/`fit_transform`.
        
    - It does **not** recompute means/std; it uses the parameters stored inside `scaler` from the training data.