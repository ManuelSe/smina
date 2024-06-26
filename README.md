# Smina Fork


This is a private fork of smina. The only modification is that here, all explicit hydrogen atoms are retained while they are removed in the original code. The original code was cloned from `http://git.code.sf.net/p/smina/code`.

To build smina from source, follow these steps:
1) Install dependencies:
```bash
sudo apt install libeigen3-dev cmake libboost-dev swig libxml2-dev libboost-all-dev
```
2) Install openbabel if not already done:
```bash
git clone https://github.com/openbabel/openbabel.git
cd openbabel
mkdir build
cd build
cmake .. -DRUN_SWIG=1  -DWITH_MAEPARSER=1
make -j4
sudo make install
```
3) Build smina from source:
```bash
git clone https://github.com/ManuelSe/smina.git
cd smina
mkdir build
cd build
cmake ..
make -j4
```
4) Optional: copy smina executable to `/usr/bin/` to make it available to all users:
```bash
sudo cp smina /usr/bin/
```

 --- 

smina is a fork of Autodock Vina (http://vina.scripps.edu/) that 
focuses on improving scoring and minimization.  Changes from the
standard Vina (version 1.1.2) include:
 -comprehensive support for ligand molecular formats (via OpenBabel)*
 -support for multi-ligand files (e.g., an sdf file)*
 -support for addition term types (e.g., desolvation, electrostatics)
 -support for custom, user-parameterized scoring functions (see --custom_scoring)
 -automatic box creation based on a user-specified bound ligand
 -allow the output of more than 20 docking poses
 -vastly improved minimization algorithms (--minimize goes to convergence)
 -*experimental* easily define flexible residues of receptor (--flexres and --flexdist)
 
For workflows where AutoDock Vina is used for minimization (local_only) 
as opposed to of docking, these changes make Vina much easer to use and 
10-20x faster. Docking performance is about the same since partial charge 
calculation and file i/o isn't such a big part of the performance.

If you find smina useful, please cite our paper: 
http://pubs.acs.org/doi/abs/10.1021/ci300604z

*Non-pdbqt ligand files must have partial charges added.  This is done
using OpenBabel and will get different results than the prepare_ligand4.py
script that comes with AutoDock Tools.

Pre-built binaries are provided that were built on Ubuntu 14.04.  The main
dependencies are boost (1.54) and openbabel.  A static binary is provided
in case these dependencies cannot be met (however, it probably still will
not work if the kernel is older than 2.6.24).

If building from source:
```
apt install git libboost-all-dev libopenbabel-dev build-essential libeigen3-dev
git clone https://git.code.sf.net/p/smina/code smina-code
cd smina-code
mkdir build
cd build
cmake ..
make -j12
```

*Note:*  OpenBabel3 is required.

Input:
  -r [ --receptor ] arg rigid part of the receptor 
  --flex arg            flexible side chains, if any 
  -l [ --ligand ] arg   ligand(s)
  --flexres arg         flexible side chains specified by comma separated list 
                        of chain:resid
  --flexdist_ligand arg Ligand to use for flexdist
  --flexdist arg        set all side chains within specified distance to 
                        flexdist_ligand to flexible

Search space (required):
  --center_x arg        X coordinate of the center
  --center_y arg        Y coordinate of the center
  --center_z arg        Z coordinate of the center
  --size_x arg          size in the X dimension (Angstroms)
  --size_y arg          size in the Y dimension (Angstroms)
  --size_z arg          size in the Z dimension (Angstroms)
  --autobox_ligand arg  Ligand to use for autobox
  --autobox_add arg     Amount of buffer space to add to auto-generated box 
                        (default +4 on all six sides)
  --no_lig              no ligand; for sampling/minimizing flexible residues

Scoring and minimization options:
  --scoring arg                specify alternative builtin scoring function [e.g. vinardo]
  --custom_scoring arg         custom scoring function file
  --custom_atoms arg           custom atom type parameters file
  --score_only                 score provided ligand pose
  --local_only                 local search only using autobox (you probably 
                               want to use --minimize)
  --minimize                   energy minimization
  --randomize_only             generate random poses, attempting to avoid 
                               clashes
  --minimize_iters arg (=0)    number iterations of steepest descent; default 
                               scales with rotors and usually isn't sufficient 
                               for convergence
  --accurate_line              use accurate line search
  --minimize_early_term        Stop minimization before convergence conditions 
                               are fully met.
  --approximation arg          approximation (linear, spline, or exact) to use
  --factor arg                 approximation factor: higher results in a 
                               finer-grained approximation
  --force_cap arg              max allowed force; lower values more gently 
                               minimize clashing structures
  --user_grid arg              Autodock map file for user grid data based 
                               calculations
  --user_grid_lambda arg (=-1) Scales user_grid and functional scoring
  --print_terms                Print all available terms with default 
                               parameterizations
  --print_atom_types           Print all available atom types

Output (optional):
  -o [ --out ] arg      output file name, format taken from file extension
  --out_flex arg        output file for flexible receptor residues
  --log arg             optionally, write log file
  --atom_terms arg      optionally write per-atom interaction term values
  --atom_term_data      embedded per-atom interaction terms in output sd data

Misc (optional):
  --cpu arg                  the number of CPUs to use (the default is to try 
                             to detect the number of CPUs or, failing that, use
                             1)
  --seed arg                 explicit random seed
  --exhaustiveness arg (=8)  exhaustiveness of the global search (roughly 
                             proportional to time)
  --num_modes arg (=9)       maximum number of binding modes to generate
  --energy_range arg (=3)    maximum energy difference between the best binding
                             mode and the worst one displayed (kcal/mol)
  --min_rmsd_filter arg (=1) rmsd value used to filter final poses to remove 
                             redundancy
  -q [ --quiet ]             Suppress output messages
  --addH arg                 automatically add hydrogens in ligands (on by 
                             default)

Configuration file (optional):
  --config arg          the above options can be put here

Information (optional):
  --help                display usage summary
  --help_hidden         display usage summary with hidden options
  --version             display program version






The custom scoring file consists of a weight, term description, and optional
comments on each line.  The numeric parameters of the term description 
can be varied to parameterize the scoring function.  
Use --print_terms to see all available terms.

Example (all weights 1.0, all term types listed):
1.0  ad4_solvation(d-sigma=3.6,_s/q=0.01097,_c=8)  desolvation, q determines whether value is charge dependent
1.0  ad4_solvation(d-sigma=3.6,_s/q=0.01097,_c=8)  in all terms, c is a distance cutoff
1.0  electrostatic(i=1,_^=100,_c=8)	i is the exponent of the distance, see everything.h for details
1.0  electrostatic(i=2,_^=100,_c=8)
1.0  gauss(o=0,_w=0.5,_c=8)		o is offset, w is width of gaussian
1.0  gauss(o=3,_w=2,_c=8)
1.0  repulsion(o=0,_c=8)	o is offset of squared distance repulsion
1.0  hydrophobic(g=0.5,_b=1.5,_c=8)		g is a good distance, b the bad distance
1.0  non_hydrophobic(g=0.5,_b=1.5,_c=8)	value is linearly interpolated between g and b
1.0  vdw(i=4,_j=8,_s=0,_^=100,_c=8)	i and j are LJ exponents
1.0  vdw(i=6,_j=12,_s=1,_^=100,_c=8) s is the smoothing, ^ is the cap
1.0  non_dir_h_bond(g=-0.7,_b=0,_c=8)	good and bad
1.0  non_dir_anti_h_bond_quadratic(o=0.4,_c=8) like repulsion, but for hbond, don't use	
1.0  non_dir_h_bond_lj(o=-0.7,_^=100,_c=8)	LJ 10-12 potential, capped at ^
1.0 acceptor_acceptor_quadratic(o=0,_c=8)	quadratic potential between hydrogen bond acceptors
1.0 donor_donor_quadratic(o=0,_c=8)	quadratic potential between hydroben bond donors
1.0  num_tors_div	div constant terms are not linearly independent
1.0  num_heavy_atoms_div	
1.0  num_heavy_atoms	these terms are just added
1.0  num_tors_add
1.0  num_tors_sqr
1.0  num_tors_sqrt
1.0  num_hydrophobic_atoms
1.0  ligand_length


Atom Type Terms
You can define custom functionals between pairs of specific atom types:

atom_type_gaussian(t1=,t2=,o=0,_w=0,_c=8)	guassian potential between specified atom types
atom_type_linear(t1=,t2=,g=0,_b=0,_c=8)	linear potential between specified atom types
atom_type_quadratic(t1=,t2=,o=0,_c=8)	quadratic potential between specified atom types
atom_type_inverse_power(t1=,t2=,i=0,_^=100,_c=8)	inverse power potential between specified atom types

Use --print_atom_types to see all available atom types. Note that hydrogens
are always ignored despite having atom types.

Note that these are all symmetric - you do not need to specify a term for
(t1,t2) and (t2,t1) (doing so will just double the value of the potential).

Example:  Faking covalent docking.  Consider this custom scoring function:
-0.035579    gauss(o=0,_w=0.5,_c=8)
-0.005156    gauss(o=3,_w=2,_c=8)
0.840245     repulsion(o=0,_c=8)
-0.035069    hydrophobic(g=0.5,_b=1.5,_c=8)
-0.587439    non_dir_h_bond(g=-0.7,_b=0,_c=8)
1.923        num_tors_div
-100.0       atom_type_gaussian(t1=Chlorine,t2=Sulfur,o=0,_w=3,_c=8)

All but the last term are the default Vina scoring function.  That last
term applies a very strong guassian potential between Cl and S.  In the
system we were docking, we modified the two atoms we wanted to be next
to each other (because they are known form a covalent bond) to be a chlorine
and a sulfur (the system is not physical, but that's okay).  Since these
were the only Cl and S in the system and the term has a large weight,
the best docking solutions all placed these atoms together.
The final poses could then be rescored/minimized using just the default
scoring function.
