### elo

Computes the new ratings between two or more opponents using the [Elo rating system](https://en.wikipedia.org/wiki/Elo_rating_system). It takes an array
of pre-ratings and returns an array containing post-ratings.
The array should be ordered from best performer to worst performer (winner -> loser).

Use the exponent `**` operator and math operators to compute the expected score (chance of winning)
of each opponent and compute the new rating for each. Loop through the ratings, using each permutation to compute the post-Elo rating for each player in a pairwise fashion. Omit the second argument to use the default K-factor of 32, or supply a custom K-factor value. For details on the third argument, see the last example.

```js
const elo = ([...ratings], kFactor = 32, selfRating) => {
  const [a, b] = ratings;
  const expectedScore = (self, opponent) => 1 / (1 + 10 ** ((opponent - self) / 400));
  const newRating = (rating, i) =>
    (selfRating || rating) + kFactor * (i - expectedScore(i ? a : b, i ? b : a));
  if (ratings.length === 2) {
    return [newRating(a, 1), newRating(b, 0)];
  } else {
    for (let i = 0; i < ratings.length; i++) {
      let j = i;
      while (j < ratings.length - 1) {
        [ratings[i], ratings[j + 1]] = elo([ratings[i], ratings[j + 1]], kFactor);
        j++;
      }
    }
  }
  return ratings;
};
```

```js
// Standard 1v1s
elo([1200, 1200]); // [1216, 1184]
elo([1200, 1200], 64); // [1232, 1168]
// 4 player FFA, all same rank
elo([1200, 1200, 1200, 1200]).map(Math.round); // [1246, 1215, 1185, 1154]
/*
For teams, each rating can adjusted based on own team's average rating vs. 
average rating of opposing team, with the score being added to their
own individual rating by supplying it as the third argument.
*/
```
