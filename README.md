# Creating the Highest Skilled Valorant Team

## Introduction 
Valorant is a first-person shooter game designed by an American company, Riot Games. The most popular game mode, “Competitive” is where you play in a team of 5 and work together to either plant a bomb and prevent it from being defused (attacking side), or defuse the bomb before it goes off (defending side). There is a rapidly growing professional scene for this game, where Esports Organisations recruit the best players to form a team of 5 and fight to compete on a national/international level to win tournaments. After a very exciting recent competition in Japan, I was intrigued by the question of, what if you could make a stronger team by reshuffling the players of the teams that placed 1st and 2nd in the tournament? 

The aim of this project will thus be to investigate this question using mixed integer linear programming (MILP) and a package called “lpSolve” in R. The objective function will be to maximize the overall skill levels of the team by using metrics such as a player’s ACS (average combat score), KDR (kill-death rate), and clutch rate (the percentage of rounds won in clutch situations). Additionally, the objective function will only consider numerical measurements of skill that can be measured from in-game statistics that are calculated after a match. The constraints will consider tournament requirements, such as having 5 people on a team, having at least one person for each role, etc. By utilizing “lpSolve” to maximize the objective function with the stated constraints, the result of the MILP will be analysed to see which players are selected to make a team with the highest skill level. And finally, investigate the limitations of this MILP method. 

## As a MILP Problem
MILP will be used in this project to find the most optimal Valorant team by maximizing an objective function describing a player’s skill levels through three different metrics (ACS, KDR, clutch rate) while satisfying the constraints on players. 

The decision variables will be the set of players that are among the top 2 teams from the recent Valorant tournament in Japan (VCT 2023 Challengers League Japan - Split 1). In total, 10 potential players can be chosen, 5 from each team. The decision variable will be denoted by the binary variable $`x_i`$ , where $`x_i = 1`$ when a player is chosen and $`x_i = 0`$ when a player is not chosen. 

The objective function will be written as follows: 
```math
z = 0.5 * ACS + 0.3 * KDR + 0.2 * clutch rate
```
