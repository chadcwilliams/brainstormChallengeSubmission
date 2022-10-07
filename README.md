# Williams: Brainstorm Challenge Submission
Here, I have attempted to classify the responder/non-responder outcomes in two ways:
  Hypothesis 1: Within the first session (T1 vs T2)
  Hypothesis 2: Across sessions (T1 vs T3)
  
## EEG Feature Extraction
  To extract EEG features for classification in this project, I began by conducting Fast Fourier Transforms (FFT) on each trial of the processed data for each electrode and participant. From these data, I extracted alpha power (8 to 13.5 Hz for hypothesis 1 & 10 to 13.5 Hz for hypothesis 2) for four quadrants of the head: frontal left (F7 electrode), frontal right (F8 electrode), parietal left (P7 electrode), and parietal right (P8 electrode). Additionally, I here averaged across trials for each participant. 
  
  Following this, I determined two EEG features of interest: frontal alpha asymmetry and parietal alpha asymmetry. Note that these analyses are roughly in line with the methodology of Koo et al., 2019. For these measures, we took the difference between the left and right quadrants (left minus right), normalized by their sum for the corresponding analyses (e.g., frontal asymmetry subtracted the frontal right quadrant from the frontal left quadrant). 
  
  The same procedure was followed for both T1 and Tx (Tx = T2 for hypothesis 1 and T3 for hypothesis 2). Finally, I created a difference between Tx and T1. This left me with two EEG features per participant.
  
## Differentiable Architecture Search
  I then used these EEG features as inputs to a differentiable architechure search (DARTS; Liu et al., 2019). This approach results in a (theoretically) interpretable equation that maps the inputs (here, EEG features) to the classifications via a computation graph. The advantage of this approach is that the resulting equation is explicit and thus potentially interpretable, in contrast to black-box approaches such as neural networks. 

