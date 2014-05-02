% title: Mapping the Metastable States of the Human Kinome
% author: Kyle Beauchamp
% author: Chodera Lab

---
title: The Human Kinome

<center>
<img height=550 src=figures/kinome_drugs.jpg />
</center>

<footer class="source"> 
Rix, 2009
</footer>


---
title: Ligand Binding and Metastable States

<center>
<img height=450 src=figures/gprc_ligands.jpg />
</center>

<footer class="source"> 
Kobilka, 2012
</footer>

---
title: Physical Simulation of Kinase Inhibitors

<center>
<video width="640" height="480" controls loop autoplay>
  <source src="movies/lapitinib.ogg" type="video/ogg">
</video>
</center> 

<footer class="source"> 
Shaw, 2012
</footer>

---
title: Challenges in Simulation

- Precision (Sampling)
- Quantitative Connection to Experiment (Prediction)
- Interpretation (Dimensionality Reduction)


---
title: Markov State Models of Kinetics

- Statistical sampling by short simulations (Sampling)
- Predict arbitrary time-correlation functions (Prediction)
- Few-state models for interpretability (Reduction)

<center>
<img height=365 src=figures/chodera2007.png />
</center>


<footer class="source"> 
Chodera, 2007
</footer>

---
title: Introduction to Markov State Models

<center>
<img height=400 src=figures/NewPaths1.png />
</center>

---
title: Introduction to Markov State Models

<center>
<img height=400 src=figures/NewPaths2.png />
</center>


---
title: Introduction to Markov State Models

<center>
<img height=400 src=figures/NewPaths3.png />
</center>


---
title: The Markov State Model Pipeline

<img src="https://docs.google.com/drawings/d/1T156xWFqyu6rxG2aIkI7_a-BY5e8TvXEZ-Jjjg0XgGc/pub?w=960&amp;h=384">

---
title: Challenges in MSM Construction

- No score function for overall model
- Hyperparameters in every step 
- Bias variance tradeoff is unwinable (10,000 states)  

---
title: HMMs of Molecular Kinetics

<center>
<img height=150 src="http://upload.wikimedia.org/wikipedia/commons/8/83/Hmm_temporal_bayesian_net.svg">
<img height=400 src="http://scikit-learn.org/stable/_images/plot_hmm_stock_analysis_1.png">
</center>

<footer class="source"> 
Prinz, 2013.  McGibbon, 2014.
</footer>


---
title: A HMM Pipeline for Molecular Kinetics

<center>
<img src="https://docs.google.com/drawings/d/1zgfNZ4JFH-X62KQklDFHpgZ2orxRHkfyfk_pBdi4YQs/pub?w=960&amp;h=384">
</center>



---
title: MSMBuilder, MDTraj, Mixtape (MSMB3)
subtitle: High-Performance Featurization and Analysis

<center>
   <img height=250 src=figures/msmb_logo.png />  <img height=250 src=figures/mdtraj_logo-small.png />
</center>

<footer class="source">
Contributions from Vijay Pande, Greg Bowman, Xuhui Huang, John Chodera, Sergio Bacallado, Dan Ensign, Vince Voelz, TJ Lane, Lutz Maibaum, Imran Haque, Robert McGibbon, Christian Schwantes, Toni Giorgino, Gianni de Fabritiis
</footer>




---
title: Loading Trajectories

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>

<pre class="prettyprint" data-lang="python">

import mdtraj

trj = mdtraj.load("./Trajectories/trj0.h5")

</pre>




---
title: Trajectory Featurization

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>

<pre class="prettyprint" data-lang="python">
import mixtape

featurizer = mixtape.featurizer.AtomPairsFeaturizer([[0, 1],[1, 2], [2, 3]], trj0)
X = featurizer.featurize(trj0)

array([[ 0.10102207,  0.15920012,  0.16530874]], dtype=float32)
</pre>

---
title: Slow Feature Detection with tICA

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>


<pre class="prettyprint" data-lang="python">
tica = mixtape.tica.tICA()
map(lambda trj: tica.partial_fit(featurizer.featurize(trj)), trajectories)
X_slow = map(lambda trj: tica.transform(featurizer.featurize(trj)), trajectories)

</pre>



---
title: HMMs of Molecular Kinetics

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>

<pre class="prettyprint" data-lang="python">
model = mixtape.ghmm.GaussianFusionHMM(n_states)
model.fit(X_slow)
</pre>



---
title: Recovering Known Metastable States

<img height=500 src="./figures//tICA_hp35_-1alpha_3tics_10states.png">
<img height=350 src="./figures/2f4k.png">



---
title: Recovering Known Metastable States

<img height=500 src="./figures/hp35_overlay.png">

<footer class="source"> 
Kiefhaber, 2010
</footer>

---
title: Metastable States of src Kinase?

<center>
<img height=500 src="./figures/src_tica.png">
</center>


---
title: Metastable States of src Kinase?

<center>
<img width=525 src="./figures/src.png">
</center>



---
title: Conclusions


- We are mapping metastable states of the human kinome
- Robust kinetic modeling pipeline recovers known results
- A robot will test our predictions 


---
title: Future Work

- Cross-Validated Likelihood and Model Selection
- Pipelining and Concatenating Featurizers
- Outlier Detection of Bad Trajectories
- Improved Simulation Protocols and Parameters


---
title: Acknowledgements

- Chodera Lab
- Robert McGibbon and MSMBuilder Developers
- Folding@Home + Vijay Pande
