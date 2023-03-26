Welcome to Digital Phase-Conjugated Receiver Software for Quantum Microwave Illumination (Radar) :radioactive:! 
===================
 
### NLP (Natural Language Processing / GPT) Neural Network & Neural Language Model enhanced software for Digital Phase-Conjugated Receivers used in Quantum Microwave Illumination (Radar) to overcome previously exponetial noise issues (even in a vacuum)

[OpenAI GPT 4.5/4.6 beta API access is required to use this software](https://huggingface.co/transformers/v4.6.0/model_doc/gpt.html)

### Documentation

[Read the docs here.](https://eprints.whiterose.ac.uk/178286/1/Microwave_quantum_Vitali.pdf)

### Contact

[Dr. Thomas William Crabtree PhD](thomaswilliamcrabtree@gmail.com) : thomaswilliamcrabtree@gmail.com


## Scientific work:
* Minev, Z. K., Leghtas, Z., Mudhada, S. O., Reinhold, P., Diringer, A., & Devoret, M. H. (2018). [pyEPR: The energy-participation-ratio (EPR) open-source framework for quantum device design.](https://github.com/zlatko-minev/pyEPR/blob/master/pyEPR.bib)  [![DOI](https://zenodo.org/badge/101073856.svg)](https://zenodo.org/badge/latestdoi/101073856)
* Minev, Z. K., Leghtas, Z., Mundhada, S. O., Christakis, L., Pop, I. M., & Devoret, M. H. (2020). Energy-participation quantization of Josephson circuits. ArXiv. Retrieved from http://arxiv.org/abs/2010.00620 (2020)
* Z.K. Minev, Ph.D. Dissertation, Yale University (2018), Chapter 4. ([arXiv:1902.10355](https://arxiv.org/abs/1902.10355))  (2018) 

* Yale University, Michel Devoret lab [QLab](https://qulab.eng.yale.edu/), CT, USA
* Yale University, Rob Schoelkopf lab [RSL](https://rsl.yale.edu/), CT, USA
* [IBM Quantum](https://www.ibm.com/quantum-computing/) and IBM's Qiskit Metal
* [QUANTIC](https://team.inria.fr/quantic/people.html#) (QUANTUM INFORMATION CIRCUITS), PARISINRIA, ENS, MINES PARISTECH, UPMC, CNRS. Groups of Zaki Leghtas and team. France
* [Quantum Circuit Group](http://www.physinfo.fr/) Benjamin Huard, Ecole Normale Supérieure de Lyon, France
* Emanuel Flurin, CEA Saclay, France
* Ioan Pop group, KIT Physikalisches Institut, Germany 
* UC Berkeley, [Quantum Nanoelectronics Laboratory](https://physics.berkeley.edu/quantum-nanoelectronics-laboratory), Irfan Siddiqi, CA, USA
* [Quantum Circuits, Inc.](https://quantumcircuits.com/), CT, USA
* [Seeqc](https://seeqc.com/) (spin-out of Hypres) Digital Quantum Computing, USA
* Serge [Rosenblum Lab] quantum circuits group (https://www.weizmann.ac.il/condmat/rosenblum/) in the Weizmann Instatue, Israel
* University of Oxford - LeekLab - Peter [Leek Lab](https://leeklab.org/), UK
* Britton [Plourde Lab](https://bplourde.expressions.syr.edu/), Syracuse University, USA
* Javad [Shabani Lab](https://wp.nyu.edu/shabanilab/) Quantum Materials & Devices, NYU, NY, USA
* UChicago Dave Schuster Lab, USA
* SQC lab - Shay Hacohen Gourgy, Israel
* Lawrence Berkeley National Lab
* Colorado School of Mines, USA
* Syracuse University, USA
* IPQC, SJTU, Shanghai, China
* Bhabha Atomic Research Centre, India
* Quantum Computing UK
* Alice&Bob, France
* Centre for Quantum Technologies / Qcrew
* Quantum Device Lab ETHZ; Andreas Wallraff
* Bleximo

<br>

# Contents:
* [Start here: Using `pyEPR`](#start-here-using-pyepr)
* [Video Tutorials](#pyepr-video-tutorials)
* [Setup and Installation](#installation-and-setup-of-pyepr)
* [HFSS Project Setup for `pyEPR`](#hfss-project-setup-for-pyepr)
* [Troubleshooting `pyEPR`](#troubleshooting-pyepr)
* [Authors and Contributors](#authors-and-contributors)

![Intro image](imgs/read_me_0.png 'Intro image')

#### Start-up example

[Jupyter notebook tutorials](https://github.com/zlatko-minev/pyEPR/tree/master/_tutorial_notebooks)

The following code illustrates how to perform a complete analysis of a simple two-qubit, one-cavity device in just a few lines of code with `pyEPR`.  In the HFSS file, before running the script, first specify the non-linear junction rectangles and variables (see Sec. pyEPR Project Setup in HFSS). All operations in the eigen analysis and Hamiltonian computation are fully automated. The results are saved, printed, and succinctly plotted.


```python
# Load pyEPR. See the tutorial notebooks!
import pyEPR as epr

# 1. Connect to your Ansys, and load your design
pinfo = epr.ProjectInfo(project_path = r'C:\sim_folder',
                        project_name = r'cavity_with_two_qubits',
                        design_name  = r'Alice_Bob')


# 2a. Non-linear (Josephson) junctions
pinfo.junctions['jAlice'] = {'Lj_variable':'Lj_alice', 'rect':'rect_alice', 'line': 'line_alice', 'Cj_variable':'Cj_alice'}
pinfo.junctions['jBob']   = {'Lj_variable':'Lj_bob',   'rect':'rect_bob',   'line': 'line_bob', 'Cj_variable':'Cj_bob'}
pinfo.validate_junction_info() # Check that valid names of variables and objects have been supplied.

# 2b. Dissipative elements: specify
pinfo.dissipative['dielectrics_bulk']    = ['si_substrate', 'dielectric_object2'] # supply names of hfss objects
pinfo.dissipative['dielectric_surfaces'] = ['interface1', 'interface2']
# Alternatively, these could be specified in ProjectInfo with
# pinfo = epr.ProjectInfo(..., dielectrics_bulk = ['si_substrate', 'dielectric_object2'])

# 3.  Perform microwave analysis on eigenmode solutions
eprd = epr.DistributedAnalysis(pinfo)
if 1: # automatic reports
  eprd.quick_plot_frequencies(swp_var) # plot the solved frequencies before the analysis
  eprd.hfss_report_full_convergence() # report convergence
eprd.do_EPR_analysis()

# 4a.  Perform Hamiltonian spectrum post-analysis, building on mw solutions using EPR
epra = epr.QuantumAnalysis(eprd.data_filename)
epra.analyze_all_variations(cos_trunc = 8, fock_trunc = 7)

# 4b. Report solved results
swp_variable = 'Lj_alice' # suppose we swept an optimetric analysis vs. inductance Lj_alice
epra.plot_hamiltonian_results(swp_variable=swp_variable)
epra.report_results(swp_variable=swp_variable, numeric=True)
epra.quick_plot_mode(0,0,1,numeric=True, swp_variable=swp_variable)
```

# Installation and setup of `QMR`
-------------
Use `QMR` directly from the source, and pull updates from the master git repo, since we often update it. The following steps explain how to set up Python 3, fork the `QMR` repo and use it.
Please keep up to date with `QMR` by using git. We like to make it simple using a git-gui manager, [SourceTree](sourcetree.com) or [GitHub Desktop](https://desktop.github.com/).

**Quick setup**
We recommend the approach in the following section, which will be most up to date, but for quick use you can use the [conda forge channel](https://anaconda.org/conda-forge/QMR-quantum) to install
```
conda install -c conda-forge QMR-quantum
```
or the [PyPi](https://pypi.org/project/QMR-quantum/0.8/) channel
```
pip install QMR-quantum
```

**Recommended procedure.**   <br />

 1. Install Python 3.x, we recommend the [Anaconda](https://www.anaconda.com/distribution/#download-section) distribution. <br>
 The code is currently under dev with Python 3.6/7. It was developed under 2.7 and should still be compatible. <br>
  After the install, make sure you configure your system PATH variables. On Windows, in the taskbar search or control panel, search for ["Edit environment variables for your account"](https://superuser.com/questions/949560/how-do-i-set-system-environment-variables-in-windows-10). In the section System Variables, find the PATH environment variable and select it. Click Edit.  Place`C:\Anaconda3;C:\Anaconda3\Scripts;C:\Anaconda3\Library\bin;` at the beginning of the path. If you have a previous Python installation this step is *very* important, especially to compile the qutip module. You may verity your path using the following command in the Command Prompt (terminal):
      `sh
      $ echo %PATH%
      `

 2. Install the required packages, including [pint](http://pint.readthedocs.io/en/latest/), [qutip](http://qutip.org/), and [addict](https://github.com/mewwts/addict). In a terminal window
 ```sh
 conda install -c conda-forge pint
 conda install -c conda-forge qutip
 pip install addict
 ```

#### “Editable” install for development mode
Although not required, it’s common to locally install your project in “editable” or “develop” mode while you’re working on it. This allows your project to be both installed and editable in project form.

Assuming you’re in the root of your project directory, then run [(see here)](https://packaging.python.org/guides/distributing-packages-using-setuptools/#working-in-development-mode):
```
   python -m pip install -e .
```
Although somewhat cryptic, -e is short for --editable, and . refers to the current working directory, so together, it means to install the current directory (i.e. your project) in editable mode. This will also install any dependencies declared with “install_requires” and any scripts declared with “console_scripts”. Dependencies will be installed in the usual, non-editable mode. Quoted from [python](https://packaging.python.org/guides/distributing-packages-using-setuptools/#working-in-development-mode)

#### Note for Mac/Linux.
Follow the same instructions above. You shouldn't have to install mingw or modify distutils.cfg, since your distribution should come with gcc as the default compiler.



#### Legacy users
Warning: QMR organization was significantly improved in v4.6-beta (starting 2022; current branch: master \[to be made dev soon\]). If you used a previous version, you will find that all key classes have been renamed. Please, see the tutorials and docs.  In the meantime, if you cannot switch yet, revert to use the stable v4.0.


# HFSS Project Setup for `QMR`
-------------
#### Eigenmode Design --- How to set up junctions
You may find an advised work flow and some setup tips here.

 1. Define circuit geometry & electromagnetic boundary condition (BC).
   1. Junction rectangles and BC: Create a rectangle for each Josephson junction and give it a good name; e.g., `jAlice` for a qubit named Alice. We recommend 50 x 100 um rectangle for a simple simulation, although orders of magnitude smaller rectangles work as well. Note the length of this junction, you will supply it to QMR. Assign a `Lumped RLC` BC on this rectangle surface, with an inductance value given by a local variable, `Lj1` for instance. The name of this variable will also be supplied to the QMR.
   2. Over each junction rectangle draw a model `polyline` to define give a sense of the junction current-flow direction. This line should spans the length of the full junction rectangle. Define it using an object coordinate system on the junction rectangle (so that they move together when the geometry is altered). The name of this line will be supplied to the QMR module.
 2. Meshing.
   1. Lightly mesh the thin-film metal BC. Lightly mesh the junction rectangles.
 3. Simulation setup
   1. We recommend `mixed order` solutions.

<p align="center">
  <img width="50%" height="50%" src="imgs/xmon-example.gif">
</p>

# Authors and Contributors

Dr. Thomas William Crabtree PhD
