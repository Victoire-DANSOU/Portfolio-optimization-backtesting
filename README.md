# Portfolio Optimisation & Backtesting

> Gestion quantitative de portefeuille sur 8 actions Europe/USA (2020–2024).
> Projet personnel réalisé dans le cadre d'une spécialisation Risque Management et Finance Quantitative à l'ENSAI.


## Objectif

Construire un portefeuille d'actions optimisé selon la théorie de Markowitz, mesurer son risque de marché (VaR, CVaR, Monte Carlo) et comparer une stratégie momentum algorithmique à une stratégie passive équipondérée sur la période 2020–2024.


## Dataset

**Données boursières** — Yahoo Finance via `yfinance`
8 actions Mix Europe + USA · Période : 01/01/2020 → 31/12/2024 · 1 258 jours de cotation

| Action | Zone | Secteur | Performance 2020–2024 |
|---|---|---|---|
| Apple (AAPL) | 🇺🇸 USA | Tech | +169.4% |
| TotalEnergies (TTE.PA) | 🇫🇷 France | Énergie | +73.7% |
| ExxonMobil (XOM) | 🇺🇸 USA | Énergie | +50.4% |
| SAP (SAP.DE) | 🇩🇪 Allemagne | Tech | +41.6% |
| LVMH (MC.PA) | 🇫🇷 France | Luxe | +29.2% |
| BNP Paribas (BNP.PA) | 🇫🇷 France | Finance | +11.9% |
| J&J (JNJ) | 🇺🇸 USA | Santé | +2.6% |
| JPMorgan (JPM) | 🇺🇸 USA | Finance | +1.8% |


## Structure du projet

portfolio-optimization-backtesting/
│
└── projet2_portefeuille_backtesting.ipynb   # Notebook complet (4 parties)


## Méthodologie

### Partie 1 — Données & Analyse exploratoire
- Téléchargement via `yfinance`, calcul des **rendements logarithmiques**
- Performance cumulée et matrice de corrélation des rendements
- Identification des paires les plus corrélées (JPMorgan/BNP = 0.63, ExxonMobil/SAP = 0.57)

### Partie 2 — Optimisation Markowitz

Optimisation du portefeuille par maximisation du **ratio de Sharpe** :

$$\text{Sharpe} = \frac{R_p - R_f}{\sigma_p}$$

- Simulation de **10 000 portefeuilles aléatoires** → frontière efficiente
- Optimisation numérique (scipy `SLSQP`) → portefeuille optimal
- Taux sans risque : 2% annualisé

**Résultats :**

| Métrique | Valeur |
|---|---|
| Allocation optimale | Apple 61.5%, TotalEnergies 29.2%, SAP 9.3% |
| Rendement annualisé | 21.2% |
| Volatilité annualisée | 24.7% |
| **Ratio de Sharpe** | **0.776** |

> Markowitz sélectionne naturellement un mix géographique (US tech + énergie européenne + tech allemande) grâce à leurs faibles corrélations. JPMorgan et BNP Paribas sont éliminés malgré leurs propriétés de diversification — rendement historique insuffisant.

### Partie 3 — Mesure du risque de marché

**VaR et CVaR historiques (95%) sur 100 000€ :**

| Métrique | Valeur journalière | Sur 100 000€ |
|---|---|---|
| VaR 95% | 2.26% | 2 262€ |
| CVaR 95% | 3.65% | 3 652€ |

> CVaR = 1.6 × VaR : confirme des **fat tails** (queues épaisses) — la distribution normale sous-estime le risque réel. Cohérent avec les exigences Bâle III qui imposent la CVaR pour le calcul du capital réglementaire.

**Monte Carlo — 10 000 simulations sur 1 an (100 000€) :**

| Scénario | Valeur finale |
|---|---|
| Médiane | 119 660€ (+19.7%) |
| Meilleur cas (99%) | 212 023€ (+112%) |
| Pire cas (1%) | 67 309€ (-32.7%) |
| VaR annuelle 95% | 20 700€ |

### Partie 4 — Backtesting : Momentum vs Stratégie Passive

**Stratégie momentum :** chaque mois, sélection des 3 meilleures actions sur les 3 derniers mois.
**Stratégie passive :** portefeuille équipondéré sur les 8 actions, rééquilibré mensuellement.

**Résultats (57 mois, avril 2020 → décembre 2024) :**

| Métrique | Momentum | Passive |
|---|---|---|
| Rendement annualisé | 19.88% | 19.30% |
| Volatilité annualisée | 22.00% | 17.32% |
| **Ratio de Sharpe** | **0.812** | **0.999** |
| Max Drawdown | -25.86% | -15.74% |
| Performance totale | +136.59% | +131.24% |

> **Résultat contre-intuitif :** la stratégie passive bat la momentum sur le Sharpe (0.999 vs 0.812) malgré un rendement légèrement inférieur. La momentum prend 27% de volatilité en plus pour un gain marginal de +0.58%/an — rapport risque/rendement défavorable. Sur ce portefeuille et cette période, investir passivement est plus efficace que timer le marché.

---

## Limites du modèle

- **Markowitz** : sur-optimisation sur données historiques (look-ahead bias) ; concentration irréaliste (61.5% Apple) sans contraintes réglementaires
- **Monte Carlo** : suppose une distribution normale des rendements — sous-estime les queues épaisses
- **Backtesting** : ne prend pas en compte les frais de transaction (la momentum tourne le portefeuille chaque mois)
- **Fenêtre 2020–2024** : période globalement haussière — résultats différents sur une période de crise prolongée


## Stack technique

![Python](https://img.shields.io/badge/Python-3.10-blue)
![yfinance](https://img.shields.io/badge/yfinance-0.2-orange)
![scipy](https://img.shields.io/badge/scipy-1.11-green)

- **Langage :** Python 3.10
- **Librairies :** pandas, numpy, yfinance, scipy, matplotlib, seaborn
- **Environnement :** Jupyter Notebook

---

## Installation

```bash
git clone https://github.com/Victoire-DANSOU/portfolio-optimization-backtesting.git
cd portfolio-optimization-backtesting
pip install yfinance pandas numpy scipy matplotlib seaborn
jupyter notebook
```

---

## Contexte

Projet personnel réalisé en autonomie en 3ème année du cycle ingénieur à l'ENSAI (spécialisation Économie & Finance, option Finance Quantitative). Fait suite au projet de scoring crédit et prépare une candidature en stage de fin d'études dans les domaines Risk Management, Finance Quantitative ou Asset Management.

Les techniques de VaR/CVaR et de Monte Carlo implémentées sont directement applicables aux exigences **Bâle III** pour le calcul du capital réglementaire des banques (SVaR, stress tests).

---

## Références

- Markowitz, H. (1952). *Portfolio Selection*. Journal of Finance.
- Basel Committee — *Minimum Capital Requirements for Market Risk* (FRTB, 2016)
- [Yahoo Finance API — yfinance](https://github.com/ranaroussi/yfinance)# Portfolio-optimization-backtesting
Portfolio optimisation &amp; backtesting — Markowitz, VaR/CVaR, Monte Carlo, momentum strategy | ENSAI Finance
