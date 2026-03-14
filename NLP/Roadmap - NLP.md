
Natural Language Processing = Dataset -> Text


### **Text Pre-processing**:  

- Text Data which is not clean. ( in form of paragraphs )
- Converting sentences/words into formats like **Vectors**.
- Vectors are like numerical representation of words which will help understand relationship between words.
- Techniques - Bag of words, TF-IDF, stopwords, Lemmetization, etc


![[Pasted image 20260119162314.png]]




## **Text Pre-Processing 1** 


![[Pasted image 20260119164113.png]]
	
- ### Tokenization

	-  Converting Sentences into Words.
	-  Each word is a Token.
	-  Example : 
		- "Hey buddy I want to go to your house" converted into list of words
		- Result: `[ Hey, buddy, I , want, to , go , to , your , house]`

- ### Stopwords

	-  Removing Irrelevant words based on the Use case.
	-  Example : `[ Hey, buddy, I , want, to , go , to , your , house]` 
		-  We can remove words like -  " **to , of** "  - in a use case like Spam check.
	- We can create our own Stop - words


- ### Stemming

	- Historical / History --> Histi
	- Converting the words to their **Base word Stem**
	-  We are getting the Root word/ Base form.
	-  Some words can be impact-full using Stemming. 
		- Eg. goes, going, gone --> go
	- Advantages:
		- Stemming is really Fast for huge Datasets
	-  Disadvantage:
		- Word might lose its meaning
	- Use Cases:
		- Spam Classification
		- Review Classification


- ### Lemmatization

	-  Finding the Root word of a word by comparing with a Dictionary
	- More accurate and meaningful results than Stemming
	- Example
		- history/historical --> history
	-  Advantages
		-  Meaningful and More accurate
	- Disadvantages
		- It is slow
	- Use Cases:
		- Text summarizing
		- Language Translation
		- Chatbot



## Text Pre-processing 2 - Converting words to vectors

- One Hot Encoding
- Bag Of Words => Ngrams
-  TF - IDF ( Term Frequency - Inverse Document Frequency )
-  Word2Vec

Library - NLTK

### Terminologies

-  **Corpus** ( Paragraph )
-  **Documents**
-  **Vocabulary**
-  **Words**



![[Pasted image 20260120173442.png]]

- The entire Data-points ( D1, D3, D3, D4 ) - is the **CORPUS** ( Combining all will become a paragraph )
-  A sentence ( D1 ) is a **DOCUMENT**
-  Number of unique words in the dataset **VOCABULARY**
- **Words** are individual words in the text.


![[Pasted image 20260120174326.png]]


- ### One Hot Encoding

- ![[Pasted image 20260120180434.png]]
	- One code Encoded Format
	- Advantages:
		- Simple to Implement
		-  Intuitive
	- Disadvantages:
		- Sparse Matrix ( Very Huge Matrix)
		- Out of Vocabulary
		-  Semantic Meaning between words is not Captured
		-  If extra words are present in Testing Dataset, this will fail



- ### Bag Of Words

	-   Case: We have 3 Sentences
	- ![[Pasted image 20260122161123.png]]
	- We use stop words and remove the Highlights words, as they are Not needed for use cases like sentiment Analysis.
	- Get the Vocabulary and treat each vocabulary as a Feature
	- Order the feature as per their Frequency
	- ![[Pasted image 20260122161340.png]]
	- ![[Pasted image 20260122161512.png]]
	-  In Bag of Words we Have Binary Bag of Words, where frequency is not counted.
	- ![[Pasted image 20260122161553.png]]
	-  Advantages 
		-  Simple and Intuitive

	- Disadvantages
		- Sparsity ( Lot of Features )
		- Out of Vocabulary
		- Ordering of Words changed
		- Semantic Meaning is Lost


To capture the Semantic Meaning we use **Ngrams**.

- ### Ngrams

	- Bigrams, Trigrams, .. N grams
	-  The Combination of the Single Word Features
	- ![[Pasted image 20260122162946.png]]
	- ![[Pasted image 20260122163205.png]]
	- ![[Pasted image 20260122163232.png]]
	- 






