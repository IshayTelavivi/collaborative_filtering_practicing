# collaborative_filtering_practicing  

Practice the concept of collaborative filtering, both in explicit and implicit modes, since they use different concepts, algorithms and Python packages.

---

## Explicit Mode

I used a dataset of song rating:

<img width="240" height="180" alt="image" src="https://github.com/user-attachments/assets/541aedac-b8ac-4522-98f1-5ae495bd426c" />


So I needed to predict the ratings of users/songs based on the available data. 

Notes: 
- The chosen metric was RMSE between the actual and prediction rating.
- I used SVD from `surprise` package.
- The matrix was super sparsy (99.99%), not enough signal to learn meaningful user embeddings. Hence, the number of latent factors was 5, relatively small.
- Bottom line, despite of the sparcity, there was some learning, as seen in the chart, howerver with some regression to the mean: <img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/9cc4605d-6b4c-4f55-922a-b5ee28b9badd" />
- RSME of ~1.3, compaerd to ~1.9 of random prediction.
---

## Implicit Mode

Here I used a dataset of usage of online system. The purpose was to predict the nature of the system usage (duraiton in minutes and actions):

<img width="500" height="180" alt="image" src="https://github.com/user-attachments/assets/6df81e3a-7f64-42dd-8d58-2f8b78d27a38" />

After the EDA, I decided to use a combinaiton of both `duration` and `density` as the "item" dimension (I called it "comb" for combination), where density is the number of actions per minute.

Notes:
- I used ALS from `implicit` library (AlternatingLeastSquares).
- I had to round both the `duration` and `density` to make a reasonably dense matrix.
- I took the approach of hierarchical collaborative filtering (as in category-item), where the duraiton is the higher level and the density is a sub-level. This means that we use two martices, one for diration anf one for density, and we use λ as a proportion parameter:  `Final Score = λ · (User-Duration MF score) + (1-λ) · (User-Density MF score)`.
- Since I am building out confidence on signals, a linear signal may be too extreme. To get diminishing returns on certainty, I used log-dampened frequency: `w = 1 + α * log(1 + count)` for representing the confidence.
- The precision recall and NDCG were very colse because in the test set each customer had very little samples.
- Overall, the scores I got were almost double than the popularity score:
  <img width="1489" height="490" alt="image" src="https://github.com/user-attachments/assets/aab241bb-9bca-43d6-82b5-e1e1a0cce825" />

