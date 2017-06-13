# GMT\*
This repository houses some example code from the paper "Group Marching Tree: Sampling-Based Approximately Optimal Motion Planning on GPUs" presented at the IEEE International Conference on Robotic Computing (IRC) 2017 by Brian Ichter, Edward Schmerling, and Marco Pavone of Stanford's Autonomous Systems Lab. The paper can be found here <https://arxiv.org/pdf/1705.02403.pdf>.

## Disclaimers
The code contained herein is all of "research grade", in that is messy, largely uncommented, and intended for reference more than anything else. It is unlikely to be useful out of the box, but may help elucidate descriptions in the paper or guide some coding strategies for users implementing GMT\*/similar algorithms. For further disclaimers, please see the __Disclaimer__ section here, <https://github.com/schmrlng/MotionPlanning.jl>.

## Instructions
### What's included
CUDA C Code- this is the meat of the algorithm that runs on the GPU.

MATLAB Code- this code visualizes solutions from the GPU code (output in `soln.txt`)

### Running the code
The code is run through the `mainGround.cu` (for the double integrator) and `mainDubins.cu` (for the Dubins airplane). To compile, type `make <dubins,ground> NUM=<NUM> DIM=<DIM>`, where `<dubins,ground>` is `dubins` or `ground`, `<NUM>` is the number of state space samples, and `<DIM>` is the dimension of the state space (4 for dubins, 6 for double integrator). The code can then be run through `/.ground <file.txt>` where `<file.txt>` is the name of a file which contains the problem specification (currently only implemented for the double integrator; for Dubin's this file can be anything and will not be used). Two example problems are given in `input.txt` and `gates.txt`.

### Adding a new dynamical system
To use some of the code framework to add a new system, one would primarily need to add a new CUDA file similar to `2pBVP.cu` and `dubinsAirplane.cu` which performs all local steering functionality. Depending on the geometry of the system, a new collision checker would also be required (if it can be checked with just straight line paths, perhaps discretized to model a larger curve, then the current collision checker should be useable).

### Changing the problem state
To change the problem state, i.e. Xfree (and Xobs), Xgoal, xinit, one needs to change the sampling bounds at the top of the respective main file (`hi` and `lo`), the obstacles and obstacle count in `obstacles.cu`, and finally `initial` and `goal` in the main file. It is also possible to determine these after compile time by adding some sort of interface, but this is not currently implemented (though I expect very little computation time penalties).

## Notes
Much of the offline precomputation is in no way optimized (particularly for Dubin's). It is currently implemented on the CPU, but could see an additional ~100x speed up if implemented on the GPU. 

Only a group cost threshold factor (lambda) of 1 is implemented for the kinodynamic planning problem, but an example of arbitrary values is shown for the geometric planning problem in `geometricGMT.cu`. The extension should be relatively straight forward. If using a known value of lambda (i.e., once you decide on a value for your problem based on the desire for speed or low-cost), it is likely most performant to hardcode the data structures (as I have done for 1 here). 

Obstacles are currently hardcoded, but with nearly no speed penalty, these can be added at runtime (as can initial and goal states). 

The collision checker is currently fairly basic, in that it only performs a broadphase AABB check for obstacles. It can however be replaced as needed.

## Contact
If you're using the code, let me know! I'm happy to discuss. You can contact me either through github or email me at <ichter@stanford.edu>.

## License
Copyright 2017 Brian Ichter

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
