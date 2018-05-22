# bmm4uml
## UML development for BMM4

to extract signal lifetime ----

+ Branching fraction fit

+ sPlot lifetime fit

----------------------------------------------------------------------------------------

### System requirement 
- gcc 4.8 or newer
- ROOT 6.10/02

### Initial the working area
```
cd your_working_area
git clone https://github.com/kfjack/bmm4uml
cd bmm4uml
mkdir fig input/bmm4
```
Please modify the Makefile to match your system (generally it should work out of box, but just in case!)

There are 3 tools at this moment:
- bmm4fitter: the main fitter and for UML model construction.
- bmm4toystudy: the toy generator and fitter
- bmm4validation: for sPlot lifetime systematic studies 

The wspace_prefit.root contains all of the PDFs, parameters, and constraints. 

### How to generate and fit toy samples

Please take a look at the full command list:
% ./bmm4toystudy
> commands:
> - genfit                                : gen & fit to toy
> - minos                                 : call MINOS for BFs
> - tausplot                              : enable lifetime sPlot study
> - freq                                  : produce frequentist toy (w/ postfit nuisances)
> - bdstat                                : calculate profile likelihood test statistics (F&C study or upper limit for Bd)
> - make_plots                            : produce resulting plots from toy
> - make_demo                             : produce demo projection plots
> - seed=[1234]                           : set the random seed
> - iterations=[10]                       : set # of iterations
> - yield_scale=[1.0]                     : set scaling factor for the yields
> - ws_gen=[wspace_prefit.root]           : set generation workspace
> - ws_fit=[n/a]                          : fit model workspace
> - ws_res=[wspace_toyresult.root]        : resulting workspace file
> - set_genpar [name]=[value]=[float/fix] : set parameter value & state (for gen workspace)
> - set_fitpar [name]=[value]=[float/fix] : set parameter value & state (for fit workspace)

A typical call:<br>
```% ./bmm4toystudy genfit minos tausplot seed=1234 iterations=10 ws_gen=wspace_prefit.root ws_res=wspace_toyresult.root```

This will generate 10 sets of toy, with initial seed of 1234, based on the wspace_prefit.root UML model. 
The fitting model is not set, so it will be the same as the gen model (=wspace_prefit.root).
The minos option means the MINOS command will be called for the two branching fractions.
The tausplot option means the gen & fit also involes sPlot lifetime fit.

The results will be stored in wspace_toyresult.root. 
One can excute the following commend to analyize the results and produce the figures.<br>
```% ./bmm4toystudy make_plots ws_res=wspace_toyresult.root```

This command also supports for multiple result files, for example, if all of the ```wspace_toyresult*.root``` are placed under ```toy/```, one can analyize them in a single call:
```% ./bmm4toystudy make_plots ws_res=toy/wspace_toyresult\*.root```

The resulting figures (mean/error distributions/pulls/etc) will be stored under ```fig/```.

A demonstration of generator mass PDF + overlapping toy events can be generated by 
```% ./bmm4toystudy make_demo```

For advanced users:
- set_genpar/set_fitpar command can change any of the generating/fitting PDF parameters on the fly. 
- yield_scale commmand can scale the overall yields by the given factor in each toy.

### How to (re-)build the UML model 
- Please copy all of the files under ```bmm4/small/180428/s01/``` to ```input/bmm4/```

```% ./bmm4fitter create_base```<br>
This should re-build the full UML model based on the input data card and small trees. Note this will take a long time. 
The default outputs are wspace_base.root and wspace_prefit.root. The contents in wspace_base.root and wspace_prefit.root
should be exactly the same at this stage.

```% ./bmm4fitter update_pdf```<br>
This will only update the PDF of the UML model, reads from wspace_base.root, and saves to wspace_prefit.root. 
This will take a long time.

```% ./bmm4fitter update_config```<br>
This will only update the configuration of the UML model (yields and contraints):
reads from wspace_base.root, and saves to wspace_prefit.root. 

```% ./bmm4fitter update_data```<br>
This will only update the data events storaged in the workspace:
reads from wspace_base.root, and saves to wspace_prefit.root. 
