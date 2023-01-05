## Steam recommender system using implicitly inferred ratings
### How it works
This is just another recommender system. In fact, the main piece of work takes place before the recommender system is even initialised. In the data preprocessing segment, a dataset with **users**, **game name**, and **hours played** is imported. The idea was to convert this dataset into a dataset with **users**, **games** (or more generally **items**), and **ratings** which the recommender systems generally work with. This is why I used the **hours played** feature in order to deduce the **implicit rating** of a particular user for a particular game.

#### Implicit ratings
The implicit ratings are calculated using a formula that consists of two parts:
- calculating the `joy_coefficient`
- feeding the `joy_coefficient` to an `activation` function.


##### Joy coefficient
Joy coefficient of user `u` for the game `g`:
```
joy_coefficient_u_g = (hours_played_u_g - average_hours_played_g) / average_hours_played_g
```

##### Activation
The main idea of this part is to convert a continous value of the `joy_coefficient` into a value that ranges from 0 to 5.

A perfect function for that is the [Sigmoid](https://en.wikipedia.org/wiki/Sigmoid_function#:~:text=A%20sigmoid%20function%20is%20a%20bounded%2C%20differentiable%2C%20real%20function%20that,refer%20to%20the%20same%20object.) function, whose value approaches `1` as `x` approaches infinity, and also its value approaches 0 as `x` approaches negative infinity - it has a range of `(0, 1)`. By multiplying the Sigmoid by `5`, a function with a range of `(0, 5)` is obtained.

So finally, the rating of user `u` for a game `g` is obtained as:
```
rating_u_g = 5 * Sigmoid(joy_coefficient_u_g)
```

##### Example
From the dataset, a user with id `98649241` has played a game called `Warframe` for `83` hours.
The average playtime for `Warframe` is `31.96` hours.

Calculating the `joy_coefficient`:
```
joy_coefficient = (83 - 31.96) / 31.96 = 1.596
```

The `rating` is then:
```
rating = 5 * Sigmoid(1.596) = 4.158
```

The user is playing the game more than average, and as a consequence the rating is quite high.
This behaviour can be understood more clearly by taking a look at the plot below:
https://drive.google.com/file/d/12krBswoap1c35m9Pp8pZfqxb1gWg5xIV/view?usp=sharing
![Sigmoid Image](https://drive.google.com/uc?id=12krBswoap1c35m9Pp8pZfqxb1gWg5xIV)

In the above illustration you can also see that if the `joy_coefficient` is negative, the ratings will be in the range of `(0, 2.5)`, and if the `joy_coefficient` is positive, the rating will be in the range of `(2.5, 5)`.

### Benefits of implicit ratings
- The system is not dependent on direct user feedback (ratings)
- Bias is generally decreased
- More ratings since the system is instantly aware of each users playtime for each game that they have bought, whereas with direct/explictit ratings the system is only aware of the user's rating for a game once they decide to actually rate it. Most of the users have played a lot of games, but have only left reviews for a handful.
