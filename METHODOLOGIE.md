# Méthodologie du registre

**Aequitas Intelligence** — registre public des recommandations
Registre ouvert le 15 août 2026. Aucune entrée antérieure n'existe.

---

## 1. Ce qu'est ce registre

Un journal append-only de **toutes** les recommandations publiées — accès libre comme accès payant, gagnantes comme perdantes — horodaté par l'historique Git de ce dépôt.

Ce qu'il **n'est pas** :

- Ce n'est pas un relevé de portefeuille personnel. Il ne dit rien de ce que l'auteur détient ni en quelle quantité.
- Ce n'est pas un service de signaux.
- Ce n'est pas un conseil en investissement personnalisé.

**Le périmètre est éditorial, pas patrimonial :** il trace des publications, pas des transactions.

---

## 2. Ce qui entre au registre

> Une recommandation entre au registre **si et seulement si** elle est publiée sous format formel — rapport daté portant un identifiant `AEQ-XXXX`.

Tout le reste — commentaires, réactions à l'actualité, discussions de marché, réponses à des questions — est du commentaire et n'est jamais une recommandation.

Corollaire assumé : une idée évoquée en discussion et jamais formalisée ne peut être revendiquée, quelle que soit sa performance ultérieure. **Si ce n'est pas au registre, cela n'a pas eu lieu.**

Le niveau d'accès du rapport (`libre`, `analyste`, `associe`) ne change rien à l'obligation d'inscription. Une recommandation réservée aux abonnés est inscrite au registre public au même instant qu'une recommandation en accès libre.

---

## 3. Engagement puis divulgation

Les entrées à accès payant sont inscrites en deux temps.

**À la publication (T0)** — la ligne est créée avec : `ledger_id`, `date_publication`, `acces`, `statut`, et `hash_entree`. Les champs d'analyse (`actif`, `ticker`, `sens`, `verdict`, `prix_reference`…) restent vides.

`hash_entree` est le SHA-256 de la concaténation suivante, séparateur `|`, sans espace :

```
ledger_id|date_publication|type|actif|ticker|identifiant|sens|verdict|prix_reference|devise|horizon|benchmark|benchmark_reference
```

**Encodage exact** — la chaîne est en UTF-8, sans saut de ligne final, sans espace autour des séparateurs. Le hash est exprimé en hexadécimal minuscule, 64 caractères. Toute vérification qui inclurait un saut de ligne terminal produirait un résultat différent et invalide.

```bash
echo -n "AEQ-0002|2026-09-05T09:00:00Z|equity|..." | shasum -a 256
```

**À la divulgation** — J+90 après publication, ou à la clôture si elle intervient avant. Les champs sont renseignés. Quiconque recalcule le SHA-256 de la ligne divulguée obtient le hash inscrit à T0.

Ce mécanisme rend impossible de modifier, remplacer ou dissimuler une recommandation payante après coup, tout en préservant sa valeur commerciale jusqu'à la divulgation. Le hash prouve l'antériorité ; la divulgation prouve le contenu.

Les entrées en accès libre sont renseignées immédiatement. Leur `hash_entree` est calculé à l'identique et sert de contrôle d'intégrité.

---

## 4. Les trois règles

**1. Le registre publie en premier.** Le commit d'inscription précède toujours la diffusion du rapport. L'horodatage du commit fait foi.

**2. Aucun ajout rétroactif.** Le registre ouvre à sa date de création. Aucune position antérieure n'y est ajoutée.

**3. Aucune suppression, aucune réécriture.** Une entrée inscrite ne sort jamais du registre. Les erreurs d'analyse y figurent au même titre que les réussites. Une correction prend la forme d'un commit dédié au message explicite, jamais d'une édition silencieuse.

Ces règles sont mécaniquement vérifiables : toute violation apparaîtrait dans l'historique Git, protégé contre la réécriture et la suppression au niveau du dépôt.

---

## 5. Dictionnaire des colonnes

| Colonne | Rempli à | Description |
|---|---|---|
| `ledger_id` | T0 | Identifiant séquentiel `AEQ-XXXX`, jamais réattribué |
| `date_publication` | T0 | ISO 8601 UTC |
| `acces` | T0 | `libre` \| `analyste` \| `associe` |
| `hash_entree` | T0 | SHA-256 de l'entrée complète (§3) |
| `type` | divulgation | `equity` \| `crypto` |
| `actif` | divulgation | Dénomination complète |
| `ticker` | divulgation | Symbole |
| `identifiant` | divulgation | ISIN, ou adresse de contrat + chaîne |
| `sens` | divulgation | `Long` \| `Short` \| `Neutre` |
| `verdict` | divulgation | Vocabulaire fixe (§6) |
| `prix_reference` | divulgation | Prix à l'instant de l'inscription |
| `devise` | divulgation | `EUR` \| `USD` |
| `horizon` | divulgation | Durée annoncée de la thèse |
| `benchmark` | divulgation | Indice de comparaison (§7) |
| `benchmark_reference` | divulgation | Niveau du benchmark à l'inscription |
| `statut` | T0 puis clôture | `Ouverte` \| `Clôturée` |
| `date_cloture` | clôture | ISO 8601 UTC |
| `prix_cloture` | clôture | Prix à la clôture |
| `benchmark_cloture` | clôture | Niveau du benchmark à la clôture |
| `motif_cloture` | clôture | Vocabulaire fixe (§6) |
| `rapport` | divulgation | Lien vers le rapport, si accès libre |

Pour une entrée en accès libre, T0 et divulgation sont simultanés.

**Aucune performance n'est stockée.** Les rendements se recalculent à partir des prix et des niveaux de benchmark. Stocker un chiffre dérivé reviendrait à demander qu'on me fasse confiance sur un calcul — l'inverse de l'objet de ce registre.

---

## 6. Vocabulaires fixes

**Verdict** — échelle figée, jamais redéfinie :

`VENTE FORTE` · `VENTE` · `ALLÉGER` · `NEUTRE` · `ACHAT` · `ACHAT FORT`

**Accès** : `libre` · `analyste` · `associe`

**Statut** : `Ouverte` · `Clôturée`

**Motif de clôture** :

| Motif | Sens |
|---|---|
| `Objectif atteint` | La thèse s'est réalisée |
| `Thèse invalidée` | Un fait nouveau contredit l'hypothèse centrale |
| `Horizon échu` | L'horizon annoncé est atteint, sans réalisation ni invalidation |
| `Erreur d'analyse` | Le raisonnement initial était faux, indépendamment du résultat de marché |

La distinction entre `Thèse invalidée` et `Erreur d'analyse` est délibérée : la première est un accident, la seconde une faute. Les deux sont publiées.

---

## 7. Calcul de performance

Performance brute : `(prix_cloture − prix_reference) / prix_reference`, signe inversé pour un `Short`.

Performance relative : différence avec la variation du benchmark sur la même fenêtre.

| Type | Benchmark |
|---|---|
| Actions zone euro | STOXX Europe 600 |
| Actions US | S&P 500 |
| Crypto | BTC |

Le benchmark est fixé à l'inscription et ne change jamais pour une entrée donnée.

Les statistiques agrégées distinguent toujours les positions clôturées des positions ouvertes, et n'excluent aucune entrée — y compris les entrées payantes non encore divulguées, comptées au dénominateur.

---

## 8. Clôture

Une position est clôturée à la survenue de l'un des quatre motifs, par un commit distinct. La date de clôture est celle du commit, pas une date choisie après coup.

La clôture d'une entrée payante déclenche sa divulgation intégrale, même si J+90 n'est pas atteint.

---

## 9. Vérifier ce registre

```bash
git clone https://github.com/AequitasHub/ledger
cd ledger
git log --follow -p LEDGER.csv
```

Chaque modification apparaît avec son horodatage et son diff. Une entrée antidatée, modifiée ou supprimée serait visible. La branche `main` interdit la réécriture d'historique et la suppression.

Pour vérifier une divulgation, recalculer le SHA-256 de la chaîne définie au §3 et le comparer au `hash_entree` inscrit à T0.

---

## 10. Mentions

Les recommandations inscrites à ce registre sont des recommandations d'investissement générales au sens du règlement (UE) n° 596/2014 (MAR). Elles ne constituent ni un conseil en investissement personnalisé, ni une sollicitation, ni une offre.

Les performances passées ne préjugent pas des performances futures. Tout investissement comporte un risque de perte en capital pouvant aller jusqu'à la totalité du montant investi.

Chaque rapport comporte une déclaration d'intérêt indiquant si l'auteur détient une position sur l'actif concerné à la date de publication.

---

## 11. Legal notice (English)

**Nature** — The recommendations recorded in this register are general investment recommendations within the meaning of Article 3(1)(35) of Regulation (EU) No 596/2014 (Market Abuse Regulation) and Commission Delegated Regulation (EU) 2016/958. They do not constitute personalised investment advice, investment research within the meaning of MiFID II, a solicitation, or an offer to buy or sell any financial instrument.

**Producer** — Lorenzo Reuille, trading as Aequitas Intelligence, sole proprietorship (entreprise individuelle), France. Not authorised as an investment firm or investment adviser.

**Risk** — Past performance is not a reliable indicator of future results. All investment carries a risk of capital loss, which may extend to the entire amount invested. Recommendations are not tailored to the financial situation, investment objectives or risk tolerance of any individual recipient.

**Conflicts of interest** — Each report discloses whether the author holds a position in the instrument concerned at the date of publication. No compensation is received from issuers or related parties in connection with these recommendations.

**Timing** — The date and time of production and of first dissemination of each recommendation are recorded in this register and evidenced by the repository commit history.
