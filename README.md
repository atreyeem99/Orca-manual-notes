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
Both DLPNO-CCSD(T) and DLPNO-MP2 are linear-scaling methods (albeit the former has a larger prefactor). This means that if a DLPNO-MP2 calculation can be performed, DLPNO-CCSD(T) is often going to be within reach, too. However, CCSD(T) is generally much more accurate than MP2 and thus should be given preference.
