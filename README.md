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
