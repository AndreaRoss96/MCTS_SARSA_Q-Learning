# MTCS_SARSA_Q-Learning

## 1 Monte Carlo Tree Search (MCTS)
This section explains first, the implementation and the execution of the algorithm and then argue the results.
Moreover, it discusses a variant of the algorithm. This variant consists of the classical algorithm, but, the root of the three and the node from where the roll-out is computed change during the algorithm computation. Finally, it makes a comparison of the two algorithms.
This report follows the attached Python notebook's structure (there is no need to check the code to understand this report content).

### 1.1 Implementation
The implementation has been realized from scratch. Each node of the tree is represented through a Python object. This object contains the essential information and methods to make the node fully independent from the tree itself (useful during the debugging phase). Each node links to its parent and the left and right child. In this way, it is possible to build the binary tree recursively. Besides, just the leaf nodes have a value; this is randomly generated within an uniform distribution between $0$ and $100$, as indicated in the assignment text. The object (\textit{MCTS}) contains the logic of the algorithm. It calls node methods following the steps of the algorithm: \textit{selection, expansion, roll-out, and backup}.\\
This code factorization allowed me to perform several experiments with trees of different depth, and try different values for the algorithm's parameters.

### 1.2 Execution
The experiments have been performed on a binary tree of a depth $d=14$ (just for time reason, because the code works perfectly using higher depths; but with a depth $d>20$, Colab notebook's RAM tends to get full) and a snow-cap of $50$ and $100$. The algorithm run with 100 different values for the hyper-parameter $c$, used to compute UCB-score. To collect precise results, each of the 100 $c$ parameters is used 1000 times, storing the results and the computation times.\\
For the study of the algorithm behaviour, I decided to generate $c$ value randomly; but between three different intervals. Thus, the first 34 $c$ values are randomly generated within $0\leq c \leq 3$, other 34 $c$ values within $3<c\leq 6$, and finally the last 32 $c$ values are between $6 < c \leq 10$ (I also tried values higher than 10, but the results were not significantly relevant).

### 1.3 Results
See the report

### 1.4 MTCS Variant
In this variant, the MCTS-iterations starting from a specific node are limited to 10. After those 10 iterations, the root changes and one of the best former root's children became the new root. Moreover, a similar assumption has been done for the number of roll-outs starting from a particular snow-cap node. Indeed, the roll-out starts from the same node two times in a row then, it changes to the most promising node in the snow-cap. When this change occurs, even if the new ``roll-out node" collects worse results, the old ``roll-out node" can not be used as such anymore.\\
Using the same $c$ parameters of the MTCS implementation from above and the same tree, we can study the newly collected data.

## 2 Reinforcement Learning: SARSA and Q-Learning for Gridworld
As for the first section, the code has been written from scratch. Similarly, as I did for the binary tree, I decided on an Object-Oriented approach. Each gridworld cell is independent and contains all the information that concerns the cell itself (type, coordinates on the grid, immediate reward, state-value and action-value functions, and so forth). Another object manages the logic of the world. Cell values can be modified through this last object. Besides, it returns the states according to the agent actions. It also allows to print the cells (for debugging) and plot the gridworld heat-map.

### 2.1 Monte-Carlo for Policy Evaluation
his section explains the implementation of the Monte-Carlo algorithm for policy evaluation and then discusses the results. \\
I decided to implement the first-visit and theevery-visit algorithms and compare the results.

#### 2.1.1 Implementation
An object encapsulates the logic of Monte-Carlo algorithm. This object takes in input the policy with which the agent takes decisions. This feature allowed me to play with policies during development. Furthermore, the number of episodes per simulations is editable. The results, represented as heatmaps, show the state-value functions of the environment after all the episodes taken into account.

#### 2.1.2. Execution and Results
I decided to run the algorithm(s) with an increasing number of episodes. In this way, it is possible to see how the gridworld changes. The value of $\gamma$ is set to $0.99$ to exploit the foresight of the algorithm. Thanks to this, I could run more experiments using fewer episodes and save time. As always, the showed data are the result of $1000$ experiments with the same number of episodes and parameters.

For the Heatmap and the stats see the report.

### 2.2 Learning Agents
The implementation of the SARSA and Q-Learning agents is summarized in the diagram in figure (vd report). An abstract class contains all the methods and parameters common to both algorithms. The two implementations are completely sticking with the pseudo-code seen during the lectures. As did for Monte-Carlo policy evaluation, both, Q-Learning and SARSA, are computed used increasing number of episodes. To obtain more precise results, for each number of episodes the algorithm is computed $1000$ times. That is to say, if we want to run SARSA with, let's say, 100 episodes, we do so, but, after the 100 episodes, the algorithm is computed other $999$ times. At each iteration, the results are stored. All the heat-maps related to Q-Learning and SARSA show in each cell (except walls, pit, and goal) the expected action-value function.

#### 2.2.1 SARSA Results
Starting with 10 episodes, this experiment aims to show how the quality of the results grows with the increasing of the number of episodes. All the experiments have been carried out using a learning rate $\alpha=0.5$, an exploration probability $\epsilon=0.25$, a discount factor $\gamma=1$. I chose these value for the following reasons: $\alpha=0.5$ is a value very common on literature and I decide to take this value for granted, $\gamma=1$ simply because the math used to check if the algorithms were working correctly was easier, and $\epsilon=0.25$ is explained at the end of this section.

To obtain balanced data, each episode consists on the agent starting from the top-left cell (1,1) until it reaches a terminal cell (Pit or Goal).
The figures 9-13 show the expected action-value functions of the environment computed by the algorithm after a certain number of episodes (excluding walls, pit, and goals). The figures show the average results after all the episodes and the 1000 repetitions of each experiment.

#### 2.2.2 Q-Learning Results
The results are computed as for SARSA, using the same parameters and number of episodes.\\
Discussing the results from figure 14-18, We can easily notice that this algorithm is way faster at reaching an acceptable policy. Indeed, with 100 episodes, Q-Learning agent is already able to draw a pattern on the gridworld. Nonetheless, the average total reward is still negative. Besides, in 300 episodes, this agent reaches the total reward that SARSA agent obtains in 500 episodes. As expected, the Q-Learning algorithm converges faster than SARSA. The last figure of Q-Learning results shows a more ``convinced" path from the starting node to the Goal.

#### 2.2.3 Conclusions 
The two algorithms are very similar in terms of execution time. The key difference between these two algorithms is shown on figures 19-20. These gridworlds represent the result of an "exaggerated" experiment run with $\epsilon=0.99$ and with 500 episodes (again, both experiments has been run 1000 times). This key difference between SARSA and Q-learning is that the first one is an \textit{on-policy} algorithm, so it follows the policy that is learning, and Q-learning is \textit{off-policy}. This means that Q-Learning agent updates its Q-values using the Q-value of the next state $s'$ and the greedy action $a'$. In other words, Q-Learning estimates the return (total discounted future reward) for state-action values following a greedy policy, so the learner learns the value of the optimal policy independently of the agent's actions. This behaviour can clearly seen on figure 20, which shows that Q-Learning agent can find the (almost) perfect policy (i.e. the cell at position 1,1 has a $Q^*(s, a)=34.5$ which is a very good value, considering the minimum number of cells that the agent has to cross is 15). Despite this SARSA implementation uses a greedification to find an optimal policy, the difference between the two algorithms is still deep. it updates its Q-values using the Q-value of the next state $s'$ and the current policy's action $a''$. It estimates the return for state-action pairs assuming the current policy continues to be followed. Indeed, with a high $\epsilon$ SARSA, after 500 episodes, is still struggling to find an optimal policy.

# ALL THE RESULTS ARE VISUALIZED AND COMMENTED ON THE REPORT FILE
