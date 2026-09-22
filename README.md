# postAH65
Flank protection - Tournai - fty_16

Author : Dr. ir. LIMBREE Christophe
Date : 2026-09-20
Version : 0.
Goal : Provide the model related to the compositional verification of the flank protection property

## Modifications


# Introduction

This TAR contains :
- the RIS model (smv files)
- the TRAIN model (smv file)
- the definition of the contracts (oss file)
- this README.md file

The smv model reflects the SSI language (RIs used in Belgium) and the Belgian signalling principles. The goal of this README.md and associated files is not to explain the model in detail. However the contracts, defined in the flank_1.oss file, are explained in the article. The smv file are only provided to prove the correct implementation of the contracts by the RIS smv model.

The process of compositional verification is only provided for RIS 16. The process for RIS 15 is exactly the same. 

# Files modified for CV of flank protection 

Two files were modified to verify the flank protection property with a CV approach :
1. leafMod_fty_16_FP.smv
2. Pcond.smv

The first one contains the main smv module with the declaration of all the other modules like the points and the routes. It also declare the inputs and outputs refered in the oss file. The "leaf" prefix is used to show that it is the implementation of the component as isolated by the decomposition algorithm that is supposed to retrieve the smallest sub-network possible.

The second one contains the points related conditions like the conditions to move and the conditions to command. 

# ocra commands

This section provides the commands needed to start the ocra tool and verify the properties.

## Starting ocra

ocra is used under the Ubuntu 24.04 WSL in Windows 11. The binary can be downloaded from : https://xsap.fbk.eu/useful-links.html

ocra must be started with the C++ preprocessor (-pre cpp) as included files are used in the smv model.

ocra-linux64 -int -pre cpp flank_1.oss

## Loading the oss file and verifying the syntax

This first command allows verifying the syntax of the oss file.

ocra > ocra_check_syntax -i flank_1.oss
Success: the oss specification is syntactically correct

## Generating the template

ocra can generate smv templates of the leaf smv modules.

ocra > ocra_print_implementation_template
  Written file RIS16.hydi
  Written file RIS15.hydi

## Checking the refinements 

The fisrt step of verification consists in verifying that (section 4.3 of the article) :

⟨𝐴1, 𝐺1⟩ and ⟨𝐴2, 𝐺2⟩, entail the system contract ⟨𝐴, 𝐺⟩.

ocra > ocra_check_refinement

Checking refinement of component: Tournai
  Checking "CONTRACT Flank_32H-33H REFINEDBY VIXL16.Flank_32H-33H, VIXL15.Flank_32H-33H;"
    Checking the correct implementation of "Flank_32H-33H"... [OK]
    Checking the correct environment of "VIXL16.Flank_32H-33H"... [OK]
    Checking the correct environment of "VIXL15.Flank_32H-33H"... [OK]

Checking refinement of component: RIS16

Checking refinement of component: RIS15

  Summary: everything is OK.

## Checking the implementation

ocra calls nuxmv to verify the implementation of the contract in the RIS smv model.

Contract to be checked (Flank_32H-33H) : CR_33H -> CR_32H, where CR_33H is an input and CR_32H is an output for RIS 16.

ocra_check_implementation -c RIS16 -C Flank_32H-33H -a ic3 -I leafMod_fty_16_FP.smv -i flank_1.oss

The ic3 algorithm is used as the verification algorithm for nuxmv.

Property to prove :
HRELTLSPEC NAME Flank_32H-33H_norm_guarantee := (!( G TRUE) |  G (CR_33H -> CR_32H));

ocra > ocra_check_implementation -c RIS16 -C Flank_32H-33H -a ic3 -I leafMod_fty_16_FP.smv -i flank_1.oss

Checking implementation of contract RIS16.Flank_32H-33H ... [OK]

Options :
- c is the component
- C is the contract
- a is the algorithm used for the verification
- I is the implementation
- i is the oss file
