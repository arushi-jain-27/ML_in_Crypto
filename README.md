# ML_in_Crypto

Problem Summary

Consider that Alice and Bob share a secret key s. If Alice wants to identify herself to Bob, she needs to share her secret key s with Bob so that Bob can verify that she is indeed Alice and not an imposter. However, by directly communicating the secret key s, an adversary may sniff the key and can later re-use it. Hence, this method of direct communication is not safe.

To solve this, Bob can instead ask Alice a set of questions that Alice can answer using her secret key s (HB Protocol). The questions asked by Bob and the answers given by Alice can still be overheard by the adversary, however, now the adversary will have to solve a system of linear equations to recover the key. This may sound slightly smarter but the key is still recoverable if the set of questions and answers is known (by solving a linear system).

To tackle this, Alice adds a random noise with probability p to her answer instead of providing the exact answer. The adversary will then not be able to solve this problem as it is conjectured to be NP-Hard. This is called the Learning Parity with Noise (LPN) problem.

We want to recover the secret key s, given Bob’s input questions and Alice’s noisy output answers. 



Datasets and Algorithm

We will generate synthetic data for our problem using a fixed error probability (p < 0.5) and secret key (s) of length n. Based on n, we will construct the
training set by simulating the question matrix A with dimensions m x n where m is given by: 





We will also simulate a noise vector e that follows a Bernoulli distribution with error probability p. 



Finally, we will generate our answer vector using the following equation:





Here, we compute modulo 2 since A*s is XOR product and A*s + e is XOR addition.



The test set will be an identity matrix of dimension n to recover the predicted secret key s_hat. 



To check our secret key s_hat, we will compute how much our predicted answer differs from the true answer:





We will also compute an error threshold τ to check if our error is less than the expected threshold:





We will assume that the key is recovered if .



Techniques



We will train an ML model given an input matrix A and output vector b. Since it is a classification problem, we will use Logistic Regression with Robustness, CART, OCT, OCT-H, Random Forest Classifier, and XGBoost Classifier. We will perform cross-validation for hyperparameter tuning for sparsity, minbucket, cp, depth, and the number of trees/ estimators. We will try to use Stable Regression to come up with the best training-validation split to make our model stable.



For each secret key s, we will train our models x number of times (~100-200) to see how many times each model is able to recover the key correctly. We will try to generate exhaustive combinations of keys for different key lengths and try to understand which combinations are easier to recover (intuitively key of type 0n or 1n  should be easier to recover).



Challenges

Recovering Keys for Higher Dimensions: Based on our initial analysis, we have seen that a basic model can recover keys up to n = 8 in most cases. However, the major challenge lies in recovering keys for higher dimensions which will require us to train strong cross-validated models.

Assessment Against Baselines/ Benchmarks: We need to implement a non-ML algorithm based on the extensive work done on this problem to compare our performance. There are several approaches that solve LPN given time, memory, and sample limitations using either Combinatorial or Decoding algorithms. Most of the basic approaches we came across (such as Gauss Elimination) use matrix inverse calculations on the Galois field, so we may be limited by the libraries that allow this. In the worst case, we can always implement a brute-force algorithm that searches the entire key space just for comparison purposes.













[1] R. Kübler, Time-Memory Trade-Offs for the Learning Parity with Noise Problem (2018), Dissertation (Ruhr University Bochum)
