 
- A relationship between Output Variables and Input Variables

$$
y=mx+b
$$
Equation of the Line

In Machine Learning :

$$y=\beta_1 x+\beta_0 $$

$$
\beta_1 = \text{Slope of the line}$$

$$\beta_0 = \text{The Intercept OR Constant OR bias}$$

$$x = \text{The Input OR The Independent Variable}$$

$$y = \text{The predicted Value or Output of the Model}$$

- There can be Multiple Dimensions / Input Variables like x1,x2, x3 ....

$$
y=\beta_1 x_1+\beta_2x_2+\beta_3x_3+....+\beta_0
$$

## 2 - D Data.

-  A simple Data table which shows the number of time taken for a muffin to be made.
- Example - 1 muffin took 10 min, 2 muffin took 15 min etc.
-  x is and Independent Variable. Number of muffin does not depend on Time taken to make as that is independent decision.
-  Table below
![[Pasted image 20251112223641.png]]

Plot of the Data : 
![[Pasted image 20251112223711.png]]


- Using Linear Regression to Model this relationship.

**Regression Line:**
$$y=\beta_1 x+\beta_0 $$

- **y  :-**  Estimated Time take / Predicted Time Taken
- $x_1$  :- Number of Muffins 

The regression Line will the best fit of all the data Points

#### Steps to find Slope and Intercept: ( Hypothesis Function )

-  Calculate Mean of x and y points:
	$$
	\overline{x}= \frac{1}{n}\sum_{i=1}^{n} x_i
	$$
	$$
	\overline{y}= \frac{1}{n}\sum_{i=1}^{n} y_i
	$$
- Slope of line $\beta_1$
$$ \beta_1 = \frac{\sum_{i=1}^n (x_i - \overline{x})(y_i - \overline{y})}{\sum_{i=1}^n (x_i - \overline{x})^2} $$

- Intercept of line:
		$$
			\beta_0 = \overline{y} - \beta_1 \overline{x}
		$$

Final Equation of Line is :

$$ y= 2.12 * x + 2.15
$$
**NOTE:**  But this is not the actual Regression Line.

![[Pasted image 20251112230607.png]]

The error is the difference between the values from the regression line and the Actual Data Point observed.
$$ e_i \text{  Is the residual OR the ERROR}
$$
$$ e_i = y_{actual} - y_{line}
$$

![[Pasted image 20251112232401.png]]

**Hypothesis Function** - The first Line we create

$$y=\beta_1 x+\beta_0 $$

Cost Function - Is the sum of of all the errors


**Cost Function OR Loss Function:**

- We square the difference of all errors and add them

A **loss function** measures how wrong the model is **for one training example**.

$$Loss = (y-\hat{y})^2 $$

![[Pasted image 20251112234528.png]]
The sum of the square function should be the minimum to find the actual $\beta_1$ and $\beta_0$, and hence find the most accurate Regression Line.

- SSE is the **Cost Function**. ( n = number of data points )
	$$SSE\text{(Sum of Squared Error)} = \frac{1}{n}\sum_{i=1}^{n} e_i^2  $$
- Aggregate measure of the Error.

- We can write this SSE in the below format
$$J(\theta_0, \theta_1) = \frac{1}{2m} \sum_{i=1}^{m} \left( h_\theta(x^{(i)}) - y^{(i)} \right)^2$$
- We add $1/2$ because when we tale derivative of the the Equation

**Reason:** The least Value provides the most accurate line closest to each actual data point.

- The $\beta_1$ and $\beta_0$  coefficients can be found by various methods  like matrix decomposition to gradient decent, we use python sklearn Libraries.
- After finding the coefficients, we get
$$\hat{y}= 2.60 + 1.97x_1
$$
- Intercept $\beta_0$ = 2.60 is the Minimum wait time no matter how many Muffins you order

![[Pasted image 20251113000318.png]]

Ordinary Least Square (OLS)
3-D Datapoints
## Assumptions of Linear Regression

-  Prerequisites for Linear Regression
	-  The Dataset should be Linear
	![[Pasted image 20251114204815.png]]
		-  The predictors and variable on which we are predicting should have linear relationship.
		-  Example - Population growth over time. **Exponential relationship**
	-  Independence of Data Points
		-  The columns(Data variables) does not influence each other
		![[Pasted image 20251114205503.png]]
		- Above table, houseID, square footage, etc
		
	-  Homoscedasticity of Errors
		- Errors have Constant variance
		![[Pasted image 20251114205819.png]]

	- Normality of Errors
		- P(Error) follow a Gaussian distribution
		
		![[Pasted image 20251114210049.png]]

	-  No Multicollinearity

		-  The independent/input Should not have correlation between them.
		![[Pasted image 20251114210548.png]]

## Interpretation of Coefficients

Linear Regression:
	$$y=\beta_1 x_1+\beta_2x_2+\beta_3x_3+....+\beta_0$$

$\beta_0$  - ( Intercept ) : If there is no input, "y" has a value.

$\beta_n$ - ( Coefficients ) : measures the relationship between the change in "x" and change in "y".

For multiple Coefficients, for unit change in $x_1$ indicates a $\beta_1$ predicted change in "y" if all other predictors are constant.

Example: 
		Sales = $\beta_0$ + $\beta_1$ * Advertising + $\beta_2$ * Discount + $\beta_3$ * Footfall ,
			then $\beta_3$ would indicate how much sales would increase if Footfall increase by 1 unit, if Advertising and Discount is constant.

+Positive Coefficient - Direct Relationship ( Upward line )
-Negative Coefficient - Inverse Relation ( Downward line )
Larger Coefficient - Larger deviation/More strong relationship
Smaller Coefficient - Smaller Slope

Example: 
		Cost is a negative relationship as cost increases less can be the sales. ( Not always )


## Handling Categorical Predictors/ Input ( "$x_n$")

Sample Data:

![[Pasted image 20251114213936.png]]

Each Level/Category can be represented as a variable input ($x_n$).

![[Pasted image 20251114214213.png]]

***No coaching*** can be represented as default.

## Evaluating the Coefficient | ERRORS


ML Model -> Mathematical Equation

![[Pasted image 20251116211410.png]]


-  **Baseline or Naive Model**

	-  A baseline model is mean of all the "y" ( predicted value )points

	![[Pasted image 20251116212053.png]]


- Total Sum of Squares ( TSS ):

	- Error of the Naive Model.
	![[Pasted image 20251116212918.png]]

$$TSS=\sum_{i=1}^{n}(y_i - \bar{y})^2$$
		$\bar{y}$ = mean of all y ( predicted values ) points.
		

- **Residual Sum of Squares ( RSS ):**

	- Error of the Linear Model 
	![[Pasted image 20251116213323.png]]
	
$$RSS = \sum_{i=1}^{n}(y_i - \hat{y})^2$$

**NOTE:**   RSS should always be very less than TSS.

			RSS <<< TSS

	Reason: Mean Line is the baseline Model.


- **Explained Sum of Square ( ESS ):**

		ESS = TSS - RSS


- $R^2$ - A metric to quantify how well the model fit the data Points :

	 $R_2$ explains how well the regression predictions approximate the actual data points

	 It is the ratio of ESS to the TSS.

$$R^2 = \frac{ESS}{TSS} = \frac{(TSS - RSS)}{ESS} = 1 - \frac{RSS}{TSS}$$

![[Pasted image 20251116215330.png]]

- Adjusted  $R^2$ = $R_{adj}^2$    
	- $R^2$ always increases with the number of predictors ( $x_1$, $x_2$, $x_3$, $x_4$, ....$x_p$)
	-  Adjusted $R^2$ penalizes irrelevant predictors

		 $$R_{adj}^2 = 1 - \frac{(1 - R^2)(n-1)}{n-p-1}$$  
	 - $n$ = number of records / observation
	 - $p$ = Number of predictors ( or number of "x")

NOTE:  If we take a large sample where n>>>p, then $$R^2\approx R_{adj}^2$$

## Predictive Metrics

- ### Mean Squared Error (MSE)
- ### Root Mean Squared Error (RMSE)
- ### Mean Absolute Error ( MAE )
- ### Mean Absolute Percentage Error (MAPE)


	**MSE ( Mean Squared Errors ):**
	$$MSE = \frac{1}{n}\sum_{i=1}^{n}e_i^2$$
		where ,$$e_i = y_i - \hat{y}$$
	
	**RMSE ( Root Mean Squared Error ):**
	$$RMSE = \sqrt{\frac{1}{n}\sum_{i=1}^{n}e_i^2}$$
	**MAE ( Mean Absolute Error ) :**
	$$MAE = = \frac{1}{n}\sum_{i=1}^{n}|e_i|$$
	MAPE (Mean absolute Percentage Error ):
	$$MAPE = 100 \times \frac{1}{n}\sum_{i=1}^{n}\frac{|e_i|}{|y_i|}$$


