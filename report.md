# Meta-graph report: Regulation M-C

## Reading this report
- **Team weight**: each team counts as placement × recency × variant split. Placement gives `placementTopWeight` at or better than rank `placementTopRank`, `placementMidWeight` at or better than rank `placementMidRank`, and `placementDefaultWeight` otherwise (no rank, or a ladder-peak rank). Recency halves every `recencyHalfLifeDays` days before the as-of date. Teams linked by `variantOf` form one variant family and share one team's weight.
- **Support**: the share of the total team weight carried by the teams that contain a species, a species@item, a pair, or a triple.
- **Lift**: a pair's support divided by the product of its two sides' supports. It is one when the two appear together exactly as often as chance predicts, and higher when they appear together more often. Lift cannot exceed one over the more common side's support, so a pair with a very common species always has a low lift; the normalized lift divides lift by that ceiling.
- **Confidence**: Conf(B|A) is the pair's support divided by A's support, the share of A's team weight whose teams also contain B.
- **Core**: a pair (two species, or a species@item with a species) found in at least `coreMinTeams` variant families, whose lift is at least `coreMinLift`, or exceeds `communityMinLift` with a confidence of at least `coreMinConfidence` in either direction. Two species@item tokens never form a core in the species graph.
- **Community**: Louvain communities (resolution `louvainResolution`, seed `louvainSeed`) over the species pairs with at least `minPairTeams` teams and lift above `communityMinLift`, each weighted by its Louvain weight (`louvainWeightMode`). A species with no such pair is unconnected and belongs to no community. A community is named by its three members with the highest in-community support.
- **Primary, hybrid, unassigned**: a team scores against each community the summed Louvain weight of the cores it contains, counting only the strongest core per species pair, and a core counts toward the communities of both its species. The best-scoring community is the team's primary; a runner-up scoring at least `hybridRunnerUpRatio` of the best is its hybrid. A team with no core is unassigned. Primary shares plus the unassigned share make up the whole team weight; hybrid shares are counted apart.
- **Sub-communities**: a community whose primary share is at least `subPassMinParentShare` gets a second Louvain pass (resolution `subLouvainResolution`) over its primary teams alone; the teams for which it is only the hybrid are left out. Its nodes are species@item tokens: a set whose item is on fewer than `subMinTokenTeams` of those teams, or that holds no item, counts as its bare species, shown as "(other item)" with the most common of those items and how many of the bare species' teams hold it. Support, lift, cores, and the primary, hybrid, and unassigned split are computed within those teams by the rules above, two species@item tokens can form a core there, and a sub-community's shares are of its parent community's primary team weight. Each sub-community is headed by its primary teams and distinct builds (teams with the same tokens, or of one variant family, are one build), the Megas those teams carry, and its most common species; one with fewer than `subMinDistinctBuilds` distinct builds is listed as minor on one line, and its teams count as minor in the cluster comparison. With `subMegaPairToken`, a team carrying exactly two Mega Stones also yields one Mega-pair token when that pair is on at least `subMinTokenTeams` of the parent's teams; it pairs with every set of the team but its two Mega holders and is left out of labels, species counts, and distinct builds.
- **Sheet vs. ladder**: the tournament sample compared with the ladder snapshot nearest and at or before the as-of date in this regulation, over its top `ladderTopSpecies` species by usage rank; the ladder items held by at least `ladderItemMinShare` of a species' sets on the ladder, more than in the sheet, on fewer than `minNodeTeams` sheet teams; the species the two rank most differently (each list capped at `ladderBiasListSize`); and each species' ladder teammate list against its sheet partners by P(B|A). Since the ladder gives usage and teammates as ranks and lists with no shares, every comparison here is by rank or list membership, never a share derived from a rank.

## 1. Window & Applied Defaults
- Regulation: Regulation M-C
- As of: 2026-09-25, window: 16 days
- Date range: 2026-09-09 to 2026-09-22
- Teams analyzed: 395 (total weight 236.08)
- Ladder snapshot: 2026-09-24 (M6, M-C)
- Placement: 308 of 395 teams have no tournament placement and weigh placementDefaultWeight, so the placement tiers move few teams. The tiers ignore event size (a small cup's winner weighs like a large event's top cut), and Seniors teams are not separated.
- Applied defaults:
  - placementTopRank: 8
  - placementTopWeight: 1.5
  - placementMidRank: 32
  - placementMidWeight: 1.2
  - placementDefaultWeight: 1
  - recencyHalfLifeDays: 14
  - fastSpeThreshold: 24
  - fastSpeWithNatureThreshold: 16
  - offensiveThreshold: 24
  - bulkyThreshold: 32
  - minNodeTeams: 3
  - minPairTeams: 4
  - minTripleTeams: 4
  - conditionalMinTeams: 3
  - moveShiftThreshold: 15
  - coreMinTeams: 4
  - coreMinLift: 1.2
  - coreMinConfidence: 0.6
  - communityMinLift: 1
  - louvainWeightMode: count-lnlift
  - louvainResolution: 1
  - louvainSeed: 1
  - resolutionSweep: 0.5, 0.75, 1, 1.25, 1.5
  - hybridRunnerUpRatio: 0.5
  - forceAtlas2Iterations: 200
  - subPassMinParentShare: 0.3
  - subMinTokenTeams: 5
  - subMegaPairToken: false
  - subLouvainResolution: 1
  - subMinDistinctBuilds: 3
  - trickRoomAbuserMaxSpeed: 85
  - topSetSignatures: 5
  - topPairsByLift: 25
  - topPairsBySupport: 25
  - topTriples: 20
  - itemSynergyLiftDelta: 0.3
  - representativeTeamsPerCommunity: 3
  - conditionalTableSpeciesCount: 8
  - conditionalTablePartnersCount: 5
  - knownCoreChecks: Salamence+Rillaboom, Sneasler+Rillaboom, Tyranitar+Excadrill, Gardevoir+Indeedee-F
  - archetypeMatchDepth: 5
  - ladderTopSpecies: 60
  - ladderItemMinShare: 0.15
  - ladderBiasListSize: 15

## 2. Known-Core Check
Species pairs A + B from knownCoreChecks, with their best species@item + species pair (highest lift) beside them: an item can carry a synergy the species-level pair does not show.
| Pair | Present | Teams | Families | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Best item-level pair | Item teams | Item lift | Conf(partner | item) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Salamence + Rillaboom | yes | 107 | 103 | 1.195 | 0.676 | 0.47 | 0.68 | yes | Rillaboom@Expert Belt + Salamence | 4 | 2.531 | 1.00 |
| Sneasler + Rillaboom | yes | 107 | 103 | 0.982 | 0.556 | 0.46 | 0.56 | no | Sneasler@Grassy Seed + Rillaboom | 62 | 1.767 | 1.00 |
| Tyranitar + Excadrill | yes | 32 | 30 | 11.070 | 1.000 | 1.00 | 0.80 | yes | Tyranitar@Tyranitarite + Excadrill | 30 | 12.106 | 0.87 |
| Gardevoir + Indeedee-F | yes | 34 | 33 | 4.134 | 1.000 | 0.32 | 1.00 | yes | Indeedee-F@Colbur Berry + Gardevoir | 12 | 5.537 | 0.43 |

## 3. Top Pairs by Lift and Support
### Species pairs by lift
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Corviknight | Indeedee | 7 | 1.5% | 18.460 | 1.000 | 0.27 | 1.00 | yes | 5 |
| Gengar | Politoed | 8 | 2.5% | 12.623 | 0.671 | 0.67 | 0.47 | yes | 3 |
| Corviknight | Excadrill | 6 | 1.2% | 11.698 | 0.841 | 0.17 | 0.84 | yes | 3 |
| Excadrill | Tyranitar | 32 | 7.2% | 11.070 | 1.000 | 0.80 | 1.00 | yes | 1 |
| Corviknight | Tyranitar | 7 | 1.5% | 11.070 | 1.000 | 0.16 | 1.00 | yes | 1 |
| Charizard | Venusaur | 8 | 2.0% | 10.619 | 0.910 | 0.91 | 0.24 | yes | n/a |
| Hatterene | Torkoal | 7 | 1.6% | 9.752 | 0.606 | 0.26 | 0.61 | yes | 3 |
| Gengar | Kommo-o | 4 | 1.2% | 9.498 | 0.505 | 0.50 | 0.22 | yes | 7 |
| Glimmora | Volcarona | 8 | 2.4% | 9.343 | 0.498 | 0.50 | 0.45 | yes | 6 |
| Pelipper | Swampert | 11 | 3.1% | 8.906 | 0.928 | 0.93 | 0.30 | yes | 7 |
| Archaludon | Swampert | 10 | 2.9% | 8.332 | 0.862 | 0.86 | 0.28 | yes | 5 |
| Baxcalibur | Ninetales-Alola | 6 | 1.3% | 8.182 | 0.481 | 0.48 | 0.22 | yes | 4 |
| Excadrill | Indeedee | 14 | 2.9% | 7.513 | 0.540 | 0.54 | 0.41 | yes | 5 |
| Aerodactyl | Lucario | 6 | 1.4% | 7.305 | 0.487 | 0.21 | 0.49 | yes | n/a |
| Archaludon | Grimmsnarl | 9 | 2.2% | 6.839 | 0.708 | 0.71 | 0.21 | yes | 7 |
| Archaludon | Politoed | 10 | 2.5% | 6.627 | 0.686 | 0.69 | 0.24 | yes | 6 |
| Indeedee | Tyranitar | 15 | 3.2% | 6.460 | 0.584 | 0.35 | 0.58 | yes | 3 |
| Archaludon | Pelipper | 24 | 6.9% | 6.428 | 0.670 | 0.67 | 0.67 | yes | 1 |
| Grimmsnarl | Pelipper | 7 | 1.9% | 6.010 | 0.626 | 0.19 | 0.63 | yes | 2 |
| Gardevoir | Talonflame | 4 | 0.8% | 5.858 | 0.454 | 0.45 | 0.11 | yes | n/a |
| Charizard | Grimmsnarl | 6 | 1.5% | 5.569 | 0.477 | 0.48 | 0.17 | yes | n/a |
| Aerodactyl | Sylveon | 6 | 1.8% | 5.490 | 0.609 | 0.16 | 0.61 | yes | 5 |
| Raichu | Staraptor | 10 | 2.8% | 5.146 | 0.602 | 0.60 | 0.24 | yes | 6 |
| Glimmora | Whimsicott | 4 | 1.1% | 5.069 | 0.270 | 0.27 | 0.21 | yes | 7 |
| Golisopod | Pelipper | 31 | 8.3% | 4.946 | 0.801 | 0.80 | 0.52 | yes | 1 |

### Species pairs by support
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Incineroar | Rillaboom | 105 | 26.8% | 1.325 | 0.750 | 0.47 | 0.75 | yes | 1 |
| Rillaboom | Salamence | 107 | 26.7% | 1.195 | 0.676 | 0.68 | 0.47 | yes | 2 |
| Rillaboom | Sneasler | 107 | 26.2% | 0.982 | 0.556 | 0.56 | 0.46 | no | 1 |
| Salamence | Sneasler | 92 | 21.8% | 1.171 | 0.552 | 0.46 | 0.55 | no | 2 |
| Gholdengo | Rillaboom | 66 | 18.4% | 1.350 | 0.764 | 0.33 | 0.76 | yes | 1 |
| Kingambit | Rillaboom | 71 | 18.1% | 1.106 | 0.626 | 0.32 | 0.63 | yes | 2 |
| Incineroar | Sneasler | 70 | 17.5% | 1.037 | 0.489 | 0.37 | 0.49 | no | 2 |
| Kingambit | Sneasler | 70 | 17.0% | 1.250 | 0.589 | 0.36 | 0.59 | yes | 1 |
| Kingambit | Salamence | 65 | 15.9% | 1.390 | 0.549 | 0.40 | 0.55 | yes | 3 |
| Gholdengo | Salamence | 53 | 14.2% | 1.492 | 0.590 | 0.36 | 0.59 | yes | 2 |
| Floette-Eternal | Rillaboom | 58 | 13.5% | 1.430 | 0.809 | 0.24 | 0.81 | yes | 1 |
| Indeedee-F | Sneasler | 53 | 12.7% | 1.117 | 0.526 | 0.27 | 0.53 | no | 1 |
| Floette-Eternal | Incineroar | 52 | 12.7% | 2.128 | 0.761 | 0.35 | 0.76 | yes | 3 |
| Basculegion | Rillaboom | 52 | 12.1% | 0.997 | 0.564 | 0.21 | 0.56 | no | 2 |
| Floette-Eternal | Sneasler | 52 | 12.0% | 1.525 | 0.719 | 0.25 | 0.72 | yes | 2 |
| Basculegion | Sneasler | 49 | 11.3% | 1.119 | 0.527 | 0.24 | 0.53 | no | 1 |
| Incineroar | Salamence | 48 | 11.3% | 0.802 | 0.317 | 0.29 | 0.32 | no | 3 |
| Arcanine-Hisui | Rillaboom | 39 | 11.0% | 1.380 | 0.781 | 0.19 | 0.78 | yes | 1 |
| Raichu | Rillaboom | 37 | 10.9% | 1.654 | 0.936 | 0.19 | 0.94 | yes | 1 |
| Gholdengo | Incineroar | 37 | 10.6% | 1.234 | 0.441 | 0.30 | 0.44 | yes | 4 |
| Milotic | Rillaboom | 40 | 10.2% | 1.152 | 0.652 | 0.18 | 0.65 | yes | 1 |
| Basculegion | Salamence | 42 | 9.9% | 1.166 | 0.461 | 0.25 | 0.46 | no | 4 |
| Gholdengo | Sneasler | 37 | 9.7% | 0.851 | 0.401 | 0.21 | 0.40 | no | 3 |
| Gholdengo | Raichu | 31 | 9.0% | 3.196 | 0.770 | 0.77 | 0.37 | yes | 5 |
| Armarouge | Indeedee-F | 35 | 8.7% | 3.860 | 0.934 | 0.36 | 0.93 | yes | 1 |

### Item-level pairs by lift
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Dragapult@Life Orb | Milotic@Psychic Seed | 4 | 0.8% | 68.844 | 1.000 | 1.00 | 0.56 | no | 2 |
| Dragapult | Milotic@Psychic Seed | 4 | 0.8% | 51.937 | 1.000 | 1.00 | 0.42 | no | 2 |
| Corviknight@Psychic Seed | Indeedee@Choice Scarf | 4 | 0.8% | 28.460 | 1.000 | 0.23 | 1.00 | no | 5 |
| Corviknight | Indeedee@Choice Scarf | 6 | 1.2% | 23.942 | 0.841 | 0.35 | 0.84 | yes | 5 |
| Incineroar@Passho Berry | Politoed@Sitrus Berry | 5 | 1.6% | 21.476 | 0.676 | 0.68 | 0.50 | no | n/a |
| Baxcalibur@Baxcalibrite | Ninetales-Alola@Light Clay | 4 | 0.9% | 21.412 | 1.000 | 1.00 | 0.18 | no | 4 |
| Glimmora@Glimmoranite | Volcarona@Rocky Helmet | 4 | 1.2% | 19.612 | 0.639 | 0.64 | 0.38 | no | 6 |
| Gengar | Rillaboom@Eject Button | 8 | 2.7% | 18.808 | 1.000 | 1.00 | 0.52 | yes | 2 |
| Gengar@Gengarite | Rillaboom@Eject Button | 8 | 2.7% | 18.808 | 1.000 | 1.00 | 0.52 | no | 2 |
| Indeedee | Corviknight@Psychic Seed | 4 | 0.8% | 18.460 | 1.000 | 1.00 | 0.15 | no | 6 |
| Gengar | Politoed@Sitrus Berry | 7 | 2.1% | 17.253 | 0.917 | 0.92 | 0.40 | yes | 3 |
| Gengar@Gengarite | Politoed@Sitrus Berry | 7 | 2.1% | 17.253 | 0.917 | 0.92 | 0.40 | no | 3 |
| Baxcalibur | Ninetales-Alola@Light Clay | 4 | 0.9% | 17.010 | 1.000 | 1.00 | 0.15 | yes | 4 |
| Incineroar@Passho Berry | Rillaboom@Eject Button | 4 | 1.5% | 16.909 | 0.532 | 0.53 | 0.46 | no | 1 |
| Corviknight@Psychic Seed | Excadrill@Focus Sash | 4 | 0.8% | 15.890 | 1.000 | 0.13 | 1.00 | no | 3 |
| Glimmora | Volcarona@Rocky Helmet | 5 | 1.5% | 14.017 | 0.748 | 0.75 | 0.27 | yes | 6 |
| Excadrill | Corviknight@Psychic Seed | 4 | 0.8% | 13.905 | 1.000 | 1.00 | 0.11 | no | 8 |
| Politoed | Incineroar@Passho Berry | 5 | 1.6% | 13.508 | 0.498 | 0.50 | 0.43 | yes | 3 |
| Corviknight | Excadrill@Focus Sash | 6 | 1.2% | 13.368 | 0.841 | 0.20 | 0.84 | yes | 3 |
| Corviknight | Tyranitar@Tyranitarite | 7 | 1.5% | 12.905 | 1.000 | 0.19 | 1.00 | yes | 1 |
| Corviknight@Psychic Seed | Tyranitar@Tyranitarite | 4 | 0.8% | 12.905 | 1.000 | 0.11 | 1.00 | no | 1 |
| Swampert | Pelipper@Focus Sash | 7 | 2.2% | 12.812 | 0.651 | 0.43 | 0.65 | yes | 1 |
| Pelipper@Focus Sash | Swampert@Swampertite | 7 | 2.2% | 12.812 | 0.651 | 0.65 | 0.43 | no | 7 |
| Volcarona | Glimmora@Glimmoranite | 6 | 2.0% | 12.647 | 0.605 | 0.61 | 0.41 | yes | n/a |
| Politoed | Gengar@Gengarite | 8 | 2.5% | 12.623 | 0.671 | 0.47 | 0.67 | yes | 5 |

### Item-level pairs by support
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | Salamence@Salamencite | 107 | 26.7% | 1.195 | 0.676 | 0.68 | 0.47 | yes | 2 |
| Sneasler | Salamence@Salamencite | 92 | 21.8% | 1.171 | 0.552 | 0.55 | 0.46 | no | 2 |
| Rillaboom | Incineroar@Sitrus Berry | 73 | 18.7% | 1.301 | 0.736 | 0.74 | 0.33 | yes | 3 |
| Sneasler | Rillaboom@Miracle Seed | 75 | 18.6% | 1.052 | 0.496 | 0.50 | 0.40 | no | 1 |
| Salamence | Rillaboom@Miracle Seed | 73 | 18.1% | 1.216 | 0.480 | 0.48 | 0.46 | yes | 1 |
| Rillaboom@Miracle Seed | Salamence@Salamencite | 73 | 18.1% | 1.216 | 0.480 | 0.46 | 0.48 | no | 2 |
| Incineroar | Rillaboom@Miracle Seed | 69 | 17.7% | 1.319 | 0.496 | 0.47 | 0.50 | yes | 1 |
| Rillaboom | Gholdengo@Life Orb | 58 | 16.6% | 1.437 | 0.813 | 0.81 | 0.29 | yes | 4 |
| Kingambit | Salamence@Salamencite | 65 | 15.9% | 1.390 | 0.549 | 0.40 | 0.55 | yes | 3 |
| Gholdengo | Rillaboom@Miracle Seed | 54 | 15.0% | 1.657 | 0.623 | 0.40 | 0.62 | yes | 1 |
| Rillaboom | Sneasler@Grassy Seed | 62 | 14.6% | 1.767 | 1.000 | 1.00 | 0.26 | yes | 1 |
| Gholdengo | Salamence@Salamencite | 53 | 14.2% | 1.492 | 0.590 | 0.36 | 0.59 | yes | 2 |
| Incineroar@Sitrus Berry | Rillaboom@Miracle Seed | 55 | 14.1% | 1.482 | 0.557 | 0.38 | 0.56 | no | 1 |
| Sneasler | Incineroar@Sitrus Berry | 55 | 13.9% | 1.162 | 0.547 | 0.55 | 0.29 | no | 5 |
| Gholdengo@Life Orb | Rillaboom@Miracle Seed | 48 | 13.7% | 1.781 | 0.670 | 0.36 | 0.67 | no | 1 |
| Rillaboom | Floette-Eternal@Floettite | 58 | 13.5% | 1.430 | 0.809 | 0.81 | 0.24 | yes | n/a |
| Incineroar | Floette-Eternal@Floettite | 52 | 12.7% | 2.128 | 0.761 | 0.76 | 0.35 | yes | n/a |
| Salamence | Gholdengo@Life Orb | 45 | 12.3% | 1.523 | 0.602 | 0.60 | 0.31 | yes | 4 |
| Gholdengo@Life Orb | Salamence@Salamencite | 45 | 12.3% | 1.523 | 0.602 | 0.31 | 0.60 | no | 2 |
| Sneasler | Floette-Eternal@Floettite | 52 | 12.0% | 1.525 | 0.719 | 0.72 | 0.25 | yes | n/a |
| Incineroar | Salamence@Salamencite | 48 | 11.3% | 0.802 | 0.317 | 0.29 | 0.32 | no | 3 |
| Rillaboom | Raichu@Raichunite Y | 37 | 10.9% | 1.654 | 0.936 | 0.94 | 0.19 | yes | n/a |
| Kingambit | Rillaboom@Miracle Seed | 42 | 10.6% | 0.977 | 0.368 | 0.28 | 0.37 | no | 2 |
| Rillaboom | Arcanine-Hisui@Focus Sash | 37 | 10.5% | 1.389 | 0.786 | 0.79 | 0.19 | yes | 7 |
| Salamence | Incineroar@Sitrus Berry | 42 | 10.1% | 1.007 | 0.398 | 0.40 | 0.26 | no | 5 |

## 4. Item Synergies
Species@item pairs whose lift beats the species-level pair's lift by >= 0.3.
| Token | Partner | Item lift | Species lift | Delta | Teams |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Milotic@Psychic Seed | Dragapult | 51.937 | 2.683 | 49.254 | 4 |
| Rillaboom@Eject Button | Gengar | 18.808 | 1.635 | 17.173 | 8 |
| Incineroar@Passho Berry | Politoed | 13.508 | 1.877 | 11.631 | 5 |
| Incineroar@Passho Berry | Gengar | 12.369 | 2.348 | 10.021 | 7 |
| Ninetales-Alola@Light Clay | Baxcalibur | 17.010 | 8.182 | 8.829 | 4 |
| Garchomp@Choice Scarf | Charizard | 9.181 | 3.433 | 5.748 | 7 |
| Indeedee@Choice Scarf | Corviknight | 23.942 | 18.460 | 5.482 | 6 |
| Volcarona@Rocky Helmet | Glimmora | 14.017 | 9.343 | 4.674 | 5 |
| Politoed@Sitrus Berry | Gengar | 17.253 | 12.623 | 4.631 | 7 |
| Incineroar@Passho Berry | Archaludon | 5.536 | 0.911 | 4.625 | 6 |
| Rillaboom@Eject Button | Archaludon | 5.386 | 0.764 | 4.622 | 4 |
| Milotic@Sitrus Berry | Excadrill | 7.328 | 3.067 | 4.261 | 12 |
| Farigiraf@Colbur Berry | Pelipper | 5.993 | 1.740 | 4.253 | 4 |
| Milotic@Sitrus Berry | Tyranitar | 7.306 | 3.073 | 4.233 | 14 |
| Sneasler@Psychic Seed | Gardevoir | 5.517 | 1.563 | 3.954 | 23 |
| Pelipper@Focus Sash | Swampert | 12.812 | 8.906 | 3.906 | 7 |
| Sneasler@Psychic Seed | Indeedee | 5.831 | 2.031 | 3.801 | 16 |
| Milotic@Psychic Seed | Indeedee-F | 4.134 | 0.415 | 3.719 | 4 |
| Incineroar@Chople Berry | Baxcalibur | 5.020 | 1.439 | 3.581 | 6 |
| Rillaboom@Occa Berry | Charizard | 3.836 | 0.531 | 3.305 | 5 |
| Glimmora@Glimmoranite | Volcarona | 12.647 | 9.343 | 3.303 | 6 |
| Garchomp@Life Orb | Gardevoir | 3.819 | 0.907 | 2.912 | 4 |
| Kingambit@Black Glasses | Froslass | 5.642 | 2.748 | 2.894 | 4 |
| Aerodactyl@Aerodactylite | Lucario | 10.057 | 7.305 | 2.752 | 4 |
| Glimmora@Glimmoranite | Pawmot | 6.140 | 3.750 | 2.390 | 4 |
| Indeedee-F@Rocky Helmet | Talonflame | 4.146 | 1.877 | 2.269 | 4 |
| Sneasler@White Herb | Froslass | 3.804 | 1.547 | 2.257 | 6 |
| Corviknight@Psychic Seed | Excadrill | 13.905 | 11.698 | 2.207 | 4 |
| Rillaboom@Occa Berry | Floette-Eternal | 3.591 | 1.430 | 2.160 | 10 |
| Garchomp@Life Orb | Charizard | 5.585 | 3.433 | 2.152 | 4 |
| Baxcalibur@Baxcalibrite | Ninetales-Alola | 10.298 | 8.182 | 2.117 | 6 |
| Indeedee-F@Rocky Helmet | Dragapult | 3.829 | 1.733 | 2.096 | 4 |
| Armarouge@Life Orb | Gardevoir | 3.971 | 1.894 | 2.077 | 6 |
| Politoed@Sitrus Berry | Archaludon | 8.642 | 6.627 | 2.015 | 7 |
| Kingambit@Focus Sash | Volcarona | 3.748 | 1.743 | 2.005 | 4 |
| Sneasler@White Herb | Corviknight | 4.085 | 2.122 | 1.963 | 4 |
| Sneasler@Focus Sash | Floette-Eternal | 3.412 | 1.525 | 1.887 | 14 |
| Tyranitar@Tyranitarite | Corviknight | 12.905 | 11.070 | 1.835 | 7 |
| Sneasler@Psychic Seed | Indeedee-F | 2.828 | 1.117 | 1.712 | 36 |
| Volcarona@Rocky Helmet | Kingambit | 3.454 | 1.743 | 1.711 | 7 |
| Sneasler@Psychic Seed | Armarouge | 2.780 | 1.083 | 1.697 | 13 |
| Incineroar@Chople Berry | Farigiraf | 2.603 | 0.913 | 1.691 | 4 |
| Excadrill@Focus Sash | Corviknight | 13.368 | 11.698 | 1.670 | 6 |
| Kingambit@Chople Berry | Whimsicott | 2.799 | 1.141 | 1.658 | 5 |
| Rillaboom@Sitrus Berry | Arcanine-Hisui | 3.014 | 1.380 | 1.633 | 6 |
| Milotic@Sitrus Berry | Indeedee | 4.050 | 2.464 | 1.586 | 5 |
| Milotic@Leftovers | Absol | 3.572 | 2.037 | 1.535 | 4 |
| Delphox@Delphoxite | Floette-Eternal | 4.721 | 3.195 | 1.526 | 4 |
| Indeedee@Choice Scarf | Excadrill | 9.022 | 7.513 | 1.509 | 11 |
| Incineroar@Chople Berry | Golisopod | 2.094 | 0.601 | 1.493 | 5 |
| Rillaboom@Eject Button | Incineroar | 2.797 | 1.325 | 1.472 | 8 |
| Garchomp@Garchompite Z | Metagross | 3.669 | 2.215 | 1.454 | 6 |
| Farigiraf@Sitrus Berry | Grimmsnarl | 3.780 | 2.335 | 1.445 | 4 |
| Basculegion@Choice Scarf | Pelipper | 2.625 | 1.194 | 1.431 | 7 |
| Indeedee-F@Psychic Seed | Charizard | 2.519 | 1.098 | 1.421 | 4 |
| Indeedee-F@Colbur Berry | Gardevoir | 5.537 | 4.134 | 1.402 | 12 |
| Garchomp@Choice Scarf | Floette-Eternal | 2.731 | 1.334 | 1.397 | 4 |
| Incineroar@Chople Berry | Charizard | 2.558 | 1.199 | 1.359 | 4 |
| Rillaboom@Expert Belt | Salamence | 2.531 | 1.195 | 1.336 | 4 |
| Rillaboom@Life Orb | Kingambit | 2.411 | 1.106 | 1.304 | 12 |
| Basculegion@Choice Scarf | Archaludon | 2.473 | 1.179 | 1.295 | 7 |
| Volcarona@Grassy Seed | Incineroar | 1.793 | 0.555 | 1.238 | 4 |
| Indeedee-F@Psychic Seed | Torkoal | 4.523 | 3.305 | 1.218 | 6 |
| Garchomp@Life Orb | Basculegion | 1.947 | 0.741 | 1.205 | 4 |
| Sneasler@Psychic Seed | Excadrill | 2.372 | 1.188 | 1.184 | 10 |
| Charizard@Charizardite Y | Venusaur | 11.789 | 10.619 | 1.170 | 8 |
| Indeedee-F@Rocky Helmet | Absol | 3.427 | 2.271 | 1.156 | 5 |
| Indeedee-F@Psychic Seed | Golisopod | 2.505 | 1.352 | 1.153 | 8 |
| Sinistcha@Colbur Berry | Milotic | 2.377 | 1.232 | 1.144 | 4 |
| Farigiraf@Sitrus Berry | Politoed | 2.876 | 1.777 | 1.099 | 4 |
| Sinistcha@Colbur Berry | Tyranitar | 4.110 | 3.021 | 1.088 | 4 |
| Indeedee-F@Rocky Helmet | Metagross | 3.002 | 1.936 | 1.065 | 7 |
| Basculegion@Focus Sash | Indeedee-F | 2.055 | 0.992 | 1.063 | 5 |
| Basculegion@Life Orb | Baxcalibur | 2.352 | 1.303 | 1.049 | 6 |
| Kingambit@Focus Sash | Charizard | 2.031 | 0.992 | 1.039 | 4 |
| Baxcalibur@Baxcalibrite | Volcarona | 5.047 | 4.010 | 1.037 | 5 |
| Tyranitar@Tyranitarite | Excadrill | 12.106 | 11.070 | 1.036 | 30 |
| Basculegion@Choice Scarf | Golisopod | 1.900 | 0.880 | 1.020 | 9 |
| Basculegion@Choice Scarf | Pawmot | 2.782 | 1.783 | 0.998 | 4 |
| Rillaboom@Sitrus Berry | Kingambit | 2.072 | 1.106 | 0.966 | 10 |
| Rillaboom@Life Orb | Milotic | 2.096 | 1.152 | 0.944 | 6 |
| Sneasler@Focus Sash | Baxcalibur | 2.252 | 1.317 | 0.935 | 4 |
| Garchomp@Life Orb | Indeedee-F | 1.625 | 0.692 | 0.933 | 5 |
| Sneasler@Grassy Seed | Floette-Eternal | 2.448 | 1.525 | 0.923 | 28 |
| Kingambit@Focus Sash | Sylveon | 1.849 | 0.950 | 0.899 | 5 |
| Sinistcha@Colbur Berry | Sneasler | 1.524 | 0.631 | 0.893 | 6 |
| Garchomp@Garchompite Z | Pawmot | 2.251 | 1.359 | 0.892 | 4 |
| Sneasler@Psychic Seed | Tyranitar | 2.098 | 1.210 | 0.888 | 11 |
| Farigiraf@Colbur Berry | Incineroar | 1.791 | 0.913 | 0.878 | 4 |
| Farigiraf@Grassy Seed | Rillaboom | 1.767 | 0.891 | 0.876 | 4 |
| Dragapult@Life Orb | Arcanine-Hisui | 3.560 | 2.686 | 0.874 | 4 |
| Dragapult@Life Orb | Milotic | 3.557 | 2.683 | 0.874 | 4 |
| Indeedee-F@Colbur Berry | Pelipper | 2.223 | 1.349 | 0.874 | 5 |
| Basculegion@Choice Scarf | Gardevoir | 2.768 | 1.915 | 0.853 | 8 |
| Basculegion@Life Orb | Glimmora | 2.354 | 1.539 | 0.815 | 5 |
| Basculegion@Life Orb | Floette-Eternal | 2.145 | 1.332 | 0.813 | 15 |
| Garchomp@Garchompite Z | Milotic | 2.040 | 1.231 | 0.809 | 11 |
| Basculegion@Life Orb | Lucario | 3.159 | 2.354 | 0.805 | 7 |
| Farigiraf@Sitrus Berry | Sylveon | 2.077 | 1.283 | 0.794 | 5 |
| Sneasler@Grassy Seed | Rillaboom | 1.767 | 0.982 | 0.785 | 62 |
| Sinistcha@Colbur Berry | Kingambit | 1.612 | 0.831 | 0.782 | 4 |
| Indeedee@Choice Scarf | Tyranitar | 7.183 | 6.460 | 0.723 | 11 |
| Basculegion@Life Orb | Volcarona | 2.046 | 1.333 | 0.713 | 4 |
| Delphox@Delphoxite | Incineroar | 2.197 | 1.487 | 0.710 | 4 |
| Politoed@Sitrus Berry | Incineroar | 2.566 | 1.877 | 0.689 | 7 |
| Sneasler@Psychic Seed | Torkoal | 1.385 | 0.698 | 0.687 | 5 |
| Milotic@Sitrus Berry | Gholdengo | 2.273 | 1.588 | 0.685 | 11 |
| Farigiraf@Colbur Berry | Golisopod | 3.856 | 3.173 | 0.683 | 4 |
| Sinistcha@Sitrus Berry | Golisopod | 3.262 | 2.580 | 0.682 | 4 |
| Farigiraf@Sitrus Berry | Charizard | 2.843 | 2.162 | 0.681 | 7 |
| Indeedee-F@Sitrus Berry | Sneasler | 1.796 | 1.117 | 0.679 | 5 |
| Pawmot@Focus Sash | Glimmora | 4.421 | 3.750 | 0.671 | 4 |
| Rillaboom@Occa Berry | Incineroar | 1.966 | 1.325 | 0.641 | 12 |
| Sneasler@White Herb | Volcarona | 1.498 | 0.871 | 0.626 | 4 |
| Milotic@Leftovers | Golisopod | 1.648 | 1.024 | 0.624 | 10 |
| Garchomp@Choice Scarf | Kingambit | 1.543 | 0.923 | 0.620 | 5 |
| Charizard@Charizardite Y | Grimmsnarl | 6.183 | 5.569 | 0.614 | 6 |
| Rillaboom@Life Orb | Golisopod | 1.239 | 0.625 | 0.614 | 4 |
| Milotic@Sitrus Berry | Salamence | 1.816 | 1.206 | 0.610 | 15 |
| Sneasler@Focus Sash | Kingambit | 1.853 | 1.250 | 0.603 | 14 |
| Kingambit@Life Orb | Arcanine-Hisui | 1.716 | 1.119 | 0.597 | 4 |
| Sinistcha@Colbur Berry | Incineroar | 1.746 | 1.151 | 0.595 | 5 |
| Archaludon@Leftovers | Swampert | 8.922 | 8.332 | 0.590 | 10 |
| Kingambit@Life Orb | Floette-Eternal | 2.191 | 1.605 | 0.586 | 8 |
| Sneasler@White Herb | Baxcalibur | 1.901 | 1.317 | 0.585 | 7 |
| Milotic@Leftovers | Baxcalibur | 1.861 | 1.281 | 0.580 | 4 |
| Torkoal@Charcoal | Hatterene | 10.330 | 9.752 | 0.578 | 7 |
| Pelipper@Sitrus Berry | Farigiraf | 2.314 | 1.740 | 0.574 | 5 |
| Glimmora@Glimmoranite | Salamence | 1.697 | 1.128 | 0.569 | 7 |
| Rillaboom@Sitrus Berry | Floette-Eternal | 1.997 | 1.430 | 0.567 | 7 |
| Dragapult@Life Orb | Indeedee-F | 2.297 | 1.733 | 0.564 | 4 |
| Incineroar@Sitrus Berry | Dragonite | 1.868 | 1.325 | 0.543 | 4 |
| Rillaboom@Miracle Seed | Raichu | 2.191 | 1.654 | 0.538 | 33 |
| Garchomp@Choice Scarf | Incineroar | 2.000 | 1.463 | 0.537 | 7 |
| Sneasler@White Herb | Pelipper | 1.302 | 0.766 | 0.536 | 8 |
| Farigiraf@Sitrus Berry | Torkoal | 3.753 | 3.218 | 0.535 | 6 |
| Basculegion@Choice Scarf | Charizard | 1.451 | 0.919 | 0.531 | 4 |
| Rillaboom@Sitrus Berry | Basculegion | 1.525 | 0.997 | 0.528 | 6 |
| Rillaboom@Miracle Seed | Blastoise | 1.568 | 1.042 | 0.526 | 5 |
| Pelipper@Focus Sash | Archaludon | 6.952 | 6.428 | 0.524 | 12 |
| Sneasler@Grassy Seed | Gholdengo | 1.371 | 0.851 | 0.521 | 19 |
| Volcarona@Rocky Helmet | Salamence | 1.937 | 1.420 | 0.517 | 5 |
| Sneasler@Grassy Seed | Arcanine-Hisui | 1.418 | 0.905 | 0.512 | 12 |
| Sneasler@White Herb | Raichu | 1.234 | 0.732 | 0.503 | 5 |
| Tyranitar@Tyranitarite | Sinistcha | 3.522 | 3.021 | 0.501 | 6 |
| Armarouge@Life Orb | Torkoal | 3.859 | 3.362 | 0.497 | 5 |
| Indeedee@Choice Scarf | Milotic | 2.961 | 2.464 | 0.497 | 6 |
| Kingambit@Life Orb | Incineroar | 1.211 | 0.715 | 0.496 | 10 |
| Sneasler@Focus Sash | Milotic | 1.348 | 0.853 | 0.495 | 6 |
| Volcarona@Grassy Seed | Rillaboom | 1.767 | 1.275 | 0.492 | 6 |
| Milotic@Leftovers | Farigiraf | 1.461 | 0.968 | 0.492 | 5 |
| Gholdengo@Life Orb | Staraptor | 3.187 | 2.697 | 0.490 | 11 |
| Indeedee-F@Psychic Seed | Armarouge | 4.345 | 3.860 | 0.485 | 8 |
| Archaludon@Leftovers | Grimmsnarl | 7.323 | 6.839 | 0.485 | 9 |
| Sneasler@Grassy Seed | Incineroar | 1.520 | 1.037 | 0.483 | 34 |
| Kingambit@Chople Berry | Pawmot | 1.793 | 1.311 | 0.482 | 4 |
| Sneasler@Grassy Seed | Lucario | 1.130 | 0.649 | 0.481 | 4 |
| Rillaboom@Sitrus Berry | Salamence | 1.676 | 1.195 | 0.480 | 12 |
| Blastoise@Blastoisinite | Indeedee-F | 3.619 | 3.141 | 0.478 | 7 |
| Archaludon@Leftovers | Politoed | 7.097 | 6.627 | 0.470 | 10 |
| Excadrill@Focus Sash | Sinistcha | 3.749 | 3.280 | 0.468 | 5 |
| Kingambit@Focus Sash | Floette-Eternal | 2.072 | 1.605 | 0.466 | 10 |
| Venusaur@Focus Sash | Indeedee-F | 2.501 | 2.037 | 0.464 | 4 |
| Pelipper@Sitrus Berry | Basculegion | 1.656 | 1.194 | 0.461 | 6 |
| Rillaboom@Miracle Seed | Staraptor | 1.601 | 1.141 | 0.460 | 10 |
| Rillaboom@Sitrus Berry | Sneasler | 1.440 | 0.982 | 0.458 | 12 |
| Basculegion@Choice Scarf | Indeedee-F | 1.445 | 0.992 | 0.453 | 12 |
| Ceruledge@Grassy Seed | Rillaboom | 1.767 | 1.320 | 0.447 | 4 |
| Kingambit@Chople Berry | Arcanine-Hisui | 1.562 | 1.119 | 0.443 | 9 |
| Incineroar@Leftovers | Rillaboom | 1.767 | 1.325 | 0.442 | 4 |
| Basculegion@Life Orb | Sylveon | 1.696 | 1.254 | 0.441 | 7 |
| Indeedee-F@Rocky Helmet | Basculegion | 1.426 | 0.992 | 0.434 | 14 |
| Incineroar@Sitrus Berry | Kommo-o | 2.482 | 2.052 | 0.430 | 5 |
| Gholdengo@Grassy Seed | Rillaboom | 1.767 | 1.350 | 0.417 | 4 |
| Kingambit@Chople Berry | Basculegion | 1.678 | 1.270 | 0.408 | 17 |
| Milotic@Leftovers | Raichu | 1.593 | 1.187 | 0.406 | 6 |
| Kommo-o@Leftovers | Incineroar | 2.457 | 2.052 | 0.405 | 5 |
| Sneasler@Grassy Seed | Charizard | 0.985 | 0.583 | 0.401 | 6 |
| Kingambit@Chople Berry | Glimmora | 1.940 | 1.545 | 0.395 | 5 |
| Indeedee-F@Colbur Berry | Torkoal | 3.692 | 3.305 | 0.387 | 6 |
| Excadrill@Focus Sash | Indeedee | 7.900 | 7.513 | 0.387 | 13 |
| Sneasler@Focus Sash | Incineroar | 1.421 | 1.037 | 0.384 | 12 |
| Charizard@Charizardite Y | Garchomp | 3.811 | 3.433 | 0.378 | 14 |
| Milotic@Leftovers | Incineroar | 0.963 | 0.585 | 0.378 | 12 |
| Incineroar@Sitrus Berry | Aerodactyl | 1.282 | 0.910 | 0.373 | 4 |
| Sneasler@White Herb | Golisopod | 0.999 | 0.630 | 0.370 | 10 |
| Kingambit@Chople Berry | Salamence | 1.759 | 1.390 | 0.368 | 32 |
| Pelipper@Sitrus Berry | Archaludon | 6.796 | 6.428 | 0.367 | 11 |
| Sneasler@Psychic Seed | Pelipper | 1.127 | 0.766 | 0.362 | 6 |
| Indeedee-F@Colbur Berry | Sneasler | 1.455 | 1.117 | 0.339 | 19 |
| Baxcalibur@Baxcalibrite | Basculegion | 1.640 | 1.303 | 0.337 | 8 |
| Indeedee-F@Colbur Berry | Golisopod | 1.688 | 1.352 | 0.336 | 6 |
| Indeedee-F@Psychic Seed | Kingambit | 0.933 | 0.598 | 0.335 | 5 |
| Rillaboom@Miracle Seed | Ceruledge | 1.654 | 1.320 | 0.333 | 4 |
| Baxcalibur@Baxcalibrite | Milotic | 1.612 | 1.281 | 0.331 | 5 |
| Garchomp@Garchompite Z | Sneasler | 1.287 | 0.962 | 0.325 | 20 |
| Rillaboom@Miracle Seed | Lucario | 1.751 | 1.428 | 0.323 | 18 |
| Basculegion@Life Orb | Whimsicott | 2.345 | 2.022 | 0.323 | 4 |
| Glimmora@Glimmoranite | Kingambit | 1.865 | 1.545 | 0.320 | 6 |
| Incineroar@Sitrus Berry | Sinistcha | 1.470 | 1.151 | 0.318 | 7 |
| Armarouge@Life Orb | Kingambit | 1.220 | 0.902 | 0.318 | 7 |
| Garchomp@Garchompite Z | Rillaboom | 1.233 | 0.917 | 0.316 | 22 |
| Incineroar@Sitrus Berry | Lucario | 1.900 | 1.588 | 0.312 | 13 |
| Aerodactyl@Aerodactylite | Rillaboom | 1.184 | 0.877 | 0.307 | 4 |
| Rillaboom@Miracle Seed | Gholdengo | 1.657 | 1.350 | 0.307 | 54 |
| Rillaboom@Miracle Seed | Sylveon | 1.363 | 1.058 | 0.304 | 22 |
| Indeedee-F@Psychic Seed | Rillaboom | 0.506 | 0.205 | 0.302 | 5 |
| Kingambit@Focus Sash | Indeedee-F | 0.899 | 0.598 | 0.301 | 6 |
| Volcarona@Rocky Helmet | Rillaboom | 1.575 | 1.275 | 0.300 | 6 |

## 5. Top Triples
| Tokens | Teams | Families | Support | Lift3 | Gain |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Gengar + Incineroar@Passho Berry + Politoed@Sitrus Berry | 5 | 4 | 1.6% | 403.92 | 18.81 |
| Gengar@Gengarite + Incineroar@Passho Berry + Politoed@Sitrus Berry | 5 | 4 | 1.6% | 403.92 | 18.81 |
| Corviknight + Excadrill@Focus Sash + Indeedee@Choice Scarf | 6 | 5 | 1.2% | 380.45 | 15.89 |
| Corviknight + Excadrill + Indeedee@Choice Scarf | 6 | 5 | 1.2% | 332.92 | 13.90 |
| Gengar + Incineroar@Passho Berry + Rillaboom@Eject Button | 4 | 4 | 1.5% | 318.02 | 16.91 |
| Gengar@Gengarite + Incineroar@Passho Berry + Rillaboom@Eject Button | 4 | 4 | 1.5% | 318.02 | 16.91 |
| Corviknight + Indeedee@Choice Scarf + Tyranitar@Tyranitarite | 6 | 5 | 1.2% | 308.99 | 12.91 |
| Corviknight + Indeedee@Choice Scarf + Tyranitar | 6 | 5 | 1.2% | 265.05 | 11.07 |
| Gengar + Incineroar@Passho Berry + Politoed | 5 | 4 | 1.6% | 254.05 | 18.81 |
| Gengar@Gengarite + Incineroar@Passho Berry + Politoed | 5 | 4 | 1.6% | 254.05 | 18.81 |
| Corviknight + Excadrill@Focus Sash + Indeedee | 6 | 5 | 1.2% | 246.77 | 13.37 |
| Corviknight + Indeedee + Tyranitar@Tyranitarite | 7 | 6 | 1.5% | 238.24 | 12.91 |
| Archaludon@Leftovers + Incineroar@Passho Berry + Politoed@Sitrus Berry | 5 | 4 | 1.6% | 222.30 | 10.35 |
| Corviknight + Excadrill + Indeedee | 6 | 5 | 1.2% | 215.94 | 11.70 |
| Archaludon + Incineroar@Passho Berry + Politoed@Sitrus Berry | 5 | 4 | 1.6% | 207.59 | 9.67 |
| Corviknight + Indeedee + Tyranitar | 7 | 6 | 1.5% | 204.36 | 11.07 |
| Corviknight + Excadrill@Focus Sash + Tyranitar@Tyranitarite | 6 | 5 | 1.2% | 172.52 | 12.91 |
| Archaludon@Leftovers + Gengar + Politoed@Sitrus Berry | 6 | 5 | 1.9% | 157.97 | 9.16 |
| Archaludon@Leftovers + Gengar@Gengarite + Politoed@Sitrus Berry | 6 | 5 | 1.9% | 157.97 | 9.16 |
| Corviknight + Excadrill + Tyranitar@Tyranitarite | 6 | 5 | 1.2% | 150.96 | 11.70 |

## 6. Resolution Sweep
Connected communities only: unconnected species are outside the graph Louvain runs on.
| Resolution | Modularity | Community count |
| :--- | :--- | :--- |
| 0.50 | 0.68 | 4 |
| 0.75 | 0.60 | 5 |
| 1.00 | 0.54 | 6 |
| 1.25 | 0.49 | 7 |
| 1.50 | 0.45 | 8 |

## 7. Communities
- Unconnected species (no pair with at least minPairTeams teams and lift above communityMinLift, so in no community): Annihilape, Sirfetch’d, Hydreigon, Camerupt, Toxtricity, Scrafty, Pyroar, Maushold, Klefki, Vanilluxe, Aegislash, Tsareena, Alakazam, Altaria, Pincurchin, Lycanroc-Dusk, Zoroark-Hisui, Typhlosion-Hisui, Scizor

### Community 0: Rillaboom / Sneasler / Salamence
- Primary teams: 202 (primary share 51.7%), hybrid teams: 73 (hybrid share 18.6%)
- Date range: 2026-09-09 to 2026-09-22
- Core pairs: Gengar+Rillaboom@Eject Button, Baxcalibur+Ninetales-Alola@Light Clay, Glimmora+Volcarona@Rocky Helmet, Politoed+Incineroar@Passho Berry, Volcarona+Glimmora@Glimmoranite, Gengar+Incineroar@Passho Berry, Ninetales-Alola+Baxcalibur@Baxcalibrite, Lucario+Aerodactyl@Aerodactylite, Glimmora+Volcarona, Baxcalibur+Ninetales-Alola, Aerodactyl+Lucario, Aerodactyl+Lucario@Lucarionite Z, Pawmot+Glimmora@Glimmoranite, Indeedee+Sneasler@Psychic Seed, Froslass+Kingambit@Black Glasses, Aerodactyl+Sylveon@Fairy Feather, Archaludon+Incineroar@Passho Berry, Gardevoir+Sneasler@Psychic Seed, Aerodactyl+Sylveon, Archaludon+Rillaboom@Eject Button, Glimmora+Whimsicott, Volcarona+Baxcalibur@Baxcalibrite, Baxcalibur+Incineroar@Chople Berry, Floette-Eternal+Delphox@Delphoxite, Glimmora+Pawmot@Focus Sash, Baxcalibur+Volcarona, Charizard+Rillaboom@Occa Berry, Froslass+Sneasler@White Herb, Glimmora+Pawmot, Volcarona+Kingambit@Focus Sash, Floette-Eternal+Rillaboom@Occa Berry, Kingambit+Volcarona@Rocky Helmet, Floette-Eternal+Sneasler@Focus Sash, Basculegion+Talonflame, Delphox+Floette-Eternal, Delphox+Floette-Eternal@Floettite, Lucario+Basculegion@Life Orb, Arcanine-Hisui+Rillaboom@Sitrus Berry, Lucario+Sylveon@Fairy Feather, Indeedee-F+Sneasler@Psychic Seed, Whimsicott+Kingambit@Chople Berry, Incineroar+Rillaboom@Eject Button, Lucario+Sylveon, Sylveon+Lucario@Lucarionite Z, Pawmot+Basculegion@Choice Scarf, Armarouge+Sneasler@Psychic Seed, Gardevoir+Basculegion@Choice Scarf, Froslass+Kingambit, Kingambit+Froslass@Froslassite, Floette-Eternal+Garchomp@Choice Scarf, Pelipper+Basculegion@Choice Scarf, Farigiraf+Incineroar@Chople Berry, Incineroar+Politoed@Sitrus Berry, Charizard+Incineroar@Chople Berry, Froslass+Arcanine-Hisui@Focus Sash, Salamence+Rillaboom@Expert Belt, Kommo-o+Incineroar@Sitrus Berry, Archaludon+Basculegion@Choice Scarf, Incineroar+Kommo-o@Leftovers, Floette-Eternal+Sneasler@Grassy Seed, Arcanine-Hisui+Froslass, Arcanine-Hisui+Froslass@Froslassite, Garchomp+Whimsicott, Kingambit+Rillaboom@Life Orb, Excadrill+Sneasler@Psychic Seed, Basculegion+Lucario, Basculegion+Lucario@Lucarionite Z, Glimmora+Basculegion@Life Orb, Baxcalibur+Basculegion@Life Orb, Gengar+Incineroar, Incineroar+Gengar@Gengarite, Whimsicott+Basculegion@Life Orb, Floette-Eternal+Incineroar@Chople Berry, Baxcalibur+Sneasler@Focus Sash, Pawmot+Garchomp@Garchompite Z, Basculegion+Whimsicott@Focus Sash, Incineroar+Delphox@Delphoxite, Raichu+Rillaboom@Miracle Seed, Floette-Eternal+Kingambit@Life Orb, Floette-Eternal+Basculegion@Life Orb, Floette-Eternal+Incineroar, Incineroar+Floette-Eternal@Floettite, Floette-Eternal+Incineroar@Sitrus Berry, Corviknight+Sneasler, Sneasler+Indeedee@Focus Sash, Tyranitar+Sneasler@Psychic Seed, Milotic+Rillaboom@Life Orb, Golisopod+Incineroar@Chople Berry, Kingambit+Rillaboom@Sitrus Berry, Floette-Eternal+Kingambit@Focus Sash, Indeedee-F+Basculegion@Focus Sash, Incineroar+Kommo-o, Volcarona+Basculegion@Life Orb, Salamence+Excadrill@Focus Sash, Charizard+Kingambit@Focus Sash, Indeedee+Sneasler, Basculegion+Whimsicott, Excadrill+Salamence, Excadrill+Salamence@Salamencite, Incineroar+Garchomp@Choice Scarf, Floette-Eternal+Rillaboom@Sitrus Berry, Salamence+Tyranitar, Tyranitar+Salamence@Salamencite, Sneasler+Indeedee@Choice Scarf, Incineroar+Rillaboom@Occa Berry, Basculegion+Garchomp@Life Orb, Glimmora+Kingambit@Chople Berry, Salamence+Volcarona@Rocky Helmet, Blaziken+Kingambit, Aerodactyl+Basculegion, Basculegion+Gardevoir, Basculegion+Gardevoir@Gardevoirite, Salamence+Tyranitar@Tyranitarite, Baxcalibur+Sneasler@White Herb, Lucario+Incineroar@Sitrus Berry, Golisopod+Basculegion@Choice Scarf, Primarina+Salamence, Primarina+Salamence@Salamencite, Incineroar+Politoed, Ninetales-Alola+Incineroar@Sitrus Berry, Dragonite+Incineroar@Sitrus Berry, Delphox+Indeedee-F, Kingambit+Glimmora@Glimmoranite, Volcarona+Kingambit@Chople Berry, Baxcalibur+Milotic@Leftovers, Kingambit+Sneasler@Focus Sash, Sylveon+Kingambit@Focus Sash, Floette-Eternal+Whimsicott, Whimsicott+Floette-Eternal@Floettite, Salamence+Milotic@Sitrus Berry, Sneasler+Indeedee-F@Sitrus Berry, Incineroar+Volcarona@Grassy Seed, Pawmot+Kingambit@Chople Berry, Incineroar+Farigiraf@Colbur Berry, Lucario+Garchomp@Garchompite Z, Basculegion+Pawmot, Rillaboom+Farigiraf@Grassy Seed, Rillaboom+Volcarona@Grassy Seed, Rillaboom+Sneasler@Grassy Seed, Rillaboom+Gholdengo@Grassy Seed, Rillaboom+Incineroar@Leftovers, Salamence+Kingambit@Chople Berry, Salamence+Indeedee@Choice Scarf, Incineroar+Ninetales-Alola, Lucario+Rillaboom@Miracle Seed, Incineroar+Sinistcha@Colbur Berry, Kingambit+Volcarona, Arcanine-Hisui+Kingambit@Life Orb, Indeedee+Sneasler@White Herb, Salamence+Glimmora@Glimmoranite, Sylveon+Basculegion@Life Orb, Kingambit+Kommo-o, Basculegion+Kingambit@Chople Berry, Salamence+Rillaboom@Sitrus Berry, Sneasler+Delphox@Delphoxite, Gholdengo+Rillaboom@Miracle Seed, Basculegion+Pelipper@Sitrus Berry, Raichu+Rillaboom, Rillaboom+Raichu@Raichunite Y, Delphox+Kingambit, Basculegion+Baxcalibur@Baxcalibrite, Gengar+Rillaboom, Rillaboom+Gengar@Gengarite, Basculegion+Glimmora@Glimmoranite, Rillaboom+Politoed@Sitrus Berry, Kingambit+Sinistcha@Colbur Berry, Milotic+Baxcalibur@Baxcalibrite, Floette-Eternal+Kingambit, Kingambit+Floette-Eternal@Floettite, Garchomp+Volcarona, Staraptor+Rillaboom@Miracle Seed, Incineroar+Lucario, Incineroar+Lucario@Lucarionite Z, Rillaboom+Volcarona@Rocky Helmet, Garchomp+Incineroar@Sitrus Berry, Garchomp+Lucario, Garchomp+Lucario@Lucarionite Z, Blastoise+Rillaboom@Miracle Seed, Gardevoir+Sneasler, Sneasler+Gardevoir@Gardevoirite, Indeedee+Salamence, Indeedee+Salamence@Salamencite, Arcanine-Hisui+Kingambit@Chople Berry, Kingambit+Basculegion@Life Orb, Incineroar+Garchomp@Garchompite Z, Rillaboom+Kommo-o@Leftovers, Froslass+Sneasler, Sneasler+Froslass@Froslassite, Glimmora+Kingambit, Kingambit+Garchomp@Choice Scarf, Basculegion+Glimmora, Delphox+Sneasler, Arcanine-Hisui+Rillaboom@Miracle Seed, Basculegion+Rillaboom@Sitrus Berry, Floette-Eternal+Sneasler, Sneasler+Floette-Eternal@Floettite, Sneasler+Sinistcha@Colbur Berry, Salamence+Gholdengo@Life Orb, Incineroar+Sneasler@Grassy Seed, Basculegion+Pawmot@Focus Sash, Charizard+Floette-Eternal, Charizard+Floette-Eternal@Floettite, Sneasler+Kingambit@Life Orb, Gholdengo+Incineroar@Sitrus Berry, Volcarona+Sneasler@White Herb, Salamence+Arcanine-Hisui@Focus Sash, Gholdengo+Salamence, Gholdengo+Salamence@Salamencite, Blastoise+Kingambit, Delphox+Incineroar, Floette-Eternal+Kingambit@Chople Berry, Sinistcha+Incineroar@Sitrus Berry, Garchomp+Incineroar, Arcanine-Hisui+Salamence, Arcanine-Hisui+Salamence@Salamencite, Sneasler+Indeedee-F@Colbur Berry, Charizard+Basculegion@Choice Scarf, Sneasler+Baxcalibur@Baxcalibrite, Indeedee-F+Basculegion@Choice Scarf, Rillaboom+Incineroar@Passho Berry, Excadrill+Sneasler@White Herb, Sneasler+Rillaboom@Sitrus Berry, Baxcalibur+Incineroar, Rillaboom+Gholdengo@Life Orb, Floette-Eternal+Rillaboom, Rillaboom+Floette-Eternal@Floettite, Lucario+Rillaboom, Rillaboom+Lucario@Lucarionite Z, Basculegion+Indeedee-F@Rocky Helmet, Floette-Eternal+Rillaboom@Miracle Seed, Incineroar+Sneasler@Focus Sash, Salamence+Volcarona, Volcarona+Salamence@Salamencite, Arcanine-Hisui+Sneasler@Grassy Seed, Salamence+Kingambit@Focus Sash, Incineroar+Pawmot@Focus Sash, Kingambit+Sneasler@Grassy Seed, Froslass+Rillaboom, Rillaboom+Froslass@Froslassite, Kingambit+Basculegion@Focus Sash, Salamence+Basculegion@Life Orb, Kingambit+Salamence, Kingambit+Salamence@Salamencite, Rillaboom+Arcanine-Hisui@Focus Sash, Baxcalibur+Raichu, Baxcalibur+Raichu@Raichunite Y, Torkoal+Sneasler@Psychic Seed, Floette-Eternal+Gholdengo, Gholdengo+Floette-Eternal@Floettite, Arcanine-Hisui+Rillaboom, Incineroar+Baxcalibur@Baxcalibrite, Salamence+Sneasler@White Herb, Gholdengo+Sneasler@Grassy Seed, Sylveon+Rillaboom@Miracle Seed, Politoed+Rillaboom, Gengar+Incineroar@Sitrus Berry, Indeedee-F+Whimsicott@Focus Sash, Garchomp+Pawmot, Gholdengo+Rillaboom, Sneasler+Armarouge@Focus Sash, Milotic+Sneasler@Focus Sash, Kingambit+Pawmot@Focus Sash, Floette-Eternal+Garchomp, Garchomp+Floette-Eternal@Floettite, Basculegion+Volcarona, Basculegion+Floette-Eternal, Basculegion+Floette-Eternal@Floettite, Floette-Eternal+Sinistcha, Sinistcha+Floette-Eternal@Floettite, Pawmot+Rillaboom@Miracle Seed, Dragonite+Incineroar, Incineroar+Rillaboom, Incineroar+Pawmot, Ceruledge+Rillaboom, Incineroar+Rillaboom@Miracle Seed, Baxcalibur+Sneasler, Rillaboom+Incineroar@Chople Berry, Kingambit+Pawmot, Basculegion+Baxcalibur, Pelipper+Sneasler@White Herb, Tyranitar+Sneasler@White Herb, Sneasler+Kingambit@Chople Berry, Rillaboom+Incineroar@Sitrus Berry, Kommo-o+Rillaboom, Salamence+Sneasler@Grassy Seed, Rillaboom+Basculegion@Life Orb, Sneasler+Garchomp@Garchompite Z, Sneasler+Basculegion@Life Orb, Rillaboom+Baxcalibur@Baxcalibrite, Aerodactyl+Incineroar@Sitrus Berry, Gholdengo+Rillaboom@Occa Berry, Baxcalibur+Milotic, Kommo-o+Rillaboom@Miracle Seed, Rillaboom+Volcarona, Basculegion+Kingambit, Salamence+Sylveon, Sylveon+Salamence@Salamencite, Rillaboom+Kingambit@Life Orb, Rillaboom+Milotic@Leftovers, Sneasler+Kingambit@Focus Sash, Sneasler+Armarouge@Life Orb, Basculegion+Sylveon, Garchomp+Sneasler@Focus Sash, Kingambit+Sneasler, Garchomp+Sneasler@Psychic Seed, Golisopod+Rillaboom@Life Orb, Salamence+Sylveon@Fairy Feather, Floette-Eternal+Gholdengo@Life Orb, Raichu+Sneasler@White Herb, Gholdengo+Incineroar, Rillaboom+Garchomp@Garchompite Z, Sneasler+Basculegion@Focus Sash, Baxcalibur+Sneasler@Grassy Seed, Kingambit+Sneasler@White Herb, Kingambit+Armarouge@Life Orb, Basculegion+Sneasler@White Herb, Salamence+Basculegion@Focus Sash, Salamence+Rillaboom@Miracle Seed, Ninetales-Alola+Rillaboom, Incineroar+Kingambit@Life Orb, Salamence+Rillaboom@Life Orb, Sneasler+Tyranitar, Floette-Eternal+Charizard@Charizardite Y, Rillaboom+Kingambit@Focus Sash, Milotic+Salamence, Milotic+Salamence@Salamencite, Rillaboom+Salamence, Rillaboom+Salamence@Salamencite, Rillaboom+Staraptor@Staraptite, Rillaboom+Aerodactyl@Aerodactylite, Milotic+Rillaboom, Primarina+Rillaboom, Rillaboom+Staraptor, Rillaboom+Pawmot@Focus Sash, Pawmot+Rillaboom, Rillaboom+Sneasler@Focus Sash, Kingambit+Rillaboom, Baxcalibur+Rillaboom, Rillaboom+Sylveon@Fairy Feather, Rillaboom+Glimmora@Glimmoranite
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Rillaboom | 72.1% | terrain-setter 100.0%, fake-out 99.7%, priority-attack 99.4%, pivot 42.8%, speed-drop 2.8% |
| Sneasler | 56.0% | seed-unburden 56.8%, fake-out 47.4%, speed-drop 10.8%, ally-boost 10.2%, setup 6.2%, quick-guard 3.5%, priority-blocker 3.5% |
| Salamence | 49.1% | intimidate 98.8%, mega-attacker 96.8%, tailwind 77.7%, setup 3.7% |
| Incineroar | 45.2% | fake-out 100.0%, intimidate 100.0%, pivot 98.4%, trick-room-abuser 13.8%, spa-drop 10.3%, helping-hand 6.3%, disruption 2.7%, status 1.1% |
| Kingambit | 36.2% | priority-attack 98.5%, setup 25.6%, trick-room-abuser 5.2% |
| Basculegion | 25.6% | priority-attack 96.8%, pivot 35.4%, setup 0.9% |
| Floette-Eternal | 23.7% | mega-attacker 100.0%, setup 73.8% |
| Lucario | 9.2% | mega-attacker 100.0%, setup 56.0%, priority-attack 4.1% |
| Baxcalibur | 6.8% | priority-attack 95.4%, mega-attacker 79.4%, setup 37.2% |
| Volcarona | 6.8% | rage-powder 62.8%, spa-drop 50.2%, setup 37.2%, tailwind 35.1% |
| Froslass | 6.5% | weather-setter 100.0%, screens 94.0%, mega-attacker 92.3%, disruption 6.0% |
| Pawmot | 5.5% | fake-out 67.2%, ally-boost 13.9%, priority-attack 10.0%, pivot 5.1%, speed-drop 4.4% |
| Glimmora | 5.4% | mega-attacker 61.1% |
| Ninetales-Alola | 3.8% | weather-setter 100.0%, screens 58.5%, disruption 33.9%, speed-drop 22.6%, ally-boost 7.6%, helping-hand 7.2% |
| Whimsicott | 3.7% | tailwind 100.0%, prankster 100.0%, disruption 71.7%, weather-setter 14.1%, terrain-setter 12.2%, screens 10.2% |
| Aerodactyl | 3.6% | tailwind 100.0%, mega-attacker 41.1%, wide-guard 30.7%, disruption 10.2% |
| Delphox | 3.6% | mega-attacker 67.7%, setup 53.2%, terrain-setter 7.8%, priority-blocker 7.8%, disruption 7.1% |
| Primarina | 2.5% | setup 71.0%, helping-hand 9.8% |
- Representative teams (primary teams with the highest score for this community):
  - [CloverBells, , 13 Sep 2026](https://pokepast.es/fdc0b961c0d0ef8c)
  - [punihina1334, , 10 Sep 2026](https://pokepast.es/202c514602d9abe9)
  - [shynessalex, Champion, 20 Sep 2026](https://pokepast.es/6f1d5b2b15285f2b)

- Sub-community pass: 202 primary teams, 131 tokens, modularity 0.47; unconnected tokens: Indeedee-F@Psychic Seed, Sneasler@Psychic Seed, Whimsicott@Focus Sash, Absol@Absolite Z, Basculegion@Mystic Water, Blaziken (other item; Blazikenite 2/5), Garchomp (other item; Life Orb 3/4), Glimmora (other item; Focus Sash 4/4), Indeedee (other item; Focus Sash 3/4), Primarina (other item; Leftovers 3/4), Baxcalibur (other item; Life Orb 2/3), Charizard (other item; Charizardite X 3/3), Indeedee-F (other item; Colbur Berry 2/3), Pawmot (other item; Choice Scarf 1/3), Volcarona (other item; Focus Sash 1/3), Whimsicott (other item; Coba Berry 1/3); unassigned within the community: 7 teams (3.4% of its primary weight); hybrid teams of the community left out: 73
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 4 (0.59) | 5 (0.52) | 7 (0.47) | 9 (0.42) | 9 (0.37) |

#### Community 0 / Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb (66 primary teams, 59 distinct builds)
- Megas on member teams: Salamence 28, Floette 21, Garchomp-Z 13, Lucario-Z 11, Raichu-Y 11, Metagross 8
- Top species by team share: Rillaboom 94%, Incineroar 76%, Sneasler 61%, Salamence 42%, Gholdengo 41%, Floette-Eternal 32%
- Primary teams: 66 (32.9% of the community's primary weight), hybrid teams: 25 (12.5%)
- Date range: 2026-09-09 to 2026-09-22
- Core pairs: Tyranitar (other item; Tyranitarite 3/6)+Gholdengo@Life Orb, Garchomp@Garchompite Z+Metagross@Metagrossite, Gholdengo@Life Orb+Raichu@Raichunite Y, Sinistcha (other item; Colbur Berry 4/6)+Floette-Eternal@Floettite, Raichu@Raichunite Y+Sneasler@White Herb, Tyranitar (other item; Tyranitarite 3/6)+Salamence@Salamencite, Sinistcha (other item; Colbur Berry 4/6)+Incineroar@Sitrus Berry, Incineroar@Sitrus Berry+Kommo-o@Leftovers, Dragonite (other item; Life Orb 3/5)+Incineroar@Sitrus Berry, Garchomp@Garchompite Z+Milotic@Leftovers, Milotic (other item; Sitrus Berry 2/4)+Rillaboom@Miracle Seed, Garchomp@Garchompite Z+Lucario@Lucarionite Z, Raichu@Raichunite Y+Rillaboom@Miracle Seed, Incineroar@Sitrus Berry+Volcarona@Grassy Seed, Garchomp@Garchompite Z+Sneasler@Focus Sash, Blastoise@Blastoisinite+Rillaboom@Miracle Seed, Rillaboom (other item; Eject Button 4/10)+Incineroar@Sitrus Berry, Gholdengo@Life Orb+Incineroar@Sitrus Berry, Incineroar@Sitrus Berry+Lucario@Lucarionite Z, Gengar@Gengarite+Incineroar@Sitrus Berry, Golisopod@Golisopite+Rillaboom@Miracle Seed, Garchomp@Garchompite Z+Incineroar@Sitrus Berry, Gholdengo@Life Orb+Sneasler@Grassy Seed, Incineroar@Sitrus Berry+Raichu@Raichunite Y, Lucario@Lucarionite Z+Rillaboom@Miracle Seed, Incineroar@Sitrus Berry+Metagross@Metagrossite, Gholdengo@Life Orb+Rillaboom@Miracle Seed, Rillaboom@Miracle Seed+Volcarona@Grassy Seed, Garchomp@Garchompite Z+Sneasler@Grassy Seed, Floette-Eternal@Floettite+Incineroar@Sitrus Berry, Gholdengo (other item; Grassy Seed 3/9)+Incineroar@Sitrus Berry, Farigiraf@Sitrus Berry+Rillaboom@Miracle Seed, Tyranitar (other item; Tyranitarite 3/6)+Rillaboom@Miracle Seed, Metagross@Metagrossite+Rillaboom@Miracle Seed, Floette-Eternal@Floettite+Gholdengo@Life Orb, Gholdengo@Life Orb+Salamence@Salamencite, Incineroar@Sitrus Berry+Indeedee-F@Rocky Helmet, Garchomp@Garchompite Z+Rillaboom@Miracle Seed, Rillaboom@Miracle Seed+Sneasler@Grassy Seed, Ninetales-Alola (other item; Light Clay 4/10)+Incineroar@Sitrus Berry, Kingambit@Life Orb+Rillaboom@Miracle Seed, Incineroar@Sitrus Berry+Rillaboom@Miracle Seed, Basculegion@Choice Scarf+Rillaboom@Miracle Seed
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Rillaboom@Miracle Seed | 85.5% |
| Incineroar@Sitrus Berry | 70.9% |
| Gholdengo@Life Orb | 36.9% |
| Garchomp@Garchompite Z | 20.0% |
| Raichu@Raichunite Y | 15.0% |
| Metagross@Metagrossite | 8.9% |
| Pawmot@Focus Sash | 6.3% |
| Tyranitar (other item; Tyranitarite 3/6) | 5.8% |
| Farigiraf (other item; Colbur Berry 2/7) | 4.9% |
| Milotic (other item; Sitrus Berry 2/4) | 4.8% |
| Dragonite (other item; Life Orb 3/5) | 4.8% |
| Indeedee-F@Rocky Helmet | 4.8% |
| Volcarona@Grassy Seed | 4.6% |
| Blastoise@Blastoisinite | 4.2% |
| Kommo-o@Leftovers | 3.8% |
| Farigiraf@Sitrus Berry | 2.8% |
| Sinistcha (other item; Colbur Berry 4/6) | 2.3% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Rod, 5th, 13 Sep 2026](https://pokepast.es/a0582a49f5490809)
  - [ImageTT7, 134th, 21 Sep 2026](https://pokepast.es/8025f0aef5f2d1b6)
  - [Kshaunish Shaik, 32nd, 21 Sep 2026](https://pokepast.es/d41341435d22d77b)

#### Community 0 / Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb (44 primary teams, 44 distinct builds)
- Megas on member teams: Salamence 39, Floette 8, Froslass 8, Glimmora 8, Golisopod 2, Raichu-Y 2
- Top species by team share: Salamence 89%, Kingambit 82%, Rillaboom 64%, Sneasler 61%, Basculegion 48%, Arcanine-Hisui 27%
- Primary teams: 44 (23.2% of the community's primary weight), hybrid teams: 12 (5.2%)
- Date range: 2026-09-09 to 2026-09-22
- Core pairs: Glimmora@Glimmoranite+Volcarona@Rocky Helmet, Froslass@Froslassite+Kingambit@Black Glasses, Arcanine-Hisui@Focus Sash+Rillaboom@Sitrus Berry, Arcanine-Hisui@Focus Sash+Froslass@Froslassite, Froslass@Froslassite+Sneasler@White Herb, Arcanine-Hisui@Focus Sash+Kingambit@Life Orb, Kingambit@Chople Berry+Rillaboom@Sitrus Berry, Rillaboom@Sitrus Berry+Sneasler@Grassy Seed, Raichu@Raichunite Y+Sneasler@White Herb, Arcanine-Hisui@Focus Sash+Kingambit@Chople Berry, Tyranitar (other item; Tyranitarite 3/6)+Salamence@Salamencite, Basculegion@Focus Sash+Salamence@Salamencite, Arcanine-Hisui@Focus Sash+Sneasler@Grassy Seed, Glimmora@Glimmoranite+Salamence@Salamencite, Salamence@Salamencite+Volcarona@Rocky Helmet, Kingambit@Chople Berry+Salamence@Salamencite, Basculegion@Choice Scarf+Sneasler@White Herb, Arcanine-Hisui@Focus Sash+Sylveon@Fairy Feather, Arcanine-Hisui@Focus Sash+Sneasler@White Herb, Rillaboom@Sitrus Berry+Salamence@Salamencite, Baxcalibur@Baxcalibrite+Sneasler@White Herb, Floette-Eternal@Floettite+Rillaboom@Sitrus Berry, Kingambit@Chople Berry+Sneasler@White Herb, Gholdengo (other item; Grassy Seed 3/9)+Salamence@Salamencite, Kingambit@Focus Sash+Salamence@Salamencite, Gholdengo@Life Orb+Salamence@Salamencite, Arcanine-Hisui@Focus Sash+Salamence@Salamencite, Basculegion@Life Orb+Salamence@Salamencite, Basculegion@Choice Scarf+Salamence@Salamencite, Salamence@Salamencite+Sneasler@White Herb, Basculegion@Life Orb+Sneasler@White Herb, Salamence@Salamencite+Sylveon@Fairy Feather, Basculegion@Choice Scarf+Rillaboom@Miracle Seed
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Salamence@Salamencite | 83.9% |
| Kingambit@Chople Berry | 48.1% |
| Sneasler@White Herb | 36.0% |
| Arcanine-Hisui@Focus Sash | 28.6% |
| Rillaboom@Sitrus Berry | 20.5% |
| Froslass@Froslassite | 20.1% |
| Glimmora@Glimmoranite | 16.9% |
| Basculegion@Choice Scarf | 11.1% |
| Volcarona@Rocky Helmet | 8.5% |
| Basculegion@Focus Sash | 1.4% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Alex Underhill, , 16 Sep 2026](https://pokepast.es/f6c343c1f47903cc)
  - [Shohei Kimura, , 16 Sep 2026](https://pokepast.es/fd6fe5bb94a024fb)
  - [Ryan Loseto, Champion, 10 Sep 2026](https://pokepast.es/2c92e63c0fd4a43c)

#### Community 0 / Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash (46 primary teams, 39 distinct builds)
- Megas on member teams: Floette 37, Salamence 21, Garchomp-Z 6, Delphox 4, Baxcalibur 3, Charizard-X 3
- Top species by team share: Rillaboom 87%, Floette-Eternal 80%, Sneasler 78%, Incineroar 70%, Kingambit 57%, Salamence 46%
- Primary teams: 46 (21.6% of the community's primary weight), hybrid teams: 22 (9.5%)
- Date range: 2026-09-09 to 2026-09-21
- Core pairs: Incineroar@Chople Berry+Rillaboom@Occa Berry, Kingambit@Black Glasses+Sneasler@Focus Sash, Kingambit@Life Orb+Sneasler@Focus Sash, Milotic@Leftovers+Sneasler@Focus Sash, Delphox@Delphoxite+Floette-Eternal@Floettite, Arcanine-Hisui@Focus Sash+Kingambit@Life Orb, Rillaboom@Life Orb+Sneasler@Focus Sash, Rillaboom@Occa Berry+Sneasler@Grassy Seed, Baxcalibur@Baxcalibrite+Sneasler@Focus Sash, Rillaboom@Sitrus Berry+Sneasler@Grassy Seed, Sinistcha (other item; Colbur Berry 4/6)+Floette-Eternal@Floettite, Floette-Eternal@Floettite+Rillaboom@Occa Berry, Floette-Eternal@Floettite+Sneasler@Focus Sash, Gholdengo (other item; Grassy Seed 3/9)+Floette-Eternal@Floettite, Floette-Eternal@Floettite+Garchomp@Choice Scarf, Floette-Eternal@Floettite+Incineroar@Chople Berry, Incineroar (other item; Leftovers 4/14)+Floette-Eternal@Floettite, Incineroar@Chople Berry+Sneasler@Grassy Seed, Arcanine-Hisui@Focus Sash+Sneasler@Grassy Seed, Garchomp@Garchompite Z+Sneasler@Focus Sash, Charizard@Charizardite Y+Floette-Eternal@Floettite, Kingambit@Focus Sash+Sneasler@Grassy Seed, Gholdengo@Life Orb+Sneasler@Grassy Seed, Floette-Eternal@Floettite+Rillaboom@Sitrus Berry, Gholdengo (other item; Grassy Seed 3/9)+Salamence@Salamencite, Floette-Eternal@Floettite+Kingambit@Life Orb, Floette-Eternal@Floettite+Sneasler@Grassy Seed, Garchomp@Garchompite Z+Sneasler@Grassy Seed, Floette-Eternal@Floettite+Incineroar@Sitrus Berry, Gholdengo (other item; Grassy Seed 3/9)+Incineroar@Sitrus Berry, Floette-Eternal@Floettite+Gholdengo@Life Orb, Floette-Eternal@Floettite+Kingambit@Focus Sash, Basculegion@Life Orb+Floette-Eternal@Floettite, Rillaboom@Miracle Seed+Sneasler@Grassy Seed, Kingambit@Life Orb+Rillaboom@Miracle Seed
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Floette-Eternal@Floettite | 79.7% |
| Sneasler@Grassy Seed | 48.8% |
| Sneasler@Focus Sash | 26.6% |
| Kingambit@Life Orb | 20.6% |
| Rillaboom@Occa Berry | 19.4% |
| Incineroar (other item; Leftovers 4/14) | 14.5% |
| Incineroar@Chople Berry | 12.8% |
| Gholdengo (other item; Grassy Seed 3/9) | 9.7% |
| Delphox@Delphoxite | 9.0% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/3ad6655b53208446)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/7fb12fe08230d7be)
  - [vi0ra_pokemon, , 14 Sep 2026](https://pokepast.es/74aea655c757917f)

#### Community 0 / Sub-community 3: Sylveon@Fairy Feather / Lucario@Lucarionite Z / Basculegion@Life Orb (23 primary teams, 20 distinct builds)
- Megas on member teams: Lucario-Z 10, Salamence 8, Baxcalibur 7, Absol-Z 2, Aerodactyl 2, Meganium 2
- Top species by team share: Rillaboom 74%, Basculegion 65%, Sylveon 61%, Incineroar 43%, Lucario 43%, Sneasler 43%
- Primary teams: 23 (10.5% of the community's primary weight), hybrid teams: 11 (4.8%)
- Date range: 2026-09-09 to 2026-09-21
- Core pairs: Ninetales-Alola (other item; Light Clay 4/10)+Baxcalibur@Baxcalibrite, Aerodactyl (other item; Aerodactylite 4/9)+Lucario@Lucarionite Z, Aerodactyl (other item; Aerodactylite 4/9)+Sylveon@Fairy Feather, Lucario@Lucarionite Z+Sylveon@Fairy Feather, Charizard@Charizardite Y+Sylveon@Fairy Feather, Basculegion@Life Orb+Lucario@Lucarionite Z, Baxcalibur@Baxcalibrite+Sneasler@Focus Sash, Basculegion@Life Orb+Baxcalibur@Baxcalibrite, Basculegion@Life Orb+Sylveon@Fairy Feather, Basculegion@Life Orb+Kingambit@Focus Sash, Garchomp@Garchompite Z+Lucario@Lucarionite Z, Arcanine-Hisui@Focus Sash+Sylveon@Fairy Feather, Kingambit@Focus Sash+Sylveon@Fairy Feather, Incineroar@Sitrus Berry+Lucario@Lucarionite Z, Baxcalibur@Baxcalibrite+Sneasler@White Herb, Lucario@Lucarionite Z+Rillaboom@Miracle Seed, Basculegion@Life Orb+Salamence@Salamencite, Basculegion@Life Orb+Floette-Eternal@Floettite, Ninetales-Alola (other item; Light Clay 4/10)+Incineroar@Sitrus Berry, Basculegion@Life Orb+Sneasler@White Herb, Salamence@Salamencite+Sylveon@Fairy Feather
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Sylveon@Fairy Feather | 45.8% |
| Lucario@Lucarionite Z | 45.7% |
| Basculegion@Life Orb | 45.2% |
| Aerodactyl (other item; Aerodactylite 4/9) | 29.6% |
| Baxcalibur@Baxcalibrite | 24.3% |
| Ninetales-Alola (other item; Light Clay 4/10) | 16.1% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Shoma Honami, , 16 Sep 2026](https://pokepast.es/a0aec76f5727c83a)
  - [Stefano Greppi, , 10 Sep 2026](https://pokepast.es/5d77ccc3504a01b1)
  - [PiyoLily145, , 12 Sep 2026](https://pokepast.es/5a82e9d03b9d6cc2)

#### Community 0 / Sub-community 4: Rillaboom@Life Orb / Milotic@Leftovers / Kingambit@Focus Sash (9 primary teams, 8 distinct builds)
- Megas on member teams: Froslass 3, Golisopod 3, Salamence 3, Garchomp-Z 2, Baxcalibur 1, Blaziken 1
- Top species by team share: Rillaboom 100%, Kingambit 67%, Milotic 67%, Farigiraf 44%, Sneasler 44%, Froslass 33%
- Primary teams: 9 (4.4% of the community's primary weight), hybrid teams: 11 (5.0%)
- Date range: 2026-09-09 to 2026-09-18
- Core pairs: Kingambit@Black Glasses+Rillaboom@Life Orb, Froslass@Froslassite+Kingambit@Black Glasses, Golisopod@Golisopite+Milotic@Leftovers, Kingambit@Black Glasses+Sneasler@Focus Sash, Milotic@Leftovers+Rillaboom@Life Orb, Kingambit@Focus Sash+Rillaboom@Life Orb, Milotic@Leftovers+Sneasler@Focus Sash, Rillaboom@Life Orb+Sneasler@Focus Sash, Garchomp@Garchompite Z+Milotic@Leftovers, Basculegion@Life Orb+Kingambit@Focus Sash, Kingambit@Focus Sash+Sylveon@Fairy Feather, Kingambit@Focus Sash+Sneasler@Grassy Seed, Golisopod@Golisopite+Rillaboom@Miracle Seed, Kingambit@Focus Sash+Salamence@Salamencite, Floette-Eternal@Floettite+Kingambit@Focus Sash
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Rillaboom@Life Orb | 46.4% |
| Milotic@Leftovers | 45.7% |
| Kingambit@Focus Sash | 38.2% |
| Kingambit@Black Glasses | 30.9% |
| Golisopod@Golisopite | 17.1% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [4milkfang, , 16 Sep 2026](https://pokepast.es/11b159014240dc52)
  - [paperrr748, , 11 Sep 2026](https://pokepast.es/9d40e2fb846322b9)
  - [yuki_mpm, , 13 Sep 2026](https://pokepast.es/e3bbfefb252a130d)

#### Community 0 / Sub-community 6: Gengar@Gengarite / Rillaboom (other item; Eject Button 4/10) (5 primary teams, 4 distinct builds)
- Megas on member teams: Gengar 5, Salamence 1
- Top species by team share: Gengar 100%, Rillaboom 100%, Incineroar 80%, Dragonite 40%, Scrafty 40%, Snorlax 40%
- Primary teams: 5 (2.8% of the community's primary weight), hybrid teams: 1 (0.4%)
- Date range: 2026-09-10 to 2026-09-20
- Core pairs: Rillaboom (other item; Eject Button 4/10)+Gengar@Gengarite, Rillaboom (other item; Eject Button 4/10)+Incineroar@Sitrus Berry, Gengar@Gengarite+Incineroar@Sitrus Berry
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Gengar@Gengarite | 100.0% |
| Rillaboom (other item; Eject Button 4/10) | 87.7% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Minche Chung, 12th, 20 Sep 2026](https://pokepast.es/b987727e0339084a)
  - [m_rada13, Champion, 11 Sep 2026](https://pokepast.es/3ac336df2eb4729b)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/60458cc1a20c2440)

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds): Sub-community 5: Charizard@Charizardite Y / Garchomp@Choice Scarf (2 distinct builds, 2 primary teams)

#### Community 0 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Rillaboom | Rillaboom@Miracle Seed | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 110 | 0: 59 · 2: 20 · 1: 15 · 3: 12 · 4: 3 · 5: 1 · unassigned: 0 |
| Rillaboom | Rillaboom@Life Orb | Sub-community 4: Rillaboom@Life Orb / Milotic@Leftovers / Kingambit@Focus Sash | 15 | 4: 6 · 1: 4 · 2: 2 · 0: 1 · 3: 1 · unassigned: 1 |
| Rillaboom | Rillaboom@Occa Berry | Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 15 | 2: 13 · 0: 1 · 3: 1 · unassigned: 0 |
| Rillaboom | Rillaboom@Sitrus Berry | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 13 | 1: 8 · 2: 5 · unassigned: 0 |
| Rillaboom | Rillaboom (other item; Eject Button 4/10) | Sub-community 6: Gengar@Gengarite / Rillaboom (other item; Eject Button 4/10) | 10 | 6: 5 · 3: 3 · 0: 1 · 1: 1 · unassigned: 0 |
| Incineroar | Incineroar@Sitrus Berry | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 85 | 0: 48 · 2: 17 · 3: 9 · 1: 5 · 6: 3 · 4: 1 · unassigned: 2 |
| Incineroar | Incineroar (other item; Leftovers 4/14) | Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 14 | 2: 8 · 0: 1 · 1: 1 · 4: 1 · 5: 1 · 6: 1 · unassigned: 1 |
| Incineroar | Incineroar@Chople Berry | Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 11 | 2: 7 · 0: 1 · 3: 1 · 4: 1 · 5: 1 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 59 | 0: 23 · 2: 23 · 1: 9 · 3: 3 · 4: 1 · unassigned: 0 |
| Sneasler | Sneasler@White Herb | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 33 | 1: 15 · 0: 13 · 3: 4 · unassigned: 1 |
| Sneasler | Sneasler@Focus Sash | Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 22 | 2: 13 · 0: 3 · 4: 3 · 1: 2 · 3: 1 · unassigned: 0 |
| Sneasler | Sneasler@Psychic Seed | none | 6 | 3: 2 · 0: 1 · 1: 1 · unassigned: 2 |
| Kingambit | Kingambit@Chople Berry | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 38 | 1: 26 · 0: 6 · 2: 4 · 6: 1 · unassigned: 1 |
| Kingambit | Kingambit@Focus Sash | Sub-community 4: Rillaboom@Life Orb / Milotic@Leftovers / Kingambit@Focus Sash | 23 | 2: 10 · 1: 4 · 3: 3 · 4: 3 · 0: 1 · unassigned: 2 |
| Kingambit | Kingambit@Life Orb | Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 18 | 2: 10 · 0: 4 · 1: 4 · unassigned: 0 |
| Kingambit | Kingambit@Black Glasses | Sub-community 4: Rillaboom@Life Orb / Milotic@Leftovers / Kingambit@Focus Sash | 8 | 4: 3 · 2: 2 · 0: 1 · 1: 1 · 3: 1 · unassigned: 0 |
| Kingambit | Kingambit (other item; Occa Berry 2/2) | none | 2 | 0: 1 · 1: 1 · unassigned: 0 |
| Basculegion | Basculegion@Life Orb | Sub-community 3: Sylveon@Fairy Feather / Lucario@Lucarionite Z / Basculegion@Life Orb | 35 | 3: 12 · 1: 11 · 2: 7 · 0: 3 · 4: 1 · unassigned: 1 |
| Basculegion | Basculegion@Choice Scarf | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 15 | 0: 6 · 1: 5 · 2: 2 · 3: 1 · unassigned: 1 |
| Basculegion | Basculegion@Focus Sash | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 5 | 3: 2 · 0: 1 · 1: 1 · 2: 1 · unassigned: 0 |
| Basculegion | Basculegion@Mystic Water | none | 5 | 0: 2 · 1: 2 · 2: 1 · unassigned: 0 |
| Basculegion | Basculegion (other item; Sitrus Berry 2/2) | none | 2 | 1: 2 · unassigned: 0 |
| Gholdengo | Gholdengo@Life Orb | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 35 | 0: 26 · 2: 4 · 1: 3 · 3: 1 · 5: 1 · unassigned: 0 |
| Gholdengo | Gholdengo (other item; Grassy Seed 3/9) | Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 9 | 2: 6 · 1: 2 · 0: 1 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 23 | 0: 13 · 2: 6 · 4: 2 · 3: 1 · unassigned: 1 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 5: Charizard@Charizardite Y / Garchomp@Choice Scarf | 7 | 1: 2 · 2: 2 · 5: 2 · 0: 1 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 3/4) | none | 4 | 1: 1 · 2: 1 · 6: 1 · unassigned: 1 |
| Milotic | Milotic@Leftovers | Sub-community 4: Rillaboom@Life Orb / Milotic@Leftovers / Kingambit@Focus Sash | 18 | 0: 6 · 4: 6 · 2: 3 · 1: 2 · 3: 1 · unassigned: 0 |
| Milotic | Milotic (other item; Sitrus Berry 2/4) | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 4 | 0: 4 · unassigned: 0 |
| Volcarona | Volcarona@Rocky Helmet | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 7 | 1: 4 · 2: 2 · unassigned: 1 |
| Volcarona | Volcarona@Grassy Seed | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 6 | 0: 3 · 3: 2 · 1: 1 · unassigned: 0 |
| Volcarona | Volcarona (other item; Focus Sash 1/3) | none | 3 | 1: 2 · 2: 1 · unassigned: 0 |

### Community 1: Indeedee-F / Armarouge / Gardevoir
- Primary teams: 66 (primary share 16.0%), hybrid teams: 10 (hybrid share 2.3%)
- Date range: 2026-09-09 to 2026-09-22
- Core pairs: Hatterene+Torkoal@Charcoal, Hatterene+Torkoal, Torkoal+Hatterene@Life Orb, Gardevoir+Talonflame, Talonflame+Gardevoir@Gardevoirite, Gardevoir+Indeedee-F@Colbur Berry, Gardevoir+Sneasler@Psychic Seed, Torkoal+Indeedee-F@Psychic Seed, Armarouge+Indeedee-F@Psychic Seed, Hatterene+Indeedee-F@Rocky Helmet, Armarouge+Hatterene, Armarouge+Hatterene@Life Orb, Gardevoir+Indeedee-F@Rocky Helmet, Talonflame+Indeedee-F@Rocky Helmet, Indeedee-F+Armarouge@Life Orb, Gardevoir+Indeedee-F, Indeedee-F+Gardevoir@Gardevoirite, Indeedee-F+Meowstic, Indeedee-F+Meowstic@Meowsticite, Hatterene+Indeedee-F, Indeedee-F+Hatterene@Life Orb, Gallade+Indeedee-F, Armarouge+Indeedee-F@Colbur Berry, Gardevoir+Torkoal@Charcoal, Gardevoir+Armarouge@Life Orb, Armarouge+Indeedee-F, Torkoal+Armarouge@Life Orb, Indeedee-F+Armarouge@Focus Sash, Gardevoir+Torkoal, Torkoal+Gardevoir@Gardevoirite, Gardevoir+Garchomp@Life Orb, Torkoal+Farigiraf@Sitrus Berry, Torkoal+Indeedee-F@Colbur Berry, Indeedee-F+Blastoise@Blastoisinite, Absol+Milotic@Leftovers, Armarouge+Torkoal@Charcoal, Indeedee-F+Torkoal@Charcoal, Absol+Indeedee-F@Rocky Helmet, Basculegion+Talonflame, Armarouge+Torkoal, Armarouge+Indeedee-F@Rocky Helmet, Indeedee-F+Torkoal, Farigiraf+Torkoal, Blastoise+Indeedee-F, Indeedee-F+Mawile, Indeedee-F+Mawile@Mawilite, Metagross+Indeedee-F@Rocky Helmet, Absol+Armarouge, Armarouge+Absol@Absolite Z, Golisopod+Hatterene, Hatterene+Golisopod@Golisopite, Golisopod+Hatterene@Life Orb, Indeedee-F+Sneasler@Psychic Seed, Farigiraf+Torkoal@Charcoal, Armarouge+Sneasler@Psychic Seed, Gardevoir+Basculegion@Choice Scarf, Charizard+Indeedee-F@Psychic Seed, Golisopod+Indeedee-F@Psychic Seed, Indeedee-F+Venusaur@Focus Sash, Absol+Indeedee-F, Indeedee-F+Absol@Absolite Z, Pelipper+Indeedee-F@Colbur Berry, Blaziken+Indeedee-F, Armarouge+Golisopod, Armarouge+Golisopod@Golisopite, Indeedee-F+Basculegion@Focus Sash, Absol+Milotic, Milotic+Absol@Absolite Z, Indeedee-F+Venusaur, Golisopod+Armarouge@Life Orb, Indeedee-F+Metagross, Indeedee-F+Metagross@Metagrossite, Blaziken+Kingambit, Basculegion+Gardevoir, Basculegion+Gardevoir@Gardevoirite, Armarouge+Gardevoir, Armarouge+Gardevoir@Gardevoirite, Indeedee-F+Talonflame, Dragonite+Incineroar@Sitrus Berry, Delphox+Indeedee-F, Absol+Gholdengo@Life Orb, Sneasler+Indeedee-F@Sitrus Berry, Torkoal+Charizard@Charizardite Y, Golisopod+Indeedee-F@Colbur Berry, Charizard+Torkoal@Charcoal, Torkoal+Indeedee-F@Rocky Helmet, Indeedee-F+Pelipper@Focus Sash, Indeedee-F+Garchomp@Life Orb, Dragonite+Indeedee-F, Charizard+Torkoal, Blastoise+Rillaboom@Miracle Seed, Absol+Gholdengo, Gholdengo+Absol@Absolite Z, Gardevoir+Sneasler, Sneasler+Gardevoir@Gardevoirite, Indeedee-F+Swampert, Indeedee-F+Swampert@Swampertite, Blastoise+Kingambit, Sneasler+Indeedee-F@Colbur Berry, Indeedee-F+Basculegion@Choice Scarf, Basculegion+Indeedee-F@Rocky Helmet, Torkoal+Sneasler@Psychic Seed, Gardevoir+Charizard@Charizardite Y, Indeedee-F+Whimsicott@Focus Sash, Golisopod+Indeedee-F, Indeedee-F+Golisopod@Golisopite, Indeedee-F+Pelipper, Sneasler+Armarouge@Focus Sash, Dragonite+Incineroar, Pelipper+Indeedee-F@Rocky Helmet, Sneasler+Armarouge@Life Orb, Gardevoir+Pelipper, Pelipper+Gardevoir@Gardevoirite, Charizard+Gardevoir, Charizard+Gardevoir@Gardevoirite, Kingambit+Armarouge@Life Orb, Armarouge+Excadrill
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Indeedee-F | 96.1% | follow-me 100.0%, terrain-setter 98.6%, priority-blocker 98.6%, helping-hand 92.2%, trick-room-setter 83.0%, disruption 7.1%, status 3.9%, spa-drop 3.5%, fake-out 2.4% |
| Armarouge | 43.7% | trick-room-setter 45.4%, wide-guard 34.0%, trick-room-abuser 29.2%, ally-switch 5.1%, setup 4.4% |
| Gardevoir | 42.3% | mega-attacker 100.0%, trick-room-setter 46.4%, setup 24.5%, disruption 5.3% |
| Torkoal | 31.0% | weather-setter 100.0%, trick-room-abuser 100.0%, helping-hand 35.2% |
| Hatterene | 14.6% | trick-room-setter 100.0%, trick-room-abuser 100.0% |
| Absol | 8.2% | mega-attacker 100.0%, status 34.6%, priority-attack 7.3%, speed-drop 6.0% |
| Blastoise | 6.0% | mega-attacker 86.8%, setup 56.8%, fake-out 32.3%, status 13.2%, pivot 13.2% |
| Blaziken | 5.9% | mega-attacker 54.9%, ally-boost 23.7% |
| Mawile | 5.9% | trick-room-abuser 100.0%, mega-attacker 100.0%, priority-attack 81.0%, intimidate 14.1% |
| Dragonite | 5.5% | mega-attacker 59.7%, tailwind 50.4%, priority-attack 47.2%, setup 13.9% |
| Gallade | 4.6% | wide-guard 100.0%, trick-room-setter 70.8% |
| Talonflame | 4.5% | tailwind 100.0%, gale-wings 100.0%, quick-guard 13.5%, priority-blocker 13.5% |
| Meowstic | 3.8% | mega-attacker 100.0%, fake-out 53.9%, setup 22.5% |
- Representative teams (primary teams with the highest score for this community):
  - [Joe Brown, , 15 Sep 2026](https://pokepast.es/2ad06f492cf12877)
  - [LosChinganas, , 10 Sep 2026](https://pokepast.es/38a84a819ec26ef8)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/b409f9d80e8f830b)

### Community 2: Gholdengo / Raichu / Arcanine-Hisui
- Primary teams: 29 (primary share 7.8%), hybrid teams: 35 (hybrid share 10.2%)
- Date range: 2026-09-10 to 2026-09-22
- Core pairs: Aerodactyl+Sylveon@Fairy Feather, Aerodactyl+Sylveon, Raichu+Staraptor@Staraptite, Raichu+Staraptor, Staraptor+Raichu@Raichunite Y, Sylveon+Staraptor@Staraptite, Staraptor+Sylveon@Fairy Feather, Staraptor+Sylveon, Raichu+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Raichu, Arcanine-Hisui+Raichu@Raichunite Y, Raichu+Gholdengo@Life Orb, Staraptor+Arcanine-Hisui@Focus Sash, Gholdengo+Raichu, Gholdengo+Raichu@Raichunite Y, Staraptor+Gholdengo@Life Orb, Arcanine-Hisui+Staraptor@Staraptite, Arcanine-Hisui+Staraptor, Arcanine-Hisui+Rillaboom@Sitrus Berry, Lucario+Sylveon@Fairy Feather, Gholdengo+Staraptor@Staraptite, Lucario+Sylveon, Sylveon+Lucario@Lucarionite Z, Raichu+Sylveon@Fairy Feather, Gholdengo+Staraptor, Raichu+Sylveon, Sylveon+Raichu@Raichunite Y, Sylveon+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Sylveon@Fairy Feather, Froslass+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Sylveon, Arcanine-Hisui+Froslass, Arcanine-Hisui+Froslass@Froslassite, Gholdengo+Milotic@Sitrus Berry, Arcanine-Hisui+Gholdengo@Life Orb, Raichu+Rillaboom@Miracle Seed, Sylveon+Farigiraf@Sitrus Berry, Gholdengo+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Gholdengo, Arcanine-Hisui+Milotic@Leftovers, Absol+Gholdengo@Life Orb, Sylveon+Kingambit@Focus Sash, Milotic+Gholdengo@Life Orb, Rillaboom+Gholdengo@Grassy Seed, Indeedee+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Kingambit@Life Orb, Sylveon+Basculegion@Life Orb, Gholdengo+Rillaboom@Miracle Seed, Gholdengo+Tyranitar, Raichu+Rillaboom, Rillaboom+Raichu@Raichunite Y, Arcanine-Hisui+Indeedee, Gholdengo+Tyranitar@Tyranitarite, Arcanine-Hisui+Milotic, Gholdengo+Excadrill@Focus Sash, Tyranitar+Gholdengo@Life Orb, Staraptor+Rillaboom@Miracle Seed, Raichu+Milotic@Leftovers, Milotic+Arcanine-Hisui@Focus Sash, Gholdengo+Milotic, Absol+Gholdengo, Gholdengo+Absol@Absolite Z, Arcanine-Hisui+Kingambit@Chople Berry, Arcanine-Hisui+Rillaboom@Miracle Seed, Salamence+Gholdengo@Life Orb, Sylveon+Charizard@Charizardite Y, Gholdengo+Incineroar@Sitrus Berry, Salamence+Arcanine-Hisui@Focus Sash, Gholdengo+Salamence, Gholdengo+Salamence@Salamencite, Arcanine-Hisui+Salamence, Arcanine-Hisui+Salamence@Salamencite, Rillaboom+Gholdengo@Life Orb, Excadrill+Gholdengo, Arcanine-Hisui+Sneasler@Grassy Seed, Charizard+Sylveon@Fairy Feather, Rillaboom+Arcanine-Hisui@Focus Sash, Baxcalibur+Raichu, Baxcalibur+Raichu@Raichunite Y, Floette-Eternal+Gholdengo, Gholdengo+Floette-Eternal@Floettite, Arcanine-Hisui+Rillaboom, Gholdengo+Sneasler@Grassy Seed, Sylveon+Rillaboom@Miracle Seed, Charizard+Sylveon, Gholdengo+Rillaboom, Gholdengo+Milotic@Leftovers, Sylveon+Gholdengo@Life Orb, Farigiraf+Sylveon, Gholdengo+Rillaboom@Occa Berry, Salamence+Sylveon, Sylveon+Salamence@Salamencite, Basculegion+Sylveon, Excadrill+Gholdengo@Life Orb, Salamence+Sylveon@Fairy Feather, Floette-Eternal+Gholdengo@Life Orb, Raichu+Sneasler@White Herb, Gholdengo+Incineroar, Arcanine-Hisui+Farigiraf@Sitrus Berry, Rillaboom+Staraptor@Staraptite, Rillaboom+Staraptor, Rillaboom+Sylveon@Fairy Feather
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Gholdengo | 76.4% | setup 89.7% |
| Raichu | 59.9% | speed-drop 100.0%, mega-attacker 100.0%, fake-out 73.2%, disruption 29.9% |
| Arcanine-Hisui | 52.1% | priority-attack 92.1%, intimidate 11.9%, spa-drop 1.7% |
| Sylveon | 31.3% | priority-attack 91.6%, status 10.8%, spa-drop 4.5%, setup 4.0% |
| Staraptor | 16.7% | intimidate 100.0%, mega-attacker 95.6%, tailwind 73.5%, pivot 4.4% |
- Representative teams (primary teams with the highest score for this community):
  - [Conner Pietrusinski, Top 8, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/q65vnQVtYoaIlJgWdymZ)
  - [ayoitsbenji, Champion, 15 Sep 2026](https://pokepast.es/d943321333ed8846)
  - [parrobot7, Champion, 14 Sep 2026](https://pokepast.es/2d234b4ec11a9aca)

### Community 3: Golisopod / Archaludon / Pelipper
- Primary teams: 50 (primary share 13.2%), hybrid teams: 13 (hybrid share 3.4%)
- Date range: 2026-09-09 to 2026-09-22
- Core pairs: Gengar+Rillaboom@Eject Button, Gengar+Politoed@Sitrus Berry, Politoed+Incineroar@Passho Berry, Swampert+Pelipper@Focus Sash, Gengar+Politoed, Politoed+Gengar@Gengarite, Gengar+Incineroar@Passho Berry, Gengar+Kommo-o, Kommo-o+Gengar@Gengarite, Swampert+Archaludon@Leftovers, Pelipper+Swampert, Pelipper+Swampert@Swampertite, Archaludon+Politoed@Sitrus Berry, Archaludon+Swampert, Archaludon+Swampert@Swampertite, Grimmsnarl+Archaludon@Leftovers, Politoed+Archaludon@Leftovers, Archaludon+Pelipper@Focus Sash, Archaludon+Grimmsnarl, Archaludon+Grimmsnarl@Light Clay, Archaludon+Pelipper@Sitrus Berry, Archaludon+Politoed, Archaludon+Pelipper, Pelipper+Archaludon@Leftovers, Swampert+Pelipper@Sitrus Berry, Grimmsnarl+Charizard@Charizardite Y, Grimmsnarl+Pelipper, Pelipper+Grimmsnarl@Light Clay, Pelipper+Farigiraf@Colbur Berry, Charizard+Grimmsnarl, Charizard+Grimmsnarl@Light Clay, Archaludon+Incineroar@Passho Berry, Archaludon+Rillaboom@Eject Button, Golisopod+Pelipper@Sitrus Berry, Golisopod+Pelipper, Pelipper+Golisopod@Golisopite, Golisopod+Pelipper@Focus Sash, Golisopod+Grimmsnarl, Golisopod+Grimmsnarl@Light Clay, Grimmsnarl+Golisopod@Golisopite, Golisopod+Swampert, Golisopod+Swampert@Swampertite, Swampert+Golisopod@Golisopite, Gengar+Archaludon@Leftovers, Tyranitar+Sinistcha@Colbur Berry, Archaludon+Gengar, Archaludon+Gengar@Gengarite, Golisopod+Farigiraf@Colbur Berry, Golisopod+Archaludon@Leftovers, Torkoal+Farigiraf@Sitrus Berry, Sinistcha+Excadrill@Focus Sash, Archaludon+Golisopod, Archaludon+Golisopod@Golisopite, Sinistcha+Tyranitar@Tyranitarite, Excadrill+Sinistcha, Golisopod+Sinistcha@Sitrus Berry, Golisopod+Farigiraf@Sitrus Berry, Farigiraf+Torkoal, Farigiraf+Golisopod, Farigiraf+Golisopod@Golisopite, Sinistcha+Tyranitar, Golisopod+Hatterene, Hatterene+Golisopod@Golisopite, Golisopod+Hatterene@Life Orb, Charizard+Farigiraf@Sitrus Berry, Farigiraf+Torkoal@Charcoal, Pelipper+Basculegion@Choice Scarf, Farigiraf+Incineroar@Chople Berry, Golisopod+Sinistcha, Sinistcha+Golisopod@Golisopite, Incineroar+Politoed@Sitrus Berry, Sinistcha+Archaludon@Leftovers, Golisopod+Indeedee-F@Psychic Seed, Kommo-o+Incineroar@Sitrus Berry, Archaludon+Basculegion@Choice Scarf, Incineroar+Kommo-o@Leftovers, Farigiraf+Charizard@Charizardite Y, Milotic+Sinistcha@Colbur Berry, Gengar+Incineroar, Incineroar+Gengar@Gengarite, Archaludon+Sinistcha, Farigiraf+Pelipper@Sitrus Berry, Pelipper+Indeedee-F@Colbur Berry, Charizard+Farigiraf, Armarouge+Golisopod, Armarouge+Golisopod@Golisopite, Golisopod+Politoed, Politoed+Golisopod@Golisopite, Golisopod+Incineroar@Chople Berry, Sylveon+Farigiraf@Sitrus Berry, Incineroar+Kommo-o, Pelipper+Sinistcha, Golisopod+Armarouge@Life Orb, Golisopod+Basculegion@Choice Scarf, Incineroar+Politoed, Archaludon+Charizard@Charizardite Y, Incineroar+Farigiraf@Colbur Berry, Charizard+Archaludon@Leftovers, Rillaboom+Farigiraf@Grassy Seed, Incineroar+Sinistcha@Colbur Berry, Farigiraf+Pelipper, Kingambit+Kommo-o, Golisopod+Indeedee-F@Colbur Berry, Archaludon+Charizard, Basculegion+Pelipper@Sitrus Berry, Golisopod+Milotic@Leftovers, Gengar+Rillaboom, Rillaboom+Gengar@Gengarite, Indeedee-F+Pelipper@Focus Sash, Rillaboom+Politoed@Sitrus Berry, Kingambit+Sinistcha@Colbur Berry, Rillaboom+Kommo-o@Leftovers, Sneasler+Sinistcha@Colbur Berry, Indeedee-F+Swampert, Indeedee-F+Swampert@Swampertite, Farigiraf+Garchomp@Garchompite Z, Sinistcha+Incineroar@Sitrus Berry, Farigiraf+Milotic@Leftovers, Farigiraf+Garchomp, Politoed+Rillaboom, Gengar+Incineroar@Sitrus Berry, Golisopod+Indeedee-F, Indeedee-F+Golisopod@Golisopite, Indeedee-F+Pelipper, Floette-Eternal+Sinistcha, Sinistcha+Floette-Eternal@Floettite, Pelipper+Sneasler@White Herb, Pelipper+Indeedee-F@Rocky Helmet, Kommo-o+Rillaboom, Farigiraf+Sylveon, Kommo-o+Rillaboom@Miracle Seed, Gardevoir+Pelipper, Pelipper+Gardevoir@Gardevoirite, Golisopod+Rillaboom@Life Orb, Milotic+Sinistcha, Arcanine-Hisui+Farigiraf@Sitrus Berry
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Golisopod | 69.2% | mega-attacker 100.0%, priority-attack 44.1%, setup 41.9%, trick-room-abuser 30.4%, pivot 9.3%, wide-guard 1.2% |
| Archaludon | 55.6% | spa-drop 18.0% |
| Pelipper | 55.1% | weather-setter 100.0%, tailwind 81.8%, wide-guard 65.5%, helping-hand 5.1%, trick-room-abuser 2.5%, pivot 1.8% |
| Farigiraf | 28.6% | trick-room-setter 100.0%, priority-blocker 100.0%, helping-hand 49.1%, trick-room-abuser 31.8%, disruption 8.7%, weather-setter 2.7%, screens 2.1%, terrain-setter 2.0%, setup 1.9% |
| Gengar | 25.6% | mega-attacker 84.7%, perish-song 80.2%, disruption 9.4%, status 4.2% |
| Swampert | 20.4% | mega-attacker 100.0% |
| Politoed | 20.0% | weather-setter 100.0%, perish-song 77.0%, disruption 58.6%, status 18.4%, trick-room-abuser 5.5%, helping-hand 5.2% |
| Grimmsnarl | 16.1% | prankster 100.0%, screens 100.0%, spa-drop 93.5%, pivot 92.5%, trick-room-abuser 31.8%, fake-out 7.5% |
| Sinistcha | 10.2% | rage-powder 100.0%, trick-room-setter 84.7%, trick-room-abuser 26.3%, disruption 4.1% |
| Kommo-o | 7.2% | setup 82.3% |
- Representative teams (primary teams with the highest score for this community):
  - [kara, 7th, 10 Sep 2026](https://pokepast.es/5cdc704aa8f08294)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/11f6986b45453ef1)
  - [nihileyugo, , 13 Sep 2026](https://pokepast.es/30899280e5d3329e)

### Community 4: Tyranitar / Excadrill / Milotic
- Primary teams: 37 (primary share 8.6%), hybrid teams: 11 (hybrid share 2.6%)
- Date range: 2026-09-09 to 2026-09-22
- Core pairs: Corviknight+Indeedee@Choice Scarf, Corviknight+Indeedee, Corviknight+Excadrill@Focus Sash, Corviknight+Tyranitar@Tyranitarite, Excadrill+Tyranitar@Tyranitarite, Corviknight+Excadrill, Excadrill+Tyranitar, Tyranitar+Excadrill@Focus Sash, Corviknight+Tyranitar, Tyranitar+Excadrill@Life Orb, Excadrill+Indeedee@Choice Scarf, Indeedee+Excadrill@Focus Sash, Excadrill+Indeedee, Excadrill+Milotic@Sitrus Berry, Tyranitar+Milotic@Sitrus Berry, Tyranitar+Indeedee@Choice Scarf, Indeedee+Tyranitar@Tyranitarite, Indeedee+Tyranitar, Indeedee+Sneasler@Psychic Seed, Tyranitar+Sinistcha@Colbur Berry, Indeedee+Milotic@Sitrus Berry, Sinistcha+Excadrill@Focus Sash, Absol+Milotic@Leftovers, Sinistcha+Tyranitar@Tyranitarite, Milotic+Tyranitar@Tyranitarite, Excadrill+Sinistcha, Milotic+Excadrill@Focus Sash, Milotic+Tyranitar, Excadrill+Milotic, Sinistcha+Tyranitar, Milotic+Indeedee@Choice Scarf, Indeedee+Milotic, Milotic+Sinistcha@Colbur Berry, Excadrill+Sneasler@Psychic Seed, Gholdengo+Milotic@Sitrus Berry, Corviknight+Sneasler, Sneasler+Indeedee@Focus Sash, Metagross+Milotic, Milotic+Metagross@Metagrossite, Tyranitar+Sneasler@Psychic Seed, Milotic+Rillaboom@Life Orb, Milotic+Garchomp@Garchompite Z, Absol+Milotic, Milotic+Absol@Absolite Z, Salamence+Excadrill@Focus Sash, Indeedee+Sneasler, Excadrill+Salamence, Excadrill+Salamence@Salamencite, Salamence+Tyranitar, Tyranitar+Salamence@Salamencite, Sneasler+Indeedee@Choice Scarf, Arcanine-Hisui+Milotic@Leftovers, Salamence+Tyranitar@Tyranitarite, Baxcalibur+Milotic@Leftovers, Salamence+Milotic@Sitrus Berry, Milotic+Gholdengo@Life Orb, Salamence+Indeedee@Choice Scarf, Indeedee+Arcanine-Hisui@Focus Sash, Indeedee+Sneasler@White Herb, Gholdengo+Tyranitar, Golisopod+Milotic@Leftovers, Arcanine-Hisui+Indeedee, Gholdengo+Tyranitar@Tyranitarite, Arcanine-Hisui+Milotic, Gholdengo+Excadrill@Focus Sash, Tyranitar+Gholdengo@Life Orb, Milotic+Baxcalibur@Baxcalibrite, Raichu+Milotic@Leftovers, Milotic+Arcanine-Hisui@Focus Sash, Gholdengo+Milotic, Indeedee+Salamence, Indeedee+Salamence@Salamencite, Farigiraf+Milotic@Leftovers, Excadrill+Sneasler@White Herb, Garchomp+Indeedee, Excadrill+Gholdengo, Garchomp+Milotic@Leftovers, Gholdengo+Milotic@Leftovers, Milotic+Sneasler@Focus Sash, Ceruledge+Rillaboom, Tyranitar+Sneasler@White Herb, Baxcalibur+Milotic, Rillaboom+Milotic@Leftovers, Excadrill+Gholdengo@Life Orb, Milotic+Sinistcha, Garchomp+Milotic, Armarouge+Excadrill, Sneasler+Tyranitar, Milotic+Salamence, Milotic+Salamence@Salamencite, Milotic+Rillaboom
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Tyranitar | 72.2% | weather-setter 100.0%, mega-attacker 85.8%, setup 13.2% |
| Excadrill | 62.2% | setup 8.6% |
| Milotic | 61.5% | speed-drop 44.8%, status 36.6%, setup 36.6%, helping-hand 3.0%, screens 1.4% |
| Indeedee | 41.9% | terrain-setter 100.0%, priority-blocker 100.0%, trick-room-setter 31.9%, spa-drop 29.9%, disruption 29.1%, helping-hand 23.2% |
| Corviknight | 13.1% | setup 100.0%, tailwind 28.9% |
| Dragapult | 3.7% | status 100.0%, disruption 10.5% |
| Ceruledge | 1.9% | setup 100.0%, priority-attack 74.7% |
- Representative teams (primary teams with the highest score for this community):
  - [CloverBells, , 13 Sep 2026](https://pokepast.es/6a572a56178f0271)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/8d10dc6e316e86b0)
  - [jessditta, , 10 Sep 2026](https://pokepast.es/5a778ac69b5c4f0d)

### Community 5: Garchomp / Charizard / Metagross
- Primary teams: 9 (primary share 2.2%), hybrid teams: 9 (hybrid share 2.6%)
- Date range: 2026-09-10 to 2026-09-22
- Core pairs: Venusaur+Charizard@Charizardite Y, Charizard+Venusaur, Charizard+Venusaur@Focus Sash, Charizard+Garchomp@Choice Scarf, Grimmsnarl+Charizard@Charizardite Y, Charizard+Garchomp@Life Orb, Charizard+Grimmsnarl, Charizard+Grimmsnarl@Light Clay, Charizard+Rillaboom@Occa Berry, Gardevoir+Garchomp@Life Orb, Garchomp+Charizard@Charizardite Y, Metagross+Garchomp@Garchompite Z, Charizard+Garchomp, Garchomp+Venusaur, Metagross+Indeedee-F@Rocky Helmet, Charizard+Farigiraf@Sitrus Berry, Floette-Eternal+Garchomp@Choice Scarf, Charizard+Incineroar@Chople Berry, Charizard+Indeedee-F@Psychic Seed, Indeedee-F+Venusaur@Focus Sash, Garchomp+Whimsicott, Farigiraf+Charizard@Charizardite Y, Pawmot+Garchomp@Garchompite Z, Garchomp+Metagross, Garchomp+Metagross@Metagrossite, Charizard+Farigiraf, Metagross+Milotic, Milotic+Metagross@Metagrossite, Milotic+Garchomp@Garchompite Z, Indeedee-F+Venusaur, Charizard+Kingambit@Focus Sash, Incineroar+Garchomp@Choice Scarf, Basculegion+Garchomp@Life Orb, Indeedee-F+Metagross, Indeedee-F+Metagross@Metagrossite, Archaludon+Charizard@Charizardite Y, Charizard+Archaludon@Leftovers, Lucario+Garchomp@Garchompite Z, Torkoal+Charizard@Charizardite Y, Charizard+Torkoal@Charcoal, Archaludon+Charizard, Indeedee-F+Garchomp@Life Orb, Garchomp+Volcarona, Charizard+Torkoal, Garchomp+Incineroar@Sitrus Berry, Garchomp+Lucario, Garchomp+Lucario@Lucarionite Z, Incineroar+Garchomp@Garchompite Z, Kingambit+Garchomp@Choice Scarf, Sylveon+Charizard@Charizardite Y, Charizard+Floette-Eternal, Charizard+Floette-Eternal@Floettite, Farigiraf+Garchomp@Garchompite Z, Garchomp+Incineroar, Charizard+Basculegion@Choice Scarf, Garchomp+Indeedee, Charizard+Sylveon@Fairy Feather, Garchomp+Milotic@Leftovers, Gardevoir+Charizard@Charizardite Y, Farigiraf+Garchomp, Charizard+Sylveon, Garchomp+Pawmot, Floette-Eternal+Garchomp, Garchomp+Floette-Eternal@Floettite, Sneasler+Garchomp@Garchompite Z, Garchomp+Sneasler@Focus Sash, Garchomp+Sneasler@Psychic Seed, Rillaboom+Garchomp@Garchompite Z, Charizard+Gardevoir, Charizard+Gardevoir@Gardevoirite, Garchomp+Milotic, Floette-Eternal+Charizard@Charizardite Y
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Garchomp | 91.6% | mega-attacker 60.4%, speed-drop 10.5% |
| Charizard | 64.0% | mega-attacker 100.0%, weather-setter 90.1%, setup 9.9% |
| Metagross | 31.0% | mega-attacker 100.0%, priority-attack 10.3%, setup 5.7% |
| Venusaur | 16.4% | status 81.5% |
- Representative teams (primary teams with the highest score for this community):
  - [Rushil Thakkar, 15th, 10 Sep 2026](https://pokepast.es/690626d4c0e505f9)
  - [spacesnak3, 37th, 21 Sep 2026](https://pokepast.es/7daa12192d6b54cf)
  - [Federico Camporesi, , 11 Sep 2026](https://pokepast.es/b7354f9c5435d066)

## 8. Hybrids and Unassigned
- Hybrid teams: 151
| Team | Primary | Hybrid |
| :--- | :--- | :--- |
| https://pokepast.es/76a971cc5c997172 | 1 | 0 |
| https://pokepast.es/73e5d6533b781089 | 0 | 4 |
| https://pokepast.es/6534595ea1b752f8 | 0 | 2 |
| https://pokepast.es/098ae48a631c4038 | 2 | 0 |
| https://pokepast.es/a4fd7f400e9b4e49 | 0 | 1 |
| https://pokepast.es/e558d7755eb58e36 | 2 | 0 |
| https://pokepast.es/a719c9e52c55cc43 | 1 | 0 |
| https://pokepast.es/c8f60c5168bd6a83 | 0 | 2 |
| https://pokepast.es/4c6eb1d0d2cbb3f3 | 0 | 3 |
| https://pokepast.es/6c4aaf8c40a3f736 | 4 | 0 |
| https://pokepast.es/d4087f1527d4e0bc | 0 | 1 |
| https://pokepast.es/d0ee4dbd575e2250 | 1 | 0 |
| https://pokepast.es/b1e193478a17c829 | 0 | 4 |
| https://pokepast.es/4ebc34b995c1cba6 | 1 | 0 |
| https://pokepast.es/a1338edf35719660 | 0 | 5 |
| https://pokepast.es/0f4ac087557f8301 | 0 | 1 |
| https://pokepast.es/d5c07cfcb88237b6 | 4 | 0 |
| https://pokepast.es/3ac336df2eb4729b | 0 | 3 |
| https://pokepast.es/bb66ff17a1c4a911 | 1 | 0 |
| https://pokepast.es/e8d7dc3cd666c904 | 1 | 0 |
| https://pokepast.es/b230239de70d9692 | 3 | 0 |
| https://pokepast.es/02e5603c8656bf06 | 0 | 4 |
| https://pokepast.es/fb6e8f3c9d655db6 | 0 | 2 |
| https://pokepast.es/5cdf0e8a8a73eed7 | 0 | 2 |
| https://pokepast.es/027fda21958e66de | 0 | 3 |
| https://pokepast.es/81d3f0ffe6ef750c | 0 | 2 |
| https://pokepast.es/60002baa327ce677 | 4 | 1 |
| https://pokepast.es/ccab3077d06265a8 | 1 | 0 |
| https://pokepast.es/60458cc1a20c2440 | 0 | 3 |
| https://pokepast.es/0602ae9713162c9f | 0 | 2 |
| https://pokepast.es/c02e6e394a429969 | 0 | 1 |
| https://pokepast.es/595b9623f782bb8f | 1 | 0 |
| https://pokepast.es/f6455b416b2e9f80 | 0 | 2 |
| https://pokepast.es/d8faaf4f72f6aab5 | 1 | 0 |
| https://pokepast.es/4ed17394f6517aaf | 0 | 1 |
| https://pokepast.es/bed443f8a0acbe11 | 1 | 0 |
| https://pokepast.es/6c092ddbec51ef61 | 3 | 0 |
| https://pokepast.es/0a93ae91073cc690 | 2 | 0 |
| https://pokepast.es/1f0098cf6f53ab9e | 4 | 0 |
| https://pokepast.es/56f517bbd665899f | 3 | 0 |
| https://pokepast.es/0f1752f2ba99c09f | 1 | 0 |
| https://pokepast.es/6e9f14839c44a236 | 0 | 2 |
| https://pokepast.es/3020a0b5e5de04ae | 1 | 0 |
| https://pokepast.es/06af2abd81f9c34f | 1 | 0 |
| https://pokepast.es/605ab0da3552400d | 4 | 0 |
| https://pokepast.es/2015e49d83d81c82 | 4 | 0 |
| https://pokepast.es/fdc2970699476b2c | 4 | 0 |
| https://pokepast.es/07e91402b74462ef | 3 | 0 |
| https://pokepast.es/b70d72f44626b60d | 1 | 3 |
| https://pokepast.es/e6497a1a671dac90 | 1 | 0 |
| https://pokepast.es/243c449b24b74266 | 4 | 3 |
| https://pokepast.es/90f7ccc7ab5b3d0b | 2 | 0 |
| https://pokepast.es/97edd96cf0de7f14 | 4 | 0 |
| https://pokepast.es/5a342ea870e2bbe8 | 4 | 0 |
| https://pokepast.es/87947bfa8f77f2c4 | 0 | 2 |
| https://pokepast.es/73a1ddc47375195f | 3 | 0 |
| https://pokepast.es/7c4bc48a6a1906e6 | 2 | 0 |
| https://pokepast.es/c419e3e0d8f1c5d4 | 0 | 3 |
| https://pokepast.es/9cc929d200baed42 | 1 | 0 |
| https://pokepast.es/daca6f08f851a3a5 | 2 | 0 |
| https://pokepast.es/8395839107df60b4 | 4 | 5 |
| https://pokepast.es/2106e9c7e469a2dd | 3 | 1 |
| https://pokepast.es/b464a7fc05783469 | 2 | 4 |
| https://pokepast.es/412d57292e8dbd7e | 0 | 3 |
| https://pokepast.es/8782bc837073b808 | 3 | 0 |
| https://pokepast.es/a982beaee27d32ec | 2 | 0 |
| https://pokepast.es/e74e8282d41a3802 | 0 | 5 |
| https://pokepast.es/a202a04735494175 | 0 | 5 |
| https://pokepast.es/235094a08938ad08 | 0 | 2 |
| https://pokepast.es/a630a7a5018325c9 | 0 | 2 |
| https://pokepast.es/43bdff98a519db09 | 4 | 0 |
| https://pokepast.es/31a5a41549fd6ddf | 0 | 2 |
| https://pokepast.es/426195e3e323334c | 0 | 2 |
| https://pokepast.es/3c4611ccba18d35a | 0 | 3 |
| https://pokepast.es/d4ace2bc38fdbd71 | 0 | 4 |
| https://pokepast.es/6c8f1d6176940580 | 2 | 0 |
| https://pokepast.es/b6bace50909f3571 | 0 | 2 |
| https://pokepast.es/ac6967ec268cbdf8 | 4 | 0 |
| https://pokepast.es/5890d238feb764a2 | 4 | 0 |
| https://pokepast.es/a702bf47f522438b | 3 | 0 |
| https://pokepast.es/7664efb6099c8d98 | 0 | 1 |
| https://pokepast.es/a0582a49f5490809 | 0 | 2 |
| https://pokepast.es/369e75b64155b6a1 | 1 | 3 |
| https://pokepast.es/0149eb0e0ddf41fe | 4 | 0 |
| https://pokepast.es/69bd51af4fde93dc | 0 | 2 |
| https://pokepast.es/8ba4c9c260b8e9ae | 0 | 1 |
| https://pokepast.es/9144f9e5950aaa23 | 4 | 0 |
| https://pokepast.es/8bc91085c2883385 | 1 | 0 |
| https://pokepast.es/a634bf11f53735ba | 0 | 4 |
| https://pokepast.es/79fc59b9540aa76f | 1 | 0 |
| https://pokepast.es/12c7b27a8e53ed9c | 2 | 0 |
| https://pokepast.es/5a82e9d03b9d6cc2 | 0 | 2 |
| https://pokepast.es/668502969512b159 | 0 | 5 |
| https://pokepast.es/1f4da9800a6ad851 | 1 | 0 |
| https://pokepast.es/565006700c23dcd1 | 3 | 4 |
| https://pokepast.es/0d18f72df8bd8d92 | 0 | 5 |
| https://pokepast.es/31945882c8d00260 | 1 | 0 |
| https://pokepast.es/181b08493e5a2472 | 5 | 0 |
| https://pokepast.es/fd6fe5bb94a024fb | 0 | 2 |
| https://pokepast.es/0bdce0e34fab1ba0 | 0 | 2 |
| https://pokepast.es/472a153c121df28d | 1 | 0 |
| https://pokepast.es/be30055fe03e4b61 | 0 | 2 |
| https://pokepast.es/145f31732b5aa73b | 4 | 0 |
| https://pokepast.es/a1b7a6d0af006124 | 3 | 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/27LoUGHWZssTh74nA0Oc | 3 | 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/VW1m8TfHx0RIVs4aGHGl | 4 | 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/xwGrkGvg8PXbgIxsRNlv | 0 | 2 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/dB80NsfxqmJBVxknlXR3 | 3 | 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/0lbS1fGaeHdeq74HLZWq | 5 | 2 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/bASM2pmAEMjJYJ4ja7yI | 1 | 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/Ki07vssP2mbfd4C1zgZz | 1 | 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/tp1xMws30GePDkvo69zC | 5 | 4 |
| https://pokepast.es/2f1c6631010c3644 | 3 | 0 |
| https://pokepast.es/e97cb94d7e8d7678 | 1 | 0 |
| https://pokepast.es/1a24e07035a9036e | 5 | 4 |
| https://pokepast.es/ea35d1777f660322 | 5 | 4 |
| https://pokepast.es/d41341435d22d77b | 0 | 2 |
| https://pokepast.es/0bd9b6346f3630a1 | 0 | 2 |
| https://pokepast.es/d3277b02c40781f3 | 2 | 0 |
| https://pokepast.es/e4aa1030c5e684b4 | 0 | 2 |
| https://pokepast.es/270ef42ee47f0369 | 0 | 2 |
| https://pokepast.es/097433efcc505367 | 0 | 4 |
| https://pokepast.es/a8154c05ecfa0e20 | 0 | 2 |
| https://pokepast.es/8cc6b2b7139e9db9 | 1 | 0 |
| https://pokepast.es/8025f0aef5f2d1b6 | 0 | 2 |
| https://pokepast.es/00c891d2f3287a74 | 0 | 3 |
| https://pokepast.es/bfe773d9cda8999c | 2 | 0 |
| https://pokepast.es/e01ad4bad0da9ccc | 0 | 2 |
| https://pokepast.es/2d99e9105e5f7a58 | 0 | 5 |
| https://pokepast.es/dbc606408d738253 | 2 | 0 |
| https://pokepast.es/120461d751b66e3a | 0 | 5 |
| https://pokepast.es/deb9dac3ff5777d5 | 2 | 0 |
| https://pokepast.es/ecdecc58b9116d68 | 2 | 0 |
| https://pokepast.es/d9fd914e8be2c4c6 | 5 | 0 |
| https://pokepast.es/b987727e0339084a | 0 | 3 |
| https://pokepast.es/ddcf443e371a3a19 | 2 | 0 |
| https://pokepast.es/0e98dddb17860aaa | 2 | 0 |
| https://pokepast.es/5cedf6dd944db220 | 2 | 0 |
| https://pokepast.es/25d5f5aae215e08c | 3 | 0 |
| https://pokepast.es/98d6e2fba3995368 | 4 | 0 |
| https://pokepast.es/b960485d27caebf9 | 0 | 2 |
| https://pokepast.es/ffe1c04c186b2453 | 0 | 5 |
| https://pokepast.es/8bffd79270860304 | 0 | 2 |
| https://pokepast.es/6e006ee64e68ff6b | 0 | 2 |
| https://pokepast.es/4dd5fcddd90e5ebb | 3 | 0 |
| https://pokepast.es/000dee84b382d990 | 0 | 2 |
| https://pokepast.es/93b626e0c928be89 | 1 | 0 |
| https://pokepast.es/817ae21505e679f7 | 0 | 1 |
| https://pokepast.es/d6b2f01ddf4fee52 | 0 | 2 |
| https://pokepast.es/ab47ccee10152485 | 0 | 3 |
| https://pokepast.es/f85133bac58b317f | 0 | 2 |
- Unassigned teams: 2 (share 0.4%)
| Team |
| :--- |
| https://pokepast.es/f4c6b857affab3fe |
| https://pokepast.es/efddc5627f766a06 |

## 9. Communities vs. Cluster Archetypes
Rows: each team's primary community (or unassigned); columns: its cluster_teams cluster.
| Community | cluster-1 | cluster-2 | cluster-3 | cluster-4 | cluster-5 | cluster-6 | cluster-7 | cluster-8 | cluster-9 | cluster-10 | cluster-11 | cluster-12 | cluster-13 | cluster-14 | cluster-15 | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Community 0: Rillaboom / Sneasler / Salamence | 109 | 10 | 0 | 1 | 26 | 4 | 7 | 0 | 7 | 9 | 0 | 9 | 0 | 2 | 0 | 18 |
| Community 1: Indeedee-F / Armarouge / Gardevoir | 3 | 1 | 23 | 0 | 0 | 19 | 0 | 0 | 0 | 0 | 8 | 0 | 0 | 0 | 6 | 6 |
| Community 2: Gholdengo / Raichu / Arcanine-Hisui | 0 | 28 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
| Community 3: Golisopod / Archaludon / Pelipper | 2 | 0 | 6 | 0 | 0 | 0 | 8 | 14 | 4 | 0 | 1 | 0 | 8 | 6 | 0 | 1 |
| Community 4: Tyranitar / Excadrill / Milotic | 0 | 7 | 0 | 27 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 2 |
| Community 5: Garchomp / Charizard / Metagross | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 8 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 2 |
- Cluster majority:
| Cluster | Archetype | Majority community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Salamence + Rillaboom/Sneasler Balance | Community 0: Rillaboom / Sneasler / Salamence | 95.6% |
| cluster-2 | Mega Salamence Balance | Community 2: Gholdengo / Raichu / Arcanine-Hisui | 60.9% |
| cluster-3 | Mega Gardevoir Psyspam Offense | Community 1: Indeedee-F / Armarouge / Gardevoir | 79.3% |
| cluster-4 | Mega Tyranitar + Excadrill/Sneasler Sand Balance | Community 4: Tyranitar / Excadrill / Milotic | 96.4% |
| cluster-5 | Mega Floette-Eternal + Incineroar/Rillaboom Balance | Community 0: Rillaboom / Sneasler / Salamence | 96.3% |
| cluster-6 | Mega Gardevoir Psyspam Offense | Community 1: Indeedee-F / Armarouge / Gardevoir | 82.6% |
| cluster-7 | Mega Gengar + Incineroar/Rillaboom Rain | Community 3: Golisopod / Archaludon / Pelipper | 53.3% |
| cluster-8 | Mega Golisopod + Pelipper/Archaludon Rain | Community 3: Golisopod / Archaludon / Pelipper | 100.0% |
| cluster-9 | Mega Golisopod + Rillaboom/Milotic Trick Room | Community 0: Rillaboom / Sneasler / Salamence | 58.3% |
| cluster-10 | Mega Lucario + Rillaboom Tailwind Offense | Community 0: Rillaboom / Sneasler / Salamence | 100.0% |
| cluster-11 | Mega Golisopod Psyspam Trick Room | Community 1: Indeedee-F / Armarouge / Gardevoir | 88.9% |
| cluster-12 | Mega Salamence + Volcarona/Glimmora Tailwind Offense | Community 0: Rillaboom / Sneasler / Salamence | 100.0% |
| cluster-13 | Mega Golisopod + Grimmsnarl/Archaludon Rain | Community 3: Golisopod / Archaludon / Pelipper | 100.0% |
| cluster-14 | Mega Golisopod + Rillaboom/Farigiraf Trick Room | Community 3: Golisopod / Archaludon / Pelipper | 75.0% |
| cluster-15 | Mega Golisopod Psyspam Trick Room | Community 1: Indeedee-F / Armarouge / Gardevoir | 100.0% |

### Sub-communities of Community 0: Rillaboom / Sneasler / Salamence vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster_teams cluster. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Salamence + Rillaboom/Sneasler Balance) | cluster-2 (Mega Salamence Balance) | cluster-4 (Mega Tyranitar + Excadrill/Sneasler Sand Balance) | cluster-5 (Mega Floette-Eternal + Incineroar/Rillaboom Balance) | cluster-6 (Mega Gardevoir Psyspam Offense) | cluster-7 (Mega Gengar + Incineroar/Rillaboom Rain) | cluster-9 (Mega Golisopod + Rillaboom/Milotic Trick Room) | cluster-10 (Mega Lucario + Rillaboom Tailwind Offense) | cluster-12 (Mega Salamence + Volcarona/Glimmora Tailwind Offense) | cluster-14 (Mega Golisopod + Rillaboom/Farigiraf Trick Room) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 31 | 8 | 1 | 13 | 0 | 2 | 3 | 3 | 0 | 0 | 5 |
| Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 32 | 2 | 0 | 0 | 1 | 0 | 0 | 0 | 6 | 1 | 2 |
| Sub-community 2: Floette-Eternal@Floettite / Sneasler@Grassy Seed / Sneasler@Focus Sash | 32 | 0 | 0 | 10 | 0 | 0 | 0 | 0 | 1 | 0 | 3 |
| Sub-community 3: Sylveon@Fairy Feather / Lucario@Lucarionite Z / Basculegion@Life Orb | 9 | 0 | 0 | 1 | 1 | 0 | 0 | 6 | 1 | 0 | 5 |
| Sub-community 4: Rillaboom@Life Orb / Milotic@Leftovers / Kingambit@Focus Sash | 5 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 1 | 0 |
| Sub-community 6: Gengar@Gengarite / Rillaboom (other item; Eject Button 4/10) | 0 | 0 | 0 | 0 | 0 | 4 | 0 | 0 | 0 | 0 | 1 |
| minor | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 |
| unassigned | 0 | 0 | 0 | 1 | 2 | 1 | 0 | 0 | 1 | 0 | 2 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Salamence + Rillaboom/Sneasler Balance | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 29.4% |
| cluster-2 | Mega Salamence Balance | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 80.0% |
| cluster-4 | Mega Tyranitar + Excadrill/Sneasler Sand Balance | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 100.0% |
| cluster-5 | Mega Floette-Eternal + Incineroar/Rillaboom Balance | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 50.0% |
| cluster-6 | Mega Gardevoir Psyspam Offense | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 25.0% |
| cluster-7 | Mega Gengar + Incineroar/Rillaboom Rain | Sub-community 6: Gengar@Gengarite / Rillaboom (other item; Eject Button 4/10) | 57.1% |
| cluster-9 | Mega Golisopod + Rillaboom/Milotic Trick Room | Sub-community 0: Rillaboom@Miracle Seed / Incineroar@Sitrus Berry / Gholdengo@Life Orb | 42.9% |
| cluster-10 | Mega Lucario + Rillaboom Tailwind Offense | Sub-community 3: Sylveon@Fairy Feather / Lucario@Lucarionite Z / Basculegion@Life Orb | 66.7% |
| cluster-12 | Mega Salamence + Volcarona/Glimmora Tailwind Offense | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 66.7% |
| cluster-14 | Mega Golisopod + Rillaboom/Farigiraf Trick Room | Sub-community 1: Salamence@Salamencite / Kingambit@Chople Berry / Sneasler@White Herb | 50.0% |

## 10. Set Variants and Role Tags
### Rillaboom
- Teams: 220, weighted support: 56.6%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: terrain-setter 100.0%, fake-out 99.7%, priority-attack 99.4%, pivot 42.8%, speed-drop 2.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Miracle Seed | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | offensive | 28 | 14.6% |
| Miracle Seed | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | bulky | 27 | 14.0% |
| Miracle Seed | Fake Out, Grassy Glide, U-turn, Wood Hammer | bulky | 28 | 11.9% |
| Miracle Seed | Fake Out, Grassy Glide, U-turn, Wood Hammer | offensive | 29 | 11.7% |
| Occa Berry | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | bulky | 6 | 2.7% |
- Other signatures: 45.1%

### Sneasler
- Teams: 195, weighted support: 47.1%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: seed-unburden 56.8%, fake-out 47.4%, speed-drop 10.8%, ally-boost 10.2%, setup 6.2%, quick-guard 3.5%, priority-blocker 3.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| White Herb | Close Combat, Dire Claw, Fake Out, Protect | fast | 18 | 8.9% |
| Psychic Seed | Close Combat, Dire Claw, Protect, Rock Slide | fast | 14 | 7.4% |
| Grassy Seed | Close Combat, Dire Claw, Protect, Rock Slide | fast | 13 | 7.0% |
| Grassy Seed | Close Combat, Dire Claw, Fake Out, Protect | fast | 11 | 5.6% |
| Psychic Seed | Close Combat, Dire Claw, Protect, Throat Chop | fast | 10 | 4.9% |
- Other signatures: 66.2%

### Salamence
- Teams: 159, weighted support: 39.5%, mega share: 100.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: intimidate 98.8%, mega-attacker 96.8%, tailwind 77.7%, setup 3.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Salamencite | Draco Meteor, Hyper Voice, Protect, Tailwind | fast | 54 | 36.1% |
| Salamencite | Double-Edge, Hyper Voice, Protect, Tailwind | fast | 31 | 17.5% |
| Salamencite | Flamethrower, Hyper Voice, Protect, Tailwind | fast | 20 | 12.3% |
| Salamencite | Draco Meteor, Flamethrower, Hyper Voice, Protect | fast | 7 | 4.0% |
| Salamencite | Dragon Pulse, Hyper Voice, Protect, Tailwind | fast | 6 | 3.8% |
- Other signatures: 26.3%

### Incineroar
- Teams: 139, weighted support: 35.8%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: fake-out 100.0%, intimidate 100.0%, pivot 98.4%, trick-room-abuser 13.8%, spa-drop 10.3%, helping-hand 6.3%, disruption 2.7%, status 1.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Fake Out, Flare Blitz, Parting Shot, Throat Chop | bulky | 44 | 28.9% |
| Sitrus Berry | Darkest Lariat, Fake Out, Flare Blitz, Parting Shot | bulky | 18 | 11.7% |
| Sitrus Berry | Fake Out, Flare Blitz, Helping Hand, Parting Shot | bulky | 6 | 5.1% |
| Sitrus Berry | Fake Out, Flare Blitz, Parting Shot, Snarl | bulky | 6 | 4.7% |
| Chople Berry | Fake Out, Flare Blitz, Parting Shot, Snarl | bulky | 4 | 3.6% |
- Other signatures: 45.9%

### Kingambit
- Teams: 115, weighted support: 29.0%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: priority-attack 98.5%, setup 25.6%, trick-room-abuser 5.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Chople Berry | Iron Head, Kowtow Cleave, Low Kick, Sucker Punch | bulky | 16 | 14.1% |
| Focus Sash | Iron Head, Kowtow Cleave, Low Kick, Sucker Punch | fast | 15 | 12.4% |
| Life Orb | Kowtow Cleave, Protect, Sucker Punch, Swords Dance | offensive | 13 | 11.1% |
| Chople Berry | Iron Head, Kowtow Cleave, Low Kick, Sucker Punch | offensive | 11 | 9.9% |
| Black Glasses | Kowtow Cleave, Protect, Sucker Punch, Swords Dance | offensive | 6 | 5.0% |
- Other signatures: 47.5%

### Indeedee-F
- Teams: 100, weighted support: 24.2%, mega share: 0.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: follow-me 100.0%, terrain-setter 98.6%, priority-blocker 98.6%, helping-hand 92.2%, trick-room-setter 83.0%, disruption 7.1%, status 3.9%, spa-drop 3.5%, fake-out 2.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Rocky Helmet | Follow Me, Helping Hand, Psychic, Trick Room | min-speed | 11 | 11.6% |
| Rocky Helmet | Follow Me, Helping Hand, Psychic, Trick Room | bulky | 10 | 10.7% |
| Psychic Seed | Follow Me, Helping Hand, Psychic, Trick Room | min-speed | 7 | 5.9% |
| Colbur Berry | Follow Me, Helping Hand, Psychic, Trick Room | min-speed | 3 | 4.2% |
| Psychic Seed | Follow Me, Helping Hand, Psychic, Trick Room | bulky | 4 | 4.2% |
- Other signatures: 63.4%

### Gholdengo
- Teams: 87, weighted support: 24.1%, mega share: 0.0%
- Community: Community 2: Gholdengo / Raichu / Arcanine-Hisui
- Roles: setup 89.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | fast | 51 | 60.3% |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | bulky | 12 | 12.8% |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | unknown | 3 | 4.7% |
| Life Orb | Make It Rain, Power Gem, Protect, Shadow Ball | fast | 3 | 2.7% |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | offensive | 2 | 2.6% |
- Other signatures: 16.9%

### Basculegion
- Teams: 89, weighted support: 21.5%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: priority-attack 96.8%, pivot 35.4%, setup 0.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Aqua Jet, Flip Turn, Last Respects, Wave Crash | fast | 27 | 32.2% |
| Life Orb | Aqua Jet, Last Respects, Protect, Wave Crash | fast | 25 | 28.0% |
| Focus Sash | Aqua Jet, Last Respects, Liquidation, Protect | fast | 8 | 7.7% |
| Life Orb | Aqua Jet, Last Respects, Protect, Wave Crash | offensive | 7 | 6.8% |
| Life Orb | Aqua Jet, Last Respects, Protect, Wave Crash | mixed | 3 | 3.4% |
- Other signatures: 21.9%

### Floette-Eternal
- Teams: 69, weighted support: 16.6%, mega share: 100.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: mega-attacker 100.0%, setup 73.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Floettite | Calm Mind, Dazzling Gleam, Moonblast, Protect | bulky | 15 | 18.6% |
| Floettite | Dazzling Gleam, Light of Ruin, Moonblast, Protect | fast | 13 | 18.0% |
| Floettite | Calm Mind, Dazzling Gleam, Draining Kiss, Protect | fast | 8 | 14.6% |
| Floettite | Calm Mind, Dazzling Gleam, Draining Kiss, Protect | bulky | 10 | 13.7% |
| Floettite | Calm Mind, Dazzling Gleam, Moonblast, Protect | fast | 8 | 10.6% |
- Other signatures: 24.5%

### Golisopod
- Teams: 66, weighted support: 16.2%, mega share: 100.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: mega-attacker 100.0%, priority-attack 44.1%, setup 41.9%, trick-room-abuser 30.4%, pivot 9.3%, wide-guard 1.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Golisopite | Iron Head, Leech Life, Protect, Sucker Punch | offensive | 6 | 10.4% |
| Golisopite | Iron Head, Leech Life, Protect, Swords Dance | offensive | 5 | 9.6% |
| Golisopite | Leech Life, Protect, Sucker Punch, Swords Dance | offensive | 5 | 7.9% |
| Golisopite | Iron Head, Leech Life, Sucker Punch, Swords Dance | offensive | 2 | 4.3% |
| Golisopite | First Impression, Iron Head, Protect, U-turn | offensive | 3 | 3.8% |
- Other signatures: 64.0%

### Milotic
- Teams: 63, weighted support: 15.6%, mega share: 0.0%
- Community: Community 4: Tyranitar / Excadrill / Milotic
- Roles: speed-drop 44.8%, status 36.6%, setup 36.6%, helping-hand 3.0%, screens 1.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Ice Beam, Icy Wind, Protect, Scald | bulky | 12 | 19.7% |
| Leftovers | Coil, Hypnosis, Muddy Water, Protect | bulky | 8 | 13.9% |
| Leftovers | Coil, Hypnosis, Muddy Water, Recover | bulky | 4 | 5.9% |
| Leftovers | Ice Beam, Protect, Recover, Scald | bulky | 4 | 5.7% |
| Leftovers | Ice Beam, Icy Wind, Protect, Scald | bulky | 2 | 4.3% |
- Other signatures: 50.5%

### Garchomp
- Teams: 54, weighted support: 14.5%, mega share: 60.4%
- Community: Community 5: Garchomp / Charizard / Metagross
- Roles: mega-attacker 60.4%, speed-drop 10.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Garchompite Z | Draco Meteor, Flamethrower, Power Gem, Protect | fast | 10 | 17.4% |
| Garchompite Z | Dragon Pulse, Flamethrower, Power Gem, Protect | fast | 4 | 9.5% |
| Life Orb | Dragon Claw, Earthquake, Protect, Rock Slide | fast | 5 | 7.8% |
| Choice Scarf | Dragon Claw, Earthquake, Rock Slide, Stomping Tantrum | fast | 5 | 7.0% |
| Garchompite Z | Draco Meteor, Earth Power, Flamethrower, Protect | fast | 4 | 6.9% |
- Other signatures: 51.4%

### Arcanine-Hisui
- Teams: 52, weighted support: 14.0%, mega share: 0.0%
- Community: Community 2: Gholdengo / Raichu / Arcanine-Hisui
- Roles: priority-attack 92.1%, intimidate 11.9%, spa-drop 1.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Extreme Speed, Flare Blitz, Head Smash, Protect | fast | 39 | 74.1% |
| Focus Sash | Extreme Speed, Flare Blitz, Head Smash, Protect | unknown | 3 | 10.6% |
| Focus Sash | Extreme Speed, Flare Blitz, Protect, Rock Slide | fast | 3 | 5.7% |
| Focus Sash | Flare Blitz, Head Smash, Protect, Rock Slide | fast | 3 | 3.7% |
| Charcoal | Close Combat, Flare Blitz, Protect, Rock Slide | min-speed | 1 | 1.7% |
- Other signatures: 4.1%

### Raichu
- Teams: 40, weighted support: 11.7%, mega share: 100.0%
- Community: Community 2: Gholdengo / Raichu / Arcanine-Hisui
- Roles: speed-drop 100.0%, mega-attacker 100.0%, fake-out 73.2%, disruption 29.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Raichunite Y | Fake Out, Focus Blast, Protect, Zap Cannon | fast | 27 | 63.8% |
| Raichunite Y | Encore, Focus Blast, Protect, Zap Cannon | fast | 8 | 22.6% |
| Raichunite Y | Fake Out, Focus Blast, Protect, Zap Cannon | unknown | 2 | 6.4% |
| Raichunite Y | Encore, Focus Blast, Protect, Zap Cannon | unknown | 1 | 4.2% |
| Raichunite Y | Encore, Fake Out, Focus Blast, Zap Cannon | fast | 2 | 3.0% |
- Other signatures: 0.0%

### Sylveon
- Teams: 43, weighted support: 11.1%, mega share: 0.0%
- Community: Community 2: Gholdengo / Raichu / Arcanine-Hisui
- Roles: priority-attack 91.6%, status 10.8%, spa-drop 4.5%, setup 4.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Quick Attack | offensive | 10 | 23.5% |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Quick Attack | mixed | 8 | 17.5% |
| Fairy Feather | Hyper Beam, Hyper Voice, Protect, Quick Attack | bulky | 4 | 12.7% |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Quick Attack | bulky | 6 | 11.5% |
| Fairy Feather | Hyper Beam, Hyper Voice, Protect, Quick Attack | unknown | 1 | 4.5% |
- Other signatures: 30.4%

### Pelipper
- Teams: 39, weighted support: 10.4%, mega share: 0.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: weather-setter 100.0%, tailwind 81.8%, wide-guard 65.5%, helping-hand 5.1%, trick-room-abuser 2.5%, pivot 1.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Hurricane, Tailwind, Weather Ball, Wide Guard | bulky | 10 | 24.2% |
| Focus Sash | Hurricane, Protect, Tailwind, Weather Ball | fast | 6 | 18.3% |
| Focus Sash | Hurricane, Protect, Weather Ball, Wide Guard | fast | 3 | 8.1% |
| Focus Sash | Hurricane, Tailwind, Weather Ball, Wide Guard | bulky | 3 | 6.4% |
| Sitrus Berry | Hurricane, Protect, Tailwind, Weather Ball | bulky | 2 | 5.4% |
- Other signatures: 37.5%

### Archaludon
- Teams: 37, weighted support: 10.3%, mega share: 0.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: spa-drop 18.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Dragon Pulse, Electro Shot, Flash Cannon, Protect | bulky | 17 | 45.2% |
| Leftovers | Dragon Pulse, Electro Shot, Protect, Snarl | bulky | 4 | 11.2% |
| Leftovers | Dragon Pulse, Electro Shot, Flash Cannon, Protect | unknown | 2 | 8.6% |
| Leftovers | Aura Sphere, Dragon Pulse, Electro Shot, Protect | bulky | 2 | 6.8% |
| Leftovers | Dragon Pulse, Electro Shot, Flash Cannon, Precipice Blades | offensive | 2 | 4.5% |
- Other signatures: 23.6%

### Farigiraf
- Teams: 40, weighted support: 10.0%, mega share: 0.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: trick-room-setter 100.0%, priority-blocker 100.0%, helping-hand 49.1%, trick-room-abuser 31.8%, disruption 8.7%, weather-setter 2.7%, screens 2.1%, terrain-setter 2.0%, setup 1.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Helping Hand, Psychic, Thunderbolt, Trick Room | min-speed | 3 | 8.7% |
| Sitrus Berry | Expanding Force, Imprison, Protect, Trick Room | bulky | 2 | 6.4% |
| Colbur Berry | Protect, Thunderbolt, Trick Room, Twin Beam | bulky | 3 | 6.0% |
| Colbur Berry | Helping Hand, Psychic, Thunderbolt, Trick Room | bulky | 2 | 5.5% |
| Sitrus Berry | Protect, Psychic, Thunderbolt, Trick Room | min-speed | 2 | 5.1% |
- Other signatures: 68.3%

### Armarouge
- Teams: 37, weighted support: 9.3%, mega share: 0.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: trick-room-setter 45.4%, wide-guard 34.0%, trick-room-abuser 29.2%, ally-switch 5.1%, setup 4.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Armor Cannon, Aura Sphere, Expanding Force, Protect | fast | 5 | 11.9% |
| Life Orb | Armor Cannon, Expanding Force, Protect, Trick Room | min-speed | 4 | 9.6% |
| Life Orb | Armor Cannon, Expanding Force, Protect, Trick Room | offensive | 2 | 8.4% |
| Life Orb | Armor Cannon, Expanding Force, Trick Room, Wide Guard | min-speed | 2 | 4.4% |
| Leftovers | Calm Mind, Heat Wave, Psychic, Trick Room | bulky | 1 | 4.4% |
- Other signatures: 61.3%

### Tyranitar
- Teams: 39, weighted support: 9.0%, mega share: 85.8%
- Community: Community 4: Tyranitar / Excadrill / Milotic
- Roles: weather-setter 100.0%, mega-attacker 85.8%, setup 13.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Tyranitarite | Knock Off, Low Kick, Protect, Rock Slide | fast | 18 | 45.8% |
| Tyranitarite | Knock Off, Low Kick, Protect, Rock Slide | bulky | 4 | 9.9% |
| Tyranitarite | Knock Off, Low Kick, Protect, Rock Slide | offensive | 4 | 9.7% |
| Tyranitarite | Dragon Dance, Knock Off, Protect, Rock Slide | fast | 2 | 6.2% |
| Choice Scarf | Ice Punch, Knock Off, Low Kick, Rock Slide | fast | 2 | 4.9% |
- Other signatures: 23.5%

### Charizard
- Teams: 33, weighted support: 8.6%, mega share: 100.0%
- Community: Community 5: Garchomp / Charizard / Metagross
- Roles: mega-attacker 100.0%, weather-setter 90.1%, setup 9.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Charizardite Y | Ancient Power, Heat Wave, Protect, Weather Ball | fast | 9 | 29.1% |
| Charizardite Y | Ancient Power, Heat Wave, Protect, Weather Ball | bulky | 6 | 21.1% |
| Charizardite Y | Heat Wave, Protect, Solar Beam, Weather Ball | fast | 5 | 16.0% |
| Charizardite X | Dragon Claw, Dragon Dance, Flare Blitz, Protect | fast | 3 | 7.1% |
| Charizardite Y | Heat Wave, Overheat, Protect, Solar Beam | bulky | 2 | 4.7% |
- Other signatures: 22.1%

### Gardevoir
- Teams: 34, weighted support: 7.7%, mega share: 100.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: mega-attacker 100.0%, trick-room-setter 46.4%, setup 24.5%, disruption 5.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Gardevoirite | Expanding Force, Hyper Voice, Protect, Trick Room | bulky | 8 | 24.0% |
| Gardevoirite | Expanding Force, Hyper Voice, Protect, Trick Room | offensive | 6 | 17.3% |
| Gardevoirite | Calm Mind, Expanding Force, Hyper Voice, Protect | unknown | 2 | 7.7% |
| Gardevoirite | Calm Mind, Expanding Force, Hyper Voice, Protect | fast | 1 | 5.4% |
| Gardevoirite | Expanding Force, Hyper Voice, Protect, Psychic | fast | 2 | 5.3% |
- Other signatures: 40.3%

### Excadrill
- Teams: 32, weighted support: 7.2%, mega share: 0.0%
- Community: Community 4: Tyranitar / Excadrill / Milotic
- Roles: setup 8.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | High Horsepower, Iron Head, Protect, Rock Slide | fast | 20 | 60.8% |
| Focus Sash | Earthquake, Iron Head, Protect, Rock Slide | fast | 4 | 11.2% |
| Life Orb | Earthquake, High Horsepower, Iron Head, Protect | fast | 3 | 8.7% |
| Focus Sash | High Horsepower, Iron Head, Protect, Swords Dance | fast | 2 | 8.6% |
| Life Orb | High Horsepower, Iron Head, Protect, Rock Slide | fast | 1 | 3.8% |
- Other signatures: 6.9%

### Lucario
- Teams: 27, weighted support: 6.7%, mega share: 100.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: mega-attacker 100.0%, setup 56.0%, priority-attack 4.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Lucarionite Z | Aura Sphere, Calm Mind, Detect, Flash Cannon | fast | 3 | 12.6% |
| Lucarionite Z | Aura Sphere, Calm Mind, Flash Cannon, Protect | fast | 4 | 12.4% |
| Lucarionite Z | Aura Sphere, Calm Mind, Detect, Flash Cannon | bulky | 2 | 8.2% |
| Lucarionite Z | Aura Sphere, Detect, Flash Cannon, Terrain Pulse | fast | 2 | 6.5% |
| Lucarionite Z | Aura Sphere, Flash Cannon, Protect, Steel Beam | fast | 2 | 6.2% |
- Other signatures: 54.0%

### Torkoal
- Teams: 26, weighted support: 6.2%, mega share: 0.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: weather-setter 100.0%, trick-room-abuser 100.0%, helping-hand 35.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Charcoal | Eruption, Helping Hand, Protect, Weather Ball | min-speed | 8 | 28.6% |
| Charcoal | Earth Power, Eruption, Heat Wave, Protect | min-speed | 4 | 18.8% |
| Charcoal | Eruption, Heat Wave, Protect, Weather Ball | min-speed | 5 | 17.5% |
| Charcoal | Earth Power, Eruption, Protect, Weather Ball | min-speed | 4 | 13.8% |
| Charcoal | Earth Power, Eruption, Heat Wave, Solar Beam | min-speed | 1 | 5.9% |
- Other signatures: 15.5%

### Baxcalibur
- Teams: 27, weighted support: 5.9%, mega share: 79.4%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: priority-attack 95.4%, mega-attacker 79.4%, setup 37.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Glaive Rush, Ice Shard, Icicle Crash, Protect | offensive | 4 | 16.4% |
| Baxcalibrite | Glaive Rush, Ice Shard, Protect, Swords Dance | offensive | 3 | 11.4% |
| Baxcalibrite | Glaive Rush, Ice Shard, Protect, Swords Dance | fast | 3 | 10.9% |
| Baxcalibrite | Glaive Rush, Ice Shard, Icicle Crash, Protect | offensive | 3 | 10.1% |
| Baxcalibrite | Glaive Rush, Ice Shard, Icicle Spear, Protect | fast | 2 | 6.9% |
- Other signatures: 44.4%

### Indeedee
- Teams: 23, weighted support: 5.4%, mega share: 0.0%
- Community: Community 4: Tyranitar / Excadrill / Milotic
- Roles: terrain-setter 100.0%, priority-blocker 100.0%, trick-room-setter 31.9%, spa-drop 29.9%, disruption 29.1%, helping-hand 23.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Expanding Force, Helping Hand, Protect, Trick Room | fast | 2 | 10.8% |
| Choice Scarf | Expanding Force, Imprison, Trick, Trick Room | unknown | 1 | 9.2% |
| Choice Scarf | Dazzling Gleam, Expanding Force, Mystical Fire, Trick | fast | 2 | 8.0% |
| Choice Scarf | Dazzling Gleam, Expanding Force, Psychic, Trick | fast | 2 | 7.5% |
| Twisted Spoon | Expanding Force, Helping Hand, Mystical Fire, Protect | fast | 1 | 4.8% |
- Other signatures: 59.8%

### Glimmora
- Teams: 19, weighted support: 5.3%, mega share: 61.1%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: mega-attacker 61.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Glimmoranite | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | fast | 10 | 53.6% |
| Focus Sash | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | fast | 7 | 34.7% |
| Glimmoranite | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | unknown | 1 | 7.4% |
| Expert Belt | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | fast | 1 | 4.2% |
- Other signatures: 0.0%

### Gengar
- Teams: 18, weighted support: 5.3%, mega share: 100.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: mega-attacker 84.7%, perish-song 80.2%, disruption 9.4%, status 4.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Gengarite | Perish Song, Protect, Shadow Ball, Sludge Bomb | bulky | 9 | 51.8% |
| Gengarite | Perish Song, Protect, Shadow Ball, Sludge Bomb | unknown | 2 | 13.1% |
| Gengarite | Disable, Perish Song, Protect, Shadow Ball | fast | 2 | 9.4% |
| Gengarite | Protect, Shadow Ball, Sludge Bomb, Substitute | fast | 2 | 8.2% |
| Gengarite | Focus Blast, Protect, Shadow Ball, Sludge Bomb | unknown | 1 | 7.5% |
- Other signatures: 10.1%

### Sinistcha
- Teams: 21, weighted support: 5.2%, mega share: 0.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: rage-powder 100.0%, trick-room-setter 84.7%, trick-room-abuser 26.3%, disruption 4.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | bulky | 4 | 19.7% |
| Colbur Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | bulky | 3 | 11.5% |
| Sitrus Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | min-speed | 2 | 9.7% |
| Colbur Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | unknown | 1 | 7.7% |
| Rocky Helmet | Matcha Gotcha, Rage Powder, Scald, Trick Room | bulky | 1 | 6.7% |
- Other signatures: 44.7%

### Metagross
- Teams: 20, weighted support: 5.0%, mega share: 100.0%
- Community: Community 5: Garchomp / Charizard / Metagross
- Roles: mega-attacker 100.0%, priority-attack 10.3%, setup 5.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Metagrossite | Body Press, Protect, Psychic Fangs, Steel Roller | fast | 4 | 22.7% |
| Metagrossite | Iron Head, Protect, Psychic Fangs, Stomping Tantrum | fast | 4 | 18.9% |
| Metagrossite | Body Press, Iron Head, Protect, Psychic Fangs | fast | 3 | 12.8% |
| Metagrossite | Body Press, Protect, Psychic Fangs, Steel Roller | unknown | 1 | 10.0% |
| Metagrossite | Protect, Psychic Fangs, Steel Roller, Stomping Tantrum | fast | 1 | 6.0% |
- Other signatures: 29.5%

### Volcarona
- Teams: 18, weighted support: 4.8%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: rage-powder 62.8%, spa-drop 50.2%, setup 37.2%, tailwind 35.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Rocky Helmet | Overheat, Protect, Rage Powder, Tailwind | bulky | 3 | 17.0% |
| Rocky Helmet | Overheat, Protect, Rage Powder, Struggle Bug | bulky | 2 | 14.1% |
| Grassy Seed | Giga Drain, Heat Wave, Protect, Quiver Dance | fast | 2 | 11.1% |
| Grassy Seed | Flamethrower, Giga Drain, Protect, Quiver Dance | fast | 2 | 10.5% |
| Rocky Helmet | Heat Wave, Protect, Rage Powder, Struggle Bug | bulky | 2 | 9.6% |
- Other signatures: 37.7%

### Staraptor
- Teams: 18, weighted support: 4.6%, mega share: 95.6%
- Community: Community 2: Gholdengo / Raichu / Arcanine-Hisui
- Roles: intimidate 100.0%, mega-attacker 95.6%, tailwind 73.5%, pivot 4.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Staraptite | Brave Bird, Close Combat, Protect, Tailwind | fast | 10 | 49.5% |
| Staraptite | Brave Bird, Close Combat, Protect, Tailwind | unknown | 2 | 16.1% |
| Staraptite | Brave Bird, Close Combat, Protect, Roost | fast | 2 | 9.2% |
| Staraptite | Brave Bird, Close Combat, Protect, Roost | unknown | 1 | 8.6% |
| Staraptite | Close Combat, Dual Wingbeat, Protect, Tailwind | fast | 1 | 8.0% |
- Other signatures: 8.7%

### Pawmot
- Teams: 19, weighted support: 4.6%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: fake-out 67.2%, ally-boost 13.9%, priority-attack 10.0%, pivot 5.1%, speed-drop 4.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Close Combat, Double Shock, Fake Out, Revival Blessing | fast | 5 | 31.0% |
| Focus Sash | Close Combat, Double Shock, Fake Out, Protect | fast | 1 | 7.6% |
| Leppa Berry | Close Combat, Double Shock, Protect, Revival Blessing | fast | 1 | 5.9% |
| Focus Sash | Close Combat, Coaching, Double Shock, Fake Out | fast | 1 | 5.1% |
| Focus Sash | Close Combat, Double Shock, Fake Out, Volt Switch | fast | 1 | 5.1% |
- Other signatures: 45.3%

### Froslass
- Teams: 14, weighted support: 4.6%, mega share: 100.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: weather-setter 100.0%, screens 94.0%, mega-attacker 92.3%, disruption 6.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Froslassite | Aurora Veil, Blizzard, Protect, Shadow Ball | fast | 10 | 68.2% |
| Froslassite | Aurora Veil, Blizzard, Protect, Shadow Ball | unknown | 1 | 10.9% |
| Froslassite | Aurora Veil, Blizzard, Protect, Rain Dance | bulky | 1 | 7.7% |
| Froslassite | Aurora Veil, Blizzard, Protect, Shadow Ball | offensive | 1 | 7.3% |
| Froslassite | Blizzard, Protect, Shadow Ball, Taunt | fast | 1 | 6.0% |
- Other signatures: 0.0%

### Whimsicott
- Teams: 15, weighted support: 4.1%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: tailwind 100.0%, prankster 100.0%, disruption 71.7%, weather-setter 14.1%, terrain-setter 12.2%, screens 10.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Encore, Moonblast, Protect, Tailwind | fast | 4 | 32.3% |
| Focus Sash | Encore, Moonblast, Sunny Day, Tailwind | fast | 2 | 14.1% |
| Focus Sash | Misty Terrain, Moonblast, Protect, Tailwind | fast | 1 | 12.2% |
| Focus Sash | Fake Tears, Moonblast, Protect, Tailwind | fast | 1 | 6.0% |
| Occa Berry | Charm, Light Screen, Moonblast, Tailwind | bulky | 1 | 5.5% |
- Other signatures: 29.9%

### Absol
- Teams: 14, weighted support: 3.7%, mega share: 100.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: mega-attacker 100.0%, status 34.6%, priority-attack 7.3%, speed-drop 6.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Absolite Z | Close Combat, Detect, Night Slash, Shadow Claw | fast | 3 | 18.6% |
| Absolite Z | Close Combat, Night Slash, Protect, Shadow Claw | fast | 2 | 14.6% |
| Absolite Z | Close Combat, Night Slash, Protect, Will-O-Wisp | fast | 1 | 12.7% |
| Absolite Z | Detect, Knock Off, Shadow Claw, Will-O-Wisp | fast | 1 | 8.0% |
| Absolite Z | Detect, Focus Energy, Night Slash, Superpower | fast | 1 | 7.6% |
- Other signatures: 38.5%

### Politoed
- Teams: 14, weighted support: 3.7%, mega share: 0.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: weather-setter 100.0%, perish-song 77.0%, disruption 58.6%, status 18.4%, trick-room-abuser 5.5%, helping-hand 5.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Encore, Perish Song, Protect, Weather Ball | bulky | 4 | 30.3% |
| Sitrus Berry | Encore, Perish Song, Protect, Weather Ball | unknown | 2 | 18.8% |
| Zoom Lens | Hypnosis, Perish Song, Protect, Weather Ball | bulky | 1 | 9.9% |
| Eject Button | Encore, Perish Song, Protect, Weather Ball | bulky | 1 | 9.4% |
| Sitrus Berry | Hypnosis, Perish Song, Protect, Weather Ball | bulky | 1 | 8.5% |
- Other signatures: 23.0%

### Swampert
- Teams: 12, weighted support: 3.4%, mega share: 100.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: mega-attacker 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Swampertite | Earthquake, Ice Punch, Protect, Wave Crash | fast | 5 | 42.5% |
| Swampertite | High Horsepower, Ice Punch, Protect, Wave Crash | offensive | 2 | 22.6% |
| Swampertite | Earthquake, Ice Punch, Protect, Wave Crash | offensive | 3 | 21.1% |
| Swampertite | High Horsepower, Ice Punch, Protect, Wave Crash | bulky | 1 | 7.2% |
| Swampertite | High Horsepower, Ice Punch, Protect, Wave Crash | fast | 1 | 6.6% |
- Other signatures: 0.0%

### Grimmsnarl
- Teams: 13, weighted support: 3.1%, mega share: 0.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: prankster 100.0%, screens 100.0%, spa-drop 93.5%, pivot 92.5%, trick-room-abuser 31.8%, fake-out 7.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Light Clay | Light Screen, Parting Shot, Reflect, Spirit Break | bulky | 6 | 54.2% |
| Light Clay | Light Screen, Parting Shot, Reflect, Spirit Break | min-speed | 4 | 26.5% |
| Light Clay | Fake Out, Light Screen, Reflect, Spirit Break | bulky | 1 | 7.5% |
| Light Clay | Foul Play, Light Screen, Parting Shot, Reflect | bulky | 1 | 6.5% |
| Light Clay | Light Screen, Parting Shot, Reflect, Spirit Break | unknown | 1 | 5.3% |
- Other signatures: 0.0%

### Dragonite
- Teams: 10, weighted support: 2.9%, mega share: 59.7%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: mega-attacker 59.7%, tailwind 50.4%, priority-attack 47.2%, setup 13.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Dragon Claw, Extreme Speed, Low Kick, Protect | offensive | 3 | 26.5% |
| Dragoninite | Dragon Pulse, Heat Wave, Protect, Tailwind | fast | 1 | 17.8% |
| Dragoninite | Dragon Pulse, Extreme Speed, Heat Wave, Tailwind | offensive | 1 | 13.9% |
| Dragoninite | Dragon Pulse, Heat Wave, Protect, Tailwind | offensive | 1 | 11.9% |
| Dragoninite | Dragon Pulse, Heat Wave, Hurricane, Protect | fast | 1 | 9.2% |
- Other signatures: 20.7%

### Aerodactyl
- Teams: 11, weighted support: 2.9%, mega share: 41.1%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: tailwind 100.0%, mega-attacker 41.1%, wide-guard 30.7%, disruption 10.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Dual Wingbeat, Protect, Rock Slide, Tailwind | fast | 3 | 29.2% |
| Aerodactylite | Dual Wingbeat, Protect, Rock Slide, Tailwind | fast | 2 | 13.8% |
| Aerodactylite | Dual Wingbeat, Rock Slide, Tailwind, Wide Guard | unknown | 1 | 13.6% |
| Focus Sash | Protect, Rock Slide, Tailwind, Taunt | fast | 1 | 10.2% |
| Focus Sash | Protect, Rock Slide, Tailwind, Wide Guard | fast | 1 | 10.2% |
- Other signatures: 23.0%

### Delphox
- Teams: 9, weighted support: 2.7%, mega share: 67.7%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: mega-attacker 67.7%, setup 53.2%, terrain-setter 7.8%, priority-blocker 7.8%, disruption 7.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Delphoxite | Heat Wave, Nasty Plot, Protect, Psychic | unknown | 2 | 32.9% |
| Life Orb | Expanding Force, Focus Blast, Heat Wave, Protect | fast | 1 | 13.5% |
| Delphoxite | Heat Wave, Nasty Plot, Protect, Psychic | fast | 1 | 12.8% |
| Life Orb | Expanding Force, Heat Wave, Protect, Substitute | fast | 1 | 11.0% |
| Life Orb | Expanding Force, Flamethrower, Protect, Psychic Terrain | fast | 1 | 7.8% |
- Other signatures: 21.9%

### Hatterene
- Teams: 11, weighted support: 2.7%, mega share: 0.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: trick-room-setter 100.0%, trick-room-abuser 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Dazzling Gleam, Expanding Force, Protect, Trick Room | min-speed | 8 | 66.0% |
| Life Orb | Dazzling Gleam, Expanding Force, Giga Drain, Trick Room | min-speed | 1 | 13.7% |
| Life Orb | Dazzling Gleam, Expanding Force, Psychic, Trick Room | min-speed | 1 | 10.2% |
| Life Orb | Dazzling Gleam, Expanding Force, Magic Powder, Trick Room | min-speed | 1 | 10.2% |
- Other signatures: 0.0%

### Ninetales-Alola
- Teams: 11, weighted support: 2.6%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: weather-setter 100.0%, screens 58.5%, disruption 33.9%, speed-drop 22.6%, ally-boost 7.6%, helping-hand 7.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Light Clay | Aurora Veil, Blizzard, Moonblast, Protect | fast | 4 | 32.5% |
| Choice Scarf | Blizzard, Encore, Freeze-Dry, Icy Wind | fast | 1 | 15.0% |
| Never-Melt Ice | Blizzard, Encore, Freeze-Dry, Protect | fast | 1 | 11.3% |
| Choice Scarf | Aurora Veil, Blizzard, Freeze-Dry, Psyshock | fast | 1 | 10.8% |
| Light Ball | Aurora Veil, Blizzard, Moonblast, Protect | fast | 1 | 8.0% |
- Other signatures: 22.5%

### Kommo-o
- Teams: 8, weighted support: 2.4%, mega share: 0.0%
- Community: Community 3: Golisopod / Archaludon / Pelipper
- Roles: setup 82.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | fast | 4 | 50.9% |
| Life Orb | Aura Sphere, Clanging Scales, Flamethrower, Protect | fast | 1 | 17.7% |
| Leftovers | Clanging Scales, Clangorous Soul, Flamethrower, Protect | fast | 1 | 14.7% |
| Grassy Seed | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | fast | 1 | 8.5% |
| Leftovers | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | mixed | 1 | 8.1% |
- Other signatures: 0.0%

### Blaziken
- Teams: 9, weighted support: 2.3%, mega share: 54.9%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: mega-attacker 54.9%, ally-boost 23.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Blazikenite | Close Combat, Detect, Flare Blitz, Rock Slide | fast | 3 | 37.3% |
| Focus Sash | Aura Sphere, Coaching, Detect, Heat Wave | fast | 2 | 23.7% |
| Blazikenite | Close Combat, Flare Blitz, Protect, Rock Slide | fast | 2 | 17.6% |
| Expert Belt | Close Combat, Detect, Flare Blitz, Thunder Punch | offensive | 1 | 12.8% |
| Life Orb | Close Combat, Detect, Flare Blitz, Rock Slide | fast | 1 | 8.6% |
- Other signatures: 0.0%

### Venusaur
- Teams: 9, weighted support: 2.2%, mega share: 0.0%
- Community: Community 5: Garchomp / Charizard / Metagross
- Roles: status 81.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Leaf Storm, Protect, Sleep Powder, Sludge Bomb | fast | 3 | 34.3% |
| Wide Lens | Leaf Storm, Protect, Sleep Powder, Sludge Bomb | fast | 1 | 15.6% |
| Focus Sash | Energy Ball, Protect, Sleep Powder, Sludge Bomb | fast | 1 | 12.2% |
| Wide Lens | Earth Power, Leaf Storm, Protect, Sleep Powder | fast | 1 | 10.0% |
| Focus Sash | Earth Power, Leaf Storm, Sleep Powder, Sludge Bomb | fast | 1 | 9.5% |
- Other signatures: 18.5%

### Primarina
- Teams: 6, weighted support: 2.2%, mega share: 0.0%
- Community: Community 0: Rillaboom / Sneasler / Salamence
- Roles: setup 71.0%, helping-hand 9.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Grassy Seed | Calm Mind, Hyper Voice, Moonblast, Protect | offensive | 1 | 22.9% |
| Grassy Seed | Calm Mind, Hyper Voice, Moonblast, Protect | bulky | 1 | 22.9% |
| Life Orb | Dazzling Gleam, Hyper Voice, Moonblast, Protect | mixed | 1 | 19.2% |
| Leftovers | Calm Mind, Hyper Voice, Moonblast, Protect | bulky | 1 | 16.0% |
| Leftovers | Helping Hand, Hyper Voice, Moonblast, Protect | offensive | 1 | 9.8% |
- Other signatures: 9.3%

### Blastoise
- Teams: 9, weighted support: 2.2%, mega share: 86.8%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: mega-attacker 86.8%, setup 56.8%, fake-out 32.3%, status 13.2%, pivot 13.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Blastoisinite | Protect, Shell Smash, Terrain Pulse, Water Spout | offensive | 4 | 44.3% |
| Leftovers | Fake Out, Flip Turn, Ice Beam, Yawn | offensive | 1 | 13.2% |
| Blastoisinite | Ice Beam, Protect, Shell Smash, Water Spout | fast | 1 | 12.6% |
| Blastoisinite | Dark Pulse, Ice Beam, Protect, Waterfall | offensive | 1 | 10.8% |
| Blastoisinite | Fake Out, Protect, Terrain Pulse, Water Spout | min-speed | 1 | 9.8% |
- Other signatures: 9.3%

### Dragapult
- Teams: 8, weighted support: 1.9%, mega share: 0.0%
- Community: Community 4: Tyranitar / Excadrill / Milotic
- Roles: status 100.0%, disruption 10.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Draco Meteor, Protect, Shadow Ball, Will-O-Wisp | fast | 4 | 48.0% |
| Life Orb | Draco Meteor, Protect, Shadow Ball, Will-O-Wisp | unknown | 2 | 27.5% |
| Focus Sash | Draco Meteor, Dragon Cheer, Shadow Ball, Will-O-Wisp | fast | 1 | 14.1% |
| Focus Sash | Disable, Protect, Shadow Ball, Will-O-Wisp | fast | 1 | 10.5% |
- Other signatures: 0.0%

### Talonflame
- Teams: 8, weighted support: 1.8%, mega share: 0.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: tailwind 100.0%, gale-wings 100.0%, quick-guard 13.5%, priority-blocker 13.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Charcoal | Air Slash, Overheat, Protect, Tailwind | fast | 2 | 23.3% |
| Sharp Beak | Brave Bird, Feint, Flare Blitz, Tailwind | fast | 1 | 16.6% |
| Life Orb | Brave Bird, Flare Blitz, Protect, Tailwind | fast | 1 | 13.5% |
| Sharp Beak | Dual Wingbeat, Flare Blitz, Quick Guard, Tailwind | fast | 1 | 13.5% |
| Life Orb | Dual Wingbeat, Flare Blitz, Protect, Tailwind | fast | 1 | 11.1% |
- Other signatures: 22.1%

### Ceruledge
- Teams: 7, weighted support: 1.6%, mega share: 0.0%
- Community: Community 4: Tyranitar / Excadrill / Milotic
- Roles: setup 100.0%, priority-attack 74.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Colbur Berry | Bitter Blade, Bulk Up, Protect, Shadow Sneak | bulky | 1 | 17.4% |
| Grassy Seed | Bitter Blade, Protect, Shadow Sneak, Swords Dance | bulky | 1 | 16.2% |
| Grassy Seed | Bitter Blade, Protect, Shadow Sneak, Swords Dance | unknown | 1 | 15.4% |
| Grassy Seed | Bitter Blade, Bulk Up, Protect, Shadow Sneak | offensive | 1 | 13.2% |
| Focus Sash | Bitter Blade, Close Combat, Poltergeist, Swords Dance | fast | 1 | 13.0% |
- Other signatures: 24.9%

### Corviknight
- Teams: 7, weighted support: 1.5%, mega share: 0.0%
- Community: Community 4: Tyranitar / Excadrill / Milotic
- Roles: setup 100.0%, tailwind 28.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Psychic Seed | Brave Bird, Bulk Up, Power Trip, Roost | bulky | 2 | 25.5% |
| Psychic Seed | Brave Bird, Bulk Up, Power Trip, Roost | unknown | 1 | 16.8% |
| Sitrus Berry | Body Press, Brave Bird, Bulk Up, Tailwind | bulky | 1 | 15.9% |
| Leftovers | Body Press, Bulk Up, Iron Head, Roost | bulky | 1 | 15.1% |
| Psychic Seed | Body Press, Iron Defense, Iron Head, Roost | bulky | 1 | 13.7% |
- Other signatures: 13.0%

### Annihilape
- Teams: 5, weighted support: 1.4%, mega share: 0.0%
- Community: none
- Roles: setup 30.0%, pivot 23.1%, weather-setter 18.8%, ally-boost 18.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Close Combat, Ice Punch, Seed Bomb, Shadow Claw | fast | 1 | 28.1% |
| Choice Scarf | Close Combat, Ice Punch, Shadow Claw, U-turn | fast | 1 | 23.1% |
| Choice Scarf | Close Combat, Coaching, Rain Dance, Shadow Claw | fast | 1 | 18.8% |
| Leftovers | Bulk Up, Drain Punch, Protect, Rage Fist | bulky | 1 | 15.4% |
| Sitrus Berry | Bulk Up, Drain Punch, Protect, Rage Fist | bulky | 1 | 14.6% |
- Other signatures: 0.0%

### Sirfetch’d
- Teams: 5, weighted support: 1.4%, mega share: 0.0%
- Community: none
- Roles: trick-room-abuser 26.4%, quick-guard 19.0%, priority-blocker 19.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Expert Belt | Close Combat, Detect, Leaf Blade, Meteor Assault | min-speed | 1 | 26.4% |
| Leek | Close Combat, Detect, Feint, Meteor Assault | offensive | 1 | 25.8% |
| Leek | Close Combat, Detect, Meteor Assault, Quick Guard | fast | 1 | 19.0% |
| Life Orb | Close Combat, Detect, First Impression, Meteor Assault | fast | 1 | 14.8% |
| Leek | Close Combat, Detect, Meteor Assault, Poison Jab | offensive | 1 | 14.1% |
- Other signatures: 0.0%

### Mawile
- Teams: 6, weighted support: 1.4%, mega share: 100.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: trick-room-abuser 100.0%, mega-attacker 100.0%, priority-attack 81.0%, intimidate 14.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Mawilite | Iron Head, Play Rough, Protect, Sucker Punch | min-speed | 4 | 66.8% |
| Mawilite | Iron Head, Play Rough, Protect, Rock Slide | min-speed | 1 | 19.0% |
| Mawilite | Play Rough, Protect, Rock Slide, Sucker Punch | min-speed | 1 | 14.1% |
- Other signatures: 0.0%

### Hydreigon
- Teams: 6, weighted support: 1.4%, mega share: 0.0%
- Community: none
- Roles: spa-drop 86.4%, pivot 25.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Dark Pulse, Draco Meteor, Heat Wave, Snarl | fast | 2 | 33.3% |
| Focus Sash | Dark Pulse, Draco Meteor, Snarl, U-turn | fast | 1 | 25.9% |
| Choice Scarf | Dark Pulse, Draco Meteor, Earth Power, Snarl | fast | 1 | 13.6% |
| Choice Scarf | Dark Pulse, Draco Meteor, Flamethrower, Flash Cannon | fast | 1 | 13.6% |
| Haban Berry | Dark Pulse, Draco Meteor, Protect, Snarl | fast | 1 | 13.6% |
- Other signatures: 0.0%

### Camerupt
- Teams: 4, weighted support: 1.2%, mega share: 100.0%
- Community: none
- Roles: trick-room-abuser 100.0%, mega-attacker 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Cameruptite | Ancient Power, Earth Power, Heat Wave, Protect | min-speed | 4 | 100.0% |
- Other signatures: 0.0%

### Toxtricity
- Teams: 4, weighted support: 0.9%, mega share: 0.0%
- Community: none
- Roles: spa-drop 21.3%, pivot 21.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Boomburst, Overdrive, Protect, Sludge Bomb | offensive | 2 | 57.5% |
| Magnet | Overdrive, Protect, Sludge Bomb, Thunderbolt | offensive | 1 | 21.3% |
| Choice Scarf | Overdrive, Sludge Bomb, Snarl, Volt Switch | fast | 1 | 21.3% |
- Other signatures: 0.0%

### Scrafty
- Teams: 4, weighted support: 0.9%, mega share: 21.4%
- Community: none
- Roles: fake-out 100.0%, intimidate 100.0%, trick-room-abuser 21.4%, mega-attacker 21.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Close Combat, Fake Out, Knock Off, Protect | fast | 1 | 33.7% |
| Sharp Beak | Detect, Drain Punch, Fake Out, Knock Off | offensive | 1 | 23.6% |
| Focus Sash | Close Combat, Fake Out, Knock Off, Protect | bulky | 1 | 21.4% |
| Scraftinite | Close Combat, Fake Out, Knock Off, Rock Slide | min-speed | 1 | 21.4% |
- Other signatures: 0.0%

### Meowstic
- Teams: 4, weighted support: 0.9%, mega share: 100.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: mega-attacker 100.0%, fake-out 53.9%, setup 22.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Meowsticite | Alluring Voice, Expanding Force, Fake Out, Protect | fast | 2 | 53.9% |
| Meowsticite | Alluring Voice, Expanding Force, Protect, Shadow Ball | fast | 1 | 23.6% |
| Meowsticite | Alluring Voice, Expanding Force, Nasty Plot, Protect | fast | 1 | 22.5% |
- Other signatures: 0.0%

### Pyroar
- Teams: 3, weighted support: 0.9%, mega share: 100.0%
- Community: none
- Roles: mega-attacker 100.0%, spa-drop 53.0%, status 53.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Pyroarite | Heat Wave, Protect, Snarl, Yawn | fast | 2 | 53.0% |
| Pyroarite | Heat Wave, Overheat, Protect, Scorching Sands | fast | 1 | 47.0% |
- Other signatures: 0.0%

### Maushold
- Teams: 3, weighted support: 0.9%, mega share: 0.0%
- Community: none
- Roles: follow-me 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Wide Lens | Bite, Follow Me, Population Bomb, Protect | fast | 1 | 45.9% |
| Chople Berry | Feint, Follow Me, Protect, Super Fang | fast | 1 | 27.7% |
| Wide Lens | Beat Up, Follow Me, Population Bomb, Protect | fast | 1 | 26.4% |
- Other signatures: 0.0%

### Klefki
- Teams: 3, weighted support: 0.9%, mega share: 0.0%
- Community: none
- Roles: prankster 100.0%, weather-setter 69.9%, screens 69.9%, setup 30.1%, trick-room-setter 23.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Light Clay | Dazzling Gleam, Light Screen, Rain Dance, Reflect | unknown | 1 | 46.3% |
| Sitrus Berry | Calm Mind, Draining Kiss, Psych Up, Substitute | bulky | 1 | 30.1% |
| Focus Sash | Dazzling Gleam, Light Screen, Rain Dance, Trick Room | offensive | 1 | 23.5% |
- Other signatures: 0.0%

### Gallade
- Teams: 4, weighted support: 0.8%, mega share: 0.0%
- Community: Community 1: Indeedee-F / Armarouge / Gardevoir
- Roles: wide-guard 100.0%, trick-room-setter 70.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Poison Jab, Psycho Cut, Sacred Sword, Wide Guard | fast | 1 | 29.2% |
| Sitrus Berry | Psycho Cut, Sacred Sword, Trick Room, Wide Guard | min-speed | 1 | 24.0% |
| Life Orb | Psycho Cut, Sacred Sword, Trick Room, Wide Guard | min-speed | 1 | 24.0% |
| White Herb | Psycho Cut, Sacred Sword, Trick Room, Wide Guard | offensive | 1 | 22.8% |
- Other signatures: 0.0%

### Vanilluxe
- Teams: 3, weighted support: 0.8%, mega share: 0.0%
- Community: none
- Roles: weather-setter 100.0%, speed-drop 70.4%, screens 34.3%, priority-attack 29.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Blizzard, Freeze-Dry, Icy Wind, Sheer Cold | fast | 1 | 36.1% |
| Choice Scarf | Aurora Veil, Blizzard, Freeze-Dry, Icy Wind | fast | 1 | 34.3% |
| Never-Melt Ice | Blizzard, Freeze-Dry, Ice Shard, Protect | offensive | 1 | 29.6% |
- Other signatures: 0.0%

### Aegislash
- Teams: 3, weighted support: 0.8%, mega share: 0.0%
- Community: none
- Roles: wide-guard 100.0%, priority-attack 27.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Kasib Berry | Iron Head, Poltergeist, Sacred Sword, Wide Guard | fast | 1 | 40.8% |
| Focus Sash | Close Combat, Iron Head, Poltergeist, Wide Guard | fast | 1 | 31.8% |
| Focus Sash | Close Combat, Poltergeist, Shadow Sneak, Wide Guard | fast | 1 | 27.4% |
- Other signatures: 0.0%

### Tsareena
- Teams: 3, weighted support: 0.7%, mega share: 0.0%
- Community: none
- Roles: priority-blocker 100.0%, helping-hand 63.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Wide Lens | Helping Hand, Low Kick, Power Whip, Triple Axel | fast | 2 | 63.3% |
| Focus Sash | Low Kick, Power Whip, Protect, Triple Axel | fast | 1 | 36.7% |
- Other signatures: 0.0%

### Alakazam
- Teams: 3, weighted support: 0.7%, mega share: 100.0%
- Community: none
- Roles: mega-attacker 100.0%, setup 61.1%, disruption 38.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Alakazite | Encore, Expanding Force, Focus Blast, Protect | fast | 1 | 38.9% |
| Alakazite | Calm Mind, Expanding Force, Focus Blast, Protect | fast | 1 | 33.6% |
| Alakazite | Calm Mind, Dazzling Gleam, Expanding Force, Protect | fast | 1 | 27.5% |
- Other signatures: 0.0%

### Altaria
- Teams: 4, weighted support: 0.7%, mega share: 0.0%
- Community: none
- Roles: status 100.0%, tailwind 57.8%, perish-song 42.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Fire Spin, Perish Song, Protect, Will-O-Wisp | bulky | 1 | 42.2% |
| Haban Berry | Ice Beam, Protect, Tailwind, Will-O-Wisp | fast | 2 | 39.2% |
| Haban Berry | Ice Beam, Protect, Tailwind, Will-O-Wisp | unknown | 1 | 18.6% |
- Other signatures: 0.0%

### Pincurchin
- Teams: 3, weighted support: 0.7%, mega share: 0.0%
- Community: none
- Roles: terrain-setter 100.0%, trick-room-abuser 100.0%, priority-attack 68.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Electric Seed | Liquidation, Poison Jab, Sucker Punch, Zing Zap | min-speed | 1 | 34.4% |
| Life Orb | Acupressure, Protect, Rising Voltage, Sucker Punch | min-speed | 1 | 34.4% |
| Focus Sash | Acupressure, Protect, Rising Voltage, Scald | min-speed | 1 | 31.2% |
- Other signatures: 0.0%

### Lycanroc-Dusk
- Teams: 3, weighted support: 0.7%, mega share: 0.0%
- Community: none
- Roles: priority-attack 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Accelerock, Close Combat, Protect, Rock Slide | fast | 3 | 100.0% |
- Other signatures: 0.0%

### Zoroark-Hisui
- Teams: 3, weighted support: 0.7%, mega share: 0.0%
- Community: none
- Roles: speed-drop 64.5%, disruption 30.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Flamethrower, Psychic, Shadow Ball, Trick | fast | 1 | 35.5% |
| Focus Sash | Bitter Malice, Fake Tears, Icy Wind, Memento | fast | 1 | 33.8% |
| Focus Sash | Flamethrower, Grass Knot, Icy Wind, Taunt | fast | 1 | 30.6% |
- Other signatures: 0.0%

### Typhlosion-Hisui
- Teams: 3, weighted support: 0.6%, mega share: 0.0%
- Community: none
- Roles: none
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Eruption, Heat Wave, Overheat, Shadow Ball | fast | 2 | 68.5% |
| Choice Scarf | Eruption, Focus Blast, Heat Wave, Shadow Ball | fast | 1 | 31.5% |
- Other signatures: 0.0%

### Scizor
- Teams: 3, weighted support: 0.6%, mega share: 66.7%
- Community: none
- Roles: priority-attack 100.0%, setup 66.7%, mega-attacker 66.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Scizorite | Bug Bite, Bullet Punch, Protect, Swords Dance | offensive | 2 | 66.7% |
| Life Orb | Assurance, Bug Bite, Bullet Punch, Protect | offensive | 1 | 33.3% |
- Other signatures: 0.0%

## 11. Conditional Set Table
### Rillaboom
| Partner | Teams | Support | Lift | Ladder teammate rank | Rillaboom given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Incineroar | 105 | 26.8% | 1.325 | 1 (Rillaboom in Incineroar's) | items: Miracle Seed 66.2%, Occa Berry 10.6%, Eject Button 10.2%, Life Orb 4.8%, Sitrus Berry 4.7%, Grassy Seed 1.6%, Iron Ball 1.2%, Leftovers 0.8%; spreads: bulky 57.9%, offensive 24.3%, min-speed 8.6%, fast 4.8%, unknown 4.4% |
| Salamence | 107 | 26.7% | 1.195 | 2 (Salamence in Rillaboom's) | items: Miracle Seed 67.6%, Sitrus Berry 11.4%, Life Orb 7.9%, Expert Belt 5.2%, Occa Berry 3.9%, Leftovers 1.6%, Rocky Helmet 0.8%, Grassy Seed 0.8%, Focus Sash 0.8%; spreads: offensive 50.3%, bulky 40.5%, fast 7.3%, min-speed 1.9% |
| Sneasler | 107 | 26.2% | 0.982 | 1 (Sneasler in Rillaboom's) | items: Miracle Seed 71.2%, Sitrus Berry 11.9%, Occa Berry 8.4%, Life Orb 6.9%, Grassy Seed 0.8%, Focus Sash 0.8%; spreads: bulky 52.9%, offensive 37.2%, fast 5.3%, unknown 3.8%, min-speed 0.9% |
| Gholdengo | 66 | 18.4% | 1.350 | 1 (Rillaboom in Gholdengo's) | items: Miracle Seed 81.6%, Occa Berry 6.8%, Sitrus Berry 5.7%, Expert Belt 3.4%, Life Orb 1.3%, Leftovers 1.3%; spreads: bulky 51.4%, offensive 39.1%, fast 5.5%, unknown 4.0%; moves: High Horsepower +18pp, U-turn -17pp |
| Kingambit | 71 | 18.1% | 1.106 | 2 (Rillaboom in Kingambit's) | items: Miracle Seed 58.7%, Life Orb 17.0%, Sitrus Berry 15.2%, Occa Berry 5.8%, Eject Button 2.2%, Focus Sash 1.1%; spreads: bulky 44.6%, offensive 42.7%, unknown 5.5%, fast 4.4%, min-speed 2.8% |

### Sneasler
| Partner | Teams | Support | Lift | Ladder teammate rank | Sneasler given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 107 | 26.2% | 0.982 | 1 (Sneasler in Rillaboom's) | items: Grassy Seed 55.8%, White Herb 28.7%, Focus Sash 15.5%; spreads: fast 72.9%, bulky 10.2%, offensive 7.8%, mixed 5.3%, unknown 3.8% |
| Salamence | 92 | 21.8% | 1.171 | 2 (Sneasler in Salamence's) | items: Grassy Seed 34.3%, White Herb 34.1%, Psychic Seed 18.2%, Focus Sash 13.4%; spreads: fast 70.5%, bulky 12.1%, mixed 9.0%, unknown 5.2%, offensive 3.1% |
| Incineroar | 70 | 17.5% | 1.037 | 2 (Sneasler in Incineroar's) | items: Grassy Seed 45.4%, White Herb 27.2%, Focus Sash 18.7%, Psychic Seed 8.6%; spreads: fast 72.2%, bulky 7.6%, offensive 7.6%, unknown 7.4%, mixed 5.1% |
| Kingambit | 70 | 17.0% | 1.250 | 1 (Sneasler in Kingambit's) | items: Grassy Seed 35.0%, White Herb 28.5%, Focus Sash 20.2%, Psychic Seed 16.3%; spreads: fast 79.6%, unknown 8.1%, bulky 7.0%, offensive 3.7%, mixed 1.6%; moves: Fake Out +15pp |
| Indeedee-F | 53 | 12.7% | 1.117 | 1 (Sneasler in Indeedee-F's) | items: Psychic Seed 66.6%, White Herb 23.1%, Grassy Seed 6.1%, Focus Sash 4.2%; spreads: fast 74.8%, bulky 11.9%, offensive 7.1%, unknown 4.7%, mixed 1.6%; moves: Fake Out -24pp |

### Salamence
| Partner | Teams | Support | Lift | Ladder teammate rank | Salamence given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 107 | 26.7% | 1.195 | 2 (Salamence in Rillaboom's) | items: Salamencite 100.0%; spreads: fast 97.4%, mixed 1.9%, bulky 0.7% |
| Sneasler | 92 | 21.8% | 1.171 | 2 (Sneasler in Salamence's) | items: Salamencite 100.0%; spreads: fast 92.9%, unknown 5.2%, mixed 1.0%, bulky 0.9% |
| Kingambit | 65 | 15.9% | 1.390 | 3 (Salamence in Kingambit's) | items: Salamencite 100.0%; spreads: fast 95.3%, mixed 3.2%, bulky 1.5% |
| Gholdengo | 53 | 14.2% | 1.492 | 2 (Salamence in Gholdengo's) | items: Salamencite 100.0%; spreads: fast 95.8%, unknown 2.8%, bulky 1.4% |
| Incineroar | 48 | 11.3% | 0.802 | 3 (Salamence in Incineroar's) | items: Salamencite 100.0%; spreads: fast 92.5%, bulky 3.9%, unknown 3.5%; moves: Flamethrower -15pp |

### Incineroar
| Partner | Teams | Support | Lift | Ladder teammate rank | Incineroar given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 105 | 26.8% | 1.325 | 1 (Rillaboom in Incineroar's) | items: Sitrus Berry 69.7%, Chople Berry 11.8%, Passho Berry 9.6%, Rocky Helmet 3.4%, Leftovers 3.1%, Expert Belt 0.9%, Grassy Seed 0.8%, Bright Powder 0.7%; spreads: bulky 75.1%, min-speed 13.0%, fast 4.5%, unknown 4.4%, offensive 3.0% |
| Sneasler | 70 | 17.5% | 1.037 | 2 (Sneasler in Incineroar's) | items: Sitrus Berry 79.5%, Chople Berry 9.4%, Passho Berry 4.8%, Rocky Helmet 3.7%, Leftovers 2.5%; spreads: bulky 74.7%, unknown 7.4%, fast 6.9%, min-speed 6.4%, offensive 2.6%, mixed 2.0% |
| Floette-Eternal | 52 | 12.7% | 2.128 | 3 (Incineroar in Floette-Eternal's) | items: Sitrus Berry 70.8%, Chople Berry 12.6%, Passho Berry 6.7%, Rocky Helmet 5.2%, Leftovers 3.2%, Bright Powder 1.5%; spreads: bulky 82.8%, unknown 7.1%, fast 5.9%, mixed 2.7%, min-speed 1.5% |
| Salamence | 48 | 11.3% | 0.802 | 3 (Salamence in Incineroar's) | items: Sitrus Berry 89.1%, Rocky Helmet 4.0%, Chople Berry 1.8%, Grassy Seed 1.8%, Bright Powder 1.7%, Leftovers 1.7%; spreads: bulky 81.7%, min-speed 7.8%, offensive 5.2%, unknown 3.5%, fast 1.8% |
| Gholdengo | 37 | 10.6% | 1.234 | 4 (Incineroar in Gholdengo's) | items: Sitrus Berry 86.3%, Chople Berry 9.4%, Rocky Helmet 4.2%; spreads: bulky 86.5%, min-speed 4.2%, unknown 3.7%, fast 3.3%, offensive 2.3% |

### Kingambit
| Partner | Teams | Support | Lift | Ladder teammate rank | Kingambit given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 71 | 18.1% | 1.106 | 2 (Rillaboom in Kingambit's) | items: Chople Berry 37.6%, Life Orb 24.4%, Focus Sash 23.5%, Black Glasses 12.0%, Occa Berry 2.5%; spreads: offensive 49.1%, fast 24.9%, bulky 16.6%, unknown 5.5%, min-speed 2.7%, mixed 1.2% |
| Sneasler | 70 | 17.0% | 1.250 | 1 (Sneasler in Kingambit's) | items: Chople Berry 42.4%, Life Orb 25.6%, Focus Sash 21.8%, Black Glasses 10.2%; spreads: offensive 45.2%, fast 22.7%, bulky 20.4%, unknown 8.1%, min-speed 3.5% |
| Salamence | 65 | 15.9% | 1.390 | 3 (Salamence in Kingambit's) | items: Chople Berry 51.5%, Focus Sash 22.0%, Life Orb 13.9%, Black Glasses 10.7%, Occa Berry 1.9%; spreads: offensive 42.3%, fast 31.5%, bulky 21.8%, min-speed 3.1%, mixed 1.4% |
| Basculegion | 33 | 7.9% | 1.270 | 3 (Kingambit in Basculegion's) | items: Chople Berry 53.8%, Focus Sash 19.7%, Life Orb 11.0%, Occa Berry 9.3%, Black Glasses 6.2%; spreads: offensive 46.4%, bulky 28.3%, fast 22.5%, mixed 2.8%; moves: Protect -16pp |
| Floette-Eternal | 33 | 7.7% | 1.605 | 5 (Kingambit in Floette-Eternal's) | items: Chople Berry 37.7%, Life Orb 29.0%, Focus Sash 27.8%, Black Glasses 5.5%; spreads: offensive 32.1%, bulky 28.5%, fast 27.8%, unknown 11.5% |

### Indeedee-F
| Partner | Teams | Support | Lift | Ladder teammate rank | Indeedee-F given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sneasler | 53 | 12.7% | 1.117 | 1 (Sneasler in Indeedee-F's) | items: Rocky Helmet 43.2%, Colbur Berry 35.0%, Psychic Seed 9.7%, Sitrus Berry 8.7%, Eject Button 1.8%, Wide Lens 1.5%; spreads: min-speed 48.6%, bulky 46.7%, unknown 4.7% |
| Armarouge | 35 | 8.7% | 3.860 | 1 (Indeedee-F in Armarouge's) | items: Rocky Helmet 39.0%, Colbur Berry 28.4%, Psychic Seed 19.7%, Sitrus Berry 7.9%, Eject Button 2.7%, Tanga Berry 2.3%; spreads: min-speed 51.4%, bulky 44.1%, unknown 4.6% |
| Gardevoir | 34 | 7.7% | 4.134 | 1 (Indeedee-F in Gardevoir's) | items: Rocky Helmet 45.5%, Colbur Berry 36.0%, Sitrus Berry 8.1%, Psychic Seed 5.3%, Chesto Berry 2.6%, Wide Lens 2.5%; spreads: min-speed 50.2%, bulky 42.1%, unknown 7.7%; moves: Psychic -19pp |
| Golisopod | 21 | 5.3% | 1.352 | 3 (Indeedee-F in Golisopod's) | items: Colbur Berry 33.5%, Psychic Seed 32.5%, Rocky Helmet 28.9%, Sitrus Berry 5.1%; spreads: bulky 51.9%, min-speed 48.1%; moves: Protect -19pp, Trick Room +17pp, Psychic +16pp |
| Basculegion | 22 | 5.2% | 0.992 | 5 (Indeedee-F in Basculegion's) | items: Rocky Helmet 65.0%, Colbur Berry 21.7%, Psychic Seed 13.2%; spreads: bulky 57.3%, min-speed 42.7% |

### Gholdengo
| Partner | Teams | Support | Lift | Ladder teammate rank | Gholdengo given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 66 | 18.4% | 1.350 | 1 (Rillaboom in Gholdengo's) | items: Life Orb 90.0%, Grassy Seed 5.0%, Choice Scarf 2.6%, Leftovers 2.4%; spreads: fast 76.4%, bulky 13.8%, offensive 5.7%, unknown 4.0% |
| Salamence | 53 | 14.2% | 1.492 | 2 (Salamence in Gholdengo's) | items: Life Orb 86.4%, Leftovers 5.8%, Grassy Seed 4.7%, Focus Sash 1.7%, White Herb 1.4%; spreads: fast 73.7%, bulky 14.7%, offensive 7.4%, unknown 2.8%, mixed 1.4% |
| Incineroar | 37 | 10.6% | 1.234 | 4 (Incineroar in Gholdengo's) | items: Life Orb 82.0%, White Herb 5.2%, Choice Scarf 4.4%, Leftovers 4.2%, Focus Sash 2.3%, Grassy Seed 1.9%; spreads: fast 81.2%, bulky 13.2%, unknown 3.7%, offensive 1.9% |
| Sneasler | 37 | 9.7% | 0.851 | 3 (Sneasler in Gholdengo's) | items: Life Orb 85.7%, Leftovers 4.6%, Psychic Seed 2.7%, Focus Sash 2.5%, Grassy Seed 2.4%, Choice Scarf 2.1%; spreads: fast 82.2%, offensive 7.9%, bulky 5.8%, unknown 4.1% |
| Raichu | 31 | 9.0% | 3.196 | 5 (Raichu in Gholdengo's) | items: Life Orb 92.0%, Choice Scarf 5.2%, Grassy Seed 2.7%; spreads: fast 79.3%, bulky 12.5%, unknown 8.3% |

### Basculegion
| Partner | Teams | Support | Lift | Ladder teammate rank | Basculegion given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 52 | 12.1% | 0.997 | 2 (Rillaboom in Basculegion's) | items: Life Orb 54.1%, Choice Scarf 27.9%, Focus Sash 8.7%, Mystic Water 7.6%, Sitrus Berry 1.8%; spreads: fast 81.3%, offensive 12.2%, bulky 4.7%, mixed 1.8% |
| Sneasler | 49 | 11.3% | 1.119 | 1 (Sneasler in Basculegion's) | items: Life Orb 48.1%, Choice Scarf 32.6%, Focus Sash 10.7%, Mystic Water 6.2%, Sitrus Berry 2.4%; spreads: fast 82.9%, bulky 7.6%, offensive 7.3%, mixed 2.2% |
| Salamence | 42 | 9.9% | 1.166 | 4 (Salamence in Basculegion's) | items: Life Orb 50.0%, Choice Scarf 30.3%, Focus Sash 10.2%, Mystic Water 7.2%, Sitrus Berry 2.2%; spreads: fast 77.0%, offensive 10.4%, bulky 7.8%, mixed 4.7% |
| Kingambit | 33 | 7.9% | 1.270 | 3 (Kingambit in Basculegion's) | items: Life Orb 51.3%, Choice Scarf 19.1%, Mystic Water 12.6%, Focus Sash 10.7%, Sitrus Berry 6.2%; spreads: fast 68.0%, mixed 16.0%, offensive 10.1%, bulky 5.9%; moves: Protect +20pp, Flip Turn -16pp |
| Incineroar | 27 | 6.3% | 0.818 | 6 (Incineroar in Basculegion's) | items: Life Orb 51.2%, Choice Scarf 32.5%, Focus Sash 9.8%, Mystic Water 6.6%; spreads: fast 82.7%, offensive 7.3%, bulky 6.1%, mixed 3.9% |

## 12. Sheet vs. Ladder
Ladder snapshot: 2026-09-24 (M6, M-C, Doubles).
Sheet = shared teams (87 of 395 placed; of the window's teams, only those with a placement are results; the rest are social shares, videos, and ladder pastes), not only Bo3 open-sheet results.
Ladder teammates: the site's top 8 in its order (metric unstated); absence means not in the top 8, not rare.

### 12.1 Coverage
Ladder top 60 species by rank: 59 node, 1 thin (fewer than minNodeTeams sheet teams), 0 absent from the sheet.
| Ladder rank | Ladder species | Sheet species | Sheet teams | Weighted support | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Rillaboom | Rillaboom | 220 | 56.6% | node |
| 2 | Sneasler | Sneasler | 195 | 47.1% | node |
| 3 | Salamence | Salamence | 159 | 39.5% | node |
| 4 | Incineroar | Incineroar | 139 | 35.8% | node |
| 5 | Indeedee-F | Indeedee-F | 100 | 24.2% | node |
| 6 | Kingambit | Kingambit | 115 | 29.0% | node |
| 7 | Basculegion | Basculegion | 89 | 21.5% | node |
| 8 | Golisopod | Golisopod | 66 | 16.2% | node |
| 9 | Garchomp | Garchomp | 54 | 14.5% | node |
| 10 | Gholdengo | Gholdengo | 87 | 24.1% | node |
| 11 | Pelipper | Pelipper | 39 | 10.4% | node |
| 12 | Archaludon | Archaludon | 37 | 10.3% | node |
| 13 | Milotic | Milotic | 63 | 15.6% | node |
| 14 | Farigiraf | Farigiraf | 40 | 10.0% | node |
| 15 | Charizard | Charizard | 33 | 8.6% | node |
| 16 | Gardevoir | Gardevoir | 34 | 7.7% | node |
| 17 | Raichu | Raichu | 40 | 11.7% | node |
| 18 | Arcanine-Hisui | Arcanine-Hisui | 52 | 14.0% | node |
| 19 | Sylveon | Sylveon | 43 | 11.1% | node |
| 20 | Tyranitar | Tyranitar | 39 | 9.0% | node |
| 21 | Whimsicott | Whimsicott | 15 | 4.1% | node |
| 22 | Armarouge | Armarouge | 37 | 9.3% | node |
| 23 | Staraptor | Staraptor | 18 | 4.6% | node |
| 24 | Torkoal | Torkoal | 26 | 6.2% | node |
| 25 | Sinistcha | Sinistcha | 21 | 5.2% | node |
| 26 | Metagross | Metagross | 20 | 5.0% | node |
| 27 | Indeedee | Indeedee | 23 | 5.4% | node |
| 28 | Floette-Eternal | Floette-Eternal | 69 | 16.6% | node |
| 29 | Excadrill | Excadrill | 32 | 7.2% | node |
| 30 | Volcarona | Volcarona | 18 | 4.8% | node |
| 31 | Politoed | Politoed | 14 | 3.7% | node |
| 32 | Lucario | Lucario | 27 | 6.7% | node |
| 33 | Grimmsnarl | Grimmsnarl | 13 | 3.1% | node |
| 34 | Swampert | Swampert | 12 | 3.4% | node |
| 35 | Froslass | Froslass | 14 | 4.6% | node |
| 36 | Baxcalibur | Baxcalibur | 27 | 5.9% | node |
| 37 | Gengar | Gengar | 18 | 5.3% | node |
| 38 | Ninetales-Alola | Ninetales-Alola | 11 | 2.6% | node |
| 39 | Dragonite | Dragonite | 10 | 2.9% | node |
| 40 | Glimmora | Glimmora | 19 | 5.3% | node |
| 41 | Pawmot | Pawmot | 19 | 4.6% | node |
| 42 | Aerodactyl | Aerodactyl | 11 | 2.9% | node |
| 43 | Venusaur | Venusaur | 9 | 2.2% | node |
| 44 | Primarina | Primarina | 6 | 2.2% | node |
| 45 | Sableye | Sableye | 2 | 0.5% | thin |
| 46 | Hatterene | Hatterene | 11 | 2.7% | node |
| 47 | Blastoise | Blastoise | 9 | 2.2% | node |
| 48 | Delphox | Delphox | 9 | 2.7% | node |
| 49 | Annihilape | Annihilape | 5 | 1.4% | node |
| 50 | Absol | Absol | 14 | 3.7% | node |
| 51 | Corviknight | Corviknight | 7 | 1.5% | node |
| 52 | Maushold-Four | Maushold | 3 | 0.9% | node |
| 53 | Talonflame | Talonflame | 8 | 1.8% | node |
| 54 | Kommo-o | Kommo-o | 8 | 2.4% | node |
| 55 | Ceruledge | Ceruledge | 7 | 1.6% | node |
| 56 | Dragapult | Dragapult | 8 | 1.9% | node |
| 57 | Camerupt | Camerupt | 4 | 1.2% | node |
| 58 | Hydreigon | Hydreigon | 6 | 1.4% | node |
| 59 | Blaziken | Blaziken | 9 | 2.3% | node |
| 60 | Mawile | Mawile | 6 | 1.4% | node |
Sheet-only (no ladder entry, or ranked below 60): Sirfetch’d, Toxtricity, Scrafty, Meowstic, Pyroar, Klefki, Gallade, Vanilluxe, Aegislash, Tsareena, Alakazam, Altaria, Pincurchin, Lycanroc-Dusk, Zoroark-Hisui, Typhlosion-Hisui, Scizor

### 12.2 Item Gaps
Ladder items on a top-60 species held by at least ladderItemMinShare of the species on the ladder -- a higher share than in the sheet -- that have fewer than minNodeTeams sheet teams.
| Ladder rank | Species | Item | Ladder share | Sheet share | Sheet teams |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 23 | Staraptor | Choice Scarf | 16.8% | 4.4% | 1 |
| 30 | Volcarona | Sitrus Berry | 15.3% | 12.3% | 2 |
| 31 | Politoed | Life Orb | 17.0% | 5.5% | 1 |
| 38 | Ninetales-Alola | Focus Sash | 20.5% | 0.0% | 0 |
| 43 | Venusaur | Life Orb | 17.7% | 9.5% | 1 |
| 43 | Venusaur | Venusaurite | 16.7% | 0.0% | 0 |
| 44 | Primarina | Life Orb | 29.5% | 19.2% | 1 |
| 44 | Primarina | Mystic Water | 16.2% | 0.0% | 0 |
| 49 | Annihilape | Leftovers | 22.3% | 15.4% | 1 |
| 51 | Corviknight | Leftovers | 31.6% | 28.1% | 2 |
| 52 | Maushold | Focus Sash | 15.7% | 0.0% | 0 |
| 53 | Talonflame | Focus Sash | 27.2% | 0.0% | 0 |
| 54 | Kommo-o | Life Orb | 32.1% | 17.7% | 1 |
| 55 | Ceruledge | Colbur Berry | 27.2% | 17.4% | 1 |
| 56 | Dragapult | Focus Sash | 24.9% | 24.6% | 2 |
| 58 | Hydreigon | Life Orb | 15.6% | 0.0% | 0 |
| 59 | Blaziken | Focus Sash | 24.6% | 23.7% | 2 |

### 12.3 Bias
#### By usage rank
Over-represented in the sheet vs. the ladder (sheet nodes, ladder rank within the top 60, score above zero, highest first):
| Species | Sheet rank | Ladder rank | Sheet teams | Score (log2) |
| :--- | :--- | :--- | :--- | :--- |
| Floette-Eternal | 9 | 28 | 69 | 1.64 |
| Gholdengo | 7 | 10 | 87 | 0.51 |
| Glimmora | 28 | 40 | 19 | 0.51 |
| Arcanine-Hisui | 13 | 18 | 52 | 0.47 |
| Baxcalibur | 26 | 36 | 27 | 0.47 |
| Absol | 37 | 50 | 14 | 0.43 |
| Lucario | 24 | 32 | 27 | 0.42 |
| Gengar | 29 | 37 | 18 | 0.35 |
| Sylveon | 15 | 19 | 43 | 0.34 |
| Excadrill | 23 | 29 | 32 | 0.33 |
| Blaziken | 47 | 59 | 9 | 0.33 |
| Raichu | 14 | 17 | 40 | 0.28 |
| Pawmot | 34 | 41 | 19 | 0.27 |
| Kingambit | 5 | 6 | 115 | 0.26 |
| Milotic | 11 | 13 | 63 | 0.24 |

Under-represented in the sheet (ladder rank within the top 60, score below zero, lowest first):
| Species | Sheet rank | Ladder rank | Sheet teams | Score (log2) |
| :--- | :--- | :--- | :--- | :--- |
| Sableye | 80 | 45 | 2 | -0.83 |
| Whimsicott | 36 | 21 | 15 | -0.78 |
| Pelipper | 16 | 11 | 39 | -0.54 |
| Staraptor | 33 | 23 | 18 | -0.52 |
| Archaludon | 17 | 12 | 37 | -0.50 |
| Charizard | 21 | 15 | 33 | -0.49 |
| Gardevoir | 22 | 16 | 34 | -0.46 |
| Garchomp | 12 | 9 | 54 | -0.42 |
| Farigiraf | 18 | 14 | 40 | -0.36 |
| Golisopod | 10 | 8 | 66 | -0.32 |
| Maushold | 64 | 52 | 3 | -0.30 |
| Politoed | 38 | 31 | 14 | -0.29 |
| Grimmsnarl | 40 | 33 | 13 | -0.28 |
| Indeedee-F | 6 | 5 | 100 | -0.26 |
| Sinistcha | 30 | 25 | 21 | -0.26 |

#### By teammate list
Mean overlap between a species' ladder teammate list and its sheet partners by P(B|A), over the species compared: 64.7%.
- Rillaboom 7/8 shared. Ladder adds Milotic (#8; sheet P=18%, 40 teams). Sheet adds Floette-Eternal (P=24%, 58 teams; Floette-Eternal's own ladder list includes Rillaboom).
- Sneasler 7/8 shared. Ladder adds Gardevoir (#7; sheet P=12%, 25 teams). Sheet adds Floette-Eternal (P=25%, 52 teams; Floette-Eternal's own ladder list includes Sneasler).
- Salamence 6/8 shared. Ladder adds Milotic (#7; sheet P=19%, 29 teams), Tyranitar (#8; sheet P=18%, 30 teams). Sheet adds Arcanine-Hisui (P=21%, 30 teams; Arcanine-Hisui's own ladder list includes Salamence), Floette-Eternal (P=19%, 36 teams; Floette-Eternal's own ladder list includes Salamence).
- Incineroar 7/8 shared. Ladder adds Golisopod (#8; sheet P=10%, 14 teams). Sheet adds Floette-Eternal (P=35%, 52 teams; Floette-Eternal's own ladder list includes Incineroar).
- Indeedee-F 7/8 shared. Ladder adds Pelipper (#8; sheet P=14%, 13 teams). Sheet adds Salamence (P=15%, 15 teams; Salamence's own ladder list does not include Indeedee-F).
- Kingambit 6/8 shared. Ladder adds Garchomp (#6; sheet P=13%, 16 teams), Charizard (#8; sheet P=8%, 9 teams). Sheet adds Floette-Eternal (P=27%, 33 teams; Floette-Eternal's own ladder list includes Kingambit), Arcanine-Hisui (P=16%, 15 teams; Arcanine-Hisui's own ladder list includes Kingambit).
- Basculegion 6/8 shared. Ladder adds Gardevoir (#7; sheet P=15%, 14 teams), Pelipper (#8; sheet P=12%, 9 teams). Sheet adds Floette-Eternal (P=22%, 21 teams; Floette-Eternal's own ladder list includes Basculegion), Lucario (P=16%, 13 teams; Lucario's own ladder list includes Basculegion).
- Golisopod 7/8 shared. Ladder adds Basculegion (#8; sheet P=19%, 12 teams). Sheet adds Armarouge (P=20%, 12 teams; Armarouge's own ladder list includes Golisopod).
- Garchomp 5/8 shared. Ladder adds Basculegion (#6; sheet P=16%, 8 teams), Whimsicott (#7; sheet P=10%, 5 teams), Farigiraf (#8; sheet P=14%, 6 teams). Sheet adds Floette-Eternal (P=22%, 11 teams; Floette-Eternal's own ladder list includes Garchomp), Gholdengo (P=21%, 9 teams; Gholdengo's own ladder list does not include Garchomp), Milotic (P=19%, 11 teams; Milotic's own ladder list does not include Garchomp).
- Gholdengo 7/8 shared. Ladder adds Tyranitar (#8; sheet P=15%, 14 teams). Sheet adds Floette-Eternal (P=23%, 22 teams; Floette-Eternal's own ladder list includes Gholdengo).
- Pelipper 7/8 shared. Ladder adds Grimmsnarl (#8; sheet P=19%, 7 teams). Sheet adds Incineroar (P=20%, 8 teams; Incineroar's own ladder list does not include Pelipper).
- Archaludon 6/8 shared. Ladder adds Grimmsnarl (#7; sheet P=21%, 9 teams), Charizard (#8; sheet P=14%, 6 teams). Sheet adds Basculegion (P=25%, 9 teams; Basculegion's own ladder list does not include Archaludon), Gengar (P=22%, 7 teams; Gengar's own ladder list includes Archaludon).
- Milotic 7/8 shared. Ladder adds Indeedee-F (#8; sheet P=10%, 7 teams). Sheet adds Arcanine-Hisui (P=23%, 14 teams; Arcanine-Hisui's own ladder list does not include Milotic).
- Farigiraf 6/8 shared. Ladder adds Pelipper (#6; sheet P=18%, 8 teams), Sylveon (#7; sheet P=14%, 5 teams). Sheet adds Salamence (P=29%, 12 teams; Salamence's own ladder list does not include Farigiraf), Torkoal (P=20%, 8 teams; Torkoal's own ladder list includes Farigiraf).
- Charizard 5/8 shared. Ladder adds Whimsicott (#5; <4 sheet teams), Basculegion (#6; sheet P=20%, 6 teams), Archaludon (#8; sheet P=17%, 6 teams). Sheet adds Indeedee-F (P=27%, 10 teams; Indeedee-F's own ladder list does not include Charizard), Floette-Eternal (P=25%, 7 teams; Floette-Eternal's own ladder list does not include Charizard), Venusaur (P=24%, 8 teams; Venusaur's own ladder list includes Charizard).
- Gardevoir 7/8 shared. Ladder adds Golisopod (#7; sheet P=13%, 4 teams). Sheet adds Salamence (P=20%, 6 teams; Salamence's own ladder list does not include Gardevoir).
- Raichu 8/8 shared.
- Arcanine-Hisui 7/8 shared. Ladder adds Basculegion (#7; sheet P=14%, 8 teams). Sheet adds Milotic (P=25%, 14 teams; Milotic's own ladder list does not include Arcanine-Hisui).
- Sylveon 7/8 shared. Ladder adds Farigiraf (#8; sheet P=13%, 5 teams). Sheet adds Basculegion (P=27%, 13 teams; Basculegion's own ladder list does not include Sylveon).
- Tyranitar 8/8 shared.
- Whimsicott 4/8 shared. Ladder adds Charizard (#1; <4 sheet teams), Sneasler (#4; sheet P=22%, 4 teams), Rillaboom (#6; <4 sheet teams), Staraptor (#8; <4 sheet teams). Sheet adds Floette-Eternal (P=31%, 4 teams; Floette-Eternal's own ladder list does not include Whimsicott), Salamence (P=29%, 5 teams; Salamence's own ladder list does not include Whimsicott), Glimmora (P=27%, 4 teams; Glimmora's own ladder list includes Whimsicott), Indeedee-F (P=26%, 4 teams; Indeedee-F's own ladder list does not include Whimsicott).
- Armarouge 7/8 shared. Ladder adds Milotic (#8; <4 sheet teams). Sheet adds Hatterene (P=11%, 4 teams; Hatterene's own ladder list includes Armarouge).
- Staraptor 6/8 shared. Ladder adds Kingambit (#6; <4 sheet teams), Whimsicott (#8; <4 sheet teams).
- Torkoal 7/8 shared. Ladder adds Incineroar (#8; <4 sheet teams). Sheet adds Hatterene (P=26%, 7 teams; Hatterene's own ladder list includes Torkoal).
- Sinistcha 6/8 shared. Ladder adds Pelipper (#3; sheet P=21%, 4 teams), Grimmsnarl (#8; <4 sheet teams). Sheet adds Salamence (P=36%, 8 teams; Salamence's own ladder list does not include Sinistcha), Kingambit (P=24%, 5 teams; Kingambit's own ladder list does not include Sinistcha).
- Metagross 7/8 shared. Ladder adds Salamence (#4; <4 sheet teams). Sheet adds Basculegion (P=22%, 4 teams; Basculegion's own ladder list does not include Metagross).
- Indeedee 6/8 shared. Ladder adds Kingambit (#7; <4 sheet teams), Metagross (#8; <4 sheet teams). Sheet adds Arcanine-Hisui (P=23%, 4 teams; Arcanine-Hisui's own ladder list does not include Indeedee), Garchomp (P=21%, 5 teams; Garchomp's own ladder list does not include Indeedee).
- Floette-Eternal 8/8 shared.
- Excadrill 8/8 shared.
- Volcarona 5/8 shared. Ladder adds Incineroar (#3; sheet P=20%, 4 teams), Gholdengo (#7; <4 sheet teams), Raichu (#8; <4 sheet teams). Sheet adds Glimmora (P=50%, 8 teams; Glimmora's own ladder list includes Volcarona), Basculegion (P=29%, 6 teams; Basculegion's own ladder list does not include Volcarona), Baxcalibur (P=24%, 5 teams; Baxcalibur's own ladder list does not include Volcarona).
- Politoed 6/8 shared. Ladder adds Kingambit (#7; <4 sheet teams), Staraptor (#8; <4 sheet teams).
- Lucario 7/8 shared. Ladder adds Indeedee-F (#6; sheet P=19%, 5 teams). Sheet adds Aerodactyl (P=21%, 6 teams; Aerodactyl's own ladder list does not include Lucario).
- Grimmsnarl 4/8 shared. Ladder adds Swampert (#5; <4 sheet teams), Rillaboom (#6; <4 sheet teams), Sinistcha (#7; <4 sheet teams), Venusaur (#8; <4 sheet teams). Sheet adds Farigiraf (P=23%, 4 teams; Farigiraf's own ladder list does not include Grimmsnarl).
- Swampert 5/8 shared. Ladder adds Grimmsnarl (#4; <4 sheet teams), Rillaboom (#5; <4 sheet teams), Sinistcha (#7; <4 sheet teams). Sheet adds Indeedee-F (P=37%, 4 teams; Indeedee-F's own ladder list does not include Swampert).
- Froslass 5/8 shared. Ladder adds Incineroar (#4; <4 sheet teams), Archaludon (#6; <4 sheet teams), Politoed (#8; <4 sheet teams). Sheet adds Salamence (P=40%, 6 teams; Salamence's own ladder list does not include Froslass).
- Baxcalibur 7/8 shared. Ladder adds Gholdengo (#6; sheet P=16%, 4 teams). Sheet adds Volcarona (P=19%, 5 teams; Volcarona's own ladder list does not include Baxcalibur).
- Gengar 5/8 shared. Ladder adds Froslass (#5; <4 sheet teams), Vivillon (#6; <4 sheet teams), Swampert (#8; <4 sheet teams). Sheet adds Kingambit (P=22%, 4 teams; Kingambit's own ladder list does not include Gengar).
- Ninetales-Alola 4/8 shared. Ladder adds Milotic (#5; <4 sheet teams), Gholdengo (#6; <4 sheet teams), Kingambit (#7; <4 sheet teams), Basculegion (#8; <4 sheet teams).
- Dragonite 3/8 shared. Ladder adds Basculegion (#4; <4 sheet teams), Kingambit (#5; <4 sheet teams), Archaludon (#6; <4 sheet teams), Pelipper (#7; <4 sheet teams), Gholdengo (#8; <4 sheet teams). Sheet adds Indeedee-F (P=39%, 5 teams; Indeedee-F's own ladder list does not include Dragonite).
- Glimmora 6/8 shared. Ladder adds Incineroar (#5; <4 sheet teams), Golisopod (#8; <4 sheet teams). Sheet adds Pawmot (P=17%, 4 teams; Pawmot's own ladder list does not include Glimmora), Indeedee-F (P=17%, 4 teams; Indeedee-F's own ladder list does not include Glimmora).
- Pawmot 4/8 shared. Ladder adds Golisopod (#1; <4 sheet teams), Farigiraf (#4; <4 sheet teams), Politoed (#7; <4 sheet teams), Staraptor (#8; <4 sheet teams). Sheet adds Basculegion (P=38%, 7 teams; Basculegion's own ladder list does not include Pawmot), Glimmora (P=20%, 4 teams; Glimmora's own ladder list does not include Pawmot), Garchomp (P=20%, 4 teams; Garchomp's own ladder list does not include Pawmot).
- Aerodactyl 4/8 shared. Ladder adds Garchomp (#1; <4 sheet teams), Charizard (#2; <4 sheet teams), Kingambit (#3; <4 sheet teams), Farigiraf (#8; <4 sheet teams). Sheet adds Lucario (P=49%, 6 teams; Lucario's own ladder list does not include Aerodactyl), Basculegion (P=41%, 5 teams; Basculegion's own ladder list does not include Aerodactyl).
- Venusaur 2/8 shared. Ladder adds Archaludon (#2; <4 sheet teams), Pelipper (#3; <4 sheet teams), Grimmsnarl (#4; <4 sheet teams), Sneasler (#6; <4 sheet teams), Basculegion (#7; <4 sheet teams), Golisopod (#8; <4 sheet teams). Sheet adds Indeedee-F (P=49%, 5 teams; Indeedee-F's own ladder list does not include Venusaur).
- Primarina 2/8 shared. Ladder adds Incineroar (#2; <4 sheet teams), Farigiraf (#4; <4 sheet teams), Golisopod (#5; <4 sheet teams), Sneasler (#6; <4 sheet teams), Indeedee-F (#7; <4 sheet teams), Kingambit (#8; <4 sheet teams).
- Hatterene 4/8 shared. Ladder adds Camerupt (#5; <4 sheet teams), Incineroar (#6; <4 sheet teams), Kingambit (#7; <4 sheet teams), Farigiraf (#8; <4 sheet teams).
- Blastoise 4/8 shared. Ladder adds Farigiraf (#4; <4 sheet teams), Sinistcha (#5; <4 sheet teams), Incineroar (#6; <4 sheet teams), Pelipper (#8; <4 sheet teams).
- Delphox 5/8 shared. Ladder adds Garchomp (#6; <4 sheet teams), Sinistcha (#7; <4 sheet teams), Whimsicott (#8; <4 sheet teams). Sheet adds Floette-Eternal (P=53%, 4 teams; Floette-Eternal's own ladder list does not include Delphox).
- Annihilape 0/8 shared. Ladder adds Rillaboom (#1; <4 sheet teams), Indeedee-F (#2; <4 sheet teams), Incineroar (#3; <4 sheet teams), Golisopod (#4; <4 sheet teams), Salamence (#5; <4 sheet teams), Pelipper (#6; <4 sheet teams), Archaludon (#7; <4 sheet teams), Armarouge (#8; <4 sheet teams).
- Absol 7/8 shared. Ladder adds Salamence (#6; <4 sheet teams).
- Corviknight 4/8 shared. Ladder adds Salamence (#4; <4 sheet teams), Garchomp (#6; <4 sheet teams), Rillaboom (#7; <4 sheet teams), Indeedee-F (#8; <4 sheet teams).
- Talonflame 3/8 shared. Ladder adds Rillaboom (#1; <4 sheet teams), Garchomp (#2; <4 sheet teams), Sneasler (#3; <4 sheet teams), Kingambit (#6; <4 sheet teams), Milotic (#8; <4 sheet teams).
- Kommo-o 4/8 shared. Ladder adds Indeedee-F (#3; <4 sheet teams), Gardevoir (#6; <4 sheet teams), Basculegion (#7; <4 sheet teams), Sneasler (#8; <4 sheet teams).
- Ceruledge 2/8 shared. Ladder adds Raichu (#3; <4 sheet teams), Sneasler (#4; <4 sheet teams), Gholdengo (#5; <4 sheet teams), Salamence (#6; <4 sheet teams), Staraptor (#7; <4 sheet teams), Kingambit (#8; <4 sheet teams).
- Dragapult 2/8 shared. Ladder adds Rillaboom (#3; <4 sheet teams), Metagross (#4; <4 sheet teams), Incineroar (#5; <4 sheet teams), Sneasler (#6; <4 sheet teams), Salamence (#7; <4 sheet teams), Staraptor (#8; <4 sheet teams). Sheet adds Arcanine-Hisui (P=38%, 4 teams; Arcanine-Hisui's own ladder list does not include Dragapult).
- Camerupt 0/8 shared. Ladder adds Indeedee-F (#1; <4 sheet teams), Farigiraf (#2; <4 sheet teams), Hatterene (#3; <4 sheet teams), Kingambit (#4; <4 sheet teams), Rillaboom (#5; <4 sheet teams), Golisopod (#6; <4 sheet teams), Incineroar (#7; <4 sheet teams), Torkoal (#8; <4 sheet teams).
- Hydreigon 0/8 shared. Ladder adds Rillaboom (#1; <4 sheet teams), Sneasler (#2; <4 sheet teams), Metagross (#3; <4 sheet teams), Indeedee-F (#4; <4 sheet teams), Incineroar (#5; <4 sheet teams), Charizard (#6; <4 sheet teams), Golisopod (#7; <4 sheet teams), Salamence (#8; <4 sheet teams).
- Blaziken 3/8 shared. Ladder adds Metagross (#4; <4 sheet teams), Salamence (#5; <4 sheet teams), Basculegion (#6; <4 sheet teams), Farigiraf (#7; <4 sheet teams), Milotic (#8; <4 sheet teams).
- Mawile 1/8 shared. Ladder adds Farigiraf (#1; <4 sheet teams), Torkoal (#2; <4 sheet teams), Incineroar (#4; <4 sheet teams), Rillaboom (#5; <4 sheet teams), Armarouge (#6; <4 sheet teams), Sneasler (#7; <4 sheet teams), Hatterene (#8; <4 sheet teams).
Too few sheet teams to compare: Maushold.
