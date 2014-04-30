% title: Towards Robust Kinetic Models at the Kinome Scale
% author: Kyle Beauchamp
% author: Chodera Lab

---
title: The Human Kinome

<center>
<img height=550 src=figures/kinome_drugs.jpg />
</center>

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
- Interpretation of High-Dimensional Data (Reduction)



---
title: Markov State Models for Molecular Kinetics
class: segue dark nobackground


---
title: Markov State Models

- Statistical sampling by short simulations
- Predict arbitrary time-correlation functions
- Few-state models for interpretability

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
- Hyperparameters in every step (tICA, Clustering, microstates, lumping, macrostates)
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

<img src="https://docs.google.com/drawings/d/1zgfNZ4JFH-X62KQklDFHpgZ2orxRHkfyfk_pBdi4YQs/pub?w=960&amp;h=384">




---
title: MDTraj, MSMBuilder, Mixtape (MSMB3)
subtitle: Open-Source, High-Performance Featurization, Modeling, and Analysis

<center>
<img height=250 src=figures/mdtraj_logo-small.png />   <img height=250 src=figures/msmb_logo.png />  
</center>

<footer class="source">
Contributions from Vijay Pande, Greg Bowman, Xuhui Huang, John Chodera, Sergio Bacallado, Dan Ensign, Vince Voelz, TJ Lane, Lutz Maibaum, Imran Haque, Robert McGibbon, Christian Schwantes, Toni Giorgino, Gianni de Fabritiis
</footer>




---
title: Loading and Saving Trajectories

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>

<pre class="prettyprint" data-lang="python">

import mdtraj as md
import glob

trj0 = md.load("./Trajectories/trj0.h5")
trj0.save("out.pdb")

filenames = sorted(glob.glob("./Trajectories/*.h5"))
trajectories = [md.load(filename, stride=stride) for filename in filenames]

</pre>




---
title: Trajectory Featurization

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>

<pre class="prettyprint" data-lang="python">
import mixtape.featurizer

featurizer = mixtape.featurizer.AtomPairsFeaturizer([[0, 1],[1, 2], [2, 3]], trj0)
X = featurizer.featurize(trj0)

array([[ 0.10102207,  0.15920012,  0.16530874]], dtype=float32)
</pre>

---
title: Slow Feature Extraction with tICA

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>

<pre class="prettyprint" data-lang="python">
import mixtape.tica

tica = mixtape.tica.tICA()
# Calculate correlation functions and solve the tICA eigenproblem
map(lambda trj: tica.partial_fit(featurizer.featurize(trj)), trajectories)
# Calculate our projected features
X_slow = map(lambda trj: tica.transform(featurizer.featurize(trj)), trajectories)

</pre>


---
title: HMMs of Molecular Kinetics

<center>
<img src="https://docs.google.com/drawings/d/1cySR96A8koo-xM7CB8srEFf9ScODGtIP-Z_PCH6_rHc/pub?w=754&amp;h=137">
</center>

<pre class="prettyprint" data-lang="python">
import mixtape.ghmm

n_states = 4

model = mixtape.ghmm.GaussianFusionHMM(n_states)
model.fit(X_slow)
</pre>



---
title: Kinetic Modeling of the human Kinome
class: segue dark nobackground



---
title: SRC Kinase

<img height=500 src="/home/kyleb/src/kyleabeauchamp/tICABenchmark/figures/tICA_src_-1alpha_4tics_3states.png">




---
title: Future Work

- Cross-Validated Likelihood
- Pipelining and Concatenating Featurizers
- Randomized Feature Selection
- Outlier Detection of Bad Trajectories


---
title: Counting Transitions

<center>

<img height=300 src=figures/NewPaths-2State.png />

$\downarrow$

$A = (111222222)$

---
title: Counting Transitions

<center>

$A = (111222222)$

$\downarrow$

$C = \begin{pmatrix} C_{1\rightarrow 1} & C_{1\rightarrow 2} \\\ C_{2 \rightarrow 1} & C_{2 \rightarrow 2} \end{pmatrix} =  \begin{pmatrix}2 & 1 \\\ 0 & 5\end{pmatrix}$

</center>

---
title: Estimating Rates

<center>
$C = \begin{pmatrix} C_{1\rightarrow 1} & C_{1\rightarrow 2} \\\ C_{2 \rightarrow 1} & C_{2 \rightarrow 2} \end{pmatrix} = \begin{pmatrix}2 & 1 \\\ 0 & 5\end{pmatrix}$

$\downarrow$

$T = \begin{pmatrix} T_{1\rightarrow 1} & T_{1\rightarrow 2} \\\ T_{2 \rightarrow 1} & T_{2 \rightarrow 2} \end{pmatrix} = \begin{pmatrix}\frac{2}{3} & \frac{1}{3} \\\ 0 & 1\end{pmatrix}$

</center>
