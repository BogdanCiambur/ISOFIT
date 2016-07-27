# Isofit
IRAF Isofit and Cmodel: Fit Complex Isophotes and Construct Realistic Galaxy Models

----------***************INSTRUCTIONS ON HOW TO INSTALL ISOFIT ON A LINUX MACHINE********************----------------

               __________________
############## |A) Installation | #################
               —————————————————-

1. Install the preferred version of the IRAF software.
2. In the IRAF installation directory (e.g, “/iraf”), paste the following script called "environ.sh", which contains:

	export IRAF_DIR=/iraf
	export iraf=$IRAF_DIR/iraf/
	export MACH=linux64
	export IRAFARCH=linux64
	source ${iraf}/unix/hlib/irafuser.sh
	export C_INCLUDE_PATH=$IRAF_DIR/include
	export PATH=$IRAF_DIR/bin:$PATH

	export tables=$IRAF_DIR/iraf/extern/tables/
	export stsdas=$IRAF_DIR/iraf/extern/stsdas/

[NB the above is valid for architecture “linux64” (lines 3 and 4)]

3. Make a local copy of the "stsdas" directory, (I call it "stsdasbc" to avoid conflicts). Here, replace the "isophote" 
directory with the one provided. (I's located in "stsdasbc/pkg/analysis/isophote").

4. Copy the binary "x_isophote.e" (provided) in the "stsdasbc/bin.linux64" directory (or bin.'architecture', for whatever 
linux architecture you have on your machine).

4. Create a bash command to initialise the new package. Edit your “.bashrc” file to contain the function...

	function isofit {

        	source /iraf/environ.sh
        	export stsdasbc=/home/stsdasbc/
	}

5. ... then type in the terminal 

	 source ~/.bashrc
	 isofit

[Note: You need to type isofit (or whatever you named the function in #4) every time you want to use Isofit (to load the 
environment). Otherwise it will use the old Ellipse version.]

6. Then in a directory of your choice do

mkdir IRAF
cd IRAF
mkiraf

(select xgterm as usual)

7. Edit your “login.cl” file to contain (at the top):

reset stsdasbc = /path/to/stsdasbc/
task stsdasbc.pkg = stsdasbc$stsdasbc.cl

Then type cl and use the isophote package as usual, noting the remarks below.

               __________ 
############## | B) Use | #################
               ——————————

1. You should use isofit the same way you would ellipse, i.e.

	cl
	stsdasbc
	analysis
	isophote

	epar ellipse

However, in setting the “samplepar” field, you must put at least the harmonics “2 3 4” (without quotes). This
is to ensure compatibility with the model construction task cmodel.

If you decide to use higher harmonics, they must be in ascending order, even and odd.

The practical limit of harmonics is ~10-12 total (because they are fit simultaneously, unlike in Ellipse), 
and generally, with galaxies that are symmetric 
about the major/minor axis, it's only the even harmonics that are useful (such as 
the b4, b6 etc.). But make sure that the “samplepar” field “harmonics” is not left empty 
but always has “2 3 4” minimum. 

Example 1.) Say you need to go up to the 5th harmonic in odd and up to 8th in even. Then you
should put 2 3 4 5 6 8 (ascending odd, ascending even.)

Example 2.) Say you don't care about odd harmonics but want to go up to 12th in even. Then
you should put 2 3 4 6 8 10 12.

Example 3.) 2 3 4 7 8 10 is wrong because the odd harmonics sequence is missing 5. Always
go in ascending order and include all harmonics >4 up to the upper limit (both odd and even).

Finally, sometimes (e.g. if the image quality is low) including too many harmonics makes things
too degenerate and the fits may crash with a “segmentation fault”. In this case I'd suggest to 
reduce the number of harmonics you use, or change the sampling “SMA” step - depends on the case.

2. You can use “cmodel” to construct the 2D model in the same way as you would “bmodel”.

4. The output file of isofit is slightly different than ellipse: 

-----------------------------------------------------------------IMPORTANT: the values of
A3, B3, A4, B4 are NOT the same as in Ellipse output, but represent different numbers. To 
actually get the Fourier coefficients, you need to look at the end of the output table, where 
they are all marked as AI3, BI3 etc, and are NOT the dimensionless quantities that ellipse produces
but have units of intensity. To get the dimensionless coefficients you need to divide AI#
and BI# by the gradient GRAD and semi-major axis length SMA.

Apart from the harmonics, the rest of the output table has the same structure as that of Ellipse.

