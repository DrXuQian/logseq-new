- #Attention
- https://jaykmody.com/blog/attention-intuition/
- ## Word Vectors and Similarity
- For each token, we can encode that using a vector, and the [euclidean distance](https://en.wikipedia.org/wiki/Euclidean_distance) of two token would be smaller when the meaning of token are similar.
- ![image.png](../assets/image_1691475495787_0.png){:height 370, :width 706}
- ## Attention Scores using the Dot Product
- Similarity of two token:
	- ![image.png](../assets/image_1691475626618_0.png){:height 70, :width 177}
		- Where q is the token, and ki is the key we are quering.
- Attention Score:
	- ![image.png](../assets/image_1691475675646_0.png){:height 95, :width 246}
- Code:
	- ```python
	  import numpy as np
	  
	  def get_word_vector(word, d_k=8):
	      """Hypothetical mapping that returns a word vector of size
	      d_k for the given word. For demonstrative purposes, we initialize
	      this vector randomly, but in practice this would come from a learned
	      embedding or some kind of latent representation."""
	      return np.random.normal(size=(d_k,))
	  
	  def softmax(x):
	      # assumes x is a vector
	      return np.exp(x) / np.sum(np.exp(x))
	  
	  def attention(q, K, v):
	      # assumes q is a vector of shape (d_k)
	      # assumes K is a matrix of shape (n_k, d_k)
	      # assumes v is a vector of shape (n_k)
	      return softmax(q @ K.T) @ v
	  
	  def kv_lookup(query, keys, values):
	      return attention(
	          q = get_word_vector(query),
	          K = np.array([get_word_vector(key) for key in keys]),
	          v = values,
	      )
	  
	  # returns some float number
	  print(kv_lookup("fruit", ["apple", "banana", "chair"], [10, 5, 2]))
	  ```
- ## Scaled Dot Product Attention
- #### Scaling
- The dot product between our query and keys can get really large in magnitude if $$d_k$$ is large. This makes the output of softmax more *extreme*. For example, `softmax([3, 2, 1]) = [0.665, 0.244, 0.090]`, but with larger values `softmax([30, 20, 10]) = [9.99954600e-01, 4.53978686e-05, 2.06106005e-09]`. When training a neural network, this would mean the gradients would become really small which is undesirable. As a solution, we scale our pre-softmax scores by $$1/sqrt(d_k)$$ :
- ![image.png](../assets/image_1691476081322_0.png){:height 100, :width 429}
-