# Chapter 2. Fancy Tricks with Simple Numbers
1. The first sanity check for numeric data is whether the **magnitude** matters
2. Next, consider the scale of the features.
   - Normalize if the model is smooth function of input features
   - Logical functions, step functions are not sensitive to input feature scale.
   - Consider the distribution of numeric features. e.g. it matters if e Gaussian error assumption

## Dealing with Counts
1. **Binarization**, convert counts to 0 and 1
2. **Quantization or Binning**
   - Fixed-width binning
   - Quantile binning
3. **Log Transformation**. The log transform is a powerful tool for dealing with positive numbers with a heavy-tailed distribution.  It compresses the long tail in the high end of the distribution into a shorter tail, and expands the low end into a longer head.
4. **Power Transforms: Generalization of the Log Transform**: are e variance-stabilizing transformations.
   - A generalization of both the square root transform and the log transform is known as the Box-Cox transform (only works when the data is positive):
$$x\hat = \left \{ 
\begin{array}{ll}
\frac{x^\lambda}{\lambda} \\
ln(x) & if \lambda = 0 \\
\end{array}
\right.
$$
Setting λ to be less than 1 compresses the higher values, and setting λ higher than 1 has the opposite effect.
Box-cox transforms are trying to convert the distribution of input features close to normal distribution. 
```Python
from scipy.stats import boxcox
```

## Feature Scaling or Normalization
1. Min-Max Scaling:
$$\hat x=\frac{x-min(x)}{max(x)-min(x)}$$
2. Standardization (Variance Scaling:
$$\hat x=\frac{x-mean(x)}{sqrt(var(x))}$$
**Note: DON'T CENTER SPARSE DATA**: Min-Max Scaling and Standardization can turn a sparse feature vector where most values are zero into a dense one. This could create a huge computational burden for the classifier.
3. $l^2$ Normalization:
$$\hat x=\frac{x}{||x||_2}$$
$$\sqrt{x_1^2+\dot + x_m^2}$$
**Note: feature scaling not change the shape of distribution**
4. Interaction Features
5. Feature selection:
   - Filtering: e.g. compute the correlation or mutual information between each feature and the response variable, and filter out the features that fall below a threshold
   - Wrapper methods: expensive. Computes a quality score of a proposed subset for features
   - Embedded methods: part of training process, $l^1$ regularizer, balance between computational expense and quality of results.

# Chapter 3. Text Data: Flattening, Filtering, and Chunking
## Bag-of-X: Turning Natural Text into Flat Vectors
1. **Bag-of-Words**: convert docs int vector counts. Flat, not contain the original textual structures.
2. **Bag-of-n-Grams**
```Python
 from sklearn.feature_extraction.text import CountVectorizer
```

## Filtering for Cleaner Features
1. **Stopwords**: contains apostrophes, lowercase
2. **Frequency-Based Filtering**:
   - Frequent words: combine frequency-based filtering with a stopword list.
   - Rare words: garbage bin. The garbage bin need to be collected as post-processing step.
**Note**: featurization and modeling tricks differs for long / short text.
3. **Stemming**: may not be always used, e.g.  The words “new” and “news” have very different meanings, but both would be stemmed to “new.”

## Atoms of Meaning: From Words to n-Grams to Phrases
1. **Parsing and Tokenization**: e how to handle the markup, the headers and footers, or the uninteresting sections of the log.
docs->sentences->tokens
2. **Collocation Extraction for Phrase Detection**: collocations (e.g. idioms) are more meaningful thant he sum of their parts.
   - predefine list, look through the corpus, expensive to compute
   - Frequency-based methods: e.g. common pairs
   - Hypothesis testing for collocation extraction: . For a given pair of words, the method tests two hypotheses on the observed dataset. Hypothesis 1 (the null hypothesis) says that word 1 appears independently from word 2.
>The algorithm for detecting common phrases through likelihood ratio test analysis proceeds as follows:
>>1. Compute occurrence probabilities for all singleton words: P(w).
>>2. Compute conditional pairwise word occurrence probabilities for all unique bigrams: P(w | w ).
>>3. Compute the likelihood ratio log λ for all unique bigrams.
>>4. Sort the bigrams based on their likelihood ratio.
>>5. Take the bigrams with the smallest likelihood ratio values as features.
3. **Chunking and part-of-speech tagging**: For longer phrase detection. Examine the token’s neighborhood to look for part-of-speech groupings, or “chunks”.
```Python
import spacy
# preload the language model
nlp = spacy.load('en')
doc_df = review_df['text'].apply(nlp)
for doc in doc_df[4]:
	print([doc.text, doc.pos_, doc.tag_])
```

# Chapter 4. The Effects of Feature Scaling: From Bag-of-Words to Tf-Idf
## Tf-Idf : A Simple Twist on Bag-of-Words
1. **Tf-Idf**:
   - bow(w, d) = # times word w appears in document d
   - tf-idf(w, d) = bow(w, d) * N / (# documents in which word w appears)
   Alternatively,
   - tf-idf(w, d) = bow(w, d) * log (N / # documents in which word w appears)
2. **Creating a Classification Dataset**: downsample the larger class (restaurants) to be roughly the same size as the smaller class.
3. **Scaling Bag-of-Words with Tf-Idf Transformation**: Tf-idf and $ℓ^2$ normalization are both column operations \(column scaling\) on the data matrix. The solution space is characterized by the column space and the null space of the data matrix. The quality of the trained linear classifier directly depends upon the null space and the column space of the data matrix. The null pace contains “novel” data points that cannot be formulated as linear combinations of existing data; a large null space could be problematic. Column scaling operations do NOT affect the column space and null space much.
Rank-deficient row space and column space lead to the model being overly provisioned for the problem.


# Chapter 5 Categorical Variables: Counting Eggs in the Age of Robotic Chickens
## Encoding Categorical Variables
Categorical data: nonordinal data

### One-Hot Encoding
>sklearn.preprocess.OneHotEncoder / pandas.get_dummies
- One-hot encoding has degree of freedom equal to number of features k. So there is linear dependent for the model, which result in the training linear model will not be unique.
- Intercpt represents global mean, linear coefficients represent how much one category differs from global mean.
- Pros: Redundent. Cons: Missing value can be encoded as all-zero vector

### Dummy Encoding
- Degree of freedom = k-1, Reference category is represented by a vector of all zeros.
- Intercpt represents the mean value of the response variable y for the reference category, linear coefficients represent how much one category differs from reference category.
- Pros: Not redundent. Cons: Cannot handle missing value easily

### Effect Encoding
- Degree of freedom = k-1, Reference category is represented by a vector of all -1's.
- Intercpt represents the global mean (called main effect), linear coefficients represent how much one category differs from global mean.  In effect coding, no single feature (coeffcient) represents the reference category, so the effect of the reference category needs to be separately computed as the negative sum of the coefficients of all other categories.
- Pros: Redundent. Cons: all -1's is a dense vector, expensive in storage and computation

## Dealing with Large Categorical Variables
1. Do nothing fancy with the encoding. Use a simple model that is cheap to train.
2. Compress the features. There are two choices:
   a. Feature hashing, popular with linear models
   b. Bin counting, popular with linear models as well as trees

### Feature Hashing
>sklearn.feature_extraction import FeatureHasher
- Collision: multiple numbers may get mapped to the same
- Uniform hash function: ensures that roughly the same number of numbers are mapped into each of the m bins
- The size of the hash table m can be selected based on acceptable errors. 
- Can be used for models that involve the inner product of feature vectors and coefficients, such as linear models and kernel methods. Perform well for spam filter but bad for targeted advertising.
- Features are not interpretable after hashing

### Bin Counting
- Rather than use raw value of categorical variable, use conditional probability of target under that value.
- Encode features to a single features with a real value between 0 and 1.
- Odds ratio and log odds ratio for bin counting. Odds is to evaluate 'How much more likely is it for Y to be true when X is true?' 
$$odds ratio = \frac{P(Y=1|X=1)/P(Y=1|X=1)}{P(Y=1|X=0)/P(Y=1|X=0)}$$
- Space complexity: O(k), k is # of unique category variables
- For rare category: 
   - use special bin if the count of a category is less than threshold
   - counting-min sketch: rare or frequent alike are mapped through multiple hash func with an output range m << k. Refer to figure 5-4.
- Guarding against data leakage (higher potential for leakage):
   - strict separation between training, validation, and test data
   - add a small random noise with distribution $Laplace(0, 1$ is sufficient to cover up any potential leakage from single data points
- Counts without bounds: normalise data or use log transform