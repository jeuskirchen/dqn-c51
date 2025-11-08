Based mostly on cleanrl 
  https://github.com/vwxyzjn/cleanrl/blob/master/cleanrl/dqn_atari.py 
  https://github.com/vwxyzjn/cleanrl/blob/master/cleanrl/c51_atari.py
  which in turn is also partly based on Stable Baselines 3 
  simplified code by removing unnecessary things, including having multiple parallel envs
    now I need to call env.reset, which apparently happens automatically in multiple envs (vectorized) 
  renamed a lot of stuff based on personal preference 
  removed some environment wrappers, mostly due to compatibility issues with MinAtar 
  adjusted the neural network architecture to the MinAtar env 
  batch size 32 -> 64
  changed frame stacking to just 1, as suggested in MinAtar paper (they use trailing, etc. so you can
    deduce relevant past from this observation)
  I kept n_bins=51 constant since that worked well in the paper across many envs, so I don't expect that is the problem 
  hyperparameter tuning: 
    hyperparams related to training duration were suggested to be 1/10 of those from DQN Atari 
      due to MinAtar being much simpler, some of them I changed during experimentation 
      e.g. instead of 5_000_000/10=500_000, I ended up using 1_000_000 training steps 
           instead of 100_000/10=10_000 buffer size, I ended up using 500_000
    vmax
      rewards are non-negative, so vmin=0, but different values for vmax 
      for this, I used empirical results from DQN 
      (though those were undiscounted returns, and the support is for discounted returns, 
      but then it should be an even safer choice) 
  added reward clipping, so that all rewards can only be 0 or 1 
    see DQN paper
  also keep track of MSE loss, though the network is not trained on this!! 
    it's just for comparison against DQN 
    but, I suppose, with a grain of salt, as they are estimated very differently, 
    so I don't think they're perfectly comparable 

Things I experimented with at some point, that I did not keep 
  batch size of 64 instead of 32
  constant epsilon for entire training duration, rather than decayed 

Partly inspired by MinAtar/examples/dqn.py as well, but not that much 
  https://github.com/kenjyoung/MinAtar/blob/master/examples/dqn.py 
  I generally preferred the structure of cleanrl 

Using torch, gymnasium, minatar 

SeaQuest:
https://ale.farama.org/environments/seaquest/ 
"Rewards. Score points are your only reward. 
 Blasting enemy sub and killer shark is worth 20 points. 
 Every time you surface with six divers, the value of enemy subs and killer sharks increases by 10, 
 up to a maximum of 90 points each." 
Rewards in MinAtar/SeaQuest: https://github.com/kenjyoung/MinAtar/tree/master
