# Fantasy Premier league team optimisation

This is an attempt to optimise my fantasy football squad selection, picking an initial team and subsequent transfers. I've used data from https://github.com/vaastav/Fantasy-Premier-League/ which includes an 'xP' field, which isn't exactly what's required but is a good enough placeholder until the season starts with more accurate player predictions.

Player points prediction is probably the most technical element of a task like this. It would involve not only gathering the most common data but also searching for alternative data that could improve accuracy, such as betting odds for various markets or even something as stupid as the tweets of the player may improve accuracy. There are a number of available models currently out there, put together by people who have thought a lot more about the problem than I have, so initially this project is purely an optimisation problem based on the predicted points and any other factors useful for creating a team.

The optimisation is carried out using `pulp`. I'd considered converting the problem into `pytorch` to crunch the numbers on a GPU, but it's only taking 5 seconds or so to run over a CPU for 6 gameweeks which is fast enough for now.

### Single gameweek optimisation

Here's the optimised output for the first gameweek of the 2023-24 season, based on maximising the xP value within the constraints of the game. This gives total xP of 47.1 and uses all of the 100 budget. We can see that the bench players are simply the cheapest options as they are not important when only optimising a single week.

| name                      | pos   | team      |   price |   xP |   lineup |   captain |   vicecaptain |
|:--------------------------|:------|:----------|--------:|-----:|---------:|----------:|--------------:|
| Trent Alexander-Arnold    | DEF   | Liverpool |      80 |  4.5 |        1 |         0 |             0 |
| João Cancelo              | DEF   | Man City  |      60 |  4.3 |        1 |         0 |             0 |
| Kieran Trippier           | DEF   | Newcastle |      65 |  3.6 |        1 |         0 |             0 |
| Andrew Robertson          | DEF   | Liverpool |      65 |  3.6 |        1 |         0 |             0 |
| Virgil van Dijk           | DEF   | Liverpool |      60 |  3.4 |        1 |         0 |             0 |
| Callum Wilson             | FWD   | Newcastle |      80 |  3.1 |        1 |         0 |             0 |
| Ederson Santana de Moraes | GK    | Man City  |      55 |  5.5 |        1 |         1 |             0 |
| Kevin De Bruyne           | MID   | Man City  |     105 |  4.9 |        1 |         0 |             1 |
| Bruno Borges Fernandes    | MID   | Man Utd   |      85 |  3.4 |        1 |         0 |             0 |
| Bukayo Saka               | MID   | Arsenal   |      85 |  3.4 |        1 |         0 |             0 |
| Martin Ødegaard           | MID   | Arsenal   |      85 |  3.4 |        1 |         0 |             0 |
| Andi Zeqiri               | FWD   | Brighton  |      45 |  1.5 |        0 |         0 |             0 |
| Ellis Simms               | FWD   | Everton   |      45 |  0   |        0 |         0 |             0 |
| Mark Gillespie            | GK    | Newcastle |      40 |  1.5 |        0 |         0 |             0 |
| Tyrese Francois           | MID   | Fulham    |      45 |  1   |        0 |         0 |             0 |

### Multi gameweek optimisation

Below shows an example of the optimisation for the first 6 gameweeks of the 2023-24 season, which has a total expected points of 518. The xP is 45.4 for the first gameweek, less than the 47.1 as in the single week optimised team above. This is obviously as expected due to more consideration being made for subs that we may actually find benefit in using in order to not spend points on additional transfers.

#### First Gameweek Team

| name                  | pos   | team        |   price |   xP |   lineup |   captain |   vicecaptain |
|:----------------------|:------|:------------|--------:|-----:|---------:|----------:|--------------:|
| Kieran Trippier       | DEF   | Newcastle   |      65 |  3.6 |        1 |         0 |             0 |
| Andrew Robertson      | DEF   | Liverpool   |      65 |  3.6 |        1 |         0 |             0 |
| Pervis Estupiñán      | DEF   | Brighton    |      50 |  2.9 |        1 |         0 |             0 |
| Raphaël Varane        | DEF   | Man Utd     |      50 |  2.8 |        1 |         0 |             0 |
| Erling Haaland        | FWD   | Man City    |     140 |  5.5 |        1 |         1 |             0 |
| Julián Álvarez        | FWD   | Man City    |      65 |  3.7 |        1 |         0 |             0 |
| Alisson Ramses Becker | GK    | Liverpool   |      55 |  4.5 |        1 |         0 |             1 |
| Rodrigo Hernandez     | MID   | Man City    |      55 |  3.6 |        1 |         0 |             0 |
| Bukayo Saka           | MID   | Arsenal     |      85 |  3.4 |        1 |         0 |             0 |
| Son Heung-min         | MID   | Spurs       |      90 |  3   |        1 |         0 |             0 |
| Solly March           | MID   | Brighton    |      65 |  2.8 |        1 |         0 |             0 |
| Matty Cash            | DEF   | Aston Villa |      45 |  1.5 |        0 |         0 |             0 |
| Yoane Wissa           | FWD   | Brentford   |      60 |  2.1 |        0 |         0 |             0 |
| Alphonse Areola       | GK    | West Ham    |      40 |  1   |        0 |         0 |             0 |
| Bryan Mbeumo          | MID   | Brentford   |      65 |  2.3 |        0 |         0 |             0 |

#### Transfers

|   Gameweek | Transfers In         | Transfers Out    |
|-----------:|:---------------------|:-----------------|
|          2 | Cristian Romero      | Andrew Robertson |
|          3 | Destiny Udogie       | Raphaël Varane   |
|          4 | Darwin Núñez Ribeiro | Yoane Wissa      |
|          5 | Pedro Lomba Neto     | Bryan Mbeumo     |
|          6 | Anthony Gordon       | Solly March      |

Optimising based on the actual points the players scored leads the model to make far more transfers as the cost of 4 points is more than offset by the foresight of knowing the player will score more than 4 points. The selection over the first 6 gameweeks of the 2023-24 season, based on actual points scored, leads to a total of 695 points (177 more than previous) and takes 7 hits for an additional transfer.

### Gameweek 1 Picks

| name        | pos   | team   |   price |   xP |   lineup |   captain |   vicecaptain |
|:------------|:------|:-------|--------:|-----:|---------:|----------:|--------------:|
| Pedro Porro | DEF   | TOT    |     5.5 |  4.7 |        1 |         0 |             0 |
| Romero      | DEF   | TOT    |     5   |  4.4 |        1 |         0 |             0 |
| Gvardiol    | DEF   | MCI    |     6   |  4.3 |        1 |         0 |             0 |
| Watkins     | FWD   | AVL    |     9   |  5.2 |        1 |         0 |             0 |
| Bowen       | FWD   | WHM    |     8   |  5.2 |        1 |         0 |             0 |
| A.Becker    | GK    | LIV    |     5.5 |  3.9 |        1 |         0 |             0 |
| M.Salah     | MID   | LIV    |    14.5 |  7.5 |        1 |         1 |             0 |
| Palmer      | MID   | CHE    |    10.5 |  6.8 |        1 |         0 |             1 |
| Mitoma      | MID   | BRI    |     6.5 |  4.9 |        1 |         0 |             0 |
| McNeil      | MID   | EVE    |     6   |  4.1 |        1 |         0 |             0 |
| Tielemans   | MID   | AVL    |     6   |  4   |        1 |         0 |             0 |
| Senesi      | DEF   | BOU    |     4.5 |  2.3 |        0 |         0 |             0 |
| Toti        | DEF   | WOL    |     4.5 |  2.3 |        0 |         0 |             0 |
| Barnes      | FWD   | BUR    |     4.5 |  0.3 |        0 |         0 |             0 |
| Foderingham | GK    | WHM    |     4   |  0.1 |        0 |         0 |             0 |

### Gameweek 2 Picks

| name        | pos   | team   |   price |   xP |   lineup |   captain |   vicecaptain |
|:------------|:------|:-------|--------:|-----:|---------:|----------:|--------------:|
| Gvardiol    | DEF   | MCI    |     6   |  4.2 |        1 |         0 |             0 |
| Senesi      | DEF   | BOU    |     4.5 |  4.2 |        1 |         0 |             0 |
| Lacroix     | DEF   | CRY    |     5   |  4   |        1 |         0 |             0 |
| Watkins     | FWD   | AVL    |     9   |  5.1 |        1 |         0 |             0 |
| Bowen       | FWD   | WHM    |     8   |  4.4 |        1 |         0 |             0 |
| A.Becker    | GK    | LIV    |     5.5 |  3.4 |        1 |         0 |             0 |
| Palmer      | MID   | CHE    |    10.5 |  6.1 |        1 |         1 |             0 |
| M.Salah     | MID   | LIV    |    14.5 |  5.8 |        1 |         0 |             1 |
| Mitoma      | MID   | BRI    |     6.5 |  4.2 |        1 |         0 |             0 |
| McNeil      | MID   | EVE    |     6   |  4.2 |        1 |         0 |             0 |
| Tielemans   | MID   | AVL    |     6   |  3.9 |        1 |         0 |             0 |
| Toti        | DEF   | WOL    |     4.5 |  2.5 |        0 |         0 |             0 |
| Pedro Porro | DEF   | TOT    |     5.5 |  2.1 |        0 |         0 |             0 |
| Barnes      | FWD   | BUR    |     4.5 |  0.3 |        0 |         0 |             0 |
| Foderingham | GK    | WHM    |     4   |  0.1 |        0 |         0 |             0 |

### Gameweek 3 Picks

| name        | pos   | team   |   price |   xP |   lineup |   captain |   vicecaptain |
|:------------|:------|:-------|--------:|-----:|---------:|----------:|--------------:|
| Pedro Porro | DEF   | TOT    |     5.5 |  3.7 |        1 |         0 |             0 |
| Toti        | DEF   | WOL    |     4.5 |  3.7 |        1 |         0 |             0 |
| Virgil      | DEF   | LIV    |     6   |  3.5 |        1 |         0 |             0 |
| Watkins     | FWD   | AVL    |     9   |  5.5 |        1 |         0 |             1 |
| Bowen       | FWD   | WHM    |     8   |  4.3 |        1 |         0 |             0 |
| A.Becker    | GK    | LIV    |     5.5 |  3.6 |        1 |         0 |             0 |
| Palmer      | MID   | CHE    |    10.5 |  6.8 |        1 |         1 |             0 |
| M.Salah     | MID   | LIV    |    14.5 |  5.5 |        1 |         0 |             0 |
| Tielemans   | MID   | AVL    |     6   |  4.2 |        1 |         0 |             0 |
| Mitoma      | MID   | BRI    |     6.5 |  4   |        1 |         0 |             0 |
| McNeil      | MID   | EVE    |     6   |  3.9 |        1 |         0 |             0 |
| Lacroix     | DEF   | CRY    |     5   |  3.1 |        0 |         0 |             0 |
| Senesi      | DEF   | BOU    |     4.5 |  2.8 |        0 |         0 |             0 |
| Barnes      | FWD   | BUR    |     4.5 |  0.3 |        0 |         0 |             0 |
| Foderingham | GK    | WHM    |     4   |  0.1 |        0 |         0 |             0 |

### Gameweek 4 Picks

| name        | pos   | team   |   price |   xP |   lineup |   captain |   vicecaptain |
|:------------|:------|:-------|--------:|-----:|---------:|----------:|--------------:|
| Munoz       | DEF   | CRY    |     5.5 |  4.8 |        1 |         0 |             0 |
| Lacroix     | DEF   | CRY    |     5   |  4.5 |        1 |         0 |             0 |
| Virgil      | DEF   | LIV    |     6   |  4.4 |        1 |         0 |             0 |
| Bowen       | FWD   | WHM    |     8   |  4.7 |        1 |         0 |             0 |
| Watkins     | FWD   | AVL    |     9   |  4.6 |        1 |         0 |             0 |
| A.Becker    | GK    | LIV    |     5.5 |  4.2 |        1 |         0 |             0 |
| M.Salah     | MID   | LIV    |    14.5 |  7.7 |        1 |         1 |             0 |
| Palmer      | MID   | CHE    |    10.5 |  6.2 |        1 |         0 |             1 |
| Mitoma      | MID   | BRI    |     6.5 |  4.2 |        1 |         0 |             0 |
| McNeil      | MID   | EVE    |     6   |  4.1 |        1 |         0 |             0 |
| Tielemans   | MID   | AVL    |     6   |  3.8 |        1 |         0 |             0 |
| Senesi      | DEF   | BOU    |     4.5 |  3.4 |        0 |         0 |             0 |
| Toti        | DEF   | WOL    |     4.5 |  2.3 |        0 |         0 |             0 |
| Barnes      | FWD   | BUR    |     4.5 |  0.3 |        0 |         0 |             0 |
| Foderingham | GK    | WHM    |     4   |  0.1 |        0 |         0 |             0 |

### Gameweek 5 Picks

| name        | pos   | team          |   price |   xP |   lineup |   captain |   vicecaptain |
|:------------|:------|:--------------|--------:|-----:|---------:|----------:|--------------:|
| Virgil      | DEF   | LIV           |     6   |  4.5 |        1 |         0 |             0 |
| Milenkovic  | DEF   | Nott'm Forest |     5.5 |  3.8 |        1 |         0 |             0 |
| Toti        | DEF   | WOL           |     4.5 |  3.6 |        1 |         0 |             0 |
| Lacroix     | DEF   | CRY           |     5   |  3.5 |        1 |         0 |             0 |
| Watkins     | FWD   | AVL           |     9   |  5.8 |        1 |         0 |             1 |
| Bowen       | FWD   | WHM           |     8   |  4.8 |        1 |         0 |             0 |
| A.Becker    | GK    | LIV           |     5.5 |  4.3 |        1 |         0 |             0 |
| M.Salah     | MID   | LIV           |    14.5 |  7.1 |        1 |         1 |             0 |
| Palmer      | MID   | CHE           |    10.5 |  5.4 |        1 |         0 |             0 |
| Mitoma      | MID   | BRI           |     6.5 |  4.6 |        1 |         0 |             0 |
| Tielemans   | MID   | AVL           |     6   |  4.3 |        1 |         0 |             0 |
| Senesi      | DEF   | BOU           |     4.5 |  3.1 |        0 |         0 |             0 |
| Barnes      | FWD   | BUR           |     4.5 |  0.3 |        0 |         0 |             0 |
| Foderingham | GK    | WHM           |     4   |  0.1 |        0 |         0 |             0 |
| McNeil      | MID   | EVE           |     6   |  3.2 |        0 |         0 |             0 |

### Gameweek 6 Picks

| name        | pos   | team          |   price |   xP |   lineup |   captain |   vicecaptain |
|:------------|:------|:--------------|--------:|-----:|---------:|----------:|--------------:|
| Gvardiol    | DEF   | MCI           |     6   |  5.3 |        1 |         0 |             0 |
| Milenkovic  | DEF   | Nott'm Forest |     5.5 |  4.4 |        1 |         0 |             0 |
| Senesi      | DEF   | BOU           |     4.5 |  3.3 |        1 |         0 |             0 |
| Watkins     | FWD   | AVL           |     9   |  5.5 |        1 |         0 |             0 |
| Bowen       | FWD   | WHM           |     8   |  4.1 |        1 |         0 |             0 |
| A.Becker    | GK    | LIV           |     5.5 |  3.8 |        1 |         0 |             0 |
| Palmer      | MID   | CHE           |    10.5 |  6.6 |        1 |         1 |             0 |
| M.Salah     | MID   | LIV           |    14.5 |  6.2 |        1 |         0 |             1 |
| McNeil      | MID   | EVE           |     6   |  4.4 |        1 |         0 |             0 |
| Tielemans   | MID   | AVL           |     6   |  4.1 |        1 |         0 |             0 |
| Mitoma      | MID   | BRI           |     6.5 |  3.7 |        1 |         0 |             0 |
| Lacroix     | DEF   | CRY           |     5   |  3   |        0 |         0 |             0 |
| Toti        | DEF   | WOL           |     4.5 |  2.5 |        0 |         0 |             0 |
| Barnes      | FWD   | BUR           |     4.5 |  0.3 |        0 |         0 |             0 |
| Foderingham | GK    | WHM           |     4   |  0.1 |        0 |         0 |             0 |

### Transfers Summary

|   Gameweek | Transfers In   | Transfers Out   |
|-----------:|:---------------|:----------------|
|          2 | Lacroix        | Romero          |
|          3 | Virgil         | Gvardiol        |
|          4 | Munoz          | Pedro Porro     |
|          5 | Milenkovic     | Munoz           |
|          6 | Gvardiol       | Virgil          |