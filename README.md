# My Master's Thesis
The project architecture is a little overly complex, as the code is meant to run from 
three separate computers (2 physical, 1 virtual), and as such is separated into 3 main parts. 
In order to properly get the whole thing running, you need to get each separate part running properly.

## Host Computer

The host computer is the one running the training of the machine learning model. The training code included runs tensorflow,
but if you have a pre-trained model to test under either Tensorflow, Pytorch, or ONNX, then this step can be skipped.

To execute, make sure to load the packages in environment.yml, and run the jupyter notebook, with the correct kernel.

## Compilation, Synthesis, and Implementation

Compilation, Synthesis, and Implementation is where the model is converted to an hls4ml model,
and is translated to a Vivado project. In order to run this step you need a few more steps.

- install Vivado version 18.1 or .2 (19.1 is too late)
- Make sure Sleep is disabled on the machine
- If in a virtual machine, make sure  sleep is disabled on the host computer as well.
- Load the packages in environment.yml
- place the model in ```models/```
- place the test data under ```test/```
- Edit the ```configurations.json``` with the configurations that you want to test. (see TO INSERT LATER for detailed information on how to edit this). Make sure you update the model and test names in the configuration file.
- execute the program by running  ```python main.py restart```

The code should run through each of the possible configurations provided. If the process is interrupted for any reason, you can execute ```python main.py continue``` to pick up where you left off.
Any incomplete synthesis/implementation runs will be restarted, but complete ones will be skipped.

After implementation is complete on all models, you can copy all the data to the PYNQ-Z2 by executing ```python export.py```. Be sure to edit the IP in the python file to the correct IP of the PYNQ-Z2. 
This will also save the current run of models to the ```backup/``` folder. In order to restore a backup, you can run ```python restore.py 'name of tar file to restore.tar`, and then to re-export to the PYNQ-Z2
execute ```python export.py --skip```. If you do not include the ```--skip``` option, the program will fail.

## Testing
The test rig should be scp'd to the directory on the PYNQ-Z2 that the compilation should start from. If you copied the data in the right format in the previous section, 
it should just run through all the tests one by one if it is run. If you are starting a new compilation run, the PYNQ-Z2 must be restarted, this can be accomplished by running
running ```sudo reboot``` in the terminal of the PYNQ-Z2 (not on the host). This can take a little while depending on how big the models are when unzipped. 

You can retrieve this data from the compilation machine by running ```python do_testing.py```. This will automatically retrieve the data from the PYNQ-Z2 and collate the latency, accuracy, and utilization data from the model.
If you loaded the data from a backup, you must instead run ```python do_testing.py --skip```, or the testing phase will also fail. 

The results will be stored in the ```out_test/``` folder, as ```<DATECODE>.csv``` This data can then be transferred anywhere you need for analysis. The specific analysis code that I ran is found in the __Host Computer__  section above. 

