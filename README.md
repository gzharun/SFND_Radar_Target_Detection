Implementation steps for the 2D CFAR process.
1. First we select input parameters: Training, Guard cells, offset.
2. We form the window basing on the selected Training/Guard cells counts with CUT (cell under test) as a central cell. We slide the window over the signal matrix, summing the values in training cell, ignoring Guard cell and CUT cell values. If values are in db we need to convert them to power in order to sum.
3. When we have a sum of all training values for the particular window position, we normalize this sum dividing by the number of training cells in a window. T = 2*(2*(Gd+Td) + 1)*Tr + 2*(2*Gr + 1)*Td - number of training cells in a window;
4. Now we add an offset to the obtained level of noise. In my case the offset was calculated into db, so I convert the noise level into db and the add the offset. Note, multiplication is replaced with addition in db space.
5. Finally we take signal value in CUT and compare it with final noise level estimation (thresholding the signal). If signal value is less than noise estimation level we discard such signal replacing CUT value with 0.


Selection of Training, Guard cells and offset.
Training cells: are the cells using which we estimate the noise level.
We should select enough of them to get a representative estimation.
If we select too many of them noise spikes will be covered. If the amount will be too small the noise variation will be too strong and such calculation will not be representative. So the amount of training cells is selected basing on the noise amplitude variance.

Guard cells: are required in order to interpret useful signal tails as noise.
The required amount of them is based more on a form of the target signal.
Some what artificial example. If we consider 2d FFT target signal as a 2d Gaussian, I would select guard amount enough to cover [-sigma, +sigma] range.

Offset: training cells summation and normalization averages the estimated noise. It would be nice to form some sort of "signal envelope" over the noise, however without offset the noice estimation looks more like a moving average of the noice. So some spikes are missed and stay above such threshold. We want to "lift" the whole threshold to form "upper envelope". That's what the offset for.


Steps taken to suppress the non-thresholded cells at the edges.
I've used a zero filled matrix for filtered signal. For me it seems the easiest solution.