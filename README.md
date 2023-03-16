### March Madness Predictions

This repo hosts a March Madness model's predictions of a hypothetical matchup between each team in the tournaments.

The baseline model is a probabilistic model that is fit to all regular season games since 2000. We formulate a game score model as

```python 
s = offense[team1] - defense[team2]) * scaler + base
score = Normal(mu=s, sigma=score_variance[team1])
```

and we update our model with the observed score of each game. 

We can then sample from the likley values of `offense[team1]`, `defense[team2]`, `scaler`, `base`, and `score_variance[team1]` in order to find the distribution of point totals we would expect team 1 to score in a matchup against team 2.

We can then make the (bad) assumption that the two distributions aren't dependent and we can just subtract the distributions and take the `cdf(0)` in order to find the likelihood that team 1 scores more points.

While this is a simple formulation, more complicated models tended to result in less consistent parameters or worse predictive power.

To check the quality of this metric, we can look at its calibration over all *tournament* games since 2000. Note the model has not seen any tournament games before, so this should be a fair exercise. Here is the calibration curve for the Men's tournament:

![base model 1](https://user-images.githubusercontent.com/7809188/225518499-1e5ac0cf-d027-467e-aaa4-332c18da21c1.png)

The predictions are surprisingly well calibrated for coming from only regular season games, but they're off in a few ranges (note: the symmetry is because every game is in the dataset flipping team1 and team2). We can solve this by trying to calibrate our predictions to tournament outcomes. When we do this, we get a very smooth curve, but still with areas of poor calibration.

![calibration 1](https://user-images.githubusercontent.com/7809188/225518498-8ffc05a2-856e-4b9f-89c1-b63eb2084d65.png)

To try get the best of both worlds, we can take the mean of the two values over most of the range and keep the original value in the high-confidence ranges - it's hard to calibrate to a small dataset, so it's unsurprising the raw datasets do better at the extremes. The combined predictions are better than either alone:

![combined 1](https://user-images.githubusercontent.com/7809188/225518496-c3236ea3-f0c1-48af-b156-c40d1efc6b1b.png)


The above charts are all for the NCAAM data, but the same approximate dynamic applies for the NCAAW tournament: there are ranges where the calibrated values are better and areas where the model is better, but the combination results in a well-calibrated set of predictions

![ncaaw 1](https://user-images.githubusercontent.com/7809188/225518495-2bdc2917-3057-4ad3-8d78-602d8107f560.png)

The output predictions are housed in this repository under `predictions/`

[predictions](/predictions/)