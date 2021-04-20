# Assignment 2:  Spin-Spin Correlation in the Ising Magnet

The purpose of this assignment will be to guide you through modifiying the `ising_mc.c` program I demonstrated in class so that it computes the spin-spin correlation function.  You will plot this function for various temperatures.

## Spin-spin correlation

Correlation between spins (or between a spin and itself) is the idea that the likelihood that the two spins have relative spin values that occur more frequently together than random chance would suggest.  Consider two spins `i` and `j` with spin variables `si` and `sj` respectively.  The expecations `<si>` and `<sj>` are not really interesting; they are just `<s>` since we compute that by averaging over all spins.  The expectation `<si*sj>`, however, is interesting:  if `i` and `j` are more likely to have the same spin value than not, this number is close to 1; if they are more likely to have opposite spin values, this number is close to -1.  We define the correlation function `C(d)` such that

```
C(d) = <si*sj> - <s>*<s>
```

where `d` is the distance (along a row or column) between `i` and `j`.  

A temperatures above the critical temperature, this correlation function has the property `C(0) = 1` (since a spin is always positively correlated with itself) that dies out to 0 as `d` gets large.  `d` cannot exceed `L/2` for a finite lattice of side-length `L` with periodic boundaries.  Its decay behavior mimics an exponential:

`C(d) ~ exp(-d/xi)`

where `xi` is called the "correlation length".

At temperatures below the critical temperature, `C(d)` no longer decays to 0 since we have large domains.  

## The Assignment

You are to modify `ising_mc.c` provided in this repository to compute the `C(d)` for lattices of size `L`= 20 and for temperatures of 2.3, 3, 5, and 10.  You should generate a single plot in PNG format that plots `C(d)` vs `d` on `[0:10]` for each of these temperatures on the same axes.

## What to do to the code

### In `main()`:

1. Declare arrays for sisj and sisjsum
   ```
   double * sisj, * sisjsum;
   ```

2. Allocate:
   ```
   sisj=(double*)malloc(L/2*sizeof(double));
   sisjsum=(double*)malloc(L/2*sizeof(double));
   ```

3. Sample:
  - include `sisj` as an argument to `samp()`
  - include a for loop from 0 to L/2 to update the sisjsum tally:
    ```
    for (i=0;i<L/2;i++) sisjsum[i]+=sisj[i];
    ```

4. Output:
   ```
   for (i=0;i<L/2;i++)
    fprintf(stdout,"%i  %.5lf\n",i,
      sisjsum[i]/nSamp-ssum*ssum/(nSamp*nSamp));
   ```

### In `samp()`

1. Include `double * sisj` as a parameter declaration and `d` as a local integer

2. Before the loop over all spins, initialize `sisj`
   ```
   for (d=0;d<L/2;d++) sisj[d]=0.0;
   ```

3. Inside the loop over all spins, increment `sisj` by considering only eastern and southern neighbors a distance `d` away:
   ```
   for (d=0;d<L/2;d++) 
	  sisj[d]+=F[i][j]*(F[(i+d)%L][j]+F[i][(j+d)%L]);
   ```

4. At the end of `samp()` divide all elements if `sisj[]` by `2*L*L`
   ```
   for (d=0;d<L/2;d++) 
     sisj[d]/=(2*L*L);
   ```

## Generating a plot

You must write a short python script to generate the required plot.  A workflow you might want to consider might look like this:

1. Run the code for each temperature for at least a few thousand cycles, and copy and paste the numerical output into a unique file for each run; for instace `C-T1.dat`, `C-T2.dat`, etc.

2. Edit the python template `my_plot.py` to read each file into unique arrays:

```python
d1,C1=np.loadtxt('C-T1.dat',unpack=True)
d2,C2=np.loadtxt('C-T2.dat',unpack=True)
(etc)
```

3. ...and for each, issue a plot command, e.g.,

```
ax.scatter(d1,C1,label='T=2.3')
```

## Submitting

Just push this repository once you're happy with your plot.