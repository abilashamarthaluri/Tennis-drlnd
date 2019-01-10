[//]: # (Image References)

[image1]: https://raw.githubusercontent.com/abilashamarthaluri/Tennis-drlnd/master/images/tennis.jpg "Report for Tennis"

## Report

I have implemented [MADDPG](https://arxiv.org/abs/1706.02275) which is an off policy, actor critic method which uses ideas from [DQN](https://www.nature.com/articles/nature14236) and Deep Deterministic Policy Gradients [DDPG](https://arxiv.org/pdf/1509.02971.pdf), and addresses the problems that there two papers have on their own. Q-learning is challenged by an inherent non-stationarity of the environment, where as Policy Gradients suffers from variance that increases as the number of agents grows. 

## Model Architecture

In MADDPG, centralized training and decentralized execution is the main core idea of reliably training agents to learn to collaborate and compete with each other.

There are both Actor, Critic and their respective target networks, similar to DDPG.

The key idea is Actor's are only exposed to their local observations(same agent), where as the critics are given access to the policies of other agents. Critic helps the actor decide what actions to reinforce during training. 

Here is a animation of how the agents are trained and how it differs at execution time from [OPENAI](https://blog.openai.com/content/images/2017/06/nipsdiagram_2.gif)


Actor Network ==> BatchNorm(States) --> FC1(state_size * 128) --> FC2 (128 * 64) --> FC3 (64 * action_size).
    RELU is used for the Non Linearity, for the initial layers, and TANH is used in the final layer since every entry in the action vector should be a number between -1 and 1.

Critic Network ==> BatchNorm(States) ==> FCS1(state_size * 128) --> FC2(fcs1_units+action_size,64) --> FC3(64, 1)

Final Layer weights of both the Actor and Critic are initialized from a uniform distribution (-3e-3, 3e-3) and all the other layers are initialized from uniform distributions (-1/sqrt(f), 1/sqrt(f)) where f is the fanin of the layer.

Also the initial paraemeters are hardly copied over from local to target networks so that they start from the same weights.

## Hyper Parameters

BUFFER_SIZE = int(1e6)  # replay buffer size
BATCH_SIZE = 128        # minibatch size
GAMMA = 0.99            # discount factor
TAU = 1e-3              # for soft update of target parameters
LR_ACTOR = 1e-4         # learning rate of the actor 
LR_CRITIC = 1e-4        # learning rate of the critic
WEIGHT_DECAY = 0        # L2 weight decay
N_LEARN = 10

Using MADDPG, I am able to Solve Tennis Environment. 

Ornstein-Uhlenbeck process is used for introducing noise in the actions, but the values of actions are clipped between -1 and 1.

## Rewards Plot: 

[Rewards Plot][image1]

## Ideas for Future Work

Initially working on this project, I found that the training was really unstsable, but after some experimentation, started getting some decent results from MADDPG, but Ideally, I would want to do a Hyperparameter search and figure out the optimal parameters. I would also like to explore how other algorithms like [PPO](https://arxiv.org/abs/1707.06347) perform on this parituclar task.
