# Williams et al: Brainstorm Challenge Submission
Here, we have attempted to classify the responder/non-responder outcomes in two ways:

-Hypothesis 1: Within the first session (T1 vs T2)

-Hypothesis 2: Across sessions (T1 vs T3)
  
  
## EEG Feature Extraction [[[CHECK ALPHA FREQS]]]
  To extract EEG features for classification in this project, we began by conducting Fast Fourier Transforms (FFT) on each trial of the processed data for each electrode and participant. From these data, we extracted alpha power (8 to 13.5 Hz for hypothesis 1 and 10 to 13.5 Hz for hypothesis 2) for four quadrants of the head: frontal left (F7 electrode), frontal right (F8 electrode), parietal left (P7 electrode), and parietal right (P8 electrode). Additionally, we here averaged across trials for each participant. 
  
  Following this, we determined two EEG features of interest: frontal alpha asymmetry and parietal alpha asymmetry, computed as. Note that these analyses are roughly in line with the methodology of Koo et al., 2019. For these measures, we took the difference between the left and right quadrants (right minus left), normalized by their sum for the corresponding analyses (e.g., frontal asymmetry subtracted the frontal right quadrant from the frontal left quadrant).
  
$$ EEGFeature = {RightAlphaPower - LeftAlphaPower \over RightAlphaPower+LeftAlphaPower} $$

  The same procedure was followed for both T1 and Tx (Tx = T2 for hypothesis 1 and T3 for hypothesis 2). Finally, we created a difference between T1 and Tx (Tx – T1), leaving us with two EEG features per participant, per hypothesis.
  
## Differentiable Architecture Search
  We then used these EEG features as predictors in a Differentiable Architecture Search (DARTS; Liu et al., 2019; Musslick et al., 2021). This approach results in an interpretable equation that maps the predictors (here, EEG features) to the classification outcomes via a computation graph. The advantage of this approach is that the resulting equation is explicit and thus interpretable, in contrast to black-box approaches such as neural networks. 

  We first determined an architecture and then determined model predictability using the leave-one-out method. Specifically, we began by training DARTS using all data in order to develop an architecture – i.e., an equation in the form of a computation graph [[[see Figure 1 for an example architecture & equation]]]. As this process included all data and not only a training set, the architecture would be biased, but further investigations will be necessary to determine how much of a bias it would have. We then iteratively fit this fixed architecture’s coefficients, with each iteration using a training set containing all but one participant. On each iteration, we classified the remaining participant to determine classification accuracy [[[see Figure 2 for an example of the different coefficients while keeping the architecture stable]]]. The averaged accuracy was used as our model’s predictability metric. 
  
  
  
![Alt text](/Images/FixedArchitecture.png "Figure 2. Example Architecture")
**Figure 1.** An example architecture.


$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$



![Alt text](/Images/ArchitectureCoeffs.png "Figure 2. Example Architecture Coefficients")
**figure 2.** An example of changing coefficients in a fixed architecture.


**Equation for architecture A in Figure 2:**
$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$
  
**Equation for architecture B in Figure 2:**
$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$

**Equation for architecture C in Figure 2:**
$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$

## References

Koo, P. C., Berger, C., Kronenberg, G., Bartz, J., Wybitul, P., Reis, O., & Hoeppner, J. (2019). Combined cognitive, psychomotor and electrophysiological biomarkers in major depressive disorder. European archives of psychiatry and clinical neuroscience, 269(7), 823-832.

Liu, H., Simonyan, K., & Yang, Y. (2018). Darts: Differentiable architecture search. In International Conference on Learning Representations. arXiv: https://arxiv.org/abs/1806.09055

Musslick, S. (2021). Recovering quantitative models of human information processing with differentiable architecture search. In Proceedings of the 43rd Annual Conference of the Cognitive Science Society (pp. 348–354). Vienna, AT. arXiv: https://arxiv.org/abs/2103.13939

