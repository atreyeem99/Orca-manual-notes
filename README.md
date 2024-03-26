# 1) To moniter output file
```
tail -f filename.out
```
# 2) Natural transition orbitals
```
! PBE D3ZERO def2-SVPD tightscf
%tddft
nroots 5
DoNTO true
NTOStates 1,2,3
NTOThresh 1e-4
end
* xyz 0 1 inp.xyz
```
# 3) RI approximation (AO basis)
- The RI approximation saves a large amount of time while giving close to identical results (the errors will usually be <0.1 eV) and is generally recommended.
# 4) RI approximation (MO basis)
```
! RKS B3LYP/G SV(P) def2-SVP/C TightSCF
%tddft
mode
nroots
end
riints
8
* int 0 1 inp.xyz
```
-note: Do not forget to assign a suitable auxiliary basis set! If Hartree-Fock exchange is present (HF or hybrid-DFT) these are the auxiliary bases optimized for correlation while for non-hybrid functionals the standard RI-J bases are suitable.
# 5) Potential energy surface scans
```
! def2-TZVPD
%method scanguess pmodel # this assignment forces a PModel guess at each step
                         # which is often better if diffuse functions are present
end
%cis NRoots
7
end
%paras rCO = 0.85,1.45,21;
end
* xyz 0 1
O 0 0 0
C 0 0 {rCO}
*
```
# Excited states calculation with ADC2
```
! ADC2 cc-pVDZ cc-pVDZ/C TightSCF
%mdci
nroots 9
end
*xyz 0 1 geom.xyz
```
# Excited states with EOM-CCSD
```
! RHF EOM-CCSD cc-pVDZ TightSCF
%mdci
nroots 9
end
*xyz 0 1 geom.xyz
```
-IP and EA versions can be called using the keywords IP-EOM-CCSD and EA-EOM-CCSD respectively. 
-For open-shell systems (UHF reference wavefunction), IP/EA-EOM-CCSD calculations require an additional keywords. 
-An IP/EA calculation involving the removal/attachment of an ↵ electron is requested by setting the DoAlpha keyword to true in the %mdci block, while setting the DoBeta keyword to true selects an IP/EA calculation for the removal/attachment of a electron.
-Note that DoAlpha and DoBeta cannot simultaneously be true and that the calculation defaults to one in which DoAlpha is true if no keyword is specified on input.
```
! IP-EOM-CCSD cc-pVDZ
%mdci
DoAlpha true
NRoots 7
end
*xyz 0 3
O
0.0 0.0 0.0
O
0.0 0.0 1.207
*
```
Both DLPNO-CCSD(T) and DLPNO-MP2 are linear-scaling methods (albeit the former has a larger prefactor). This means that if a DLPNO-MP2 calculation can be performed, DLPNO-CCSD(T) is often going to be within reach, too. However, CCSD(T) is generally much more accurate than MP2 and thus should be given preference.

ORCA features a module to perform TD-DFT, single-excitation CI (CIS) and RPA. The module works with either closed-shell (RHF or RKS) or unrestricted (UHF or UKS) reference wavefunctions. For DFT models the module automatically chooses TD-DFT and for HF wavefunctions the CIS model. If the RI approximation is used in the SCF part it will also be used in the excited states calculation.

# Approximate Second Order SCF (SOSCF)
SOSCF is an approximately quadratically convergent variant of the SCF procedure [144, 145]. The theory is
relatively involved and will not be described here. In short – SOSCF computes an initial guess to the inverse
orbital Hessian and then uses the BFGS formula in a recursive way to update orbital rotation angles. As
information from a few iterations accumulates, the guess to the inverse orbital Hessian becomes better and
better and the calculation reaches a regime where it converges superlinearly. As implemented, the procedure
converges as well or slightly better than DIIS and takes a somewhat less time. However, it is also a lot
less robust, so that DIIS is the method of choice for many problems (see also the description of the full
Newton-Raphson procedure in the next section). On the other hand, SOSCF is useful when DIIS gets stuck
at some error around ⇠ 0.001 or 0.0001. Such cases were the primary motive for the implementation of
SOSCF into ORCA.
# Autostart for orbitals
Older versions of ORCA always created a new GBW file at the beginning of the run no matter whether a file
of the same name existed or perhaps contained orbitals. Now, in the case of single-point calculations the
program automatically checks if a .gbw file of the same name exists. If yes, the program checks if it contains
orbitals and all other necessary information for a restart. If yes, the variable Guess is set to MORead. The
existing .gbw file is renamed to BaseName.ges and MOInp is set to this filename. If the AutoStart feature is
not desired, set AutoStart false in the %scf block or give the keyword !NoAutoStart in the simple input
line. Note that AutoStart is ignored for geometry optimizations: in this case, using previously converged
orbitals contained in a .gbw file (of a di↵erent name) can be achieved via MORead and MOInp.
# basis set
The basis set is specified in the block %BASIS. Note that there are three distinguished slots for auxiliary basis
sets (AuxJ, AuxC and AuxJK) to be used with RI approximation. Which auxiliary basis slot is used in the
actual program depends on the context. The AuxJ and AuxJK slots are used in the context of Fock matrix
construction, whereas the AuxC slot is used for all other integral generation steps e.g. in post-Hartree Fock
methods. Assigning the auxiliary basis with the simple input, takes care of the individual slots. However, in
specific cases they must be set explicitly in the block input. For example, a “/JK” basis may be assigned to
AuxJ in this way.
# cc
The coupled-cluster method is presently available for RHF and UHF references. The implementation is fairly
efficient and suitable for large-scale calculations.
# Orca and symmetry
with the UseSym keyword or a %symmetry input block (for which the
abbreviation %sym is allowed), the program detects the point group, cleans up the coordinates, orients the
molecule and produces symmetry-adapted orbitals in SCF/CASSCF calculations. Note however that the
calculation time will not be reduced. While for geometry cleanup the full point group is taken into account (as
long as the number of group operations does not exceed 120), only D2h and subgroups are currently supported
for electronic-structure calculations. The only correlation module that makes use of this information so far is
the MRCI module.
# The singlet and triplets in the output of orca is not sorted. 
# sort condition should be added in extract file
# Maxcore use:
Some ORCA modules (in particular those that perform some kind of wavefunction
based correlation calculations) require large scratch arrays. Each module has an independent variable
to control the size of these dominant scratch arrays. However, since these modules are never running
simultaneously, we provide a global variable MaxCore that assigns a certain amount of scratch memory to all
of these modules. Thus:
```
%MaxCore 4000
```
sets 4000 MB (= 4 GB) as the limit for these scratch arrays. This limit applies per processing core.
Do not be surprised if the program takes more than that – this size only refers to the dominant work areas.
Thus, you are well advised to provide a number that is significantly less than your physical memory. Note
also that the memory use of the SCF program cannot be controlled: it dynamically allocates all memory that
it needs and if it runs out of physical memory you are out of luck. This, however, rarely happens unless you
run on a really small memory computer or you are running a gigantic job.
