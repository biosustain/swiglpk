swiglpk
=======

### Why?
_swiglpk_ is not a high-level wrapper for GLPK (take a look at [optlang](https://github.com/biosustain/optlang) if you are interested in a python-based mathematical programming language). It just provides plain vanilla [swig](http://www.swig.org/) bindings to the underlying C library. In constrast to other GLPK wrappers for python (e.g. [PyGLPK](http://tfinley.net/software/pyglpk/), [Python-GLPK](http://www.dcc.fc.up.pt/~jpp/code/python-glpk/), [ctypes-glpk](https://code.google.com/p/ctypes-glpk/), [ecyglpki](https://github.com/equaeghe/ecyglpki) etc.) it is fairly version agnostic: it will try to guess the location of the glpk.h header file  (using `which glpsol`) and then compile the extension for your particular GLPK installation.

### Dependencies
* GLPK (swiglpk has been tested with versions 4.45 and 4.52 on OS X)
* swig (swiglpk has been tested tested with version 3.0.2 on OS X)

If you're on OS X, swig and GLPK can easily be installed with [homebrew](http://brew.sh/).

    brew install swig glpk
    
If you're using ubuntu linux, you can install swig and GLPK using `apt-get`.

    apt-get install glpk-utils libglpk-dev swig

### Installation

    python setup.py install

### Example
Running the following (slightly adapted) example from the [GLPK manual](http://kam.mff.cuni.cz/~elias/glpk.pdf) ...

	from swiglpk import *
	
	ia = intArray(1+1000); ja = intArray(1+1000);
	ar = doubleArray(1+1000);
	lp = glp_create_prob();
	glp_set_prob_name(lp, "sample");
	glp_set_obj_dir(lp, GLP_MAX);
	glp_add_rows(lp, 3);
	glp_set_row_name(lp, 1, "p");
	glp_set_row_bnds(lp, 1, GLP_UP, 0.0, 100.0);
	glp_set_row_name(lp, 2, "q");
	glp_set_row_bnds(lp, 2, GLP_UP, 0.0, 600.0);
	glp_set_row_name(lp, 3, "r");
	glp_set_row_bnds(lp, 3, GLP_UP, 0.0, 300.0);
	glp_add_cols(lp, 3);
	glp_set_col_name(lp, 1, "x1");
	glp_set_col_bnds(lp, 1, GLP_LO, 0.0, 0.0);
	glp_set_obj_coef(lp, 1, 10.0);
	glp_set_col_name(lp, 2, "x2");
	glp_set_col_bnds(lp, 2, GLP_LO, 0.0, 0.0);
	glp_set_obj_coef(lp, 2, 6.0);
	glp_set_col_name(lp, 3, "x3");
	glp_set_col_bnds(lp, 3, GLP_LO, 0.0, 0.0);
	glp_set_obj_coef(lp, 3, 4.0);
	ia[1] = 1; ja[1] = 1; ar[1] = 1.0; # a[1,1] = 1
	ia[2] = 1; ja[2] = 2; ar[2] = 1.0; # a[1,2] = 1
	ia[3] = 1; ja[3] = 3; ar[3] = 1.0; # a[1,3] = 1
	ia[4] = 2; ja[4] = 1; ar[4] = 10.0; # a[2,1] = 10
	ia[5] = 3; ja[5] = 1; ar[5] = 2.0; # a[3,1] = 2
	ia[6] = 2; ja[6] = 2; ar[6] = 4.0; # a[2,2] = 4
	ia[7] = 3; ja[7] = 2; ar[7] = 2.0; # a[3,2] = 2
	ia[8] = 2; ja[8] = 3; ar[8] = 5.0; # a[2,3] = 5
	ia[9] = 3; ja[9] = 3; ar[9] = 6.0; # a[3,3] = 6
	glp_load_matrix(lp, 9, ia, ja, ar);
	glp_simplex(lp, None);
	Z = glp_get_obj_val(lp);
	x1 = glp_get_col_prim(lp, 1);
	x2 = glp_get_col_prim(lp, 2);
	x3 = glp_get_col_prim(lp, 3);
	print "\nZ = %g; x1 = %g; x2 = %g; x3 = %g\n" % (Z, x1, x2, x3);
	glp_delete_prob(lp);

... will produce the following output (the example can also be found at examples/example.py):

	GLPK Simplex Optimizer, v4.52
	3 rows, 3 columns, 9 non-zeros
	*     0: obj =   0.000000000e+00  infeas =  0.000e+00 (0)
	*     2: obj =   7.333333333e+02  infeas =  0.000e+00 (0)
	OPTIMAL LP SOLUTION FOUND
	
	Z = 733.333; x1 = 33.3333; x2 = 66.6667; x3 = 0


