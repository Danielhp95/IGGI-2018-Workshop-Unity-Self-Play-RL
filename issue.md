#Contribution suggestion: self-play

We are a group of researchers in the University of York, UK. We are using Unity ML-agents as a testbed for our RL experiments. We are currently implementing a self-play system for Unity ML-agents based on the following paper: [Emergent Complexity via Multi-Agent Competition](https://arxiv.org/abs/1710.03748). It is our intention to offer it as a contribution to the existing Unity ML-agents codebase.  

## Motivation

On the current Unity 0.4 version, there are two ways of implementing self-play:

### One 

- Following the example of the Tennis sample environment: 
- Create two or more identical agents.
- Link each agent to the same brain.
- Start training.

**Issues**: We have tested the following disadvantage through local testing: 
The agent only gets to play against the current version of itself, overfitting to its own behaviour. And does not generalize to other strategies.

### Two

- Create two or more identical agents
- Link each agent to its own brain *(both brains trained using the same algorithm and only differing in their initial weights)*    
      
**Issues**:
Early in training one agent eventually becomes dominant and overpowers the other agent for the rest of the training. This means that the stronger agent relies too much on exploiting the weaknesses of the weaker agent.

## Our self-play contribution

### Theoretical approach

We would like to implement a self-play system inspired by that introduced in [Emergent Complexity via Multi-Agent Competition](https://arxiv.org/abs/1710.03748). The overall idea is to keep track of the latest iteration of a policy alongside with checkpoints from training history (how many checkpoints should be kept is subject to hyperparameters). Every fixed interval the latest policy is matched against itself or a previous checkpoint. This previous checkpoint is sampled uniformly at random from eligible versions.

This is done by introducing two hyper parameters:

- **Delta (𝛿)**: which takes values between [0,1]. It indicates how much of the policy history will be considered when sampling a new opponent. 𝛿= 1, only the latest policy will be used, 𝛿= 0, all of the history will be considered.      

- **Opponent policyHH change interval**: positive number. how many episodes will be played out before a new opponent will be sampled. 

A graphical representation of the training process can be found below: 

!(training-graph)(URL)  

**Benefits:**
- Agent trains against a varied set of opponents. Avoiding overfitting to its own strategy and becoming more resilient to different strategies. We hypothesize that this will prevent overfitting to a single playstyle, making an AI that can more easily adapt to different players.
- It becomes easy to monitor if the latest version of the agent can defeat previous (and random) versions of itself. If the training only consists of matches between the latest version of the algorithm and itself, we are not monitoring performance against other possible opponents. Meaning that an increase in overall model performance may be due to overfitting against the agent’s latest strategy.


### Code contribution

### Request for advice / feedback:
- How and where to store whether or not the environment should use self-play. E.g. should the brain know, and track its agents separately (in both or either c# and python), should the academy know, and have separate brains for the ‘main policy’ and past policies. Should it be handled entirely in python?
- How and where to store variables about the self-play config (e.g. 𝛿) should it be stored `trainer_config.yaml`?
