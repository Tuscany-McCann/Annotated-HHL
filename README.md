# An Annotated Implementation of the HHL Algorithm [[1]](#1)
In this notebook, we will describe and step through a Qiskit circuit implementing the HHL algorithm for solving linear systems on a quantum computer.  
To be specific, we are trying to solve for $\vec{x}$ in a matrix equation $$ A\vec{x}=\vec{b} $$ where both $A$ and $b$ are known and (we assume) available to represent on the computer as needed. We also assume the matrix $A$ is indeed invertible, so it has no zero eigenvalues, and we will not treat negative eigenvalues at this time either.

The essence of the algorithm is as follows: we are trying to compute $A^{-1}\vec{b}$, and the central observation is that by decomposing $\vec{b}$ into the eigenbasis of $A$ (or if you prefer, viewing it as a superposition of eigenvectors), the action of the inverse becomes diagonal, just dividing by the eigenvalues of $A$ instead of multiplying on each eigenvector.  

We can obtain the eigenvalues of $A$ in two steps: first, assuming $A$ is self-adjoint/Hermitian, we want to implement the unitary (which it is when $A$ is Hermitian) $U=e^{iAt}$ for some appropriate $t\in\mathbb{R}$. This means the eigenvalues of $A$ will appear as phases in the action of $U$ on the eigenbasis of $A$ (which is also the eigenbasis of $U$). (If $A$ is not Hermitian, it is easy to construct a block matrix containing $A$ which is Hermitian and has the desired equation associated to one of its blocks.)

This is the setting for Quantum Phase Estimation to extract the eigenvalues of $A$ and encode them each (approximately) into a bitstring $|\tilde\lambda_k\rangle^C$. We can then do arithmetic on those bitstrings to compute functions of the eigenvalues; specifically, we want to rotate a flag qubit $S$ so that the amplitude of $|1\rangle^S|\tilde\lambda_k\rangle^C$ is proportional to $1/\tilde\lambda_k$, which corresponds to the action of $A^{-1}$. 

Once we clean up the computation, we have (approximately) carried out the inversion $A^{-1}\vec{b}$ and have the (normalized) solution $|x\rangle^I$ in our register. At this point we can repeat the process to approximate its bits, or compute some observable in a single step.
