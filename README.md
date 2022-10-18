# Williams, C.C., Holland, J., & Musslick, S. (2022). Brainstorm Challenge Submission

Here, we sought to determine whether we could classify responders from non-responders undergoing a TMS therapy as a method of treating depression by utilizing electroencephalographic (EEG) measures of the brain. This therapy includes four time points T1 to T4 where T1 and T2 are pre and post intervention on the first session and T3 and T4 are pre and post intervention on the final session. There were two analyses of interest wherein Hypothesis 1 investigated whether there were any immediate effects of the intervention and thus compared the pre and post of the first session (T1 vs T2) and Hypothesis 2 investigated whether there were any long-term effects of the intervention by comparing the first session to the final session (focusing on pre-intervention: T1 vs T3). Although we investigated both of these hypotheses, we were only able to classify Hypothesis 2 and thus it is solely reported here. Our approach to classify treatment outcome included first extracting EEG features of pre-processed data and then using these features within a differentiable architecture search (DARTS).
  
## EEG Feature Extraction [[[CHECK ALPHA FREQS]]]
  To extract EEG features for classification in this project, we began by conducting Fast Fourier Transforms (FFT) on each trial of the processed data for each electrode and participant. From these data, we extracted alpha power (8 to 13.5 Hz for hypothesis 1 and 10 to 13.5 Hz for hypothesis 2) for four quadrants of the head: frontal left (F7 electrode), frontal right (F8 electrode), parietal left (P7 electrode), and parietal right (P8 electrode). Additionally, we here averaged across trials for each participant. 
  
  Following this, we determined two EEG features of interest: frontal alpha asymmetry and parietal alpha asymmetry, computed as. Note that these analyses are roughly in line with the methodology of Koo et al., 2019. For these measures, we took the difference between the left and right quadrants (right minus left), normalized by their sum for the corresponding analyses (e.g., frontal asymmetry subtracted the frontal right quadrant from the frontal left quadrant).
  
$$ EEGFeature = {RightAlphaPower - LeftAlphaPower \over RightAlphaPower+LeftAlphaPower} $$

  The same procedure was followed for both T1 and Tx (Tx = T2 for hypothesis 1 and T3 for hypothesis 2). Finally, we created a difference between T1 and Tx (Tx – T1), leaving us with two EEG features per participant, per hypothesis.
  
$$ EEGFeature_{Difference} = {EEGFeatures_{Tx} - EEGFeatures_{T1} \over EEGFeatures_{Tx} + EEGFeatures_{T1}} $$
  
## Differentiable Architecture Search
  We then used these EEG features as predictors in a Differentiable Architecture Search (DARTS; Liu et al., 2019; Musslick et al., 2021). This approach results in an interpretable equation that maps the predictors (here, EEG features) to the classification outcomes via a computation graph. The advantage of this approach is that the resulting equation is explicit and thus interpretable, in contrast to black-box approaches such as neural networks. 

  We first determined an architecture and then determined model predictability using the leave-one-out method. Specifically, we began by training DARTS using all data in order to develop an architecture – i.e., an equation in the form of a computation graph [[[see Figure 1 for an example architecture & equation]]]. As this process included all data and not only a training set, the architecture would be biased, but further investigations will be necessary to determine how much of a bias it would have. We then iteratively fit this fixed architecture’s coefficients, with each iteration using a training set containing all but one participant. On each iteration, we classified the remaining participant to determine classification accuracy [[[see Figure 2 for an example of the different coefficients while keeping the architecture stable]]]. The averaged accuracy was used as our model’s predictability metric. 
  
<hr style="border:2px solid gray">
  
![Alt text](/Images/FixedArchitecture.png "Figure 2. Example Architecture")
**Figure 1.** An example architecture.

**Equation for architecture in Figure 1:**
$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$

<hr style="border:2px solid gray">

**Equation for architecture when predicting participant 10:**
$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$
  
**Equation for architecture when predicting participant 20:**
$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$

**Equation for architecture when predicting participant 30:**
$$k1 = ReLU(x1) + cos(x2)$$
$$k2 = ReLU(x1) + cos(x2) + tanh(k1)$$
$$y1 = -0.24 * k1 + -0.16 * k2 + 0.659459$$

<hr style="border:2px solid gray">

## References

Koo, P. C., Berger, C., Kronenberg, G., Bartz, J., Wybitul, P., Reis, O., & Hoeppner, J. (2019). Combined cognitive, psychomotor and electrophysiological biomarkers in major depressive disorder. European archives of psychiatry and clinical neuroscience, 269(7), 823-832.

Liu, H., Simonyan, K., & Yang, Y. (2018). Darts: Differentiable architecture search. In International Conference on Learning Representations. arXiv: https://arxiv.org/abs/1806.09055

Musslick, S. (2021). Recovering quantitative models of human information processing with differentiable architecture search. In Proceedings of the 43rd Annual Conference of the Cognitive Science Society (pp. 348–354). Vienna, AT. arXiv: https://arxiv.org/abs/2103.13939

