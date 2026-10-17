# Écoulement d'air autour d'un aéronef : volumes finis pour les équations d'Euler

[![tests](https://github.com/harounidriss/euler-airfoil/actions/workflows/tests.yml/badge.svg)](https://github.com/harounidriss/euler-airfoil/actions)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/harounidriss/euler-airfoil/blob/main/notebooks/euler1d_sod.ipynb)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

Projet de simulation numérique de l'écoulement compressible non visqueux autour d'un profil d'aile (NACA 0012), construit étape par étape : **chaque brique est validée avant de passer à la suivante**.

> **English summary.** A 1D finite-volume solver for the compressible Euler equations (Roe, HLL and HLLC fluxes, MUSCL reconstruction with minmod limiter, SSP-RK2 time stepping), validated against the exact Riemann solution on Sod's shock tube. It is the first building block of a 2D solver for transonic flow around a NACA 0012 airfoil.

## Avancement

| Étape | Contenu | État |
|---|---|---|
| 1 | Solveur 1D (Roe, HLL, HLLC, MUSCL, SSP-RK2), validation sur le tube de Sod | ✅ terminé |
| 2 | Solveur 2D sur maillage non structuré, vortex isentropique (ordre 2) | à venir |
| 3 | Conditions aux limites (paroi glissante, champ lointain), choc oblique sur un coin supersonique | à venir |
| 4 | Écoulement transsonique autour d'un NACA 0012 (cas AGARD, M = 0,8, α = 1,25°) | à venir |

## Résultats de l'étape 1

Tube à choc de Sod (N = 200, t = 0,2) : les trois flux suivent la solution exacte (détente, contact, choc).

![Comparaison des flux](figures/sod_comparaison.png)

Erreur L¹ sur la densité et ordre observé :

| Flux | Ordre | N = 50 | N = 100 | N = 200 | N = 400 | N = 800 | Ordre observé |
|---|---|---|---|---|---|---|---|
| Roe | 1 | 2,62e-2 | 1,78e-2 | 1,14e-2 | 7,24e-3 | 4,59e-3 | ≈ 0,66 |
| Roe | 2 (MUSCL) | 1,29e-2 | 7,51e-3 | 4,13e-3 | 2,34e-3 | 1,35e-3 | ≈ 0,80 |
| HLLC | 1 | 2,71e-2 | 1,83e-2 | 1,16e-2 | 7,38e-3 | 4,66e-3 | ≈ 0,66 |
| HLLC | 2 (MUSCL) | 1,34e-2 | 7,73e-3 | 4,22e-3 | 2,38e-3 | 1,37e-3 | ≈ 0,80 |

Ces ordres sont inférieurs à l'ordre formel du schéma, ce qui est attendu pour une solution discontinue (le contact converge plus lentement que le choc). L'ordre 2 formel sera vérifié à l'étape 2 sur une solution régulière (vortex isentropique). Détails dans le [rapport](report/main.tex).

![Convergence](figures/sod_convergence.png)

## Démarrage rapide

```bash
git clone https://github.com/harounidriss/euler-airfoil.git
cd euler-airfoil
pip install -r requirements.txt

python -m pytest -q          # tests de validation
python scripts/run_sod.py    # régénère les figures et le tableau de convergence
```

Ou directement dans le navigateur : bouton **Open in Colab** ci-dessus.

## Structure du dépôt

```
euler-airfoil/
├── src/euler1d.py        # modèle, flux (HLL, HLLC, Roe), MUSCL, SSP-RK2, solution exacte de Riemann
├── scripts/run_sod.py    # reproduit les figures et le tableau de convergence
├── tests/test_sod.py     # tests : consistance des flux, conservation, solution exacte, précision
├── notebooks/            # version Colab (une cellule par bloc)
├── report/main.tex       # rapport LaTeX (se compile sur Overleaf, figures incluses)
└── figures/              # figures générées
```

## Tests

Les tests (`pytest`, exécutés aussi par GitHub Actions) vérifient notamment :
- la **consistance** des flux numériques, Φ(U, U) = F(U) ;
- la **solution exacte** de Riemann (valeurs classiques de la zone centrale de Sod : p* ≈ 0,30313, u* ≈ 0,92745) ;
- la **conservation** de la masse et de l'énergie ;
- la **positivité** de la densité et de la pression, et la précision L¹ ;
- le gain de MUSCL par rapport à l'ordre 1 et la convergence en maillage.

## Méthode

- Équations d'Euler en forme conservative, gaz parfait (γ = 1,4).
- Volumes finis avec flux **HLL**, **HLLC** et **Roe** (correction d'entropie de Harten).
- Reconstruction **MUSCL** en variables primitives, limiteur *minmod*.
- Intégration en temps **SSP-RK2**, pas de temps CFL = 0,4.

## Références

- E. F. Toro, *Riemann Solvers and Numerical Methods for Fluid Dynamics*, Springer, 3e éd., 2009.
- R. J. LeVeque, *Finite Volume Methods for Hyperbolic Problems*, Cambridge University Press, 2002.
- E. Godlewski et P.-A. Raviart, *Numerical Approximation of Hyperbolic Systems of Conservation Laws*, Springer, 1996.
- P. L. Roe, *J. Comput. Phys.* 43 (1981) 357–372.
- A. Harten, *J. Comput. Phys.* 49 (1983) 357–393.
- E. F. Toro, M. Spruce, W. Speares, *Shock Waves* 4 (1994) 25–34.
- G. A. Sod, *J. Comput. Phys.* 27 (1978) 1–31.

## Auteur

Ahmat Haroun Idriss, diplômé de master en mathématiques appliquées (modélisation et analyse numérique), Université de Montpellier.

## Licence

[MIT](LICENSE)
