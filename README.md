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

#### Gameweek 1 Picks

| name                 | pos   | team           |   price |   xP |   lineup |   captain |   vicecaptain |
|:---------------------|:------|:---------------|--------:|-----:|---------:|----------:|--------------:|
| Raphaël Varane       | DEF   | Man Utd        |      50 |   14 |        1 |         1 |             0 |
| Tim Ream             | DEF   | Fulham         |      45 |    7 |        1 |         0 |             0 |
| Cristian Romero      | DEF   | Spurs          |      45 |    7 |        1 |         0 |             0 |
| Erling Haaland       | FWD   | Man City       |     140 |   13 |        1 |         0 |             1 |
| Odsonne Edouard      | FWD   | Crystal Palace |      55 |    7 |        1 |         0 |             0 |
| Bernd Leno           | GK    | Fulham         |      45 |   12 |        1 |         0 |             0 |
| Rodrigo Hernandez    | MID   | Man City       |      55 |   13 |        1 |         0 |             0 |
| Solly March          | MID   | Brighton       |      65 |    9 |        1 |         0 |             0 |
| Jarrod Bowen         | MID   | West Ham       |      70 |    9 |        1 |         0 |             0 |
| Bryan Mbeumo         | MID   | Brentford      |      65 |    7 |        1 |         0 |             0 |
| Mohamed Salah        | MID   | Liverpool      |     125 |    5 |        1 |         0 |             0 |
| Malo Gusto           | DEF   | Chelsea        |      40 |    1 |        0 |         0 |             0 |
| Matty Cash           | DEF   | Aston Villa    |      45 |   -1 |        0 |         0 |             0 |
| Julián Álvarez       | FWD   | Man City       |      65 |    5 |        0 |         0 |             0 |
| Norberto Murara Neto | GK    | Bournemouth    |      45 |    2 |        0 |         0 |             0 |

#### Gameweek 2 Picks

| name                 | pos   | team           |   price |   xP |   lineup |   captain |   vicecaptain |
|:---------------------|:------|:---------------|--------:|-----:|---------:|----------:|--------------:|
| Cristian Romero      | DEF   | Spurs          |      45 |    6 |        1 |         0 |             0 |
| Matty Cash           | DEF   | Aston Villa    |      45 |    6 |        1 |         0 |             0 |
| Destiny Udogie       | DEF   | Spurs          |      45 |    5 |        1 |         0 |             0 |
| Julián Álvarez       | FWD   | Man City       |      65 |    7 |        1 |         0 |             0 |
| Erling Haaland       | FWD   | Man City       |     140 |    2 |        1 |         0 |             0 |
| Norberto Murara Neto | GK    | Bournemouth    |      45 |   10 |        1 |         0 |             0 |
| Bryan Mbeumo         | MID   | Brentford      |      65 |   16 |        1 |         1 |             0 |
| Solly March          | MID   | Brighton       |      65 |   15 |        1 |         0 |             1 |
| Mohamed Salah        | MID   | Liverpool      |     125 |    5 |        1 |         0 |             0 |
| Rodrigo Hernandez    | MID   | Man City       |      56 |    3 |        1 |         0 |             0 |
| Jarrod Bowen         | MID   | West Ham       |      70 |    2 |        1 |         0 |             0 |
| Malo Gusto           | DEF   | Chelsea        |      40 |    1 |        0 |         0 |             0 |
| Tim Ream             | DEF   | Fulham         |      45 |   -2 |        0 |         0 |             0 |
| Odsonne Edouard      | FWD   | Crystal Palace |      55 |    2 |        0 |         0 |             0 |
| Bernd Leno           | GK    | Fulham         |      45 |    2 |        0 |         0 |             0 |

#### Gameweek 3 Picks

| name                 | pos   | team           |   price |   xP |   lineup |   captain |   vicecaptain |
|:---------------------|:------|:---------------|--------:|-----:|---------:|----------:|--------------:|
| Matty Cash           | DEF   | Aston Villa    |      45 |   17 |        1 |         0 |             1 |
| Malo Gusto           | DEF   | Chelsea        |      40 |   14 |        1 |         0 |             0 |
| Destiny Udogie       | DEF   | Spurs          |      45 |   12 |        1 |         0 |             0 |
| Cristian Romero      | DEF   | Spurs          |      45 |    7 |        1 |         0 |             0 |
| Erling Haaland       | FWD   | Man City       |     140 |    4 |        1 |         0 |             0 |
| Odsonne Edouard      | FWD   | Crystal Palace |      55 |    2 |        1 |         0 |             0 |
| Bernd Leno           | GK    | Fulham         |      45 |    5 |        1 |         0 |             0 |
| Raheem Sterling      | MID   | Chelsea        |      70 |   19 |        1 |         1 |             0 |
| Jarrod Bowen         | MID   | West Ham       |      70 |   12 |        1 |         0 |             0 |
| Rodrigo Hernandez    | MID   | Man City       |      56 |   10 |        1 |         0 |             0 |
| Mohamed Salah        | MID   | Liverpool      |     125 |    5 |        1 |         0 |             0 |
| Tim Ream             | DEF   | Fulham         |      45 |    0 |        0 |         0 |             0 |
| Julián Álvarez       | FWD   | Man City       |      66 |    2 |        0 |         0 |             0 |
| Norberto Murara Neto | GK    | Bournemouth    |      45 |    2 |        0 |         0 |             0 |
| Bryan Mbeumo         | MID   | Brentford      |      67 |    2 |        0 |         0 |             0 |

#### Gameweek 4 Picks

| name                 | pos   | team           |   price |   xP |   lineup |   captain |   vicecaptain |
|:---------------------|:------|:---------------|--------:|-----:|---------:|----------:|--------------:|
| Tim Ream             | DEF   | Fulham         |      45 |    7 |        1 |         0 |             0 |
| Cristian Romero      | DEF   | Spurs          |      46 |    7 |        1 |         0 |             0 |
| Destiny Udogie       | DEF   | Spurs          |      46 |    5 |        1 |         0 |             0 |
| Erling Haaland       | FWD   | Man City       |     140 |   20 |        1 |         0 |             1 |
| Julián Álvarez       | FWD   | Man City       |      66 |   14 |        1 |         0 |             0 |
| Odsonne Edouard      | FWD   | Crystal Palace |      55 |   13 |        1 |         0 |             0 |
| Norberto Murara Neto | GK    | Bournemouth    |      45 |    1 |        1 |         0 |             0 |
| Son Heung-min        | MID   | Spurs          |      90 |   20 |        1 |         1 |             0 |
| Bryan Mbeumo         | MID   | Brentford      |      68 |    8 |        1 |         0 |             0 |
| Mohamed Salah        | MID   | Liverpool      |     125 |    8 |        1 |         0 |             0 |
| Jarrod Bowen         | MID   | West Ham       |      71 |    7 |        1 |         0 |             0 |
| Malo Gusto           | DEF   | Chelsea        |      41 |    2 |        0 |         0 |             0 |
| Matty Cash           | DEF   | Aston Villa    |      46 |   -1 |        0 |         0 |             0 |
| Bernd Leno           | GK    | Fulham         |      45 |    0 |        0 |         0 |             0 |
| Rodrigo Hernandez    | MID   | Man City       |      57 |    1 |        0 |         0 |             0 |

#### Gameweek 5 Picks

| name                               | pos   | team           |   price |   xP |   lineup |   captain |   vicecaptain |
|:-----------------------------------|:------|:---------------|--------:|-----:|---------:|----------:|--------------:|
| Sven Botman                        | DEF   | Newcastle      |      45 |    9 |        1 |         0 |             0 |
| Tim Ream                           | DEF   | Fulham         |      45 |    8 |        1 |         0 |             0 |
| Malo Gusto                         | DEF   | Chelsea        |      42 |    6 |        1 |         0 |             0 |
| Cristian Romero                    | DEF   | Spurs          |      47 |    2 |        1 |         0 |             0 |
| Julián Álvarez                     | FWD   | Man City       |      67 |    9 |        1 |         0 |             0 |
| Odsonne Edouard                    | FWD   | Crystal Palace |      55 |    8 |        1 |         0 |             0 |
| Erling Haaland                     | FWD   | Man City       |     141 |    6 |        1 |         0 |             0 |
| Norberto Murara Neto               | GK    | Bournemouth    |      45 |   10 |        1 |         0 |             1 |
| Bernardo Veiga de Carvalho e Silva | MID   | Man City       |      64 |   13 |        1 |         1 |             0 |
| Mohamed Salah                      | MID   | Liverpool      |     125 |   10 |        1 |         0 |             0 |
| Bryan Mbeumo                       | MID   | Brentford      |      68 |    2 |        1 |         0 |             0 |
| Matty Cash                         | DEF   | Aston Villa    |      47 |    1 |        0 |         0 |             0 |
| Bernd Leno                         | GK    | Fulham         |      46 |    6 |        0 |         0 |             0 |
| Son Heung-min                      | MID   | Spurs          |      91 |    2 |        0 |         0 |             0 |
| Jarrod Bowen                       | MID   | West Ham       |      71 |    2 |        0 |         0 |             0 |

#### Gameweek 6 Picks

| name                            | pos   | team           |   price |   xP |   lineup |   captain |   vicecaptain |
|:--------------------------------|:------|:---------------|--------:|-----:|---------:|----------:|--------------:|
| Kieran Trippier                 | DEF   | Newcastle      |      65 |   18 |        1 |         1 |             0 |
| James Tarkowski                 | DEF   | Everton        |      44 |   14 |        1 |         0 |             0 |
| Sven Botman                     | DEF   | Newcastle      |      46 |   12 |        1 |         0 |             0 |
| Jonny Evans                     | DEF   | Man Utd        |      40 |   12 |        1 |         0 |             0 |
| Carlton Morris                  | FWD   | Luton          |      55 |    9 |        1 |         0 |             0 |
| Bernd Leno                      | GK    | Fulham         |      46 |   10 |        1 |         0 |             0 |
| Kaoru Mitoma                    | MID   | Brighton       |      65 |   14 |        1 |         0 |             1 |
| Son Heung-min                   | MID   | Spurs          |      91 |   14 |        1 |         0 |             0 |
| Bukayo Saka                     | MID   | Arsenal        |      87 |   13 |        1 |         0 |             0 |
| Bruno Guimarães Rodriguez Moura | MID   | Newcastle      |      58 |   13 |        1 |         0 |             0 |
| Mohamed Salah                   | MID   | Liverpool      |     125 |   10 |        1 |         0 |             0 |
| Malo Gusto                      | DEF   | Chelsea        |      43 |   -2 |        0 |         0 |             0 |
| Odsonne Edouard                 | FWD   | Crystal Palace |      56 |    2 |        0 |         0 |             0 |
| Julián Álvarez                  | FWD   | Man City       |      69 |    1 |        0 |         0 |             0 |
| Norberto Murara Neto            | GK    | Bournemouth    |      45 |    1 |        0 |         0 |             0 |

#### Transfers Summary

|   Gameweek | Transfers In                       | Transfers Out                      |
|-----------:|:-----------------------------------|:-----------------------------------|
|          2 | Destiny Udogie                     | Raphaël Varane                     |
|          3 | Raheem Sterling                    | Solly March                        |
|          4 | Son Heung-min                      | Raheem Sterling                    |
|          5 | Bernardo Veiga de Carvalho e Silva | Destiny Udogie                     |
|          5 | Sven Botman                        | Rodrigo Hernandez                  |
|          6 | James Tarkowski                    | Bernardo Veiga de Carvalho e Silva |
|          6 | Kaoru Mitoma                       | Tim Ream                           |
|          6 | Kieran Trippier                    | Bryan Mbeumo                       |
|          6 | Bukayo Saka                        | Erling Haaland                     |
|          6 | Bruno Guimarães Rodriguez Moura    | Cristian Romero                    |
|          6 | Carlton Morris                     | Jarrod Bowen                       |
|          6 | Jonny Evans                        | Matty Cash                         |