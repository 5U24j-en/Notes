When Decision tree classifies into categories its a Classification Tree

Reference: https://www.youtube.com/watch?v=_L39rN6gz7Y
### Data :

This data tells us if someone loves popcorn , if they love soda , their Age and The predictor value loves cool as ice(CAI).
![[Pasted image 20251207160133.png]]


- ### First we Identify the Root Node:

	-  The first question we ask is if Someone who like Popcorn likes CAI or not ( Yes OR No Tree )
	-  We count the Total Values and group them
	- If they like Popcorn the count goes to the leaf on the left
	- If they don't like the Popcorn they go to the leaf on the right
	- ![[Pasted image 20251207161407.png]]
	-  Repeat this step for Love Soda.
	- ![[Pasted image 20251207161533.png]]
	-  ![[Pasted image 20251207161755.png]] ==This type of Result is called Impure==
	- We need to Quantify Impurity to identify the category ( Column ) with the lowest Impure Value which will be the Root Node
	- The Reason is, because the Pure Results means better categorization of the predictions as there are no mixed output.
	- Example
	- ![[Pasted image 20251207162152.png]]
	-  To calculate Impurity , there is Gini Impurity, Entropy, Information Gain , etc
	- Gini Impurity
	- ![[Pasted image 20251207162418.png]]
	- ![[Pasted image 20251207162510.png]]
	- ![[Pasted image 20251207162602.png]]
	- To calculate the Total Gini Impurities for the leaves we take the weighted average.
	- ![[Pasted image 20251207162739.png]]
	- Similarly for Loves Soda
	- ![[Pasted image 20251207162824.png]]
	- For Continuous Data Like Age we need to take cumulative values and categories as people less than Age ( of each cumulative ) True or False, liking the CAI or not.
	- Cumulative
	- ![[Pasted image 20251207163249.png]]
	- Then we Calculate the Gini Impurity values for each cumulative value by grouping less than.
	- Example in first iteration the Age < 9.5 , true or false will be the root.
	- ![[Pasted image 20251207163602.png]]
	- Calculate Gini Impurity
	- Gini Impurity for leaf on left
	- ![[Pasted image 20251207163650.png]]
	- Gini Impurity for right and Total weighted Average of both Impurites
	- ![[Pasted image 20251207164130.png]]
	- Follow the Above steps for all the remaining values.
	- ![[Pasted image 20251207164210.png]]
	- Find the Age group which has the lowest Impurity Value
	- Now that we have found out the Gini Impurity values of all the columns , we need to select the Feature ( column ) with lowest Gini Value as Root Tree
	- ![[Pasted image 20251207164524.png]]
	-  Since Loves Soda has the lowest Impurity, we keep it as the Root Node
	- ![[Pasted image 20251207164609.png]]

- ### Now we create Decision Tree
	-  We select Loves Soda as the Root Node
	- ![[Pasted image 20251207165252.png]]
	- The Left Node have people who love Soda
	- This Node is Impure
	- We will now reduce the Impurity by Splitting the people that Love Soda based on Loves Popcorn or Age.
	- First lets see Popcorn
	- ![[Pasted image 20251207165558.png]]
	- We calculate the Total Gini Impurity
	- Now we Take the Age
	- We use the same cumulative method as before but only with Data /Group who loves Soda
	- ![[Pasted image 20251207165707.png]]
	- The lowest Impurity is Age less than 12.5
	- ![[Pasted image 20251207165800.png]]
	- Since Age>12.5 has the lowest Impurity 0, we use that Age as the Node
	- ![[Pasted image 20251207165826.png]]
	- ![[Pasted image 20251207165946.png]]
	- No There is No reason to split the Data as all the last Nodes are Pure , Hence they are Leaves
	- ![[Pasted image 20251207170037.png]]

- ### Output Values for each Leaves
	- In general The output is the category which has the most number of values
	- Majority of people are in Does Not like CAI, hence the category/Output Value
	- ![[Pasted image 20251207170326.png]]


We need to Prune the Data, set limits on the leaf, we need to do Cross Validation and prevent over fitting by Regularization 

