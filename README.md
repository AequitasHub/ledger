# Registre public — Aequitas Intelligence

Journal horodaté et append-only de **toutes** les recommandations d'investissement publiées par Aequitas Intelligence — accès libre comme accès payant.

**Registre ouvert le 15 août 2026.** Aucune entrée antérieure n'existe.

| Fichier | Contenu |
|---|---|
| [`LEDGER.csv`](LEDGER.csv) | Le registre |
| [`METHODOLOGIE.md`](METHODOLOGIE.md) | Périmètre, règles, dictionnaire des colonnes, calcul de performance |
| [`rapports/`](rapports/) | Rapports en accès libre |

## Principe

Toute recommandation publiée est inscrite ici **avant** diffusion. Les entrées ne sont jamais supprimées, modifiées ni antidatées — y compris les perdantes et les erreurs d'analyse.

L'historique Git constitue la preuve : chaque inscription porte l'horodatage d'un tiers et son diff est public. La branche `main` interdit la réécriture d'historique et la suppression.

```bash
git log --follow -p LEDGER.csv
```

## Recommandations à accès payant

Elles figurent au registre au même instant que les autres, mais en deux temps : à la publication, seul le **hash SHA-256** de l'entrée est inscrit, champs vides. À J+90 ou à la clôture, les champs sont divulgués et le hash devient vérifiable.

L'antériorité est prouvée dès le premier jour ; le contenu reste réservé aux abonnés jusqu'à la divulgation. Aucune recommandation ne peut être dissimulée, remplacée ou réécrite entre les deux.

## Ce que ce registre n'est pas

Ce n'est pas un relevé de portefeuille personnel, ni un service de signaux, ni un conseil en investissement personnalisé.

Recommandations générales au sens du règlement (UE) n° 596/2014 (MAR). Les performances passées ne préjugent pas des performances futures. Risque de perte en capital pouvant aller jusqu'à la totalité du montant investi.

Détail complet dans [`METHODOLOGIE.md`](METHODOLOGIE.md).

---

## Legal notice

General investment recommendations within the meaning of Regulation (EU) No 596/2014 (MAR). Not personalised investment advice, not an offer or solicitation. Past performance is not a reliable indicator of future results. Capital at risk — you may lose the entire amount invested.

Full notice in [`METHODOLOGIE.md`](METHODOLOGIE.md), section 11.
