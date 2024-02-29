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
Different weights will be assigned to each metric. ACS stands for Average Combat Score and it measures a player’s impact on the game through their kills, multi-kills, damage, etc. Due to how it’s able to measure an individual player’s influence on the game, it will be given the highest weightage of 50%. KDR stands for Kill-Death Ratio and it can be used to indicate how many kills a player can get while staying alive in that round. Hence, it’s a good indicator of how well a player can create a numerical advantage (against enemies), which is an important skill to help win rounds. However, since KDR is solely based on killing ability, it fails to take into account how well a player can use their utility and skills to help their team, therefore, it will be given a slightly lower weightage of 30%. Finally, the clutch rate is a measurement of the percentage of clutch situations a player has won. A clutch situation is when a player is the last one left alive from their team. But since clutch situations occur relatively less frequently, it will be given the lowest weightage of 20%. 

Other metrics such as assists per round (APR), first deaths per round (FDPR), etc., will not be considered because these metrics will either be high or low depending on what role a player is playing. For example, initiators play a more supportive role, hence, their APR would be higher than duelists, who have the responsibility of rushing in to get the first kill (higher FDPR). Therefore, we will only use metrics that can indicate a player’s skill level no matter what role they are playing. 

There will be three types of constraints for this MILP project. 
1. Number of players constraint: The resulting team can only have exactly 5 players. This is because Valorant requires 5 players to play in a team. It is not against the rules to have 6 players (1 player is a substitute), however, in this tournament, there have been no teams that have more than 5 players. Furthermore, since we only want to find the team that can give the highest skill level, we will only consider the 5 players that will be playing the match. The constraint can be written as follows:
```math
\sum x_i = 5
```
2. Nationality constraint: A team can have at most 2 non-Japanese players. According to tournament rules, an Esports Organization team based in Japan can only have at most 2 non-Japanese players, in other words, at least 3 Japanese players. Among the top 2 teams in this tournament, there is only 1 Korean player on each team, making the data set for this project have a total of 2 Korean players in total. $`x_{Japanese}`$ is a binary variable that will be 1 if a player’s nationality is Japanese and 0 if a player’s nationality is non-Japanese (i.e., Korean), and hence, the constraint equation can be as follows:
```math
\sum x_{Japanese} \geq 3
```
3. Role constraint: There has to be at least 1 player for each role on the team. Valorant has 4 types of roles: duelist, controller, sentinel, and initiator. Usually, team compositions will need to have at least 1 player for each unique role, and the last player is free to play any role. Therefore, the role constraint will be represented by 4 constraint equations, where $`x_{[role]}`$ is a binary variable that is 1 or 0 depending if a player specializes in that role or not.
```math
\displaylines{
\sum x_{duelist} \geq 1 \\
\sum x_{controller}  \geq 1 \\
\sum x_{sentinel}  \geq 1 \\
\sum x_{initiator}  \geq 1 \\
}
```

## MILP Assumptions 
One assumption of the MILP is that the decision variables (i.e., each of the 10 players) are independent of each other. The selection of one variable/player will not influence the value of another variable/player. This is because each player is different from the other, and it will be assumed that they don’t have to rely on other players to increase or decrease their skill levels because it has originated from their skills which they have practised and honed for the tournament. 

Another assumption is that the variables of the objective function and constraints can be represented through a linear relationship through a straight line. This is because the relationships between variables in the objective function and constraints have a linear relationship with each other. This is a necessary assumption as we are working with a MILP problem to maximize the highest skill level of a Valorant team. 

Furthermore, since MILP can take integers in addition to continuous variables, an assumption is that when binary variables are used in this project, they must only take in whole numbers that are inside the specified bounds of the variable (i.e., 1 or 0). 

## Project-Specific Assumptions 
One assumption that is specific to this project is that the level of Japanese communication the Koreans can use to communicate during a match is the same. Since there are only 2 Korean players among the top 2 teams, it will be assumed that their level of communication is the same. This is necessary because if a player is unable to speak Japanese, it could severely impact a team’s teamwork if they fail to communicate with each other. Furthermore, it is unknown exactly how good each Korean’s Japanese skill is as viewers are not able to hear their in-game voice chat. Hence, it will be assumed that their Japanese communication skills are at the same level. 

Another assumption originates from the role constraint, which is that the team composition for this project will be 1 player for each of the 4 unique roles, and the last player can be from any role. To solve this problem, we will assume the same team composition to be selected for every map. This is because, in reality, teams may choose to change their composition to optimize their team strategy for a specific map or match up against a specific team. For instance, in recent international tournaments, teams have slowly started coming up with new team compositions that don’t have any duelist, and instead have 2 controllers, 2 sentinels, and 1 initiator. However, since team compositions also depend on the current meta of the game, this project will assume and use a fixed team composition that has been the most standard and commonly used in professional Valorant history. 

The final assumption is that every player specializes in only one role. This follows that the data collected will record a player’s most-played role (in tournament matches) and the role they are most known for in terms of the use of utility in that role. This is assumed because some players may play a duelist role for a particular map, but for another map, they may go for a sentinel role, for example. The reason behind this is related to the previous assumption, as some teams may prefer different team compositions for different scenarios. But for this project, we will only consider the role they play most often, as it is a greater indicator of their skill level and strength of that role. 

## Data Collection
The data for a player’s ACS, KDR, clutch rate, nationality, and specialized role will be collected through the website vlr.gg (https://www.vlr.gg/event/stats/1432/challengers-league-japan-split-1), which has the statistics for all players and an average for each metric from the entire tournament. The value for each metric will be collected as an average from all their matches in the tournament. 

After collecting all the data, it has been recorded in an Excel spreadsheet as shown below,
<p align="center">
  <img src="https://github.com/kellyngsf/valo_teams/blob/main/images/data_snapshot.png" width=450>
</p>

## Analysis 
After collecting the data, the package “lpSolve” in R was used to analyse the data set. Firstly, the objective function was defined with the relative weights as discussed earlier. 
```
objective_function_coefficients <- c(data$ACS * 0.5 + data$KDR * 0.3 + data$clutch_rate * 0.2)
```

The constraints are dfeined by coding a constraints matrix as follows,
```
A <- rbind(
  rep(1, n),
  as.integer(data$nationality == "Japanese"),
  as.integer(data$position == "initiator"),
  as.integer(data$position == "duelist"),
  as.integer(data$position == "sentinel"),
  as.integer(data$position == "controller")
)

# Constraints directions
dir <- c("==", ">=", ">=", ">=", ">=", ">=")

# Right-hand side (rhs) values
rhs <- c(5, 3, 1, 1, 1, 1)
```
The first row of the constraint matrix A is to ensure the sum of all the players’ binary variables is equal to 5 (i.e., 5 players on a team). The second row guarantees that the sum of all the players’ nationalities is so that there are at least 3 players of Japanese nationality. Finally, rows 3-6 are defined to have at least 1 player for every role. The constraints’ sign directions and values on the right-hand side of the constraint equations for each constraint are then defined through `dir` and `rhs`. 

The `lp()` function will then be used to solve the MILP problem:
```
result <- lp(direction = "max", objective.in = objective_function_coefficients, const.mat = A, const.dir = dir, const.rhs = rhs, all.bin = TRUE)
```
Finally, an if-statement will be used to check if the solution is optimal; if not optimal, the function will output “No optimal solution found”, otherwise, the players’ ID/names will be extracted and outputted. 
```
if (result$status == 0) {
  # Get the binary variables indicating the selected players
  selected_players <- result$solution
  
  # Extract the player IDs of the selected players
  selected_player_ids <- data$player_ID[selected_players == 1]
  
  # Display the selected player IDs
  print(selected_player_ids)
} else {
  cat("No optimal solution found.")
}
```

## Results 
From the previous code chunks, the resulting team has been outputted as follows,
![](https://github.com/kellyngsf/valo_teams/blob/main/images/final_result.png)

Out of the 5 players chosen, 3 (Meiy, Popogachi, Medusa) are from the team that placed second in the tournament, and 2 are from the team that placed first (Syouta, CLZ). Furthermore, even though one of the constraints was to have at least 3 Japanese players, the solution to the MILP has 4 Japanese players, the only Korean player being Medusa. 

The roles that are present in this team are 2 duelists (Meiy and CLZ) and 1 for each other role (Popogachi: initiator, Medusa: sentinel, Syouta: controller). This means that the last role was chosen to be for a duelist, which results primarily from CLZ’s high ACS of 239.6, which is the second highest after Meiy (246.7). Another observation from these 5 chosen players is that all players are among the top 5 with the highest ACS. However, only 2 of them are within the top 5 highest clutch rate players, the highest being Medusa. Finally, all players except for Popogachi are within the top 5 for the highest KDR. This is unsurprising as the observation function was defined to place the most emphasis on ACS, as it is the most looked-at metric in the professional Valorant scene to determine how well a player performed. 

## Limitations and Evaluations 
The MILP has successfully outputted a team that has the highest skill levels based on ACS, KDR, and clutch rate, however, there are also a few limitations of this method for this problem. 

One of the biggest limitations is that linear programming in general is unable to consider factors that are unmeasurable in numbers. For this project, we are unable to account for an important intangible factor, which is team chemistry and the level of communication between players. This is because it isn’t a metric that can be quantified numerically. Although all players know each other and are friends with one another, team chemistry and chemistry between players in-game are often unknowable until they are properly put together in a team, have practised together, and experienced pressures in tournaments together. Hence, although team chemistry is a crucial factor to determine the overall skill level of a team, it is intangible and unable to be considered in the MILP. 

Similar to the previous limitation, the level of Japanese communication the Korean players have can affect the quality of teamwork a team may have. This is why it was assumed the two Korean players have the same level of Japanese communication, however, the fact that this is an intangible and an unmeasurable variable, places the solution to this MILP at a slight disadvantage on how high levelled a team will be. 

These two limitations could be improved by creating and calculating a metric that represents how well players get along with each other. For example, if players have played with each other before on the same team, or just for fun, we could have a high chemistry rating between those two players. Consequently, if two players have not interacted with each other, we could say that the chemistry level between them is lower. Additionally, we can measure the non-Japanese player’s Japanese level by how fluent they are in interviews or from feedback from their teammates. However, this rating may be slightly subjective because it will be based only on media that I have seen, not a statistic calculated from a standard formula or from the game itself. 

## Extensions 
A possible extension of this project would be to factor in the budget or cost of hiring the players. This can either be an objective function that has to be minimized or be one of the constraints, where we allocate a maximum amount of money to hire players. However, this will only be possible if the data is available for each player. This can be an interesting extension because so far, we have only considered variables to do with skills or constraints to obey tournament rules, therefore, it may be interesting to consider budget limitations. 

Another extension that can be part of a larger project would be to answer the problem of if we have all 10 players from the top 2 teams available, which players would we choose for every map in the game? As mentioned in the assumptions, team compositions may change depending on the map, team match-up, or meta. To combat this, we can research more into the current meta of Valorant and see what are the most common team compositions for a specific map. More specifically, which 5 characters/agents are used in the current meta? Valorant has 4 unique roles, and within each role, there are a wide variety of characters that have the same role title but different skills and ultimates. Then, we can calculate the statistics of all players and every character they have played, not just consider data based on the role they specialize in. Then, we can use a similar objective function to calculate which players are chosen for the highest-skilled team, depending on how well they can play a certain character. For example, if a player produces a high skill level in the duelist role, but another player may perform better on a certain character (that is a duelist), the latter player may be chosen to play, instead of the former one because of their extreme specialization of a specific character. 
