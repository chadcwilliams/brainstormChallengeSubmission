# Williams Brainstorm Challenge Submission
Here, we sought to determine whether we could classify responders from non-responders undergoing a TMS therapy as a method of treating depression by utilizing electroencephalographic (EEG) measures. This therapy included four time points, T1 to T4, where T1 and T2 were pre and post intervention on the first session and T3 and T4 were pre and post intervention on the final session. There were two analyses of interest wherein Hypothesis 1 investigated whether there were any immediate effects of the intervention and thus compared the pre and post of the first session (T1 vs T2) and Hypothesis 2 investigated whether there were any long-term effects of the intervention by comparing the first session to the final session (focusing on pre-intervention: T1 vs T3). Although we investigated both of these hypotheses, we were only able to classify Hypothesis 2 and thus only report it here. Our approach to classify treatment outcome included first extracting EEG features of pre-processed data and then using these features within a differentiable architecture search (DARTS). For Hypothesis 2, we found a classification accuracy of **71%**
  
## EEG Feature Extraction
To extract EEG features for classification in this project, we began by conducting Fast Fourier Transforms (FFT) on each trial of the pre-processed data for each electrode and participant. From these data, we averaged across trials and then extracted averaged alpha power (8 to 13.5 Hz) for four quadrants of the head: frontal left (F7 electrode), frontal right (F8 electrode), parietal left (P7 electrode), and parietal right (P8 electrode).
  
Following the extraction of alpha power, we determined two EEG features of interest: frontal alpha asymmetry and parietal alpha asymmetry - these analyses were roughly in line with EEG literature classifying depression, such as the work of Koo et al., 2019. For each measure, we took the difference between the left and right quadrants (right minus left), normalized by their sum:
  
$$ EEGFeature = {RightAlphaPower - LeftAlphaPower \over RightAlphaPower+LeftAlphaPower}$$

The alpha assymetry extraction procedure was followed for both T1 and T3 and then we created a difference between T1 and T3, again normalized by their sum:
  
$$ EEGFeature_{Difference} = {EEGFeatures_{T3} - EEGFeatures_{T1} \over EEGFeatures_{T3} + EEGFeatures_{T1}} $$

In total, our procedure left us with two EEG features per participant, which were then used as predictors in DARTS.
  
## Differentiable Architecture Search
We used the extracted EEG features as predictors in a Differentiable Architecture Search (DARTS; Liu et al., 2019; Musslick et al., 2021). This approach resulted in an interpretable equation that maps the predictors (here, EEG features) to the classification outcomes (here, responders versus non-responders) via a computation graph. The advantage of this approach is that the resulting equation is explicit and thus interpretable, in contrast to black-box approaches such as neural networks. An additional advantage to using DARTS is that it has a feature selection mechanism in that it may remove edges between predictors and nodes. Indeed, this occured in the current implementation of DARTS wherein it predicted outcomes by using only one of the two predictors provided. Specifically, it only used parietal alpha assymetry to predict outcomes. 

We first determined an architecture and then determined model predictability using the leave-one-out cross-validation method. Specifically, we began by training DARTS using all data in order to develop an architecture – i.e., an equation in the form of a computation graph, see Figure 1 with accompanying equation for the architechture. The resulting architecture contained four coefficients.

<hr style="border:2px solid gray">
  
![Alt text](/Images/williamsBrainstormChallenge_Figure1.svg "Figure 2. Example Architecture")

**Figure 1.** Computation Graph Representation of Classification Equation.

**Equation for architecture in Figure 1:**

$k1 = sin(2.65 * x2 + -2.40)$  
$y1 = -2.26 * k1 + 0.46696842$  
$p(detected) = sigmoid(y1)$  

<hr style="border:2px solid gray">

We then followed a leave-one-out cross-validation method in that we iteratively fit this fixed architecture’s four coefficients for all but one participant and then predicted the outcome of the remaining participant, see the following three example equations that signify different iterations of this process. The averaged accuracy was used as our model’s predictability metric - here, the resulting accuracy was **71%**

**Equation for architecture when predicting participant 10:**

$k1 = sin(2.68 * x2 + -2.52)$  
$y1 = -2.24 * k1 + 0.29738835$  
$p(detected) = sigmoid(y1)$  

  
**Equation for architecture when predicting participant 20:**

$k1 = sin(2.67 * x2 + -1.88)$  
$y1 = -2.19 * k1 + 0.56886053$  
$p(detected) = sigmoid(y1)$  


**Equation for architecture when predicting participant 30:**

$k1 = sin(1.85 * x2 + -2.32)$  
$y1 = -2.04 * k1 + 0.64289635$  
$p(detected) = sigmoid(y1)$  

<hr style="border:2px solid gray">

## References

Koo, P. C., Berger, C., Kronenberg, G., Bartz, J., Wybitul, P., Reis, O., & Hoeppner, J. (2019). Combined cognitive, psychomotor and electrophysiological biomarkers in major depressive disorder. European archives of psychiatry and clinical neuroscience, 269(7), 823-832.

Liu, H., Simonyan, K., & Yang, Y. (2018). Darts: Differentiable architecture search. In International Conference on Learning Representations. arXiv: https://arxiv.org/abs/1806.09055

Musslick, S. (2021). Recovering quantitative models of human information processing with differentiable architecture search. In Proceedings of the 43rd Annual Conference of the Cognitive Science Society (pp. 348–354). Vienna, AT. arXiv: https://arxiv.org/abs/2103.13939

## GitHub Files Dictionary

**Training:**

**williamsBrainstormChallenge_EEGFeaturesExtraction.ipynb:** File that extracts EEG features from the training set of participants and produces the williamsBrainstormChallenge_T1vsT3Features.csv file.

**williamsBrainstormChallenge_DeterminingArchitecture.ipynb:** The main script to determine a DARTS architecture and thus the equation linking predictors to outcomes. This script also produces the file williamsBrainstormChallenge_Architecture.pickle, which contains the architecture and is used by the williamsBrainstormChallenge_DeterminingValidationOutcomes.ipynb to predict outcomes of the validation set.

**williamsBrainstormChallenge_DeterminingArchitecture(WithOutput).ipynb:** Same as above but including the outputs of the current model.


**Validation:**

**williamsBrainstormChallenge_DeterminingValidationOutcomes.ipynb:** File that extracts EEG features from the validation set of participants and produces the williamsBrainstormChallenge_T1vsT3ValidationOutcomes.csv file. Further, it uses the williamsBrainstormChallenge_Architecture.pickle file, as produced by williamsBrainstormChallenge_DeterminingArchitecture.ipynb, to predict outcomes of the validation set, stored in file williamsBrainstormChallenge_T1T3(H2)PredictedOutcomes.csv (this is the file of interest for the challenge).
