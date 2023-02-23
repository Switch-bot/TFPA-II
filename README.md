Repository for the "Topics in Particle Physics and Astroparticles II" course from IST.

This will contain all of the content provided by the teachers for the classes, such as templates and small tutorials.

In order to download the contents on this github you can copy it using git clone, but notice that there is no need to download the ROOT folder.
```
git clone https://github.com/Switch-bot/TFPA
```

### Creating a git repository

In order to create a git repository so that you can submit your notebooks you can utilize github.

If you do not already have an account, create one following the usual procedures. For GitHub sign up using your e-mail. If it is an institutional e-mail you might get the option to register as a student and apply for student priviledges. Although you should not need it for this course, you are free to do so.

The first thing you will do once your account is set up is to create a repository where you can upload your notebooks to. In order to do this select the option "Create repository" on the top left of the first screen that shows up after creating your account.

After creating a name for your repository you can create it without any further work, but feel free to look at the options available. Just remember that if you set your repository private you will need to grant access to it so that we can retrieve your projects.

From here, several options appear on how to initiate your repository. Following "or create a new repository on the command line" should yield you with a repository containing a simple file, corresponding to a specific main folder from which you can upload your progress.

The only thing that you need to do before proceeding to create your local version of the repository is to make sure that you have an ssh key so that you can upload it to the online repository (it acts as a security measure).

In order to set one go to the account settings by clicking on the icon on the top right of the page, and then select the "SSH and GPG keys" option on the left bar. Try to do this on a different tab so that you can go back to the initial repository page more easily.

If you already have an SSH key in your computer you can simply click on "New SSH key" and copy it to the appropriate space. Be careful to only copy the public key and not the private one.

If you have never created an SSH key or do not remember the password to your pivate key follow the instructions from "generating SSH keys".

Once you resolve the SSH key issues go back to the repository page

As you can see from the example code, if you want to upload files to your repository save them inside the main folder, and run the command
```
git add path_to_file.txt
```

After adding a file to your repository, then you have to commit your changes through
```
git commit -m "small message that describes the changes in the repository that you commited to"
```

However, this only changes you local version of the repository. In order to upload the changes of your local repository to your online one that everyone can access to you need to push the local version by running
```
git push -u origin main
```

If by some reason you want to remove the file from git, then instead of running the add command, run rm, followed by the usual commit and push.
```
git rm path_to_file.txt
```

If you want to move a file to a new folder within your main git folder you need to both add the folder after the file has been moved, so that git adds the folder and all that is within it, and remove the file from its original folder in git, such as the following example:
```
cd git_folder
mkdir new_folder
mv file.txt new_folder/
git add new_folder
git rm file.txt
git commit -m "moved file.txt from the main git_folder to a new new_folder"
git push -u origin main
```
If you just add the folder without removing the file in its old location, once you push your local version to the online repository you will realize that the file will be duplicated. It will appear on its older location as well as on the newly created folder.

If you just wish to upload your google colabs notebook to your github there is a built in command. In order to do so, while on the google colab notebook, select the "Save a copy in GitHub" option from the "File" bar on the upper left, and simply choose the GitHub repository you wish to upload it to.

If you check your GitHub repository afterwards, the notebook should automatically be there. 

## ROOT Basics

If this is the first time you are using ROOT you can look at Template_0.ipynb, which reads zjet.root.

zjet.ROOT is a very simple root file where all the variables that we need to access have a corresponding branch.

The goal with this file is to get used to the basic functionalities of ROOT: How to read a file, how to check what is inside, and in the case of the dimensions not being explicit, what can we do with it.

The first thing to do is to check how to read what is inside a .root file. A quick way to check this is to utilize the TFile::ls() function, which states what are the imediate contents inside the file. Notice that root stores information in the form of TTrees, which has branches and subsequently leaves.

By following the template you can see that zjet.root has a TTree called Tdata, under which all the information is stored. In order to see what is inside this TTree one can use the Print() function, which most default ROOT objects have. In this case, it prints out all of the branches of Tdata, with their types.

With this file it is easy to plot the variables by using the default TFile.TTree.Draw("TBranch") function. However, if we check the branches which are TVectors, we can see that they all have dimension 2. Why is it?

Lastly, if we want to plot Px vs Py we can use the TH2F class, and fill the histogram one particle at a time by utilizing TH2F::Fill("valuex","valuey") within a loop over all events and all entries for each event.


## ROOT Fitting real data from Dimuon events

The next step is to utilize slightly more complex files, with data that is saved in a realistic format.

In order to do this you can follow Template_RooFit.ipynb, which reads the file Skim4.root. The first blocks of code mimick the previous template, where we can see the main TTree of the file, and its branches. This time around there are only 4 branches, with the 3 most relevant being muonP_p4, muonN_p4 and dimuon_p4.

Unlike for zjet.root, these branches are more complex TLorentzVectors, and as such we cannot use the trivial TTree::Draw("TBranch") function to plot the variables. In this case we need to create a TH1F histogram and fill it one event at a time in order to plot the kinematic properties.

Use the functions corresponding to the [TLorentzVector object](https://root.cern.ch/doc/master/classTLorentzVector.html) in order to plot said properties, like the mass and momenta, and vary the range and scale of the axes in order to get a better sense of the information contained file. In the template there is an example on how to create heterogenous bins in a TH1F object by creating an array where entry x corresponds to the dimension of bin x (remember that python creates lists by default, hence the inclusion of a numpy array).

Compare the mass plots for all 3 branches. By eye, what is the mass value for the muonP and muonN branches, and their respective errors?

Create a new histogram containing the masses of a new TLorentzVector that is the sum of muonP and muonN, and compare it to the mass spectrum for dimuon_p4.

If you wish to utilize C++ there is a dimuon class created by LIP personel with code analogous to the one presented in Template_ROOFit.ipynb, that can be found at "https://github.com/aboletti/LIP-analysis-tutorial".

### Applying fits to the data using RooFit

There are several ways of applying a fit to data using ROOT's capacities. One of these is by taking advantage of [RooFit](https://root.cern/manual/roofit/).

The template exemplifies how to fit the J/Psi peak with a Gaussian function describing the signal, accompanied by a background with an exponential behaviour.

In order to do this we start by defining the mass range to be fitted and create a TH1F histogram with the dimuon mass data. Notice that the TH1F::Fill() function only adds data if the value is within the histogram's limits, and as such there is no need to select the data within the relevant range a priori. We do this because we need to create a RooDataHist object that contains the histogram data to be fitted, and one of the inputs can be the relevant data in a TH1F format.

One peculiarity of RooFit is the need to create a RooRealVar for each variable that we will be using in our models. As such, we aso need to create our "mass" variable, that will be the x axis of our histogram. In order to do so we need to define its minimum and maximum values. Notice that this variable is also used to initialize the RooDataHist object (for initialization purposes the actual object used on the template is the RooArgList "args", but its only entry is the RooRealVar "mass").

The next step is to create our fitting functions. RooFit already has several functions implemented, such as the exponential and gaussian ones that we will be using.

Once again, we need to define a RooRealVar for each variable in our models. Starting with the background model we create a "Lambda" variable, which will be the exponential coeficient of our RooExponential function. Notice that since this is a variable that the model will try to fit, besides defining minimum (-4.0) and maximum (0.0) values we also need to give a starting estimation value (-0.3). By doing something analogous for the Gaussian function (RooGaussian) we have our signal and background functions defined.

The last thing to do before finishing our fitting model and performing the fit is to define variables for the number of predicted signal and background events, which are the RooRealVars n_signal and n_background.

At last we can compile all our functions and variables in a single model through model = RooAddPdf("name", "title", RooArgList(Functions), RooArgList(Vars)).

In order to perform a fit to the model we can simply run model.fitTo(RooDataHist), that spits out all of the relevant information regarding the fit.

The remainder of the code shows how to plot the different RooFit models, the RooDataHist that we created, and a nice legend to accompany it.

### Performing an Unbinned Fit

Performing an Unbinned Fit is not inherently more dificult than the regular histogram fit, but it does provide better results since we are not "compressing" data by binning all the data points, hence leaving more precision.

Although difficult to visualize, this can be interpreted as trying to fit to a histogram where the bins are arbitrarily small so that each bin has no more than one entry. In reality, this is more akin to an optimization problem, where there is a likelyhood function that depends on two different parameter groups, the parameter space of our data points "X={x\_0,x\_1,...,x\_n}" and the free parameters of our model that we will try to fit "Θ={θ\_0, θ\_1, ..., θ\_m}". 

The likelihood function is usualy written in the form of -log[P(X,Θ)], and we utilize iterative minimizing/maximizing methods (Minuit2 in ROOT) in order to obtain the parameters Θ that provide the minimum/maximum Sum over all X in our data of the likelyhood function.

In order to implement this with RooFit, instead of utilizing RooDataHist, we will utilize RooDataSet. The rest of the process remains very similar.

By following the template, you can see that initializing the RooDataSet object is slightly more complicated. In our case, we will do so by first defining an empty RooDataSet, that has as a real variable the "mass", and we will then fill it one entry at a time. Notice that our method allows us to perform the mass cuts a priori to only include the relevant range.

While you can always just load the whole dataset into RooDataSet, values outside of the specified range for the variable are counted as being on the limit, damaging the quality of the data for the fit, unless posterior cuts are made.

With all of this set in place, and performing the usual shennanigans to create our model, we are then able to create the RooDataSet object.

As you can see, plotting a RooDataSet automatically yields a RooDataHist-like plot.

