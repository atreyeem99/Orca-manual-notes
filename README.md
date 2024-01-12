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
# 3) RI approximation
- The RI approximation saves a large amount of time while giving close to identical results (the errors will usually be <0.1 eV) and is generally recommended.
