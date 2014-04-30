% title: Towards Robust Models of Molecular Kinetics
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

- <strike> Systematic Error (Forcefields) </strike>
- Precision (Sampling)
- Quantitative Connection to Experiment (Prediction)
- Interpretation of High-Dimensional Data (Reduction)


---
title: Markov State Models

- Statistical sampling by short simulations
- Predict arbitrary time-correlation functions
- Few-state models for interpretability

<center>
<img height=400 src=figures/chodera2007.png />
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

---
title: The Markov State Model Pipeline

<img src="https://docs.google.com/drawings/d/1T156xWFqyu6rxG2aIkI7_a-BY5e8TvXEZ-Jjjg0XgGc/pub?w=960&amp;h=384">

---
title: Challenges in MSM Construction

- No score function for overall model
- Hyperparameters in every step (tICA, Clustering, microstates, lumping, macrostates)
- Bias variance tradeoff is unwinable (10,000 states)  

---
title: HMMs

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
title: Towards a Data Pipeline for Molecular Kinetics
class: segue dark nobackground

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

<pre class="prettyprint" data-lang="python">

import mdtraj as md

trj = md.load("./Trajectories/trj0.h5")
trj.save("out.pdb")

</pre>

---
title: Watch your memory footprint!

<pre class="prettyprint" data-lang="python">

import mdtraj as md

for chunk in md.iterload("./Trajectories/trj0.h5"):
    do_something(chunk)

</pre>

---
title: Arbitrary Trajectory Buckets

<pre class="prettyprint" data-lang="python">
import glob
import msmbuilder as msmb

filenames = sorted(glob.glob("./Trajectories/*.h5"), key=msmb.utils.keynat)
trajectories = [md.load(filename, stride=stride) for filename in filenames]

</pre>


- Out of core loading is almost finished!

---
title: Analyzing MD Data in MDTraj

<pre class="prettyprint" data-lang="python">

import mdtraj as md

trj = md.load("./Trajectories/trj0.h5")
atom_indices, phi = md.compute_phi(trj)

</pre>

Need a more generalizable approach!



---
title: Featurizers

<pre class="prettyprint" data-lang="python">
import mixtape.featurizer

featurizer = mixtape.featurizer.AtomPairsFeaturizer([[0, 1],[1, 2], [2, 3]], trj0)
X = featurizer.featurize(trj)

array([[ 0.10102207,  0.15920012,  0.16530874]], dtype=float32)
</pre>



---
title: Mass Featurization

<pre class="prettyprint" data-lang="python">
import mixtape.featurizer

featurizer = mixtape.featurizer.AtomPairsFeaturizer([[0, 1],[1, 2], [2, 3]], trj0)
X_list = map(lambda trj: featurizer.featurize(trj), trajectories)

[array([[ 0.10100006,  0.17686693,  0.15999277],
       [ 0.1010002 ,  0.15915124,  0.15710162],
       [ 0.10100002,  0.16685238,  0.1592802 ],
       ..., 
       [ 0.10099998,  0.16684847,  0.15866663],
       [ 0.10100007,  0.16221181,  0.16088213],
       [ 0.10099998,  0.1550298 ,  0.17153391]], dtype=float32),
 array([[ 0.10099967,  0.17153431,  0.17215465],
       [ 0.10100014,  0.15179284,  0.16040237],
       [ 0.10100014,  0.16157277,  0.16634522],
       ..., 
       [ 0.10100002,  0.17515557,  0.16074601],
       [ 0.101     ,  0.15392499,  0.1583055 ],
       [ 0.10100027,  0.17150088,  0.15800913]], dtype=float32)]

</pre>


---
title: tICA

<pre class="prettyprint" data-lang="python">
import mixtape.tica

tica = mixtape.tica.tICA()
# Calculate correlation functions and solve the tICA eigenproblem
map(lambda trj: tica.partial_fit(featurizer.featurize(trj)), trajectories)
# Calculate our projected features
projected = map(lambda trj: tica.transform(featurizer.featurize(trj)), trajectories)

</pre>



---
title: HMMs

<pre class="prettyprint" data-lang="python">
import mixtape.ghmm

n_states = 4

model = mixtape.ghmm.GaussianFusionHMM(n_states)
model.fit(X_all)
model.timescales_
</pre>

---
title: Kinetic Modeling of the human Kinome
class: segue dark nobackground

---
title: SRC Kinase

<img height=500 src="/home/kyleb/src/kyleabeauchamp/tICABenchmark/figures/tICA_src_-1alpha_4tics_3states.png">




---
title: Future Work

- Evaluate Cross-Validated Likelihood Models
- Pipelining and Concatenating Featurizers
- Randomized Feature Selection

