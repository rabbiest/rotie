# Meta-graph report: Regulation M-C

## Reading this report
- **Team weight**: each team counts as placement × recency × variant split. Placement gives `placementTopWeight` at or better than rank `placementTopRank`, `placementMidWeight` at or better than rank `placementMidRank`, and `placementDefaultWeight` otherwise (no rank, or a ladder-peak rank). Recency halves every `recencyHalfLifeDays` days before the as-of date. Teams linked by `variantOf` form one variant family and share one team's weight.
- **Support**: the share of the total team weight carried by the teams that contain a species, a species@item, a pair, or a triple.
- **Lift**: a pair's support divided by the product of its two sides' supports. It is one when the two appear together exactly as often as chance predicts, and higher when they appear together more often. Lift cannot exceed one over the more common side's support, so a pair with a very common species always has a low lift; the normalized lift divides lift by that ceiling.
- **Confidence**: Conf(B|A) is the pair's support divided by A's support, the share of A's team weight whose teams also contain B.
- **Core**: a pair (two species, or a species@item with a species) found in at least `coreMinTeams` variant families, whose lift is at least `coreMinLift`, or exceeds `communityMinLift` with a confidence of at least `coreMinConfidence` in either direction. Two species@item tokens never form a core in the species graph.
- **Community**: Louvain communities (resolution `louvainResolution`, seed `louvainSeed`) over the species pairs with at least `minPairTeams` teams and lift above `communityMinLift`, each weighted by its Louvain weight (`louvainWeightMode`). A species with no such pair is unconnected and belongs to no community. A member's in-community support is the share of the community's primary team weight carried by the teams that contain it; teams for which the community is only a hybrid don't count.
- **Mode tags**: each team carries a tag for what its sets set up: Sun, Rain, Sand, Snow, Psyspam, Trick Room, Tailwind, Perish Trap, Screens, Setup. A weather: a set whose ability on entry (its own, or its Mega's when it holds its stone) sets it. Psyspam: a set whose ability sets Psychic Terrain, plus an Expanding Force user. Trick Room: a Trick Room setter and another set that is a Trick Room abuser (its Speed in battle at or below `trickRoomAbuserMaxSpeed`: from its stat points whatever its nature, or, for a set that publishes none, only when its nature lowers Speed). Tailwind: a Tailwind user. Perish Trap: a Perish Song user, plus a Shadow Tag holder or a Mean Look or Block user. Screens: a Light Clay holder (the item alone), or a set with both Reflect and Light Screen. Setup: at least `setupModeMinSets` sets with a setup move. Its Megas are the formes its stones resolve to.
- **Names**: a community (or sub-community) is named by the mode tags, then the Megas, each on at least `labelMinCoverage` of its primary teams, by coverage ("Sand Psyspam (Mega Tyranitar)"); a tag on at least `labelDominantTagShare` of all the window's teams names nothing, and a minor sub-community, or one with fewer than `subMinTokenTeams` primary teams, gets no such name. With no such tag, it is named by its token label. The token label is always shown beside it: its three members with the highest in-community support, as bare tokens (a folded species without its item note), `A / B / C`; when fewer than `labelAlternativeMaxCooccurrence` of its primary teams carrying the second or the third carry both, the two are alternatives and it reads `A + B/C`. The first two slots always show; the third only when its member (the second and third jointly when they are alternatives) is on at least `labelMinCoverage` of the primary teams. A sub-community that would read like its parent adds its first member's species, and two entries that would share a name add the species of their token label's first member, then fall back to their token labels.
- **Replicated builds**: rosters, the teams with the same six species, at least `buildMinCopies` of them and one with a tournament placement, named by those six species, with the tags and Megas of those teams, their copies, earliest-dated pilot, best placement, and the community or sub-community holding most of them. They change no community, assignment, or share.
- **Primary, hybrid, unassigned**: a team scores against each community the summed Louvain weight of the cores it contains, counting only the strongest core per species pair, and a core counts toward the communities of both its species. The best-scoring community is the team's primary; every other community scoring at least `hybridRunnerUpRatio` of the best, and at least `hybridMinMedianRatio` of the median score of that community's own primary teams, is one of its hybrids, so a team that fits every community poorly collects none. A team with no core is unassigned. Primary shares plus the unassigned share make up the whole team weight; hybrid shares are counted apart, and a team with several hybrids counts toward each.
- **Sub-communities**: a community with at least `subPassMinParentTeams` primary teams gets a second Louvain pass (resolution `subLouvainResolution`) over its primary teams alone; the teams for which it is only the hybrid are left out. Its nodes are species@item tokens: a set counts as its species@item only when its item defines a build, by a fixed item-class table (mega-stone (any Mega Stone), seed (Grassy Seed, Psychic Seed, Electric Seed, Misty Seed), speed (Choice Scarf), screens (Light Clay), pivot (Eject Button); an item the table doesn't class counts as defining), and that token is on at least `subMinTokenTeams` of those teams. Any other set (an item of another class, a rarer defining item, or no item) counts as its bare species, shown as "(other item)" with the most common of its items and how many of the bare species' teams hold it. Support, lift, cores, and the primary, hybrid, and unassigned split are computed within those teams by the rules above, two species@item tokens can form a core there, two tokens of the same species are never named as alternatives, and a sub-community's shares are of its parent community's primary team weight. Each sub-community is headed by its primary teams, its distinct builds (teams with the same tokens, or of one variant family, are one build), and how many of its primary teams carry its top two tokens together ("top pair on k/n"), then lists the Megas those teams carry and its most common species; one is minor when it has fewer than `subMinDistinctBuilds` distinct builds, or neither a shared core (its top pair on at least `subMinSharedCoverage` of its primary teams) nor a shared mode (a mode tag that names something in this window on at least that share): a minor sub-community is listed on one line, gets no composed name, and its teams count as minor in the cluster comparison.
- **Sheet vs. ladder**: the tournament sample compared with the ladder's daily snapshots dated from the window's start to the as-of date and recorded under this regulation. A species is on the ladder when at least half of those snapshots list it; its median rank is the lower median of its ranks in the snapshots that list it, and the ladder order is by median rank, then rank in the latest snapshot, then name. Over the ladder's top `ladderTopSpecies` species in that order: the ladder items (latest snapshot) held by at least `ladderItemMinShare` of a species' sets on the ladder, more than in the sheet, on fewer than `minNodeTeams` sheet teams; the species the two rank most differently (each list capped at `ladderBiasListSize`); and each species' ladder teammate list (latest snapshot) against its sheet partners by P(B|A). Since the ladder gives usage and teammates as ranks and lists with no shares, these comparisons are by rank or list membership only.
- **Ladder-calibrated view**: with `ladderBlend` above zero, the same teams are weighed a second way. Among the sheet's species nodes with a ladder entry, each takes as its rank-matched support the sheet support found at its own position in the ladder order among them, and every team's weight is multiplied once by the geometric mean over its species of (rank-matched support / sheet support) raised to `ladderBlend`; any other species counts as one. Species support and the community and sub-community shares are then shown under both weights, over the unchanged communities and assignments. It is a model built from the ladder's rank order, not a ladder usage share: it moves shares only part of the way, and not always toward the ladder's order, since each factor is averaged with its teammates'; it cannot add a build the sheet lacks; and a species thin or absent on the sheet can't be reweighted.
- **Tags that name nothing in this window** (on at least `labelDominantTagShare` of its teams): Tailwind.

## 1. Window & Applied Defaults
- Regulation: Regulation M-C
- As of: 2026-10-02, window: 23 days
- Date range: 2026-09-09 to 2026-09-27
- Teams analyzed: 2915 (total weight 1853.19)
- Ladder: 2026-09-16 to 2026-09-30, median rank over 15 daily snapshots (M6, M-C). Battle data provided by Pokémon Champions Battle Data (https://championsbattledata.com); only figures derived from these snapshots are shown.
- Placement: 308 of 2915 teams have no tournament placement and weigh placementDefaultWeight, so the placement tiers move few teams. The tiers ignore event size (a small cup's winner weighs like a large event's top cut), and Seniors teams are not separated.
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
  - hybridMinMedianRatio: 0.09
  - labelAlternativeMaxCooccurrence: 0.3333333333333333
  - labelMinCoverage: 0.5
  - labelDominantTagShare: 0.5
  - setupModeMinSets: 2
  - buildMinCopies: 3
  - forceAtlas2Iterations: 200
  - subPassMinParentTeams: 20
  - subMinTokenTeams: 5
  - subLouvainResolution: 1
  - subMinDistinctBuilds: 3
  - subMinSharedCoverage: 0.5
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
  - ladderBlend: 1

## 2. Known-Core Check
Species pairs A + B from knownCoreChecks, with their best species@item + species pair (highest lift) beside them: an item can carry a synergy the species-level pair does not show.
| Pair | Present | Teams | Families | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Best item-level pair | Item teams | Item lift | Conf(partner | item) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Salamence + Rillaboom | yes | 638 | 601 | 1.233 | 0.674 | 0.37 | 0.67 | yes | Rillaboom@Expert Belt + Salamence | 23 | 2.813 | 0.85 |
| Sneasler + Rillaboom | yes | 691 | 660 | 1.005 | 0.549 | 0.42 | 0.55 | no | Sneasler@Grassy Seed + Rillaboom | 420 | 1.829 | 1.00 |
| Tyranitar + Excadrill | yes | 216 | 204 | 10.607 | 0.974 | 0.97 | 0.80 | yes | Tyranitar@Tyranitarite + Excadrill | 204 | 11.450 | 0.86 |
| Gardevoir + Indeedee-F | yes | 222 | 214 | 5.340 | 0.970 | 0.39 | 0.97 | yes | Indeedee-F@Colbur Berry + Gardevoir | 94 | 7.686 | 0.56 |

## 3. Top Pairs by Lift and Support
### Species pairs by lift
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Pincurchin | Raichu-Alola | 4 | 0.1% | 487.712 | 1.000 | 1.00 | 0.55 | yes | 2 |
| Espathra | Goodra-Hisui | 4 | 0.2% | 78.464 | 0.540 | 0.54 | 0.25 | yes | n/a |
| Araquanid | Kleavor | 4 | 0.1% | 73.071 | 0.631 | 0.17 | 0.63 | yes | n/a |
| Absol | Goodra-Hisui | 5 | 0.2% | 44.924 | 0.674 | 0.67 | 0.14 | yes | n/a |
| Lycanroc-Dusk | Scovillain | 6 | 0.2% | 39.085 | 0.324 | 0.23 | 0.32 | yes | 6 |
| Houndoom | Torkoal | 4 | 0.1% | 32.508 | 1.000 | 0.05 | 1.00 | yes | 1 |
| Abomasnow | Camerupt | 4 | 0.2% | 31.292 | 0.765 | 0.06 | 0.77 | yes | 3 |
| Camerupt | Hatterene | 33 | 1.2% | 24.757 | 0.605 | 0.61 | 0.49 | yes | 4 |
| Altaria | Dragapult | 12 | 0.4% | 19.627 | 0.622 | 0.12 | 0.62 | yes | 5 |
| Blastoise | Maushold | 13 | 0.5% | 19.151 | 0.328 | 0.33 | 0.31 | yes | n/a |
| Glimmora | Klefki | 7 | 0.3% | 18.305 | 0.779 | 0.78 | 0.06 | yes | n/a |
| Absol | Espathra | 4 | 0.2% | 16.326 | 0.245 | 0.25 | 0.11 | yes | n/a |
| Gallade | Hatterene | 5 | 0.2% | 16.172 | 0.319 | 0.08 | 0.32 | yes | n/a |
| Kommo-o | Pyroar | 14 | 0.5% | 15.227 | 0.631 | 0.63 | 0.12 | yes | n/a |
| Gengar | Vivillon | 29 | 1.2% | 14.808 | 0.841 | 0.84 | 0.22 | yes | 6 |
| Empoleon | Ninetales-Alola | 4 | 0.1% | 14.030 | 0.268 | 0.08 | 0.27 | yes | n/a |
| Aegislash | Venusaur | 6 | 0.2% | 13.803 | 0.498 | 0.05 | 0.50 | yes | n/a |
| Pyroar | Whimsicott | 16 | 0.6% | 13.372 | 0.737 | 0.11 | 0.74 | yes | 2 |
| Aerodactyl | Tsareena | 5 | 0.2% | 12.466 | 0.386 | 0.39 | 0.06 | yes | n/a |
| Altaria | Metagross | 12 | 0.4% | 11.529 | 0.622 | 0.07 | 0.62 | yes | 3 |
| Politoed | Vivillon | 27 | 1.1% | 11.472 | 0.783 | 0.78 | 0.17 | yes | n/a |
| Toxapex | Venusaur | 6 | 0.2% | 11.317 | 0.408 | 0.07 | 0.41 | yes | n/a |
| Corviknight | Indeedee | 54 | 2.1% | 11.182 | 0.751 | 0.31 | 0.75 | yes | 5 |
| Baxcalibur | Ninetales-Alola | 14 | 0.4% | 11.076 | 0.217 | 0.22 | 0.21 | yes | 4 |
| Corviknight | Excadrill | 59 | 2.3% | 11.045 | 0.830 | 0.31 | 0.83 | yes | 3 |

### Species pairs by support
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Gholdengo | Rillaboom | 710 | 24.5% | 1.556 | 0.851 | 0.45 | 0.85 | yes | 1 |
| Rillaboom | Sneasler | 691 | 22.8% | 1.005 | 0.549 | 0.55 | 0.42 | no | 2 |
| Raichu | Rillaboom | 619 | 22.5% | 1.606 | 0.878 | 0.41 | 0.88 | yes | 1 |
| Rillaboom | Salamence | 638 | 20.3% | 1.233 | 0.674 | 0.67 | 0.37 | yes | 3 |
| Incineroar | Rillaboom | 603 | 19.9% | 1.252 | 0.684 | 0.36 | 0.68 | yes | 1 |
| Salamence | Sneasler | 578 | 18.6% | 1.488 | 0.617 | 0.45 | 0.62 | yes | 2 |
| Arcanine-Hisui | Rillaboom | 513 | 17.8% | 1.549 | 0.847 | 0.33 | 0.85 | yes | 1 |
| Gholdengo | Raichu | 469 | 16.7% | 2.265 | 0.653 | 0.65 | 0.58 | yes | 5 |
| Kingambit | Sneasler | 408 | 13.5% | 1.334 | 0.553 | 0.33 | 0.55 | yes | 1 |
| Arcanine-Hisui | Raichu | 368 | 13.3% | 2.465 | 0.633 | 0.52 | 0.63 | yes | 5 |
| Incineroar | Sneasler | 393 | 12.9% | 1.073 | 0.445 | 0.31 | 0.45 | no | 2 |
| Kingambit | Rillaboom | 382 | 12.8% | 0.959 | 0.524 | 0.23 | 0.52 | no | 2 |
| Gholdengo | Salamence | 381 | 12.2% | 1.398 | 0.422 | 0.40 | 0.42 | yes | 2 |
| Arcanine-Hisui | Gholdengo | 347 | 12.0% | 1.972 | 0.568 | 0.42 | 0.57 | yes | 4 |
| Gholdengo | Sneasler | 331 | 10.8% | 0.906 | 0.376 | 0.26 | 0.38 | no | 3 |
| Arcanine-Hisui | Salamence | 296 | 9.8% | 1.536 | 0.464 | 0.32 | 0.46 | yes | 2 |
| Milotic | Rillaboom | 281 | 9.7% | 1.090 | 0.596 | 0.18 | 0.60 | no | 1 |
| Arcanine-Hisui | Sneasler | 280 | 9.5% | 1.090 | 0.452 | 0.23 | 0.45 | no | 3 |
| Rillaboom | Staraptor | 250 | 9.4% | 1.286 | 0.703 | 0.70 | 0.17 | yes | n/a |
| Gholdengo | Staraptor | 246 | 9.3% | 2.411 | 0.695 | 0.69 | 0.32 | yes | n/a |
| Floette-Eternal | Incineroar | 280 | 9.2% | 2.610 | 0.759 | 0.32 | 0.76 | yes | 3 |
| Floette-Eternal | Rillaboom | 278 | 9.1% | 1.362 | 0.744 | 0.17 | 0.74 | yes | 1 |
| Raichu | Staraptor | 238 | 9.0% | 2.615 | 0.671 | 0.67 | 0.35 | yes | 5 |
| Kingambit | Salamence | 283 | 8.8% | 1.197 | 0.361 | 0.29 | 0.36 | no | 3 |
| Indeedee-F | Sneasler | 267 | 8.5% | 1.128 | 0.468 | 0.20 | 0.47 | no | 1 |

### Item-level pairs by lift
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Espathra@Grassy Seed | Goodra-Hisui@Leftovers | 4 | 0.2% | 248.051 | 0.701 | 0.60 | 0.70 | no | n/a |
| Goodra-Hisui | Espathra@Grassy Seed | 4 | 0.2% | 224.394 | 0.701 | 0.70 | 0.54 | yes | n/a |
| Aegislash@Focus Sash | Venusaur@Life Orb | 4 | 0.1% | 103.129 | 0.653 | 0.19 | 0.65 | no | n/a |
| Klefki@Light Clay | Volcarona@Sitrus Berry | 7 | 0.3% | 92.642 | 1.000 | 0.23 | 1.00 | no | n/a |
| Espathra | Goodra-Hisui@Leftovers | 4 | 0.2% | 86.736 | 0.596 | 0.60 | 0.25 | yes | n/a |
| Kleavor@Focus Sash | Whimsicott@Fairy Feather | 4 | 0.1% | 86.141 | 0.387 | 0.39 | 0.32 | no | 3 |
| Klefki | Volcarona@Sitrus Berry | 7 | 0.3% | 72.201 | 0.779 | 0.23 | 0.78 | yes | n/a |
| Maushold@Chople Berry | Sinistcha@Occa Berry | 8 | 0.3% | 64.479 | 0.558 | 0.56 | 0.40 | no | 5 |
| Politoed@Life Orb | Staraptor@Choice Scarf | 4 | 0.1% | 56.527 | 0.455 | 0.45 | 0.15 | no | n/a |
| Toxapex@Leftovers | Venusaur@Life Orb | 4 | 0.2% | 52.447 | 0.332 | 0.28 | 0.33 | no | n/a |
| Absol | Goodra-Hisui@Leftovers | 5 | 0.2% | 49.660 | 0.745 | 0.75 | 0.14 | yes | n/a |
| Absol@Absolite Z | Goodra-Hisui@Leftovers | 5 | 0.2% | 49.660 | 0.745 | 0.75 | 0.14 | no | n/a |
| Aegislash | Venusaur@Life Orb | 4 | 0.1% | 49.309 | 0.312 | 0.19 | 0.31 | yes | n/a |
| Toxapex | Venusaur@Life Orb | 4 | 0.2% | 47.406 | 0.300 | 0.28 | 0.30 | yes | n/a |
| Absol | Espathra@Grassy Seed | 4 | 0.2% | 46.689 | 0.701 | 0.70 | 0.11 | yes | n/a |
| Absol@Absolite Z | Espathra@Grassy Seed | 4 | 0.2% | 46.689 | 0.701 | 0.70 | 0.11 | no | n/a |
| Goodra-Hisui | Absol@Absolite Z | 5 | 0.2% | 44.924 | 0.674 | 0.14 | 0.67 | yes | n/a |
| Kleavor | Whimsicott@Fairy Feather | 4 | 0.1% | 44.812 | 0.387 | 0.39 | 0.17 | yes | 3 |
| Pyroar | Kommo-o@Life Orb | 14 | 0.5% | 43.060 | 0.631 | 0.34 | 0.63 | yes | n/a |
| Kommo-o@Life Orb | Pyroar@Pyroarite | 14 | 0.5% | 43.060 | 0.631 | 0.63 | 0.34 | no | n/a |
| Lycanroc-Dusk@Focus Sash | Scovillain@Scovillainite | 6 | 0.2% | 42.692 | 0.342 | 0.24 | 0.34 | no | 6 |
| Scovillain | Lycanroc-Dusk@Focus Sash | 6 | 0.2% | 41.160 | 0.342 | 0.34 | 0.23 | yes | 7 |
| Lycanroc-Dusk | Scovillain@Scovillainite | 6 | 0.2% | 40.540 | 0.324 | 0.24 | 0.32 | yes | 6 |
| Altaria@Haban Berry | Milotic@Psychic Seed | 12 | 0.4% | 40.126 | 0.901 | 0.17 | 0.90 | no | 1 |
| Maushold@Chople Berry | Sinistcha@Coba Berry | 4 | 0.2% | 38.405 | 0.333 | 0.33 | 0.19 | no | 5 |

### Item-level pairs by support
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | Raichu@Raichunite Y | 618 | 22.5% | 1.634 | 0.894 | 0.89 | 0.41 | yes | 6 |
| Rillaboom | Gholdengo@Life Orb | 624 | 21.6% | 1.573 | 0.860 | 0.86 | 0.39 | yes | 4 |
| Gholdengo | Rillaboom@Miracle Seed | 596 | 20.7% | 1.817 | 0.719 | 0.52 | 0.72 | yes | 1 |
| Rillaboom | Salamence@Salamencite | 638 | 20.3% | 1.234 | 0.675 | 0.67 | 0.37 | yes | 3 |
| Gholdengo@Life Orb | Rillaboom@Miracle Seed | 536 | 18.7% | 1.881 | 0.744 | 0.47 | 0.74 | no | 1 |
| Raichu | Rillaboom@Miracle Seed | 514 | 18.6% | 1.836 | 0.726 | 0.47 | 0.73 | yes | 1 |
| Sneasler | Salamence@Salamencite | 578 | 18.6% | 1.490 | 0.618 | 0.62 | 0.45 | yes | 2 |
| Raichu@Raichunite Y | Rillaboom@Miracle Seed | 513 | 18.6% | 1.868 | 0.739 | 0.47 | 0.74 | no | 1 |
| Rillaboom | Arcanine-Hisui@Focus Sash | 503 | 17.5% | 1.558 | 0.852 | 0.85 | 0.32 | yes | n/a |
| Sneasler | Rillaboom@Miracle Seed | 504 | 16.8% | 1.022 | 0.424 | 0.42 | 0.40 | no | 1 |
| Gholdengo | Raichu@Raichunite Y | 467 | 16.7% | 2.300 | 0.663 | 0.66 | 0.58 | yes | 5 |
| Raichu | Gholdengo@Life Orb | 420 | 15.0% | 2.337 | 0.600 | 0.60 | 0.59 | yes | 2 |
| Rillaboom@Miracle Seed | Salamence@Salamencite | 466 | 15.0% | 1.260 | 0.499 | 0.50 | 0.38 | no | 3 |
| Salamence | Rillaboom@Miracle Seed | 466 | 15.0% | 1.259 | 0.498 | 0.38 | 0.50 | yes | 1 |
| Gholdengo@Life Orb | Raichu@Raichunite Y | 419 | 15.0% | 2.377 | 0.599 | 0.60 | 0.60 | no | 5 |
| Arcanine-Hisui | Rillaboom@Miracle Seed | 400 | 13.9% | 1.673 | 0.662 | 0.35 | 0.66 | yes | 1 |
| Arcanine-Hisui@Focus Sash | Rillaboom@Miracle Seed | 396 | 13.8% | 1.695 | 0.671 | 0.35 | 0.67 | no | 1 |
| Rillaboom | Incineroar@Sitrus Berry | 414 | 13.5% | 1.299 | 0.710 | 0.71 | 0.25 | yes | 1 |
| Rillaboom | Sneasler@Grassy Seed | 420 | 13.5% | 1.829 | 1.000 | 1.00 | 0.25 | yes | 2 |
| Arcanine-Hisui | Raichu@Raichunite Y | 366 | 13.2% | 2.498 | 0.629 | 0.53 | 0.63 | yes | 5 |
| Raichu | Arcanine-Hisui@Focus Sash | 365 | 13.2% | 2.504 | 0.642 | 0.64 | 0.51 | yes | 4 |
| Arcanine-Hisui@Focus Sash | Raichu@Raichunite Y | 364 | 13.2% | 2.545 | 0.641 | 0.52 | 0.64 | no | 5 |
| Incineroar | Rillaboom@Miracle Seed | 401 | 13.2% | 1.144 | 0.452 | 0.33 | 0.45 | no | 1 |
| Gholdengo | Salamence@Salamencite | 381 | 12.2% | 1.400 | 0.422 | 0.40 | 0.42 | yes | 2 |
| Gholdengo | Arcanine-Hisui@Focus Sash | 342 | 11.8% | 1.993 | 0.574 | 0.57 | 0.41 | yes | 6 |

## 4. Item Synergies
Species@item pairs whose lift beats the species-level pair's lift by >= 0.3.
| Token | Partner | Item lift | Species lift | Delta | Teams |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Espathra@Grassy Seed | Goodra-Hisui | 224.394 | 78.464 | 145.930 | 4 |
| Volcarona@Sitrus Berry | Klefki | 72.201 | 10.876 | 61.325 | 7 |
| Whimsicott@Fairy Feather | Kleavor | 44.812 | 5.425 | 39.387 | 4 |
| Venusaur@Life Orb | Toxapex | 47.406 | 11.317 | 36.089 | 4 |
| Venusaur@Life Orb | Aegislash | 49.309 | 13.803 | 35.506 | 4 |
| Incineroar@White Herb | Hatterene | 33.198 | 1.590 | 31.608 | 21 |
| Espathra@Grassy Seed | Absol | 46.689 | 16.326 | 30.363 | 4 |
| Incineroar@White Herb | Camerupt | 32.115 | 2.085 | 30.029 | 25 |
| Kommo-o@Life Orb | Pyroar | 43.060 | 15.227 | 27.833 | 14 |
| Staraptor@Choice Scarf | Pawmot | 26.711 | 0.657 | 26.055 | 4 |
| Sinistcha@Occa Berry | Maushold | 34.504 | 8.514 | 25.990 | 8 |
| Incineroar@Passho Berry | Vivillon | 27.576 | 2.932 | 24.644 | 25 |
| Milotic@Psychic Seed | Altaria | 27.703 | 3.816 | 23.887 | 12 |
| Basculegion@Mystic Water | Pyroar | 26.789 | 5.040 | 21.749 | 14 |
| Rillaboom@Eject Button | Vivillon | 22.749 | 1.559 | 21.190 | 22 |
| Milotic@Psychic Seed | Dragapult | 24.788 | 3.903 | 20.885 | 48 |
| Ninetales-Alola@Light Clay | Baxcalibur | 31.009 | 11.076 | 19.933 | 11 |
| Sinistcha@Occa Berry | Blastoise | 26.919 | 9.918 | 17.001 | 7 |
| Indeedee-F@Focus Sash | Torkoal | 20.040 | 3.492 | 16.548 | 4 |
| Garchomp@Life Orb | Klefki | 21.451 | 4.947 | 16.504 | 7 |
| Garchomp@Choice Scarf | Toxapex | 20.740 | 4.778 | 15.963 | 10 |
| Rillaboom@Eject Button | Gengar | 17.232 | 1.398 | 15.834 | 75 |
| Incineroar@Lum Berry | Gengar | 17.618 | 2.610 | 15.008 | 4 |
| Maushold@Chople Berry | Blastoise | 31.775 | 19.151 | 12.624 | 11 |
| Politoed@Sitrus Berry | Vivillon | 23.808 | 11.472 | 12.335 | 26 |
| Sinistcha@Coba Berry | Maushold | 20.552 | 8.514 | 12.038 | 4 |
| Indeedee@Focus Sash | Typhlosion-Hisui | 16.360 | 4.940 | 11.420 | 4 |
| Indeedee-F@Psychic Seed | Alakazam | 15.921 | 4.632 | 11.289 | 4 |
| Kingambit@Occa Berry | Pawmot | 12.016 | 1.058 | 10.958 | 5 |
| Ninetales-Alola@Never-Melt Ice | Glimmora | 13.242 | 2.361 | 10.881 | 5 |
| Indeedee-F@Psychic Seed | Hatterene | 16.258 | 5.385 | 10.872 | 28 |
| Basculegion@Mystic Water | Typhlosion-Hisui | 12.340 | 1.857 | 10.483 | 5 |
| Armarouge@Twisted Spoon | Dragapult | 17.401 | 6.969 | 10.432 | 15 |
| Incineroar@Passho Berry | Gengar | 12.368 | 2.610 | 9.758 | 51 |
| Kingambit@Occa Berry | Blaziken | 11.525 | 1.947 | 9.578 | 5 |
| Indeedee-F@Psychic Seed | Camerupt | 13.130 | 3.580 | 9.550 | 26 |
| Rillaboom@Life Orb | Lycanroc-Dusk | 10.454 | 0.922 | 9.532 | 4 |
| Rillaboom@Eject Button | Politoed | 10.389 | 0.884 | 9.505 | 54 |
| Sinistcha@Coba Berry | Blastoise | 19.392 | 9.918 | 9.473 | 4 |
| Politoed@Life Orb | Pawmot | 10.936 | 1.545 | 9.391 | 5 |
| Rillaboom@Eject Button | Altaria | 9.614 | 0.566 | 9.048 | 4 |
| Milotic@Psychic Seed | Armarouge | 10.940 | 1.897 | 9.043 | 40 |
| Blaziken@Focus Sash | Metagross | 11.574 | 2.669 | 8.906 | 8 |
| Altaria@Haban Berry | Dragapult | 28.429 | 19.627 | 8.802 | 12 |
| Kingambit@Focus Sash | Aerodactyl | 10.958 | 2.246 | 8.712 | 38 |
| Sinistcha@Occa Berry | Delphox | 19.224 | 10.694 | 8.531 | 12 |
| Incineroar@Passho Berry | Politoed | 10.038 | 1.562 | 8.476 | 49 |
| Sneasler@Focus Sash | Delphox | 10.135 | 1.688 | 8.446 | 49 |
| Garchomp@Life Orb | Aerodactyl | 12.077 | 3.670 | 8.407 | 37 |
| Goodra-Hisui@Leftovers | Espathra | 86.736 | 78.464 | 8.272 | 4 |
| Farigiraf@Colbur Berry | Mawile | 12.607 | 4.414 | 8.192 | 6 |
| Kingambit@Black Glasses | Camerupt | 10.147 | 2.032 | 8.115 | 23 |
| Politoed@Mystic Water | Grimmsnarl | 13.127 | 5.113 | 8.014 | 41 |
| Kingambit@Black Glasses | Hatterene | 9.751 | 1.902 | 7.849 | 18 |
| Kingambit@Black Glasses | Lycanroc-Dusk | 9.373 | 1.881 | 7.492 | 6 |
| Kommo-o@Life Orb | Whimsicott | 11.705 | 4.269 | 7.435 | 26 |
| Maushold@Chople Berry | Sinistcha | 15.910 | 8.514 | 7.396 | 15 |
| Glimmora@Glimmoranite | Klefki | 25.548 | 18.305 | 7.243 | 7 |
| Maushold@Chople Berry | Delphox | 16.839 | 9.604 | 7.235 | 15 |
| Politoed@Sitrus Berry | Gengar | 14.584 | 7.623 | 6.962 | 73 |
| Sneasler@Focus Sash | Sinistcha | 8.046 | 1.092 | 6.954 | 41 |
| Dragonite@Life Orb | Gengar | 7.760 | 0.897 | 6.863 | 4 |
| Incineroar@Chople Berry | Sirfetch’d | 8.546 | 1.741 | 6.806 | 5 |
| Sneasler@Focus Sash | Maushold | 7.761 | 1.113 | 6.648 | 13 |
| Basculegion@Mystic Water | Kommo-o | 7.990 | 1.492 | 6.498 | 22 |
| Staraptor@Choice Scarf | Politoed | 6.661 | 0.164 | 6.497 | 4 |
| Kingambit@Black Glasses | Scovillain | 8.101 | 1.713 | 6.389 | 7 |
| Whimsicott@Fairy Feather | Metagross | 7.165 | 0.784 | 6.380 | 4 |
| Whimsicott@Occa Berry | Kommo-o | 10.609 | 4.269 | 6.339 | 7 |
| Basculegion@Mystic Water | Whimsicott | 9.049 | 2.769 | 6.281 | 31 |
| Sneasler@Psychic Seed | Meowstic-F | 8.225 | 1.998 | 6.228 | 7 |
| Milotic@Sitrus Berry | Ceruledge | 11.188 | 4.969 | 6.219 | 47 |
| Sneasler@Focus Sash | Blastoise | 7.715 | 1.505 | 6.211 | 14 |
| Armarouge@Twisted Spoon | Hatterene | 9.853 | 3.894 | 5.959 | 6 |
| Sableye@Roseli Berry | Sinistcha | 9.380 | 3.568 | 5.812 | 4 |
| Volcarona@Rocky Helmet | Glimmora | 10.906 | 5.128 | 5.778 | 21 |
| Armarouge@Focus Sash | Dragapult | 12.731 | 6.969 | 5.762 | 19 |
| Torkoal@Life Orb | Armarouge | 10.442 | 4.780 | 5.662 | 4 |
| Sinistcha@Coba Berry | Delphox | 16.307 | 10.694 | 5.613 | 9 |
| Pelipper@Sitrus Berry | Venusaur | 9.745 | 4.160 | 5.586 | 38 |
| Indeedee-F@Colbur Berry | Lopunny | 8.987 | 3.405 | 5.582 | 5 |
| Klefki@Light Clay | Glimmora | 23.488 | 18.305 | 5.182 | 7 |
| Altaria@Haban Berry | Metagross | 16.699 | 11.529 | 5.170 | 12 |
| Rillaboom@Occa Berry | Vivillon | 6.659 | 1.559 | 5.099 | 5 |
| Glimmora@Focus Sash | Blaziken | 7.618 | 2.537 | 5.081 | 4 |
| Indeedee-F@Rocky Helmet | Altaria | 8.871 | 3.799 | 5.072 | 13 |
| Indeedee-F@Rocky Helmet | Pyroar | 8.787 | 3.763 | 5.024 | 15 |
| Whimsicott@Occa Berry | Glimmora | 9.631 | 4.693 | 4.938 | 6 |
| Rillaboom@Expert Belt | Excadrill | 5.512 | 0.616 | 4.895 | 12 |
| Indeedee@Focus Sash | Kommo-o | 6.555 | 1.725 | 4.829 | 8 |
| Glimmora@Focus Sash | Hatterene | 6.420 | 1.636 | 4.783 | 4 |
| Goodra-Hisui@Leftovers | Absol | 49.660 | 44.924 | 4.736 | 5 |
| Incineroar@White Herb | Farigiraf | 5.721 | 1.034 | 4.687 | 27 |
| Staraptor@Choice Scarf | Farigiraf | 5.046 | 0.397 | 4.649 | 6 |
| Garchomp@Garchompite | Tyranitar | 4.921 | 0.356 | 4.565 | 4 |
| Delphox@Life Orb | Indeedee-F | 5.503 | 1.090 | 4.413 | 4 |
| Rillaboom@Expert Belt | Tyranitar | 4.939 | 0.587 | 4.352 | 13 |
| Aegislash@Focus Sash | Venusaur | 18.095 | 13.803 | 4.292 | 4 |
| Pelipper@Sitrus Berry | Grimmsnarl | 8.878 | 4.639 | 4.239 | 56 |
| Sneasler@Psychic Seed | Gardevoir | 5.770 | 1.581 | 4.189 | 136 |
| Espathra@Grassy Seed | Floette-Eternal | 7.200 | 3.021 | 4.179 | 5 |
| Milotic@Psychic Seed | Indeedee-F | 5.296 | 1.139 | 4.157 | 58 |
| Milotic@Psychic Seed | Gengar | 5.236 | 1.142 | 4.095 | 16 |
| Incineroar@White Herb | Indeedee-F | 4.570 | 0.517 | 4.052 | 26 |
| Whimsicott@Focus Sash | Pyroar | 17.357 | 13.372 | 3.985 | 16 |
| Indeedee@Focus Sash | Dragonite | 5.344 | 1.396 | 3.949 | 5 |
| Rillaboom@Eject Button | Archaludon | 4.576 | 0.644 | 3.932 | 51 |
| Pelipper@Choice Scarf | Basculegion | 5.676 | 1.759 | 3.917 | 4 |
| Archaludon@Magnet | Pelipper | 9.234 | 5.393 | 3.841 | 4 |
| Farigiraf@Grassy Seed | Torkoal | 6.212 | 2.400 | 3.812 | 6 |
| Rillaboom@Eject Button | Kommo-o | 4.673 | 0.877 | 3.796 | 15 |
| Venusaur@Focus Sash | Grimmsnarl | 10.921 | 7.132 | 3.789 | 37 |
| Indeedee-F@Rocky Helmet | Dragapult | 7.567 | 3.886 | 3.681 | 51 |
| Politoed@Mystic Water | Charizard | 6.028 | 2.368 | 3.660 | 43 |
| Politoed@Life Orb | Farigiraf | 6.425 | 2.830 | 3.595 | 20 |
| Garchomp@Choice Scarf | Venusaur | 5.671 | 2.108 | 3.564 | 17 |
| Kommo-o@Life Orb | Gardevoir | 6.499 | 2.993 | 3.506 | 20 |
| Indeedee@Choice Scarf | Corviknight | 14.653 | 11.182 | 3.471 | 51 |
| Sableye@Light Clay | Swampert | 11.793 | 8.341 | 3.452 | 6 |
| Volcarona@Rocky Helmet | Baxcalibur | 8.744 | 5.296 | 3.448 | 7 |
| Basculegion@Focus Sash | Lucario | 5.846 | 2.428 | 3.419 | 4 |
| Sneasler@White Herb | Corviknight | 5.401 | 2.008 | 3.393 | 48 |
| Kingambit@Occa Berry | Glimmora | 4.937 | 1.548 | 3.388 | 5 |
| Garchomp@Sitrus Berry | Gardevoir | 3.926 | 0.569 | 3.357 | 5 |
| Farigiraf@Grassy Seed | Absol | 4.256 | 0.918 | 3.338 | 4 |
| Incineroar@Passho Berry | Archaludon | 4.154 | 0.855 | 3.299 | 44 |
| Incineroar@White Herb | Torkoal | 3.892 | 0.606 | 3.286 | 4 |
| Venusaur@Life Orb | Sylveon | 4.697 | 1.418 | 3.279 | 9 |
| Farigiraf@Grassy Seed | Primarina | 5.681 | 2.418 | 3.262 | 5 |
| Armarouge@Focus Sash | Gengar | 5.225 | 2.007 | 3.218 | 14 |
| Sneasler@Psychic Seed | Indeedee | 5.343 | 2.137 | 3.206 | 110 |
| Incineroar@Life Orb | Farigiraf | 4.205 | 1.034 | 3.171 | 4 |
| Blaziken@Blazikenite | Torkoal | 10.619 | 7.509 | 3.110 | 10 |
| Klefki@Light Clay | Volcarona | 13.956 | 10.876 | 3.079 | 7 |
| Politoed@Mystic Water | Golisopod | 6.942 | 3.869 | 3.073 | 53 |
| Incineroar@Chople Berry | Politoed | 4.629 | 1.562 | 3.067 | 26 |
| Sinistcha@Colbur Berry | Delphox | 13.750 | 10.694 | 3.057 | 26 |
| Hydreigon@Focus Sash | Golisopod | 4.158 | 1.130 | 3.028 | 4 |
| Corviknight@Psychic Seed | Indeedee | 14.205 | 11.182 | 3.024 | 44 |
| Rillaboom@Occa Berry | Ninetales-Alola | 4.112 | 1.105 | 3.007 | 5 |
| Incineroar@Expert Belt | Farigiraf | 4.019 | 1.034 | 2.986 | 5 |
| Indeedee-F@Colbur Berry | Rotom-Heat | 4.598 | 1.622 | 2.976 | 5 |
| Staraptor@Choice Scarf | Golisopod | 3.385 | 0.427 | 2.958 | 4 |
| Pelipper@Choice Scarf | Golisopod | 6.619 | 3.737 | 2.883 | 4 |
| Indeedee@Focus Sash | Glimmora | 4.065 | 1.225 | 2.840 | 6 |
| Farigiraf@Colbur Berry | Baxcalibur | 3.407 | 0.590 | 2.817 | 5 |
| Aerodactyl@Focus Sash | Lucario | 6.912 | 4.110 | 2.802 | 4 |
| Venusaur@Life Orb | Indeedee | 3.382 | 0.593 | 2.789 | 4 |
| Kingambit@Occa Berry | Whimsicott | 4.154 | 1.382 | 2.772 | 5 |
| Politoed@Mystic Water | Farigiraf | 5.580 | 2.830 | 2.750 | 46 |
| Milotic@Psychic Seed | Staraptor | 4.910 | 2.207 | 2.703 | 38 |
| Kingambit@Occa Berry | Volcarona | 3.672 | 0.969 | 2.703 | 6 |
| Milotic@Psychic Seed | Metagross | 5.345 | 2.646 | 2.699 | 20 |
| Baxcalibur@Life Orb | Arcanine-Hisui | 3.390 | 0.698 | 2.692 | 4 |
| Rotom-Heat@Sitrus Berry | Gardevoir | 6.131 | 3.443 | 2.688 | 4 |
| Armarouge@Psychic Seed | Golisopod | 4.567 | 1.892 | 2.675 | 4 |
| Blaziken@Blazikenite | Hatterene | 8.128 | 5.476 | 2.652 | 5 |
| Politoed@Life Orb | Golisopod | 6.505 | 3.869 | 2.636 | 19 |
| Sinistcha@Sitrus Berry | Swampert | 4.566 | 1.985 | 2.581 | 7 |
| Indeedee-F@Psychic Seed | Torkoal | 6.069 | 3.492 | 2.577 | 19 |
| Armarouge@Twisted Spoon | Golisopod | 4.441 | 1.892 | 2.549 | 16 |
| Indeedee-F@Rocky Helmet | Meganium | 4.457 | 1.909 | 2.548 | 5 |
| Raichu@Raichunite X | Archaludon | 2.726 | 0.193 | 2.533 | 5 |
| Kleavor@Focus Sash | Metagross | 7.165 | 4.632 | 2.533 | 5 |
| Garchomp@Sitrus Berry | Whimsicott | 4.839 | 2.308 | 2.530 | 5 |
| Rillaboom@Sitrus Berry | Froslass | 3.913 | 1.408 | 2.506 | 32 |
| Armarouge@Life Orb | Torkoal | 7.253 | 4.780 | 2.473 | 21 |
| Venusaur@Venusaurite | Farigiraf | 3.239 | 0.773 | 2.466 | 5 |
| Raichu@Raichunite X | Pelipper | 2.736 | 0.278 | 2.458 | 4 |
| Basculegion@Life Orb | Baxcalibur | 4.034 | 1.589 | 2.445 | 15 |
| Sableye@Light Clay | Golisopod | 4.854 | 2.409 | 2.445 | 8 |
| Kommo-o@Life Orb | Basculegion | 3.933 | 1.492 | 2.441 | 25 |
| Sneasler@Psychic Seed | Lopunny | 3.620 | 1.185 | 2.435 | 4 |
| Kingambit@Focus Sash | Sylveon | 3.561 | 1.139 | 2.422 | 48 |
| Kingambit@Focus Sash | Charizard | 3.590 | 1.171 | 2.419 | 51 |
| Aerodactyl@Focus Sash | Gardevoir | 3.442 | 1.031 | 2.411 | 5 |
| Farigiraf@Colbur Berry | Swampert | 3.881 | 1.471 | 2.410 | 8 |
| Kommo-o@Leftovers | Gengar | 6.902 | 4.494 | 2.409 | 27 |
| Sneasler@Focus Sash | Floette-Eternal | 4.014 | 1.607 | 2.407 | 61 |
| Farigiraf@Twisted Spoon | Incineroar | 3.439 | 1.034 | 2.405 | 5 |
| Sneasler@Psychic Seed | Indeedee-F | 3.527 | 1.128 | 2.399 | 204 |
| Kingambit@Life Orb | Delphox | 4.559 | 2.164 | 2.395 | 34 |
| Baxcalibur@Baxcalibrite | Ninetales-Alola | 13.464 | 11.076 | 2.388 | 14 |
| Incineroar@Chople Berry | Gengar | 4.973 | 2.610 | 2.363 | 24 |
| Indeedee-F@Colbur Berry | Gardevoir | 7.686 | 5.340 | 2.346 | 94 |
| Primarina@Life Orb | Torkoal | 4.580 | 2.290 | 2.289 | 4 |
| Garchomp@Choice Scarf | Charizard | 5.038 | 2.751 | 2.287 | 57 |
| Glimmora@Glimmoranite | Sirfetch’d | 8.056 | 5.772 | 2.284 | 5 |
| Basculegion@Life Orb | Kleavor | 3.773 | 1.505 | 2.268 | 5 |
| Corviknight@Psychic Seed | Excadrill | 13.313 | 11.045 | 2.268 | 46 |
| Venusaur@Focus Sash | Swampert | 7.864 | 5.604 | 2.260 | 21 |
| Rillaboom@Expert Belt | Pelipper | 2.779 | 0.528 | 2.250 | 8 |
| Rillaboom@Life Orb | Ninetales-Alola | 3.349 | 1.105 | 2.244 | 5 |
| Sinistcha@Sitrus Berry | Excadrill | 3.657 | 1.414 | 2.243 | 8 |
| Rillaboom@Life Orb | Annihilape | 2.918 | 0.685 | 2.233 | 4 |
| Sneasler@Psychic Seed | Armarouge | 3.181 | 0.976 | 2.205 | 65 |
| Kingambit@Focus Sash | Farigiraf | 3.589 | 1.386 | 2.203 | 57 |
| Ceruledge@Focus Sash | Sneasler | 2.411 | 0.212 | 2.198 | 4 |
| Volcarona@Sitrus Berry | Glimmora | 7.306 | 5.128 | 2.178 | 9 |
| Garchomp@Life Orb | Whimsicott | 4.478 | 2.308 | 2.169 | 25 |
| Glimmora@Glimmoranite | Typhlosion-Hisui | 7.626 | 5.464 | 2.162 | 4 |
| Indeedee-F@Psychic Seed | Blaziken | 3.611 | 1.450 | 2.160 | 6 |
| Rillaboom@Life Orb | Camerupt | 2.603 | 0.455 | 2.148 | 5 |
| Volcarona@Rocky Helmet | Swampert | 2.912 | 0.778 | 2.134 | 5 |
| Hatterene@Life Orb | Gallade | 18.286 | 16.172 | 2.114 | 5 |
| Garchomp@Life Orb | Charizard | 4.860 | 2.751 | 2.109 | 63 |
| Basculegion@Life Orb | Lycanroc-Dusk | 4.362 | 2.258 | 2.104 | 4 |
| Kleavor@Focus Sash | Whimsicott | 7.520 | 5.425 | 2.095 | 5 |
| Kingambit@Life Orb | Froslass | 4.561 | 2.486 | 2.075 | 47 |
| Lycanroc-Dusk@Focus Sash | Scovillain | 41.160 | 39.085 | 2.075 | 6 |
| Sinistcha@Rocky Helmet | Golisopod | 3.296 | 1.235 | 2.061 | 4 |
| Incineroar@Chople Berry | Golisopod | 2.707 | 0.656 | 2.051 | 32 |
| Sinistcha@Sitrus Berry | Grimmsnarl | 3.244 | 1.213 | 2.031 | 7 |
| Rillaboom@Eject Button | Incineroar | 3.273 | 1.252 | 2.021 | 73 |
| Pelipper@Sitrus Berry | Charizard | 3.743 | 1.733 | 2.009 | 53 |
| Basculegion@Life Orb | Lucario | 4.432 | 2.428 | 2.004 | 22 |
| Volcarona@Focus Sash | Kingambit | 2.958 | 0.969 | 1.988 | 4 |
| Sinistcha@Rocky Helmet | Archaludon | 3.084 | 1.101 | 1.983 | 4 |
| Incineroar@Leftovers | Farigiraf | 3.014 | 1.034 | 1.980 | 5 |
| Armarouge@Focus Sash | Lucario | 3.418 | 1.445 | 1.973 | 4 |
| Basculegion@Mystic Water | Gardevoir | 4.461 | 2.490 | 1.971 | 22 |
| Dragapult@Focus Sash | Incineroar | 2.320 | 0.356 | 1.965 | 4 |
| Rillaboom@Leftovers | Golisopod | 2.487 | 0.548 | 1.939 | 4 |
| Talonflame@Life Orb | Indeedee-F | 3.266 | 1.328 | 1.938 | 5 |
| Basculegion@Life Orb | Scovillain | 4.058 | 2.123 | 1.935 | 5 |
| Rillaboom@Expert Belt | Milotic | 3.021 | 1.090 | 1.932 | 14 |
| Armarouge@Twisted Spoon | Staraptor | 4.348 | 2.436 | 1.912 | 16 |
| Primarina@Grassy Seed | Raichu | 3.494 | 1.587 | 1.907 | 10 |
| Venusaur@Life Orb | Garchomp | 4.009 | 2.108 | 1.902 | 11 |
| Sinistcha@Colbur Berry | Floette-Eternal | 4.418 | 2.554 | 1.864 | 24 |
| Kleavor@Choice Scarf | Golisopod | 3.723 | 1.862 | 1.862 | 4 |
| Armarouge@Twisted Spoon | Metagross | 3.058 | 1.211 | 1.846 | 5 |
| Kommo-o@Life Orb | Glimmora | 4.464 | 2.632 | 1.832 | 7 |
| Ceruledge@Colbur Berry | Salamence | 2.482 | 0.673 | 1.809 | 9 |
| Rillaboom@Occa Berry | Kommo-o | 2.673 | 0.877 | 1.797 | 6 |
| Farigiraf@Colbur Berry | Pelipper | 3.235 | 1.439 | 1.796 | 19 |
| Armarouge@Life Orb | Gardevoir | 4.728 | 2.947 | 1.781 | 31 |
| Incineroar@White Herb | Kingambit | 2.575 | 0.804 | 1.771 | 20 |
| Gholdengo@Choice Scarf | Pelipper | 2.061 | 0.295 | 1.767 | 4 |
| Gallade@White Herb | Indeedee-F | 5.503 | 3.753 | 1.750 | 5 |
| Kingambit@Life Orb | Sinistcha | 3.011 | 1.263 | 1.748 | 23 |
| Indeedee@Focus Sash | Charizard | 2.515 | 0.777 | 1.739 | 11 |
| Volcarona@Sitrus Berry | Archaludon | 2.168 | 0.437 | 1.730 | 9 |
| Whimsicott@Occa Berry | Gardevoir | 4.459 | 2.729 | 1.730 | 6 |
| Indeedee-F@Colbur Berry | Scovillain | 3.439 | 1.710 | 1.729 | 4 |
| Kingambit@Focus Sash | Garchomp | 3.110 | 1.383 | 1.727 | 54 |
| Sylveon@Life Orb | Kingambit | 2.865 | 1.139 | 1.725 | 6 |
| Indeedee@Choice Scarf | Excadrill | 8.479 | 6.759 | 1.720 | 86 |
| Altaria@Haban Berry | Milotic | 5.527 | 3.816 | 1.711 | 12 |
| Altaria@Haban Berry | Indeedee-F | 5.503 | 3.799 | 1.704 | 13 |
| Rillaboom@Occa Berry | Floette-Eternal | 3.063 | 1.362 | 1.702 | 29 |
| Milotic@Sitrus Berry | Excadrill | 4.261 | 2.585 | 1.676 | 60 |
| Armarouge@Twisted Spoon | Milotic | 3.570 | 1.897 | 1.673 | 16 |
| Glimmora@Glimmoranite | Volcarona | 6.794 | 5.128 | 1.666 | 40 |
| Primarina@Life Orb | Farigiraf | 4.078 | 2.418 | 1.660 | 16 |
| Garchomp@Life Orb | Sylveon | 3.235 | 1.589 | 1.645 | 38 |
| Aegislash@Focus Sash | Garchomp | 6.348 | 4.704 | 1.645 | 6 |
| Dragapult@Life Orb | Altaria | 21.260 | 19.627 | 1.633 | 12 |
| Venusaur@Focus Sash | Pelipper | 5.773 | 4.160 | 1.613 | 39 |
| Aerodactyl@Focus Sash | Indeedee-F | 2.176 | 0.566 | 1.610 | 8 |
| Kommo-o@Leftovers | Ninetales-Alola | 5.468 | 3.868 | 1.600 | 6 |
| Sneasler@Focus Sash | Baxcalibur | 2.776 | 1.185 | 1.591 | 8 |
| Rillaboom@Expert Belt | Salamence | 2.813 | 1.233 | 1.581 | 23 |
| Aegislash@Focus Sash | Incineroar | 3.028 | 1.448 | 1.580 | 5 |
| Corviknight@Leftovers | Garchomp | 2.277 | 0.705 | 1.571 | 7 |
| Sinistcha@Sitrus Berry | Gardevoir | 1.953 | 0.387 | 1.566 | 4 |
| Sinistcha@Sitrus Berry | Pelipper | 3.029 | 1.469 | 1.559 | 11 |
| Sylveon@Life Orb | Salamence | 2.323 | 0.768 | 1.555 | 6 |
| Venusaur@Wide Lens | Garchomp | 3.649 | 2.108 | 1.541 | 7 |
| Klefki@Light Clay | Archaludon | 6.968 | 5.431 | 1.538 | 7 |
| Rillaboom@Occa Berry | Delphox | 2.153 | 0.620 | 1.533 | 7 |
| Sneasler@Psychic Seed | Annihilape | 2.006 | 0.487 | 1.519 | 10 |
| Sneasler@Psychic Seed | Typhlosion-Hisui | 2.548 | 1.030 | 1.518 | 5 |
| Garchomp@Sitrus Berry | Staraptor | 1.927 | 0.413 | 1.514 | 4 |
| Sableye@Light Clay | Archaludon | 5.507 | 3.994 | 1.513 | 9 |
| Rillaboom@Life Orb | Blaziken | 2.504 | 0.991 | 1.513 | 4 |
| Rillaboom@Eject Button | Swampert | 1.959 | 0.450 | 1.509 | 7 |
| Kingambit@Chople Berry | Kleavor | 2.387 | 0.880 | 1.507 | 5 |
| Gholdengo@Focus Sash | Sneasler | 2.411 | 0.906 | 1.505 | 4 |
| Indeedee-F@Psychic Seed | Farigiraf | 1.961 | 0.457 | 1.504 | 24 |
| Rillaboom@Occa Berry | Gengar | 2.887 | 1.398 | 1.489 | 8 |
| Incineroar@Chople Berry | Kommo-o | 2.777 | 1.307 | 1.470 | 11 |
| Indeedee-F@Rocky Helmet | Maushold | 2.815 | 1.349 | 1.466 | 9 |
| Rotom-Heat@Sitrus Berry | Golisopod | 3.337 | 1.874 | 1.463 | 4 |
| Sinistcha@Kasib Berry | Golisopod | 2.697 | 1.235 | 1.462 | 4 |
| Armarouge@Twisted Spoon | Whimsicott | 2.443 | 0.985 | 1.458 | 4 |
| Kingambit@Black Glasses | Blaziken | 3.404 | 1.947 | 1.457 | 6 |
| Scovillain@Scovillainite | Lycanroc-Dusk | 40.540 | 39.085 | 1.455 | 6 |
| Primarina@Grassy Seed | Arcanine-Hisui | 2.675 | 1.220 | 1.455 | 6 |
| Indeedee-F@Sitrus Berry | Torkoal | 4.943 | 3.492 | 1.451 | 7 |
| Farigiraf@Sitrus Berry | Grapploct | 5.209 | 3.774 | 1.434 | 5 |
| Garchomp@Garchompite Z | Volcarona | 3.598 | 2.164 | 1.433 | 50 |
| Sneasler@Psychic Seed | Rotom-Heat | 2.498 | 1.068 | 1.429 | 5 |
| Indeedee@Choice Scarf | Tyranitar | 7.366 | 5.944 | 1.422 | 92 |
| Aegislash@Focus Sash | Sylveon | 5.670 | 4.254 | 1.416 | 4 |
| Klefki@Light Clay | Garchomp | 6.348 | 4.947 | 1.401 | 7 |
| Incineroar@Sitrus Berry | Goodra-Hisui | 4.045 | 2.647 | 1.398 | 6 |
| Sneasler@White Herb | Lycanroc-Dusk | 2.523 | 1.126 | 1.398 | 6 |
| Pelipper@Focus Sash | Sableye | 4.961 | 3.566 | 1.395 | 8 |
| Milotic@Sitrus Berry | Tyranitar | 3.783 | 2.391 | 1.392 | 66 |
| Pelipper@Sitrus Berry | Hydreigon | 2.158 | 0.768 | 1.390 | 5 |
| Kleavor@Focus Sash | Basculegion | 2.893 | 1.505 | 1.388 | 6 |
| Dragonite@Life Orb | Indeedee-F | 2.119 | 0.734 | 1.385 | 5 |
| Milotic@Leftovers | Baxcalibur | 2.874 | 1.493 | 1.381 | 13 |
| Swampert@Sitrus Berry | Rillaboom | 1.829 | 0.450 | 1.379 | 4 |
| Gholdengo@Choice Scarf | Archaludon | 1.555 | 0.180 | 1.375 | 4 |
| Volcarona@Rocky Helmet | Kingambit | 2.338 | 0.969 | 1.369 | 25 |
| Sneasler@Psychic Seed | Metagross | 2.612 | 1.244 | 1.369 | 41 |
| Hatterene@Life Orb | Sirfetch’d | 11.817 | 10.451 | 1.366 | 4 |
| Incineroar@Chople Berry | Farigiraf | 2.399 | 1.034 | 1.365 | 30 |
| Gholdengo@Grassy Seed | Arcanine-Hisui | 3.337 | 1.972 | 1.364 | 39 |
| Sneasler@White Herb | Excadrill | 2.742 | 1.385 | 1.357 | 67 |
| Indeedee@Focus Sash | Whimsicott | 2.322 | 0.981 | 1.341 | 4 |
| Pelipper@Sitrus Berry | Swampert | 8.561 | 7.225 | 1.336 | 42 |
| Sneasler@Grassy Seed | Altaria | 1.976 | 0.641 | 1.334 | 4 |
| Corviknight@Psychic Seed | Tyranitar | 10.894 | 9.564 | 1.331 | 46 |
| Kingambit@Occa Berry | Basculegion | 2.697 | 1.374 | 1.323 | 11 |
| Rillaboom@Occa Berry | Politoed | 2.204 | 0.884 | 1.320 | 7 |
| Sinistcha@Sitrus Berry | Tyranitar | 2.993 | 1.674 | 1.319 | 8 |
| Incineroar@Sitrus Berry | Toxapex | 4.220 | 2.919 | 1.301 | 13 |
| Venusaur@Focus Sash | Archaludon | 4.526 | 3.244 | 1.282 | 40 |
| Basculegion@Choice Scarf | Pelipper | 3.041 | 1.759 | 1.282 | 70 |
| Rillaboom@Expert Belt | Golisopod | 1.826 | 0.548 | 1.278 | 7 |
| Rillaboom@Expert Belt | Basculegion | 2.173 | 0.906 | 1.267 | 9 |
| Gholdengo@Grassy Seed | Kommo-o | 1.686 | 0.422 | 1.264 | 4 |
| Garchomp@Sitrus Berry | Gholdengo | 1.862 | 0.606 | 1.256 | 8 |
| Rillaboom@Grassy Seed | Charizard | 1.618 | 0.365 | 1.253 | 4 |
| Politoed@Sitrus Berry | Incineroar | 2.815 | 1.562 | 1.252 | 72 |
| Indeedee@Focus Sash | Basculegion | 1.667 | 0.415 | 1.252 | 9 |
| Aegislash@Focus Sash | Charizard | 5.251 | 4.006 | 1.245 | 4 |
| Baxcalibur@Baxcalibrite | Pawmot | 7.006 | 5.764 | 1.243 | 6 |
| Indeedee-F@Rocky Helmet | Whimsicott | 2.879 | 1.641 | 1.238 | 35 |
| Armarouge@Focus Sash | Staraptor | 3.674 | 2.436 | 1.238 | 24 |
| Politoed@Sitrus Berry | Froslass | 2.307 | 1.071 | 1.236 | 14 |
| Farigiraf@Grassy Seed | Rillaboom | 1.829 | 0.598 | 1.231 | 39 |
| Blaziken@Blazikenite | Glimmora | 3.766 | 2.537 | 1.229 | 5 |
| Incineroar@Chople Berry | Archaludon | 2.079 | 0.855 | 1.224 | 24 |
| Annihilape@Choice Scarf | Venusaur | 4.186 | 2.976 | 1.210 | 5 |
| Maushold@Focus Sash | Incineroar | 2.615 | 1.409 | 1.206 | 6 |
| Toxapex@Leftovers | Venusaur | 12.521 | 11.317 | 1.203 | 6 |
| Sinistcha@Coba Berry | Indeedee-F | 2.379 | 1.182 | 1.197 | 5 |
| Pelipper@Focus Sash | Scovillain | 2.657 | 1.463 | 1.194 | 4 |
| Rillaboom@Sitrus Berry | Arcanine-Hisui | 2.739 | 1.549 | 1.190 | 78 |
| Garchomp@Garchompite Z | Typhlosion-Hisui | 2.924 | 1.738 | 1.186 | 4 |
| Archaludon@Chople Berry | Pelipper | 6.579 | 5.393 | 1.186 | 10 |
| Talonflame@Life Orb | Basculegion | 3.308 | 2.128 | 1.180 | 4 |
| Farigiraf@Sitrus Berry | Aerodactyl | 4.236 | 3.069 | 1.167 | 38 |
| Ceruledge@Colbur Berry | Kingambit | 1.708 | 0.545 | 1.163 | 5 |
| Volcarona@Rocky Helmet | Dragapult | 2.873 | 1.714 | 1.159 | 4 |
| Pawmot@Focus Sash | Mawile | 7.722 | 6.567 | 1.155 | 4 |
| Indeedee-F@Sitrus Berry | Metagross | 2.791 | 1.639 | 1.152 | 6 |
| Sneasler@White Herb | Tyranitar | 2.534 | 1.390 | 1.144 | 76 |
| Baxcalibur@Baxcalibrite | Volcarona | 6.438 | 5.296 | 1.142 | 22 |
| Garchomp@Life Orb | Glimmora | 2.861 | 1.721 | 1.140 | 13 |
| Farigiraf@Colbur Berry | Primarina | 3.557 | 2.418 | 1.139 | 5 |
| Sneasler@Psychic Seed | Venusaur | 1.719 | 0.582 | 1.138 | 20 |
| Empoleon@Life Orb | Sneasler | 1.853 | 0.719 | 1.134 | 4 |
| Garchomp@Sitrus Berry | Indeedee-F | 1.579 | 0.446 | 1.133 | 5 |
| Sableye@Light Clay | Pelipper | 4.696 | 3.566 | 1.130 | 6 |
| Annihilape@Choice Scarf | Swampert | 3.908 | 2.778 | 1.130 | 6 |
| Venusaur@Wide Lens | Incineroar | 1.898 | 0.769 | 1.129 | 6 |
| Tyranitar@Tyranitarite | Corviknight | 10.678 | 9.564 | 1.114 | 62 |
| Altaria@Haban Berry | Arcanine-Hisui | 4.282 | 3.185 | 1.097 | 12 |
| Kommo-o@Leftovers | Sinistcha | 2.539 | 1.446 | 1.093 | 7 |
| Kingambit@Occa Berry | Gardevoir | 2.182 | 1.094 | 1.088 | 5 |
| Sneasler@White Herb | Froslass | 2.814 | 1.731 | 1.083 | 60 |
| Indeedee-F@Rocky Helmet | Blastoise | 4.639 | 3.556 | 1.083 | 17 |
| Politoed@Mystic Water | Archaludon | 6.623 | 5.542 | 1.081 | 54 |
| Sneasler@Psychic Seed | Absol | 2.120 | 1.039 | 1.081 | 11 |
| Espathra@Focus Sash | Incineroar | 3.439 | 2.360 | 1.079 | 4 |
| Rillaboom@Expert Belt | Archaludon | 1.709 | 0.644 | 1.065 | 7 |
| Sneasler@Life Orb | Kingambit | 2.394 | 1.334 | 1.060 | 4 |
| Sneasler@White Herb | Scovillain | 2.192 | 1.133 | 1.059 | 7 |
| Armarouge@Focus Sash | Annihilape | 5.137 | 4.084 | 1.053 | 5 |
| Rillaboom@Life Orb | Pelipper | 1.579 | 0.528 | 1.051 | 13 |
| Excadrill@Life Orb | Milotic | 3.629 | 2.585 | 1.044 | 5 |
| Glimmora@Glimmoranite | Kommo-o | 3.674 | 2.632 | 1.042 | 12 |
| Indeedee-F@Rocky Helmet | Armarouge | 6.258 | 5.217 | 1.041 | 88 |
| Volcarona@Grassy Seed | Baxcalibur | 6.334 | 5.296 | 1.038 | 14 |
| Arcanine-Hisui@Life Orb | Sneasler | 2.128 | 1.090 | 1.038 | 4 |
| Indeedee-F@Sitrus Berry | Armarouge | 6.255 | 5.217 | 1.038 | 15 |
| Ceruledge@Grassy Seed | Staraptor | 6.233 | 5.207 | 1.026 | 52 |
| Pawmot@Focus Sash | Baxcalibur | 6.778 | 5.764 | 1.014 | 6 |
| Venusaur@Life Orb | Incineroar | 1.781 | 0.769 | 1.012 | 9 |
| Volcarona@Focus Sash | Sneasler | 1.745 | 0.738 | 1.007 | 4 |
| Blaziken@Blazikenite | Froslass | 3.083 | 2.077 | 1.006 | 5 |
| Garchomp@Choice Scarf | Sylveon | 2.595 | 1.589 | 1.006 | 26 |
| Rillaboom@Sitrus Berry | Kingambit | 1.964 | 0.959 | 1.006 | 68 |
| Sneasler@Focus Sash | Kingambit | 2.336 | 1.334 | 1.002 | 70 |
| Aerodactyl@Aerodactylite | Farigiraf | 4.069 | 3.069 | 0.999 | 38 |
| Aerodactyl@Aerodactylite | Charizard | 6.204 | 5.210 | 0.995 | 50 |
| Volcarona@Sitrus Berry | Gardevoir | 1.665 | 0.673 | 0.992 | 4 |
| Pelipper@Sitrus Berry | Dragonite | 1.845 | 0.856 | 0.989 | 5 |
| Kommo-o@Leftovers | Delphox | 2.296 | 1.308 | 0.988 | 6 |
| Blaziken@Focus Sash | Raichu | 1.555 | 0.568 | 0.987 | 5 |
| Farigiraf@Sitrus Berry | Politoed | 3.804 | 2.830 | 0.975 | 69 |
| Kingambit@Focus Sash | Torkoal | 2.748 | 1.776 | 0.972 | 10 |
| Kingambit@Focus Sash | Primarina | 1.745 | 0.775 | 0.970 | 5 |
| Rillaboom@Kebia Berry | Kingambit | 1.927 | 0.959 | 0.968 | 5 |
| Incineroar@Rocky Helmet | Floette-Eternal | 3.566 | 2.610 | 0.955 | 23 |
| Excadrill@Focus Sash | Corviknight | 11.998 | 11.045 | 0.953 | 59 |
| Politoed@Life Orb | Staraptor | 1.113 | 0.164 | 0.949 | 4 |
| Basculegion@Choice Scarf | Golisopod | 2.119 | 1.174 | 0.946 | 63 |
| Primarina@Grassy Seed | Gholdengo | 2.095 | 1.152 | 0.943 | 7 |
| Gholdengo@Grassy Seed | Salamence | 2.338 | 1.398 | 0.940 | 41 |
| Milotic@Leftovers | Absol | 1.719 | 0.781 | 0.939 | 6 |
| Aerodactyl@Focus Sash | Floette-Eternal | 1.252 | 0.319 | 0.934 | 4 |
| Incineroar@Chople Berry | Baxcalibur | 2.058 | 1.130 | 0.928 | 7 |
| Empoleon@Leftovers | Rillaboom | 1.829 | 0.901 | 0.928 | 4 |
| Milotic@Sitrus Berry | Hydreigon | 2.337 | 1.414 | 0.923 | 4 |
| Incineroar@Passho Berry | Kommo-o | 2.223 | 1.307 | 0.916 | 6 |
| Politoed@Sitrus Berry | Swampert | 2.695 | 1.782 | 0.913 | 11 |
| Torkoal@Life Orb | Kingambit | 2.689 | 1.776 | 0.913 | 4 |
| Sirfetch’d@Leek | Golisopod | 4.517 | 3.605 | 0.912 | 8 |
| Blaziken@Blazikenite | Primarina | 4.866 | 3.957 | 0.909 | 4 |
| Garchomp@Choice Scarf | Delphox | 1.839 | 0.930 | 0.909 | 7 |
| Glimmora@Focus Sash | Indeedee-F | 1.493 | 0.588 | 0.905 | 9 |
| Sinistcha@Colbur Berry | Kingambit | 2.168 | 1.263 | 0.904 | 23 |
| Garchomp@Life Orb | Kingambit | 2.284 | 1.383 | 0.901 | 56 |
| Milotic@Psychic Seed | Golisopod | 1.713 | 0.814 | 0.899 | 13 |
| Kommo-o@Life Orb | Indeedee-F | 2.772 | 1.874 | 0.897 | 21 |
| Gholdengo@Leftovers | Floette-Eternal | 2.263 | 1.368 | 0.896 | 4 |
| Sneasler@Focus Sash | Incineroar | 1.968 | 1.073 | 0.896 | 70 |
| Pelipper@Choice Scarf | Rillaboom | 1.423 | 0.528 | 0.894 | 4 |
| Garchomp@Sitrus Berry | Floette-Eternal | 2.190 | 1.296 | 0.894 | 5 |
| Venusaur@Focus Sash | Charizard | 7.980 | 7.097 | 0.882 | 64 |
| Talonflame@Focus Sash | Basculegion | 3.010 | 2.128 | 0.882 | 4 |
| Garchomp@Garchompite Z | Lucario | 2.444 | 1.562 | 0.882 | 15 |
| Volcarona@Leftovers | Basculegion | 2.866 | 1.985 | 0.881 | 5 |
| Talonflame@Focus Sash | Raichu | 1.837 | 0.957 | 0.880 | 4 |
| Aerodactyl@Aerodactylite | Sylveon | 5.709 | 4.830 | 0.879 | 44 |
| Baxcalibur@Life Orb | Raichu | 1.699 | 0.820 | 0.878 | 4 |
| Sylveon@Life Orb | Sneasler | 1.226 | 0.349 | 0.877 | 4 |
| Garchomp@Garchompite Z | Torkoal | 1.714 | 0.838 | 0.876 | 10 |
| Ninetales-Alola@Focus Sash | Incineroar | 2.070 | 1.199 | 0.871 | 8 |
| Venusaur@Focus Sash | Annihilape | 3.843 | 2.976 | 0.868 | 4 |
| Indeedee@Focus Sash | Pelipper | 1.362 | 0.499 | 0.863 | 5 |
| Indeedee-F@Rocky Helmet | Kommo-o | 2.734 | 1.874 | 0.860 | 25 |
| Garchomp@Garchompite | Kingambit | 2.240 | 1.383 | 0.857 | 4 |
| Torkoal@Charcoal | Hatterene | 10.353 | 9.496 | 0.857 | 19 |
| Basculegion@Mystic Water | Glimmora | 2.939 | 2.086 | 0.853 | 7 |
| Basculegion@Choice Scarf | Gardevoir | 3.340 | 2.490 | 0.850 | 53 |
| Rillaboom@Life Orb | Metagross | 1.529 | 0.679 | 0.850 | 7 |
| Sinistcha@Sitrus Berry | Golisopod | 2.081 | 1.235 | 0.846 | 11 |
| Rotom-Heat@Sitrus Berry | Indeedee-F | 2.465 | 1.622 | 0.843 | 4 |
| Tyranitar@Tyranitarite | Excadrill | 11.450 | 10.607 | 0.843 | 204 |
| Glimmora@Glimmoranite | Dragapult | 2.974 | 2.131 | 0.843 | 8 |
| Indeedee-F@Sitrus Berry | Kingambit | 1.777 | 0.935 | 0.843 | 17 |
| Hippowdon@Leftovers | Kingambit | 4.086 | 3.245 | 0.841 | 5 |
| Gholdengo@Grassy Seed | Froslass | 1.129 | 0.292 | 0.837 | 5 |
| Kingambit@Occa Berry | Indeedee-F | 1.767 | 0.935 | 0.832 | 9 |
| Garchomp@Choice Scarf | Sinistcha | 1.331 | 0.503 | 0.828 | 5 |
| Kingambit@Chople Berry | Whimsicott | 2.208 | 1.382 | 0.827 | 33 |
| Ninetales-Alola@Choice Scarf | Kingambit | 1.803 | 0.977 | 0.826 | 7 |
| Incineroar@Passho Berry | Swampert | 1.545 | 0.720 | 0.825 | 5 |
| Sneasler@Grassy Seed | Rillaboom | 1.829 | 1.005 | 0.824 | 420 |
| Kingambit@Life Orb | Raichu | 1.579 | 0.757 | 0.822 | 71 |
| Rillaboom@Grassy Seed | Golisopod | 1.369 | 0.548 | 0.821 | 4 |
| Aerodactyl@Focus Sash | Basculegion | 1.426 | 0.608 | 0.818 | 5 |
| Basculegion@Choice Scarf | Venusaur | 1.449 | 0.636 | 0.813 | 13 |
| Sneasler@Grassy Seed | Lucario | 1.603 | 0.792 | 0.811 | 17 |
| Indeedee@Focus Sash | Sylveon | 1.070 | 0.263 | 0.807 | 4 |
| Politoed@Sitrus Berry | Lucario | 1.505 | 0.699 | 0.806 | 4 |
| Garchomp@Choice Scarf | Glimmora | 2.527 | 1.721 | 0.806 | 9 |
| Glimmora@Focus Sash | Staraptor | 1.755 | 0.950 | 0.805 | 7 |
| Garchomp@Garchompite | Sneasler | 1.616 | 0.812 | 0.804 | 5 |
| Kommo-o@Life Orb | Indeedee | 2.526 | 1.725 | 0.801 | 6 |
| Pelipper@Choice Scarf | Archaludon | 6.194 | 5.393 | 0.801 | 4 |
| Sneasler@Grassy Seed | Floette-Eternal | 2.406 | 1.607 | 0.799 | 129 |
| Volcarona@Grassy Seed | Primarina | 1.935 | 1.141 | 0.794 | 5 |
| Blastoise@Blastoisinite | Maushold | 19.939 | 19.151 | 0.788 | 13 |
| Rillaboom@Life Orb | Delphox | 1.407 | 0.620 | 0.787 | 4 |
| Garchomp@Garchompite Z | Metagross | 2.254 | 1.467 | 0.787 | 28 |
| Sneasler@Psychic Seed | Pelipper | 1.406 | 0.622 | 0.784 | 44 |
| Sneasler@White Herb | Volcarona | 1.521 | 0.738 | 0.783 | 41 |
| Basculegion@Life Orb | Pawmot | 2.236 | 1.452 | 0.783 | 7 |
| Sinistcha@Occa Berry | Sneasler | 1.871 | 1.092 | 0.780 | 11 |
| Indeedee@Focus Sash | Archaludon | 1.185 | 0.407 | 0.778 | 6 |
| Maushold@Focus Sash | Rillaboom | 1.391 | 0.614 | 0.777 | 6 |
| Rillaboom@Life Orb | Golisopod | 1.324 | 0.548 | 0.777 | 16 |
| Farigiraf@Grassy Seed | Arcanine-Hisui | 1.230 | 0.454 | 0.776 | 11 |
| Kingambit@Focus Sash | Volcarona | 1.745 | 0.969 | 0.776 | 15 |
| Garchomp@Garchompite | Incineroar | 2.182 | 1.407 | 0.775 | 5 |
| Milotic@Sitrus Berry | Staraptor | 2.981 | 2.207 | 0.774 | 54 |
| Hydreigon@Choice Scarf | Metagross | 4.198 | 3.424 | 0.774 | 5 |
| Sneasler@Focus Sash | Ninetales-Alola | 1.612 | 0.839 | 0.773 | 4 |
| Kommo-o@Leftovers | Swampert | 1.789 | 1.019 | 0.770 | 6 |
| Incineroar@Sitrus Berry | Aegislash | 2.212 | 1.448 | 0.764 | 5 |
| Indeedee-F@Rocky Helmet | Gengar | 1.598 | 0.836 | 0.762 | 17 |
| Rillaboom@Eject Button | Froslass | 2.170 | 1.408 | 0.762 | 12 |
| Sirfetch’d@Leek | Glimmora | 6.533 | 5.772 | 0.761 | 4 |
| Indeedee-F@Colbur Berry | Corviknight | 1.408 | 0.648 | 0.760 | 6 |
| Politoed@Life Orb | Garchomp | 1.233 | 0.477 | 0.756 | 4 |
| Rillaboom@Occa Berry | Lucario | 2.282 | 1.526 | 0.756 | 4 |
| Rillaboom@Occa Berry | Incineroar | 2.007 | 1.252 | 0.755 | 42 |
| Charizard@Charizardite X | Sneasler | 1.321 | 0.567 | 0.754 | 5 |
| Baxcalibur@Life Orb | Incineroar | 1.883 | 1.130 | 0.753 | 4 |
| Garchomp@Choice Scarf | Floette-Eternal | 2.049 | 1.296 | 0.753 | 24 |
| Sinistcha@Occa Berry | Incineroar | 2.601 | 1.848 | 0.753 | 11 |
| Indeedee-F@Colbur Berry | Annihilape | 2.744 | 1.992 | 0.752 | 8 |
| Indeedee-F@Psychic Seed | Incineroar | 1.270 | 0.517 | 0.752 | 31 |
| Rotom-Heat@Sitrus Berry | Sneasler | 1.820 | 1.068 | 0.752 | 7 |
| Armarouge@Life Orb | Absol | 4.169 | 3.419 | 0.751 | 6 |
| Whimsicott@Occa Berry | Arcanine-Hisui | 1.065 | 0.315 | 0.751 | 4 |
| Indeedee@Focus Sash | Floette-Eternal | 1.043 | 0.293 | 0.750 | 5 |
| Kleavor@Focus Sash | Garchomp | 2.208 | 1.459 | 0.750 | 4 |
| Armarouge@Focus Sash | Milotic | 2.644 | 1.897 | 0.747 | 21 |
| Corviknight@Leftovers | Raichu | 1.117 | 0.376 | 0.741 | 5 |
| Corviknight@Psychic Seed | Salamence | 3.097 | 2.356 | 0.741 | 42 |
| Milotic@Grassy Seed | Rillaboom | 1.829 | 1.090 | 0.740 | 20 |
| Sneasler@White Herb | Dragonite | 2.302 | 1.564 | 0.738 | 23 |
| Volcarona@Grassy Seed | Metagross | 2.385 | 1.654 | 0.731 | 15 |
| Klefki@Light Clay | Salamence | 3.313 | 2.582 | 0.731 | 7 |
| Ninetales-Alola@Never-Melt Ice | Kingambit | 1.707 | 0.977 | 0.731 | 4 |
| Basculegion@Life Orb | Sylveon | 1.310 | 0.581 | 0.729 | 25 |
| Basculegion@Mystic Water | Indeedee-F | 1.991 | 1.264 | 0.727 | 24 |
| Sirfetch’d@Leek | Farigiraf | 3.193 | 2.468 | 0.725 | 6 |
| Farigiraf@Sitrus Berry | Grimmsnarl | 3.437 | 2.712 | 0.725 | 49 |
| Armarouge@Life Orb | Blastoise | 2.839 | 2.114 | 0.724 | 4 |
| Incineroar@Sitrus Berry | Floette-Eternal | 3.333 | 2.610 | 0.723 | 230 |
| Basculegion@Focus Sash | Indeedee-F | 1.985 | 1.264 | 0.721 | 9 |
| Kingambit@Life Orb | Floette-Eternal | 2.117 | 1.397 | 0.720 | 47 |
| Swampert@Swampertite | Sableye | 9.060 | 8.341 | 0.719 | 10 |
| Hatterene@Life Orb | Blaziken | 6.192 | 5.476 | 0.716 | 5 |
| Politoed@Sitrus Berry | Rillaboom | 1.597 | 0.884 | 0.713 | 77 |
| Kingambit@Chople Berry | Basculegion | 2.086 | 1.374 | 0.711 | 93 |
| Garchomp@Life Orb | Talonflame | 2.707 | 1.997 | 0.710 | 4 |
| Sneasler@Grassy Seed | Arcanine-Hisui | 1.800 | 1.090 | 0.710 | 153 |
| Kommo-o@Leftovers | Incineroar | 2.016 | 1.307 | 0.708 | 41 |
| Whimsicott@Occa Berry | Garchomp | 3.017 | 2.308 | 0.708 | 7 |
| Indeedee-F@Colbur Berry | Pelipper | 1.890 | 1.193 | 0.697 | 36 |
| Indeedee-F@Sitrus Berry | Gardevoir | 6.035 | 5.340 | 0.695 | 18 |
| Kingambit@Life Orb | Arcanine-Hisui | 1.813 | 1.118 | 0.695 | 68 |
| Kingambit@Black Glasses | Indeedee-F | 1.625 | 0.935 | 0.691 | 30 |
| Politoed@Sitrus Berry | Kommo-o | 2.519 | 1.829 | 0.690 | 10 |
| Glimmora@Glimmoranite | Pawmot | 2.428 | 1.740 | 0.688 | 6 |
| Milotic@Psychic Seed | Whimsicott | 0.963 | 0.279 | 0.684 | 4 |
| Basculegion@Life Orb | Kingambit | 2.058 | 1.374 | 0.684 | 87 |
| Torkoal@Charcoal | Blaziken | 8.187 | 7.509 | 0.678 | 11 |
| Charizard@Charizardite X | Incineroar | 1.392 | 0.716 | 0.677 | 4 |
| Sneasler@Grassy Seed | Gholdengo | 1.582 | 0.906 | 0.676 | 190 |
| Kommo-o@Leftovers | Politoed | 2.504 | 1.829 | 0.674 | 13 |
| Garchomp@Life Orb | Farigiraf | 2.408 | 1.735 | 0.673 | 35 |
| Milotic@Sitrus Berry | Gholdengo | 2.212 | 1.542 | 0.670 | 100 |
| Annihilape@Choice Scarf | Pelipper | 2.313 | 1.644 | 0.669 | 9 |
| Incineroar@Passho Berry | Froslass | 1.205 | 0.538 | 0.667 | 7 |
| Incineroar@Sitrus Berry | Rotom-Wash | 1.926 | 1.261 | 0.666 | 5 |
| Pelipper@Damp Rock | Archaludon | 6.058 | 5.393 | 0.665 | 5 |
| Farigiraf@Colbur Berry | Camerupt | 5.921 | 5.257 | 0.664 | 7 |
| Indeedee-F@Rocky Helmet | Gallade | 4.416 | 3.753 | 0.663 | 5 |
| Espathra@Grassy Seed | Rillaboom | 1.829 | 1.167 | 0.662 | 6 |
| Whimsicott@Fairy Feather | Kingambit | 2.043 | 1.382 | 0.661 | 5 |
| Basculegion@Choice Scarf | Archaludon | 1.642 | 0.981 | 0.661 | 50 |
| Farigiraf@Grassy Seed | Garchomp | 2.396 | 1.735 | 0.661 | 14 |
| Rillaboom@Kebia Berry | Raichu | 2.267 | 1.606 | 0.660 | 6 |
| Volcarona@Grassy Seed | Incineroar | 1.874 | 1.216 | 0.658 | 54 |
| Rillaboom@Sitrus Berry | Ninetales-Alola | 1.760 | 1.105 | 0.654 | 5 |
| Incineroar@Chople Berry | Swampert | 1.369 | 0.720 | 0.650 | 6 |
| Indeedee-F@Rocky Helmet | Metagross | 2.289 | 1.639 | 0.650 | 30 |
| Basculegion@Mystic Water | Indeedee | 1.064 | 0.415 | 0.649 | 4 |
| Incineroar@Rocky Helmet | Whimsicott | 1.426 | 0.783 | 0.643 | 4 |
| Sneasler@Grassy Seed | Raichu | 1.416 | 0.779 | 0.638 | 142 |
| Charizard@Charizardite X | Rillaboom | 1.002 | 0.365 | 0.638 | 5 |
| Farigiraf@Grassy Seed | Raichu | 1.081 | 0.443 | 0.638 | 9 |
| Garchomp@Life Orb | Gardevoir | 1.205 | 0.569 | 0.637 | 11 |
| Sinistcha@Sitrus Berry | Staraptor | 1.166 | 0.530 | 0.636 | 4 |
| Sinistcha@Sitrus Berry | Basculegion | 0.997 | 0.361 | 0.636 | 4 |
| Basculegion@Life Orb | Delphox | 1.088 | 0.453 | 0.635 | 6 |
| Basculegion@Life Orb | Volcarona | 2.619 | 1.985 | 0.634 | 30 |
| Rillaboom@Life Orb | Dragonite | 1.712 | 1.079 | 0.632 | 4 |
| Volcarona@Leftovers | Sneasler | 1.369 | 0.738 | 0.632 | 6 |
| Gholdengo@Grassy Seed | Raichu | 2.895 | 2.265 | 0.629 | 39 |
| Rillaboom@Sitrus Berry | Dragapult | 0.950 | 0.321 | 0.629 | 4 |
| Farigiraf@Colbur Berry | Incineroar | 1.660 | 1.034 | 0.626 | 27 |
| Annihilape@Choice Scarf | Archaludon | 2.166 | 1.540 | 0.626 | 11 |
| Rillaboom@Sitrus Berry | Salamence | 1.858 | 1.233 | 0.625 | 84 |
| Armarouge@Life Orb | Venusaur | 1.098 | 0.473 | 0.625 | 4 |
| Farigiraf@Sitrus Berry | Charizard | 3.021 | 2.397 | 0.624 | 105 |
| Swampert@Swampertite | Pelipper | 7.848 | 7.225 | 0.623 | 99 |
| Glimmora@Glimmoranite | Ninetales-Alola | 2.983 | 2.361 | 0.622 | 5 |
| Indeedee-F@Rocky Helmet | Staraptor | 1.652 | 1.030 | 0.622 | 45 |
| Torkoal@Charcoal | Gallade | 7.501 | 6.880 | 0.621 | 4 |
| Primarina@Grassy Seed | Rillaboom | 1.829 | 1.209 | 0.620 | 11 |
| Incineroar@Rocky Helmet | Salamence | 1.291 | 0.671 | 0.620 | 21 |
| Indeedee@Choice Scarf | Metagross | 4.167 | 3.547 | 0.619 | 30 |
| Kingambit@Chople Berry | Salamence | 1.813 | 1.197 | 0.616 | 161 |
| Sneasler@Psychic Seed | Torkoal | 1.372 | 0.756 | 0.616 | 17 |
| Archaludon@Chople Berry | Rillaboom | 1.258 | 0.644 | 0.614 | 10 |
| Incineroar@Chople Berry | Pelipper | 1.134 | 0.523 | 0.612 | 11 |
| Pawmot@Focus Sash | Talonflame | 4.071 | 3.462 | 0.609 | 4 |
| Incineroar@Sitrus Berry | Vanilluxe | 1.747 | 1.143 | 0.604 | 4 |
| Sneasler@Focus Sash | Garchomp | 1.414 | 0.812 | 0.602 | 29 |
| Basculegion@Focus Sash | Sylveon | 1.182 | 0.581 | 0.602 | 4 |
| Toxapex@Leftovers | Charizard | 6.250 | 5.649 | 0.601 | 11 |
| Ceruledge@Grassy Seed | Milotic | 5.565 | 4.969 | 0.597 | 57 |
| Indeedee-F@Colbur Berry | Golisopod | 2.040 | 1.445 | 0.596 | 46 |
| Basculegion@Life Orb | Dragonite | 1.805 | 1.211 | 0.594 | 8 |
| Indeedee-F@Colbur Berry | Venusaur | 1.820 | 1.226 | 0.593 | 12 |
| Indeedee-F@Colbur Berry | Mawile | 2.037 | 1.446 | 0.591 | 4 |
| Kingambit@Black Glasses | Incineroar | 1.394 | 0.804 | 0.591 | 41 |
| Incineroar@Chople Berry | Milotic | 1.035 | 0.446 | 0.589 | 16 |
| Incineroar@Sitrus Berry | Blastoise | 2.221 | 1.632 | 0.588 | 20 |
| Hydreigon@Choice Scarf | Staraptor | 1.797 | 1.209 | 0.588 | 5 |
| Garchomp@Garchompite Z | Golisopod | 1.278 | 0.693 | 0.586 | 36 |
| Indeedee-F@Rocky Helmet | Talonflame | 1.914 | 1.328 | 0.586 | 7 |
| Primarina@Mystic Water | Gholdengo | 1.735 | 1.152 | 0.584 | 4 |
| Kingambit@Focus Sash | Armarouge | 1.455 | 0.872 | 0.583 | 12 |
| Indeedee-F@Colbur Berry | Lucario | 1.357 | 0.775 | 0.582 | 6 |
| Garchomp@Sitrus Berry | Charizard | 3.332 | 2.751 | 0.581 | 8 |
| Dragapult@Life Orb | Armarouge | 7.549 | 6.969 | 0.580 | 36 |
| Incineroar@Lum Berry | Rillaboom | 1.829 | 1.252 | 0.577 | 4 |
| Rillaboom@Life Orb | Lucario | 2.104 | 1.526 | 0.577 | 4 |
| Glimmora@Focus Sash | Raichu | 1.023 | 0.454 | 0.569 | 8 |
| Rillaboom@Kebia Berry | Sneasler | 1.574 | 1.005 | 0.569 | 7 |
| Indeedee-F@Colbur Berry | Sneasler | 1.697 | 1.128 | 0.569 | 121 |
| Milotic@Grassy Seed | Kingambit | 0.828 | 0.260 | 0.568 | 4 |
| Kingambit@Chople Berry | Dragonite | 1.337 | 0.773 | 0.564 | 10 |
| Sneasler@Psychic Seed | Milotic | 1.194 | 0.632 | 0.562 | 60 |
| Rillaboom@Sitrus Berry | Basculegion | 1.467 | 0.906 | 0.561 | 35 |
| Whimsicott@Occa Berry | Kingambit | 1.942 | 1.382 | 0.560 | 7 |
| Pelipper@Focus Sash | Gardevoir | 1.732 | 1.175 | 0.557 | 23 |
| Sinistcha@Colbur Berry | Incineroar | 2.404 | 1.848 | 0.556 | 32 |
| Basculegion@Focus Sash | Floette-Eternal | 1.853 | 1.298 | 0.555 | 4 |
| Garchomp@Sitrus Berry | Basculegion | 1.702 | 1.150 | 0.552 | 5 |
| Goodra-Hisui@Leftovers | Floette-Eternal | 5.768 | 5.218 | 0.550 | 5 |
| Glimmora@Glimmoranite | Baxcalibur | 2.706 | 2.156 | 0.550 | 5 |
| Sneasler@Psychic Seed | Blastoise | 2.054 | 1.505 | 0.549 | 10 |
| Rillaboom@Life Orb | Froslass | 1.957 | 1.408 | 0.549 | 12 |
| Garchomp@Choice Scarf | Whimsicott | 2.855 | 2.308 | 0.547 | 14 |
| Glimmora@Focus Sash | Milotic | 0.815 | 0.268 | 0.546 | 4 |
| Sinistcha@Colbur Berry | Excadrill | 1.959 | 1.414 | 0.546 | 8 |
| Sneasler@Psychic Seed | Hydreigon | 1.457 | 0.913 | 0.544 | 6 |
| Sneasler@White Herb | Baxcalibur | 1.727 | 1.185 | 0.542 | 16 |
| Kleavor@Focus Sash | Kingambit | 1.421 | 0.880 | 0.541 | 4 |
| Whimsicott@Fairy Feather | Sneasler | 0.932 | 0.392 | 0.540 | 4 |
| Rillaboom@Miracle Seed | Goodra-Hisui | 1.946 | 1.408 | 0.538 | 6 |
| Torkoal@Charcoal | Mawile | 6.487 | 5.950 | 0.537 | 6 |
| Tyranitar@Tyranitarite | Indeedee | 6.480 | 5.944 | 0.537 | 99 |
| Sinistcha@Coba Berry | Sneasler | 1.628 | 1.092 | 0.536 | 9 |
| Pelipper@Focus Sash | Basculegion | 2.295 | 1.759 | 0.536 | 62 |
| Garchomp@Garchompite Z | Pawmot | 1.507 | 0.973 | 0.533 | 7 |
| Rillaboom@Leftovers | Incineroar | 1.778 | 1.252 | 0.527 | 7 |
| Blaziken@Blazikenite | Kingambit | 2.473 | 1.947 | 0.526 | 19 |
| Maushold@Chople Berry | Indeedee-F | 1.874 | 1.349 | 0.525 | 7 |
| Indeedee-F@Psychic Seed | Kingambit | 1.455 | 0.935 | 0.520 | 33 |
| Armarouge@Life Orb | Tyranitar | 1.237 | 0.719 | 0.519 | 9 |
| Rillaboom@Kebia Berry | Salamence | 1.751 | 1.233 | 0.518 | 5 |
| Maushold@Chople Berry | Sneasler | 1.631 | 1.113 | 0.518 | 14 |
| Pawmot@Focus Sash | Primarina | 3.463 | 2.945 | 0.518 | 5 |
| Incineroar@Sitrus Berry | Sinistcha | 2.359 | 1.848 | 0.511 | 57 |
| Whimsicott@Focus Sash | Sirfetch’d | 5.791 | 5.280 | 0.511 | 5 |
| Sinistcha@Sitrus Berry | Archaludon | 1.610 | 1.101 | 0.509 | 8 |
| Toxapex@Leftovers | Garchomp | 5.286 | 4.778 | 0.508 | 12 |
| Gholdengo@Choice Scarf | Floette-Eternal | 1.869 | 1.368 | 0.501 | 6 |
| Rillaboom@Sitrus Berry | Sneasler | 1.506 | 1.005 | 0.501 | 90 |
| Basculegion@Focus Sash | Incineroar | 1.436 | 0.937 | 0.500 | 10 |
| Milotic@Grassy Seed | Arcanine-Hisui | 1.613 | 1.114 | 0.498 | 6 |
| Indeedee-F@Rocky Helmet | Milotic | 1.637 | 1.139 | 0.498 | 56 |
| Sneasler@White Herb | Blaziken | 0.870 | 0.374 | 0.496 | 5 |
| Corviknight@Leftovers | Indeedee-F | 1.144 | 0.648 | 0.496 | 4 |
| Sinistcha@Sitrus Berry | Salamence | 0.910 | 0.415 | 0.495 | 8 |
| Farigiraf@Grassy Seed | Salamence | 0.949 | 0.455 | 0.494 | 13 |
| Venusaur@Focus Sash | Gardevoir | 2.382 | 1.891 | 0.492 | 13 |
| Incineroar@Rocky Helmet | Sylveon | 1.038 | 0.546 | 0.492 | 7 |
| Sneasler@White Herb | Indeedee | 2.625 | 2.137 | 0.488 | 57 |
| Milotic@Grassy Seed | Gholdengo | 2.029 | 1.542 | 0.487 | 11 |
| Milotic@Leftovers | Maushold | 1.098 | 0.611 | 0.486 | 4 |
| Swampert@Swampertite | Grimmsnarl | 6.124 | 5.638 | 0.486 | 39 |
| Kingambit@Occa Berry | Floette-Eternal | 1.880 | 1.397 | 0.483 | 5 |
| Swampert@Swampertite | Venusaur | 6.087 | 5.604 | 0.483 | 24 |
| Swampert@Swampertite | Archaludon | 6.047 | 5.568 | 0.480 | 99 |
| Hydreigon@Choice Scarf | Milotic | 1.893 | 1.414 | 0.479 | 8 |
| Talonflame@Focus Sash | Rillaboom | 1.115 | 0.636 | 0.479 | 5 |
| Rillaboom@Grassy Seed | Garchomp | 1.277 | 0.799 | 0.478 | 4 |
| Indeedee-F@Rocky Helmet | Delphox | 1.566 | 1.090 | 0.476 | 14 |
| Kingambit@Life Orb | Sneasler | 1.810 | 1.334 | 0.476 | 139 |
| Indeedee-F@Colbur Berry | Basculegion | 1.739 | 1.264 | 0.475 | 46 |
| Volcarona@Sitrus Berry | Indeedee-F | 0.821 | 0.346 | 0.475 | 5 |
| Kingambit@Black Glasses | Gengar | 1.220 | 0.746 | 0.474 | 8 |
| Kingambit@Focus Sash | Glimmora | 2.021 | 1.548 | 0.472 | 10 |
| Lycanroc-Dusk@Focus Sash | Froslass | 9.351 | 8.880 | 0.471 | 9 |
| Garchomp@Sitrus Berry | Raichu | 1.006 | 0.535 | 0.471 | 4 |
| Indeedee-F@Rocky Helmet | Sinistcha | 1.652 | 1.182 | 0.470 | 17 |
| Armarouge@Life Orb | Swampert | 1.017 | 0.548 | 0.470 | 5 |
| Farigiraf@Sitrus Berry | Sylveon | 2.255 | 1.786 | 0.469 | 78 |
| Armarouge@Life Orb | Excadrill | 1.220 | 0.752 | 0.467 | 7 |
| Sneasler@Psychic Seed | Charizard | 1.034 | 0.567 | 0.467 | 38 |
| Garchomp@Life Orb | Indeedee-F | 0.912 | 0.446 | 0.467 | 19 |
| Incineroar@Sitrus Berry | Volcarona | 1.682 | 1.216 | 0.466 | 62 |
| Incineroar@Rocky Helmet | Gholdengo | 1.409 | 0.944 | 0.465 | 22 |
| Baxcalibur@Baxcalibrite | Glimmora | 2.621 | 2.156 | 0.465 | 6 |
| Indeedee-F@Psychic Seed | Dragonite | 1.197 | 0.734 | 0.463 | 5 |
| Volcarona@Sitrus Berry | Salamence | 1.305 | 0.842 | 0.463 | 12 |
| Politoed@Choice Scarf | Archaludon | 6.005 | 5.542 | 0.463 | 4 |
| Sneasler@Psychic Seed | Golisopod | 0.963 | 0.501 | 0.463 | 40 |
| Kingambit@Focus Sash | Blastoise | 1.619 | 1.157 | 0.462 | 4 |
| Torkoal@Charcoal | Annihilape | 5.572 | 5.111 | 0.461 | 8 |
| Excadrill@Life Orb | Sneasler | 1.846 | 1.385 | 0.461 | 8 |
| Sableye@Roseli Berry | Pelipper | 4.026 | 3.566 | 0.460 | 4 |
| Incineroar@Sitrus Berry | Dragonite | 2.027 | 1.567 | 0.460 | 31 |
| Garchomp@Garchompite Z | Politoed | 0.934 | 0.477 | 0.458 | 12 |
| Kommo-o@Leftovers | Kingambit | 1.434 | 0.977 | 0.458 | 23 |
| Hatterene@Life Orb | Torkoal | 9.953 | 9.496 | 0.457 | 18 |
| Sinistcha@Colbur Berry | Garchomp | 0.954 | 0.503 | 0.452 | 7 |
| Indeedee@Choice Scarf | Salamence | 2.598 | 2.147 | 0.450 | 105 |
| Kommo-o@Leftovers | Froslass | 1.439 | 0.989 | 0.449 | 6 |
| Hydreigon@Choice Scarf | Arcanine-Hisui | 1.372 | 0.923 | 0.449 | 6 |
| Whimsicott@Focus Sash | Floette-Eternal | 2.085 | 1.636 | 0.449 | 31 |
| Excadrill@Life Orb | Salamence | 3.028 | 2.581 | 0.447 | 9 |
| Rillaboom@Miracle Seed | Ceruledge | 2.134 | 1.688 | 0.445 | 66 |
| Incineroar@Sitrus Berry | Delphox | 2.370 | 1.927 | 0.443 | 51 |
| Milotic@Leftovers | Volcarona | 1.140 | 0.698 | 0.442 | 16 |
| Delphox@Delphoxite | Maushold | 10.045 | 9.604 | 0.441 | 16 |
| Vanilluxe@Choice Scarf | Raichu | 2.210 | 1.769 | 0.441 | 5 |
| Incineroar@Chople Berry | Whimsicott | 1.217 | 0.783 | 0.434 | 8 |
| Politoed@Life Orb | Kingambit | 0.609 | 0.176 | 0.433 | 4 |
| Rillaboom@Life Orb | Farigiraf | 1.031 | 0.598 | 0.432 | 14 |
| Volcarona@Grassy Seed | Floette-Eternal | 1.700 | 1.267 | 0.432 | 19 |
| Basculegion@Mystic Water | Arcanine-Hisui | 0.963 | 0.533 | 0.430 | 16 |
| Indeedee-F@Sitrus Berry | Charizard | 1.228 | 0.799 | 0.429 | 6 |
| Armarouge@Life Orb | Annihilape | 4.512 | 4.084 | 0.427 | 6 |
| Pelipper@Focus Sash | Golisopod | 4.162 | 3.737 | 0.426 | 102 |
| Primarina@Life Orb | Kingambit | 1.201 | 0.775 | 0.425 | 8 |
| Rillaboom@Occa Berry | Charizard | 0.790 | 0.365 | 0.425 | 9 |
| Pelipper@Sitrus Berry | Annihilape | 2.069 | 1.644 | 0.425 | 4 |
| Sneasler@Psychic Seed | Tyranitar | 1.815 | 1.390 | 0.425 | 58 |
| Whimsicott@Fairy Feather | Basculegion | 3.193 | 2.769 | 0.424 | 5 |
| Garchomp@Garchompite Z | Rillaboom | 1.215 | 0.799 | 0.416 | 143 |
| Kingambit@Focus Sash | Blaziken | 2.362 | 1.947 | 0.414 | 5 |
| Archaludon@Leftovers | Klefki | 5.844 | 5.431 | 0.413 | 7 |
| Basculegion@Choice Scarf | Indeedee-F | 1.676 | 1.264 | 0.412 | 66 |
| Farigiraf@Sitrus Berry | Sirfetch’d | 2.880 | 2.468 | 0.412 | 6 |
| Blastoise@Blastoisinite | Sinistcha | 10.327 | 9.918 | 0.408 | 20 |
| Aerodactyl@Focus Sash | Incineroar | 1.360 | 0.952 | 0.408 | 8 |
| Basculegion@Mystic Water | Kingambit | 1.783 | 1.374 | 0.408 | 29 |
| Indeedee-F@Sitrus Berry | Sneasler | 1.533 | 1.128 | 0.405 | 26 |
| Garchomp@Garchompite Z | Sneasler | 1.216 | 0.812 | 0.404 | 112 |
| Ninetales-Alola@Light Clay | Gholdengo | 0.967 | 0.564 | 0.404 | 5 |
| Torkoal@Charcoal | Typhlosion-Hisui | 4.878 | 4.474 | 0.404 | 4 |
| Volcarona@Grassy Seed | Garchomp | 2.567 | 2.164 | 0.403 | 40 |
| Incineroar@Sitrus Berry | Aerodactyl | 1.354 | 0.952 | 0.402 | 26 |
| Basculegion@Life Orb | Blaziken | 1.502 | 1.100 | 0.401 | 4 |
| Pawmot@Focus Sash | Sinistcha | 2.680 | 2.279 | 0.401 | 5 |
| Sneasler@Psychic Seed | Excadrill | 1.786 | 1.385 | 0.401 | 48 |
| Volcarona@Grassy Seed | Ninetales-Alola | 2.509 | 2.109 | 0.400 | 5 |
| Incineroar@Chople Berry | Froslass | 0.937 | 0.538 | 0.399 | 5 |
| Volcarona@Grassy Seed | Froslass | 1.972 | 1.575 | 0.398 | 10 |
| Milotic@Sitrus Berry | Raichu | 1.594 | 1.199 | 0.395 | 55 |
| Armarouge@Life Orb | Kingambit | 1.265 | 0.872 | 0.392 | 31 |
| Sneasler@Grassy Seed | Incineroar | 1.464 | 1.073 | 0.391 | 186 |
| Glimmora@Focus Sash | Garchomp | 2.112 | 1.721 | 0.391 | 10 |
| Annihilape@Choice Scarf | Charizard | 1.944 | 1.554 | 0.389 | 8 |
| Garchomp@Garchompite Z | Incineroar | 1.795 | 1.407 | 0.388 | 114 |
| Indeedee-F@Rocky Helmet | Aerodactyl | 0.951 | 0.566 | 0.385 | 7 |
| Aerodactyl@Aerodactylite | Kingambit | 2.626 | 2.246 | 0.380 | 42 |
| Basculegion@Choice Scarf | Ninetales-Alola | 1.076 | 0.696 | 0.379 | 4 |
| Incineroar@Sitrus Berry | Absol | 1.494 | 1.116 | 0.378 | 12 |
| Milotic@Leftovers | Dragonite | 0.838 | 0.464 | 0.374 | 6 |
| Blastoise@Blastoisinite | Delphox | 9.440 | 9.066 | 0.373 | 17 |
| Incineroar@Rocky Helmet | Milotic | 0.818 | 0.446 | 0.372 | 7 |
| Ninetales-Alola@Light Clay | Arcanine-Hisui | 1.027 | 0.655 | 0.372 | 4 |
| Volcarona@Grassy Seed | Raichu | 1.578 | 1.206 | 0.372 | 37 |
| Pelipper@Focus Sash | Indeedee-F | 1.565 | 1.193 | 0.372 | 51 |
| Primarina@Life Orb | Golisopod | 1.266 | 0.896 | 0.371 | 5 |
| Kingambit@Life Orb | Incineroar | 1.175 | 0.804 | 0.371 | 64 |
| Incineroar@Sitrus Berry | Altaria | 1.503 | 1.133 | 0.369 | 4 |
| Milotic@Leftovers | Farigiraf | 0.768 | 0.400 | 0.368 | 28 |
| Rillaboom@Sitrus Berry | Baxcalibur | 1.523 | 1.158 | 0.365 | 5 |
| Sirfetch’d@Leek | Gholdengo | 0.919 | 0.555 | 0.364 | 4 |
| Archaludon@Leftovers | Grimmsnarl | 6.217 | 5.855 | 0.363 | 126 |
| Armarouge@Life Orb | Pelipper | 1.108 | 0.750 | 0.358 | 12 |
| Glimmora@Glimmoranite | Whimsicott | 5.050 | 4.693 | 0.357 | 25 |
| Rillaboom@Miracle Seed | Staraptor | 1.643 | 1.286 | 0.356 | 230 |
| Sinistcha@Occa Berry | Floette-Eternal | 2.910 | 2.554 | 0.355 | 5 |
| Baxcalibur@Baxcalibrite | Sinistcha | 2.004 | 1.648 | 0.355 | 5 |
| Venusaur@Life Orb | Sneasler | 0.935 | 0.582 | 0.354 | 7 |
| Volcarona@Grassy Seed | Rillaboom | 1.829 | 1.476 | 0.354 | 98 |
| Armarouge@Focus Sash | Absol | 3.771 | 3.419 | 0.353 | 4 |
| Indeedee@Focus Sash | Kingambit | 0.579 | 0.226 | 0.352 | 5 |
| Milotic@Leftovers | Lucario | 1.100 | 0.748 | 0.352 | 6 |
| Excadrill@Focus Sash | Rotom-Heat | 4.414 | 4.064 | 0.351 | 5 |
| Corviknight@Psychic Seed | Sneasler | 2.356 | 2.008 | 0.348 | 45 |
| Typhlosion-Hisui@Choice Scarf | Basculegion | 2.204 | 1.857 | 0.347 | 5 |
| Excadrill@Focus Sash | Indeedee | 7.105 | 6.759 | 0.346 | 93 |
| Sinistcha@Kasib Berry | Incineroar | 2.193 | 1.848 | 0.346 | 6 |
| Gholdengo@Life Orb | Ceruledge | 2.930 | 2.584 | 0.345 | 57 |
| Tyranitar@Choice Scarf | Rillaboom | 0.932 | 0.587 | 0.345 | 4 |
| Milotic@Leftovers | Camerupt | 0.959 | 0.616 | 0.343 | 5 |
| Archaludon@Leftovers | Vivillon | 4.855 | 4.512 | 0.343 | 23 |
| Garchomp@Choice Scarf | Kingambit | 1.726 | 1.383 | 0.343 | 38 |
| Basculegion@Choice Scarf | Metagross | 1.117 | 0.774 | 0.343 | 12 |
| Incineroar@Sitrus Berry | Primarina | 1.532 | 1.190 | 0.342 | 23 |
| Pelipper@Focus Sash | Gengar | 0.865 | 0.525 | 0.340 | 8 |
| Glimmora@Glimmoranite | Indeedee | 1.565 | 1.225 | 0.340 | 8 |
| Delphox@Delphoxite | Sinistcha | 11.033 | 10.694 | 0.339 | 54 |
| Whimsicott@Focus Sash | Torkoal | 1.460 | 1.125 | 0.335 | 5 |
| Sneasler@Grassy Seed | Salamence | 1.824 | 1.488 | 0.335 | 237 |
| Pawmot@Focus Sash | Froslass | 2.241 | 1.906 | 0.335 | 8 |
| Toxapex@Leftovers | Sylveon | 3.481 | 3.146 | 0.335 | 5 |
| Glimmora@Focus Sash | Gholdengo | 0.722 | 0.390 | 0.332 | 6 |
| Garchomp@Garchompite Z | Archaludon | 1.159 | 0.828 | 0.331 | 30 |
| Aerodactyl@Aerodactylite | Garchomp | 4.001 | 3.670 | 0.331 | 41 |
| Kingambit@Chople Berry | Swampert | 0.586 | 0.256 | 0.330 | 6 |
| Sneasler@Psychic Seed | Kommo-o | 0.642 | 0.313 | 0.329 | 7 |
| Kingambit@Life Orb | Rillaboom | 1.287 | 0.959 | 0.329 | 132 |
| Incineroar@Sitrus Berry | Lucario | 2.375 | 2.046 | 0.329 | 40 |
| Sneasler@White Herb | Talonflame | 1.017 | 0.688 | 0.328 | 4 |
| Indeedee-F@Rocky Helmet | Absol | 2.332 | 2.004 | 0.328 | 10 |
| Sneasler@Psychic Seed | Dragapult | 0.691 | 0.365 | 0.327 | 7 |
| Ceruledge@Grassy Seed | Raichu | 3.527 | 3.203 | 0.325 | 56 |
| Typhlosion-Hisui@Choice Scarf | Garchomp | 2.063 | 1.738 | 0.325 | 5 |
| Kommo-o@Leftovers | Rillaboom | 1.200 | 0.877 | 0.324 | 46 |
| Milotic@Leftovers | Incineroar | 0.769 | 0.446 | 0.323 | 54 |
| Dragapult@Life Orb | Indeedee-F | 4.209 | 3.886 | 0.323 | 61 |
| Blaziken@Blazikenite | Farigiraf | 2.068 | 1.745 | 0.323 | 10 |
| Kingambit@Black Glasses | Farigiraf | 1.709 | 1.386 | 0.323 | 24 |
| Sinistcha@Sitrus Berry | Indeedee-F | 1.501 | 1.182 | 0.320 | 8 |
| Alakazam@Alakazite | Sneasler | 1.416 | 1.099 | 0.317 | 4 |
| Metagross@Metagrossite | Altaria | 11.844 | 11.529 | 0.316 | 12 |
| Volcarona@Grassy Seed | Basculegion | 2.300 | 1.985 | 0.315 | 34 |
| Pelipper@Sitrus Berry | Archaludon | 5.708 | 5.393 | 0.315 | 96 |
| Garchomp@Garchompite Z | Indeedee | 0.875 | 0.560 | 0.315 | 13 |
| Kingambit@Chople Berry | Gardevoir | 1.407 | 1.094 | 0.312 | 32 |
| Garchomp@Garchompite Z | Pelipper | 1.142 | 0.830 | 0.312 | 23 |
| Sinistcha@Sitrus Berry | Gholdengo | 0.568 | 0.256 | 0.312 | 5 |
| Whimsicott@Focus Sash | Charizard | 2.862 | 2.551 | 0.312 | 42 |
| Indeedee-F@Sitrus Berry | Milotic | 1.450 | 1.139 | 0.311 | 8 |
| Sneasler@Psychic Seed | Whimsicott | 0.703 | 0.392 | 0.311 | 11 |
| Rillaboom@Life Orb | Archaludon | 0.954 | 0.644 | 0.310 | 10 |
| Farigiraf@Grassy Seed | Incineroar | 1.343 | 1.034 | 0.310 | 15 |
| Volcarona@Leftovers | Salamence | 1.152 | 0.842 | 0.310 | 4 |
| Sinistcha@Colbur Berry | Milotic | 1.014 | 0.704 | 0.310 | 9 |
| Garchomp@Sitrus Berry | Kingambit | 1.693 | 1.383 | 0.310 | 8 |
| Kingambit@Black Glasses | Golisopod | 0.534 | 0.225 | 0.309 | 8 |
| Dragapult@Life Orb | Metagross | 4.024 | 3.714 | 0.309 | 20 |
| Basculegion@Choice Scarf | Armarouge | 0.694 | 0.385 | 0.309 | 10 |
| Vivillon@Focus Sash | Gengar | 15.116 | 14.808 | 0.308 | 29 |
| Whimsicott@Occa Berry | Staraptor | 1.708 | 1.401 | 0.307 | 4 |
| Kingambit@Chople Berry | Arcanine-Hisui | 1.424 | 1.118 | 0.306 | 84 |
| Indeedee-F@Sitrus Berry | Tyranitar | 1.129 | 0.824 | 0.306 | 4 |
| Milotic@Leftovers | Indeedee | 2.013 | 1.708 | 0.304 | 28 |
| Indeedee@Choice Scarf | Milotic | 2.011 | 1.708 | 0.303 | 47 |
| Farigiraf@Sitrus Berry | Archaludon | 2.342 | 2.040 | 0.302 | 88 |

## 5. Top Triples
| Tokens | Teams | Families | Support | Lift3 | Gain |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Absol + Espathra@Grassy Seed + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 16523.78 | 66.61 |
| Absol@Absolite Z + Espathra@Grassy Seed + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 16523.78 | 66.61 |
| Absol + Espathra@Grassy Seed + Goodra-Hisui | 4 | 4 | 0.2% | 14947.88 | 66.61 |
| Absol@Absolite Z + Espathra@Grassy Seed + Goodra-Hisui | 4 | 4 | 0.2% | 14947.88 | 66.61 |
| Absol + Espathra + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 5777.90 | 66.61 |
| Absol@Absolite Z + Espathra + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 5777.90 | 66.61 |
| Absol + Espathra + Goodra-Hisui | 4 | 4 | 0.2% | 5226.85 | 66.61 |
| Absol@Absolite Z + Espathra + Goodra-Hisui | 4 | 4 | 0.2% | 5226.85 | 66.61 |
| Pawmot@Focus Sash + Politoed@Life Orb + Staraptor@Choice Scarf | 4 | 4 | 0.1% | 3906.09 | 69.10 |
| Pawmot + Politoed@Life Orb + Staraptor@Choice Scarf | 4 | 4 | 0.1% | 3321.78 | 58.76 |
| Glimmora@Glimmoranite + Klefki@Light Clay + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 3036.92 | 32.78 |
| Blastoise@Blastoisinite + Maushold@Chople Berry + Sinistcha@Occa Berry | 6 | 6 | 0.3% | 2959.90 | 45.91 |
| Blastoise + Maushold@Chople Berry + Sinistcha@Occa Berry | 6 | 6 | 0.3% | 2842.87 | 44.09 |
| Garchomp@Life Orb + Klefki@Light Clay + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 2549.90 | 27.52 |
| Glimmora@Glimmoranite + Klefki + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 2366.84 | 32.78 |
| Glimmora + Klefki@Light Clay + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 2175.94 | 23.49 |
| Espathra@Grassy Seed + Floette-Eternal@Floettite + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 2045.64 | 8.25 |
| Espathra@Grassy Seed + Floette-Eternal + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 2038.56 | 8.22 |
| Garchomp@Life Orb + Klefki + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 1987.28 | 27.52 |
| Espathra@Grassy Seed + Floette-Eternal@Floettite + Goodra-Hisui | 4 | 4 | 0.2% | 1850.55 | 8.25 |

## 6. Resolution Sweep
Connected communities only: unconnected species are outside the graph Louvain runs on.
| Resolution | Modularity | Community count |
| :--- | :--- | :--- |
| 0.50 | 0.67 | 5 |
| 0.75 | 0.61 | 6 |
| 1.00 | 0.55 | 6 |
| 1.25 | 0.50 | 7 |
| 1.50 | 0.45 | 9 |

## 7. Communities
- Unconnected species (no pair with at least minPairTeams teams and lift above communityMinLift, so in no community): Scizor, Heracross, Mimikyu, Overqwil, Drampa, Tinkaton, Ditto, Crabominable, Arcanine, Gyarados, Gliscor, Greninja, Steelix, Ninetales, Torterra, Bellibolt, Runerigus, Palafin, Sceptile, Cofagrigus, Azumarill, Noivern, Tauros-Paldea-Aqua, Dragalge, Aggron, Persian-Alola, Pidgeot, Krookodile, Feraligatr, Jolteon, Basculegion-F, Malamar, Clawitzer, Cinderace

### Community 0: Rillaboom / Raichu / Gholdengo
- Token label: Rillaboom / Raichu / Gholdengo
- Mode tags on primary teams: Tailwind 673, Setup 297, Snow 95, Trick Room 32, Sun 29, Screens 20, Rain 17, Sand 17, Psyspam 17, Perish Trap 13
- Megas on primary teams: Raichu-Y 632, Salamence 374, Staraptor 266, Floette 128, Froslass 68, Garchomp-Z 61, Lucario-Z 48, Golisopod 30, Metagross 28, Charizard-Y 26, Baxcalibur 20, Dragonite 20, Gengar 19, Glimmora 15, Absol-Z 14, Aerodactyl 14, Tyranitar 13, Delphox 10, Gardevoir 9, Blaziken 5, Camerupt 4, Altaria 3, Mawile 3, Raichu-X 3, Swampert 3, Charizard-X 2, Houndoom 2, Scrafty 2, Abomasnow 1, Aggron 1, Blastoise 1, Chandelure 1, Crabominable 1, Dragalge 1, Emboar 1, Gallade 1, Greninja 1, Manectric 1, Pidgeot 1, Pyroar 1, Sceptile 1, Scovillain 1, Starmie 1
- Primary teams: 977 (primary share 33.9%), hybrid teams: 412 (hybrid share 13.5%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Altaria+Milotic@Psychic Seed, Pawmot+Staraptor@Choice Scarf, Dragapult+Milotic@Psychic Seed, Vivillon+Rillaboom@Eject Button, Gengar+Rillaboom@Eject Button, Ceruledge+Milotic@Sitrus Berry, Armarouge+Milotic@Psychic Seed, Lycanroc-Dusk+Rillaboom@Life Orb, Politoed+Rillaboom@Eject Button, Altaria+Rillaboom@Eject Button, Politoed+Staraptor@Choice Scarf, Vivillon+Rillaboom@Occa Berry, Staraptor+Ceruledge@Grassy Seed, Sylveon+Aerodactyl@Aerodactylite, Primarina+Farigiraf@Grassy Seed, Sylveon+Aegislash@Focus Sash, Milotic+Ceruledge@Grassy Seed, Milotic+Altaria@Haban Berry, Excadrill+Rillaboom@Expert Belt, Metagross+Milotic@Psychic Seed, Ceruledge+Staraptor@Staraptite, Indeedee-F+Milotic@Psychic Seed, Gengar+Milotic@Psychic Seed, Ceruledge+Staraptor, Farigiraf+Staraptor@Choice Scarf, Aerodactyl+Sylveon@Fairy Feather, Ceruledge+Milotic, Tyranitar+Rillaboom@Expert Belt, Staraptor+Milotic@Psychic Seed, Primarina+Blaziken@Blazikenite, Aerodactyl+Sylveon, Sylveon+Venusaur@Life Orb, Kommo-o+Rillaboom@Eject Button, Torkoal+Primarina@Life Orb, Archaludon+Rillaboom@Eject Button, Aegislash+Sylveon@Fairy Feather, Staraptor+Armarouge@Twisted Spoon, Arcanine-Hisui+Altaria@Haban Berry, Excadrill+Milotic@Sitrus Berry, Aegislash+Sylveon, Milotic+Dragapult@Life Orb, Ninetales-Alola+Rillaboom@Occa Berry, Farigiraf+Primarina@Life Orb, Blaziken+Primarina, Froslass+Rillaboom@Sitrus Berry, Dragapult+Milotic, Altaria+Milotic, Tyranitar+Milotic@Sitrus Berry, Staraptor+Armarouge@Focus Sash, Milotic+Excadrill@Life Orb, Milotic+Armarouge@Twisted Spoon, Sylveon+Kingambit@Focus Sash, Primarina+Farigiraf@Colbur Berry, Staraptor+Dragapult@Life Orb, Raichu+Ceruledge@Grassy Seed, Raichu+Primarina@Grassy Seed, Sylveon+Toxapex@Leftovers, Primarina+Pawmot@Focus Sash, Arcanine-Hisui+Baxcalibur@Life Orb, Golisopod+Staraptor@Choice Scarf, Milotic+Ceruledge@Colbur Berry, Ninetales-Alola+Rillaboom@Life Orb, Dragapult+Staraptor@Staraptite, Arcanine-Hisui+Gholdengo@Grassy Seed, Dragapult+Staraptor, Incineroar+Rillaboom@Eject Button, Toxapex+Sylveon@Fairy Feather, Sylveon+Garchomp@Life Orb, Ceruledge+Raichu@Raichunite Y, Ceruledge+Raichu, Staraptor+Sylveon@Fairy Feather, Altaria+Arcanine-Hisui, Sylveon+Toxapex, Sylveon+Staraptor@Staraptite, Staraptor+Sylveon, Floette-Eternal+Rillaboom@Occa Berry, Altaria+Arcanine-Hisui@Focus Sash, Milotic+Rillaboom@Expert Belt, Staraptor+Milotic@Sitrus Berry, Pawmot+Primarina, Ceruledge+Gholdengo@Life Orb, Annihilape+Rillaboom@Life Orb, Raichu+Gholdengo@Grassy Seed, Gengar+Rillaboom@Occa Berry, Baxcalibur+Milotic@Leftovers, Kingambit+Sylveon@Life Orb, Gholdengo+Ceruledge@Grassy Seed, Salamence+Rillaboom@Expert Belt, Toxtricity+Sylveon@Fairy Feather, Pelipper+Rillaboom@Expert Belt, Arcanine-Hisui+Rillaboom@Sitrus Berry, Pelipper+Raichu@Raichunite X, Archaludon+Raichu@Raichunite X, Sylveon+Toxtricity, Arcanine-Hisui+Primarina@Grassy Seed, Kommo-o+Rillaboom@Occa Berry, Raichu+Staraptor@Staraptite, Staraptor+Raichu@Raichunite Y, Metagross+Milotic, Milotic+Armarouge@Focus Sash, Milotic+Metagross@Metagrossite, Staraptor+Gholdengo@Life Orb, Milotic+Excadrill@Focus Sash, Raichu+Staraptor, Camerupt+Rillaboom@Life Orb, Sylveon+Garchomp@Choice Scarf, Raichu+Ceruledge@Colbur Berry, Excadrill+Milotic, Ceruledge+Gholdengo, Milotic+Tyranitar@Tyranitarite, Milotic+Weavile, Sylveon+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Sylveon@Fairy Feather, Blaziken+Rillaboom@Life Orb, Raichu+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Raichu@Raichunite Y, Primarina+Torkoal@Charcoal, Golisopod+Rillaboom@Leftovers, Salamence+Ceruledge@Colbur Berry, Arcanine-Hisui+Sylveon, Arcanine-Hisui+Raichu, Gholdengo+Staraptor@Staraptite, Ceruledge+Milotic@Leftovers, Armarouge+Staraptor, Armarouge+Staraptor@Staraptite, Farigiraf+Primarina, Gholdengo+Staraptor, Sneasler+Gholdengo@Focus Sash, Milotic+Tyranitar, Sylveon+Aerodactyl@Focus Sash, Salamence+Gholdengo@Grassy Seed, Raichu+Gholdengo@Life Orb, Hydreigon+Milotic@Sitrus Berry, Salamence+Sylveon@Life Orb, Primarina+Lucario@Lucarionite Z, Froslass+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Froslass, Arcanine-Hisui+Froslass@Froslassite, Gholdengo+Raichu@Raichunite Y, Primarina+Torkoal, Lucario+Primarina, Lucario+Rillaboom@Occa Berry, Arcanine-Hisui+Staraptor@Staraptite, Excadrill+Milotic@Leftovers, Raichu+Rillaboom@Kebia Berry, Gholdengo+Raichu, Floette-Eternal+Gholdengo@Leftovers, Sylveon+Farigiraf@Sitrus Berry, Milotic+Staraptor@Staraptite, Staraptor+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Staraptor, Gholdengo+Milotic@Sitrus Berry, Raichu+Vanilluxe@Choice Scarf, Metagross+Milotic@Leftovers, Milotic+Staraptor, Politoed+Rillaboom@Occa Berry, Metagross+Milotic@Sitrus Berry, Basculegion+Rillaboom@Expert Belt, Froslass+Rillaboom@Eject Button, Raichu+Sylveon@Fairy Feather, Delphox+Rillaboom@Occa Berry, Sylveon+Raichu@Raichunite Y, Tyranitar+Milotic@Leftovers, Ceruledge+Rillaboom@Miracle Seed, Sneasler+Arcanine-Hisui@Life Orb, Raichu+Sylveon, Lucario+Rillaboom@Life Orb, Gholdengo+Primarina@Grassy Seed, Pelipper+Gholdengo@Choice Scarf, Gholdengo+Milotic@Grassy Seed, Indeedee+Milotic@Leftovers, Milotic+Indeedee@Choice Scarf, Incineroar+Rillaboom@Occa Berry, Gholdengo+Arcanine-Hisui@Focus Sash, Sylveon+Charizard@Charizardite Y, Arcanine-Hisui+Gholdengo, Arcanine-Hisui+Gholdengo@Leftovers, Kingambit+Rillaboom@Sitrus Berry, Arcanine-Hisui+Gholdengo@Life Orb, Charizard+Sylveon@Fairy Feather, Swampert+Rillaboom@Eject Button, Froslass+Rillaboom@Life Orb, Farigiraf+Primarina@Leftovers, Indeedee+Milotic@Sitrus Berry, Goodra-Hisui+Rillaboom@Miracle Seed, Charizard+Sylveon, Primarina+Volcarona@Grassy Seed, Staraptor+Garchomp@Sitrus Berry, Kingambit+Rillaboom@Kebia Berry, Armarouge+Milotic, Milotic+Hydreigon@Choice Scarf, Floette-Eternal+Gholdengo@Choice Scarf, Gholdengo+Garchomp@Sitrus Berry, Salamence+Rillaboom@Sitrus Berry, Raichu+Talonflame@Focus Sash, Raichu+Rillaboom@Miracle Seed, Gholdengo+Rillaboom@Kebia Berry, Rillaboom+Ceruledge@Grassy Seed, Rillaboom+Volcarona@Grassy Seed, Rillaboom+Empoleon@Leftovers, Rillaboom+Sneasler@Grassy Seed, Rillaboom+Farigiraf@Grassy Seed, Rillaboom+Milotic@Grassy Seed, Rillaboom+Gholdengo@Grassy Seed, Hippowdon+Rillaboom, Rillaboom+Hippowdon@Leftovers, Rillaboom+Primarina@Grassy Seed, Rillaboom+Incineroar@Lum Berry, Rillaboom+Swampert@Sitrus Berry, Rillaboom+Espathra@Grassy Seed, Golisopod+Rillaboom@Expert Belt, Farigiraf+Sylveon@Fairy Feather, Gholdengo+Rillaboom@Miracle Seed, Arcanine-Hisui+Kingambit@Life Orb, Vanilluxe+Raichu@Raichunite Y, Arcanine-Hisui+Sneasler@Grassy Seed, Staraptor+Hydreigon@Choice Scarf, Farigiraf+Sylveon, Incineroar+Rillaboom@Leftovers, Hippowdon+Rillaboom@Miracle Seed, Raichu+Vanilluxe, Ninetales-Alola+Rillaboom@Sitrus Berry, Staraptor+Glimmora@Focus Sash, Salamence+Rillaboom@Kebia Berry, Primarina+Kingambit@Focus Sash, Metagross+Arcanine-Hisui@Focus Sash, Sylveon+Gholdengo@Life Orb, Gholdengo+Primarina@Mystic Water, Arcanine-Hisui+Rillaboom@Kebia Berry, Absol+Milotic@Leftovers, Arcanine-Hisui+Metagross@Metagrossite, Golisopod+Milotic@Psychic Seed, Dragonite+Rillaboom@Life Orb, Archaludon+Rillaboom@Expert Belt, Indeedee+Milotic, Kingambit+Ceruledge@Colbur Berry, Staraptor+Whimsicott@Occa Berry, Arcanine-Hisui+Metagross, Milotic+Gholdengo@Life Orb, Ceruledge+Rillaboom, Kommo-o+Gholdengo@Grassy Seed, Gholdengo+Sylveon@Fairy Feather, Volcarona+Rillaboom@Miracle Seed, Arcanine-Hisui+Rillaboom@Miracle Seed, Gholdengo+Dragonite@Dragoninite, Milotic+Baxcalibur@Baxcalibrite, Staraptor+Indeedee-F@Rocky Helmet, Staraptor+Rillaboom@Miracle Seed, Milotic+Indeedee-F@Rocky Helmet, Rillaboom+Raichu@Raichunite Y, Gholdengo+Sylveon, Rillaboom+Scrafty, Lucario+Rillaboom@Miracle Seed, Charizard+Rillaboom@Grassy Seed, Primarina+Raichu@Raichunite Y, Arcanine-Hisui+Milotic@Grassy Seed, Gholdengo+Ceruledge@Colbur Berry, Garchomp+Sylveon@Fairy Feather, Raichu+Rillaboom, Rillaboom+Politoed@Sitrus Berry, Raichu+Milotic@Sitrus Berry, Rillaboom+Vivillon@Focus Sash, Garchomp+Sylveon, Primarina+Raichu, Gholdengo+Sneasler@Grassy Seed, Pelipper+Rillaboom@Life Orb, Raichu+Kingambit@Life Orb, Raichu+Volcarona@Grassy Seed, Sneasler+Rillaboom@Kebia Berry, Rillaboom+Gholdengo@Life Orb, Raichu+Rillaboom@Sitrus Berry, Rillaboom+Vivillon, Salamence+Milotic@Sitrus Berry, Rillaboom+Arcanine-Hisui@Focus Sash, Rillaboom+Goodra-Hisui@Leftovers, Gholdengo+Rillaboom, Archaludon+Gholdengo@Choice Scarf, Raichu+Blaziken@Focus Sash, Raichu+Primarina@Leftovers, Arcanine-Hisui+Rillaboom, Salamence+Arcanine-Hisui@Focus Sash, Staraptor+Gholdengo@Choice Scarf, Rillaboom+Lucario@Lucarionite Z, Gholdengo+Milotic, Arcanine-Hisui+Salamence@Salamencite, Arcanine-Hisui+Salamence, Primarina+Incineroar@Sitrus Berry, Metagross+Rillaboom@Life Orb, Rillaboom+Volcarona@Rocky Helmet, Lucario+Rillaboom, Baxcalibur+Rillaboom@Sitrus Berry, Manectric+Rillaboom, Rillaboom+Manectric@Manectite, Blaziken+Milotic@Leftovers, Sneasler+Rillaboom@Sitrus Berry, Primarina+Staraptor@Staraptite, Dragonite+Gholdengo@Life Orb, Dragonite+Gholdengo, Baxcalibur+Milotic, Indeedee-F+Toxtricity, Floette-Eternal+Rillaboom@Sitrus Berry, Sylveon+Rillaboom@Miracle Seed, Raichu+Milotic@Grassy Seed, Incineroar+Primarina@Grassy Seed, Rillaboom+Volcarona, Gholdengo+Rillaboom@Expert Belt, Primarina+Staraptor, Salamence+Primarina@Grassy Seed, Basculegion+Rillaboom@Sitrus Berry, Salamence+Milotic@Leftovers, Gholdengo+Milotic@Leftovers, Milotic+Indeedee-F@Sitrus Berry, Floette-Eternal+Rillaboom@Life Orb, Rillaboom+Incineroar@Rocky Helmet, Incineroar+Weavile, Whimsicott+Staraptor@Staraptite, Arcanine-Hisui+Kingambit@Chople Berry, Rillaboom+Pelipper@Choice Scarf, Rillaboom+Incineroar@Passho Berry, Sylveon+Venusaur, Raichu+Sneasler@Grassy Seed, Hydreigon+Milotic, Gholdengo+Incineroar@Rocky Helmet, Rillaboom+Gengar@Gengarite, Goodra-Hisui+Rillaboom, Froslass+Rillaboom, Rillaboom+Froslass@Froslassite, Sylveon+Gholdengo@Grassy Seed, Delphox+Rillaboom@Life Orb, Staraptor+Whimsicott, Gholdengo+Salamence@Salamencite, Gholdengo+Salamence, Gengar+Rillaboom, Rillaboom+Primarina@Leftovers, Incineroar+Primarina@Leftovers, Incineroar+Rillaboom@Life Orb, Salamence+Primarina@Leftovers, Rillaboom+Maushold@Focus Sash, Floette-Eternal+Gholdengo@Life Orb, Gholdengo+Floette-Eternal@Floettite, Arcanine-Hisui+Hydreigon@Choice Scarf, Golisopod+Rillaboom@Grassy Seed, Floette-Eternal+Gholdengo, Venusaur+Sylveon@Fairy Feather, Hydreigon+Milotic@Leftovers, Froslass+Raichu@Raichunite Y, Floette-Eternal+Rillaboom, Rillaboom+Floette-Eternal@Floettite, Raichu+Kleavor@Focus Sash, Salamence+Gholdengo@Life Orb, Primarina+Farigiraf@Sitrus Berry, Floette-Eternal+Rillaboom@Miracle Seed, Primarina+Rillaboom@Miracle Seed, Froslass+Raichu, Raichu+Froslass@Froslassite, Froslass+Rillaboom@Occa Berry, Salamence+Primarina@Life Orb, Volcarona+Rillaboom@Life Orb, Golisopod+Rillaboom@Life Orb, Rillaboom+Milotic@Sitrus Berry, Rillaboom+Staraptor@Staraptite, Sylveon+Basculegion@Life Orb, Gholdengo+Tyranitar@Tyranitarite, Gholdengo+Rillaboom@Occa Berry, Rillaboom+Incineroar@Sitrus Berry, Excadrill+Gholdengo@Life Orb, Tyranitar+Gholdengo@Life Orb, Rillaboom+Kingambit@Life Orb, Blaziken+Milotic, Rillaboom+Staraptor, Milotic+Blaziken@Blazikenite, Clefable+Rillaboom, Milotic+Salamence@Salamencite, Salamence+Gholdengo@Leftovers, Staraptor+Whimsicott@Focus Sash, Milotic+Salamence, Garchomp+Rillaboom@Grassy Seed, Rillaboom+Baxcalibur@Baxcalibrite, Rillaboom+Ninetales-Alola@Choice Scarf, Gholdengo+Excadrill@Focus Sash, Arcanine-Hisui+Milotic@Leftovers, Primarina+Salamence@Salamencite, Golisopod+Primarina@Life Orb, Primarina+Salamence, Rillaboom+Incineroar@Chople Berry, Rillaboom+Gholdengo@Leftovers, Salamence+Rillaboom@Miracle Seed, Rillaboom+Archaludon@Chople Berry, Salamence+Rillaboom@Leftovers, Incineroar+Rillaboom, Primarina+Arcanine-Hisui@Focus Sash, Raichu+Rillaboom@Life Orb, Rillaboom+Salamence@Salamencite, Hydreigon+Staraptor@Staraptite, Milotic+Rillaboom@Grassy Seed, Rillaboom+Salamence, Rillaboom+Sylveon@Fairy Feather, Arcanine-Hisui+Farigiraf@Grassy Seed, Rillaboom+Sylveon, Raichu+Ninetales-Alola@Light Clay, Sneasler+Sylveon@Life Orb, Tsareena+Gholdengo@Life Orb, Incineroar+Rillaboom@Grassy Seed, Arcanine-Hisui+Primarina, Primarina+Metagross@Metagrossite, Rillaboom+Garchomp@Garchompite Z, Gholdengo+Incineroar@Sitrus Berry, Lucario+Sylveon@Fairy Feather, Rillaboom+Ninetales-Alola@Focus Sash, Hydreigon+Staraptor, Primarina+Rillaboom, Raichu+Volcarona, Rillaboom+Sylveon@Life Orb, Milotic+Raichu@Raichunite Y, Kingambit+Primarina@Life Orb, Rillaboom+Kommo-o@Leftovers, Rillaboom+Milotic@Leftovers, Rillaboom+Basculegion@Life Orb, Espathra+Rillaboom, Baxcalibur+Rillaboom, Rillaboom+Arcanine-Hisui@Choice Scarf, Rillaboom+Ninetales-Alola@Light Clay, Rillaboom+Dragonite@Dragoninite, Rillaboom+Talonflame@Focus Sash, Ninetales-Alola+Rillaboom, Rillaboom+Dragonite@Life Orb, Mamoswine+Rillaboom, Rillaboom+Mamoswine@Focus Sash
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Rillaboom | 94.6% | terrain-setter 99.9%, priority-attack 99.2%, fake-out 99.2%, pivot 28.6%, speed-drop 2.0%, disruption 0.4% |
| Raichu | 67.9% | mega-attacker 99.9%, speed-drop 98.4%, fake-out 80.4%, disruption 16.6%, pivot 2.2%, terrain-setter 1.7%, setup 0.3%, screens 0.2% |
| Gholdengo | 67.3% | setup 94.5% |
| Arcanine-Hisui | 49.2% | priority-attack 91.1%, intimidate 1.6%, spa-drop 0.1% |
| Staraptor | 29.7% | intimidate 99.7%, mega-attacker 98.0%, tailwind 78.7%, pivot 2.9%, weather-setter 0.3%, priority-attack 0.3% |
| Sylveon | 22.7% | priority-attack 83.6%, status 10.0%, setup 5.9%, trick-room-abuser 5.7%, spa-drop 3.5%, helping-hand 0.9%, weather-setter 0.4% |
| Milotic | 21.9% | speed-drop 46.0%, setup 40.3%, status 38.9%, helping-hand 2.7%, weather-setter 0.5%, screens 0.3%, pivot 0.2% |
| Ceruledge | 8.1% | setup 97.7%, priority-attack 96.1%, ally-switch 1.0% |
| Primarina | 4.1% | setup 49.7%, trick-room-abuser 11.6%, priority-attack 10.2%, speed-drop 5.9%, pivot 1.6%, perish-song 1.6%, disruption 1.1%, helping-hand 0.7% |
| Weavile | 0.6% | fake-out 80.8%, priority-attack 22.5%, speed-drop 5.4% |
| Scrafty | 0.4% | fake-out 100.0%, intimidate 100.0%, mega-attacker 60.5%, trick-room-abuser 33.3% |
| Toxtricity | 0.2% | spa-drop 55.9%, pivot 29.8%, trick-room-abuser 12.3%, speed-drop 8.7% |
- Representative teams (primary teams with the highest score for this community):
  - [Naoya Takasago, 16th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0135/teamlist)
  - [Conner Pietrusinski, Top 8, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/q65vnQVtYoaIlJgWdymZ)
  - [Jonathan Clarke, 39th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0106/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Karlos Aquino, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0030/teamlist)
  - [Sky Konsti, 118th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0084/teamlist)
  - [Anthony McMullen, 267th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0251/teamlist)
  - [Arthur Ehrecke, 99th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0741/teamlist)
  - [Sinan Dinlamaz, 168th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1113/teamlist)
  - [Noah van der Voort, 237th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0590/teamlist)
  - [Nils Breunig, 490th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1123/teamlist)
  - [Andre Kautz, 813th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0881/teamlist)
  - [Carden Shelly, 231st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0936/teamlist)
  - [Jeffrey Falberg, 312th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0677/teamlist)
  - [Nicholas Tran, 412th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0472/teamlist)
  - [Noah Earlenbaugh, 621st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0422/teamlist)
  - [Kaid Hackler, 879th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0396/teamlist)
  - [Casey Campbell, 896th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0441/teamlist)
  - [Zachary Sefcik, 133rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0915/teamlist)
  - [lovejapanfrombr, Peak 6th, 13 Sep 2026](https://pokepast.es/0149eb0e0ddf41fe)
  - [Justin Cerioni, , 13 Sep 2026](https://pokepast.es/9144f9e5950aaa23)
  - [Declan Lomboy, 572nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0976/teamlist)
  - [Christian Walloschek, 866th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0543/teamlist)
  - [Jan-Philipp Gnaß, 314th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1114/teamlist)
  - [Niek Fenijn, 409th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1110/teamlist)
  - [Amelius van Etten, 924th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0966/teamlist)
  - [Nate Timms, 192nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0828/teamlist)
  - [Kyle Morris, 332nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0278/teamlist)
  - [Sean Mondor, 482nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0747/teamlist)
  - [Constanza Castro, 1032nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0485/teamlist)
  - [David Morais, 880th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0159/teamlist)
  - [Patrick Hackes, 509th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0908/teamlist)
  - [Nikita Saxena, 530th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0628/teamlist)
  - [Diego Galeas, 539th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0548/teamlist)
  - [Brandon Bynon, 803rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0774/teamlist)
  - [Darius Miranda, 957th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0272/teamlist)
  - [Ezequiel Cordero, Champion, 14 Sep 2026](https://pokepast.es/43bdff98a519db09)
  - [Patrick McKenna, 450th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0690/teamlist)
  - [Anders Beckman, 506th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0981/teamlist)
  - [lovejapanfrombr, , 15 Sep 2026](https://pokepast.es/5a342ea870e2bbe8)
  - [Chris Iwaskiw, 561st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1056/teamlist)
  - [Dan Hopper, 696th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0877/teamlist)
  - [Rafael Callou de Morais Messias, 211th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0222/teamlist)
  - [Behnam Sorbi, 677th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0564/teamlist)
  - [Grant Brown, 714th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0569/teamlist)
  - [Randall Miller, 749th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0221/teamlist)
  - [Aaryan Tomar, 856th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1014/teamlist)
  - [Brandon Hill, 872nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0897/teamlist)
  - [Jay Little, 915th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1038/teamlist)
  - [Forrest DeMaio, 529th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0702/teamlist)
  - [Alex Underhill, , 16 Sep 2026](https://pokepast.es/f6c343c1f47903cc)
  - [Adam Naish, 134th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0035/teamlist)
  - [Jan-Luca Heinrich, 159th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0538/teamlist)
  - [Phönix Meschkat, 586th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0719/teamlist)
  - [Matteo DiRende, 473rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0258/teamlist)
  - [Alexander Hobson, 653rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0738/teamlist)
  - [punihina1334, Champion, 19 Sep 2026](https://pokepast.es/98d6e2fba3995368)
  - [max_Zi_ma, , 12 Sep 2026](https://pokepast.es/73e5d6533b781089)
  - [Tyler Aitken, 293rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0054/teamlist)
  - [Drew Labick, 617th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0455/teamlist)
  - [Trey Clizbe, 282nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0911/teamlist)
  - [Joseph Bekampis, 516th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0938/teamlist)
  - [Ethan Foster, 977th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0671/teamlist)
  - [Joseph Andrews, 1006th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1052/teamlist)
  - [Aedan Kearns, 1072nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0804/teamlist)
  - [Ben Gray, 176th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1022/teamlist)
  - [Klaus Bögl, 189th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0018/teamlist)
  - [Adrian Francisco, 1044th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0768/teamlist)
  - [José Luis García Sánchez, 477th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0683/teamlist)
  - [Nathan Rouby, 1036th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0862/teamlist)
  - [Brian Kem, 91st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0004/teamlist)
  - [Jordan Schumann, 749th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0207/teamlist)
  - [James McKinley, 424th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0192/teamlist)
  - [Jarrod Rose, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0044/teamlist)
  - [Mats Schrader, 769th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0382/teamlist)
  - [Rocco Hauboldt, 969th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0623/teamlist)
  - [Kyle Brynteson, 154th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0134/teamlist)
  - [Anthony Glover, 363rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0311/teamlist)
  - [Hunter Wellens, 537th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0606/teamlist)
  - [Nathaniel Ledford, 713th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1017/teamlist)
  - [Michael Garnsey, 319th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0294/teamlist)
  - [Rielly Chambers, 66th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0092/teamlist)
  - [Ville-Veikko Vähäaho, 781st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0841/teamlist)
  - [Leonard Laatz, 995th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1028/teamlist)
  - [Rodney van den Velden, 1119th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0321/teamlist)
  - [Zach Franks, 829th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0404/teamlist)
  - [Ryan Loseto, Champion, 10 Sep 2026](https://pokepast.es/2c92e63c0fd4a43c)
  - [Adam Wright, , 12 Sep 2026](https://pokepast.es/b2d0e7f2f14c59e4)
  - [Connor Weston, 487th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0016/teamlist)
  - [Josiah Brechler, 579th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0250/teamlist)
  - [Tyus Colbert, 981st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0681/teamlist)
  - [dengus075, Top 4, 13 Sep 2026](https://pokepast.es/a5dfce8794a63b55)
  - [Travis Clizbe, 240th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0137/teamlist)
  - [Devin Traudt, 673rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0997/teamlist)
  - [Michel Kaiser, 670th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0809/teamlist)
  - [Seanvin Nugroho, 218th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0073/teamlist)
  - [Wang Yi-Liang, , 10 Sep 2026](https://pokepast.es/bad1f0554790c541)
  - [Adrian van Dijk, 717th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0180/teamlist)
  - [Zachery Logan, 816th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0977/teamlist)
  - [Jacob O'Connor, 1003rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0284/teamlist)
  - [Titania N, 934th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0701/teamlist)
  - [Hao Lu, 303rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0417/teamlist)
  - [Chase Weight, 207th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0266/teamlist)
  - [Oscar Lopez, 1019th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0968/teamlist)
  - [Bennet Reimer, 734th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0908/teamlist)
  - [Samuel Galera Barrera, 688th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0410/teamlist)
  - [Konrad Schmedes, 397th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0129/teamlist)
  - [Bart van Doorn, 47th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0266/teamlist)
  - [David Benjamin King, 973rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0672/teamlist)
  - [Benjamin Saracevic, 500th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0498/teamlist)
  - [Peter Espy, 186th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0903/teamlist)
  - [Brendan DeWerth, 330th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0482/teamlist)
  - [Rily Corn, 460th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1058/teamlist)
  - [Alexander Phipps, 810th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0012/teamlist)
  - [Giuseppe Musicco, 8th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0703/teamlist)
  - [Nakul Umashankar, 146th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0151/teamlist)
  - [Luca Lussignoli, 46th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0204/teamlist)
  - [Emanuele Briganti, 56th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0899/teamlist)
  - [Théotime Massaut, 65th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0633/teamlist)
  - [Matteo Moscardini, 232nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0984/teamlist)
  - [Lorenzo Allemagna, 263rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0235/teamlist)
  - [Karl Akpovi, 544th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0587/teamlist)
  - [Karim Salem, 676th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0505/teamlist)
  - [Simon Gosset, 875th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0824/teamlist)
  - [Dominik Stryk, 1050th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0133/teamlist)
  - [Eden Batchelor, 4th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0423/teamlist)
  - [DidiVGC, Champion, 21 Sep 2026](https://pokepast.es/c06c65c34f56a094)
  - [yozora_952, , 15 Sep 2026](https://pokepast.es/90f7ccc7ab5b3d0b)
  - [Dicky Nicholas, 53rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0164/teamlist)
  - [Joseph Russell, 149th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0939/teamlist)
  - [Filippo Guagliardo, 158th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0037/teamlist)
  - [Alexander Kampf, 160th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0250/teamlist)
  - [Merle van Doren, 191st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0866/teamlist)
  - [Lennart Christe, 393rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0073/teamlist)
  - [Alessandro Tuzi, 428th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0819/teamlist)
  - [Joey van Diepen, 432nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0795/teamlist)
  - [Jonathan Fues, 631st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0228/teamlist)
  - [Chiand Balasubramaniam, 713th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0079/teamlist)
  - [Kim Ronkainen, 941st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0822/teamlist)
  - [Leon Böhnlein, 1025th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0983/teamlist)
  - [Dorian Kang, Top 16, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/10gY7gYrOnPAGK7Nudmq)
  - [Dylan Bower, 183rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0297/teamlist)
  - [Thomas Parzuchowski, 721st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0065/teamlist)
  - [Amir Harris, 672nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0617/teamlist)
  - [Dylan Coleman, 1117th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0648/teamlist)
  - [Roman Manukjan, 212th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1073/teamlist)
  - [Emily Olynick, 432nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0758/teamlist)
  - [Christopher Carcamo, 979th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0269/teamlist)
  - [Santiago Molina, 144th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0754/teamlist)
  - [Stefan Mott, 190th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0045/teamlist)
  - [Rosemary Kelley, 356th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0319/teamlist)
  - [John Thi, 374th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0778/teamlist)
  - [Thijmen Hasselt, 696th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0624/teamlist)
  - [Efe Obanor, 902nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0992/teamlist)
  - [Luke Wray, 932nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0420/teamlist)
  - [Stefan Mott, 25th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0032/teamlist)
  - [Kylan Van Severen, 46th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0556/teamlist)
  - [Elias Hein, 940th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0915/teamlist)
  - [David Grigoleit, 579th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0954/teamlist)
  - [Trent Rose, 695th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0168/teamlist)
  - [Bayley Moore, 277th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0009/teamlist)
  - [Fabian Mayer, 873rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0005/teamlist)
  - [Tommy Chov, 1086th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0183/teamlist)
  - [joej_live, 52nd, 21 Sep 2026](https://pokepast.es/dce537b603b784c6)
  - [Joey McGinley, 52nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0487/teamlist)
  - [Skylar Simonds, 775th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0049/teamlist)
  - [Matthew Irwin, 658th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0901/teamlist)
  - [Alexa Belgard, 1010th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1077/teamlist)
  - [Hisashiro Egashira, 836th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1046/teamlist)
  - [Darryl Brice, 498th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0126/teamlist)
  - [Joshua Fitzhardy, 110th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0108/teamlist)
  - [Giovanni Cabrera, 964th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0914/teamlist)
  - [Lorenzo Bucci, , 10 Sep 2026](https://pokepast.es/42f46820310783d1)
  - [Charles Hsiao, 625th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0399/teamlist)
  - [Naoyuki Matsuhashi, , 9 Sep 2026](https://pokepast.es/0071e895c381dd1c)
  - [Robin Lucas Bachofner, 1085th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0014/teamlist)
  - [Manuel Schwab, 649th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0562/teamlist)
  - [Dennis Bouma, 931st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1127/teamlist)
  - [Ryan Bogdansky, 908th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0232/teamlist)
  - [Mouad Tiahi, 990th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0676/teamlist)
  - [Sean Alexis Balayon, 994th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0394/teamlist)
  - [starfish0206, , 11 Sep 2026](https://pokepast.es/5d8e24080b6d3200)
  - [mop, , 10 Sep 2026](https://pokepast.es/8536a2b4f89d3e72)
  - [Steven Guo, 799th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0873/teamlist)
  - [Jay Gelunas, 1062nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0446/teamlist)
  - [Alejandro, , 10 Sep 2026](https://pokepast.es/785dbfcd2432c102)
  - [Wyatt McDonald, , 9 Sep 2026](https://pokepast.es/5b9fae64b204f805)
  - [Luis Medina, 388th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0040/teamlist)
  - [Jesse Trevino, 155th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0476/teamlist)
  - [Amrit Mann, 444th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0962/teamlist)
  - [Tyler Coleman, 378th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0999/teamlist)
  - [Steven Mancilla, 582nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0810/teamlist)
  - [CloverBells, , 13 Sep 2026](https://pokepast.es/2d9490a4361c8159)
  - [Gregory Gramling, 663rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1023/teamlist)
  - [Nils-Lennart Ottemann, 442nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0527/teamlist)
  - [John Taylor, 739th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0262/teamlist)
  - [ragimali, , 10 Sep 2026](https://pokepast.es/674ac2a18201012a)
  - [Matthew Henry, 5th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0085/teamlist)
  - [Mustafaa Olomi, 318th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0120/teamlist)
  - [Jules Gallo, 35th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0642/teamlist)
  - [James Wright, 786th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0332/teamlist)
  - [Iván Amate, 299th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0434/teamlist)
  - [Evan Carpenter, 580th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0292/teamlist)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/ede01efa45931c7c)
  - [Deonté Hughes, 737th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0163/teamlist)
  - [Marc Hooijenga, 759th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0071/teamlist)
  - [Matthew Molnar, 555th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0525/teamlist)
  - [David Mainato, 795th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0014/teamlist)
  - [Amar Curic, 188th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0289/teamlist)
  - [Maurice Ludwig, 1124th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0039/teamlist)
  - [indigo_telecas, , 9 Sep 2026](https://pokepast.es/d7be7226c71f3624)
  - [Daniel Locklear, 131st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0178/teamlist)
  - [Cosmo, , 10 Sep 2026](https://pokepast.es/a8fc4cfab65cd411)
  - [Kevin Kühne, 147th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1033/teamlist)
  - [Jordan Litsinger, 859th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0233/teamlist)
  - [Stefan Filipovski, 733rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0152/teamlist)
  - [Aurore Anne Denise, 1031st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0910/teamlist)
  - [Brandon Kinslow, 58th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0427/teamlist)
  - [May Yu, 689th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0698/teamlist)
  - [Kathryn Aplin, 646th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1079/teamlist)
  - [Kaleb Thurman, 494th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0975/teamlist)
  - [Matthew Herndon, 999th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0094/teamlist)
  - [Adam Warren, 1056th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0657/teamlist)
  - [Timo Florian, 384th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0796/teamlist)
  - [Alex Thompson, 400th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0115/teamlist)
  - [Motochika Nabeshima, , 9 Sep 2026](https://pokepast.es/5268ba8166d67ebf)
  - [Jonathan Lin, 123rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0648/teamlist)
  - [joserockzvgc, , 16 Sep 2026](https://pokepast.es/ee07bfede4b559fc)
  - [Marco Metelli, 205th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0028/teamlist)
  - [Florian Hoffmann, 174th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0702/teamlist)
  - [Luka Rüeger, 929th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0214/teamlist)
  - [Juan Francisco Alcaraz, 106th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0870/teamlist)
  - [Roi Gómez García, 1037th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0268/teamlist)
  - [Andrew Krebs, Top 4, 13 Sep 2026](https://pokepast.es/a4768a9bbf6876de)
  - [Jon Huntley, 650th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0087/teamlist)
  - [Brett Saguid, 708th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0667/teamlist)
  - [Dane Bodamer, 863rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0475/teamlist)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/3ad6655b53208446)
  - [Pasty, , 10 Sep 2026](https://pokepast.es/9c4f7915b1d3ae07)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/7fb12fe08230d7be)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/016cd16a7d929d4f)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/81427a109e744097)
  - [Brian Salyerds Jr, 440th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0145/teamlist)
  - [Julian Knapp, 599th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0963/teamlist)
  - [Benjamin Haeseler, 461st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0140/teamlist)
  - [Kyle Moore, 783rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0694/teamlist)
  - [Sten Schram, 901st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0867/teamlist)
  - [Keegan Wilson, 33rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0272/teamlist)
  - [Christian Zichler, 169th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0863/teamlist)
  - [Joel Strerath, 336th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0361/teamlist)
  - [Nico Märker, 364th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0594/teamlist)
  - [Chris-jan Borgers, 672nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0479/teamlist)
  - [Daniel Yu, 20th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0235/teamlist)
  - [Joey Woodring, 850th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0962/teamlist)
  - [Miles Morales, 930th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0907/teamlist)
  - [dylanis_chilln, 16th, 21 Sep 2026](https://pokepast.es/1a24e07035a9036e)
  - [Daniel Yu, 20th, 21 Sep 2026](https://pokepast.es/ea35d1777f660322)
  - [Dylan Matthews, Top 16, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/tp1xMws30GePDkvo69zC)
  - [Lev Shkolnikov, 70th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0882/teamlist)
  - [Charlie Caddell, 371st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0061/teamlist)
  - [Jacob Hvied, 619th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0864/teamlist)
  - [Damon Rodriguez, 213th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0104/teamlist)
  - [Brandon Harrison, 486th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0185/teamlist)
  - [Jonas Birarda, 307th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0794/teamlist)
  - [Alyse Johnson, 183rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0106/teamlist)
  - [Jay Carson, 710th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0039/teamlist)
  - [Jaime Blanch Vázquez, 590th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0687/teamlist)
  - [Will Connor, 174th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0306/teamlist)
  - [Leopold Zaitz, 701st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0912/teamlist)
  - [Brian Lin, 930th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0510/teamlist)
  - [Kevin Menden, 824th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0036/teamlist)
  - [Wasilis Sotiropoulos, 1116th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0509/teamlist)
  - [Philemon Knafo, 140th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0752/teamlist)
  - [Thomas Wall, 768th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0639/teamlist)
  - [Minche Chung, 12th, 20 Sep 2026](https://pokepast.es/b987727e0339084a)
  - [Adonis Watford, 272nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0792/teamlist)
  - [Hunter Jones, 479th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0465/teamlist)
  - [Justin Tobias, 297th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0647/teamlist)
  - [Lucas MacKenzie, 971st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0216/teamlist)
  - [Anthony Mubiala, 562nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0423/teamlist)
  - [Aspen Leahy, 735th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1002/teamlist)
  - [Albert Kinas, 703rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0987/teamlist)
  - [Eric Partelow, 884th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0206/teamlist)
  - [Stefano Greppi, 40th, 16 Sep 2026](https://pokepast.es/0d18f72df8bd8d92)
  - [Victor Bo, 445th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0624/teamlist)
  - [Fang Yu-Yang, , 17 Sep 2026](https://pokepast.es/f22e21bb4d824590)
  - [Xena, , 11 Sep 2026](https://pokepast.es/983fa284e2d45493)
  - [Justin Cerioni, , 11 Sep 2026](https://pokepast.es/a88b8c2cfc274ab2)
  - [homura_kurenai_, , 9 Sep 2026](https://pokepast.es/183a005c8a677937)
  - [William Kodama, 120th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0321/teamlist)
  - [Michael Cenatiempo, 693rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0202/teamlist)
  - [Felix Renaud-Chartier, 768th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0878/teamlist)
  - [v1nzvgc, , 18 Sep 2026](https://pokepast.es/ab47ccee10152485)
  - [Michał Młynarczyk, 989th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0923/teamlist)
  - [yuki_mpm, , 13 Sep 2026](https://pokepast.es/e3bbfefb252a130d)
  - [Maxwell Richards, 483rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0635/teamlist)
  - [Joe Carlino, , 10 Sep 2026](https://pokepast.es/1e6a03dc1764214e)
  - [koyuki, , 9 Sep 2026](https://pokepast.es/8f4c2600a4a63a90)
  - [gastrodon, , 10 Sep 2026](https://pokepast.es/59b0a674b59141a2)
  - [Kiwamu Endo, , 14 Sep 2026](https://pokepast.es/419384c3fd6c9e16)
  - [Mateo Rodriguez, 64th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0241/teamlist)
  - [Garrett Wright, 972nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0297/teamlist)
  - [Douglas Miller, 604th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0214/teamlist)
  - [Víctor Medina, , 12 Sep 2026](https://pokepast.es/6534595ea1b752f8)
  - [Cole Gouslin, 610th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0739/teamlist)
  - [Laura Craig, 968th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1087/teamlist)
  - [Eleanor Feeney, 1019th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0691/teamlist)
  - [Daniel Oatley, 894th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0433/teamlist)
  - [Ruben Brandfass, 1063rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0011/teamlist)
  - [Tim Westerhoff, 1028th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1125/teamlist)
  - [Joshua Dodds, 153rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0123/teamlist)
  - [Marielle Lynnsen, 1072nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0419/teamlist)
  - [Fabio David, 58th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0345/teamlist)
  - [Rubén Gómez, 401st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0946/teamlist)
  - [Junhee Lee, , 10 Sep 2026](https://pokepast.es/24b7e1e280b1088f)
  - [Francesco Le Pera, 553rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0388/teamlist)
  - [Escen Schaferin, 880th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0082/teamlist)
  - [Eliel Gonzalez, 567th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0105/teamlist)
  - [Eliah Werner, 882nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0242/teamlist)
  - [poke_sky39, 9th, 13 Sep 2026](https://pokepast.es/eedf279fbc3ad845)
  - [Athan Mallios, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0074/teamlist)
  - [Luke Weiland, 875th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0638/teamlist)
  - [Jeremiah Paul, 212th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0587/teamlist)
  - [Nikhil Rajbhandary, 443rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0848/teamlist)
  - [Matthew Coldhill-Smink, 278th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0005/teamlist)
  - [Julio Barbosa, 735th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0227/teamlist)
  - [Dorean Neron, 504th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0033/teamlist)
  - [Lance Lee, 1028th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0478/teamlist)
  - [drexrg, , 12 Sep 2026](https://pokepast.es/a4fd7f400e9b4e49)
  - [KingYabber, , 10 Sep 2026](https://pokepast.es/b230239de70d9692)
  - [Murphy Hartzenberg, 135th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0001/teamlist)
  - [Valentijn Visser, 7th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0818/teamlist)
  - [Livio Sandberg, 83rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0965/teamlist)
  - [Amethyst Leine, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0362/teamlist)
  - [Ivan Radosevic, 535th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1091/teamlist)
  - [Roel Egberts, 555th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0875/teamlist)
  - [Ignatius Lee, 159th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0091/teamlist)
  - [krlos_cb, , 13 Sep 2026](https://pokepast.es/5a8934668d366537)
  - [Víctor Medina, 29th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1013/teamlist)
  - [David Peralta Bozada, 145th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0080/teamlist)
  - [Carlos Cabal, 148th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0573/teamlist)
  - [Samuel Pereira, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0995/teamlist)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [Jordan Goggin, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0089/teamlist)
  - [Reynaldo Boles, 171st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0064/teamlist)
  - [Yu Xiang, 41st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1043/teamlist)
  - [Sascha Eilts, 372nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0025/teamlist)
  - [Nick Theunis, 677th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0916/teamlist)
  - [Nikita Gnatenko, 679th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0997/teamlist)
  - [Michell Osew, 724th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0153/teamlist)
  - [Julian Pluta, 925th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0271/teamlist)
  - [Chenyi Tao, 985th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0050/teamlist)
  - [Josh Hamilton, 319th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0021/teamlist)
  - [Alexander Ballin, 355th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0173/teamlist)
  - [Steven Stark, 550th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0074/teamlist)
  - [Gerald Braden, 642nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0488/teamlist)
  - [Clay McGill, 728th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0054/teamlist)
  - [KaSun Thompson, 238th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0906/teamlist)
  - [Shuji ENDO, 10th, 13 Sep 2026](https://pokepast.es/e74e8282d41a3802)
  - [Yuta Ishigaki, , 12 Sep 2026](https://pokepast.es/668502969512b159)
  - [Timo Jonker, 365th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0116/teamlist)
  - [Marcos Perez, 906th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0210/teamlist)
  - [alexandre FRIZZO, 755th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0232/teamlist)
  - [Nathaniel Spann, 132nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0125/teamlist)
  - [Justin Tang, , 14 Sep 2026](https://pokepast.es/ca08bca9b981be5c)
  - [Matthew Laughlin, 806th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1010/teamlist)
  - [ikorin_poke, , 13 Sep 2026](https://pokepast.es/ac6967ec268cbdf8)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)
  - [Rani De Schoenmacker, 740th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0150/teamlist)
  - [Matteo Paviza, 877th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0022/teamlist)
  - [Tyler Norton, 546th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0458/teamlist)
  - [Gabe Baum, 53rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0364/teamlist)
  - [Yuma Kinugawa, , 12 Sep 2026](https://pokepast.es/348d665e808bb457)
  - [George Caddell, 804th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0692/teamlist)
  - [Andrew Gouck, 74th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0108/teamlist)
  - [Óscar Martínez Rosell, 600th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0708/teamlist)
  - [jessditta, , 10 Sep 2026](https://pokepast.es/25ab0498e06c8ffb)
  - [mofumofunatsuhi, , 13 Sep 2026](https://pokepast.es/f85b026e5b0e6567)
  - [Martin Steinbaron, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1006/teamlist)
  - [Ronan Kitchen, 551st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0970/teamlist)
  - [oshio_pokemon, , 11 Sep 2026](https://pokepast.es/667cb69f9c820c84)
  - [Padrick Moran, 748th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1071/teamlist)
  - [Prncessdiana, , 20 Sep 2026](https://pokepast.es/1c95ff346ce5b161)
  - [Curtis Ridings, 128th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0184/teamlist)
  - [Damahni Palmer, , 15 Sep 2026](https://pokepast.es/b915ba990518d865)
  - [Wesley Brainard, 201st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0114/teamlist)
  - [Vito Jacono, 825th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0175/teamlist)
  - [Max Hofmann, 954th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1089/teamlist)
  - [Chern Yean Sim, 200th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0213/teamlist)
  - [William Pye, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0229/teamlist)
  - [Kazeno_shion, , 10 Sep 2026](https://pokepast.es/e638bc044d3cb47f)
  - [Victor Bonfili, 929th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0979/teamlist)
  - [Nate Innocenti, , 10 Sep 2026](https://pokepast.es/af730dd6acb60086)
  - [Thomas Schultz, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1054/teamlist)
  - [Tom de Gruijter, 217th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1093/teamlist)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/01069fa4762c8613)
  - [Duy Nguyen, 892nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0598/teamlist)
  - [Nick Smith, 780th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1003/teamlist)
  - [Leon-Máxim Seul, 447th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0513/teamlist)
  - [Jayson Lyon, 837th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0516/teamlist)
  - [Herbert Herrmann, 950th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0373/teamlist)
  - [Jack Kent, 934th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0725/teamlist)
  - [squishvgc, , 13 Sep 2026](https://pokepast.es/4e238aef62574bf3)
  - [PathogenVGC, , 11 Sep 2026](https://pokepast.es/e8e7cf8271e66481)
  - [Nicholas McLean, 31st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0058/teamlist)
  - [Joseph Frontera, 848th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0883/teamlist)
  - [Jordan Duprey, 698th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0447/teamlist)
  - [Eric Uada, 268th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0199/teamlist)
  - [Noah Sim, 1030th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0579/teamlist)
  - [René Busam, 908th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1117/teamlist)
  - [Yuta Ishigaki, , 10 Sep 2026](https://pokepast.es/f5b17f03de49b848)
  - [Luna Zacharias Zetsche, 857th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0859/teamlist)
  - [Christopher Gibson, 21st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0275/teamlist)
  - [Duy Thang Nguyen, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0617/teamlist)

- Sub-community pass: 977 primary teams, 151 tokens, modularity 0.50; unconnected tokens: Ninetales-Alola (other item; Focus Sash 6/12), Indeedee-F@Psychic Seed, Maushold (other item; Chople Berry 5/9), Talonflame (other item; Focus Sash 5/9), Weavile (other item; Focus Sash 4/6), Dragonite (other item; Life Orb 4/5), Indeedee (other item; Choice Scarf 4/5), Scrafty (other item; Focus Sash 2/5), Mamoswine (other item; Focus Sash 4/4), Politoed (other item; Sitrus Berry 3/4), Aerodactyl (other item; Focus Sash 2/3), Annihilape (other item; Leftovers 2/3), Armarouge (other item; Life Orb 2/3), Chandelure (other item; Chandelurite 1/3), Corviknight (other item; Leftovers 3/3), Espathra (other item; Focus Sash 2/3), Mawile (other item; Mawilite 3/3), Raichu (other item; Raichunite X 3/3), Torkoal (other item; Charcoal 2/3), Toxapex (other item; Leftovers 3/3), Tsareena (other item; Wide Lens 3/3); unassigned within the community: 5 teams (0.5% of its primary weight); hybrid teams of the community left out: 412
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 4 (0.65) | 6 (0.57) | 8 (0.50) | 9 (0.43) | 10 (0.38) |

#### Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo (639 primary teams, 274 distinct builds, top pair on 475/639)
- Megas on member teams: Raichu-Y 504, Staraptor 263, Salamence 244, Froslass 30, Golisopod 24, Garchomp-Z 20
- Top species by team share: Rillaboom 93%, Raichu 79%, Gholdengo 74%, Arcanine-Hisui 65%, Staraptor 41%, Salamence 38%
- Token label: Rillaboom / Raichu@Raichunite Y / Gholdengo
- Mode tags on primary teams: Tailwind 512, Setup 166, Snow 46, Trick Room 22, Psyspam 12, Screens 12, Sand 11, Sun 9, Rain 6, Perish Trap 1
- Primary teams: 639 (67.2% of the community's primary weight), hybrid teams: 127 (13.1%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Excadrill (other item; Focus Sash 3/4)+Milotic (other item; Leftovers 108/185), Milotic (other item; Leftovers 108/185)+Ceruledge@Grassy Seed, Milotic (other item; Leftovers 108/185)+Hydreigon@Choice Scarf, Ceruledge (other item; Colbur Berry 10/10)+Milotic (other item; Leftovers 108/185), Ceruledge@Grassy Seed+Staraptor@Staraptite, Milotic (other item; Leftovers 108/185)+Grimmsnarl@Light Clay, Sylveon (other item; Fairy Feather 215/221)+Staraptor@Staraptite, Milotic (other item; Leftovers 108/185)+Tyranitar@Tyranitarite, Milotic (other item; Leftovers 108/185)+Metagross@Metagrossite, Farigiraf (other item; Sitrus Berry 41/51)+Sylveon (other item; Fairy Feather 215/221), Garchomp@Choice Scarf+Staraptor@Staraptite, Whimsicott (other item; Focus Sash 13/18)+Staraptor@Staraptite, Milotic (other item; Leftovers 108/185)+Baxcalibur@Baxcalibrite, Hydreigon@Choice Scarf+Staraptor@Staraptite, Milotic (other item; Leftovers 108/185)+Absol@Absolite Z, Milotic (other item; Leftovers 108/185)+Aerodactyl@Aerodactylite, Garchomp (other item; Sitrus Berry 6/15)+Staraptor@Staraptite, Milotic (other item; Leftovers 108/185)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 241/299)+Primarina@Grassy Seed, Arcanine-Hisui (other item; Focus Sash 465/475)+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 465/475)+Gardevoir@Gardevoirite, Glimmora (other item; Focus Sash 10/12)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 465/475)+Sylveon (other item; Fairy Feather 215/221), Arcanine-Hisui (other item; Focus Sash 465/475)+Annihilape@Choice Scarf, Milotic (other item; Leftovers 108/185)+Staraptor@Staraptite, Primarina@Grassy Seed+Raichu@Raichunite Y, Baxcalibur (other item; Life Orb 4/5)+Raichu@Raichunite Y, Incineroar (other item; Sitrus Berry 241/299)+Garchomp@Choice Scarf, Arcanine-Hisui (other item; Focus Sash 465/475)+Kingambit (other item; Life Orb 74/151), Arcanine-Hisui (other item; Focus Sash 465/475)+Gholdengo@Grassy Seed, Milotic (other item; Leftovers 108/185)+Charizard@Charizardite Y, Gholdengo (other item; Life Orb 575/595)+Ceruledge@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Staraptor@Staraptite, Ceruledge@Grassy Seed+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 575/595)+Blaziken@Blazikenite, Primarina (other item; Leftovers 12/28)+Staraptor@Staraptite, Gholdengo (other item; Life Orb 575/595)+Floette-Eternal@Floettite, Gholdengo@Choice Scarf+Staraptor@Staraptite, Gholdengo (other item; Life Orb 575/595)+Aerodactyl@Aerodactylite, Gholdengo (other item; Life Orb 575/595)+Absol@Absolite Z, Arcanine-Hisui (other item; Focus Sash 465/475)+Sneasler (other item; White Herb 95/112), Raichu@Raichunite Y+Staraptor@Staraptite, Salamence@Salamencite+Tyranitar@Tyranitarite, Arcanine-Hisui (other item; Focus Sash 465/475)+Primarina@Grassy Seed, Basculegion (other item; Life Orb 43/63)+Sylveon (other item; Fairy Feather 215/221), Arcanine-Hisui (other item; Focus Sash 465/475)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 465/475)+Indeedee-F (other item; Rocky Helmet 8/17), Glimmora (other item; Focus Sash 10/12)+Incineroar (other item; Sitrus Berry 241/299), Gholdengo (other item; Life Orb 575/595)+Milotic@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Tyranitar@Tyranitarite, Annihilape@Choice Scarf+Raichu@Raichunite Y, Sylveon (other item; Fairy Feather 215/221)+Raichu@Raichunite Y, Raichu@Raichunite Y+Volcarona@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Milotic (other item; Leftovers 108/185), Arcanine-Hisui (other item; Focus Sash 465/475)+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 575/595)+Garchomp@Choice Scarf, Gholdengo (other item; Life Orb 575/595)+Annihilape@Choice Scarf, Froslass@Froslassite+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 575/595)+Primarina@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Sneasler@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Sylveon (other item; Fairy Feather 215/221), Gholdengo@Grassy Seed+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 575/595)+Raichu@Raichunite Y, Rillaboom (other item; Miracle Seed 741/901)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 741/901)+Farigiraf@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Ceruledge@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Gholdengo@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 741/901)+Aerodactyl@Aerodactylite, Blaziken (other item; Focus Sash 6/8)+Rillaboom (other item; Miracle Seed 741/901), Pelipper (other item; Focus Sash 6/13)+Rillaboom (other item; Miracle Seed 741/901), Rillaboom (other item; Miracle Seed 741/901)+Glimmora@Glimmoranite, Rillaboom (other item; Miracle Seed 741/901)+Primarina@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Volcarona@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 741/901)+Milotic@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Blaziken@Blazikenite, Lycanroc-Dusk (other item; Focus Sash 4/4)+Rillaboom (other item; Miracle Seed 741/901), Rillaboom (other item; Miracle Seed 741/901)+Vanilluxe (other item; Choice Scarf 4/4), Camerupt (other item; Cameruptite 4/4)+Rillaboom (other item; Miracle Seed 741/901), Rillaboom (other item; Miracle Seed 741/901)+Froslass@Froslassite, Rillaboom (other item; Miracle Seed 741/901)+Sneasler@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Dragonite@Dragoninite, Kingambit (other item; Life Orb 74/151)+Raichu@Raichunite Y, Rillaboom (other item; Miracle Seed 741/901)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 741/901)+Lucario@Lucarionite Z, Rillaboom (other item; Miracle Seed 741/901)+Salamence@Salamencite, Arcanine-Hisui (other item; Focus Sash 465/475)+Rillaboom (other item; Miracle Seed 741/901), Arcanine-Hisui (other item; Focus Sash 465/475)+Gholdengo (other item; Life Orb 575/595), Gholdengo (other item; Life Orb 575/595)+Rillaboom (other item; Miracle Seed 741/901), Gholdengo (other item; Life Orb 575/595)+Gardevoir@Gardevoirite, Rillaboom (other item; Miracle Seed 741/901)+Raichu@Raichunite Y, Ceruledge (other item; Colbur Berry 10/10)+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 575/595)+Salamence@Salamencite, Sneasler (other item; White Herb 95/112)+Raichu@Raichunite Y, Rillaboom (other item; Miracle Seed 741/901)+Sneasler (other item; White Herb 95/112), Rillaboom (other item; Miracle Seed 741/901)+Tyranitar@Tyranitarite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Rillaboom (other item; Miracle Seed 741/901) | 92.5% |
| Raichu@Raichunite Y | 80.4% |
| Gholdengo (other item; Life Orb 575/595) | 67.8% |
| Arcanine-Hisui (other item; Focus Sash 465/475) | 64.2% |
| Staraptor@Staraptite | 43.5% |
| Sylveon (other item; Fairy Feather 215/221) | 30.3% |
| Milotic (other item; Leftovers 108/185) | 25.5% |
| Ceruledge@Grassy Seed | 10.8% |
| Primarina@Grassy Seed | 1.7% |
| Glimmora (other item; Focus Sash 10/12) | 1.6% |
| Milotic@Grassy Seed | 1.6% |
| Grimmsnarl@Light Clay | 1.5% |
| Hydreigon@Choice Scarf | 1.5% |
| Garchomp@Choice Scarf | 1.4% |
| Tyranitar@Tyranitarite | 1.3% |
| Excadrill (other item; Focus Sash 3/4) | 0.6% |
| Lycanroc-Dusk (other item; Focus Sash 4/4) | 0.6% |
| Blaziken@Blazikenite | 0.5% |
| Vanilluxe (other item; Choice Scarf 4/4) | 0.4% |
| Baxcalibur (other item; Life Orb 4/5) | 0.4% |
| Camerupt (other item; Cameruptite 4/4) | 0.2% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Naoya Takasago, 16th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0135/teamlist)
  - [Conner Pietrusinski, Top 8, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/q65vnQVtYoaIlJgWdymZ)
  - [Jonathan Clarke, 39th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0106/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Lorenzo Colombo, 15th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0430/teamlist)
  - [Esa Ishaque, Top 8, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/xwGrkGvg8PXbgIxsRNlv)
  - [Jack Wallis, 187th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0017/teamlist)
  - [Natasha Pearson, 253rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0145/teamlist)
  - [Georgia Challoner, 307th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0244/teamlist)
  - [Jannik Koch, 75th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0745/teamlist)
  - [Richard Kunze, 95th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0712/teamlist)
  - [Arthur Sticha, 180th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0707/teamlist)
  - [Liam Chatterjee, 288th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0871/teamlist)
  - [Alessandro Di Rosa, 433rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1103/teamlist)
  - [Patrick Tiedemann, 471st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0013/teamlist)
  - [Julian Riegel, 489th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0973/teamlist)
  - [Fabian Dreischmeier, 603rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0868/teamlist)
  - [Hélio Sven Figueiredo Borges, 630th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0788/teamlist)
  - [Bene Stemann, 644th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0258/teamlist)
  - [Jolyne Alessandra Wojach, 655th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0563/teamlist)
  - [Corentin Craeyeveld, 1055th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0425/teamlist)
  - [Sofia Volonnino, 1095th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0762/teamlist)
  - [Niklas Lämmermann, 1102nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1115/teamlist)
  - [Jonas Nienhüser, 509th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0603/teamlist)
  - [Kristopher Marinas, 681st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0916/teamlist)
  - [zoryavgc, 41st, 22 Sep 2026](https://pokepast.es/0bd9b6346f3630a1)
  - [Hoang Ho, 41st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0584/teamlist)
  - [Liam Shorter, 47th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0237/teamlist)
  - [jade hyde, 49th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0006/teamlist)
  - [Dillon Kleinvehn, 113th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0098/teamlist)
  - [Norah Bowman, 251st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0287/teamlist)
  - [Gerald Mickles, 283rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0660/teamlist)
  - [Shohei Kimura, , 16 Sep 2026](https://pokepast.es/fd6fe5bb94a024fb)
  - [misterteshima, 75th, 21 Sep 2026](https://pokepast.es/a8154c05ecfa0e20)
  - [Noah Neel, 75th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1078/teamlist)
  - [Lorenzo Dal Prato, 206th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0528/teamlist)
  - [Jose Rider Lorenzo, 228th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0099/teamlist)
  - [Ricardo Fuentes, 589th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0811/teamlist)
  - [Jasmine Brailey, 954th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0143/teamlist)
  - [Jan Schultis, 105th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0372/teamlist)
  - [David Garcia, 596th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0579/teamlist)
  - [Sergio Peregrín Onorato, 671st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0141/teamlist)
  - [Thomas Gravouille, 10th, 20 Sep 2026](https://pokepast.es/f1837233cdf5dcda)
  - [Benjamin Philipp, 269th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0187/teamlist)
  - [Fabian Seipt, 419th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0903/teamlist)
  - [Maurice Hofmann, 641st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0988/teamlist)
  - [Denis Jorganovic, 865th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0369/teamlist)
  - [Jackson Kostantin, 266th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0371/teamlist)
  - [Jacob Doria-Rose, 620th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0939/teamlist)
  - [Samuel Rotton, 727th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0281/teamlist)
  - [catrachokidd01, 383rd, 21 Sep 2026](https://pokepast.es/e01ad4bad0da9ccc)
  - [Jonathan Echeverria, 383rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1032/teamlist)
  - [slothdiesel, , 14 Sep 2026](https://pokepast.es/31a5a41549fd6ddf)
  - [Ethan Matijevic, 725th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0224/teamlist)
  - [Rehan Ahmed, 753rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0037/teamlist)
  - [Gerry Thompson, 1037th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0496/teamlist)
  - [Ifeanyi Okafor, 1048th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0456/teamlist)
  - [Patrick Daglas, 148th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0007/teamlist)
  - [Ricky Fleszewski, 514th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0545/teamlist)
  - [Mads Lehde Fischer, 818th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0949/teamlist)
  - [Friedrich Halbrügge, 871st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0163/teamlist)
  - [Michel Dombrowski, 1027th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0076/teamlist)
  - [Brian Theis, 271st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0971/teamlist)
  - [Jonathan Melendez, 300th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0280/teamlist)
  - [Jarvis Johnson, 609th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0089/teamlist)
  - [Kshaunish Shaik, 32nd, 21 Sep 2026](https://pokepast.es/d41341435d22d77b)
  - [Kshaunish Shaik, 32nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0656/teamlist)
  - [Mirko Fioravanti, 258th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0167/teamlist)
  - [Sean Chalungsooth, 1067th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0285/teamlist)
  - [Roxanne Joniau, 339th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0324/teamlist)
  - [Joseph Calderon, 771st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0092/teamlist)
  - [Mikal Mahoney, 117th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0703/teamlist)
  - [Glenn Rackley, 848th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0137/teamlist)
  - [Tyler Deacy, 127th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0188/teamlist)
  - [Tristan Brissette, 826th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0238/teamlist)
  - [Dylan Thommen, 315th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0802/teamlist)
  - [Saad Sohail, 911th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0131/teamlist)
  - [Pascal Doert, 132nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0415/teamlist)
  - [Riley Hart, 505th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0180/teamlist)
  - [Andrea Nesti, 45th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0930/teamlist)
  - [Gianmaria Sorbino, 222nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0770/teamlist)
  - [Nicolò Pollato, 262nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0447/teamlist)
  - [Leonardo Bonanomi, 1047th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0619/teamlist)
  - [Shohei Kimura, , 12 Sep 2026](https://pokepast.es/8ef29e6c905c1699)
  - [Thomas Schneider, 308th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0211/teamlist)
  - [Stefan Roos, 574th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0613/teamlist)
  - [Avery Cambero, 146th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0010/teamlist)
  - [Stefan Brandt, 405th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0108/teamlist)
  - [Kyler Williams, 411th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0631/teamlist)
  - [Falko Boere, 60th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0195/teamlist)
  - [Jacob Baines-Vosper, 987th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0721/teamlist)
  - [David Cases, 1065th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0909/teamlist)
  - [Ruby McEachern, 89th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0434/teamlist)
  - [Alexander Rassael, 520th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0078/teamlist)
  - [Jacob Zlotnitsky, 736th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0104/teamlist)
  - [Simon Carmichael, 177th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0020/teamlist)
  - [Julian Hernandez-Perocier, 418th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0978/teamlist)
  - [Luca Santelli, 411th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0462/teamlist)
  - [Edoardo Bertani, 113th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0560/teamlist)
  - [John Polzin, 1071st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0093/teamlist)
  - [Mary Cook, 167th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0498/teamlist)
  - [Jonte Schwedler, 353rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0650/teamlist)
  - [Radu Troasca, 634th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0046/teamlist)
  - [Chris Meikle, 160th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0045/teamlist)
  - [Dylan Morgan, 711th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0369/teamlist)
  - [Jannek Brödling, 375th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0542/teamlist)
  - [Andres Jacobo, 951st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0704/teamlist)
  - [Aidan Junker, 526th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0383/teamlist)
  - [Patrick Gabbett, 662nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0740/teamlist)
  - [Jack Geronime, 260th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0220/teamlist)
  - [Micah Campbell, 922nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0326/teamlist)
  - [Hiroto Kamazawa, , 21 Sep 2026](https://pokepast.es/652a6122d64aa2c1)
  - [Richard Wan, 433rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0879/teamlist)
  - [Salvatore Maira, 597th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0626/teamlist)
  - [Sandro Pocrnja, 98th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0886/teamlist)
  - [Guilherme Martins, 659th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0941/teamlist)
  - [Astrid Gurski, 983rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0809/teamlist)
  - [Wyatt McDonald, , 14 Sep 2026](https://pokepast.es/7c4bc48a6a1906e6)
  - [pathogenvgc, , 14 Sep 2026](https://pokepast.es/a630a7a5018325c9)
  - [Vincent VILLIERS, 523rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0689/teamlist)
  - [Yuta Ishigaki, , 10 Sep 2026](https://pokepast.es/57c82d09be532ce2)
  - [Emanuel Ruf, 938th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0283/teamlist)
  - [Robin Peter, 944th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1104/teamlist)
  - [Gabriel Buchta, 1038th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1094/teamlist)
  - [Ryan Caldwell, 199th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0325/teamlist)
  - [_hendez_, , 21 Sep 2026](https://pokepast.es/2d99e9105e5f7a58)
  - [Henry Hernandez, 339th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0377/teamlist)
  - [David Sakulov, 1084th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0645/teamlist)
  - [Stephen Morris, 791st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0780/teamlist)
  - [Robbie Van der Raaf, 179th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0244/teamlist)
  - [Michael Lewis, 615th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0015/teamlist)

#### Community 0 / Sub-community 2: Setup (232 primary teams, 102 distinct builds, top pair on 136/232)
- Megas on member teams: Floette 110, Raichu-Y 89, Salamence 83, Garchomp-Z 41, Lucario-Z 39, Aerodactyl 14
- Top species by team share: Rillaboom 100%, Incineroar 91%, Sneasler 67%, Gholdengo 65%, Floette-Eternal 47%, Raichu 38%
- Token label: Incineroar / Sneasler@Grassy Seed
- Mode tags on primary teams: Setup 117, Tailwind 111, Sun 14, Snow 8, Rain 7, Trick Room 6, Perish Trap 5, Psyspam 3, Sand 2, Screens 2
- Primary teams: 232 (22.1% of the community's primary weight), hybrid teams: 60 (6.0%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Aerodactyl@Aerodactylite+Charizard@Charizardite Y, Aerodactyl@Aerodactylite+Lucario@Lucarionite Z, Basculegion (other item; Life Orb 43/63)+Lucario@Lucarionite Z, Pelipper (other item; Focus Sash 6/13)+Lucario@Lucarionite Z, Basculegion@Choice Scarf+Volcarona@Grassy Seed, Baxcalibur@Baxcalibrite+Volcarona@Grassy Seed, Volcarona (other item; Rocky Helmet 14/24)+Garchomp@Garchompite Z, Basculegion@Choice Scarf+Garchomp@Garchompite Z, Garchomp@Garchompite Z+Volcarona@Grassy Seed, Garchomp@Garchompite Z+Lucario@Lucarionite Z, Basculegion (other item; Life Orb 43/63)+Garchomp@Garchompite Z, Basculegion (other item; Life Orb 43/63)+Volcarona@Grassy Seed, Basculegion (other item; Life Orb 43/63)+Baxcalibur@Baxcalibrite, Floette-Eternal@Floettite+Gholdengo@Choice Scarf, Primarina (other item; Leftovers 12/28)+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Aerodactyl@Aerodactylite, Incineroar (other item; Sitrus Berry 241/299)+Rillaboom@Eject Button, Altaria (other item; Altarianite 3/5)+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 241/299)+Lucario@Lucarionite Z, Incineroar (other item; Sitrus Berry 241/299)+Gengar@Gengarite, Primarina (other item; Leftovers 12/28)+Lucario@Lucarionite Z, Floette-Eternal@Floettite+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Garchomp@Garchompite Z, Dragonite@Dragoninite+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 241/299)+Basculegion@Choice Scarf, Incineroar (other item; Sitrus Berry 241/299)+Pawmot (other item; Focus Sash 11/12), Incineroar (other item; Sitrus Berry 241/299)+Dragonite@Dragoninite, Rillaboom@Eject Button+Sneasler@Grassy Seed, Dragonite@Dragoninite+Sneasler@Grassy Seed, Milotic (other item; Leftovers 108/185)+Aerodactyl@Aerodactylite, Incineroar (other item; Sitrus Berry 241/299)+Gholdengo@Choice Scarf, Garchomp@Garchompite Z+Sneasler@Grassy Seed, Pawmot (other item; Focus Sash 11/12)+Salamence@Salamencite, Basculegion (other item; Life Orb 43/63)+Incineroar (other item; Sitrus Berry 241/299), Basculegion@Choice Scarf+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 241/299)+Pelipper (other item; Focus Sash 6/13), Incineroar (other item; Sitrus Berry 241/299)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 241/299)+Sneasler@Grassy Seed, Farigiraf (other item; Sitrus Berry 41/51)+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Primarina@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Kommo-o (other item; Leftovers 20/26), Incineroar (other item; Sitrus Berry 241/299)+Delphox@Delphoxite, Incineroar (other item; Sitrus Berry 241/299)+Absol@Absolite Z, Floette-Eternal@Floettite+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Baxcalibur@Baxcalibrite, Incineroar (other item; Sitrus Berry 241/299)+Garchomp@Choice Scarf, Incineroar (other item; Sitrus Berry 241/299)+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Volcarona (other item; Rocky Helmet 14/24), Basculegion (other item; Life Orb 43/63)+Farigiraf (other item; Sitrus Berry 41/51), Aerodactyl@Aerodactylite+Sneasler@Grassy Seed, Milotic (other item; Leftovers 108/185)+Charizard@Charizardite Y, Gholdengo@Choice Scarf+Sneasler@Grassy Seed, Kingambit (other item; Life Orb 74/151)+Sneasler@Grassy Seed, Salamence@Salamencite+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Ninetales-Alola@Light Clay, Gholdengo (other item; Life Orb 575/595)+Floette-Eternal@Floettite, Gholdengo@Choice Scarf+Staraptor@Staraptite, Gholdengo (other item; Life Orb 575/595)+Aerodactyl@Aerodactylite, Gengar@Gengarite+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Farigiraf@Grassy Seed, Kommo-o (other item; Leftovers 20/26)+Floette-Eternal@Floettite, Charizard@Charizardite Y+Sneasler@Grassy Seed, Basculegion (other item; Life Orb 43/63)+Sylveon (other item; Fairy Feather 215/221), Incineroar (other item; Sitrus Berry 241/299)+Golisopod@Golisopite, Glimmora (other item; Focus Sash 10/12)+Incineroar (other item; Sitrus Berry 241/299), Lucario@Lucarionite Z+Sneasler@Grassy Seed, Floette-Eternal@Floettite+Salamence@Salamencite, Raichu@Raichunite Y+Volcarona@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 741/901)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 741/901)+Aerodactyl@Aerodactylite, Rillaboom (other item; Miracle Seed 741/901)+Volcarona@Grassy Seed, Rillaboom (other item; Miracle Seed 741/901)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 741/901)+Sneasler@Grassy Seed, Gholdengo (other item; Life Orb 575/595)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 741/901)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 741/901)+Lucario@Lucarionite Z
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Incineroar (other item; Sitrus Berry 241/299) | 89.6% |
| Sneasler@Grassy Seed | 63.3% |
| Floette-Eternal@Floettite | 47.2% |
| Garchomp@Garchompite Z | 17.9% |
| Lucario@Lucarionite Z | 15.1% |
| Basculegion (other item; Life Orb 43/63) | 14.9% |
| Volcarona@Grassy Seed | 12.1% |
| Basculegion@Choice Scarf | 7.9% |
| Charizard@Charizardite Y | 6.3% |
| Aerodactyl@Aerodactylite | 5.6% |
| Dragonite@Dragoninite | 4.4% |
| Gholdengo@Choice Scarf | 2.9% |
| Altaria (other item; Altarianite 3/5) | 1.8% |
| Pawmot (other item; Focus Sash 11/12) | 1.7% |
| Rillaboom@Grassy Seed | 1.4% |
| Delphox@Delphoxite | 0.4% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [vi0ra_pokemon, , 14 Sep 2026](https://pokepast.es/74aea655c757917f)
  - [Christopher Epps, 732nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0150/teamlist)
  - [Stefano Greppi, , 15 Sep 2026](https://pokepast.es/045cc1b2f27f9315)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Dominik Mairiedl, 85th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1082/teamlist)
  - [Max Demjancik, 153rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0832/teamlist)
  - [Liam Shorter, 47th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0237/teamlist)
  - [jade hyde, 49th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0006/teamlist)
  - [Patrick Kwant, 213th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0496/teamlist)
  - [Dillon Kleinvehn, 113th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0098/teamlist)
  - [Blake Silver, 204th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0318/teamlist)
  - [Norah Bowman, 251st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0287/teamlist)
  - [Gerald Mickles, 283rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0660/teamlist)
  - [Shohei Kimura, , 16 Sep 2026](https://pokepast.es/fd6fe5bb94a024fb)
  - [misterteshima, 75th, 21 Sep 2026](https://pokepast.es/a8154c05ecfa0e20)
  - [Noah Neel, 75th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1078/teamlist)
  - [Adria Rodriguez Pol, 86th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0142/teamlist)
  - [Chris Zhao, 248th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0805/teamlist)
  - [Yan Yuen, 669th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0967/teamlist)
  - [Sean Chalungsooth, 1067th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0285/teamlist)
  - [Hyeonseung Lee, 975th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0872/teamlist)
  - [Patrick Blainey, 100th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0177/teamlist)
  - [Jack Mackinnon, 249th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0088/teamlist)
  - [Manuel Jesús Azogil, 226th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0869/teamlist)
  - [Matthias Brandhofer, 340th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0371/teamlist)
  - [Sidi I. Haidala, Champion, 12 Sep 2026](https://pokepast.es/a634bf11f53735ba)
  - [Ryan Jackson, 352nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0518/teamlist)
  - [Stephen Taylor, 762nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0429/teamlist)
  - [Liam Heppard, 868th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0847/teamlist)
  - [sinistchacha, 69th, 22 Sep 2026](https://pokepast.es/097433efcc505367)
  - [Jakob Graff, 69th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0989/teamlist)
  - [Marius Wels, 274th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0569/teamlist)
  - [Shoma Honami, , 11 Sep 2026](https://pokepast.es/199e4fdf392315b2)
  - [Emery Joseph, 43rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0004/teamlist)
  - [James Berkley, 752nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0867/teamlist)
  - [Eric Rios, 1st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1025/teamlist)
  - [Adrián Lozano Navarro, 101st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0450/teamlist)
  - [Luis Miguel Montesdeoca Martínez, 107th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0640/teamlist)
  - [Alex Gascon Bononad, 318th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0781/teamlist)
  - [David Perez, 858th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0383/teamlist)
  - [Jordi Casado Rejas, 976th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1002/teamlist)
  - [Deondre Cutler, 794th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0943/teamlist)
  - [Sahen Rai, 301st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1028/teamlist)
  - [Roman Carfi, 173rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0002/teamlist)
  - [Jannek Brödling, 375th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0542/teamlist)
  - [Dylan Morgan, 711th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0369/teamlist)
  - [Matthew Suarez, 224th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0096/teamlist)
  - [Shane De Silva, 200th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0538/teamlist)
  - [Pelle Becker, 1005th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0855/teamlist)
  - [Adam Azaiez, 244th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1057/teamlist)
  - [Charlie Hall, 984th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0948/teamlist)
  - [Sandro Pocrnja, 98th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0886/teamlist)
  - [David Sakulov, 1084th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0645/teamlist)
  - [Sam Sperl, 117th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0873/teamlist)
  - [Alejandro Tovar, 331st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0251/teamlist)
  - [Michael Lewis, 615th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0015/teamlist)
  - [Daniel Harris, 975th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0775/teamlist)
  - [Richard Wan, 433rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0879/teamlist)
  - [Alexander Miller, 655th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0718/teamlist)
  - [sectoniaservant, , 11 Sep 2026](https://pokepast.es/c8f60c5168bd6a83)
  - [Leon Drescher, 67th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1016/teamlist)
  - [Nicholas Woodhouse, 226th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0292/teamlist)
  - [clark smith, 1011th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0116/teamlist)
  - [FranDrawer03, , 11 Sep 2026](https://pokepast.es/961f0667b90b830c)

#### Community 0 / Sub-community 4: Volcarona / Glimmora@Glimmoranite (8 primary teams, 6 distinct builds, top pair on 5/8)
- Megas on member teams: Glimmora 5, Raichu-Y 3, Staraptor 2, Charizard-Y 1, Gengar 1, Metagross 1
- Top species by team share: Rillaboom 88%, Volcarona 75%, Glimmora 63%, Kingambit 38%, Raichu 38%, Swampert 38%
- Token label: Volcarona / Glimmora@Glimmoranite
- Mode tags on primary teams: Tailwind 6, Sand 2, Setup 2, Sun 1
- Primary teams: 8 (0.9% of the community's primary weight), hybrid teams: 11 (1.1%)
- Date range: 2026-09-15 to 2026-09-27
- Core pairs: Indeedee-F (other item; Rocky Helmet 8/17)+Milotic@Psychic Seed, Indeedee-F (other item; Rocky Helmet 8/17)+Gardevoir@Gardevoirite, Blaziken (other item; Focus Sash 6/8)+Metagross@Metagrossite, Dragapult (other item; Life Orb 11/14)+Glimmora@Glimmoranite, Volcarona (other item; Rocky Helmet 14/24)+Glimmora@Glimmoranite, Swampert (other item; Sitrus Berry 3/9)+Volcarona (other item; Rocky Helmet 14/24), Dragapult (other item; Life Orb 11/14)+Indeedee-F (other item; Rocky Helmet 8/17), Indeedee-F (other item; Rocky Helmet 8/17)+Metagross@Metagrossite, Volcarona (other item; Rocky Helmet 14/24)+Garchomp@Garchompite Z, Kingambit (other item; Life Orb 74/151)+Glimmora@Glimmoranite, Milotic (other item; Leftovers 108/185)+Metagross@Metagrossite, Kingambit (other item; Life Orb 74/151)+Volcarona (other item; Rocky Helmet 14/24), Arcanine-Hisui (other item; Focus Sash 465/475)+Gardevoir@Gardevoirite, Incineroar (other item; Sitrus Berry 241/299)+Volcarona (other item; Rocky Helmet 14/24), Arcanine-Hisui (other item; Focus Sash 465/475)+Indeedee-F (other item; Rocky Helmet 8/17), Blaziken (other item; Focus Sash 6/8)+Rillaboom (other item; Miracle Seed 741/901), Rillaboom (other item; Miracle Seed 741/901)+Glimmora@Glimmoranite, Gholdengo (other item; Life Orb 575/595)+Gardevoir@Gardevoirite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Volcarona (other item; Rocky Helmet 14/24) | 77.0% |
| Glimmora@Glimmoranite | 63.5% |
| Swampert (other item; Sitrus Berry 3/9) | 36.5% |
| Dragapult (other item; Life Orb 11/14) | 23.0% |
| Indeedee-F (other item; Rocky Helmet 8/17) | 23.0% |
| Milotic@Psychic Seed | 13.5% |
| Blaziken (other item; Focus Sash 6/8) | 9.5% |
| Metagross@Metagrossite | 9.5% |
| Gardevoir@Gardevoirite | 0.0% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Jannik Pawasserat, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0120/teamlist)
  - [Matt Tennant, 42nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0790/teamlist)
  - [Emanuel Ruf, 938th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0283/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Diana Clark, 210th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0454/teamlist)
  - [Jack Mountain, 784th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0385/teamlist)
  - [Daniel Tautz, 49th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0397/teamlist)
  - [Damahni Palmer, 27th, 21 Sep 2026](https://pokepast.es/9e422cab36495fcb)
  - [Hiroto Kamazawa, , 21 Sep 2026](https://pokepast.es/652a6122d64aa2c1)
  - [Sam Grifa, 57th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0162/teamlist)
  - [Thomas Marshall, 73rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0105/teamlist)
  - [Lucas Mester-Christensen, 497th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0136/teamlist)
  - [Demond Williams, 1049th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0589/teamlist)
  - [Justin Cerioni, , 15 Sep 2026](https://pokepast.es/87947bfa8f77f2c4)
  - [Juri Michael Schwarzer, 664th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0008/teamlist)

#### Community 0 / Sub-community 5: Perish Trap (Mega Gengar) (7 primary teams, 6 distinct builds, top pair on 5/7)
- Megas on member teams: Gengar 7, Charizard-Y 1, Lucario-Z 1, Scovillain 1
- Top species by team share: Gengar 100%, Incineroar 100%, Rillaboom 100%, Kommo-o 57%, Dragonite 29%, Gholdengo 29%
- Token label: Gengar@Gengarite + Rillaboom@Eject Button/Kommo-o
- Mode tags on primary teams: Perish Trap 5, Setup 3, Sun 1
- Primary teams: 7 (0.7% of the community's primary weight), hybrid teams: 7 (0.7%)
- Date range: 2026-09-10 to 2026-09-27
- Core pairs: Gengar@Gengarite+Rillaboom@Eject Button, Kommo-o (other item; Leftovers 20/26)+Rillaboom@Eject Button, Kommo-o (other item; Leftovers 20/26)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 241/299)+Rillaboom@Eject Button, Kingambit (other item; Life Orb 74/151)+Rillaboom@Eject Button, Kingambit (other item; Life Orb 74/151)+Gengar@Gengarite, Kommo-o (other item; Leftovers 20/26)+Gholdengo@Grassy Seed, Incineroar (other item; Sitrus Berry 241/299)+Gengar@Gengarite, Rillaboom@Eject Button+Sneasler@Grassy Seed, Milotic (other item; Leftovers 108/185)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 241/299)+Kommo-o (other item; Leftovers 20/26), Kingambit (other item; Life Orb 74/151)+Kommo-o (other item; Leftovers 20/26), Gengar@Gengarite+Sneasler@Grassy Seed, Kommo-o (other item; Leftovers 20/26)+Floette-Eternal@Floettite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Gengar@Gengarite | 100.0% |
| Rillaboom@Eject Button | 69.1% |
| Kommo-o (other item; Leftovers 20/26) | 61.8% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Danial Syed, 453rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0658/teamlist)
  - [Jordi Martinez, 469th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0729/teamlist)
  - [Emery Joseph, 43rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0004/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Santino Tarquinio, 173rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0204/teamlist)
  - [Thaddeus Valentine, 258th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0727/teamlist)
  - [Paschalis Dermentzis, 20th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0846/teamlist)
  - [Lazaros Lazaropoulos, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0813/teamlist)
  - [Charalampos Frimas, 185th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0069/teamlist)
  - [Michael Mullen, 666th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0282/teamlist)
  - [Joshua Flickinger, 838th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0567/teamlist)

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite (45 distinct builds, 81 primary teams, top pair on 27/81); Sub-community 3: Golisopod@Golisopite / Farigiraf@Grassy Seed (3 distinct builds, 3 primary teams, top pair on 0/3); Sub-community 6: Garchomp / Whimsicott (0 distinct builds, 0 primary teams, top pair on 0/0); Sub-community 7: Archaludon / Pelipper (2 distinct builds, 2 primary teams, top pair on 2/2)

#### Community 0 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Rillaboom | Rillaboom (other item; Miracle Seed 741/901) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 901 | 0: 589 · 2: 224 · 1: 75 · 4: 7 · 3: 2 · 5: 2 · 7: 2 · unassigned: 0 |
| Rillaboom | Rillaboom@Eject Button | Sub-community 5: Perish Trap (Mega Gengar) | 11 | 2: 5 · 5: 5 · 0: 1 · unassigned: 0 |
| Rillaboom | Rillaboom@Grassy Seed | Sub-community 2: Setup | 11 | 0: 7 · 2: 3 · 1: 1 · unassigned: 0 |
| Gholdengo | Gholdengo (other item; Life Orb 575/595) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 595 | 0: 435 · 2: 137 · 1: 18 · 4: 1 · 5: 1 · 7: 1 · unassigned: 2 |
| Gholdengo | Gholdengo@Grassy Seed | Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite | 54 | 0: 34 · 1: 12 · 2: 6 · 5: 1 · 7: 1 · unassigned: 0 |
| Gholdengo | Gholdengo@Choice Scarf | Sub-community 2: Setup | 12 | 2: 7 · 0: 4 · 1: 1 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 2: Setup | 267 | 2: 148 · 0: 107 · 1: 12 · unassigned: 0 |
| Sneasler | Sneasler (other item; White Herb 95/112) | Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite | 112 | 0: 54 · 1: 49 · 2: 8 · 4: 1 · unassigned: 0 |
| Milotic | Milotic (other item; Leftovers 108/185) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 185 | 0: 159 · 2: 13 · 1: 9 · 5: 2 · unassigned: 2 |
| Milotic | Milotic@Grassy Seed | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 14 | 0: 9 · 2: 2 · 1: 1 · 3: 1 · 4: 1 · unassigned: 0 |
| Milotic | Milotic@Psychic Seed | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 6 | 0: 4 · 4: 1 · unassigned: 1 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 2: Setup | 61 | 2: 41 · 0: 20 · unassigned: 0 |
| Garchomp | Garchomp (other item; Sitrus Berry 6/15) | Sub-community 6: Garchomp / Whimsicott | 15 | 0: 14 · 2: 1 · unassigned: 0 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 9 | 0: 8 · 2: 1 · unassigned: 0 |
| Ceruledge | Ceruledge@Grassy Seed | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 60 | 0: 60 · unassigned: 0 |
| Ceruledge | Ceruledge (other item; Colbur Berry 10/10) | Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite | 10 | 0: 7 · 1: 3 · unassigned: 0 |
| Volcarona | Volcarona@Grassy Seed | Sub-community 2: Setup | 47 | 2: 22 · 0: 21 · 1: 4 · unassigned: 0 |
| Volcarona | Volcarona (other item; Rocky Helmet 14/24) | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 24 | 0: 9 · 4: 6 · 1: 4 · 2: 3 · 5: 1 · unassigned: 1 |
| Primarina | Primarina (other item; Leftovers 12/28) | Sub-community 3: Golisopod@Golisopite / Farigiraf@Grassy Seed | 28 | 0: 19 · 2: 7 · 1: 1 · 3: 1 · unassigned: 0 |
| Primarina | Primarina@Grassy Seed | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 10 | 0: 10 · unassigned: 0 |
| Baxcalibur | Baxcalibur@Baxcalibrite | Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite | 20 | 1: 8 · 0: 7 · 2: 5 · unassigned: 0 |
| Baxcalibur | Baxcalibur (other item; Life Orb 4/5) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 5 | 0: 3 · 2: 2 · unassigned: 0 |
| Glimmora | Glimmora@Glimmoranite | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 15 | 0: 7 · 4: 5 · 1: 2 · 2: 1 · unassigned: 0 |
| Glimmora | Glimmora (other item; Focus Sash 10/12) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 12 | 0: 10 · 1: 1 · 3: 1 · unassigned: 0 |
| Blaziken | Blaziken (other item; Focus Sash 6/8) | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 8 | 0: 6 · 1: 1 · 4: 1 · unassigned: 0 |
| Blaziken | Blaziken@Blazikenite | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 5 | 0: 3 · 1: 2 · unassigned: 0 |

### Community 1: Sneasler / Salamence
- Token label: Sneasler / Salamence
- Mode tags on primary teams: Tailwind 345, Sand 238, Psyspam 201, Snow 83, Setup 62, Sun 43, Trick Room 30, Rain 8, Screens 4
- Megas on primary teams: Salamence 431, Tyranitar 217, Froslass 71, Floette 59, Metagross 45, Charizard-Y 35, Garchomp-Z 28, Delphox 25, Gardevoir 17, Dragonite 15, Golisopod 14, Raichu-Y 12, Glimmora 11, Staraptor 11, Scovillain 10, Blaziken 9, Baxcalibur 6, Gengar 6, Absol-Z 5, Blastoise 5, Lucario-Z 5, Meowstic-F 4, Alakazam 3, Excadrill 3, Garchomp 3, Meganium 3, Steelix 3, Camerupt 2, Mawile 2, Pidgeot 2, Raichu-X 2, Aerodactyl 1, Chandelure 1, Eelektross 1, Feraligatr 1, Heracross 1, Lopunny 1, Manectric 1, Pyroar 1, Sableye 1, Sceptile 1, Starmie 1, Swampert 1, Venusaur 1
- Primary teams: 587 (primary share 19.2%), hybrid teams: 453 (hybrid share 15.1%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Scovillain+Lycanroc-Dusk@Focus Sash, Lycanroc-Dusk+Scovillain@Scovillainite, Lycanroc-Dusk+Scovillain, Typhlosion-Hisui+Indeedee@Focus Sash, Corviknight+Indeedee@Choice Scarf, Indeedee+Corviknight@Psychic Seed, Excadrill+Corviknight@Psychic Seed, Pawmot+Kingambit@Occa Berry, Corviknight+Excadrill@Focus Sash, Blaziken+Kingambit@Occa Berry, Excadrill+Tyranitar@Tyranitarite, Corviknight+Indeedee, Corviknight+Excadrill, Aerodactyl+Kingambit@Focus Sash, Tyranitar+Excadrill@Focus Sash, Tyranitar+Excadrill@Life Orb, Tyranitar+Corviknight@Psychic Seed, Corviknight+Tyranitar@Tyranitarite, Excadrill+Tyranitar, Lycanroc-Dusk+Rillaboom@Life Orb, Camerupt+Kingambit@Black Glasses, Delphox+Sneasler@Focus Sash, Hatterene+Kingambit@Black Glasses, Corviknight+Tyranitar, Lycanroc-Dusk+Kingambit@Black Glasses, Froslass+Lycanroc-Dusk@Focus Sash, Froslass+Lycanroc-Dusk, Lycanroc-Dusk+Froslass@Froslassite, Excadrill+Indeedee@Choice Scarf, Meowstic-F+Sneasler@Psychic Seed, Scovillain+Kingambit@Black Glasses, Sinistcha+Sneasler@Focus Sash, Maushold+Sneasler@Focus Sash, Blastoise+Sneasler@Focus Sash, Excadrill+Tyranitar@Choice Scarf, Tyranitar+Indeedee@Choice Scarf, Excadrill+Corviknight@Leftovers, Tyranitar+Corviknight@Leftovers, Indeedee+Excadrill@Focus Sash, Excadrill+Indeedee, Kommo-o+Indeedee@Focus Sash, Indeedee+Tyranitar@Tyranitarite, Gardevoir+Rotom-Heat@Sitrus Berry, Indeedee+Tyranitar, Indeedee+Excadrill@Life Orb, Gardevoir+Sneasler@Psychic Seed, Froslass+Scovillain@Scovillainite, Excadrill+Tyranitar@Chople Berry, Froslass+Scovillain, Scovillain+Froslass@Froslassite, Excadrill+Rillaboom@Expert Belt, Corviknight+Sneasler@White Herb, Dragonite+Indeedee@Focus Sash, Indeedee+Sneasler@Psychic Seed, Rotom-Heat+Tyranitar@Tyranitarite, Indeedee+Typhlosion-Hisui, Tyranitar+Rillaboom@Expert Belt, Glimmora+Kingambit@Occa Berry, Rotom-Heat+Tyranitar, Tyranitar+Garchomp@Garchompite, Indeedee+Typhlosion-Hisui@Choice Scarf, Indeedee+Corviknight@Leftovers, Rotom-Heat+Indeedee-F@Colbur Berry, Froslass+Kingambit@Life Orb, Delphox+Kingambit@Life Orb, Rotom-Heat+Excadrill@Focus Sash, Lycanroc-Dusk+Basculegion@Life Orb, Excadrill+Milotic@Sitrus Berry, Metagross+Indeedee@Choice Scarf, Whimsicott+Kingambit@Occa Berry, Kingambit+Hippowdon@Leftovers, Glimmora+Indeedee@Focus Sash, Excadrill+Rotom-Heat, Scovillain+Basculegion@Life Orb, Floette-Eternal+Sneasler@Focus Sash, Froslass+Rillaboom@Sitrus Berry, Tyranitar+Milotic@Sitrus Berry, Volcarona+Kingambit@Occa Berry, Excadrill+Sinistcha@Sitrus Berry, Indeedee+Metagross@Metagrossite, Milotic+Excadrill@Life Orb, Charizard+Kingambit@Focus Sash, Farigiraf+Kingambit@Focus Sash, Sylveon+Kingambit@Focus Sash, Indeedee+Metagross, Indeedee-F+Sneasler@Psychic Seed, Gardevoir+Rotom-Heat, Rotom-Heat+Gardevoir@Gardevoirite, Scovillain+Indeedee-F@Colbur Berry, Blaziken+Kingambit@Black Glasses, Indeedee+Venusaur@Life Orb, Golisopod+Rotom-Heat@Sitrus Berry, Salamence+Klefki@Light Clay, Hippowdon+Kingambit, Armarouge+Sneasler@Psychic Seed, Garchomp+Kingambit@Focus Sash, Salamence+Corviknight@Psychic Seed, Froslass+Blaziken@Blazikenite, Salamence+Excadrill@Life Orb, Sinistcha+Kingambit@Life Orb, Tyranitar+Sinistcha@Sitrus Berry, Kingambit+Volcarona@Focus Sash, Kingambit+Sylveon@Life Orb, Froslass+Sneasler@White Herb, Salamence+Rillaboom@Expert Belt, Baxcalibur+Sneasler@Focus Sash, Torkoal+Kingambit@Focus Sash, Excadrill+Sneasler@White Herb, Basculegion+Kingambit@Occa Berry, Kingambit+Torkoal@Life Orb, Scovillain+Pelipper@Focus Sash, Salamence+Excadrill@Focus Sash, Kingambit+Aerodactyl@Aerodactylite, Indeedee+Sneasler@White Herb, Milotic+Excadrill@Focus Sash, Metagross+Sneasler@Psychic Seed, Salamence+Indeedee@Choice Scarf, Klefki+Salamence@Salamencite, Excadrill+Milotic, Excadrill+Salamence@Salamencite, Klefki+Salamence, Excadrill+Salamence, Kingambit+Incineroar@White Herb, Milotic+Tyranitar@Tyranitarite, Kingambit+Meowstic-F, Kingambit+Meowstic-F@Meowsticite, Typhlosion-Hisui+Sneasler@Psychic Seed, Tyranitar+Sneasler@White Herb, Indeedee+Kommo-o@Life Orb, Lycanroc-Dusk+Sneasler@White Herb, Charizard+Indeedee@Focus Sash, Rotom-Heat+Sneasler@Psychic Seed, Froslass+Kingambit, Kingambit+Froslass@Froslassite, Salamence+Ceruledge@Colbur Berry, Kingambit+Blaziken@Blazikenite, Indeedee-F+Rotom-Heat@Sitrus Berry, Salamence+Tyranitar@Tyranitarite, Hippowdon+Incineroar, Froslass+Kingambit@Black Glasses, Sneasler+Gholdengo@Focus Sash, Sneasler+Indeedee@Colbur Berry, Floette-Eternal+Sneasler@Grassy Seed, Kingambit+Sneasler@Life Orb, Milotic+Tyranitar, Kleavor+Kingambit@Chople Berry, Delphox+Kingambit@Black Glasses, Basculegion+Lycanroc-Dusk@Focus Sash, Blaziken+Kingambit@Focus Sash, Corviknight+Salamence@Salamencite, Corviknight+Salamence, Sneasler+Corviknight@Psychic Seed, Talonflame+Tyranitar, Kingambit+Volcarona@Rocky Helmet, Tyranitar+Salamence@Salamencite, Salamence+Gholdengo@Grassy Seed, Kingambit+Sneasler@Focus Sash, Salamence+Tyranitar, Salamence+Sylveon@Life Orb, Whimsicott+Indeedee@Focus Sash, Froslass+Politoed@Sitrus Berry, Froslass+Arcanine-Hisui@Focus Sash, Dragonite+Sneasler@White Herb, Arcanine-Hisui+Froslass, Arcanine-Hisui+Froslass@Froslassite, Kingambit+Garchomp@Life Orb, Garchomp+Corviknight@Leftovers, Excadrill+Milotic@Leftovers, Basculegion+Lycanroc-Dusk, Aerodactyl+Kingambit, Froslass+Pawmot@Focus Sash, Kingambit+Garchomp@Garchompite, Sneasler+Indeedee@Choice Scarf, Kingambit+Delphox@Delphoxite, Whimsicott+Kingambit@Chople Berry, Basculegion+Scovillain@Scovillainite, Scovillain+Sneasler@White Herb, Gardevoir+Kingambit@Occa Berry, Froslass+Rillaboom@Eject Button, Kingambit+Sinistcha@Colbur Berry, Salamence+Indeedee@Twisted Spoon, Delphox+Kingambit, Indeedee+Salamence@Salamencite, Indeedee+Salamence, Indeedee+Sneasler, Tyranitar+Milotic@Leftovers, Sneasler+Arcanine-Hisui@Life Orb, Basculegion+Scovillain, Absol+Sneasler@Psychic Seed, Floette-Eternal+Kingambit@Life Orb, Basculegion+Kingambit@Chople Berry, Blaziken+Froslass, Blaziken+Froslass@Froslassite, Kingambit+Basculegion@Life Orb, Blastoise+Sneasler@Psychic Seed, Kingambit+Whimsicott@Fairy Feather, Camerupt+Kingambit, Kingambit+Camerupt@Cameruptite, Glimmora+Kingambit@Focus Sash, Indeedee+Milotic@Leftovers, Milotic+Indeedee@Choice Scarf, Corviknight+Sneasler, Annihilape+Sneasler@Psychic Seed, Froslass+Sneasler@Grassy Seed, Meowstic-F+Sneasler, Sneasler+Meowstic-F@Meowsticite, Sneasler+Indeedee@Focus Sash, Kingambit+Hatterene@Life Orb, Kingambit+Lycanroc-Dusk@Focus Sash, Altaria+Sneasler@Grassy Seed, Froslass+Volcarona@Grassy Seed, Incineroar+Sneasler@Focus Sash, Kingambit+Rillaboom@Sitrus Berry, Excadrill+Sinistcha@Colbur Berry, Froslass+Rillaboom@Life Orb, Indeedee+Milotic@Sitrus Berry, Blaziken+Kingambit, Kingambit+Whimsicott@Occa Berry, Kingambit+Rillaboom@Kebia Berry, Froslass+Kingambit@Chople Berry, Froslass+Pawmot, Pawmot+Froslass@Froslassite, Hatterene+Kingambit, Kingambit+Lycanroc-Dusk, Floette-Eternal+Kingambit@Occa Berry, Rotom-Heat+Golisopod@Golisopite, Golisopod+Rotom-Heat, Sneasler+Sinistcha@Occa Berry, Salamence+Rillaboom@Sitrus Berry, Sneasler+Excadrill@Life Orb, Rillaboom+Sneasler@Grassy Seed, Hippowdon+Rillaboom, Rillaboom+Hippowdon@Leftovers, Salamence+Sneasler@Grassy Seed, Sneasler+Rotom-Heat@Sitrus Berry, Tyranitar+Sneasler@Psychic Seed, Salamence+Kingambit@Chople Berry, Arcanine-Hisui+Kingambit@Life Orb, Sneasler+Kingambit@Life Orb, Kingambit+Ninetales-Alola@Choice Scarf, Arcanine-Hisui+Sneasler@Grassy Seed, Excadrill+Sneasler@Psychic Seed, Kingambit+Basculegion@Mystic Water, Kingambit+Indeedee-F@Sitrus Berry, Hippowdon+Rillaboom@Miracle Seed, Kingambit+Scovillain@Scovillainite, Kingambit+Torkoal, Indeedee-F+Kingambit@Occa Berry, Salamence+Rillaboom@Kebia Berry, Primarina+Kingambit@Focus Sash, Volcarona+Kingambit@Focus Sash, Sneasler+Volcarona@Focus Sash, Kingambit+Zoroark-Hisui, Kingambit+Torkoal@Charcoal, Froslass+Sneasler, Sneasler+Froslass@Froslassite, Baxcalibur+Sneasler@White Herb, Kingambit+Garchomp@Choice Scarf, Indeedee+Kommo-o, Venusaur+Sneasler@Psychic Seed, Tyranitar+Sinistcha@Colbur Berry, Sneasler+Delphox@Delphoxite, Kingambit+Scovillain, Sinistcha+Tyranitar@Tyranitarite, Indeedee-F+Scovillain, Farigiraf+Kingambit@Black Glasses, Indeedee+Milotic, Kingambit+Ceruledge@Colbur Berry, Kingambit+Ninetales-Alola@Never-Melt Ice, Sneasler+Indeedee-F@Colbur Berry, Kingambit+Garchomp@Sitrus Berry, Sneasler+Dragonite@Dragoninite, Delphox+Sneasler, Metagross+Indeedee@Focus Sash, Sinistcha+Tyranitar, Talonflame+Tyranitar@Tyranitarite, Basculegion+Indeedee@Focus Sash, Torkoal+Kingambit@Black Glasses, Kingambit+Meganium, Kingambit+Meganium@Meganiumite, Sneasler+Maushold@Chople Berry, Sneasler+Sinistcha@Coba Berry, Indeedee-F+Kingambit@Black Glasses, Salamence+Tyranitar@Choice Scarf, Indeedee-F+Rotom-Heat, Blastoise+Kingambit@Focus Sash, Sneasler+Garchomp@Garchompite, Salamence+Sneasler@White Herb, Ninetales-Alola+Sneasler@Focus Sash, Floette-Eternal+Sneasler, Sneasler+Floette-Eternal@Floettite, Lucario+Sneasler@Grassy Seed, Indeedee+Dragonite@Dragoninite, Basculegion+Kingambit@Black Glasses, Gholdengo+Sneasler@Grassy Seed, Gardevoir+Sneasler, Sneasler+Gardevoir@Gardevoirite, Raichu+Kingambit@Life Orb, Sneasler+Indeedee@Twisted Spoon, Froslass+Volcarona, Volcarona+Froslass@Froslassite, Sneasler+Rillaboom@Kebia Berry, Mamoswine+Salamence@Salamencite, Mamoswine+Salamence, Salamence+Mamoswine@Focus Sash, Indeedee-F+Scovillain@Scovillainite, Sneasler+Blastoise@Blastoisinite, Indeedee+Glimmora@Glimmoranite, Dragonite+Sneasler, Salamence+Milotic@Sitrus Berry, Torkoal+Kingambit@Chople Berry, Glimmora+Kingambit, Baxcalibur+Froslass, Baxcalibur+Froslass@Froslassite, Kingambit+Glimmora@Glimmoranite, Salamence+Arcanine-Hisui@Focus Sash, Kingambit+Glimmora@Focus Sash, Kingambit+Farigiraf@Sitrus Berry, Arcanine-Hisui+Salamence@Salamencite, Arcanine-Hisui+Salamence, Sneasler+Indeedee-F@Sitrus Berry, Kingambit+Sneasler@Grassy Seed, Volcarona+Sneasler@White Herb, Pelipper+Scovillain@Scovillainite, Corviknight+Delphox, Garchomp+Kingambit@Occa Berry, Sneasler+Rillaboom@Sitrus Berry, Blastoise+Sneasler, Sneasler+Salamence@Salamencite, Garchomp+Rotom-Heat, Salamence+Sneasler, Sneasler+Tyranitar@Tyranitarite, Salamence+Primarina@Grassy Seed, Salamence+Milotic@Leftovers, Incineroar+Sneasler@Grassy Seed, Pelipper+Scovillain, Indeedee+Kommo-o@Leftovers, Glimmora+Kingambit@Life Orb, Hydreigon+Sneasler@Psychic Seed, Armarouge+Kingambit@Focus Sash, Kingambit+Indeedee-F@Psychic Seed, Sinistcha+Excadrill@Focus Sash, Froslass+Kommo-o@Leftovers, Blaziken+Kingambit@Chople Berry, Kingambit+Kommo-o@Leftovers, Arcanine-Hisui+Kingambit@Chople Berry, Kingambit+Kleavor@Focus Sash, Raichu+Sneasler@Grassy Seed, Garchomp+Sneasler@Focus Sash, Excadrill+Sinistcha, Salamence+Basculegion@Life Orb, Froslass+Rillaboom, Rillaboom+Froslass@Froslassite, Corviknight+Indeedee-F@Colbur Berry, Gardevoir+Kingambit@Chople Berry, Pelipper+Sneasler@Psychic Seed, Kingambit+Floette-Eternal@Floettite, Gholdengo+Salamence@Salamencite, Sneasler+Excadrill@Focus Sash, Dragonite+Froslass, Dragonite+Froslass@Froslassite, Gholdengo+Salamence, Floette-Eternal+Kingambit, Dragonite+Indeedee, Sneasler+Kingambit@Chople Berry, Incineroar+Kingambit@Black Glasses, Salamence+Primarina@Leftovers, Sneasler+Tyranitar, Farigiraf+Kingambit, Kingambit+Aerodactyl@Focus Sash, Excadrill+Sneasler, Garchomp+Kingambit, Froslass+Basculegion@Life Orb, Kingambit+Whimsicott, Sinistcha+Kingambit@Black Glasses, Floette-Eternal+Kingambit@Black Glasses, Dragonite+Sneasler@Psychic Seed, Basculegion+Kingambit, Torkoal+Sneasler@Psychic Seed, Sneasler+Volcarona@Leftovers, Dragonite+Sneasler@Focus Sash, Pelipper+Indeedee@Focus Sash, Froslass+Raichu@Raichunite Y, Hydreigon+Indeedee, Salamence+Gholdengo@Life Orb, Sneasler+Incineroar@Rocky Helmet, Dragonite+Kingambit@Chople Berry, Froslass+Dragonite@Dragoninite, Froslass+Raichu, Raichu+Froslass@Froslassite, Froslass+Rillaboom@Occa Berry, Charizard+Kingambit@Occa Berry, Kingambit+Sneasler, Kingambit+Basculegion@Focus Sash, Glimmora+Kingambit@Chople Berry, Basculegion+Rotom-Heat, Salamence+Primarina@Life Orb, Sneasler+Corviknight@Leftovers, Sneasler+Charizard@Charizardite X, Sneasler+Incineroar@Sitrus Berry, Sneasler+Baxcalibur@Baxcalibrite, Basculegion+Sneasler@Psychic Seed, Gholdengo+Tyranitar@Tyranitarite, Salamence+Volcarona@Sitrus Berry, Excadrill+Gholdengo@Life Orb, Tyranitar+Gholdengo@Life Orb, Salamence+Incineroar@Rocky Helmet, Rillaboom+Kingambit@Life Orb, Milotic+Salamence@Salamencite, Salamence+Gholdengo@Leftovers, Milotic+Salamence, Kommo-o+Kingambit@Chople Berry, Metagross+Sneasler@White Herb, Gholdengo+Excadrill@Focus Sash, Primarina+Salamence@Salamencite, Primarina+Salamence, Kingambit+Armarouge@Life Orb, Kingambit+Sinistcha, Delphox+Kingambit@Chople Berry, Salamence+Rillaboom@Miracle Seed, Salamence+Rillaboom@Leftovers, Salamence+Sneasler@Psychic Seed, Metagross+Sneasler, Sneasler+Sinistcha@Colbur Berry, Garchomp+Scovillain@Scovillainite, Sneasler+Metagross@Metagrossite, Tyranitar+Armarouge@Life Orb, Rillaboom+Salamence@Salamencite, Rillaboom+Salamence, Corviknight+Delphox@Delphoxite, Sneasler+Sylveon@Life Orb, Glimmora+Indeedee, Sneasler+Armarouge@Life Orb, Gengar+Kingambit@Black Glasses, Excadrill+Armarouge@Life Orb, Sneasler+Garchomp@Garchompite Z, Ninetales-Alola+Kingambit@Chople Berry, Sneasler+Basculegion@Life Orb, Froslass+Incineroar@Passho Berry, Salamence+Kingambit@Occa Berry, Kingambit+Whimsicott@Focus Sash, Kingambit+Primarina@Life Orb, Mamoswine+Rillaboom, Rillaboom+Mamoswine@Focus Sash
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Sneasler | 79.6% | seed-unburden 56.9%, fake-out 44.1%, speed-drop 15.4%, ally-boost 11.8%, quick-guard 5.8%, priority-blocker 5.8%, setup 3.8%, disruption 0.4%, pivot 0.2%, weather-setter 0.1% |
| Salamence | 72.2% | intimidate 99.9%, mega-attacker 98.9%, tailwind 72.9%, setup 0.6%, weather-setter 0.2%, helping-hand 0.1% |
| Tyranitar | 42.1% | weather-setter 99.7%, mega-attacker 88.0%, setup 10.4%, trick-room-abuser 1.2%, speed-drop 0.6%, disruption 0.3% |
| Kingambit | 38.6% | priority-attack 99.3%, setup 26.2%, trick-room-abuser 16.9%, speed-drop 0.5% |
| Excadrill | 37.7% | setup 3.8%, speed-drop 2.5%, mega-attacker 1.5% |
| Indeedee | 29.3% | terrain-setter 100.0%, priority-blocker 100.0%, spa-drop 65.7%, disruption 27.0%, trick-room-setter 26.9%, helping-hand 10.0%, fake-out 2.3%, setup 0.4% |
| Corviknight | 13.3% | setup 90.7%, tailwind 23.5%, disruption 1.8% |
| Froslass | 12.1% | weather-setter 100.0%, mega-attacker 95.0%, screens 91.0%, speed-drop 2.6%, setup 1.9%, disruption 0.9%, status 0.7% |
| Rotom-Heat | 1.9% | pivot 53.9%, speed-drop 47.9%, status 35.7%, screens 6.1%, setup 4.3% |
| Scovillain | 1.8% | rage-powder 100.0%, mega-attacker 83.1%, trick-room-abuser 12.3%, helping-hand 5.1%, priority-attack 3.6% |
| Lycanroc-Dusk | 1.5% | priority-attack 100.0% |
| Chandelure | 0.7% | trick-room-setter 45.4%, mega-attacker 41.3%, spa-drop 9.4%, disruption 5.7% |
| Mamoswine | 0.5% | priority-attack 100.0% |
| Zoroark-Hisui | 0.4% | speed-drop 70.7%, disruption 28.4%, pivot 13.3%, spa-drop 9.4% |
| Hippowdon | 0.1% | weather-setter 100.0%, status 100.0%, trick-room-abuser 29.7% |
- Representative teams (primary teams with the highest score for this community):
  - [Aristo Wibowanto, 11th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0026/teamlist)
  - [Bangyu Tao, 54th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0269/teamlist)
  - [Sam Pandelis, 69th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0025/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Shohei Kimura, , 16 Sep 2026](https://pokepast.es/fd6fe5bb94a024fb)
  - [Liam Heppard, 868th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0847/teamlist)
  - [Liam Shorter, 47th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0237/teamlist)
  - [Dillon Kleinvehn, 113th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0098/teamlist)
  - [Norah Bowman, 251st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0287/teamlist)
  - [Gerald Mickles, 283rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0660/teamlist)
  - [misterteshima, 75th, 21 Sep 2026](https://pokepast.es/a8154c05ecfa0e20)
  - [Noah Neel, 75th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1078/teamlist)
  - [sinistchacha, 69th, 22 Sep 2026](https://pokepast.es/097433efcc505367)
  - [Jakob Graff, 69th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0989/teamlist)
  - [jade hyde, 49th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0006/teamlist)
  - [Sidi I. Haidala, Champion, 12 Sep 2026](https://pokepast.es/a634bf11f53735ba)
  - [Younas Abachri, 926th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0068/teamlist)
  - [mimictavern, , 11 Sep 2026](https://pokepast.es/4ebc34b995c1cba6)
  - [Matthias Meier, 470th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0711/teamlist)
  - [Lorenzo Dal Prato, 206th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0528/teamlist)
  - [Jose Rider Lorenzo, 228th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0099/teamlist)
  - [Ricardo Fuentes, 589th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0811/teamlist)
  - [Jasmine Brailey, 954th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0143/teamlist)
  - [Koen van Cann, 9th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0655/teamlist)
  - [Mark Mullender, 24th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0404/teamlist)
  - [Mateus De Moura Pimentel, 243rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0113/teamlist)
  - [Oguz Salbacak, 259th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0947/teamlist)
  - [Akram Hamdi, 684th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0852/teamlist)
  - [Lennard Messerschmidt, 914th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0612/teamlist)
  - [Zachary Weed, 195th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0685/teamlist)
  - [Marcus Dion, 237th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0158/teamlist)
  - [Carson Confer, 1045th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0864/teamlist)
  - [Matthias Hilger, 426th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0924/teamlist)
  - [Tommy Blaurock, 869th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0095/teamlist)
  - [Patrick Blainey, 100th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0177/teamlist)
  - [Manuel Jesús Azogil, 226th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0869/teamlist)
  - [Ryan Jackson, 352nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0518/teamlist)
  - [Philipp Genuit, 466th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0925/teamlist)
  - [Stephen Taylor, 762nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0429/teamlist)
  - [Blake Silver, 204th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0318/teamlist)
  - [Hyeonseung Lee, 975th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0872/teamlist)
  - [Yan Yuen, 669th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0967/teamlist)
  - [Scott Sellers, 131st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0063/teamlist)
  - [Berkin Dag, 483rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0169/teamlist)
  - [Francisco Garví, 653rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0329/teamlist)
  - [Nico Corsten, 685th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1039/teamlist)
  - [Christian Polo, 884th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1008/teamlist)
  - [Tom Sommer, 972nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0599/teamlist)
  - [Benjamin Rimmer, 983rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0327/teamlist)
  - [Ben Foust, 143rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0862/teamlist)
  - [Alex Boswell, 289th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0438/teamlist)
  - [Jason Kim, 408th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0796/teamlist)
  - [Hershal Rami, 818th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0849/teamlist)
  - [Alfredo Bell, 890th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0007/teamlist)
  - [Calvin Nisson, 969th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0473/teamlist)
  - [Joey Sybert, 1008th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0499/teamlist)
  - [punihina1334, , 10 Sep 2026](https://pokepast.es/202c514602d9abe9)
  - [Andrew Navarro, 115th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0260/teamlist)
  - [CloverBells, , 13 Sep 2026](https://pokepast.es/fdc0b961c0d0ef8c)
  - [Louis Markl, 19th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0737/teamlist)
  - [Cayden Aitchison, 156th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0314/teamlist)
  - [Alessio Ferrara, 112th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1034/teamlist)
  - [Tim Eggert, 561st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0948/teamlist)
  - [Robert Haas, 970th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0767/teamlist)
  - [Joseph Eckhart, 100th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0904/teamlist)
  - [Nick Donato, 434th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0563/teamlist)
  - [Nathan Couto, 533rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0019/teamlist)
  - [Nate Curl, 745th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0608/teamlist)
  - [Alec Pineda, 1050th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0248/teamlist)
  - [shynessalex, Champion, 20 Sep 2026](https://pokepast.es/6f1d5b2b15285f2b)
  - [Wyatt McDonald, Top 4, 16 Sep 2026](https://pokepast.es/37083bbe99a1bf42)
  - [Angelina Huynh, 249th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0554/teamlist)
  - [Ria Shanmugam, 354th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1072/teamlist)
  - [Violet Mendez, 873rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0734/teamlist)
  - [Q, , 10 Sep 2026](https://pokepast.es/4ed17394f6517aaf)
  - [Ethan Tyssen, 726th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0402/teamlist)
  - [Marcus Fussell, 955th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0838/teamlist)
  - [Joshua Ketz, 1051st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0761/teamlist)
  - [Patrick Kwant, 213th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0496/teamlist)
  - [Jack Mackinnon, 249th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0088/teamlist)
  - [Deen Roussety, 224th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0308/teamlist)
  - [Florian Temme, 27th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0751/teamlist)
  - [Aiden Jamieson, 36th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0243/teamlist)
  - [Danielle Mansutti, 143rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0252/teamlist)
  - [Jarod Nguyen, 241st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0150/teamlist)
  - [Isabella Verduci, 302nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0093/teamlist)
  - [Elias Truyens, 38th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0471/teamlist)
  - [Simon Van der Borght, 97th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0757/teamlist)
  - [Kusha Kanani, 221st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0146/teamlist)
  - [Dominik Thielemann, 359th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0307/teamlist)
  - [Dominik Mellentin, 445th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0521/teamlist)
  - [Stanislav Klaus, 515th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0784/teamlist)
  - [Moritz Voß, 643rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0646/teamlist)
  - [David Gidov, 693rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0604/teamlist)
  - [Aidan Debeuckelaere, 1068th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0331/teamlist)
  - [Dimitri Kontos, 1063rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0384/teamlist)
  - [Maurice Hofmann, 641st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0988/teamlist)
  - [Jeremy Sonpar, 462nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1055/teamlist)
  - [John Watson, 800th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0413/teamlist)
  - [Marco Abal Calvo, 1058th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0475/teamlist)
  - [Dylan Thommen, 315th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0802/teamlist)
  - [Shane Kennedy, 864th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0958/teamlist)
  - [Saad Sohail, 911th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0131/teamlist)
  - [Alexander Rassael, 520th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0078/teamlist)
  - [Jacob Zlotnitsky, 736th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0104/teamlist)
  - [Daniel Jensen, 811th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0440/teamlist)
  - [Alex Moreno, 44th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0605/teamlist)
  - [Xavier Vazquez Ripoll, 689th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0264/teamlist)
  - [Diego Santiago-Rodriguez, 466th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0797/teamlist)
  - [Pedro Feitosa, , 10 Sep 2026](https://pokepast.es/3020a0b5e5de04ae)
  - [Garret Gallo, 491st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0560/teamlist)
  - [Chris Zhao, 248th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0805/teamlist)
  - [Sean Chalungsooth, 1067th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0285/teamlist)
  - [Scott Kirby, 365th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0926/teamlist)
  - [Zachary Rowe, 215th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0204/teamlist)
  - [Thomas Schneider, 308th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0211/teamlist)
  - [Joseph Calderon, 771st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0092/teamlist)
  - [Mirco Koffler, 953rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0572/teamlist)
  - [Ryukichi Santiago, 428th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1025/teamlist)
  - [Sophie Scott, 455th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0593/teamlist)
  - [Rehan Ahmed, 753rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0037/teamlist)
  - [Gerry Thompson, 1037th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0496/teamlist)
  - [Ifeanyi Okafor, 1048th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0456/teamlist)
  - [Ian Reed, 321st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0799/teamlist)
  - [CloverBells, , 13 Sep 2026](https://pokepast.es/b6bace50909f3571)
  - [Tobias Düsel, 414th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1098/teamlist)
  - [Jack Sturmer, 311th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0253/teamlist)
  - [Omar ZIYANI, 681st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0426/teamlist)
  - [Lorenzo D'Ambrosio, 251st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0934/teamlist)
  - [Daniel Martinez Camacho, 499th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0067/teamlist)
  - [Ivan Martinez, 1054th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0099/teamlist)
  - [Nicolas Colella, 430th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0491/teamlist)
  - [David Markin, 960th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0693/teamlist)
  - [Fletcher Dean, 815th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1033/teamlist)
  - [Jonathan Martin, 30th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0320/teamlist)
  - [Cayden Owens, 31st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1059/teamlist)
  - [Jacob Youn, 85th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0612/teamlist)
  - [Jules Büchler-Lecler, 895th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0161/teamlist)
  - [Ethan Regnart, 297th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0056/teamlist)
  - [Yang Yuhao, 496th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0760/teamlist)
  - [Taylor Sprouse, 1014th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0392/teamlist)
  - [PhoNoodle, , 10 Sep 2026](https://pokepast.es/81d3f0ffe6ef750c)
  - [Chirantan Joshi, 570th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0286/teamlist)
  - [Christian Mossati, 24th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0234/teamlist)
  - [Jason Franks, 28th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0181/teamlist)
  - [Caleb Wijesinha, 29th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0307/teamlist)
  - [Layla Montgomerie, 30th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0124/teamlist)
  - [Matthew Robinson, 42nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0180/teamlist)
  - [Zhengyuan Jin, 125th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0317/teamlist)
  - [Kevin Liou, 174th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0066/teamlist)
  - [Louis Fontvieille, 110th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1031/teamlist)
  - [Dylan Bedrane, 215th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0087/teamlist)
  - [Vu Nguyen, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0432/teamlist)
  - [Jake Herbert, 478th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0455/teamlist)
  - [Justin Tang, Top 16, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/urXCEsyr6l208Bg4tX4o)
  - [Joshua Lorcy, 55th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0531/teamlist)
  - [Ben Wolf, 346th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1048/teamlist)
  - [Ates Serifsoy, 563rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0411/teamlist)
  - [Adam Warren, 1056th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0657/teamlist)
  - [Bo Quel, 1043rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0753/teamlist)
  - [Luca Santelli, 411th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0462/teamlist)
  - [Yannick Mach, 874th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0166/teamlist)
  - [Daniel Anselm, 537th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0090/teamlist)
  - [zerosiki0909, , 12 Sep 2026](https://pokepast.es/a719c9e52c55cc43)
  - [Lorenzo Pugliese, 624th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1058/teamlist)
  - [Pascal Weih, 1111th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0550/teamlist)
  - [tempo777, , 10 Sep 2026](https://pokepast.es/9fed7bfc061ad8bb)
  - [Seowon Kim, , 9 Sep 2026](https://pokepast.es/6f1b0e8df51cc57d)
  - [Zhiyuan Zhang, 587th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0985/teamlist)
  - [Uch, , 9 Sep 2026](https://pokepast.es/33b3042210ffc178)
  - [Roman Grcic, 239th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0186/teamlist)
  - [Brett Saguid, 708th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0667/teamlist)
  - [Aren Moy, 764th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0829/teamlist)
  - [Alex Greenawalt, 463rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0211/teamlist)
  - [Patou Makkinje, 1082nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0007/teamlist)
  - [Leo Kutschki, 904th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0536/teamlist)
  - [Alfredo Chang-Gonzalez, 10th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0118/teamlist)
  - [Nicholas Kan, 37th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0212/teamlist)
  - [Anna Aurelia, 776th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0677/teamlist)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/3ad6655b53208446)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/7fb12fe08230d7be)
  - [Andrew Jenkins, 264th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0464/teamlist)
  - [Patrick Heinicke, 490th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0442/teamlist)
  - [Matthew Henry, 5th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0085/teamlist)
  - [Cosmo, , 10 Sep 2026](https://pokepast.es/a8fc4cfab65cd411)
  - [Saelyn Turner, 985th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0421/teamlist)
  - [YUWUNA, , 10 Sep 2026](https://pokepast.es/595b9623f782bb8f)
  - [Matthew Kendall, 988th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0659/teamlist)
  - [Alexander Balic, 1112th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0379/teamlist)
  - [Gustavo, , 10 Sep 2026](https://pokepast.es/e2bab80c4e53d89a)
  - [Ben Grissmer, 197th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0445/teamlist)
  - [Matt Bruno, 426th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0288/teamlist)
  - [Shoma Honami, , 11 Sep 2026](https://pokepast.es/199e4fdf392315b2)
  - [Bas Bauer, 748th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0127/teamlist)
  - [Erik Holmstrom, 340th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0156/teamlist)
  - [Jonathan Tran, 565th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0535/teamlist)
  - [Ping, , 10 Sep 2026](https://pokepast.es/bed443f8a0acbe11)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/016cd16a7d929d4f)
  - [Oliver Marek, 546th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0990/teamlist)
  - [Sarina Compagnino, 474th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0866/teamlist)
  - [Joe Carlino, , 10 Sep 2026](https://pokepast.es/1e6a03dc1764214e)
  - [koyuki, , 9 Sep 2026](https://pokepast.es/8f4c2600a4a63a90)
  - [Alejandro Rodríguez Revidiego, 223rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0017/teamlist)
  - [Luis Medina, 388th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0040/teamlist)
  - [Jonas Birarda, 307th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0794/teamlist)
  - [Matthew Neeson, 775th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0024/teamlist)
  - [Bruce Bermel, 814th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0132/teamlist)
  - [Adam Noschese, 454th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0243/teamlist)
  - [Caleb Floyd, 867th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0935/teamlist)
  - [Cole Basham, Top 8, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/rD6PrJBrCfzicynLnRAI)
  - [Sam Badenach, 149th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0011/teamlist)
  - [Alexander Moor, 285th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0581/teamlist)
  - [Richard Wan, 433rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0879/teamlist)
  - [Ignacio Marquez Albes, 81st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0032/teamlist)
  - [Sam Tabner, 261st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0294/teamlist)
  - [Hsuan-Chih Kuo, 424th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0826/teamlist)
  - [Tobias Gleixner, 864th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0057/teamlist)
  - [Adnan Mohammed, 861st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0941/teamlist)
  - [Thomas Cooleen, 33rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0652/teamlist)
  - [Pablo Carro, 548th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0385/teamlist)
  - [Aitor Otegi, 870th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0428/teamlist)
  - [vi0ra_pokemon, , 14 Sep 2026](https://pokepast.es/74aea655c757917f)
  - [Christopher Epps, 732nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0150/teamlist)
  - [Stefano Greppi, , 15 Sep 2026](https://pokepast.es/045cc1b2f27f9315)
  - [Michał Chyra, 187th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1010/teamlist)
  - [David Herms, 1077th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0038/teamlist)
  - [Stefan Brandt, 405th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0108/teamlist)
  - [Anton Meßner, 454th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1078/teamlist)
  - [Jeffrey Lehmann, 986th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0461/teamlist)
  - [Xingjian Mao, 377th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0487/teamlist)
  - [Ben Kirch, 331st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0679/teamlist)
  - [Eclair_EqualAir, Top 4, 12 Sep 2026](https://pokepast.es/76a971cc5c997172)
  - [Kiernan Maloney, 851st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0331/teamlist)
  - [John Polzin, 1071st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0093/teamlist)
  - [Edward Chan, 704th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0221/teamlist)
  - [Ben Alexander, 265th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0027/teamlist)
  - [Jean-Ulysses Serrano Albuerne, 807th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0705/teamlist)
  - [Chris Santalis, 101st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0613/teamlist)
  - [Ethan Lam, 345th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0988/teamlist)
  - [Marius Wels, 274th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0569/teamlist)
  - [Alexander Baumann, 668th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0230/teamlist)
  - [Jonas Sørensen, 436th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0614/teamlist)
  - [Maia Merriman, 622nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0575/teamlist)
  - [Chu Jian Hua, 690th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0803/teamlist)
  - [Albert Tagliaferri, 716th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0874/teamlist)
  - [Jake Tagliaferri, 763rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0983/teamlist)
  - [Nikolai Herrmann, 629th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0854/teamlist)
  - [Glenn Rackley, 848th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0137/teamlist)
  - [Philip Nguyen, 209th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0135/teamlist)
  - [Lukas Egging, 794th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0376/teamlist)
  - [Thomas Karg, 815th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0006/teamlist)
  - [Tayveon Barrett, 287th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0086/teamlist)
  - [Brian Heck, 458th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0813/teamlist)
  - [Dylan Morris, 517th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0468/teamlist)
  - [Scotty Lee, 940th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0581/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/e6497a1a671dac90)
  - [Shawn Holahan, 932nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1061/teamlist)
  - [Justin Tang, , 11 Sep 2026](https://pokepast.es/d0ee4dbd575e2250)
  - [Simon Carmichael, 177th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0020/teamlist)
  - [Andres Wilkerson, 13th, 21 Sep 2026](https://pokepast.es/e97cb94d7e8d7678)
  - [Andres Wilkerson, Top 16, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/bASM2pmAEMjJYJ4ja7yI)
  - [Florian Hoffmann, 174th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0702/teamlist)
  - [Jannek Brödling, 375th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0542/teamlist)
  - [Paschalis Dermentzis, 20th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0846/teamlist)
  - [Lazaros Lazaropoulos, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0813/teamlist)
  - [Charalampos Frimas, 185th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0069/teamlist)
  - [Raphael Stallhofer, 244th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0688/teamlist)
  - [Malcolm Nolasco, 1053rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0647/teamlist)
  - [Mantrel Whitaker, 239th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0932/teamlist)
  - [Patrick Gabbett, 662nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0740/teamlist)
  - [Dylan Morgan, 711th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0369/teamlist)
  - [Julian Hernandez-Perocier, 418th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0978/teamlist)
  - [Jason Stewart, 619th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0732/teamlist)
  - [Nicholas Johnson, , 12 Sep 2026](https://pokepast.es/8bc91085c2883385)
  - [Alex Hikmat, 759th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0183/teamlist)
  - [Eirik Ødegård, 530th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0932/teamlist)
  - [Mark Vestbo Olsen, 1093rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0052/teamlist)
  - [Nathan Soo, 286th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0003/teamlist)
  - [Joan Garcia, 472nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1070/teamlist)
  - [Corey Okonowitz, 98th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0354/teamlist)
  - [Christopher Edwards, 718th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1016/teamlist)
  - [Eric Dolan, 769th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0559/teamlist)
  - [Kathryn Aplin, 646th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1079/teamlist)
  - [Matthew Herndon, 999th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0094/teamlist)
  - [David Ibeneme, 978th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0468/teamlist)
  - [Andrew Krebs, Top 4, 13 Sep 2026](https://pokepast.es/a4768a9bbf6876de)
  - [Dane Bodamer, 863rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0475/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/81427a109e744097)
  - [Jude Gerard Lee Wei Cong, 3rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0040/teamlist)
  - [Stanisław Piotrowski, 198th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0850/teamlist)
  - [Santino Tarquinio, 173rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0204/teamlist)
  - [Thaddeus Valentine, 258th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0727/teamlist)
  - [tchagelado, , 13 Sep 2026](https://pokepast.es/369e75b64155b6a1)
  - [Thomas Duggan, 306th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0138/teamlist)
  - [horsea_tatsuomi, , 12 Sep 2026](https://pokepast.es/e328f6becb7a8d35)
  - [Will Inabinet, 189th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1075/teamlist)
  - [Zoe Anderson, 1068th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0194/teamlist)
  - [Motochika Nabeshima, , 9 Sep 2026](https://pokepast.es/5268ba8166d67ebf)
  - [Amy White, 729th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0653/teamlist)
  - [Juan Francisco Alcaraz, 106th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0870/teamlist)
  - [Roi Gómez García, 1037th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0268/teamlist)
  - [Jon Huntley, 650th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0087/teamlist)
  - [Pasty, , 10 Sep 2026](https://pokepast.es/9c4f7915b1d3ae07)
  - [Eduardo Araújo, 1049th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0189/teamlist)
  - [Grant Rohlfing, 760th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0195/teamlist)
  - [sectoniaservant, , 11 Sep 2026](https://pokepast.es/c8f60c5168bd6a83)
  - [Brian Compere, 367th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0663/teamlist)
  - [Ian Kormos, 36th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0783/teamlist)
  - [Shohei Kimura, , 12 Sep 2026](https://pokepast.es/8ef29e6c905c1699)
  - [Daniel Soler Sanchez, 21st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0546/teamlist)
  - [Dani Pardo, 834th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0493/teamlist)
  - [Ling, , 10 Sep 2026](https://pokepast.es/900d357c0ea74c31)
  - [Mikal Mahoney, 117th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0703/teamlist)
  - [Justin Miranda-Radbord, 324th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1001/teamlist)
  - [Daniel Kap, 557th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0743/teamlist)
  - [Dennis Jäkel, 628th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0357/teamlist)
  - [Simon Hobrecht, 845th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0213/teamlist)
  - [Xavier Perin, 868th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0346/teamlist)
  - [Benjamin Hilger, 1073rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0998/teamlist)
  - [Chase Thompson, 116th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0640/teamlist)
  - [Adam Wright, , 17 Sep 2026](https://pokepast.es/f85133bac58b317f)
  - [Raphael Conceicao, 664th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0024/teamlist)
  - [Andrew Wilson, 29th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1030/teamlist)
  - [Frank Kovacs, 457th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0536/teamlist)
  - [Kamal Saab, 752nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1074/teamlist)
  - [KingYabber, , 10 Sep 2026](https://pokepast.es/6c092ddbec51ef61)
  - [Hiroto Kamazawa, , 21 Sep 2026](https://pokepast.es/652a6122d64aa2c1)
  - [Rudra Kansara, 1000th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0651/teamlist)
  - [Jonathan Todd, 730th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1008/teamlist)
  - [Aaron Penny, 274th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0046/teamlist)
  - [Jannik Baggett, 435th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0775/teamlist)
  - [Fabien Geeraert, 601st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0367/teamlist)
  - [Rees Griffith, 95th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0890/teamlist)
  - [Charles Erbring, 233rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0247/teamlist)
  - [Richard Mogollon, 296th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0018/teamlist)
  - [Ryan Weber, 540th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0101/teamlist)
  - [Kevin Miller, 674th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1070/teamlist)
  - [willbrown_vgc, 28th, 22 Sep 2026](https://pokepast.es/3ab405a83e618503)
  - [William Brown, 28th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0029/teamlist)
  - [Thomas Dervan, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0256/teamlist)
  - [Spencer Verdoni, 500th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0691/teamlist)
  - [DjSanders, , 13 Sep 2026](https://pokepast.es/ad11cad6ec6b68f7)
  - [Salvatore Maira, 597th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0626/teamlist)
  - [Aediion_, , 11 Sep 2026](https://pokepast.es/94cb88bc739ff2af)
  - [Maxwell Richards, 483rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0635/teamlist)
  - [Daniel Harris, 975th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0775/teamlist)
  - [Tyler Deacy, 127th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0188/teamlist)
  - [Kevin Holzmann, 807th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0067/teamlist)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/36cd120de5ec394c)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/134747480705edc0)
  - [Maximilian Seitz, 1061st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0421/teamlist)
  - [joserockzvgc, , 16 Sep 2026](https://pokepast.es/31945882c8d00260)
  - [Raphael But, 1088th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0168/teamlist)
  - [Tukibiya_Moeru, , 11 Sep 2026](https://pokepast.es/78f9ef4e502c61d7)
  - [Tim Beyreuther, 621st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0731/teamlist)
  - [etc25269248, , 13 Sep 2026](https://pokepast.es/7664efb6099c8d98)
  - [Benjamin Dean, 281st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0059/teamlist)
  - [Apollo Hageman, 275th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0360/teamlist)
  - [Daniel Boyer, 542nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0844/teamlist)
  - [Michael Kelsch, , 16 Sep 2026](https://pokepast.es/b3e468f45c3c9ebb)
  - [Dominic Plume, 652nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0349/teamlist)
  - [Rod, , 15 Sep 2026](https://pokepast.es/18d8e22d3d9ad026)
  - [Jermaine Mcleod, , 10 Sep 2026](https://pokepast.es/b54467e1aa4327e1)
  - [Michael Anastasio, 709th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0335/teamlist)
  - [Dorian Luckie, 802nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0459/teamlist)
  - [Lily Ellsasser, 280th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0502/teamlist)
  - [toshiniki, , 18 Sep 2026](https://pokepast.es/93b626e0c928be89)
  - [Magnus Wallgren, 638th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0185/teamlist)
  - [albin jepping, 817th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0211/teamlist)
  - [Matin Moradi, 66th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0084/teamlist)
  - [Xena, , 9 Sep 2026](https://pokepast.es/666b7365ce0b35f6)
  - [Alyssa Smith, 374th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0749/teamlist)
  - [Devlin Ursu, 456th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0987/teamlist)
  - [Germán Francisco Tenza Rubio, 270th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0641/teamlist)
  - [Astrid Gurski, 983rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0809/teamlist)
  - [Murphy Hartzenberg, 135th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0001/teamlist)
  - [Trista Medine, 404th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1026/teamlist)
  - [homura_kurenai_, , 10 Sep 2026](https://pokepast.es/7f30345697a16032)
  - [axolodyl, , 10 Sep 2026](https://pokepast.es/37cf6b6b304aefd6)
  - [Daniel Schäfer, 632nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0457/teamlist)
  - [Jack Presland, 123rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0078/teamlist)
  - [Víctor Medina, 29th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1013/teamlist)
  - [David Peralta Bozada, 145th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0080/teamlist)
  - [Carlos Cabal, 148th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0573/teamlist)
  - [Samuel Pereira, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0995/teamlist)
  - [hattington360, , 10 Sep 2026](https://pokepast.es/0f1752f2ba99c09f)
  - [Rani De Schoenmacker, 740th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0150/teamlist)
  - [Matteo Paviza, 877th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0022/teamlist)
  - [Tyler Norton, 546th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0458/teamlist)
  - [Gabe Baum, 53rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0364/teamlist)
  - [Yuma Kinugawa, , 12 Sep 2026](https://pokepast.es/348d665e808bb457)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)
  - [William Clements, 1071st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0123/teamlist)
  - [Tristan Brissette, 826th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0238/teamlist)
  - [FranDrawer03, , 11 Sep 2026](https://pokepast.es/961f0667b90b830c)
  - [Colin Cain, 847th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0205/teamlist)
  - [Di Smith, 1017th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0717/teamlist)
  - [Nicholas Woodhouse, 226th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0292/teamlist)
  - [Amethyst Leine, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0362/teamlist)
  - [clark smith, 1011th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0116/teamlist)
  - [Dom Mori, 757th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0006/teamlist)
  - [Thomas Schultz, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1054/teamlist)
  - [Daniel walker, 6th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0315/teamlist)
  - [Felix Althaus, 994th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0812/teamlist)
  - [Mark Cotter, 542nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0747/teamlist)
  - [mofumofunatsuhi, , 13 Sep 2026](https://pokepast.es/f85b026e5b0e6567)
  - [Sam Sperl, 117th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0873/teamlist)
  - [Jeremiah Paul, 212th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0587/teamlist)
  - [Heinz Heckmann, 154th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0375/teamlist)
  - [kayato_vgc, , 18 Sep 2026](https://pokepast.es/cea79bfa59e2fd6b)
  - [Giovanni Piscitelli, Top 8, 20 Sep 2026](https://pokepast.es/ffe1c04c186b2453)
  - [Luke Owen, 242nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0593/teamlist)
  - [Steven Van, 495th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0200/teamlist)
  - [Demitrios Kaguras, 220th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1006/teamlist)
  - [Basil Hawley, 228th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0551/teamlist)
  - [Morgan Carter, 900th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1021/teamlist)
  - [Damahni Palmer, 27th, 21 Sep 2026](https://pokepast.es/9e422cab36495fcb)
  - [Noah Sim, 1030th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0579/teamlist)
  - [Jobe McDermott, 251st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0155/teamlist)
  - [Jason Hookens, 138th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0173/teamlist)
  - [Elias Pacheco Coelho, 626th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0256/teamlist)
  - [Marcos Perez, 906th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0210/teamlist)
  - [Alejandro, , 12 Sep 2026](https://pokepast.es/12c7b27a8e53ed9c)
  - [Sebastian Abenza Homberger, 711th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0285/teamlist)
  - [Ramon Schong, 569th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0386/teamlist)
  - [Kevin Hagen, 678th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0351/teamlist)
  - [Hendrik Förster, 700th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0922/teamlist)
  - [Eliseo Torres-Morales, 1036th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0353/teamlist)
  - [Joshua Moloney, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0143/teamlist)
  - [Evan Schulz, 1013th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0394/teamlist)
  - [PiyoLily145, , 9 Sep 2026](https://pokepast.es/8e37c3b00cbba6b6)
  - [Antonio Galotta, 121st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0798/teamlist)
  - [Christopher Gibson, 21st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0275/teamlist)
  - [Michael Mullen, 666th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0282/teamlist)
  - [Joshua Flickinger, 838th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0567/teamlist)
  - [Niklas Hauser, 398th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0075/teamlist)
  - [Darcy Willis, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0254/teamlist)
  - [Seth Ellsworth, 586th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0058/teamlist)
  - [Benjamin Kurtzemann, 782nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0287/teamlist)
  - [Prncessdiana, , 20 Sep 2026](https://pokepast.es/1c95ff346ce5b161)
  - [Victor Bonfili, 929th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0979/teamlist)
  - [luixens, , 12 Sep 2026](https://pokepast.es/eb89982a260f186e)
  - [Joshua Hoitink, 92nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0053/teamlist)
  - [Patrick Verrelli, 108th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0102/teamlist)
  - [Robert Pamplin, 744th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0562/teamlist)
  - [Hijito Kihara, , 10 Sep 2026](https://pokepast.es/fb6e8f3c9d655db6)
  - [Heber Henriquez, 805th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0902/teamlist)
  - [Rens Heylen, 296th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0101/teamlist)
  - [Yuta Ishigaki, , 10 Sep 2026](https://pokepast.es/f5b17f03de49b848)
  - [Max Hofmann, 954th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1089/teamlist)
  - [Noah Gelman, 706th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0418/teamlist)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Daniel Medina, 574th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0223/teamlist)
  - [pathogenvgc, , 14 Sep 2026](https://pokepast.es/a630a7a5018325c9)
  - [Castorbrown, , 13 Sep 2026](https://pokepast.es/93c86d303e85d1da)
  - [Eliana Stevens, 326th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0222/teamlist)

- Sub-community pass: 587 primary teams, 137 tokens, modularity 0.45; unconnected tokens: Tyranitar (other item; Chople Berry 7/13), Hydreigon@Choice Scarf, Gengar@Gengarite, Absol@Absolite Z, Lucario@Lucarionite Z, Tsareena (other item; Wide Lens 2/5), Typhlosion-Hisui@Choice Scarf, Archaludon (other item; Leftovers 3/4), Baxcalibur (other item; Life Orb 2/4), Delphox (other item; Life Orb 3/4), Dragapult (other item; Life Orb 3/4), Maushold (other item; Chople Berry 1/4), Alakazam (other item; Alakazite 3/3), Annihilape (other item; Choice Scarf 2/3), Ceruledge (other item; Focus Sash 2/3), Chandelure (other item; Chandelurite 1/3), Kleavor (other item; Choice Scarf 2/3), Mamoswine (other item; Focus Sash 3/3), Meganium (other item; Meganiumite 3/3), Steelix (other item; Steelixite 3/3), Torterra (other item; Life Orb 3/3), Venusaur (other item; Life Orb 1/3), Zoroark-Hisui (other item; Choice Scarf 2/3); unassigned within the community: 6 teams (1.2% of its primary weight); hybrid teams of the community left out: 453
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 2 (0.63) | 3 (0.54) | 4 (0.45) | 6 (0.37) | 7 (0.30) |

#### Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) (244 primary teams, 118 distinct builds, top pair on 199/244)
- Megas on member teams: Tyranitar 214, Salamence 196, Staraptor 9, Golisopod 8, Garchomp-Z 6, Froslass 5
- Top species by team share: Tyranitar 91%, Excadrill 85%, Salamence 80%, Sneasler 63%, Milotic 46%, Indeedee 43%
- Token label: Tyranitar@Tyranitarite / Excadrill / Salamence@Salamencite
- Mode tags on primary teams: Sand 220, Tailwind 134, Psyspam 118, Setup 27, Trick Room 15, Snow 6, Screens 3, Sun 2
- Primary teams: 244 (43.0% of the community's primary weight), hybrid teams: 36 (6.6%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Sinistcha (other item; Colbur Berry 11/28)+Staraptor@Staraptite, Corviknight (other item; Leftovers 15/19)+Garchomp@Garchompite Z, Corviknight@Psychic Seed+Indeedee@Choice Scarf, Gholdengo (other item; Life Orb 101/109)+Gardevoir@Gardevoirite, Indeedee@Choice Scarf+Metagross@Metagrossite, Excadrill (other item; Focus Sash 197/213)+Corviknight@Psychic Seed, Sneasler (other item; White Herb 156/204)+Corviknight@Psychic Seed, Milotic (other item; Sitrus Berry 81/154)+Metagross@Metagrossite, Incineroar (other item; Sitrus Berry 64/77)+Sinistcha (other item; Colbur Berry 11/28), Corviknight@Psychic Seed+Tyranitar@Tyranitarite, Excadrill (other item; Focus Sash 197/213)+Tyranitar@Tyranitarite, Excadrill (other item; Focus Sash 197/213)+Staraptor@Staraptite, Indeedee@Choice Scarf+Sneasler@Psychic Seed, Sneasler (other item; White Herb 156/204)+Scovillain@Scovillainite, Gholdengo (other item; Life Orb 101/109)+Staraptor@Staraptite, Gholdengo (other item; Life Orb 101/109)+Tyranitar@Tyranitarite, Gholdengo (other item; Life Orb 101/109)+Milotic (other item; Sitrus Berry 81/154), Corviknight (other item; Leftovers 15/19)+Tyranitar@Tyranitarite, Sneasler (other item; White Herb 156/204)+Garchomp@Choice Scarf, Corviknight (other item; Leftovers 15/19)+Indeedee@Choice Scarf, Excadrill (other item; Focus Sash 197/213)+Tyranitar@Choice Scarf, Sinistcha (other item; Colbur Berry 11/28)+Tyranitar@Tyranitarite, Excadrill (other item; Focus Sash 197/213)+Gholdengo (other item; Life Orb 101/109), Talonflame (other item; Expert Belt 2/7)+Tyranitar@Tyranitarite, Indeedee@Choice Scarf+Tyranitar@Tyranitarite, Excadrill (other item; Focus Sash 197/213)+Indeedee@Choice Scarf, Lycanroc-Dusk (other item; Focus Sash 8/8)+Sneasler (other item; White Herb 156/204), Milotic (other item; Sitrus Berry 81/154)+Staraptor@Staraptite, Corviknight (other item; Leftovers 15/19)+Excadrill (other item; Focus Sash 197/213), Excadrill (other item; Focus Sash 197/213)+Indeedee-F@Psychic Seed, Excadrill (other item; Focus Sash 197/213)+Volcarona@Grassy Seed, Milotic (other item; Sitrus Berry 81/154)+Tyranitar@Tyranitarite, Indeedee-F@Psychic Seed+Tyranitar@Tyranitarite, Milotic (other item; Sitrus Berry 81/154)+Sinistcha (other item; Colbur Berry 11/28), Excadrill (other item; Focus Sash 197/213)+Milotic (other item; Sitrus Berry 81/154), Staraptor@Staraptite+Tyranitar@Tyranitarite, Sneasler (other item; White Herb 156/204)+Indeedee-F@Psychic Seed, Excadrill (other item; Focus Sash 197/213)+Rotom-Heat (other item; Sitrus Berry 4/10), Milotic (other item; Sitrus Berry 81/154)+Sneasler@Psychic Seed, Rotom-Heat (other item; Sitrus Berry 4/10)+Tyranitar@Tyranitarite, Gholdengo (other item; Life Orb 101/109)+Sinistcha (other item; Colbur Berry 11/28), Excadrill (other item; Focus Sash 197/213)+Sinistcha (other item; Colbur Berry 11/28), Gholdengo (other item; Life Orb 101/109)+Primarina (other item; Life Orb 7/15), Corviknight (other item; Leftovers 15/19)+Sneasler@Psychic Seed, Sneasler (other item; White Herb 156/204)+Golisopod@Golisopite, Sneasler (other item; White Herb 156/204)+Basculegion@Choice Scarf, Gholdengo (other item; Life Orb 101/109)+Indeedee-F (other item; Rocky Helmet 24/56), Arcanine-Hisui (other item; Focus Sash 96/99)+Milotic (other item; Sitrus Berry 81/154), Sneasler (other item; White Herb 156/204)+Froslass@Froslassite, Sneasler (other item; White Herb 156/204)+Raichu@Raichunite Y, Arcanine-Hisui (other item; Focus Sash 96/99)+Indeedee@Choice Scarf, Sneasler (other item; White Herb 156/204)+Volcarona (other item; Sitrus Berry 7/17), Tyranitar@Tyranitarite+Volcarona@Grassy Seed, Corviknight@Psychic Seed+Salamence@Salamencite, Milotic (other item; Sitrus Berry 81/154)+Indeedee@Choice Scarf, Farigiraf (other item; Sitrus Berry 19/28)+Sneasler (other item; White Herb 156/204), Sneasler (other item; White Herb 156/204)+Indeedee@Choice Scarf, Sneasler (other item; White Herb 156/204)+Garchomp@Garchompite Z, Sneasler (other item; White Herb 156/204)+Dragonite@Dragoninite, Salamence@Salamencite+Volcarona@Grassy Seed, Gholdengo (other item; Life Orb 101/109)+Sneasler@Psychic Seed, Gholdengo (other item; Life Orb 101/109)+Salamence@Salamencite, Basculegion (other item; Life Orb 72/97)+Sneasler (other item; White Herb 156/204), Indeedee@Choice Scarf+Salamence@Salamencite, Arcanine-Hisui (other item; Focus Sash 96/99)+Salamence@Salamencite, Salamence@Salamencite+Sneasler@Grassy Seed, Pelipper (other item; Focus Sash 5/7)+Salamence@Salamencite, Milotic (other item; Sitrus Berry 81/154)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 171/271)+Salamence@Salamencite, Excadrill (other item; Focus Sash 197/213)+Salamence@Salamencite, Floette-Eternal@Floettite+Salamence@Salamencite, Salamence@Salamencite+Tyranitar@Tyranitarite, Primarina (other item; Life Orb 7/15)+Salamence@Salamencite, Metagross@Metagrossite+Salamence@Salamencite, Sylveon (other item; Fairy Feather 24/28)+Salamence@Salamencite, Basculegion (other item; Life Orb 72/97)+Salamence@Salamencite, Armarouge (other item; Life Orb 13/23)+Salamence@Salamencite, Basculegion@Choice Scarf+Salamence@Salamencite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Tyranitar@Tyranitarite | 88.5% |
| Excadrill (other item; Focus Sash 197/213) | 85.8% |
| Salamence@Salamencite | 79.5% |
| Milotic (other item; Sitrus Berry 81/154) | 42.5% |
| Sneasler (other item; White Herb 156/204) | 42.5% |
| Indeedee@Choice Scarf | 41.3% |
| Gholdengo (other item; Life Orb 101/109) | 35.8% |
| Corviknight@Psychic Seed | 22.5% |
| Sinistcha (other item; Colbur Berry 11/28) | 8.9% |
| Corviknight (other item; Leftovers 15/19) | 6.2% |
| Staraptor@Staraptite | 4.4% |
| Primarina (other item; Life Orb 7/15) | 2.7% |
| Rotom-Heat (other item; Sitrus Berry 4/10) | 2.6% |
| Talonflame (other item; Expert Belt 2/7) | 1.8% |
| Tyranitar@Choice Scarf | 1.1% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [jessditta, , 10 Sep 2026](https://pokepast.es/5a778ac69b5c4f0d)
  - [Aristo Wibowanto, 11th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0026/teamlist)
  - [Bangyu Tao, 54th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0269/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Jérémy Côté, 15th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0082/teamlist)
  - [Blaik Thompson, Top 4, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/VW1m8TfHx0RIVs4aGHGl)
  - [Mathew Trapp, 38th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0189/teamlist)
  - [Dallas Bertram, 191st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0240/teamlist)
  - [Arnav Gupta, 367th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0865/teamlist)
  - [Kevin Laranjeira, 473rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0817/teamlist)
  - [Oskar Wachek, 487th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0661/teamlist)
  - [Antonios Karatolios, 536th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1086/teamlist)
  - [Pierre CULLIN, 728th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0261/teamlist)
  - [Xavarien Perkins, 764th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0520/teamlist)
  - [Nils Woyda, 814th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0888/teamlist)
  - [Jérémy Côté, 176th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0875/teamlist)
  - [Sean Kochhar, 227th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0255/teamlist)
  - [Stephen Morioka, 285th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0896/teamlist)
  - [Matthew Sharp, 431st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0803/teamlist)
  - [Kevin Herron, 599th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1053/teamlist)
  - [Jared Pridgeon, 694th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0428/teamlist)
  - [Andrew Lim, 701st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0837/teamlist)
  - [Alec James, 723rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0733/teamlist)
  - [Kenneth Huang, 822nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0886/teamlist)
  - [Alexander Solimene, 281st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0500/teamlist)
  - [Jeffrey Hartsook, 298th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0639/teamlist)
  - [Anthony Rodriguez, 833rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0013/teamlist)
  - [Wyatt McDonald, , 15 Sep 2026](https://pokepast.es/97edd96cf0de7f14)
  - [Amar Curic, 188th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0289/teamlist)
  - [Francesco Contiguglia, 762nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0342/teamlist)
  - [Trent Rose, 695th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0168/teamlist)
  - [Natchanon Teerarat, 178th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0096/teamlist)
  - [Niklas Margaritaru, 1078th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0621/teamlist)
  - [Jack Burnett, 395th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0490/teamlist)
  - [Daniel Pioppi, , 14 Sep 2026](https://pokepast.es/9cc929d200baed42)
  - [Jurre De Mare, 425th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0905/teamlist)
  - [Jetrick Gelacio, 111th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0782/teamlist)
  - [Darryl Brice, 498th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0126/teamlist)
  - [Ivan Radosevic, 535th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1091/teamlist)
  - [joshg0tti1, , 11 Sep 2026](https://pokepast.es/4c6eb1d0d2cbb3f3)

#### Community 1 / Sub-community 1: Kingambit / Rillaboom (239 primary teams, 149 distinct builds, top pair on 160/239)
- Megas on member teams: Salamence 173, Froslass 64, Floette 53, Delphox 20, Charizard-Y 17, Garchomp-Z 14
- Top species by team share: Sneasler 90%, Kingambit 87%, Rillaboom 79%, Salamence 72%, Basculegion 49%, Froslass 27%
- Token label: Kingambit / Rillaboom
- Mode tags on primary teams: Tailwind 165, Snow 69, Setup 29, Sun 21, Trick Room 10, Rain 5, Sand 4, Psyspam 3
- Primary teams: 239 (38.8% of the community's primary weight), hybrid teams: 15 (2.4%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Lycanroc-Dusk (other item; Focus Sash 8/8)+Scovillain@Scovillainite, Pelipper (other item; Focus Sash 5/7)+Basculegion@Choice Scarf, Lycanroc-Dusk (other item; Focus Sash 8/8)+Froslass@Froslassite, Froslass@Froslassite+Scovillain@Scovillainite, Basculegion@Choice Scarf+Golisopod@Golisopite, Basculegion (other item; Life Orb 72/97)+Lycanroc-Dusk (other item; Focus Sash 8/8), Basculegion (other item; Life Orb 72/97)+Scovillain@Scovillainite, Blaziken@Blazikenite+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 96/99)+Metagross@Metagrossite, Dragonite@Dragoninite+Froslass@Froslassite, Basculegion@Choice Scarf+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 64/77)+Floette-Eternal@Floettite, Basculegion (other item; Life Orb 72/97)+Dragonite@Dragoninite, Kingambit (other item; Chople Berry 135/240)+Meowstic-F (other item; Meowsticite 4/4), Volcarona (other item; Sitrus Berry 7/17)+Froslass@Froslassite, Basculegion@Choice Scarf+Floette-Eternal@Floettite, Garchomp (other item; Life Orb 9/13)+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 96/99)+Sneasler@Grassy Seed, Floette-Eternal@Floettite+Sneasler@Grassy Seed, Kingambit (other item; Chople Berry 135/240)+Blaziken@Blazikenite, Rillaboom (other item; Miracle Seed 171/271)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 171/271)+Volcarona@Grassy Seed, Whimsicott (other item; Focus Sash 15/21)+Floette-Eternal@Floettite, Basculegion (other item; Life Orb 72/97)+Kingambit (other item; Chople Berry 135/240), Kingambit (other item; Chople Berry 135/240)+Lycanroc-Dusk (other item; Focus Sash 8/8), Kingambit (other item; Chople Berry 135/240)+Garchomp@Choice Scarf, Kingambit (other item; Chople Berry 135/240)+Baxcalibur@Baxcalibrite, Glimmora (other item; Focus Sash 5/5)+Kingambit (other item; Chople Berry 135/240), Sneasler (other item; White Herb 156/204)+Scovillain@Scovillainite, Kingambit (other item; Chople Berry 135/240)+Froslass@Froslassite, Basculegion (other item; Life Orb 72/97)+Floette-Eternal@Floettite, Kingambit (other item; Chople Berry 135/240)+Scovillain@Scovillainite, Kingambit (other item; Chople Berry 135/240)+Pawmot (other item; Focus Sash 7/7), Basculegion (other item; Life Orb 72/97)+Delphox@Delphoxite, Arcanine-Hisui (other item; Focus Sash 96/99)+Froslass@Froslassite, Kingambit (other item; Chople Berry 135/240)+Delphox@Delphoxite, Basculegion (other item; Life Orb 72/97)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 171/271)+Baxcalibur@Baxcalibrite, Kingambit (other item; Chople Berry 135/240)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 171/271)+Volcarona (other item; Sitrus Berry 7/17), Basculegion@Choice Scarf+Froslass@Froslassite, Kingambit (other item; Chople Berry 135/240)+Vanilluxe (other item; Choice Scarf 4/5), Kingambit (other item; Chople Berry 135/240)+Raichu@Raichunite Y, Kingambit (other item; Chople Berry 135/240)+Torkoal (other item; Charcoal 6/6), Kingambit (other item; Chople Berry 135/240)+Blastoise@Blastoisinite, Lycanroc-Dusk (other item; Focus Sash 8/8)+Sneasler (other item; White Herb 156/204), Froslass@Froslassite+Sneasler@Grassy Seed, Kingambit (other item; Chople Berry 135/240)+Glimmora@Glimmoranite, Arcanine-Hisui (other item; Focus Sash 96/99)+Basculegion (other item; Life Orb 72/97), Kingambit (other item; Chople Berry 135/240)+Charizard@Charizardite Y, Rillaboom (other item; Miracle Seed 171/271)+Floette-Eternal@Floettite, Excadrill (other item; Focus Sash 197/213)+Volcarona@Grassy Seed, Basculegion (other item; Life Orb 72/97)+Froslass@Froslassite, Charizard@Charizardite Y+Sneasler@Grassy Seed, Delphox@Delphoxite+Sneasler@Grassy Seed, Kingambit (other item; Chople Berry 135/240)+Floette-Eternal@Floettite, Basculegion (other item; Life Orb 72/97)+Whimsicott (other item; Focus Sash 15/21), Garchomp (other item; Life Orb 9/13)+Kingambit (other item; Chople Berry 135/240), Farigiraf (other item; Sitrus Berry 19/28)+Kingambit (other item; Chople Berry 135/240), Kingambit (other item; Chople Berry 135/240)+Volcarona (other item; Sitrus Berry 7/17), Incineroar (other item; Sitrus Berry 64/77)+Sneasler@Grassy Seed, Basculegion (other item; Life Orb 72/97)+Sylveon (other item; Fairy Feather 24/28), Kingambit (other item; Chople Berry 135/240)+Dragonite@Dragoninite, Arcanine-Hisui (other item; Focus Sash 96/99)+Dragonite@Dragoninite, Basculegion (other item; Life Orb 72/97)+Rillaboom (other item; Miracle Seed 171/271), Rillaboom (other item; Miracle Seed 171/271)+Delphox@Delphoxite, Kingambit (other item; Chople Berry 135/240)+Rillaboom (other item; Miracle Seed 171/271), Arcanine-Hisui (other item; Focus Sash 96/99)+Sneasler@Psychic Seed, Rillaboom (other item; Miracle Seed 171/271)+Blaziken@Blazikenite, Basculegion (other item; Life Orb 72/97)+Volcarona (other item; Sitrus Berry 7/17), Garchomp (other item; Life Orb 9/13)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 171/271)+Basculegion@Choice Scarf, Kingambit (other item; Chople Berry 135/240)+Whimsicott (other item; Focus Sash 15/21), Arcanine-Hisui (other item; Focus Sash 96/99)+Kingambit (other item; Chople Berry 135/240), Kingambit (other item; Chople Berry 135/240)+Sylveon (other item; Fairy Feather 24/28), Kingambit (other item; Chople Berry 135/240)+Garchomp@Garchompite Z, Sneasler (other item; White Herb 156/204)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 171/271)+Glimmora@Glimmoranite, Arcanine-Hisui (other item; Focus Sash 96/99)+Milotic (other item; Sitrus Berry 81/154), Rillaboom (other item; Miracle Seed 171/271)+Froslass@Froslassite, Sneasler (other item; White Herb 156/204)+Froslass@Froslassite, Sneasler (other item; White Herb 156/204)+Raichu@Raichunite Y, Sylveon (other item; Fairy Feather 24/28)+Sneasler@Grassy Seed, Arcanine-Hisui (other item; Focus Sash 96/99)+Indeedee@Choice Scarf, Sneasler (other item; White Herb 156/204)+Volcarona (other item; Sitrus Berry 7/17), Tyranitar@Tyranitarite+Volcarona@Grassy Seed, Froslass@Froslassite+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 64/77)+Basculegion@Choice Scarf, Sneasler (other item; White Herb 156/204)+Dragonite@Dragoninite, Salamence@Salamencite+Volcarona@Grassy Seed, Rillaboom (other item; Miracle Seed 171/271)+Golisopod@Golisopite, Arcanine-Hisui (other item; Focus Sash 96/99)+Rillaboom (other item; Miracle Seed 171/271), Basculegion (other item; Life Orb 72/97)+Sneasler (other item; White Herb 156/204), Kingambit (other item; Chople Berry 135/240)+Basculegion@Choice Scarf, Dragonite@Dragoninite+Sneasler@Psychic Seed, Arcanine-Hisui (other item; Focus Sash 96/99)+Salamence@Salamencite, Salamence@Salamencite+Sneasler@Grassy Seed, Pelipper (other item; Focus Sash 5/7)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 171/271)+Salamence@Salamencite, Floette-Eternal@Floettite+Salamence@Salamencite, Basculegion (other item; Life Orb 72/97)+Salamence@Salamencite, Basculegion@Choice Scarf+Salamence@Salamencite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Kingambit (other item; Chople Berry 135/240) | 86.2% |
| Rillaboom (other item; Miracle Seed 171/271) | 78.8% |
| Sneasler@Grassy Seed | 46.9% |
| Basculegion (other item; Life Orb 72/97) | 36.8% |
| Froslass@Froslassite | 27.5% |
| Arcanine-Hisui (other item; Focus Sash 96/99) | 26.8% |
| Floette-Eternal@Floettite | 19.6% |
| Basculegion@Choice Scarf | 10.1% |
| Delphox@Delphoxite | 9.8% |
| Volcarona (other item; Sitrus Berry 7/17) | 5.7% |
| Whimsicott (other item; Focus Sash 15/21) | 4.8% |
| Dragonite@Dragoninite | 4.4% |
| Lycanroc-Dusk (other item; Focus Sash 8/8) | 3.8% |
| Scovillain@Scovillainite | 3.7% |
| Blaziken@Blazikenite | 3.7% |
| Raichu@Raichunite Y | 3.5% |
| Glimmora@Glimmoranite | 3.0% |
| Pawmot (other item; Focus Sash 7/7) | 2.3% |
| Pelipper (other item; Focus Sash 5/7) | 2.2% |
| Volcarona@Grassy Seed | 2.1% |
| Glimmora (other item; Focus Sash 5/5) | 1.9% |
| Baxcalibur@Baxcalibrite | 1.8% |
| Ninetales-Alola@Choice Scarf | 0.8% |
| Vanilluxe (other item; Choice Scarf 4/5) | 0.8% |
| Blastoise@Blastoisinite | 0.7% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Rielly Chambers, 66th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0092/teamlist)
  - [Jarrod Rose, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0044/teamlist)
  - [Adrian van Dijk, 717th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0180/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Maura Hazen, 398th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0237/teamlist)
  - [Amar Curic, 188th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0289/teamlist)
  - [Perry Gallo, 924th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0071/teamlist)
  - [Davide Carrer, 104th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0335/teamlist)
  - [Omar Trejo, 137th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0187/teamlist)
  - [Jaden Streber, 136th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0504/teamlist)
  - [Laura Craig, 968th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1087/teamlist)
  - [Lance Lee, 1028th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0478/teamlist)
  - [rioreumi, , 9 Sep 2026](https://pokepast.es/07e91402b74462ef)
  - [Matthew Coldhill-Smink, 278th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0005/teamlist)
  - [oshio_pokemon, , 11 Sep 2026](https://pokepast.es/667cb69f9c820c84)
  - [gastrodon, , 10 Sep 2026](https://pokepast.es/59b0a674b59141a2)
  - [Tyler Coady, 427th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0376/teamlist)
  - [Matt Francis, 325th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0955/teamlist)
  - [Brandon Ebert, 1029th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0909/teamlist)

#### Community 1 / Sub-community 2: Psyspam (Mega Salamence) (98 primary teams, 61 distinct builds, top pair on 35/98)
- Megas on member teams: Salamence 61, Metagross 35, Charizard-Y 18, Gardevoir 12, Garchomp-Z 8, Dragonite 5
- Top species by team share: Sneasler 97%, Salamence 62%, Indeedee 55%, Indeedee-F 38%, Metagross 37%, Arcanine-Hisui 32%
- Token label: Sneasler@Psychic Seed + Metagross@Metagrossite/Indeedee-F
- Mode tags on primary teams: Psyspam 80, Tailwind 45, Sun 20, Sand 11, Snow 7, Setup 5, Trick Room 4, Rain 3, Screens 1
- Primary teams: 98 (17.0% of the community's primary weight), hybrid teams: 14 (2.5%)
- Date range: 2026-09-10 to 2026-09-27
- Core pairs: Meowstic-F (other item; Meowsticite 4/4)+Charizard@Charizardite Y, Armarouge (other item; Life Orb 13/23)+Indeedee-F@Psychic Seed, Kommo-o (other item; Leftovers 8/12)+Charizard@Charizardite Y, Indeedee-F (other item; Rocky Helmet 24/56)+Gardevoir@Gardevoirite, Garchomp (other item; Life Orb 9/13)+Charizard@Charizardite Y, Indeedee (other item; Focus Sash 23/36)+Kommo-o (other item; Leftovers 8/12), Charizard@Charizardite Y+Garchomp@Choice Scarf, Armarouge (other item; Life Orb 13/23)+Indeedee-F (other item; Rocky Helmet 24/56), Incineroar (other item; Sitrus Berry 64/77)+Gardevoir@Gardevoirite, Basculegion@Choice Scarf+Golisopod@Golisopite, Corviknight (other item; Leftovers 15/19)+Garchomp@Garchompite Z, Indeedee-F (other item; Rocky Helmet 24/56)+Golisopod@Golisopite, Sylveon (other item; Fairy Feather 24/28)+Charizard@Charizardite Y, Indeedee (other item; Focus Sash 23/36)+Charizard@Charizardite Y, Meowstic-F (other item; Meowsticite 4/4)+Sneasler@Psychic Seed, Arcanine-Hisui (other item; Focus Sash 96/99)+Metagross@Metagrossite, Gardevoir@Gardevoirite+Sneasler@Psychic Seed, Gholdengo (other item; Life Orb 101/109)+Gardevoir@Gardevoirite, Metagross@Metagrossite+Sneasler@Psychic Seed, Indeedee (other item; Focus Sash 23/36)+Sneasler@Psychic Seed, Indeedee (other item; Focus Sash 23/36)+Sylveon (other item; Fairy Feather 24/28), Incineroar (other item; Sitrus Berry 64/77)+Sylveon (other item; Fairy Feather 24/28), Indeedee-F (other item; Rocky Helmet 24/56)+Sneasler@Psychic Seed, Basculegion@Choice Scarf+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 64/77)+Floette-Eternal@Floettite, Indeedee@Choice Scarf+Metagross@Metagrossite, Indeedee-F (other item; Rocky Helmet 24/56)+Kommo-o (other item; Leftovers 8/12), Milotic (other item; Sitrus Berry 81/154)+Metagross@Metagrossite, Incineroar (other item; Sitrus Berry 64/77)+Sinistcha (other item; Colbur Berry 11/28), Kingambit (other item; Chople Berry 135/240)+Meowstic-F (other item; Meowsticite 4/4), Kommo-o (other item; Leftovers 8/12)+Sneasler@Psychic Seed, Garchomp@Garchompite Z+Metagross@Metagrossite, Garchomp (other item; Life Orb 9/13)+Froslass@Froslassite, Kingambit (other item; Chople Berry 135/240)+Garchomp@Choice Scarf, Indeedee@Choice Scarf+Sneasler@Psychic Seed, Armarouge (other item; Life Orb 13/23)+Sneasler@Psychic Seed, Sneasler (other item; White Herb 156/204)+Garchomp@Choice Scarf, Charizard@Charizardite Y+Sneasler@Psychic Seed, Kingambit (other item; Chople Berry 135/240)+Charizard@Charizardite Y, Excadrill (other item; Focus Sash 197/213)+Indeedee-F@Psychic Seed, Indeedee-F@Psychic Seed+Tyranitar@Tyranitarite, Charizard@Charizardite Y+Sneasler@Grassy Seed, Garchomp (other item; Life Orb 9/13)+Kingambit (other item; Chople Berry 135/240), Incineroar (other item; Sitrus Berry 64/77)+Sneasler@Grassy Seed, Sneasler (other item; White Herb 156/204)+Indeedee-F@Psychic Seed, Milotic (other item; Sitrus Berry 81/154)+Sneasler@Psychic Seed, Basculegion (other item; Life Orb 72/97)+Sylveon (other item; Fairy Feather 24/28), Arcanine-Hisui (other item; Focus Sash 96/99)+Sneasler@Psychic Seed, Incineroar (other item; Sitrus Berry 64/77)+Indeedee-F (other item; Rocky Helmet 24/56), Garchomp@Garchompite Z+Sneasler@Psychic Seed, Garchomp (other item; Life Orb 9/13)+Sneasler@Grassy Seed, Corviknight (other item; Leftovers 15/19)+Sneasler@Psychic Seed, Sneasler (other item; White Herb 156/204)+Golisopod@Golisopite, Kingambit (other item; Chople Berry 135/240)+Sylveon (other item; Fairy Feather 24/28), Kingambit (other item; Chople Berry 135/240)+Garchomp@Garchompite Z, Gholdengo (other item; Life Orb 101/109)+Indeedee-F (other item; Rocky Helmet 24/56), Farigiraf (other item; Sitrus Berry 19/28)+Incineroar (other item; Sitrus Berry 64/77), Sylveon (other item; Fairy Feather 24/28)+Sneasler@Grassy Seed, Froslass@Froslassite+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 64/77)+Basculegion@Choice Scarf, Sneasler (other item; White Herb 156/204)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 171/271)+Golisopod@Golisopite, Gholdengo (other item; Life Orb 101/109)+Sneasler@Psychic Seed, Indeedee (other item; Focus Sash 23/36)+Metagross@Metagrossite, Indeedee-F (other item; Rocky Helmet 24/56)+Metagross@Metagrossite, Dragonite@Dragoninite+Sneasler@Psychic Seed, Metagross@Metagrossite+Salamence@Salamencite, Sylveon (other item; Fairy Feather 24/28)+Salamence@Salamencite, Armarouge (other item; Life Orb 13/23)+Salamence@Salamencite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Sneasler@Psychic Seed | 86.9% |
| Metagross@Metagrossite | 37.7% |
| Indeedee-F (other item; Rocky Helmet 24/56) | 35.8% |
| Indeedee (other item; Focus Sash 23/36) | 24.8% |
| Charizard@Charizardite Y | 18.2% |
| Incineroar (other item; Sitrus Berry 64/77) | 15.3% |
| Armarouge (other item; Life Orb 13/23) | 13.1% |
| Gardevoir@Gardevoirite | 11.4% |
| Kommo-o (other item; Leftovers 8/12) | 10.4% |
| Sylveon (other item; Fairy Feather 24/28) | 8.1% |
| Garchomp@Garchompite Z | 7.1% |
| Meowstic-F (other item; Meowsticite 4/4) | 5.8% |
| Golisopod@Golisopite | 3.9% |
| Garchomp@Choice Scarf | 3.6% |
| Garchomp (other item; Life Orb 9/13) | 3.4% |
| Indeedee-F@Psychic Seed | 1.0% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Anthony Rodriguez, 833rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0013/teamlist)
  - [Wyatt McDonald, , 15 Sep 2026](https://pokepast.es/97edd96cf0de7f14)
  - [Alexander Solimene, 281st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0500/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Jeremy Shepherd, 198th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0090/teamlist)
  - [Daniel Cunningham, 851st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0240/teamlist)
  - [Kristopher Horsey, 758th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0800/teamlist)
  - [yozora_952, , 15 Sep 2026](https://pokepast.es/90f7ccc7ab5b3d0b)
  - [Christina Bacino, 773rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0596/teamlist)
  - [Liam Good, 263rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1037/teamlist)
  - [Kais Arjai, 946th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0678/teamlist)
  - [Lennex Drummond, 233rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0278/teamlist)
  - [Jairo Contreras, 327th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0215/teamlist)
  - [Iker Rodrigo, 42nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0297/teamlist)
  - [Sergio Ramirez, 227th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0541/teamlist)
  - [Dimitri Kuster, 605th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0755/teamlist)
  - [Motochika Nabeshima, , 13 Sep 2026](https://pokepast.es/8ba4c9c260b8e9ae)
  - [Hiroshi Onishi, , 18 Sep 2026](https://pokepast.es/2dfbf9f89dfbd0bb)

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): Sub-community 3: Farigiraf / Torkoal (0 distinct builds, 0 primary teams, top pair on 0/0)

#### Community 1 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Sneasler | Sneasler (other item; White Herb 156/204) | Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 204 | 1: 96 · 0: 93 · 2: 11 · unassigned: 4 |
| Sneasler | Sneasler@Psychic Seed | Sub-community 2: Psyspam (Mega Salamence) | 138 | 2: 84 · 0: 53 · 1: 1 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 1: Kingambit / Rillaboom | 125 | 1: 117 · 0: 8 · unassigned: 0 |
| Indeedee | Indeedee@Choice Scarf | Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 125 | 0: 96 · 2: 29 · unassigned: 0 |
| Indeedee | Indeedee (other item; Focus Sash 23/36) | Sub-community 2: Psyspam (Mega Salamence) | 36 | 2: 25 · 0: 10 · 1: 1 · unassigned: 0 |

### Community 2: Setup (Mega Floette)
- Token label: Incineroar + Floette-Eternal/Delphox
- Mode tags on primary teams: Setup 127, Tailwind 57, Sun 22, Trick Room 14, Snow 11, Sand 7, Psyspam 6, Perish Trap 4, Screens 4, Rain 3
- Megas on primary teams: Floette 149, Delphox 67, Garchomp-Z 38, Blastoise 23, Metagross 22, Salamence 22, Charizard-Y 18, Dragonite 17, Gengar 13, Lucario-Z 13, Absol-Z 5, Baxcalibur 5, Golisopod 5, Froslass 4, Garchomp 4, Aerodactyl 3, Camerupt 3, Charizard-X 3, Staraptor 3, Glimmora 2, Raichu-Y 2, Abomasnow 1, Ampharos 1, Chandelure 1, Golurk 1, Lopunny 1, Mawile 1, Raichu-X 1, Scovillain 1, Tyranitar 1
- Primary teams: 227 (primary share 8.0%), hybrid teams: 240 (hybrid share 7.5%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Goodra-Hisui+Espathra@Grassy Seed, Espathra+Goodra-Hisui@Leftovers, Espathra+Goodra-Hisui, Absol+Goodra-Hisui@Leftovers, Absol+Espathra@Grassy Seed, Absol+Goodra-Hisui, Goodra-Hisui+Absol@Absolite Z, Maushold+Sinistcha@Occa Berry, Hatterene+Incineroar@White Herb, Camerupt+Incineroar@White Herb, Blastoise+Maushold@Chople Berry, Vivillon+Incineroar@Passho Berry, Blastoise+Sinistcha@Occa Berry, Maushold+Sinistcha@Coba Berry, Maushold+Blastoise@Blastoisinite, Blastoise+Sinistcha@Coba Berry, Delphox+Sinistcha@Occa Berry, Blastoise+Maushold, Gengar+Incineroar@Lum Berry, Delphox+Maushold@Chople Berry, Absol+Espathra, Espathra+Absol@Absolite Z, Delphox+Sinistcha@Coba Berry, Sinistcha+Maushold@Chople Berry, Delphox+Sinistcha@Colbur Berry, Gengar+Incineroar@Passho Berry, Sinistcha+Delphox@Delphoxite, Delphox+Sinistcha, Sinistcha+Blastoise@Blastoisinite, Delphox+Sneasler@Focus Sash, Maushold+Delphox@Delphoxite, Politoed+Incineroar@Passho Berry, Blastoise+Sinistcha, Delphox+Maushold, Delphox+Blastoise@Blastoisinite, Sinistcha+Sableye@Roseli Berry, Blastoise+Delphox@Delphoxite, Blastoise+Delphox, Sirfetch’d+Incineroar@Chople Berry, Maushold+Sinistcha, Sinistcha+Sneasler@Focus Sash, Maushold+Sneasler@Focus Sash, Gengar+Dragonite@Life Orb, Blastoise+Sneasler@Focus Sash, Floette-Eternal+Espathra@Grassy Seed, Lucario+Basculegion@Focus Sash, Floette-Eternal+Goodra-Hisui@Leftovers, Farigiraf+Incineroar@White Herb, Indeedee-F+Delphox@Life Orb, Dragonite+Indeedee@Focus Sash, Goodra-Hisui+Floette-Eternal@Floettite, Floette-Eternal+Goodra-Hisui, Gengar+Incineroar@Chople Berry, Blastoise+Indeedee-F@Rocky Helmet, Politoed+Incineroar@Chople Berry, Indeedee-F+Incineroar@White Herb, Swampert+Sinistcha@Sitrus Berry, Delphox+Kingambit@Life Orb, Lucario+Basculegion@Life Orb, Floette-Eternal+Sinistcha@Colbur Berry, Toxapex+Incineroar@Sitrus Berry, Farigiraf+Incineroar@Life Orb, Absol+Armarouge@Life Orb, Aerodactyl+Lucario@Lucarionite Z, Archaludon+Incineroar@Passho Berry, Aerodactyl+Lucario, Goodra-Hisui+Incineroar@Sitrus Berry, Farigiraf+Incineroar@Expert Belt, Floette-Eternal+Sneasler@Focus Sash, Torkoal+Incineroar@White Herb, Indeedee-F+Blastoise@Blastoisinite, Excadrill+Sinistcha@Sitrus Berry, Floette-Eternal+Delphox@Delphoxite, Sableye+Sinistcha, Floette-Eternal+Incineroar@Rocky Helmet, Blastoise+Indeedee-F, Delphox+Floette-Eternal@Floettite, Delphox+Floette-Eternal, Incineroar+Espathra@Focus Sash, Incineroar+Farigiraf@Twisted Spoon, Absol+Armarouge, Armarouge+Absol@Absolite Z, Lucario+Armarouge@Focus Sash, Blastoise+Indeedee-F@Colbur Berry, Floette-Eternal+Incineroar@Sitrus Berry, Incineroar+Rillaboom@Eject Button, Grimmsnarl+Sinistcha@Sitrus Berry, Annihilape+Lucario@Lucarionite Z, Annihilape+Lucario, Floette-Eternal+Rillaboom@Occa Berry, Incineroar+Toxapex@Leftovers, Camerupt+Blastoise@Blastoisinite, Espathra+Floette-Eternal@Floettite, Pelipper+Sinistcha@Sitrus Berry, Incineroar+Aegislash@Focus Sash, Espathra+Floette-Eternal, Farigiraf+Incineroar@Leftovers, Sinistcha+Kingambit@Life Orb, Tyranitar+Sinistcha@Sitrus Berry, Pawmot+Delphox@Delphoxite, Abomasnow+Incineroar, Incineroar+Abomasnow@Abomasite, Incineroar+Vivillon, Blastoise+Camerupt, Blastoise+Camerupt@Cameruptite, Incineroar+Goodra-Hisui@Leftovers, Absol+Aerodactyl, Aerodactyl+Absol@Absolite Z, Incineroar+Vivillon@Focus Sash, Incineroar+Toxapex, Floette-Eternal+Sinistcha@Occa Berry, Pawmot+Lucario@Lucarionite Z, Blastoise+Armarouge@Life Orb, Lucario+Pawmot, Maushold+Indeedee-F@Rocky Helmet, Incineroar+Politoed@Sitrus Berry, Delphox+Pawmot, Kommo-o+Incineroar@Chople Berry, Lucario+Aerodactyl@Aerodactylite, Golisopod+Incineroar@Chople Berry, Golisopod+Sinistcha@Kasib Berry, Sinistcha+Pawmot@Focus Sash, Floette-Eternal+Sinistcha@Coba Berry, Goodra-Hisui+Incineroar, Incineroar+Gengar@Gengarite, Incineroar+Maushold@Focus Sash, Floette-Eternal+Incineroar, Gengar+Incineroar, Incineroar+Floette-Eternal@Floettite, Incineroar+Sinistcha@Occa Berry, Blastoise+Indeedee-F@Psychic Seed, Kingambit+Incineroar@White Herb, Floette-Eternal+Sinistcha, Sinistcha+Kommo-o@Leftovers, Floette-Eternal+Dragonite@Dragoninite, Sinistcha+Floette-Eternal@Floettite, Floette-Eternal+Incineroar@Leftovers, Basculegion+Lucario@Lucarionite Z, Lucario+Garchomp@Garchompite Z, Ampharos+Incineroar, Incineroar+Ampharos@Ampharosite, Basculegion+Lucario, Hippowdon+Incineroar, Clefable+Incineroar, Incineroar+Espathra@Grassy Seed, Floette-Eternal+Sneasler@Grassy Seed, Incineroar+Sinistcha@Colbur Berry, Farigiraf+Incineroar@Chople Berry, Dragonite+Floette-Eternal@Floettite, Dragonite+Floette-Eternal, Delphox+Kingambit@Black Glasses, Indeedee-F+Sinistcha@Coba Berry, Lucario+Incineroar@Sitrus Berry, Delphox+Incineroar@Sitrus Berry, Espathra+Incineroar, Sinistcha+Incineroar@Sitrus Berry, Absol+Indeedee-F@Rocky Helmet, Ninetales-Alola+Delphox@Delphoxite, Espathra+Incineroar@Sitrus Berry, Primarina+Lucario@Lucarionite Z, Dragonite+Sneasler@White Herb, Delphox+Kommo-o@Leftovers, Lucario+Primarina, Lucario+Rillaboom@Occa Berry, Pawmot+Sinistcha, Floette-Eternal+Gholdengo@Leftovers, Dragonite+Metagross@Metagrossite, Kommo-o+Incineroar@Passho Berry, Blastoise+Incineroar@Sitrus Berry, Delphox+Ninetales-Alola, Kingambit+Delphox@Delphoxite, Aegislash+Incineroar@Sitrus Berry, Armarouge+Blastoise@Blastoisinite, Incineroar+Sinistcha@Kasib Berry, Floette-Eternal+Garchomp@Sitrus Berry, Incineroar+Lopunny, Incineroar+Lopunny@Lopunnite, Dragonite+Metagross, Incineroar+Garchomp@Garchompite, Delphox+Pawmot@Focus Sash, Kingambit+Sinistcha@Colbur Berry, Delphox+Kingambit, Sinistcha+Swampert@Swampertite, Delphox+Rillaboom@Occa Berry, Absol+Sneasler@Psychic Seed, Floette-Eternal+Kingambit@Life Orb, Armarouge+Blastoise, Lucario+Dragapult@Life Orb, Lucario+Rillaboom@Life Orb, Camerupt+Incineroar, Incineroar+Camerupt@Cameruptite, Floette-Eternal+Whimsicott@Focus Sash, Golisopod+Sinistcha@Sitrus Berry, Archaludon+Incineroar@Chople Berry, Incineroar+Lucario@Lucarionite Z, Incineroar+Ninetales-Alola@Focus Sash, Baxcalibur+Incineroar@Chople Berry, Blastoise+Sneasler@Psychic Seed, Floette-Eternal+Garchomp@Choice Scarf, Incineroar+Lucario, Dragonite+Incineroar@Sitrus Berry, Metagross+Dragonite@Dragoninite, Incineroar+Kommo-o@Leftovers, Incineroar+Rillaboom@Occa Berry, Absol+Indeedee-F, Indeedee-F+Absol@Absolite Z, Sinistcha+Baxcalibur@Baxcalibrite, Incineroar+Sirfetch’d@Leek, Incineroar+Delphox@Delphoxite, Sinistcha+Swampert, Dragapult+Lucario@Lucarionite Z, Incineroar+Sneasler@Focus Sash, Excadrill+Sinistcha@Colbur Berry, Gardevoir+Sinistcha@Sitrus Berry, Dragapult+Lucario, Goodra-Hisui+Rillaboom@Miracle Seed, Delphox+Incineroar, Rotom-Wash+Incineroar@Sitrus Berry, Lucario+Incineroar@Chople Berry, Incineroar+Venusaur@Wide Lens, Delphox+Incineroar@Rocky Helmet, Floette-Eternal+Kingambit@Occa Berry, Incineroar+Volcarona@Grassy Seed, Indeedee-F+Maushold@Chople Berry, Sneasler+Sinistcha@Occa Berry, Floette-Eternal+Gholdengo@Choice Scarf, Floette-Eternal+Basculegion@Focus Sash, Incineroar+Sinistcha, Dragonite+Pelipper@Sitrus Berry, Delphox+Garchomp@Choice Scarf, Rillaboom+Incineroar@Lum Berry, Rillaboom+Espathra@Grassy Seed, Incineroar+Mawile, Incineroar+Mawile@Mawilite, Dragonite+Basculegion@Life Orb, Incineroar+Garchomp@Garchompite Z, Gardevoir+Blastoise@Blastoisinite, Incineroar+Venusaur@Life Orb, Incineroar+Rillaboom@Leftovers, Incineroar+Sinistcha@Rocky Helmet, Vanilluxe+Incineroar@Sitrus Berry, Incineroar+Sirfetch’d, Blastoise+Gardevoir, Blastoise+Gardevoir@Gardevoirite, Absol+Milotic@Leftovers, Delphox+Incineroar@Passho Berry, Tyranitar+Sinistcha@Colbur Berry, Sneasler+Delphox@Delphoxite, Maushold+Gengar@Gengarite, Dragonite+Rillaboom@Life Orb, Sinistcha+Tyranitar@Tyranitarite, Gengar+Maushold, Floette-Eternal+Volcarona@Grassy Seed, Sinistcha+Pelipper@Focus Sash, Incineroar+Dragonite@Dragoninite, Sneasler+Dragonite@Dragoninite, Garchomp+Incineroar@Sitrus Berry, Delphox+Sneasler, Volcarona+Incineroar@Sitrus Berry, Sinistcha+Tyranitar, Gholdengo+Dragonite@Dragoninite, Absol+Floette-Eternal, Floette-Eternal+Absol@Absolite Z, Espathra+Archaludon@Leftovers, Incineroar+Farigiraf@Colbur Berry, Sinistcha+Indeedee-F@Rocky Helmet, Baxcalibur+Sinistcha, Whimsicott+Floette-Eternal@Floettite, Floette-Eternal+Whimsicott, Incineroar+Garchomp@Choice Scarf, Blastoise+Incineroar, Sneasler+Maushold@Chople Berry, Sneasler+Sinistcha@Coba Berry, Lucario+Rillaboom@Miracle Seed, Blastoise+Kingambit@Focus Sash, Archaludon+Sinistcha@Sitrus Berry, Floette-Eternal+Sneasler, Sneasler+Floette-Eternal@Floettite, Lucario+Sneasler@Grassy Seed, Floette-Eternal+Maushold@Chople Berry, Indeedee+Dragonite@Dragoninite, Hatterene+Incineroar, Maushold+Incineroar@Sitrus Berry, Garchomp+Lucario@Lucarionite Z, Absol+Indeedee-F@Colbur Berry, Incineroar+Hatterene@Life Orb, Dragonite+Incineroar, Sneasler+Blastoise@Blastoisinite, Delphox+Indeedee-F@Rocky Helmet, Dragonite+Sneasler, Incineroar+Politoed, Garchomp+Lucario, Incineroar+Blastoise@Blastoisinite, Rillaboom+Goodra-Hisui@Leftovers, Swampert+Incineroar@Passho Berry, Rillaboom+Lucario@Lucarionite Z, Archaludon+Espathra, Primarina+Incineroar@Sitrus Berry, Lucario+Rillaboom, Incineroar+Sinistcha@Coba Berry, Corviknight+Delphox, Blastoise+Sneasler, Altaria+Incineroar@Sitrus Berry, Garchomp+Incineroar@Chople Berry, Indeedee-F+Sinistcha@Sitrus Berry, Dragonite+Gholdengo@Life Orb, Absol+Incineroar@Sitrus Berry, Dragonite+Gholdengo, Incineroar+Maushold@Chople Berry, Floette-Eternal+Rillaboom@Sitrus Berry, Incineroar+Primarina@Grassy Seed, Pelipper+Sinistcha, Incineroar+Sneasler@Grassy Seed, Armarouge+Lucario@Lucarionite Z, Floette-Eternal+Rillaboom@Life Orb, Aegislash+Incineroar, Kommo-o+Sinistcha, Armarouge+Lucario, Sinistcha+Excadrill@Focus Sash, Absol+Floette-Eternal@Floettite, Rillaboom+Incineroar@Rocky Helmet, Incineroar+Basculegion@Focus Sash, Incineroar+Weavile, Whimsicott+Incineroar@Rocky Helmet, Rillaboom+Incineroar@Passho Berry, Excadrill+Sinistcha, Gholdengo+Incineroar@Rocky Helmet, Incineroar+Maushold, Goodra-Hisui+Rillaboom, Garchomp+Incineroar, Delphox+Rillaboom@Life Orb, Kingambit+Floette-Eternal@Floettite, Dragonite+Froslass, Dragonite+Froslass@Froslassite, Floette-Eternal+Kingambit, Dragonite+Indeedee, Incineroar+Kingambit@Black Glasses, Incineroar+Primarina@Leftovers, Incineroar+Charizard@Charizardite X, Incineroar+Rillaboom@Life Orb, Rillaboom+Maushold@Focus Sash, Floette-Eternal+Gholdengo@Life Orb, Sinistcha+Kingambit@Black Glasses, Maushold+Floette-Eternal@Floettite, Floette-Eternal+Kingambit@Black Glasses, Dragonite+Sneasler@Psychic Seed, Floette-Eternal+Maushold, Gholdengo+Floette-Eternal@Floettite, Swampert+Incineroar@Chople Berry, Floette-Eternal+Gholdengo, Dragonite+Sneasler@Focus Sash, Floette-Eternal+Rillaboom, Incineroar+Aerodactyl@Focus Sash, Rillaboom+Floette-Eternal@Floettite, Floette-Eternal+Basculegion@Life Orb, Lucario+Indeedee-F@Colbur Berry, Pawmot+Incineroar@Sitrus Berry, Aerodactyl+Incineroar@Sitrus Berry, Floette-Eternal+Basculegion@Choice Scarf, Indeedee-F+Maushold, Floette-Eternal+Rillaboom@Miracle Seed, Sneasler+Incineroar@Rocky Helmet, Incineroar+Farigiraf@Grassy Seed, Indeedee-F+Sinistcha@Occa Berry, Dragonite+Kingambit@Chople Berry, Froslass+Dragonite@Dragoninite, Sinistcha+Garchomp@Choice Scarf, Farigiraf+Incineroar@Rocky Helmet, Sneasler+Incineroar@Sitrus Berry, Delphox+Kommo-o, Incineroar+Kommo-o, Basculegion+Floette-Eternal@Floettite, Garchomp+Floette-Eternal@Floettite, Rillaboom+Incineroar@Sitrus Berry, Basculegion+Floette-Eternal, Floette-Eternal+Garchomp, Salamence+Incineroar@Rocky Helmet, Clefable+Rillaboom, Volcarona+Floette-Eternal@Floettite, Incineroar+Indeedee-F@Psychic Seed, Floette-Eternal+Volcarona, Kingambit+Sinistcha, Rillaboom+Incineroar@Chople Berry, Delphox+Kingambit@Chople Berry, Incineroar+Rotom-Wash, Lucario+Basculegion@Choice Scarf, Kommo-o+Delphox@Delphoxite, Incineroar+Rillaboom, Sinistcha+Grimmsnarl@Light Clay, Pelipper+Sinistcha@Colbur Berry, Sneasler+Sinistcha@Colbur Berry, Mawile+Incineroar@Sitrus Berry, Sinistcha+Golisopod@Golisopite, Golisopod+Sinistcha, Gengar+Incineroar@Sitrus Berry, Corviknight+Delphox@Delphoxite, Incineroar+Rillaboom@Grassy Seed, Whimsicott+Incineroar@Chople Berry, Incineroar+Volcarona, Gholdengo+Incineroar@Sitrus Berry, Lucario+Sylveon@Fairy Feather, Basculegion+Dragonite@Dragoninite, Grimmsnarl+Sinistcha, Basculegion+Dragonite, Froslass+Incineroar@Passho Berry, Espathra+Rillaboom, Rillaboom+Dragonite@Dragoninite, Rillaboom+Dragonite@Life Orb
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Incineroar | 91.9% | intimidate 99.9%, fake-out 99.5%, pivot 93.0%, trick-room-abuser 23.9%, helping-hand 7.7%, spa-drop 6.7%, disruption 4.6%, status 0.9% |
| Floette-Eternal | 65.4% | mega-attacker 99.7%, setup 83.5% |
| Delphox | 34.0% | mega-attacker 95.6%, setup 82.1%, disruption 4.3%, status 1.0%, terrain-setter 0.4%, priority-blocker 0.4% |
| Sinistcha | 30.5% | rage-powder 97.3%, trick-room-setter 74.1%, trick-room-abuser 15.7%, disruption 1.3%, follow-me 0.9% |
| Blastoise | 11.1% | mega-attacker 96.0%, setup 81.8%, fake-out 12.1%, pivot 4.0%, status 1.5% |
| Maushold | 10.6% | follow-me 98.2%, disruption 39.2%, helping-hand 10.7%, speed-drop 2.6%, pivot 1.8% |
| Dragonite | 9.8% | mega-attacker 87.5%, tailwind 64.8%, priority-attack 18.6%, setup 1.2% |
| Lucario | 4.8% | mega-attacker 98.8%, setup 69.5%, priority-attack 2.7% |
| Espathra | 3.7% | setup 87.7% |
| Absol | 2.6% | mega-attacker 100.0%, speed-drop 10.2%, status 7.9%, priority-attack 7.2%, perish-song 2.8%, setup 2.0% |
| Goodra-Hisui | 2.5% | setup 36.5%, trick-room-abuser 9.5% |
| Clefable | 0.5% | follow-me 100.0%, helping-hand 51.2%, trick-room-abuser 29.9%, speed-drop 18.9% |
- Representative teams (primary teams with the highest score for this community):
  - [Maik Witaszak, 130th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0339/teamlist)
  - [Ignasi Estarlich, 718th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0821/teamlist)
  - [Christian Mossati, 24th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0234/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Ignacio Marquez Albes, 81st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0032/teamlist)
  - [Sam Tabner, 261st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0294/teamlist)
  - [Hsuan-Chih Kuo, 424th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0826/teamlist)
  - [vi0ra_pokemon, , 14 Sep 2026](https://pokepast.es/74aea655c757917f)
  - [Christopher Epps, 732nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0150/teamlist)
  - [Stefano Greppi, , 15 Sep 2026](https://pokepast.es/045cc1b2f27f9315)
  - [Anton Meßner, 454th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1078/teamlist)
  - [Jeffrey Lehmann, 986th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0461/teamlist)
  - [Chris Santalis, 101st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0613/teamlist)
  - [Ben Alexander, 265th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0027/teamlist)
  - [Scott Sellers, 131st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0063/teamlist)
  - [Berkin Dag, 483rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0169/teamlist)
  - [Lorenzo Pugliese, 624th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1058/teamlist)
  - [Francisco Garví, 653rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0329/teamlist)
  - [Nico Corsten, 685th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1039/teamlist)
  - [Christian Polo, 884th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1008/teamlist)
  - [Tom Sommer, 972nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0599/teamlist)
  - [Benjamin Rimmer, 983rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0327/teamlist)
  - [Pascal Weih, 1111th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0550/teamlist)
  - [Ben Foust, 143rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0862/teamlist)
  - [Alex Boswell, 289th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0438/teamlist)
  - [Jason Kim, 408th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0796/teamlist)
  - [Zhiyuan Zhang, 587th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0985/teamlist)
  - [Ethan Matijevic, 725th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0224/teamlist)
  - [Jean-Ulysses Serrano Albuerne, 807th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0705/teamlist)
  - [Hershal Rami, 818th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0849/teamlist)
  - [Alfredo Bell, 890th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0007/teamlist)
  - [Calvin Nisson, 969th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0473/teamlist)
  - [Joey Sybert, 1008th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0499/teamlist)
  - [punihina1334, , 10 Sep 2026](https://pokepast.es/202c514602d9abe9)
  - [tempo777, , 10 Sep 2026](https://pokepast.es/9fed7bfc061ad8bb)
  - [Seowon Kim, , 9 Sep 2026](https://pokepast.es/6f1b0e8df51cc57d)
  - [Uch, , 9 Sep 2026](https://pokepast.es/33b3042210ffc178)
  - [Andrew Navarro, 115th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0260/teamlist)
  - [CloverBells, , 13 Sep 2026](https://pokepast.es/fdc0b961c0d0ef8c)
  - [Bayley Moore, 277th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0009/teamlist)
  - [Victor Bo, 445th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0624/teamlist)
  - [Chang Huang, 793rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1039/teamlist)
  - [David Markin, 960th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0693/teamlist)
  - [Mouad Tiahi, 990th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0676/teamlist)
  - [Sean Alexis Balayon, 994th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0394/teamlist)
  - [Xena, , 11 Sep 2026](https://pokepast.es/983fa284e2d45493)
  - [Justin Cerioni, , 11 Sep 2026](https://pokepast.es/a88b8c2cfc274ab2)
  - [starfish0206, , 11 Sep 2026](https://pokepast.es/5d8e24080b6d3200)
  - [mop, , 10 Sep 2026](https://pokepast.es/8536a2b4f89d3e72)
  - [metalderk, , 10 Sep 2026](https://pokepast.es/b18bfd4e8d8129be)
  - [Steven Guo, 799th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0873/teamlist)
  - [Jay Gelunas, 1062nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0446/teamlist)
  - [Alejandro, , 10 Sep 2026](https://pokepast.es/785dbfcd2432c102)
  - [Wyatt McDonald, , 9 Sep 2026](https://pokepast.es/5b9fae64b204f805)
  - [Louis Markl, 19th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0737/teamlist)
  - [Cayden Aitchison, 156th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0314/teamlist)
  - [Alessio Ferrara, 112th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1034/teamlist)
  - [Tim Eggert, 561st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0948/teamlist)
  - [Robert Haas, 970th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0767/teamlist)
  - [Nathan Couto, 533rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0019/teamlist)
  - [Alec Pineda, 1050th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0248/teamlist)
  - [shynessalex, Champion, 20 Sep 2026](https://pokepast.es/6f1d5b2b15285f2b)
  - [Wyatt McDonald, Top 4, 16 Sep 2026](https://pokepast.es/37083bbe99a1bf42)
  - [Angelina Huynh, 249th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0554/teamlist)
  - [Joseph Eckhart, 100th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0904/teamlist)
  - [Nick Donato, 434th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0563/teamlist)
  - [Nate Curl, 745th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0608/teamlist)
  - [Joshua Fitzhardy, 110th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0108/teamlist)
  - [Manuel Schwab, 649th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0562/teamlist)
  - [Dennis Bouma, 931st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1127/teamlist)
  - [Giovanni Cabrera, 964th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0914/teamlist)
  - [Lorenzo Bucci, , 10 Sep 2026](https://pokepast.es/42f46820310783d1)
  - [Charles Hsiao, 625th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0399/teamlist)
  - [Naoyuki Matsuhashi, , 9 Sep 2026](https://pokepast.es/0071e895c381dd1c)
  - [Ethan Tyssen, 726th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0402/teamlist)
  - [Ryan Bogdansky, 908th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0232/teamlist)
  - [Dominik Mairiedl, 85th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1082/teamlist)
  - [Robin Lucas Bachofner, 1085th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0014/teamlist)
  - [Matthew Mitchell, 71st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0228/teamlist)
  - [john Valentino, 1039th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0090/teamlist)
  - [Sam Bentham, 123rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0722/teamlist)
  - [Zhuoran Liu, 50th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0245/teamlist)
  - [Dylan Renon, 1096th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0651/teamlist)
  - [joserockzvgc, , 16 Sep 2026](https://pokepast.es/8606abba447d2e2e)
  - [Adrian Hazel, 344th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0226/teamlist)
  - [Hunter O'Brien-Grayson, 397th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0118/teamlist)
  - [Máximo Beas Martínez, 22nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0733/teamlist)
  - [Hezekiah Saunders, 495th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0426/teamlist)
  - [Noah Carney, 328th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0444/teamlist)
  - [Riley J, 601st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0542/teamlist)
  - [Anthony Liuzzo, 5th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0219/teamlist)
  - [Hippolyte Bernard, 28th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0616/teamlist)
  - [Moonstone, , 10 Sep 2026](https://pokepast.es/adae5bcd485f81df)
  - [Joshua Sutherland-Smith, 4th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0302/teamlist)
  - [Jay Carson, 710th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0039/teamlist)
  - [Hisashiro Egashira, 836th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1046/teamlist)
  - [Tyler Coleman, 378th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0999/teamlist)
  - [Adonis Watford, 272nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0792/teamlist)
  - [Hunter Jones, 479th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0465/teamlist)
  - [Alex Thompson, 400th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0115/teamlist)
  - [Brandon Harrison, 486th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0185/teamlist)
  - [Aaron Penny, 274th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0046/teamlist)
  - [Jannik Baggett, 435th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0775/teamlist)
  - [Fabien Geeraert, 601st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0367/teamlist)
  - [Rees Griffith, 95th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0890/teamlist)
  - [Charles Erbring, 233rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0247/teamlist)
  - [Richard Mogollon, 296th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0018/teamlist)
  - [Ryan Weber, 540th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0101/teamlist)
  - [Kevin Miller, 674th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1070/teamlist)
  - [willbrown_vgc, 28th, 22 Sep 2026](https://pokepast.es/3ab405a83e618503)
  - [William Brown, 28th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0029/teamlist)
  - [Enrique Grimaldo, 109th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0070/teamlist)
  - [Adam Holmyard, 889th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0996/teamlist)
  - [Benjamin Haeseler, 461st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0140/teamlist)
  - [Santino Tarquinio, 173rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0204/teamlist)
  - [Thaddeus Valentine, 258th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0727/teamlist)
  - [Nika Wels, 511th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0370/teamlist)
  - [Alex Simsen, 628th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0174/teamlist)
  - [Ashley Scobey, 73rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0026/teamlist)
  - [Shohei Kimura, , 14 Sep 2026](https://pokepast.es/73a1ddc47375195f)
  - [Daniel Oatley, 894th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0433/teamlist)
  - [Charlie Caddell, 371st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0061/teamlist)
  - [Paschalis Dermentzis, 20th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0846/teamlist)
  - [Lazaros Lazaropoulos, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0813/teamlist)
  - [Charalampos Frimas, 185th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0069/teamlist)
  - [eeveeiynn, 194th, 21 Sep 2026](https://pokepast.es/00c891d2f3287a74)
  - [Evelyn Klaus, 193rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0148/teamlist)
  - [Emery Joseph, 43rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0004/teamlist)
  - [Rudra Kansara, 1000th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0651/teamlist)
  - [Nils Junge, 256th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0780/teamlist)
  - [Michael Lewis, 615th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0015/teamlist)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/36cd120de5ec394c)
  - [Justin Tobias, 297th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0647/teamlist)
  - [Jordi Martinez, 469th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0729/teamlist)
  - [Eloy Hahn, 172nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0878/teamlist)
  - [Wade Innes, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0230/teamlist)
  - [Wesley Brainard, 201st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0114/teamlist)
  - [Vito Jacono, 825th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0175/teamlist)
  - [DjSanders, , 13 Sep 2026](https://pokepast.es/ad11cad6ec6b68f7)
  - [Chern Yean Sim, 200th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0213/teamlist)
  - [Philemon Knafo, 140th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0752/teamlist)
  - [Thomas Wall, 768th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0639/teamlist)
  - [m_rada13, Champion, 11 Sep 2026](https://pokepast.es/3ac336df2eb4729b)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/60458cc1a20c2440)
  - [v1nzvgc, , 18 Sep 2026](https://pokepast.es/ab47ccee10152485)
  - [Kevin Holzmann, 807th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0067/teamlist)
  - [Minche Chung, 12th, 20 Sep 2026](https://pokepast.es/b987727e0339084a)
  - [Danial Syed, 453rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0658/teamlist)
  - [Lucas MacKenzie, 971st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0216/teamlist)
  - [Jan-Philipp Schmitz, 905th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0555/teamlist)
  - [Jonathan Todd, 730th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1008/teamlist)
  - [Spencer Verdoni, 500th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0691/teamlist)
  - [Kevin Guzman, 407th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0034/teamlist)
  - [Ethan Partelow, 476th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0590/teamlist)
  - [Ren Sandfry, 860th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0046/teamlist)
  - [homura_kurenai_, , 10 Sep 2026](https://pokepast.es/7f30345697a16032)
  - [gastrodon, , 10 Sep 2026](https://pokepast.es/59b0a674b59141a2)
  - [Keigo Tanizawa, 7th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0175/teamlist)
  - [Rod, , 15 Sep 2026](https://pokepast.es/18d8e22d3d9ad026)
  - [Jens de Visser, 640th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0994/teamlist)
  - [Jesse van den Kerkhof, 1046th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0827/teamlist)
  - [Angelo Pagano, 660th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0576/teamlist)
  - [David Gardner, 824th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0172/teamlist)
  - [Cassandra Maurer, 835th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0451/teamlist)
  - [Erizabeth, 4th, 13 Sep 2026](https://pokepast.es/412d57292e8dbd7e)
  - [Kashif Sayeed, 568th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0450/teamlist)
  - [Nathaniel Spann, 132nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0125/teamlist)
  - [Justin Tang, , 14 Sep 2026](https://pokepast.es/ca08bca9b981be5c)
  - [Brendan Zheng, 102nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0252/teamlist)
  - [Brendan Vaughan, 846th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0534/teamlist)
  - [Ignatius Lee, 159th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0091/teamlist)
  - [Tim Beyreuther, 621st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0731/teamlist)
  - [Stefano Greppi, , 10 Sep 2026](https://pokepast.es/5d77ccc3504a01b1)
  - [Nick Smith, 780th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1003/teamlist)
  - [Khalil Myers, 798th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0776/teamlist)
  - [Christian ONeil, 311th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0533/teamlist)
  - [Seth Ellsworth, 586th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0058/teamlist)
  - [Jairo Contreras, 327th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0215/teamlist)
  - [jt_speed, , 21 Sep 2026](https://pokepast.es/a566dfb356531ce8)
  - [Jason Smith, 692nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0129/teamlist)
  - [metalderk, , 11 Sep 2026](https://pokepast.es/c12df6cee74a35fe)
  - [gio_kuma, , 10 Sep 2026](https://pokepast.es/55029c098a5a309b)
  - [Matthew Bachman, 1031st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0552/teamlist)
  - [Adryan Sutantoso, , 11 Sep 2026](https://pokepast.es/a96e0b908f220884)
  - [starportal_, , 11 Sep 2026](https://pokepast.es/dad21fdac630175b)
  - [Laura Craig, 968th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1087/teamlist)
  - [Aediion_, , 11 Sep 2026](https://pokepast.es/94cb88bc739ff2af)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/134747480705edc0)
  - [Joshua Wong, 738th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0885/teamlist)
  - [kaki, , 13 Sep 2026](https://pokepast.es/a202a04735494175)
  - [Lukas Kiefl, 1110th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0772/teamlist)
  - [Daniel Halm, 540th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0607/teamlist)
  - [Lucia Scalies, 675th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0324/teamlist)
  - [David Durán, 1012th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0374/teamlist)
  - [Arnau Sánchez, 363rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0485/teamlist)
  - [Marc Torralba Brosa, 959th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1090/teamlist)
  - [Joseph Frontera, 848th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0883/teamlist)
  - [Majd Serhan, 891st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0416/teamlist)
  - [Jermaine Mcleod, , 10 Sep 2026](https://pokepast.es/b54467e1aa4327e1)
  - [Ellie Homen, 297th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0645/teamlist)
  - [Nikhil Rajbhandary, 443rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0848/teamlist)
  - [Chuck Yin Sin, 919th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0734/teamlist)
  - [etc25269248, , 13 Sep 2026](https://pokepast.es/7664efb6099c8d98)
  - [Marielle Lynnsen, 1072nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0419/teamlist)
  - [Ross Stewart, 234th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0622/teamlist)
  - [Athan Mallios, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0074/teamlist)
  - [Dorean Neron, 504th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0033/teamlist)
  - [John Masters, 571st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0275/teamlist)
  - [danyul_yt, , 10 Sep 2026](https://pokepast.es/57ef0cda9c81a1c6)
  - [Herbert Herrmann, 950th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0373/teamlist)
  - [Valentijn Visser, 7th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0818/teamlist)
  - [Livio Sandberg, 83rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0965/teamlist)
  - [Roel Egberts, 555th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0875/teamlist)
  - [jessditta, , 10 Sep 2026](https://pokepast.es/25ab0498e06c8ffb)
  - [Evan Graham, 602nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0970/teamlist)
  - [Benjamin Kurtzemann, 782nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0287/teamlist)
  - [MeK191817, , 11 Sep 2026](https://pokepast.es/a1338edf35719660)
  - [Ryosuke Hamasato, , 9 Sep 2026](https://pokepast.es/243c449b24b74266)
  - [Lamberto Pio Traverso, 266th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0592/teamlist)
  - [Lorenz Mirow, 789th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0565/teamlist)
  - [Kavin Gnana, 854th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0268/teamlist)
  - [francesco tomasino, 617th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0155/teamlist)
  - [David Kramer, 755th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0742/teamlist)
  - [Nils von Lengerke, 456th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0950/teamlist)
  - [Florian Klaus, 612th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0414/teamlist)
  - [Darcy Willis, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0254/teamlist)
  - [PiyoLily145, , 9 Sep 2026](https://pokepast.es/8e37c3b00cbba6b6)
  - [Jeremy Boyd, 294th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0900/teamlist)
  - [Quinn Prabhakar, 477th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0415/teamlist)
  - [Hushabye White, 966th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0616/teamlist)
  - [Miguel Stelmann Frias, 541st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0641/teamlist)
  - [Adrian Illert, 598th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0343/teamlist)
  - [Michael Mullen, 666th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0282/teamlist)
  - [Joshua Flickinger, 838th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0567/teamlist)
  - [pathogenvgc, , 14 Sep 2026](https://pokepast.es/a630a7a5018325c9)
  - [Lillian Heath, 767th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1067/teamlist)
  - [Duy Nguyen, 892nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0598/teamlist)
  - [Luisa Klöckner, 528th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0260/teamlist)
  - [Deduo Qiang, 879th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0668/teamlist)
  - [Sirhat Renklitepe, 878th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0143/teamlist)
  - [Nicole Burgy, 809th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0160/teamlist)
  - [Charlie Hall, 984th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0948/teamlist)
  - [Kristian Guimond, 671st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0928/teamlist)
  - [mie_swagger, , 14 Sep 2026](https://pokepast.es/a5b0fd2dfb4b9c13)

- Sub-community pass: 227 primary teams, 88 tokens, modularity 0.52; unconnected tokens: Annihilape (other item; Choice Scarf 2/5), Milotic (other item; Grassy Seed 2/5), Froslass (other item; Froslassite 4/4), Tyranitar (other item; Focus Sash 2/4), Camerupt (other item; Cameruptite 3/3), Charizard (other item; Charizardite X 3/3), Hippowdon (other item; Leftovers 3/3), Raichu (other item; Raichunite Y 2/3), Staraptor (other item; Staraptite 3/3), Talonflame (other item; Life Orb 2/3), Torkoal (other item; Life Orb 3/3), Volcarona (other item; Rocky Helmet 2/3); unassigned within the community: 1 teams (0.4% of its primary weight); hybrid teams of the community left out: 240
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 3 (0.68) | 4 (0.60) | 5 (0.52) | 6 (0.44) | 8 (0.38) |

#### Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar (105 primary teams, 65 distinct builds, top pair on 94/105)
- Megas on member teams: Floette 94, Charizard-Y 17, Dragonite 17, Salamence 17, Garchomp-Z 11, Blastoise 5
- Top species by team share: Incineroar 100%, Floette-Eternal 90%, Rillaboom 79%, Sneasler 55%, Garchomp 34%, Kingambit 32%
- Token label: Incineroar + Floette-Eternal@Floettite/Gholdengo
- Mode tags on primary teams: Setup 53, Tailwind 44, Sun 20, Trick Room 8, Snow 4, Screens 3, Psyspam 2, Perish Trap 2, Rain 1, Sand 1
- Primary teams: 105 (43.5% of the community's primary weight), hybrid teams: 34 (15.3%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Charizard@Charizardite Y+Garchomp@Choice Scarf, Whimsicott (other item; Focus Sash 10/11)+Charizard@Charizardite Y, Gholdengo (other item; Life Orb 29/31)+Dragonite@Dragoninite, Whimsicott (other item; Focus Sash 10/11)+Garchomp@Choice Scarf, Basculegion (other item; Life Orb 9/12)+Salamence@Salamencite, Gholdengo (other item; Life Orb 29/31)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 8/15)+Salamence@Salamencite, Gholdengo (other item; Life Orb 29/31)+Charizard@Charizardite Y, Gholdengo (other item; Life Orb 29/31)+Whimsicott (other item; Focus Sash 10/11), Basculegion@Choice Scarf+Salamence@Salamencite, Sneasler (other item; Focus Sash 64/117)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 100/145)+Sneasler@Grassy Seed, Floette-Eternal@Floettite+Sneasler@Grassy Seed, Goodra-Hisui (other item; Leftovers 5/5)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 100/145)+Dragonite@Dragoninite, Charizard@Charizardite Y+Floette-Eternal@Floettite, Gholdengo (other item; Life Orb 29/31)+Floette-Eternal@Floettite, Dragonite@Dragoninite+Floette-Eternal@Floettite, Whimsicott (other item; Focus Sash 10/11)+Floette-Eternal@Floettite, Floette-Eternal@Floettite+Garchomp@Choice Scarf, Kingambit (other item; Life Orb 42/77)+Floette-Eternal@Floettite, Basculegion@Choice Scarf+Floette-Eternal@Floettite, Dragapult (other item; Life Orb 3/5)+Floette-Eternal@Floettite, Basculegion (other item; Life Orb 9/12)+Floette-Eternal@Floettite, Absol@Absolite Z+Floette-Eternal@Floettite, Kingambit (other item; Life Orb 42/77)+Salamence@Salamencite, Floette-Eternal@Floettite+Salamence@Salamencite, Gholdengo (other item; Life Orb 29/31)+Sneasler (other item; Focus Sash 64/117), Gholdengo (other item; Life Orb 29/31)+Rillaboom (other item; Miracle Seed 100/145), Rillaboom (other item; Miracle Seed 100/145)+Charizard@Charizardite Y, Espathra (other item; Grassy Seed 4/7)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 171/210)+Whimsicott (other item; Focus Sash 10/11), Gholdengo (other item; Life Orb 29/31)+Incineroar (other item; Sitrus Berry 171/210), Incineroar (other item; Sitrus Berry 171/210)+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 171/210)+Baxcalibur@Baxcalibrite, Basculegion (other item; Life Orb 9/12)+Incineroar (other item; Sitrus Berry 171/210), Aerodactyl (other item; Aerodactylite 3/7)+Incineroar (other item; Sitrus Berry 171/210), Incineroar (other item; Sitrus Berry 171/210)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 171/210)+Basculegion@Choice Scarf, Incineroar (other item; Sitrus Berry 171/210)+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 171/210)+Garchomp@Choice Scarf, Incineroar (other item; Sitrus Berry 171/210)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 171/210)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 171/210)+Volcarona@Grassy Seed, Farigiraf (other item; Colbur Berry 3/8)+Incineroar (other item; Sitrus Berry 171/210), Incineroar (other item; Sitrus Berry 171/210)+Dragonite@Dragoninite, Goodra-Hisui (other item; Leftovers 5/5)+Incineroar (other item; Sitrus Berry 171/210), Incineroar (other item; Sitrus Berry 171/210)+Rotom-Wash (other item; Leftovers 3/4), Incineroar (other item; Sitrus Berry 171/210)+Absol@Absolite Z, Espathra (other item; Grassy Seed 4/7)+Incineroar (other item; Sitrus Berry 171/210), Incineroar (other item; Sitrus Berry 171/210)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 100/145)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 171/210)+Rillaboom (other item; Miracle Seed 100/145), Incineroar (other item; Sitrus Berry 171/210)+Kingambit (other item; Life Orb 42/77), Incineroar (other item; Sitrus Berry 171/210)+Salamence@Salamencite, Farigiraf (other item; Colbur Berry 3/8)+Rillaboom (other item; Miracle Seed 100/145), Garchomp (other item; Life Orb 8/15)+Incineroar (other item; Sitrus Berry 171/210)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Incineroar (other item; Sitrus Berry 171/210) | 100.0% |
| Floette-Eternal@Floettite | 87.1% |
| Gholdengo (other item; Life Orb 29/31) | 32.3% |
| Dragonite@Dragoninite | 20.5% |
| Sneasler@Grassy Seed | 17.3% |
| Charizard@Charizardite Y | 16.2% |
| Garchomp@Choice Scarf | 14.9% |
| Salamence@Salamencite | 14.9% |
| Garchomp (other item; Life Orb 8/15) | 10.7% |
| Whimsicott (other item; Focus Sash 10/11) | 10.1% |
| Basculegion (other item; Life Orb 9/12) | 8.6% |
| Farigiraf (other item; Colbur Berry 3/8) | 7.8% |
| Rotom-Wash (other item; Leftovers 3/4) | 2.1% |
| Dragapult (other item; Life Orb 3/5) | 1.4% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Giuseppe Musicco, 8th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0703/teamlist)
  - [Nakul Umashankar, 146th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0151/teamlist)
  - [Luca Lussignoli, 46th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0204/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Cole Basham, Top 8, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/rD6PrJBrCfzicynLnRAI)
  - [Sam Badenach, 149th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0011/teamlist)
  - [Alexander Moor, 285th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0581/teamlist)
  - [Oliver Marek, 546th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0990/teamlist)
  - [Chu Jian Hua, 690th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0803/teamlist)
  - [Edward Chan, 704th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0221/teamlist)
  - [Alexander Balic, 1112th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0379/teamlist)
  - [Gustavo, , 10 Sep 2026](https://pokepast.es/e2bab80c4e53d89a)
  - [Rahim Farzan, 96th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0901/teamlist)
  - [Jun-Wei To, 211th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0835/teamlist)
  - [Jamie Chen, 316th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0104/teamlist)
  - [Florian Menzel, 417th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0670/teamlist)
  - [Jonas Birarda, 307th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0794/teamlist)
  - [Tobias Gleixner, 864th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0057/teamlist)
  - [Hosea Gamble, 912th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0359/teamlist)
  - [mofumofunatsuhi, , 13 Sep 2026](https://pokepast.es/f85b026e5b0e6567)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [Thomas Schultz, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1054/teamlist)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [Aryan Shah, 963rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0388/teamlist)
  - [Escen Schaferin, 880th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0082/teamlist)
  - [Noah Gelman, 706th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0418/teamlist)
  - [Nicholas McLean, 31st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0058/teamlist)
  - [Curtis Ridings, 128th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0184/teamlist)
  - [Padrick Moran, 748th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1071/teamlist)
  - [Jordan Goggin, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0089/teamlist)
  - [Nate Innocenti, , 10 Sep 2026](https://pokepast.es/af730dd6acb60086)
  - [PathogenVGC, , 11 Sep 2026](https://pokepast.es/e8e7cf8271e66481)
  - [René Busam, 908th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1117/teamlist)
  - [Patrick Connolly, 301st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0178/teamlist)
  - [Alexander van der Smissen, 639th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0761/teamlist)
  - [squishvgc, , 13 Sep 2026](https://pokepast.es/4e238aef62574bf3)

#### Community 2 / Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed (42 primary teams, 24 distinct builds, top pair on 26/42)
- Megas on member teams: Garchomp-Z 26, Metagross 17, Floette 8, Lucario-Z 7, Baxcalibur 3, Gengar 3
- Top species by team share: Rillaboom 100%, Incineroar 98%, Garchomp 69%, Sneasler 62%, Volcarona 57%, Metagross 40%
- Token label: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed
- Mode tags on primary teams: Setup 8, Tailwind 7, Snow 3, Rain 2, Sun 1, Sand 1, Trick Room 1, Perish Trap 1
- Primary teams: 42 (17.5% of the community's primary weight), hybrid teams: 42 (16.1%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Aerodactyl (other item; Aerodactylite 3/7)+Lucario@Lucarionite Z, Metagross@Metagrossite+Volcarona@Grassy Seed, Garchomp@Garchompite Z+Volcarona@Grassy Seed, Garchomp@Garchompite Z+Metagross@Metagrossite, Basculegion@Choice Scarf+Volcarona@Grassy Seed, Basculegion@Choice Scarf+Salamence@Salamencite, Basculegion@Choice Scarf+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 100/145)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 100/145)+Golisopod@Golisopite, Rillaboom (other item; Miracle Seed 100/145)+Volcarona@Grassy Seed, Goodra-Hisui (other item; Leftovers 5/5)+Rillaboom (other item; Miracle Seed 100/145), Rillaboom (other item; Miracle Seed 100/145)+Absol@Absolite Z, Rillaboom (other item; Miracle Seed 100/145)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 100/145)+Gengar@Gengarite, Rillaboom (other item; Miracle Seed 100/145)+Lucario@Lucarionite Z, Rillaboom (other item; Miracle Seed 100/145)+Dragonite@Dragoninite, Sneasler (other item; Focus Sash 64/117)+Metagross@Metagrossite, Espathra (other item; Grassy Seed 4/7)+Rillaboom (other item; Miracle Seed 100/145), Sneasler (other item; Focus Sash 64/117)+Volcarona@Grassy Seed, Aerodactyl (other item; Aerodactylite 3/7)+Rillaboom (other item; Miracle Seed 100/145), Sneasler (other item; Focus Sash 64/117)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 100/145)+Metagross@Metagrossite, Ninetales-Alola (other item; Choice Scarf 2/6)+Rillaboom (other item; Miracle Seed 100/145), Basculegion@Choice Scarf+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 100/145)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 100/145)+Baxcalibur@Baxcalibrite, Gholdengo (other item; Life Orb 29/31)+Rillaboom (other item; Miracle Seed 100/145), Rillaboom (other item; Miracle Seed 100/145)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 171/210)+Garchomp@Garchompite Z, Aerodactyl (other item; Aerodactylite 3/7)+Incineroar (other item; Sitrus Berry 171/210), Incineroar (other item; Sitrus Berry 171/210)+Basculegion@Choice Scarf, Incineroar (other item; Sitrus Berry 171/210)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 171/210)+Volcarona@Grassy Seed, Rillaboom (other item; Miracle Seed 100/145)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 171/210)+Rillaboom (other item; Miracle Seed 100/145), Farigiraf (other item; Colbur Berry 3/8)+Rillaboom (other item; Miracle Seed 100/145)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Rillaboom (other item; Miracle Seed 100/145) | 100.0% |
| Garchomp@Garchompite Z | 62.9% |
| Volcarona@Grassy Seed | 59.2% |
| Metagross@Metagrossite | 38.6% |
| Basculegion@Choice Scarf | 17.7% |
| Lucario@Lucarionite Z | 15.9% |
| Aerodactyl (other item; Aerodactylite 3/7) | 8.6% |
| Golisopod@Golisopite | 7.3% |
| Ninetales-Alola (other item; Choice Scarf 2/6) | 2.7% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Reynaldo Boles, 171st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0064/teamlist)
  - [Yu Xiang, 41st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1043/teamlist)
  - [Sascha Eilts, 372nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0025/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Kathryn Aplin, 646th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1079/teamlist)
  - [Matthew Herndon, 999th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0094/teamlist)
  - [Adam Warren, 1056th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0657/teamlist)
  - [Daniel Soler Sanchez, 21st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0546/teamlist)
  - [Joan Garcia, 472nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1070/teamlist)
  - [Dani Pardo, 834th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0493/teamlist)
  - [Corey Okonowitz, 98th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0354/teamlist)
  - [horsea_tatsuomi, , 12 Sep 2026](https://pokepast.es/e328f6becb7a8d35)
  - [Ling, , 10 Sep 2026](https://pokepast.es/900d357c0ea74c31)
  - [Jon Huntley, 650th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0087/teamlist)
  - [Pasty, , 10 Sep 2026](https://pokepast.es/9c4f7915b1d3ae07)
  - [Julian Knapp, 599th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0963/teamlist)
  - [anrivgc, , 21 Sep 2026](https://pokepast.es/de8a417e6e85638a)
  - [Ann Rios, 232nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0166/teamlist)
  - [Ethan Miandrisoa, 190th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0573/teamlist)
  - [Emir Abdulovski, , 9 Sep 2026](https://pokepast.es/89d44fbed6fc7143)
  - [Florian Hoffmann, 174th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0702/teamlist)
  - [Motochika Nabeshima, , 9 Sep 2026](https://pokepast.es/5268ba8166d67ebf)
  - [Andrew Krebs, Top 4, 13 Sep 2026](https://pokepast.es/a4768a9bbf6876de)
  - [Luis Medina, 388th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0040/teamlist)
  - [Dane Bodamer, 863rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0475/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/81427a109e744097)
  - [Taya Wright, 252nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0236/teamlist)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [Jorijn Raijmakers, 573rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0693/teamlist)
  - [Andrew Wilson, 29th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1030/teamlist)
  - [Alyse Johnson, 183rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0106/teamlist)
  - [Frank Kovacs, 457th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0536/teamlist)
  - [Anthony Mubiala, 562nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0423/teamlist)
  - [Gregory Gramling, 663rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1023/teamlist)
  - [Garrett Wright, 972nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0297/teamlist)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [William Pye, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0229/teamlist)
  - [Jordan Goggin, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0089/teamlist)
  - [Thomas Schultz, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1054/teamlist)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)
  - [Michael Rogers, , 21 Sep 2026](https://pokepast.es/953deeed6ec8064d)
  - [MJ Rogers, 525th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0256/teamlist)
  - [Duy Thang Nguyen, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0617/teamlist)
  - [Tom de Gruijter, 217th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1093/teamlist)
  - [Arnout Bruijn, 1006th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0236/teamlist)

#### Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette) (72 primary teams, 30 distinct builds, top pair on 54/72)
- Megas on member teams: Delphox 64, Floette 43, Blastoise 18, Gengar 3, Salamence 3, Metagross 2
- Top species by team share: Delphox 89%, Incineroar 79%, Sinistcha 78%, Sneasler 74%, Floette-Eternal 60%, Kingambit 56%
- Token label: Delphox@Delphoxite / Sinistcha / Sneasler
- Mode tags on primary teams: Setup 62, Tailwind 6, Sand 5, Trick Room 5, Psyspam 4, Sun 1, Snow 1
- Primary teams: 72 (35.3% of the community's primary weight), hybrid teams: 21 (8.9%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Indeedee-F (other item; Rocky Helmet 17/22)+Blastoise@Blastoisinite, Maushold (other item; Chople Berry 15/21)+Blastoise@Blastoisinite, Indeedee-F (other item; Rocky Helmet 17/22)+Maushold (other item; Chople Berry 15/21), Sinistcha (other item; Colbur Berry 28/62)+Delphox@Delphoxite, Indeedee-F (other item; Rocky Helmet 17/22)+Sinistcha (other item; Colbur Berry 28/62), Maushold (other item; Chople Berry 15/21)+Sinistcha (other item; Colbur Berry 28/62), Sinistcha (other item; Colbur Berry 28/62)+Blastoise@Blastoisinite, Maushold (other item; Chople Berry 15/21)+Delphox@Delphoxite, Indeedee-F (other item; Rocky Helmet 17/22)+Delphox@Delphoxite, Blastoise@Blastoisinite+Delphox@Delphoxite, Kingambit (other item; Life Orb 42/77)+Kommo-o (other item; Leftovers 9/10), Sneasler (other item; Focus Sash 64/117)+Dragonite@Dragoninite, Kingambit (other item; Life Orb 42/77)+Gengar@Gengarite, Kommo-o (other item; Leftovers 9/10)+Sinistcha (other item; Colbur Berry 28/62), Pawmot (other item; Focus Sash 8/10)+Delphox@Delphoxite, Kingambit (other item; Life Orb 42/77)+Delphox@Delphoxite, Sneasler (other item; Focus Sash 64/117)+Baxcalibur@Baxcalibrite, Kommo-o (other item; Leftovers 9/10)+Delphox@Delphoxite, Pawmot (other item; Focus Sash 8/10)+Sinistcha (other item; Colbur Berry 28/62), Kingambit (other item; Life Orb 42/77)+Sinistcha (other item; Colbur Berry 28/62), Sneasler (other item; Focus Sash 64/117)+Metagross@Metagrossite, Sneasler (other item; Focus Sash 64/117)+Volcarona@Grassy Seed, Sneasler (other item; Focus Sash 64/117)+Blastoise@Blastoisinite, Sneasler (other item; Focus Sash 64/117)+Delphox@Delphoxite, Sneasler (other item; Focus Sash 64/117)+Garchomp@Garchompite Z, Maushold (other item; Chople Berry 15/21)+Sneasler (other item; Focus Sash 64/117), Kingambit (other item; Life Orb 42/77)+Floette-Eternal@Floettite, Sinistcha (other item; Colbur Berry 28/62)+Sneasler (other item; Focus Sash 64/117), Kingambit (other item; Life Orb 42/77)+Sneasler (other item; Focus Sash 64/117), Kingambit (other item; Life Orb 42/77)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 100/145)+Baxcalibur@Baxcalibrite, Gholdengo (other item; Life Orb 29/31)+Sneasler (other item; Focus Sash 64/117), Incineroar (other item; Sitrus Berry 171/210)+Baxcalibur@Baxcalibrite, Incineroar (other item; Sitrus Berry 171/210)+Kingambit (other item; Life Orb 42/77)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Delphox@Delphoxite | 91.4% |
| Sinistcha (other item; Colbur Berry 28/62) | 78.9% |
| Sneasler (other item; Focus Sash 64/117) | 75.0% |
| Kingambit (other item; Life Orb 42/77) | 57.8% |
| Maushold (other item; Chople Berry 15/21) | 26.5% |
| Blastoise@Blastoisinite | 25.4% |
| Indeedee-F (other item; Rocky Helmet 17/22) | 23.5% |
| Pawmot (other item; Focus Sash 8/10) | 4.9% |
| Baxcalibur@Baxcalibrite | 1.1% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Benjamin van Hoeckel, 73rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0078/teamlist)
  - [Nicholas Rottoli, 448th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0107/teamlist)
  - [Roberto Retico, 521st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0255/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Jules Büchler-Lecler, 895th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0161/teamlist)
  - [Daniel Soler Sanchez, 21st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0546/teamlist)
  - [Joan Garcia, 472nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1070/teamlist)
  - [Dani Pardo, 834th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0493/teamlist)
  - [Corey Okonowitz, 98th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0354/teamlist)
  - [horsea_tatsuomi, , 12 Sep 2026](https://pokepast.es/e328f6becb7a8d35)
  - [Ling, , 10 Sep 2026](https://pokepast.es/900d357c0ea74c31)
  - [Maksim Melnik, 467th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0940/teamlist)
  - [Drew Kendziora, 62nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0923/teamlist)
  - [Fletcher Dean, 815th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1033/teamlist)
  - [Jude Gerard Lee Wei Cong, 3rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0040/teamlist)
  - [Stanisław Piotrowski, 198th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0850/teamlist)
  - [Will Inabinet, 189th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1075/teamlist)
  - [Justin Miranda-Radbord, 324th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1001/teamlist)
  - [Zoe Anderson, 1068th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0194/teamlist)
  - [Andrew Krebs, Top 4, 13 Sep 2026](https://pokepast.es/a4768a9bbf6876de)
  - [Dane Bodamer, 863rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0475/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/81427a109e744097)
  - [Dom Mori, 757th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0006/teamlist)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)

#### Community 2 / Sub-community 3: Gengar@Gengarite / Kommo-o (3 primary teams, 3 distinct builds, top pair on 3/3)
- Megas on member teams: Gengar 3, Froslass 1, Metagross 1
- Top species by team share: Gengar 100%, Incineroar 100%, Kommo-o 100%, Rillaboom 100%, Kingambit 67%, Froslass 33%
- Token label: Gengar@Gengarite / Kommo-o
- Mode tags on primary teams: Snow 2, Perish Trap 1
- Primary teams: 3 (1.3% of the community's primary weight), hybrid teams: 5 (2.4%)
- Date range: 2026-09-10 to 2026-09-27
- Core pairs: Kommo-o (other item; Leftovers 9/10)+Gengar@Gengarite, Kingambit (other item; Life Orb 42/77)+Kommo-o (other item; Leftovers 9/10), Kingambit (other item; Life Orb 42/77)+Gengar@Gengarite, Kommo-o (other item; Leftovers 9/10)+Sinistcha (other item; Colbur Berry 28/62), Kommo-o (other item; Leftovers 9/10)+Delphox@Delphoxite, Rillaboom (other item; Miracle Seed 100/145)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 171/210)+Gengar@Gengarite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Gengar@Gengarite | 100.0% |
| Kommo-o (other item; Leftovers 9/10) | 100.0% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [Jordan Goggin, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0089/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Curtis Ridings, 128th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0184/teamlist)
  - [William Pye, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0229/teamlist)
  - [Thomas Schultz, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1054/teamlist)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): Sub-community 4: Absol@Absolite Z / Espathra / Goodra-Hisui (1 distinct build, 4 primary teams, top pair on 4/4)

#### Community 2 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Sneasler | Sneasler (other item; Focus Sash 64/117) | Sub-community 2: Setup (Mega Delphox, Mega Floette) | 117 | 2: 53 · 0: 38 · 1: 26 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 0: Setup (Mega Floette) · Incineroar | 20 | 0: 20 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 38 | 1: 26 · 0: 11 · 2: 1 · unassigned: 0 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 0: Setup (Mega Floette) · Incineroar | 20 | 0: 16 · 2: 3 · 1: 1 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 8/15) | Sub-community 0: Setup (Mega Floette) · Incineroar | 15 | 0: 9 · 2: 4 · 1: 2 · unassigned: 0 |
| Basculegion | Basculegion@Choice Scarf | Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 22 | 0: 15 · 1: 7 · unassigned: 0 |
| Basculegion | Basculegion (other item; Life Orb 9/12) | Sub-community 0: Setup (Mega Floette) · Incineroar | 12 | 0: 11 · 1: 1 · unassigned: 0 |

### Community 3: Psyspam
- Token label: Indeedee-F + Gardevoir/Armarouge
- Mode tags on primary teams: Psyspam 363, Tailwind 223, Trick Room 171, Sun 103, Setup 58, Rain 41, Snow 32, Screens 16, Sand 8
- Megas on primary teams: Gardevoir 193, Golisopod 82, Staraptor 62, Salamence 56, Camerupt 46, Glimmora 46, Metagross 45, Charizard-Y 36, Raichu-Y 24, Baxcalibur 21, Absol-Z 18, Pyroar 17, Gengar 16, Blastoise 14, Garchomp-Z 12, Blaziken 11, Mawile 9, Floette 8, Froslass 7, Lucario-Z 7, Dragonite 6, Lopunny 6, Scovillain 6, Tyranitar 6, Abomasnow 4, Delphox 4, Meowstic-F 4, Aerodactyl 3, Alakazam 3, Crabominable 3, Heracross 3, Houndoom 2, Malamar 2, Skarmory 2, Swampert 2, Aggron 1, Ampharos 1, Charizard-X 1, Dragalge 1, Drampa 1, Gallade 1, Glalie 1, Golurk 1, Gyarados 1, Meganium 1, Raichu-X 1, Sceptile 1, Scrafty 1, Slowbro 1
- Primary teams: 479 (primary share 16.2%), hybrid teams: 160 (hybrid share 5.5%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Araquanid+Kleavor, Klefki+Volcarona@Sitrus Berry, Kleavor+Whimsicott@Fairy Feather, Pyroar+Kommo-o@Life Orb, Hatterene+Incineroar@White Herb, Houndoom+Torkoal, Torkoal+Houndoom@Houndoominite, Camerupt+Incineroar@White Herb, Abomasnow+Camerupt, Abomasnow+Camerupt@Cameruptite, Camerupt+Abomasnow@Abomasite, Baxcalibur+Ninetales-Alola@Light Clay, Dragapult+Altaria@Haban Berry, Altaria+Milotic@Psychic Seed, Pyroar+Basculegion@Mystic Water, Pawmot+Staraptor@Choice Scarf, Klefki+Glimmora@Glimmoranite, Dragapult+Milotic@Psychic Seed, Camerupt+Hatterene, Hatterene+Camerupt@Cameruptite, Camerupt+Hatterene@Life Orb, Glimmora+Klefki@Light Clay, Klefki+Garchomp@Life Orb, Altaria+Dragapult@Life Orb, Altaria+Dragapult, Glimmora+Klefki, Gallade+Hatterene@Life Orb, Dragapult+Armarouge@Twisted Spoon, Pyroar+Whimsicott@Focus Sash, Metagross+Altaria@Haban Berry, Typhlosion-Hisui+Indeedee@Focus Sash, Hatterene+Indeedee-F@Psychic Seed, Gallade+Hatterene, Kommo-o+Pyroar, Kommo-o+Pyroar@Pyroarite, Empoleon+Ninetales-Alola, Volcarona+Klefki@Light Clay, Ninetales-Alola+Baxcalibur@Baxcalibrite, Pyroar+Whimsicott, Whimsicott+Pyroar@Pyroarite, Glimmora+Ninetales-Alola@Never-Melt Ice, Camerupt+Indeedee-F@Psychic Seed, Dragapult+Armarouge@Focus Sash, Typhlosion-Hisui+Basculegion@Mystic Water, Pawmot+Kingambit@Occa Berry, Altaria+Metagross@Metagrossite, Sirfetch’d+Hatterene@Life Orb, Whimsicott+Kommo-o@Life Orb, Metagross+Blaziken@Focus Sash, Altaria+Metagross, Blaziken+Kingambit@Occa Berry, Baxcalibur+Ninetales-Alola, Armarouge+Milotic@Psychic Seed, Pawmot+Politoed@Life Orb, Glimmora+Volcarona@Rocky Helmet, Klefki+Volcarona, Camerupt+Gallade, Gallade+Camerupt@Cameruptite, Torkoal+Blaziken@Blazikenite, Kommo-o+Whimsicott@Occa Berry, Hatterene+Sirfetch’d, Armarouge+Torkoal@Life Orb, Hatterene+Torkoal@Charcoal, Camerupt+Kingambit@Black Glasses, Torkoal+Hatterene@Life Orb, Hatterene+Armarouge@Twisted Spoon, Hatterene+Kingambit@Black Glasses, Glimmora+Whimsicott@Occa Berry, Altaria+Rillaboom@Eject Button, Hatterene+Torkoal, Typhlosion-Hisui+Whimsicott, Gardevoir+Pyroar, Gardevoir+Pyroar@Pyroarite, Pyroar+Gardevoir@Gardevoirite, Camerupt+Sirfetch’d, Sirfetch’d+Camerupt@Cameruptite, Whimsicott+Typhlosion-Hisui@Choice Scarf, Whimsicott+Basculegion@Mystic Water, Lopunny+Indeedee-F@Colbur Berry, Altaria+Indeedee-F@Rocky Helmet, Araquanid+Volcarona, Pyroar+Indeedee-F@Rocky Helmet, Baxcalibur+Volcarona@Rocky Helmet, Sirfetch’d+Incineroar@Chople Berry, Blaziken+Torkoal@Charcoal, Hatterene+Blaziken@Blazikenite, Sirfetch’d+Glimmora@Glimmoranite, Kommo-o+Basculegion@Mystic Water, Gardevoir+Indeedee-F@Colbur Berry, Typhlosion-Hisui+Glimmora@Glimmoranite, Blaziken+Glimmora@Focus Sash, Dragapult+Indeedee-F@Rocky Helmet, Armarouge+Dragapult@Life Orb, Whimsicott+Kleavor@Focus Sash, Blaziken+Torkoal, Gallade+Torkoal@Charcoal, Glimmora+Volcarona@Sitrus Berry, Torkoal+Armarouge@Life Orb, Typhlosion-Hisui+Whimsicott@Focus Sash, Metagross+Whimsicott@Fairy Feather, Metagross+Kleavor@Focus Sash, Pawmot+Baxcalibur@Baxcalibrite, Armarouge+Dragapult, Archaludon+Klefki@Light Clay, Gengar+Kommo-o@Leftovers, Gallade+Torkoal, Volcarona+Glimmora@Glimmoranite, Baxcalibur+Pawmot@Focus Sash, Rotom-Wash+Metagross@Metagrossite, Armarouge+Gallade, Gardevoir+Lopunny, Lopunny+Gardevoir@Gardevoirite, Gardevoir+Lopunny@Lopunnite, Metagross+Rotom-Wash, Kommo-o+Indeedee@Focus Sash, Gardevoir+Kommo-o@Life Orb, Mawile+Torkoal@Charcoal, Volcarona+Baxcalibur@Baxcalibrite, Hatterene+Glimmora@Focus Sash, Garchomp+Klefki@Light Clay, Baxcalibur+Volcarona@Grassy Seed, Armarouge+Indeedee-F@Rocky Helmet, Armarouge+Indeedee-F@Sitrus Berry, Torkoal+Farigiraf@Grassy Seed, Blaziken+Hatterene@Life Orb, Gardevoir+Rotom-Heat@Sitrus Berry, Torkoal+Indeedee-F@Psychic Seed, Gardevoir+Indeedee-F@Sitrus Berry, Mawile+Torkoal, Torkoal+Mawile@Mawilite, Camerupt+Farigiraf@Colbur Berry, Lucario+Basculegion@Focus Sash, Klefki+Archaludon@Leftovers, Sirfetch’d+Whimsicott@Focus Sash, Glimmora+Sirfetch’d, Gardevoir+Sneasler@Psychic Seed, Baxcalibur+Pawmot, Basculegion+Pelipper@Choice Scarf, Annihilape+Torkoal@Charcoal, Milotic+Altaria@Haban Berry, Indeedee-F+Delphox@Life Orb, Indeedee-F+Armarouge@Psychic Seed, Indeedee-F+Gallade@White Herb, Indeedee-F+Altaria@Haban Berry, Indeedee-F+Hatterene@Focus Sash, Blaziken+Hatterene, Ninetales-Alola+Kommo-o@Leftovers, Glimmora+Typhlosion-Hisui, Indeedee-F+Armarouge@Focus Sash, Archaludon+Klefki, Kleavor+Whimsicott, Hatterene+Indeedee-F, Indeedee-F+Hatterene@Life Orb, Indeedee-F+Armarouge@Life Orb, Glimmora+Talonflame, Metagross+Milotic@Psychic Seed, Gardevoir+Indeedee-F, Indeedee-F+Gardevoir@Gardevoirite, Abomasnow+Farigiraf, Farigiraf+Abomasnow@Abomasite, Indeedee-F+Milotic@Psychic Seed, Baxcalibur+Volcarona, Armarouge+Grapploct, Sirfetch’d+Whimsicott, Indeedee-F+Armarouge@Twisted Spoon, Camerupt+Farigiraf, Farigiraf+Camerupt@Cameruptite, Gengar+Armarouge@Focus Sash, Armarouge+Indeedee-F, Grapploct+Farigiraf@Sitrus Berry, Annihilape+Armarouge@Focus Sash, Glimmora+Volcarona, Annihilape+Torkoal, Camerupt+Farigiraf@Sitrus Berry, Whimsicott+Glimmora@Glimmoranite, Basculegion+Pyroar, Basculegion+Pyroar@Pyroarite, Talonflame+Glimmora@Glimmoranite, Garchomp+Klefki, Torkoal+Indeedee-F@Sitrus Berry, Indeedee+Typhlosion-Hisui, Glimmora+Kingambit@Occa Berry, Armarouge+Vanilluxe, Typhlosion-Hisui+Torkoal@Charcoal, Primarina+Blaziken@Blazikenite, Whimsicott+Garchomp@Sitrus Berry, Indeedee+Typhlosion-Hisui@Choice Scarf, Armarouge+Torkoal, Kleavor+Metagross@Metagrossite, Gardevoir+Indeedee-F@Rocky Helmet, Gardevoir+Armarouge@Life Orb, Araquanid+Golisopod@Golisopite, Araquanid+Golisopod, Glimmora+Whimsicott, Kommo-o+Rillaboom@Eject Button, Altaria+Gengar@Gengarite, Blastoise+Indeedee-F@Rocky Helmet, Alakazam+Indeedee-F, Kleavor+Metagross, Altaria+Gengar, Indeedee-F+Starmie, Indeedee-F+Starmie@Starminite, Rotom-Heat+Indeedee-F@Colbur Berry, Torkoal+Primarina@Life Orb, Indeedee-F+Incineroar@White Herb, Golisopod+Armarouge@Psychic Seed, Armarouge+Indeedee-F@Colbur Berry, Kommo-o+Gengar@Gengarite, Golisopod+Sirfetch’d@Leek, Annihilape+Armarouge@Life Orb, Gengar+Kommo-o, Whimsicott+Garchomp@Life Orb, Torkoal+Typhlosion-Hisui, Glimmora+Kommo-o@Life Orb, Gardevoir+Basculegion@Mystic Water, Gardevoir+Whimsicott@Occa Berry, Meganium+Indeedee-F@Rocky Helmet, Ninetales-Alola+Pawmot, Golisopod+Armarouge@Twisted Spoon, Gardevoir+Torkoal@Charcoal, Lucario+Basculegion@Life Orb, Armarouge+Torkoal@Charcoal, Gallade+Indeedee-F@Rocky Helmet, Indeedee-F+Alakazam@Alakazite, Lycanroc-Dusk+Basculegion@Life Orb, Staraptor+Armarouge@Twisted Spoon, Gardevoir+Torkoal, Torkoal+Gardevoir@Gardevoirite, Arcanine-Hisui+Altaria@Haban Berry, Kommo-o+Whimsicott, Indeedee-F+Dragapult@Life Orb, Glimmora+Hydreigon, Metagross+Hydreigon@Choice Scarf, Venusaur+Annihilape@Choice Scarf, Absol+Armarouge@Life Orb, Metagross+Indeedee@Choice Scarf, Whimsicott+Kingambit@Occa Berry, Milotic+Dragapult@Life Orb, Ninetales-Alola+Rillaboom@Occa Berry, Glimmora+Whimsicott@Focus Sash, Annihilape+Armarouge, Glimmora+Indeedee@Focus Sash, Scovillain+Basculegion@Life Orb, Blaziken+Ninetales-Alola, Baxcalibur+Basculegion@Life Orb, Metagross+Dragapult@Life Orb, Armarouge+Annihilape@Choice Scarf, Blaziken+Primarina, Hydreigon+Glimmora@Glimmoranite, Basculegion+Kommo-o@Life Orb, Gardevoir+Garchomp@Sitrus Berry, Swampert+Annihilape@Choice Scarf, Dragapult+Milotic, Armarouge+Hatterene, Torkoal+Incineroar@White Herb, Dragapult+Indeedee-F, Kommo-o+Ninetales-Alola, Annihilape+Venusaur@Focus Sash, Dragapult+Metagross@Metagrossite, Altaria+Milotic, Torkoal+Annihilape@Choice Scarf, Altaria+Indeedee-F, Farigiraf+Grapploct, Kleavor+Basculegion@Life Orb, Glimmora+Blaziken@Blazikenite, Indeedee-F+Pyroar, Indeedee-F+Pyroar@Pyroarite, Gallade+Indeedee-F, Armarouge+Hatterene@Life Orb, Talonflame+Metagross@Metagrossite, Golisopod+Kleavor@Choice Scarf, Dragapult+Metagross, Indeedee-F+Blastoise@Blastoisinite, Kommo-o+Glimmora@Glimmoranite, Staraptor+Armarouge@Focus Sash, Volcarona+Kingambit@Occa Berry, Kommo-o+Whimsicott@Focus Sash, Indeedee+Metagross@Metagrossite, Metagross+Talonflame, Indeedee-F+Torkoal@Life Orb, Sirfetch’d+Golisopod@Golisopite, Blaziken+Indeedee-F@Psychic Seed, Golisopod+Sirfetch’d, Volcarona+Garchomp@Garchompite Z, Camerupt+Indeedee-F, Indeedee-F+Camerupt@Cameruptite, Dragapult+Indeedee-F@Sitrus Berry, Milotic+Armarouge@Twisted Spoon, Blastoise+Indeedee-F, Staraptor+Dragapult@Life Orb, Indeedee+Metagross, Indeedee-F+Torkoal@Charcoal, Indeedee-F+Sneasler@Psychic Seed, Glimmora+Hydreigon@Choice Scarf, Hydreigon+Metagross@Metagrossite, Gengar+Dragapult@Life Orb, Indeedee-F+Torkoal, Armarouge+Indeedee-F@Psychic Seed, Primarina+Pawmot@Focus Sash, Armarouge+Sirfetch’d, Gardevoir+Rotom-Heat, Rotom-Heat+Gardevoir@Gardevoirite, Gardevoir+Aerodactyl@Focus Sash, Scovillain+Indeedee-F@Colbur Berry, Hatterene+Indeedee-F@Rocky Helmet, Hydreigon+Metagross, Absol+Armarouge, Armarouge+Absol@Absolite Z, Lucario+Armarouge@Focus Sash, Baxcalibur+Farigiraf@Colbur Berry, Blastoise+Indeedee-F@Colbur Berry, Indeedee-F+Lopunny, Indeedee-F+Lopunny@Lopunnite, Blaziken+Kingambit@Black Glasses, Arcanine-Hisui+Baxcalibur@Life Orb, Ninetales-Alola+Rillaboom@Life Orb, Dragapult+Staraptor@Staraptite, Gardevoir+Basculegion@Choice Scarf, Salamence+Klefki@Light Clay, Basculegion+Talonflame@Life Orb, Dragapult+Staraptor, Gardevoir+Typhlosion-Hisui, Typhlosion-Hisui+Gardevoir@Gardevoirite, Dragapult+Gengar@Gengarite, Indeedee-F+Talonflame@Life Orb, Dragapult+Gengar, Whimsicott+Glimmora@Focus Sash, Annihilape+Lucario@Lucarionite Z, Basculegion+Whimsicott@Fairy Feather, Farigiraf+Sirfetch’d@Leek, Altaria+Arcanine-Hisui, Armarouge+Sneasler@Psychic Seed, Annihilape+Lucario, Hatterene+Farigiraf@Sitrus Berry, Froslass+Blaziken@Blazikenite, Metagross+Armarouge@Twisted Spoon, Garchomp+Rotom-Wash, Camerupt+Blastoise@Blastoisinite, Pelipper+Basculegion@Choice Scarf, Farigiraf+Hatterene@Life Orb, Altaria+Arcanine-Hisui@Focus Sash, Annihilape+Swampert@Swampertite, Garchomp+Whimsicott@Occa Berry, Basculegion+Talonflame@Focus Sash, Gardevoir+Kommo-o, Kommo-o+Gardevoir@Gardevoirite, Ninetales-Alola+Glimmora@Glimmoranite, Annihilape+Venusaur, Dragapult+Glimmora@Glimmoranite, Annihilape+Gardevoir, Annihilape+Gardevoir@Gardevoirite, Kingambit+Volcarona@Focus Sash, Armarouge+Gardevoir, Armarouge+Gardevoir@Gardevoirite, Pawmot+Primarina, Pawmot+Delphox@Delphoxite, Glimmora+Basculegion@Mystic Water, Abomasnow+Incineroar, Incineroar+Abomasnow@Abomasite, Farigiraf+Hatterene, Basculegion+Meganium, Basculegion+Meganium@Meganiumite, Blastoise+Camerupt, Blastoise+Camerupt@Cameruptite, Typhlosion-Hisui+Garchomp@Garchompite Z, Glimmora+Volcarona@Grassy Seed, Annihilape+Rillaboom@Life Orb, Swampert+Volcarona@Rocky Helmet, Basculegion+Whimsicott@Focus Sash, Basculegion+Kleavor@Focus Sash, Sirfetch’d+Farigiraf@Sitrus Berry, Whimsicott+Indeedee-F@Rocky Helmet, Baxcalibur+Milotic@Leftovers, Dragapult+Volcarona@Rocky Helmet, Basculegion+Volcarona@Leftovers, Charizard+Whimsicott@Focus Sash, Pawmot+Lucario@Lucarionite Z, Glimmora+Garchomp@Life Orb, Whimsicott+Garchomp@Choice Scarf, Blastoise+Armarouge@Life Orb, Gardevoir+Whimsicott@Focus Sash, Lucario+Pawmot, Indeedee-F+Meowstic-F, Indeedee-F+Meowstic-F@Meowsticite, Maushold+Indeedee-F@Rocky Helmet, Armarouge+Mawile, Armarouge+Mawile@Mawilite, Delphox+Pawmot, Kleavor+Volcarona, Metagross+Indeedee-F@Sitrus Berry, Annihilape+Swampert, Kommo-o+Incineroar@Chople Berry, Baxcalibur+Sneasler@Focus Sash, Indeedee-F+Kommo-o@Life Orb, Basculegion+Whimsicott, Grapploct+Golisopod@Golisopite, Torkoal+Kingambit@Focus Sash, Golisopod+Grapploct, Annihilape+Indeedee-F@Colbur Berry, Blaziken+Metagross@Metagrossite, Kommo-o+Indeedee-F@Rocky Helmet, Gardevoir+Whimsicott, Whimsicott+Gardevoir@Gardevoirite, Gardevoir+Armarouge@Focus Sash, Talonflame+Garchomp@Life Orb, Baxcalibur+Glimmora@Glimmoranite, Basculegion+Kingambit@Occa Berry, Whimsicott+Basculegion@Life Orb, Gardevoir+Indeedee-F@Psychic Seed, Kingambit+Torkoal@Life Orb, Torkoal+Indeedee-F@Colbur Berry, Sinistcha+Pawmot@Focus Sash, Kommo-o+Rillaboom@Occa Berry, Blaziken+Metagross, Charizard+Whimsicott@Occa Berry, Metagross+Milotic, Milotic+Armarouge@Focus Sash, Milotic+Metagross@Metagrossite, Glimmora+Kommo-o, Glimmora+Baxcalibur@Baxcalibrite, Volcarona+Basculegion@Life Orb, Metagross+Sneasler@Psychic Seed, Camerupt+Rillaboom@Life Orb, Blastoise+Indeedee-F@Psychic Seed, Klefki+Salamence@Salamencite, Klefki+Salamence, Garchomp+Volcarona@Grassy Seed, Gallade+Gardevoir, Gallade+Gardevoir@Gardevoirite, Gardevoir+Talonflame, Talonflame+Gardevoir@Gardevoirite, Charizard+Whimsicott, Whimsicott+Charizard@Charizardite Y, Typhlosion-Hisui+Sneasler@Psychic Seed, Sinistcha+Kommo-o@Leftovers, Blaziken+Glimmora, Glimmora+Garchomp@Choice Scarf, Indeedee+Kommo-o@Life Orb, Kommo-o+Politoed@Sitrus Berry, Grapploct+Indeedee-F, Ninetales-Alola+Volcarona@Grassy Seed, Torkoal+Farigiraf@Colbur Berry, Blaziken+Rillaboom@Life Orb, Politoed+Kommo-o@Leftovers, Primarina+Torkoal@Charcoal, Basculegion+Gardevoir, Basculegion+Gardevoir@Gardevoirite, Kingambit+Blaziken@Blazikenite, Farigiraf+Sirfetch’d, Indeedee-F+Rotom-Heat@Sitrus Berry, Basculegion+Lucario@Lucarionite Z, Whimsicott+Armarouge@Twisted Spoon, Farigiraf+Torkoal@Charcoal, Armarouge+Staraptor, Hatterene+Indeedee-F@Colbur Berry, Armarouge+Staraptor@Staraptite, Pawmot+Glimmora@Glimmoranite, Basculegion+Lucario, Ninetales-Alola+Metagross@Metagrossite, Farigiraf+Torkoal, Kleavor+Kingambit@Chople Berry, Metagross+Volcarona@Grassy Seed, Gardevoir+Venusaur@Focus Sash, Indeedee-F+Sinistcha@Coba Berry, Basculegion+Lycanroc-Dusk@Focus Sash, Blaziken+Kingambit@Focus Sash, Glimmora+Ninetales-Alola, Gardevoir+Annihilape@Choice Scarf, Metagross+Ninetales-Alola, Talonflame+Tyranitar, Garchomp+Whimsicott@Focus Sash, Basculegion+Whimsicott@Occa Berry, Indeedee-F+Meowscarada, Kingambit+Volcarona@Rocky Helmet, Hydreigon+Milotic@Sitrus Berry, Absol+Indeedee-F@Rocky Helmet, Whimsicott+Indeedee@Focus Sash, Ninetales-Alola+Delphox@Delphoxite, Pelipper+Annihilape@Choice Scarf, Garchomp+Volcarona@Rocky Helmet, Garchomp+Whimsicott, Glimmora+Dragapult@Life Orb, Basculegion+Volcarona@Grassy Seed, Delphox+Kommo-o@Leftovers, Basculegion+Pelipper@Focus Sash, Primarina+Torkoal, Metagross+Indeedee-F@Rocky Helmet, Torkoal+Indeedee-F@Rocky Helmet, Pawmot+Sinistcha, Hatterene+Golisopod@Golisopite, Basculegion+Lycanroc-Dusk, Golisopod+Hatterene, Metagross+Garchomp@Garchompite Z, Dragonite+Metagross@Metagrossite, Froslass+Pawmot@Focus Sash, Pawmot+Basculegion@Life Orb, Kommo-o+Incineroar@Passho Berry, Delphox+Ninetales-Alola, Raichu+Vanilluxe@Choice Scarf, Metagross+Milotic@Leftovers, Whimsicott+Kingambit@Chople Berry, Garchomp+Kleavor@Focus Sash, Armarouge+Camerupt, Armarouge+Camerupt@Cameruptite, Basculegion+Typhlosion-Hisui@Choice Scarf, Basculegion+Scovillain@Scovillainite, Armarouge+Blastoise@Blastoisinite, Glimmora+Basculegion@Life Orb, Metagross+Milotic@Sitrus Berry, Incineroar+Lopunny, Incineroar+Lopunny@Lopunnite, Dragonite+Metagross, Gardevoir+Kingambit@Occa Berry, Indeedee-F+Aerodactyl@Focus Sash, Basculegion+Glimmora@Glimmoranite, Volcarona+Basculegion@Choice Scarf, Delphox+Pawmot@Focus Sash, Basculegion+Rillaboom@Expert Belt, Annihilape+Indeedee-F@Rocky Helmet, Indeedee-F+Typhlosion-Hisui, Archaludon+Volcarona@Sitrus Berry, Archaludon+Annihilape@Choice Scarf, Garchomp+Volcarona, Hydreigon+Pelipper@Sitrus Berry, Baxcalibur+Glimmora, Dragapult+Glimmora, Basculegion+Talonflame, Venusaur+Torkoal@Charcoal, Basculegion+Scovillain, Golisopod+Basculegion@Choice Scarf, Armarouge+Blastoise, Garchomp+Glimmora@Focus Sash, Ninetales-Alola+Volcarona, Lucario+Dragapult@Life Orb, Basculegion+Glimmora, Basculegion+Kingambit@Chople Berry, Camerupt+Incineroar, Incineroar+Camerupt@Cameruptite, Floette-Eternal+Whimsicott@Focus Sash, Blaziken+Froslass, Blaziken+Froslass@Froslassite, Incineroar+Ninetales-Alola@Focus Sash, Annihilape+Pelipper@Sitrus Berry, Farigiraf+Blaziken@Blazikenite, Golisopod+Hatterene@Life Orb, Garchomp+Typhlosion-Hisui@Choice Scarf, Kingambit+Basculegion@Life Orb, Baxcalibur+Incineroar@Chople Berry, Empoleon+Garchomp, Kingambit+Whimsicott@Fairy Feather, Golisopod+Indeedee-F@Colbur Berry, Mawile+Indeedee-F@Colbur Berry, Camerupt+Kingambit, Kingambit+Camerupt@Cameruptite, Metagross+Dragonite@Dragoninite, Armarouge+Gengar@Gengarite, Glimmora+Kingambit@Focus Sash, Incineroar+Kommo-o@Leftovers, Armarouge+Gengar, Annihilape+Sneasler@Psychic Seed, Absol+Indeedee-F, Indeedee-F+Absol@Absolite Z, Sinistcha+Baxcalibur@Baxcalibrite, Garchomp+Talonflame, Incineroar+Sirfetch’d@Leek, Annihilape+Indeedee-F, Indeedee-F+Basculegion@Mystic Water, Indeedee-F+Basculegion@Focus Sash, Basculegion+Volcarona, Kingambit+Hatterene@Life Orb, Altaria+Sneasler@Grassy Seed, Froslass+Volcarona@Grassy Seed, Dragapult+Lucario@Lucarionite Z, Farigiraf+Indeedee-F@Psychic Seed, Ninetales-Alola+Gengar@Gengarite, Gardevoir+Sinistcha@Sitrus Berry, Torkoal+Venusaur, Blaziken+Kingambit, Dragapult+Lucario, Charizard+Annihilape@Choice Scarf, Kingambit+Whimsicott@Occa Berry, Gengar+Ninetales-Alola, Primarina+Volcarona@Grassy Seed, Indeedee-F+Typhlosion-Hisui@Choice Scarf, Rotom-Wash+Incineroar@Sitrus Berry, Talonflame+Indeedee-F@Rocky Helmet, Indeedee-F+Meganium, Indeedee-F+Meganium@Meganiumite, Froslass+Pawmot, Pawmot+Froslass@Froslassite, Hatterene+Kingambit, Garchomp+Volcarona@Sitrus Berry, Armarouge+Golisopod@Golisopite, Armarouge+Milotic, Milotic+Hydreigon@Choice Scarf, Armarouge+Golisopod, Gardevoir+Venusaur, Venusaur+Gardevoir@Gardevoirite, Pelipper+Indeedee-F@Colbur Berry, Incineroar+Volcarona@Grassy Seed, Indeedee-F+Kommo-o, Indeedee-F+Maushold@Chople Berry, Kleavor+Golisopod@Golisopite, Golisopod+Kleavor, Basculegion+Typhlosion-Hisui, Floette-Eternal+Basculegion@Focus Sash, Glimmora+Hatterene@Life Orb, Kommo-o+Indeedee-F@Psychic Seed, Pawmot+Metagross@Metagrossite, Indeedee-F+Whimsicott@Focus Sash, Raichu+Talonflame@Focus Sash, Kommo-o+Politoed, Rillaboom+Volcarona@Grassy Seed, Rillaboom+Empoleon@Leftovers, Venusaur+Indeedee-F@Colbur Berry, Politoed+Pawmot@Focus Sash, Basculegion+Baxcalibur@Baxcalibrite, Dragonite+Basculegion@Life Orb, Kingambit+Ninetales-Alola@Choice Scarf, Vanilluxe+Raichu@Raichunite Y, Golisopod+Annihilape@Choice Scarf, Staraptor+Hydreigon@Choice Scarf, Metagross+Pawmot, Indeedee-F+Whimsicott@Occa Berry, Gardevoir+Blastoise@Blastoisinite, Swampert+Kommo-o@Leftovers, Kingambit+Basculegion@Mystic Water, Kingambit+Indeedee-F@Sitrus Berry, Kingambit+Torkoal, Raichu+Vanilluxe, Indeedee-F+Kingambit@Occa Berry, Baxcalibur+Metagross, Ninetales-Alola+Rillaboom@Sitrus Berry, Basculegion+Pelipper, Farigiraf+Pawmot@Focus Sash, Staraptor+Glimmora@Focus Sash, Blaziken+Farigiraf@Sitrus Berry, Vanilluxe+Incineroar@Sitrus Berry, Volcarona+Kingambit@Focus Sash, Sneasler+Volcarona@Focus Sash, Blaziken+Farigiraf, Incineroar+Sirfetch’d, Glimmora+Pawmot, Basculegion+Indeedee-F@Colbur Berry, Garchomp+Typhlosion-Hisui, Metagross+Arcanine-Hisui@Focus Sash, Kingambit+Torkoal@Charcoal, Gardevoir+Pelipper@Focus Sash, Glimmora+Basculegion@Choice Scarf, Baxcalibur+Sneasler@White Herb, Indeedee+Kommo-o, Blastoise+Gardevoir, Blastoise+Gardevoir@Gardevoirite, Pawmot+Farigiraf@Sitrus Berry, Garchomp+Glimmora, Arcanine-Hisui+Metagross@Metagrossite, Dragapult+Volcarona, Torkoal+Garchomp@Garchompite Z, Indeedee-F+Vanilluxe, Indeedee-F+Scovillain, Staraptor+Whimsicott@Occa Berry, Kingambit+Ninetales-Alola@Never-Melt Ice, Basculegion+Volcarona@Rocky Helmet, Basculegion+Garchomp@Sitrus Berry, Gardevoir+Basculegion@Focus Sash, Basculegion+Glimmora@Focus Sash, Torkoal+Farigiraf@Sitrus Berry, Floette-Eternal+Volcarona@Grassy Seed, Volcarona+Metagross@Metagrossite, Arcanine-Hisui+Metagross, Sneasler+Indeedee-F@Colbur Berry, Camerupt+Indeedee-F@Rocky Helmet, Torkoal+Armarouge@Focus Sash, Kommo-o+Gholdengo@Grassy Seed, Volcarona+Incineroar@Sitrus Berry, Talonflame+Garchomp@Garchompite Z, Indeedee-F+Basculegion@Choice Scarf, Metagross+Indeedee@Focus Sash, Volcarona+Rillaboom@Miracle Seed, Indeedee-F+Rotom-Wash, Talonflame+Tyranitar@Tyranitarite, Basculegion+Indeedee@Focus Sash, Farigiraf+Pawmot, Gardevoir+Volcarona@Sitrus Berry, Indeedee-F+Annihilape@Choice Scarf, Golisopod+Indeedee-F@Sitrus Berry, Milotic+Baxcalibur@Baxcalibrite, Metagross+Volcarona, Indeedee-F+Metagross@Metagrossite, Sinistcha+Indeedee-F@Rocky Helmet, Staraptor+Indeedee-F@Rocky Helmet, Torkoal+Kingambit@Black Glasses, Annihilape+Pelipper@Focus Sash, Baxcalibur+Sinistcha, Annihilape+Pelipper, Whimsicott+Floette-Eternal@Floettite, Archaludon+Basculegion@Choice Scarf, Indeedee-F+Whimsicott, Indeedee-F+Metagross, Milotic+Indeedee-F@Rocky Helmet, Glimmora+Hatterene, Floette-Eternal+Whimsicott, Basculegion+Volcarona@Sitrus Berry, Indeedee-F+Kingambit@Black Glasses, Glimmora+Torkoal, Indeedee-F+Rotom-Heat, Ninetales-Alola+Sneasler@Focus Sash, Golisopod+Dragapult@Life Orb, Gengar+Indeedee-F@Rocky Helmet, Hatterene+Incineroar, Basculegion+Kingambit@Black Glasses, Basculegion+Baxcalibur, Garchomp+Glimmora@Glimmoranite, Dragapult+Golisopod@Golisopite, Gardevoir+Sneasler, Sneasler+Gardevoir@Gardevoirite, Annihilape+Charizard@Charizardite Y, Dragapult+Golisopod, Indeedee-F+Garchomp@Sitrus Berry, Absol+Indeedee-F@Colbur Berry, Raichu+Volcarona@Grassy Seed, Froslass+Volcarona, Volcarona+Froslass@Froslassite, Incineroar+Hatterene@Life Orb, Metagross+Pawmot@Focus Sash, Indeedee-F+Scovillain@Scovillainite, Delphox+Indeedee-F@Rocky Helmet, Indeedee-F+Pelipper@Focus Sash, Indeedee+Glimmora@Glimmoranite, Annihilape+Golisopod@Golisopite, Golisopod+Pawmot@Focus Sash, Raichu+Blaziken@Focus Sash, Annihilape+Charizard, Annihilape+Golisopod, Torkoal+Kingambit@Chople Berry, Glimmora+Kingambit, Baxcalibur+Froslass, Baxcalibur+Froslass@Froslassite, Kingambit+Glimmora@Glimmoranite, Pawmot+Politoed, Kingambit+Glimmora@Focus Sash, Annihilape+Archaludon, Golisopod+Armarouge@Life Orb, Sneasler+Indeedee-F@Sitrus Berry, Metagross+Rillaboom@Life Orb, Rillaboom+Volcarona@Rocky Helmet, Baxcalibur+Rillaboom@Sitrus Berry, Volcarona+Sneasler@White Herb, Basculegion+Pawmot@Focus Sash, Blaziken+Milotic@Leftovers, Garchomp+Metagross@Metagrossite, Pawmot+Garchomp@Garchompite Z, Basculegion+Kleavor, Altaria+Incineroar@Sitrus Berry, Blaziken+Basculegion@Life Orb, Indeedee-F+Sinistcha@Sitrus Berry, Annihilape+Archaludon@Leftovers, Baxcalibur+Milotic, Indeedee-F+Glimmora@Focus Sash, Basculegion+Kommo-o, Indeedee-F+Toxtricity, Golisopod+Indeedee-F@Psychic Seed, Rillaboom+Volcarona, Venusaur+Indeedee-F@Psychic Seed, Garchomp+Metagross, Basculegion+Rillaboom@Sitrus Berry, Armarouge+Lucario@Lucarionite Z, Torkoal+Whimsicott@Focus Sash, Indeedee+Kommo-o@Leftovers, Glimmora+Kingambit@Life Orb, Garchomp+Kleavor, Hydreigon+Sneasler@Psychic Seed, Armarouge+Kingambit@Focus Sash, Kingambit+Indeedee-F@Psychic Seed, Volcarona+Dragapult@Life Orb, Basculegion+Pawmot, Blaziken+Indeedee-F, Milotic+Indeedee-F@Sitrus Berry, Venusaur+Basculegion@Choice Scarf, Indeedee-F+Golisopod@Golisopite, Basculegion+Garchomp@Garchompite Z, Kommo-o+Sinistcha, Indeedee-F+Mawile, Indeedee-F+Mawile@Mawilite, Golisopod+Indeedee-F, Armarouge+Lucario, Froslass+Kommo-o@Leftovers, Blaziken+Kingambit@Chople Berry, Incineroar+Basculegion@Focus Sash, Kingambit+Kommo-o@Leftovers, Indeedee-F+Blaziken@Blazikenite, Talonflame+Basculegion@Choice Scarf, Whimsicott+Staraptor@Staraptite, Basculegion+Aerodactyl@Focus Sash, Whimsicott+Incineroar@Rocky Helmet, Kingambit+Kleavor@Focus Sash, Hydreigon+Milotic, Salamence+Basculegion@Life Orb, Corviknight+Indeedee-F@Colbur Berry, Gardevoir+Kingambit@Chople Berry, Baxcalibur+Metagross@Metagrossite, Staraptor+Whimsicott, Camerupt+Golisopod@Golisopite, Pawmot+Volcarona, Froslass+Basculegion@Life Orb, Kingambit+Whimsicott, Camerupt+Golisopod, Golisopod+Camerupt@Cameruptite, Basculegion+Kingambit, Arcanine-Hisui+Hydreigon@Choice Scarf, Torkoal+Sneasler@Psychic Seed, Sneasler+Volcarona@Leftovers, Hydreigon+Milotic@Leftovers, Glimmora+Pawmot@Focus Sash, Hydreigon+Indeedee, Floette-Eternal+Basculegion@Life Orb, Lucario+Indeedee-F@Colbur Berry, Raichu+Kleavor@Focus Sash, Pawmot+Incineroar@Sitrus Berry, Floette-Eternal+Basculegion@Choice Scarf, Glimmora+Torkoal@Charcoal, Indeedee-F+Maushold, Indeedee-F+Sinistcha@Occa Berry, Kingambit+Basculegion@Focus Sash, Glimmora+Kingambit@Chople Berry, Basculegion+Rotom-Heat, Indeedee-F+Talonflame, Pawmot+Golisopod@Golisopite, Volcarona+Rillaboom@Life Orb, Golisopod+Pawmot, Sneasler+Baxcalibur@Baxcalibrite, Basculegion+Sneasler@Psychic Seed, Sylveon+Basculegion@Life Orb, Delphox+Kommo-o, Incineroar+Kommo-o, Salamence+Volcarona@Sitrus Berry, Basculegion+Floette-Eternal@Floettite, Basculegion+Floette-Eternal, Garchomp+Basculegion@Life Orb, Basculegion+Sableye, Hydreigon+Charizard@Charizardite Y, Blaziken+Milotic, Milotic+Blaziken@Blazikenite, Indeedee-F+Venusaur@Focus Sash, Metagross+Armarouge@Life Orb, Staraptor+Whimsicott@Focus Sash, Kommo-o+Kingambit@Chople Berry, Metagross+Sneasler@White Herb, Volcarona+Floette-Eternal@Floettite, Rillaboom+Baxcalibur@Baxcalibrite, Incineroar+Indeedee-F@Psychic Seed, Rillaboom+Ninetales-Alola@Choice Scarf, Charizard+Hydreigon, Floette-Eternal+Volcarona, Kingambit+Armarouge@Life Orb, Basculegion+Indeedee-F, Basculegion+Indeedee-F@Sitrus Berry, Incineroar+Rotom-Wash, Lucario+Basculegion@Choice Scarf, Kommo-o+Delphox@Delphoxite, Kleavor+Charizard@Charizardite Y, Armarouge+Metagross@Metagrossite, Metagross+Sneasler, Basculegion+Blaziken@Blazikenite, Sneasler+Metagross@Metagrossite, Tyranitar+Armarouge@Life Orb, Kommo-o+Metagross, Hydreigon+Staraptor@Staraptite, Charizard+Indeedee-F@Sitrus Berry, Raichu+Ninetales-Alola@Light Clay, Indeedee-F+Venusaur, Charizard+Kleavor, Glimmora+Indeedee, Sneasler+Armarouge@Life Orb, Gardevoir+Kommo-o@Leftovers, Excadrill+Armarouge@Life Orb, Whimsicott+Incineroar@Chople Berry, Blaziken+Indeedee-F@Colbur Berry, Incineroar+Volcarona, Ninetales-Alola+Kingambit@Chople Berry, Primarina+Metagross@Metagrossite, Sneasler+Basculegion@Life Orb, Basculegion+Dragonite@Dragoninite, Armarouge+Metagross, Basculegion+Dragonite, Rillaboom+Ninetales-Alola@Focus Sash, Hydreigon+Staraptor, Raichu+Volcarona, Indeedee-F+Kommo-o@Leftovers, Gardevoir+Garchomp@Life Orb, Basculegion+Indeedee-F@Rocky Helmet, Kingambit+Whimsicott@Focus Sash, Rillaboom+Kommo-o@Leftovers, Rillaboom+Basculegion@Life Orb, Baxcalibur+Rillaboom, Rillaboom+Ninetales-Alola@Light Clay, Rillaboom+Talonflame@Focus Sash, Ninetales-Alola+Rillaboom
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Indeedee-F | 77.9% | follow-me 99.4%, terrain-setter 99.4%, priority-blocker 99.4%, helping-hand 85.8%, trick-room-setter 79.9%, disruption 8.3%, spa-drop 2.5%, fake-out 1.5%, status 0.7%, screens 0.7% |
| Gardevoir | 38.2% | mega-attacker 99.4%, trick-room-setter 35.0%, setup 14.9%, spa-drop 13.3%, disruption 3.4%, priority-attack 0.6% |
| Armarouge | 32.7% | wide-guard 56.4%, trick-room-setter 49.1%, trick-room-abuser 24.2%, setup 2.3%, weather-setter 1.7%, ally-switch 0.7%, helping-hand 0.7% |
| Basculegion | 29.0% | priority-attack 93.7%, pivot 43.1%, speed-drop 1.6%, weather-setter 0.2%, setup 0.1% |
| Whimsicott | 16.4% | tailwind 100.0%, prankster 99.5%, disruption 64.0%, weather-setter 16.1%, helping-hand 5.7%, screens 5.1%, terrain-setter 2.3%, trick-room-setter 1.5%, speed-drop 0.5% |
| Volcarona | 13.4% | setup 54.1%, rage-powder 49.0%, spa-drop 43.9%, tailwind 27.4%, status 1.2% |
| Glimmora | 13.2% | mega-attacker 71.6% |
| Torkoal | 13.0% | weather-setter 100.0%, trick-room-abuser 95.3%, helping-hand 29.5%, status 1.0% |
| Dragapult | 13.0% | status 84.0%, screens 4.5%, disruption 4.2%, pivot 1.9% |
| Hatterene | 11.7% | trick-room-setter 100.0%, trick-room-abuser 97.9%, spa-drop 6.4% |
| Camerupt | 10.4% | mega-attacker 100.0%, trick-room-abuser 97.1% |
| Kommo-o | 9.9% | setup 67.7%, priority-attack 2.9% |
| Metagross | 9.5% | mega-attacker 97.3%, setup 13.0%, priority-attack 8.3%, trick-room-abuser 0.6%, speed-drop 0.6% |
| Baxcalibur | 4.1% | priority-attack 86.9%, mega-attacker 82.3%, setup 52.7% |
| Pyroar | 3.9% | mega-attacker 100.0%, spa-drop 21.1%, status 5.3%, disruption 5.3% |
| Ninetales-Alola | 3.8% | weather-setter 100.0%, screens 61.2%, speed-drop 33.4%, disruption 31.9%, ally-boost 5.4%, helping-hand 2.5%, terrain-setter 2.2% |
| Annihilape | 3.8% | pivot 32.5%, speed-drop 15.3%, setup 13.6%, ally-boost 4.9%, disruption 1.5%, weather-setter 1.2% |
| Blaziken | 3.2% | mega-attacker 67.4%, ally-boost 18.7%, setup 2.5%, priority-attack 1.8% |
| Typhlosion-Hisui | 2.8% | none |
| Kleavor | 2.8% | tailwind 22.1%, pivot 20.1% |
| Pawmot | 2.8% | fake-out 63.9%, ally-boost 20.3%, priority-attack 4.2%, weather-setter 2.5%, disruption 1.8%, pivot 1.2%, speed-drop 1.1% |
| Altaria | 2.4% | status 73.4%, tailwind 67.0%, perish-song 26.1%, mega-attacker 21.8%, setup 4.8% |
| Gallade | 2.2% | trick-room-setter 61.4%, wide-guard 60.9%, mega-attacker 14.2%, setup 8.3% |
| Talonflame | 2.2% | tailwind 100.0%, gale-wings 100.0%, status 16.9%, quick-guard 8.5%, priority-blocker 8.5%, disruption 6.5%, weather-setter 2.7% |
| Hydreigon | 2.0% | spa-drop 67.8%, tailwind 7.8%, pivot 4.7%, disruption 3.2% |
| Klefki | 1.6% | prankster 100.0%, weather-setter 83.6%, screens 83.6%, speed-drop 27.7%, setup 16.4%, trick-room-setter 5.6% |
| Sirfetch’d | 1.3% | trick-room-abuser 31.6%, priority-attack 5.5%, quick-guard 5.0%, priority-blocker 5.0% |
| Lopunny | 1.3% | mega-attacker 100.0%, fake-out 74.6%, disruption 50.8% |
| Empoleon | 1.1% | priority-attack 18.9%, trick-room-abuser 16.9%, status 7.8%, speed-drop 5.5% |
| Rotom-Wash | 1.1% | status 84.8%, speed-drop 39.3%, pivot 30.4%, screens 15.2%, weather-setter 8.9% |
| Grapploct | 1.0% | trick-room-abuser 57.9%, priority-attack 52.8%, ally-boost 8.7%, disruption 8.7%, setup 7.1% |
| Meowscarada | 0.9% | pivot 42.6%, priority-attack 12.2% |
| Abomasnow | 0.9% | weather-setter 100.0%, trick-room-abuser 100.0%, mega-attacker 100.0%, screens 41.3%, priority-attack 23.5% |
| Araquanid | 0.9% | wide-guard 100.0%, trick-room-abuser 36.9%, weather-setter 18.5%, speed-drop 18.5% |
| Vanilluxe | 0.8% | weather-setter 100.0%, speed-drop 69.8%, priority-attack 19.9%, screens 16.6% |
| Alakazam | 0.8% | mega-attacker 77.6%, setup 43.8%, disruption 40.4% |
| Houndoom | 0.4% | mega-attacker 100.0%, setup 29.3%, status 29.3% |
| Starmie | 0.0% | mega-attacker 100.0%, setup 16.4%, priority-attack 16.4% |
- Representative teams (primary teams with the highest score for this community):
  - [Krishna Rajesh, 465th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0754/teamlist)
  - [Stefan Böder, 727th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0110/teamlist)
  - [Sascha Schaub, 958th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0525/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Mantrel Whitaker, 239th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0932/teamlist)
  - [Paris Davis-Thompson, 531st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0382/teamlist)
  - [Evan Venhorst, 260th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0203/teamlist)
  - [Nils Güttler, 1054th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0026/teamlist)
  - [Katharina Wenzig, 1080th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0317/teamlist)
  - [Wolfe Glick, Top 16, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/Ki07vssP2mbfd4C1zgZz)
  - [Tevon Knight, 225th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1051/teamlist)
  - [Noah Gardner, 318th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0741/teamlist)
  - [Zachary Mnich, 611th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0031/teamlist)
  - [May Yu, 689th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0698/teamlist)
  - [Rowan Hall, 852nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1062/teamlist)
  - [Kyle Dobay, 883rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0081/teamlist)
  - [Jacob Mortenson, 96th, 21 Sep 2026](https://pokepast.es/8cc6b2b7139e9db9)
  - [Jacob Mortenson, 96th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0494/teamlist)
  - [Kingston Jones, 607th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0249/teamlist)
  - [Aram Reichardt, 819th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0598/teamlist)
  - [James Baek, 76th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0130/teamlist)
  - [Daniel Pioppi, , 14 Sep 2026](https://pokepast.es/9cc929d200baed42)
  - [Yuna Djelouat, 453rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0053/teamlist)
  - [James Evans, 50th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0634/teamlist)
  - [Octavio Barajas, 125th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1063/teamlist)
  - [Davide Carrer, 104th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0335/teamlist)
  - [Jonathan Pelletier, 605th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0263/teamlist)
  - [John Brotherston, 412th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0322/teamlist)
  - [Cedric Teichert, 1018th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0975/teamlist)
  - [Felix Miguel Andres, 504th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0629/teamlist)
  - [Diana Clark, 210th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0454/teamlist)
  - [Jack Mountain, 784th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0385/teamlist)
  - [Steven Oehler, 831st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0149/teamlist)
  - [Daniel Tautz, 49th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0397/teamlist)
  - [Michael Appelgate, 871st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0629/teamlist)
  - [Gabriel MonteLeon, 549th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0219/teamlist)
  - [Julian Wanis, 733rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1003/teamlist)
  - [Ethan Rauchwerk, 198th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0860/teamlist)
  - [Miguel Ángel Caño Bosque, 1057th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0243/teamlist)
  - [Liam Hogwood, 279th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0198/teamlist)
  - [Swaroop B, 84th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0273/teamlist)
  - [Zach Braun, 809th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0103/teamlist)
  - [Eli Shinn, 358th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0489/teamlist)
  - [Calvin Tobias, 222nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0607/teamlist)
  - [Nicholas Morales, 372nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0135/teamlist)
  - [Tommy O'Hara, 613th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0296/teamlist)
  - [Younes Lakhnati, 280th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0278/teamlist)
  - [Marcel Jenke, 736th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0356/teamlist)
  - [Gabriel Buchta, 1038th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1094/teamlist)
  - [Harriet Day, 822nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1062/teamlist)
  - [James Watts, 283rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0013/teamlist)
  - [kimkomsu, , 10 Sep 2026](https://pokepast.es/60002baa327ce677)
  - [etc25269248, , 13 Sep 2026](https://pokepast.es/7664efb6099c8d98)
  - [Sahil, , 9 Sep 2026](https://pokepast.es/b70d72f44626b60d)
  - [Dorian Luckie, 802nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0459/teamlist)
  - [Sarapoke0914, , 11 Sep 2026](https://pokepast.es/d4087f1527d4e0bc)
  - [humidori, , 16 Sep 2026](https://pokepast.es/472a153c121df28d)
  - [Jan-Philipp Schmitz, 905th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0555/teamlist)
  - [Demitrios Kaguras, 220th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1006/teamlist)
  - [Basil Hawley, 228th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0551/teamlist)
  - [Morgan Carter, 900th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1021/teamlist)
  - [Giovanni Piscitelli, Top 8, 20 Sep 2026](https://pokepast.es/ffe1c04c186b2453)
  - [Kamal Saab, 752nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1074/teamlist)
  - [albin jepping, 817th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0211/teamlist)
  - [Magnus Wallgren, 638th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0185/teamlist)
  - [Motochika Nabeshima, , 13 Sep 2026](https://pokepast.es/8ba4c9c260b8e9ae)
  - [Steven Van, 495th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0200/teamlist)
  - [David Mackinnon, 283rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0902/teamlist)
  - [Nikita Gnatenko, 679th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0997/teamlist)
  - [Michell Osew, 724th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0153/teamlist)
  - [Rinya Kobayashi, 322nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0121/teamlist)
  - [Mihir Desai, 152nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0171/teamlist)
  - [Brian Salyerds Jr, 440th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0145/teamlist)
  - [Omar Trejo, 137th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0187/teamlist)
  - [Michele Mattia Renda, 1041st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0540/teamlist)
  - [Mary Cook, 167th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0498/teamlist)
  - [Drew Bliss, 22nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0260/teamlist)
  - [Jackson Mayberry, 242nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0321/teamlist)
  - [Niklas Margaritaru, 1078th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0621/teamlist)
  - [Wolfgang Behrens, 362nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0431/teamlist)
  - [Devlin Ursu, 456th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0987/teamlist)
  - [Christoph Bley, 476th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0019/teamlist)
  - [David Leonardi, 413th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1053/teamlist)
  - [Ellie Homen, 297th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0645/teamlist)
  - [Rubén Gómez, 401st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0946/teamlist)
  - [Nick-Donovan Gelhorn, 853rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0302/teamlist)
  - [Jacob Curnett, 566th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0959/teamlist)
  - [Reynaldo Boles, 171st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0064/teamlist)
  - [Yu Xiang, 41st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1043/teamlist)
  - [Sascha Eilts, 372nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0025/teamlist)
  - [Nick Theunis, 677th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0916/teamlist)
  - [Julian Pluta, 925th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0271/teamlist)
  - [Chenyi Tao, 985th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0050/teamlist)
  - [Josh Hamilton, 319th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0021/teamlist)
  - [Steven Stark, 550th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0074/teamlist)
  - [Gerald Braden, 642nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0488/teamlist)
  - [Clay McGill, 728th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0054/teamlist)
  - [KaSun Thompson, 238th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0906/teamlist)
  - [Shuji ENDO, 10th, 13 Sep 2026](https://pokepast.es/e74e8282d41a3802)
  - [Yuta Ishigaki, , 12 Sep 2026](https://pokepast.es/668502969512b159)
  - [Matthew Laughlin, 806th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1010/teamlist)
  - [Dylan Assil, 623rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0420/teamlist)
  - [Florian Klaus, 612th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0414/teamlist)
  - [George Caddell, 804th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0692/teamlist)
  - [Alexander Ballin, 355th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0173/teamlist)
  - [Jannik Pawasserat, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0120/teamlist)
  - [Matt Tennant, 42nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0790/teamlist)
  - [Austin Pagel, 139th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0893/teamlist)
  - [Nelson Fonkoua, 209th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0653/teamlist)
  - [Roman Atanasiu Alonso, 1062nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0378/teamlist)
  - [Nolan Parker, 275th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0787/teamlist)
  - [Evan Graham, 602nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0970/teamlist)
  - [Rayan GUEZI, 1035th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0206/teamlist)
  - [Eliah Werner, 882nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0242/teamlist)
  - [Anthony Rodriguez, 833rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0013/teamlist)
  - [Wyatt McDonald, , 15 Sep 2026](https://pokepast.es/97edd96cf0de7f14)
  - [Jeffrey Hartsook, 298th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0639/teamlist)
  - [Damahni Palmer, 27th, 21 Sep 2026](https://pokepast.es/9e422cab36495fcb)
  - [Damahni Palmer, , 15 Sep 2026](https://pokepast.es/b915ba990518d865)
  - [Kevin Guzman, 407th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0034/teamlist)
  - [Ethan Partelow, 476th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0590/teamlist)
  - [Andres Jacobo, 951st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0704/teamlist)
  - [Alexander Solimene, 281st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0500/teamlist)
  - [Jonathan Pabon, 914th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0919/teamlist)
  - [Tobias Hofmann, 1087th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0389/teamlist)
  - [Iker Rodrigo, 42nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0297/teamlist)
  - [Sergio Ramirez, 227th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0541/teamlist)
  - [gravity030, , 9 Sep 2026](https://pokepast.es/2f512d91f56e0830)
  - [beebee10222, , 9 Sep 2026](https://pokepast.es/376176212f88e4cb)
  - [Angstrom Sharrard, 56th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0344/teamlist)
  - [Edgar Graf, 1098th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1015/teamlist)
  - [Jake Murray, 88th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0156/teamlist)
  - [Arnout Bruijn, 1006th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0236/teamlist)
  - [Diego Müller, 854th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0500/teamlist)
  - [Adam Kemmer, 390th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0065/teamlist)
  - [thepostmanp, 8th, 10 Sep 2026](https://pokepast.es/db3ce4cbdae73dbe)
  - [Ali Pütün, 742nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0084/teamlist)
  - [Jeremy Boyd, 294th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0900/teamlist)
  - [One An An, 965th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0407/teamlist)
  - [Nick Smith, 780th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1003/teamlist)
  - [Jayson Lyon, 837th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0516/teamlist)
  - [Duy Thang Nguyen, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0617/teamlist)
  - [Ross Stewart, 234th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0622/teamlist)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Tom de Gruijter, 217th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1093/teamlist)
  - [Eric Uada, 268th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0199/teamlist)
  - [Daniel Medina, 574th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0223/teamlist)
  - [Max Hofmann, 954th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1089/teamlist)
  - [Jean van Roij, 798th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0481/teamlist)
  - [Joshua Hoitink, 92nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0053/teamlist)
  - [Patrick Verrelli, 108th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0102/teamlist)
  - [Robert Pamplin, 744th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0562/teamlist)
  - [Marcus Daniels-Days, 903rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0159/teamlist)
  - [lj_darkrai, , 18 Sep 2026](https://pokepast.es/817ae21505e679f7)
  - [Qiyuan Sun, 651st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0366/teamlist)
  - [Will Schultheis, 1075th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0512/teamlist)
  - [Chloe Bourke, 70th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0262/teamlist)
  - [Kevin Swastek, 665th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0762/teamlist)
  - [Benjamin Wallace, 881st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0339/teamlist)
  - [Heber Henriquez, 805th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0902/teamlist)
  - [Nikita Stoller, 201st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0030/teamlist)
  - [Alec Moran, 1020th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0474/teamlist)
  - [Sirhat Renklitepe, 878th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0143/teamlist)
  - [Daniel Miguel mirapeix, 1113th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0806/teamlist)

- Sub-community pass: 479 primary teams, 157 tokens, modularity 0.62; unconnected tokens: Empoleon (other item; Life Orb 2/6), Scovillain@Scovillainite, Blaziken (other item; Focus Sash 3/5), Ceruledge (other item; Focus Sash 2/5), Hydreigon@Choice Scarf, Rotom-Wash (other item; Sitrus Berry 2/5), Vivillon (other item; Focus Sash 4/5), Abomasnow (other item; Abomasite 4/4), Alakazam (other item; Alakazite 3/4), Corviknight (other item; Sitrus Berry 2/4), Heracross (other item; Heracronite 3/4), Hydreigon (other item; Expert Belt 2/4), Maushold (other item; Wide Lens 3/4), Politoed (other item; Choice Scarf 1/4), Sableye (other item; Black Glasses 1/4), Vanilluxe (other item; Choice Scarf 2/4), Crabominable (other item; Crabominite 3/3), Dragonite (other item; Life Orb 2/3), Drampa (other item; Drampanite 1/3), Grimmsnarl (other item; Light Clay 2/3), Persian-Alola (other item; Choice Scarf 2/3), Primarina (other item; Mystic Water 2/3), Scizor (other item; Life Orb 2/3), Staraptor (other item; Choice Scarf 3/3), Toxtricity (other item; Choice Scarf 2/3), Typhlosion-Hisui (other item; Life Orb 2/3), Zoroark-Hisui (other item; Focus Sash 2/3); unassigned within the community: 9 teams (2.0% of its primary weight); hybrid teams of the community left out: 160
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 3 (0.73) | 5 (0.67) | 6 (0.62) | 7 (0.57) | 7 (0.52) |

#### Community 3 / Sub-community 0: Psyspam (Mega Gardevoir) (214 primary teams, 155 distinct builds, top pair on 158/214)
- Megas on member teams: Gardevoir 160, Golisopod 48, Charizard-Y 28, Salamence 28, Staraptor 11, Absol-Z 10
- Top species by team share: Indeedee-F 100%, Sneasler 75%, Gardevoir 75%, Armarouge 38%, Basculegion 34%, Kingambit 30%
- Token label: Indeedee-F / Gardevoir@Gardevoirite / Sneasler@Psychic Seed
- Mode tags on primary teams: Psyspam 205, Tailwind 92, Trick Room 81, Sun 58, Rain 37, Setup 11, Sand 6, Snow 6, Screens 1
- Primary teams: 214 (41.4% of the community's primary weight), hybrid teams: 28 (6.1%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Venusaur (other item; Focus Sash 17/21)+Charizard@Charizardite Y, Araquanid (other item; Never-Melt Ice 2/4)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 27/37)+Golisopod@Golisopite, Annihilape (other item; Focus Sash 3/8)+Torkoal (other item; Charcoal 66/70), Rotom-Heat (other item; Sitrus Berry 4/6)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 27/37)+Basculegion@Choice Scarf, Venusaur (other item; Focus Sash 17/21)+Basculegion@Choice Scarf, Armarouge (other item; Life Orb 69/153)+Lucario@Lucarionite Z, Sirfetch’d (other item; Leek 3/8)+Golisopod@Golisopite, Basculegion@Choice Scarf+Volcarona@Grassy Seed, Lucario@Lucarionite Z+Sneasler@Psychic Seed, Armarouge (other item; Life Orb 69/153)+Annihilape@Choice Scarf, Sinistcha (other item; Sitrus Berry 7/13)+Golisopod@Golisopite, Basculegion@Choice Scarf+Charizard@Charizardite Y, Annihilape@Choice Scarf+Sneasler@Psychic Seed, Venusaur (other item; Focus Sash 17/21)+Sneasler@Psychic Seed, Pelipper (other item; Focus Sash 27/37)+Sneasler@Psychic Seed, Gardevoir@Gardevoirite+Pyroar@Pyroarite, Kleavor (other item; Focus Sash 8/13)+Golisopod@Golisopite, Basculegion@Choice Scarf+Sneasler@Psychic Seed, Rotom-Heat (other item; Sitrus Berry 4/6)+Gardevoir@Gardevoirite, Rotom-Heat (other item; Sitrus Berry 4/6)+Sneasler@Psychic Seed, Gardevoir@Gardevoirite+Lopunny@Lopunnite, Gardevoir@Gardevoirite+Sneasler@Psychic Seed, Venusaur (other item; Focus Sash 17/21)+Gardevoir@Gardevoirite, Gholdengo (other item; Life Orb 8/14)+Sneasler@Psychic Seed, Armarouge (other item; Life Orb 69/153)+Tyranitar@Tyranitarite, Annihilape@Choice Scarf+Gardevoir@Gardevoirite, Basculegion@Choice Scarf+Gardevoir@Gardevoirite, Whimsicott (other item; Focus Sash 54/75)+Charizard@Charizardite Y, Milotic (other item; Leftovers 17/22)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 27/37)+Gardevoir@Gardevoirite, Gardevoir@Gardevoirite+Tyranitar@Tyranitarite, Charizard@Charizardite Y+Sneasler@Psychic Seed, Annihilape (other item; Focus Sash 3/8)+Armarouge (other item; Life Orb 69/153), Annihilape (other item; Focus Sash 3/8)+Gardevoir@Gardevoirite, Basculegion@Choice Scarf+Golisopod@Golisopite, Hatterene (other item; Life Orb 51/55)+Golisopod@Golisopite, Garchomp (other item; Life Orb 25/28)+Charizard@Charizardite Y, Indeedee-F (other item; Rocky Helmet 160/321)+Lucario@Lucarionite Z, Indeedee-F (other item; Rocky Helmet 160/321)+Sylveon (other item; Fairy Feather 6/6), Indeedee-F (other item; Rocky Helmet 160/321)+Gengar@Gengarite, Indeedee-F (other item; Rocky Helmet 160/321)+Milotic@Psychic Seed, Indeedee-F (other item; Rocky Helmet 160/321)+Meowstic-F (other item; Meowsticite 4/4), Indeedee-F (other item; Rocky Helmet 160/321)+Meowscarada (other item; Choice Scarf 2/4), Indeedee-F (other item; Rocky Helmet 160/321)+Sneasler@Psychic Seed, Indeedee-F (other item; Rocky Helmet 160/321)+Tyranitar@Tyranitarite, Indeedee-F (other item; Rocky Helmet 160/321)+Armarouge@Psychic Seed, Indeedee-F (other item; Rocky Helmet 160/321)+Lopunny@Lopunnite, Altaria (other item; Haban Berry 12/12)+Indeedee-F (other item; Rocky Helmet 160/321), Annihilape (other item; Focus Sash 3/8)+Indeedee-F (other item; Rocky Helmet 160/321), Talonflame (other item; Charcoal 4/12)+Gardevoir@Gardevoirite, Armarouge (other item; Life Orb 69/153)+Golisopod@Golisopite, Charizard@Charizardite Y+Gardevoir@Gardevoirite, Absol@Absolite Z+Sneasler@Psychic Seed, Dragapult (other item; Life Orb 57/57)+Indeedee-F (other item; Rocky Helmet 160/321), Indeedee-F (other item; Rocky Helmet 160/321)+Mawile@Mawilite, Charizard@Charizardite Y+Glimmora@Glimmoranite, Salamence@Salamencite+Sneasler@Psychic Seed, Kommo-o (other item; Life Orb 29/43)+Charizard@Charizardite Y, Indeedee-F (other item; Rocky Helmet 160/321)+Gardevoir@Gardevoirite, Aerodactyl (other item; Aerodactylite 3/7)+Gardevoir@Gardevoirite, Golisopod@Golisopite+Milotic@Psychic Seed, Kommo-o (other item; Life Orb 29/43)+Gardevoir@Gardevoirite, Gholdengo (other item; Life Orb 8/14)+Indeedee-F (other item; Rocky Helmet 160/321), Armarouge (other item; Life Orb 69/153)+Indeedee-F (other item; Rocky Helmet 160/321), Indeedee-F (other item; Rocky Helmet 160/321)+Venusaur (other item; Focus Sash 17/21), Indeedee-F (other item; Rocky Helmet 160/321)+Pyroar@Pyroarite, Indeedee-F (other item; Rocky Helmet 160/321)+Staraptor@Staraptite, Indeedee-F (other item; Rocky Helmet 160/321)+Pelipper (other item; Focus Sash 27/37), Gholdengo (other item; Life Orb 8/14)+Gardevoir@Gardevoirite, Arcanine-Hisui (other item; Focus Sash 29/31)+Gardevoir@Gardevoirite, Arcanine-Hisui (other item; Focus Sash 29/31)+Indeedee-F (other item; Rocky Helmet 160/321), Torkoal (other item; Charcoal 66/70)+Venusaur (other item; Focus Sash 17/21), Golisopod@Golisopite+Staraptor@Staraptite, Sneasler (other item; White Herb 28/35)+Golisopod@Golisopite, Blastoise@Blastoisinite+Gardevoir@Gardevoirite, Golisopod@Golisopite+Sneasler@Psychic Seed, Dragapult (other item; Life Orb 57/57)+Golisopod@Golisopite, Indeedee-F (other item; Rocky Helmet 160/321)+Rotom-Heat (other item; Sitrus Berry 4/6), Indeedee-F (other item; Rocky Helmet 160/321)+Charizard@Charizardite Y, Gallade (other item; White Herb 5/11)+Indeedee-F (other item; Rocky Helmet 160/321), Torkoal (other item; Charcoal 66/70)+Gardevoir@Gardevoirite, Indeedee-F (other item; Rocky Helmet 160/321)+Golisopod@Golisopite, Indeedee-F (other item; Rocky Helmet 160/321)+Basculegion@Choice Scarf, Indeedee-F (other item; Rocky Helmet 160/321)+Annihilape@Choice Scarf, Indeedee-F (other item; Rocky Helmet 160/321)+Blastoise@Blastoisinite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Indeedee-F (other item; Rocky Helmet 160/321) | 97.8% |
| Gardevoir@Gardevoirite | 75.3% |
| Sneasler@Psychic Seed | 69.6% |
| Basculegion@Choice Scarf | 25.4% |
| Golisopod@Golisopite | 22.9% |
| Pelipper (other item; Focus Sash 27/37) | 16.8% |
| Charizard@Charizardite Y | 13.4% |
| Venusaur (other item; Focus Sash 17/21) | 7.8% |
| Gholdengo (other item; Life Orb 8/14) | 5.8% |
| Annihilape (other item; Focus Sash 3/8) | 4.3% |
| Annihilape@Choice Scarf | 3.6% |
| Tyranitar@Tyranitarite | 2.9% |
| Lucario@Lucarionite Z | 2.8% |
| Rotom-Heat (other item; Sitrus Berry 4/6) | 2.6% |
| Lopunny@Lopunnite | 2.4% |
| Armarouge@Psychic Seed | 2.3% |
| Aerodactyl (other item; Aerodactylite 3/7) | 1.5% |
| Meowscarada (other item; Choice Scarf 2/4) | 1.1% |
| Meowstic-F (other item; Meowsticite 4/4) | 0.9% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Jack Riley, 257th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0116/teamlist)
  - [Ward Eeckhautte, 708th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0811/teamlist)
  - [Fotios Boutziloudis, 991st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0336/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Matteo Raganini, 1056th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0077/teamlist)
  - [Sten Schram, 901st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0867/teamlist)
  - [Oliver Bøgebjerg, 746th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0998/teamlist)
  - [Jeremy Ortiz, 976th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0077/teamlist)
  - [Raphael But, 1088th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0168/teamlist)
  - [espertcg, 9th, 19 Sep 2026](https://pokepast.es/e186eb86a1990ce2)
  - [Victor Vasilian, 683rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0431/teamlist)
  - [elijah castillo-esper, 813th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0337/teamlist)
  - [Ian Brito, 57th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0696/teamlist)
  - [Adrianna Massaro, 820th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0389/teamlist)
  - [Eduardo Garcia, 1042nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0558/teamlist)
  - [Tim Kelling, 68th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0273/teamlist)
  - [Robert-Jan Vedder, 186th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0170/teamlist)
  - [kai macgowan, 324th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0072/teamlist)
  - [Michał Młynarczyk, 989th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0923/teamlist)
  - [Luke Bowar, 464th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0984/teamlist)
  - [katsumaji, , 12 Sep 2026](https://pokepast.es/843abb0faf6aa3e4)
  - [Liam Hackett, 207th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0112/teamlist)
  - [Andrew Lavigne, 876th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0603/teamlist)
  - [Shybaka, , 10 Sep 2026](https://pokepast.es/019515d1edb34b3d)
  - [Chuck Yin Sin, 919th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0734/teamlist)
  - [Lee Provost, 165th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0226/teamlist)
  - [Angelo Gatta, 710th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0969/teamlist)
  - [Brian Compere, 367th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0663/teamlist)
  - [Haotian Wu, 624th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0609/teamlist)
  - [Ryota Otsubo, , 17 Sep 2026](https://pokepast.es/199745ed6f3b10ed)
  - [Marius Sandrock, 1067th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0131/teamlist)
  - [Dane Tinworth, 291st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0214/teamlist)

#### Community 3 / Sub-community 1: Psyspam (Mega Staraptor) (69 primary teams, 33 distinct builds, top pair on 38/69)
- Megas on member teams: Staraptor 43, Metagross 22, Gengar 15, Golisopod 14, Raichu-Y 5, Absol-Z 4
- Top species by team share: Indeedee-F 97%, Armarouge 77%, Milotic 77%, Dragapult 68%, Staraptor 62%, Metagross 32%
- Token label: Armarouge / Milotic@Psychic Seed / Dragapult
- Mode tags on primary teams: Psyspam 55, Tailwind 32, Setup 27, Trick Room 11, Sun 4, Snow 2, Screens 1
- Primary teams: 69 (15.7% of the community's primary weight), hybrid teams: 18 (3.6%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Altaria (other item; Haban Berry 12/12)+Arcanine-Hisui (other item; Focus Sash 29/31), Altaria (other item; Haban Berry 12/12)+Metagross@Metagrossite, Altaria (other item; Haban Berry 12/12)+Milotic@Psychic Seed, Gengar@Gengarite+Milotic@Psychic Seed, Altaria (other item; Haban Berry 12/12)+Dragapult (other item; Life Orb 57/57), Dragapult (other item; Life Orb 57/57)+Gengar@Gengarite, Dragapult (other item; Life Orb 57/57)+Milotic@Psychic Seed, Gengar@Gengarite+Staraptor@Staraptite, Milotic@Psychic Seed+Staraptor@Staraptite, Kleavor (other item; Focus Sash 8/13)+Metagross@Metagrossite, Arcanine-Hisui (other item; Focus Sash 29/31)+Metagross@Metagrossite, Dragapult (other item; Life Orb 57/57)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 29/31)+Milotic@Psychic Seed, Metagross@Metagrossite+Milotic@Psychic Seed, Arcanine-Hisui (other item; Focus Sash 29/31)+Dragapult (other item; Life Orb 57/57), Armarouge (other item; Life Orb 69/153)+Lucario@Lucarionite Z, Armarouge (other item; Life Orb 69/153)+Gengar@Gengarite, Dragapult (other item; Life Orb 57/57)+Metagross@Metagrossite, Armarouge (other item; Life Orb 69/153)+Annihilape@Choice Scarf, Armarouge (other item; Life Orb 69/153)+Staraptor@Staraptite, Armarouge (other item; Life Orb 69/153)+Milotic@Psychic Seed, Arcanine-Hisui (other item; Focus Sash 29/31)+Kommo-o (other item; Life Orb 29/43), Armarouge (other item; Life Orb 69/153)+Grapploct (other item; Psychic Seed 2/5), Armarouge (other item; Life Orb 69/153)+Mawile@Mawilite, Armarouge (other item; Life Orb 69/153)+Dragapult (other item; Life Orb 57/57), Armarouge (other item; Life Orb 69/153)+Tyranitar@Tyranitarite, Armarouge (other item; Life Orb 69/153)+Sirfetch’d (other item; Leek 3/8), Annihilape (other item; Focus Sash 3/8)+Armarouge (other item; Life Orb 69/153), Armarouge (other item; Life Orb 69/153)+Blastoise@Blastoisinite, Indeedee-F (other item; Rocky Helmet 160/321)+Gengar@Gengarite, Indeedee-F (other item; Rocky Helmet 160/321)+Milotic@Psychic Seed, Altaria (other item; Haban Berry 12/12)+Indeedee-F (other item; Rocky Helmet 160/321), Dragapult (other item; Life Orb 57/57)+Milotic (other item; Leftovers 17/22), Armarouge (other item; Life Orb 69/153)+Golisopod@Golisopite, Armarouge (other item; Life Orb 69/153)+Absol@Absolite Z, Armarouge (other item; Life Orb 69/153)+Sinistcha (other item; Sitrus Berry 7/13), Absol@Absolite Z+Sneasler@Psychic Seed, Dragapult (other item; Life Orb 57/57)+Indeedee-F (other item; Rocky Helmet 160/321), Armarouge (other item; Life Orb 69/153)+Gallade (other item; White Herb 5/11), Golisopod@Golisopite+Milotic@Psychic Seed, Armarouge (other item; Life Orb 69/153)+Indeedee-F (other item; Rocky Helmet 160/321), Indeedee-F (other item; Rocky Helmet 160/321)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 29/31)+Gardevoir@Gardevoirite, Arcanine-Hisui (other item; Focus Sash 29/31)+Indeedee-F (other item; Rocky Helmet 160/321), Garchomp (other item; Life Orb 25/28)+Staraptor@Staraptite, Armarouge (other item; Life Orb 69/153)+Torkoal (other item; Charcoal 66/70), Golisopod@Golisopite+Staraptor@Staraptite, Whimsicott (other item; Focus Sash 54/75)+Staraptor@Staraptite, Sneasler (other item; White Herb 28/35)+Metagross@Metagrossite, Dragapult (other item; Life Orb 57/57)+Golisopod@Golisopite, Armarouge (other item; Life Orb 69/153)+Raichu@Raichunite Y
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Armarouge (other item; Life Orb 69/153) | 78.2% |
| Milotic@Psychic Seed | 75.0% |
| Dragapult (other item; Life Orb 57/57) | 68.9% |
| Staraptor@Staraptite | 64.2% |
| Metagross@Metagrossite | 29.2% |
| Gengar@Gengarite | 24.6% |
| Arcanine-Hisui (other item; Focus Sash 29/31) | 18.5% |
| Altaria (other item; Haban Berry 12/12) | 15.2% |
| Absol@Absolite Z | 5.0% |
| Grapploct (other item; Psychic Seed 2/5) | 1.2% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Dicky Nicholas, 53rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0164/teamlist)
  - [Joseph Russell, 149th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0939/teamlist)
  - [Filippo Guagliardo, 158th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0037/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Ian Kormos, 36th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0783/teamlist)
  - [Elias Schüller, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0858/teamlist)
  - [Patrick Schulke, 564th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0197/teamlist)
  - [Luke Bowar, 464th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0984/teamlist)
  - [Aaron Clemons, 213th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0668/teamlist)
  - [WoodTofu, , 16 Sep 2026](https://pokepast.es/f46d41e3a61aef4e)
  - [Zachary Mulcahy, 1015th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0570/teamlist)
  - [starlightkairi, , 10 Sep 2026](https://pokepast.es/0a39ceed249b8ff7)
  - [espertcg, 9th, 19 Sep 2026](https://pokepast.es/e186eb86a1990ce2)
  - [Victor Vasilian, 683rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0431/teamlist)
  - [elijah castillo-esper, 813th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0337/teamlist)
  - [Derek Kress, 448th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0505/teamlist)
  - [Haotian Wu, 624th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0609/teamlist)
  - [Liam Hackett, 207th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0112/teamlist)
  - [Michael Koenigsberg, 938th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0368/teamlist)
  - [Vinel Kinsala, 674th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0690/teamlist)
  - [Sebastian Lindenau, 841st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0270/teamlist)
  - [Gabe Cargo, 895th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0028/teamlist)

#### Community 3 / Sub-community 2: Trick Room Psyspam (Mega Camerupt) (78 primary teams, 56 distinct builds, top pair on 33/78)
- Megas on member teams: Camerupt 44, Golisopod 13, Gardevoir 11, Blaziken 9, Blastoise 4, Mawile 4
- Top species by team share: Indeedee-F 87%, Hatterene 63%, Camerupt 56%, Kingambit 47%, Incineroar 46%, Farigiraf 45%
- Token label: Hatterene / Camerupt@Cameruptite / Indeedee-F@Psychic Seed
- Mode tags on primary teams: Trick Room 72, Psyspam 68, Sun 30, Tailwind 9, Snow 6, Setup 3, Sand 1
- Primary teams: 78 (16.7% of the community's primary weight), hybrid teams: 8 (1.8%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Glimmora (other item; Focus Sash 12/13)+Blaziken@Blazikenite, Farigiraf (other item; Sitrus Berry 31/40)+Camerupt@Cameruptite, Hatterene (other item; Life Orb 51/55)+Camerupt@Cameruptite, Sirfetch’d (other item; Leek 3/8)+Camerupt@Cameruptite, Farigiraf (other item; Sitrus Berry 31/40)+Hatterene (other item; Life Orb 51/55), Torkoal (other item; Charcoal 66/70)+Blaziken@Blazikenite, Farigiraf (other item; Sitrus Berry 31/40)+Indeedee-F@Psychic Seed, Hatterene (other item; Life Orb 51/55)+Sirfetch’d (other item; Leek 3/8), Camerupt@Cameruptite+Indeedee-F@Psychic Seed, Farigiraf (other item; Sitrus Berry 31/40)+Incineroar (other item; Sitrus Berry 29/73), Hatterene (other item; Life Orb 51/55)+Blaziken@Blazikenite, Hatterene (other item; Life Orb 51/55)+Indeedee-F@Psychic Seed, Incineroar (other item; Sitrus Berry 29/73)+Camerupt@Cameruptite, Incineroar (other item; Sitrus Berry 29/73)+Froslass@Froslassite, Gallade (other item; White Herb 5/11)+Hatterene (other item; Life Orb 51/55), Annihilape (other item; Focus Sash 3/8)+Torkoal (other item; Charcoal 66/70), Gallade (other item; White Herb 5/11)+Camerupt@Cameruptite, Incineroar (other item; Sitrus Berry 29/73)+Volcarona@Grassy Seed, Torkoal (other item; Charcoal 66/70)+Mawile@Mawilite, Blaziken@Blazikenite+Indeedee-F@Psychic Seed, Sirfetch’d (other item; Leek 3/8)+Golisopod@Golisopite, Hatterene (other item; Life Orb 51/55)+Incineroar (other item; Sitrus Berry 29/73), Incineroar (other item; Sitrus Berry 29/73)+Indeedee-F@Psychic Seed, Glimmora (other item; Focus Sash 12/13)+Hatterene (other item; Life Orb 51/55), Kingambit (other item; Chople Berry 46/137)+Dragonite@Dragoninite, Kingambit (other item; Chople Berry 46/137)+Garchomp@Choice Scarf, Gallade (other item; White Herb 5/11)+Torkoal (other item; Charcoal 66/70), Sneasler (other item; White Herb 28/35)+Indeedee-F@Psychic Seed, Hatterene (other item; Life Orb 51/55)+Torkoal (other item; Charcoal 66/70), Farigiraf (other item; Sitrus Berry 31/40)+Kingambit (other item; Chople Berry 46/137), Incineroar (other item; Sitrus Berry 29/73)+Blastoise@Blastoisinite, Armarouge (other item; Life Orb 69/153)+Mawile@Mawilite, Glimmora (other item; Focus Sash 12/13)+Whimsicott (other item; Focus Sash 54/75), Kingambit (other item; Chople Berry 46/137)+Camerupt@Cameruptite, Glimmora (other item; Focus Sash 12/13)+Kingambit (other item; Chople Berry 46/137), Torkoal (other item; Charcoal 66/70)+Indeedee-F@Psychic Seed, Sneasler (other item; White Herb 28/35)+Torkoal (other item; Charcoal 66/70), Armarouge (other item; Life Orb 69/153)+Sirfetch’d (other item; Leek 3/8), Kingambit (other item; Chople Berry 46/137)+Blastoise@Blastoisinite, Hatterene (other item; Life Orb 51/55)+Kingambit (other item; Chople Berry 46/137), Kingambit (other item; Chople Berry 46/137)+Indeedee-F@Psychic Seed, Kingambit (other item; Chople Berry 46/137)+Salamence@Salamencite, Kingambit (other item; Chople Berry 46/137)+Talonflame (other item; Charcoal 4/12), Kingambit (other item; Chople Berry 46/137)+Volcarona (other item; Rocky Helmet 18/46), Hatterene (other item; Life Orb 51/55)+Golisopod@Golisopite, Kingambit (other item; Chople Berry 46/137)+Torkoal (other item; Charcoal 66/70), Incineroar (other item; Sitrus Berry 29/73)+Rillaboom (other item; Miracle Seed 35/44), Incineroar (other item; Sitrus Berry 29/73)+Kingambit (other item; Chople Berry 46/137), Indeedee-F (other item; Rocky Helmet 160/321)+Mawile@Mawilite, Kingambit (other item; Chople Berry 46/137)+Garchomp@Garchompite Z, Armarouge (other item; Life Orb 69/153)+Gallade (other item; White Herb 5/11), Farigiraf (other item; Sitrus Berry 31/40)+Torkoal (other item; Charcoal 66/70), Kingambit (other item; Chople Berry 46/137)+Rillaboom (other item; Miracle Seed 35/44), Hatterene (other item; Life Orb 51/55)+Raichu@Raichunite Y, Kingambit (other item; Chople Berry 46/137)+Glimmora@Glimmoranite, Incineroar (other item; Sitrus Berry 29/73)+Glimmora@Glimmoranite, Torkoal (other item; Charcoal 66/70)+Venusaur (other item; Focus Sash 17/21), Armarouge (other item; Life Orb 69/153)+Torkoal (other item; Charcoal 66/70), Kingambit (other item; Chople Berry 46/137)+Blaziken@Blazikenite, Basculegion (other item; Mystic Water 33/74)+Kingambit (other item; Chople Berry 46/137), Kingambit (other item; Chople Berry 46/137)+Baxcalibur@Baxcalibrite, Kingambit (other item; Chople Berry 46/137)+Ninetales-Alola (other item; Never-Melt Ice 7/11), Gallade (other item; White Herb 5/11)+Indeedee-F (other item; Rocky Helmet 160/321), Torkoal (other item; Charcoal 66/70)+Gardevoir@Gardevoirite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Hatterene (other item; Life Orb 51/55) | 63.9% |
| Camerupt@Cameruptite | 59.6% |
| Indeedee-F@Psychic Seed | 58.6% |
| Kingambit (other item; Chople Berry 46/137) | 50.5% |
| Incineroar (other item; Sitrus Berry 29/73) | 49.5% |
| Farigiraf (other item; Sitrus Berry 31/40) | 46.3% |
| Torkoal (other item; Charcoal 66/70) | 35.1% |
| Blaziken@Blazikenite | 12.6% |
| Glimmora (other item; Focus Sash 12/13) | 6.7% |
| Gallade (other item; White Herb 5/11) | 5.4% |
| Sirfetch’d (other item; Leek 3/8) | 5.2% |
| Mawile@Mawilite | 4.5% |
| Dragonite@Dragoninite | 1.4% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Blake Bollen, 97th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0103/teamlist)
  - [Ryan Miller, 104th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0312/teamlist)
  - [Kevin Isaac, 165th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0083/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Ibrahim Maarouf, 64th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0980/teamlist)
  - [Simon Chan, 78th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0271/teamlist)
  - [Brian Compere, 367th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0663/teamlist)
  - [castillo11vgc, Peak 3rd, 10 Sep 2026](https://pokepast.es/c9915faa4b501283)
  - [Maximilian Lopata, 1123rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0126/teamlist)
  - [Diego Ferreira, 12th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0061/teamlist)
  - [John Talbot, 169th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0060/teamlist)
  - [Christopher Chau, 685th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0540/teamlist)

#### Community 3 / Sub-community 3: Psyspam · Whimsicott (52 primary teams, 33 distinct builds, top pair on 34/52)
- Megas on member teams: Gardevoir 19, Pyroar 17, Glimmora 12, Charizard-Y 6, Metagross 6, Golisopod 5
- Top species by team share: Whimsicott 85%, Basculegion 73%, Kommo-o 52%, Indeedee-F 44%, Gardevoir 37%, Pyroar 33%
- Token label: Whimsicott / Basculegion / Kommo-o
- Mode tags on primary teams: Tailwind 47, Psyspam 27, Sun 9, Setup 3, Snow 2, Rain 1, Sand 1, Trick Room 1
- Primary teams: 52 (11.9% of the community's primary weight), hybrid teams: 6 (1.3%)
- Date range: 2026-09-10 to 2026-09-27
- Core pairs: Araquanid (other item; Never-Melt Ice 2/4)+Kleavor (other item; Focus Sash 8/13), Araquanid (other item; Never-Melt Ice 2/4)+Volcarona (other item; Rocky Helmet 18/46), Kommo-o (other item; Life Orb 29/43)+Pyroar@Pyroarite, Indeedee (other item; Focus Sash 4/8)+Glimmora@Glimmoranite, Indeedee (other item; Focus Sash 4/8)+Kommo-o (other item; Life Orb 29/43), Basculegion (other item; Mystic Water 33/74)+Pyroar@Pyroarite, Araquanid (other item; Never-Melt Ice 2/4)+Golisopod@Golisopite, Whimsicott (other item; Focus Sash 54/75)+Pyroar@Pyroarite, Kleavor (other item; Focus Sash 8/13)+Metagross@Metagrossite, Delphox (other item; Delphoxite 4/5)+Whimsicott (other item; Focus Sash 54/75), Indeedee (other item; Focus Sash 4/8)+Whimsicott (other item; Focus Sash 54/75), Whimsicott (other item; Focus Sash 54/75)+Typhlosion-Hisui@Choice Scarf, Kommo-o (other item; Life Orb 29/43)+Whimsicott (other item; Focus Sash 54/75), Rillaboom (other item; Miracle Seed 35/44)+Garchomp@Garchompite Z, Basculegion (other item; Mystic Water 33/74)+Floette-Eternal@Floettite, Basculegion (other item; Mystic Water 33/74)+Kommo-o (other item; Life Orb 29/43), Basculegion (other item; Mystic Water 33/74)+Indeedee (other item; Focus Sash 4/8), Kleavor (other item; Focus Sash 8/13)+Volcarona (other item; Rocky Helmet 18/46), Basculegion (other item; Mystic Water 33/74)+Whimsicott (other item; Focus Sash 54/75), Basculegion (other item; Mystic Water 33/74)+Kleavor (other item; Focus Sash 8/13), Arcanine-Hisui (other item; Focus Sash 29/31)+Kommo-o (other item; Life Orb 29/43), Gardevoir@Gardevoirite+Pyroar@Pyroarite, Kleavor (other item; Focus Sash 8/13)+Golisopod@Golisopite, Basculegion (other item; Mystic Water 33/74)+Baxcalibur@Baxcalibrite, Basculegion (other item; Mystic Water 33/74)+Talonflame (other item; Charcoal 4/12), Basculegion (other item; Mystic Water 33/74)+Glimmora@Glimmoranite, Kommo-o (other item; Life Orb 29/43)+Glimmora@Glimmoranite, Whimsicott (other item; Focus Sash 54/75)+Garchomp@Garchompite Z, Glimmora (other item; Focus Sash 12/13)+Whimsicott (other item; Focus Sash 54/75), Basculegion (other item; Mystic Water 33/74)+Garchomp@Garchompite Z, Garchomp (other item; Life Orb 25/28)+Whimsicott (other item; Focus Sash 54/75), Whimsicott (other item; Focus Sash 54/75)+Glimmora@Glimmoranite, Kleavor (other item; Focus Sash 8/13)+Whimsicott (other item; Focus Sash 54/75), Whimsicott (other item; Focus Sash 54/75)+Charizard@Charizardite Y, Basculegion (other item; Mystic Water 33/74)+Volcarona@Grassy Seed, Whimsicott (other item; Focus Sash 54/75)+Raichu@Raichunite Y, Basculegion (other item; Mystic Water 33/74)+Rillaboom (other item; Miracle Seed 35/44), Basculegion (other item; Mystic Water 33/74)+Volcarona (other item; Rocky Helmet 18/46), Kingambit (other item; Chople Berry 46/137)+Garchomp@Garchompite Z, Kommo-o (other item; Life Orb 29/43)+Charizard@Charizardite Y, Basculegion (other item; Mystic Water 33/74)+Salamence@Salamencite, Basculegion (other item; Mystic Water 33/74)+Raichu@Raichunite Y, Kommo-o (other item; Life Orb 29/43)+Gardevoir@Gardevoirite, Indeedee-F (other item; Rocky Helmet 160/321)+Pyroar@Pyroarite, Basculegion (other item; Mystic Water 33/74)+Kingambit (other item; Chople Berry 46/137), Whimsicott (other item; Focus Sash 54/75)+Staraptor@Staraptite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Whimsicott (other item; Focus Sash 54/75) | 84.8% |
| Basculegion (other item; Mystic Water 33/74) | 67.5% |
| Kommo-o (other item; Life Orb 29/43) | 50.9% |
| Pyroar@Pyroarite | 33.0% |
| Kleavor (other item; Focus Sash 8/13) | 16.6% |
| Indeedee (other item; Focus Sash 4/8) | 13.6% |
| Typhlosion-Hisui@Choice Scarf | 9.3% |
| Araquanid (other item; Never-Melt Ice 2/4) | 7.5% |
| Garchomp@Garchompite Z | 7.1% |
| Floette-Eternal@Floettite | 6.6% |
| Delphox (other item; Delphoxite 4/5) | 6.1% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Luke Blundell, 127th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0276/teamlist)
  - [Shai Giuseppe Rabà, 55th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0074/teamlist)
  - [Gia Long Hoang, 197th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0060/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/01069fa4762c8613)
  - [Dominik Papke, 494th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0396/teamlist)
  - [Andrew Devlin, 1040th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0184/teamlist)
  - [Joshua Moloney, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0143/teamlist)
  - [Dane Tinworth, 291st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0214/teamlist)
  - [Daniel Attwell, 273rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0166/teamlist)

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): Sub-community 4: Rillaboom / Glimmora@Glimmoranite (39 distinct builds, 56 primary teams, top pair on 20/56); Sub-community 5: Blastoise@Blastoisinite / Sinistcha (1 distinct build, 1 primary team, top pair on 1/1)

#### Community 3 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Indeedee-F | Indeedee-F (other item; Rocky Helmet 160/321) | Sub-community 0: Psyspam (Mega Gardevoir) | 321 | 0: 210 · 1: 64 · 2: 23 · 3: 22 · 4: 1 · unassigned: 1 |
| Indeedee-F | Indeedee-F@Psychic Seed | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 60 | 2: 45 · 0: 4 · 1: 3 · 4: 2 · 3: 1 · 5: 1 · unassigned: 4 |
| Armarouge | Armarouge (other item; Life Orb 69/153) | Sub-community 1: Psyspam (Mega Staraptor) | 153 | 0: 78 · 1: 53 · 2: 17 · 4: 3 · 5: 1 · unassigned: 1 |
| Armarouge | Armarouge@Psychic Seed | Sub-community 0: Psyspam (Mega Gardevoir) | 6 | 0: 4 · 2: 2 · unassigned: 0 |
| Sneasler | Sneasler@Psychic Seed | Sub-community 0: Psyspam (Mega Gardevoir) | 153 | 0: 151 · 2: 2 · unassigned: 0 |
| Sneasler | Sneasler (other item; White Herb 28/35) | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 35 | 4: 12 · 0: 10 · 2: 6 · 1: 5 · 3: 2 · unassigned: 0 |
| Basculegion | Basculegion (other item; Mystic Water 33/74) | Sub-community 3: Psyspam · Whimsicott | 74 | 3: 35 · 0: 18 · 4: 16 · 2: 3 · 1: 2 · unassigned: 0 |
| Basculegion | Basculegion@Choice Scarf | Sub-community 0: Psyspam (Mega Gardevoir) | 69 | 0: 55 · 4: 8 · 3: 3 · 1: 1 · 2: 1 · unassigned: 1 |
| Milotic | Milotic@Psychic Seed | Sub-community 1: Psyspam (Mega Staraptor) | 51 | 1: 51 · unassigned: 0 |
| Milotic | Milotic (other item; Leftovers 17/22) | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 22 | 0: 11 · 2: 4 · 4: 4 · 1: 2 · unassigned: 1 |
| Glimmora | Glimmora@Glimmoranite | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 46 | 4: 30 · 3: 12 · 0: 3 · 2: 1 · unassigned: 0 |
| Glimmora | Glimmora (other item; Focus Sash 12/13) | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 13 | 2: 5 · 1: 3 · 3: 3 · 0: 1 · 4: 1 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 25/28) | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 28 | 0: 14 · 4: 9 · 3: 3 · 1: 1 · 2: 1 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 3: Psyspam · Whimsicott | 12 | 0: 4 · 3: 4 · 4: 4 · unassigned: 0 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 10 | 4: 6 · 2: 3 · 0: 1 · unassigned: 0 |

### Community 4: Rain
- Token label: Archaludon / Farigiraf
- Mode tags on primary teams: Rain 431, Tailwind 339, Sun 243, Trick Room 158, Screens 148, Perish Trap 93, Psyspam 34, Setup 31, Snow 25, Sand 6
- Megas on primary teams: Golisopod 270, Charizard-Y 230, Swampert 110, Gengar 101, Garchomp-Z 72, Salamence 51, Aerodactyl 45, Floette 22, Froslass 21, Raichu-Y 20, Metagross 15, Dragonite 13, Camerupt 12, Glimmora 12, Mawile 12, Meganium 10, Gardevoir 9, Lucario-Z 9, Venusaur 9, Staraptor 7, Blaziken 6, Absol-Z 5, Raichu-X 5, Ampharos 4, Blastoise 4, Manectric 4, Scovillain 4, Baxcalibur 3, Pyroar 3, Starmie 3, Charizard-X 2, Delphox 2, Drampa 2, Scizor 2, Tyranitar 2, Audino 1, Beedrill 1, Chandelure 1, Dragalge 1, Feraligatr 1, Garchomp 1, Greninja 1, Kangaskhan 1, Lopunny 1, Meowstic-F 1, Sableye 1, Steelix 1
- Primary teams: 637 (primary share 22.5%), hybrid teams: 139 (hybrid share 4.6%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Aegislash+Venusaur@Life Orb, Toxapex+Venusaur@Life Orb, Vivillon+Incineroar@Passho Berry, Vivillon+Politoed@Sitrus Berry, Vivillon+Rillaboom@Eject Button, Klefki+Garchomp@Life Orb, Toxapex+Garchomp@Choice Scarf, Venusaur+Aegislash@Focus Sash, Gengar+Incineroar@Lum Berry, Gengar+Rillaboom@Eject Button, Gengar+Vivillon@Focus Sash, Vivillon+Gengar@Gengarite, Gengar+Vivillon, Gengar+Politoed@Sitrus Berry, Aegislash+Venusaur, Grimmsnarl+Politoed@Mystic Water, Mawile+Farigiraf@Colbur Berry, Venusaur+Toxapex@Leftovers, Aerodactyl+Tsareena, Gengar+Incineroar@Passho Berry, Aerodactyl+Garchomp@Life Orb, Swampert+Sableye@Light Clay, Politoed+Vivillon@Focus Sash, Politoed+Vivillon, Toxapex+Venusaur, Aerodactyl+Kingambit@Focus Sash, Pawmot+Politoed@Life Orb, Grimmsnarl+Venusaur@Focus Sash, Politoed+Rillaboom@Eject Button, Politoed+Incineroar@Passho Berry, Venusaur+Pelipper@Sitrus Berry, Sinistcha+Sableye@Roseli Berry, Pelipper+Archaludon@Magnet, Sableye+Swampert@Swampertite, Grimmsnarl+Pelipper@Sitrus Berry, Swampert+Pelipper@Sitrus Berry, Sableye+Swampert, Meowstic-F+Sneasler@Psychic Seed, Charizard+Venusaur@Focus Sash, Swampert+Venusaur@Focus Sash, Pelipper+Swampert@Swampertite, Gengar+Dragonite@Life Orb, Politoed+Gengar@Gengarite, Gengar+Politoed, Venusaur+Grimmsnarl@Light Clay, Charizard+Venusaur@Wide Lens, Pelipper+Swampert, Venusaur+Charizard@Charizardite Y, Grimmsnarl+Venusaur, Charizard+Venusaur, Archaludon+Klefki@Light Clay, Golisopod+Politoed@Mystic Water, Charizard+Venusaur@Life Orb, Gengar+Kommo-o@Leftovers, Politoed+Staraptor@Choice Scarf, Vivillon+Rillaboom@Occa Berry, Archaludon+Politoed@Mystic Water, Golisopod+Pelipper@Choice Scarf, Pelipper+Archaludon@Chople Berry, Golisopod+Politoed@Life Orb, Swampert+Pelipper@Focus Sash, Mawile+Torkoal@Charcoal, Meganium+Pelipper, Pelipper+Meganium@Meganiumite, Farigiraf+Politoed@Life Orb, Garchomp+Aegislash@Focus Sash, Garchomp+Klefki@Light Clay, Charizard+Toxapex@Leftovers, Grimmsnarl+Archaludon@Leftovers, Torkoal+Farigiraf@Grassy Seed, Charizard+Aerodactyl@Aerodactylite, Archaludon+Pelipper@Choice Scarf, Grimmsnarl+Swampert@Swampertite, Venusaur+Swampert@Swampertite, Archaludon+Pelipper@Damp Rock, Archaludon+Swampert@Swampertite, Charizard+Politoed@Mystic Water, Archaludon+Politoed@Choice Scarf, Archaludon+Grimmsnarl@Light Clay, Mawile+Torkoal, Torkoal+Mawile@Mawilite, Camerupt+Farigiraf@Colbur Berry, Archaludon+Grimmsnarl, Klefki+Archaludon@Leftovers, Politoed+Archaludon@Leftovers, Pelipper+Venusaur@Focus Sash, Toxapex+Charizard@Charizardite Y, Farigiraf+Incineroar@White Herb, Swampert+Archaludon@Leftovers, Sylveon+Aerodactyl@Aerodactylite, Archaludon+Pelipper@Sitrus Berry, Primarina+Farigiraf@Grassy Seed, Basculegion+Pelipper@Choice Scarf, Venusaur+Garchomp@Choice Scarf, Sylveon+Aegislash@Focus Sash, Charizard+Toxapex, Grimmsnarl+Swampert, Swampert+Grimmsnarl@Light Clay, Swampert+Venusaur, Farigiraf+Politoed@Mystic Water, Archaludon+Swampert, Archaludon+Politoed, Archaludon+Sableye@Light Clay, Archaludon+Klefki, Archaludon+Politoed@Sitrus Berry, Archaludon+Pelipper, Archaludon+Pelipper@Focus Sash, Abomasnow+Farigiraf, Farigiraf+Abomasnow@Abomasite, Aerodactyl+Charizard@Charizardite Y, Charizard+Grimmsnarl@Light Clay, Garchomp+Toxapex@Leftovers, Politoed+Grimmsnarl@Light Clay, Grimmsnarl+Charizard@Charizardite Y, Camerupt+Farigiraf, Farigiraf+Camerupt@Cameruptite, Charizard+Aegislash@Focus Sash, Pelipper+Archaludon@Leftovers, Gengar+Milotic@Psychic Seed, Gengar+Armarouge@Focus Sash, Aerodactyl+Charizard, Grapploct+Farigiraf@Sitrus Berry, Charizard+Grimmsnarl, Meowstic-F+Charizard@Charizardite Y, Grimmsnarl+Politoed, Camerupt+Farigiraf@Sitrus Berry, Farigiraf+Staraptor@Choice Scarf, Charizard+Garchomp@Choice Scarf, Charizard+Meowstic-F, Charizard+Meowstic-F@Meowsticite, Aerodactyl+Sylveon@Fairy Feather, Gengar+Incineroar@Chople Berry, Sableye+Pelipper@Focus Sash, Garchomp+Klefki, Tyranitar+Garchomp@Garchompite, Charizard+Garchomp@Life Orb, Vivillon+Archaludon@Leftovers, Golisopod+Sableye@Light Clay, Whimsicott+Garchomp@Sitrus Berry, Aerodactyl+Sylveon, Grimmsnarl+Politoed@Life Orb, Garchomp+Toxapex, Araquanid+Golisopod@Golisopite, Pelipper+Grimmsnarl@Light Clay, Aegislash+Garchomp, Sylveon+Venusaur@Life Orb, Araquanid+Golisopod, Pelipper+Sableye@Light Clay, Golisopod+Grimmsnarl@Light Clay, Altaria+Gengar@Gengarite, Grimmsnarl+Pelipper, Politoed+Incineroar@Chople Berry, Archaludon+Pelipper@Life Orb, Altaria+Gengar, Archaludon+Vivillon@Focus Sash, Grimmsnarl+Golisopod@Golisopite, Archaludon+Rillaboom@Eject Button, Archaludon+Manectric, Archaludon+Manectric@Manectite, Golisopod+Armarouge@Psychic Seed, Swampert+Sinistcha@Sitrus Berry, Golisopod+Grimmsnarl, Kommo-o+Gengar@Gengarite, Archaludon+Venusaur@Focus Sash, Golisopod+Sirfetch’d@Leek, Archaludon+Vivillon, Gengar+Kommo-o, Ampharos+Farigiraf, Farigiraf+Ampharos@Ampharosite, Whimsicott+Garchomp@Life Orb, Meganium+Indeedee-F@Rocky Helmet, Golisopod+Armarouge@Twisted Spoon, Farigiraf+Mawile, Farigiraf+Mawile@Mawilite, Aegislash+Sylveon@Fairy Feather, Farigiraf+Kangaskhan, Aegislash+Sylveon, Aerodactyl+Farigiraf@Sitrus Berry, Toxapex+Incineroar@Sitrus Berry, Politoed+Archaludon@Chople Berry, Farigiraf+Incineroar@Life Orb, Venusaur+Annihilape@Choice Scarf, Golisopod+Pelipper@Focus Sash, Pelipper+Venusaur, Aerodactyl+Lucario@Lucarionite Z, Archaludon+Incineroar@Passho Berry, Aerodactyl+Lucario, Farigiraf+Primarina@Life Orb, Aegislash+Charizard@Charizardite Y, Farigiraf+Aerodactyl@Aerodactylite, Pelipper+Sableye@Roseli Berry, Farigiraf+Incineroar@Expert Belt, Sableye+Archaludon@Leftovers, Garchomp+Venusaur@Life Orb, Aegislash+Charizard, Garchomp+Aerodactyl@Aerodactylite, Archaludon+Sableye, Aegislash+Garchomp@Garchompite Z, Archaludon+Politoed@Life Orb, Gardevoir+Garchomp@Sitrus Berry, Golisopod+Pelipper@Life Orb, Swampert+Annihilape@Choice Scarf, Politoed+Golisopod@Golisopite, Swampert+Farigiraf@Colbur Berry, Pelipper+Venusaur@Venusaurite, Golisopod+Politoed, Annihilape+Venusaur@Focus Sash, Archaludon+Sableye@Roseli Berry, Politoed+Farigiraf@Sitrus Berry, Farigiraf+Grapploct, Pelipper+Golisopod@Golisopite, Charizard+Pelipper@Sitrus Berry, Golisopod+Pelipper, Golisopod+Kleavor@Choice Scarf, Aerodactyl+Garchomp, Garchomp+Venusaur@Wide Lens, Sirfetch’d+Golisopod@Golisopite, Golisopod+Sirfetch’d, Golisopod+Archaludon@Leftovers, Volcarona+Garchomp@Garchompite Z, Charizard+Kingambit@Focus Sash, Farigiraf+Kingambit@Focus Sash, Sableye+Sinistcha, Pelipper+Sableye, Primarina+Farigiraf@Colbur Berry, Archaludon+Golisopod@Golisopite, Archaludon+Golisopod, Gengar+Dragapult@Life Orb, Venusaur+Archaludon@Leftovers, Sylveon+Toxapex@Leftovers, Gardevoir+Aerodactyl@Focus Sash, Incineroar+Farigiraf@Twisted Spoon, Grimmsnarl+Farigiraf@Sitrus Berry, Garchomp+Aerodactyl@Focus Sash, Baxcalibur+Farigiraf@Colbur Berry, Golisopod+Farigiraf@Sitrus Berry, Golisopod+Staraptor@Choice Scarf, Indeedee+Venusaur@Life Orb, Golisopod+Rotom-Heat@Sitrus Berry, Charizard+Garchomp@Sitrus Berry, Golisopod+Farigiraf@Grassy Seed, Gengar+Archaludon@Leftovers, Dragapult+Gengar@Gengarite, Toxapex+Sylveon@Fairy Feather, Archaludon+Venusaur, Grimmsnarl+Sinistcha@Sitrus Berry, Dragapult+Gengar, Farigiraf+Venusaur@Venusaurite, Pelipper+Farigiraf@Colbur Berry, Sylveon+Garchomp@Life Orb, Farigiraf+Golisopod, Farigiraf+Golisopod@Golisopite, Archaludon+Gengar@Gengarite, Farigiraf+Sirfetch’d@Leek, Archaludon+Gengar, Sylveon+Toxapex, Garchomp+Kingambit@Focus Sash, Hatterene+Farigiraf@Sitrus Berry, Aerodactyl+Farigiraf, Garchomp+Rotom-Wash, Incineroar+Toxapex@Leftovers, Pelipper+Basculegion@Choice Scarf, Golisopod+Farigiraf@Colbur Berry, Farigiraf+Hatterene@Life Orb, Golisopod+Pelipper@Sitrus Berry, Pelipper+Sinistcha@Sitrus Berry, Incineroar+Aegislash@Focus Sash, Charizard+Farigiraf@Sitrus Berry, Annihilape+Swampert@Swampertite, Garchomp+Whimsicott@Occa Berry, Farigiraf+Incineroar@Leftovers, Annihilape+Venusaur, Farigiraf+Hatterene, Incineroar+Vivillon, Basculegion+Meganium, Basculegion+Meganium@Meganiumite, Archaludon+Venusaur@Venusaurite, Typhlosion-Hisui+Garchomp@Garchompite Z, Absol+Aerodactyl, Aerodactyl+Absol@Absolite Z, Incineroar+Vivillon@Focus Sash, Incineroar+Toxapex, Swampert+Volcarona@Rocky Helmet, Gengar+Rillaboom@Occa Berry, Sirfetch’d+Farigiraf@Sitrus Berry, Charizard+Whimsicott@Focus Sash, Glimmora+Garchomp@Life Orb, Whimsicott+Garchomp@Choice Scarf, Farigiraf+Politoed, Indeedee-F+Meowstic-F, Indeedee-F+Meowstic-F@Meowsticite, Incineroar+Politoed@Sitrus Berry, Armarouge+Mawile, Armarouge+Mawile@Mawilite, Garchomp+Charizard@Charizardite Y, Farigiraf+Grimmsnarl@Light Clay, Pelipper+Rillaboom@Expert Belt, Annihilape+Swampert, Lucario+Aerodactyl@Aerodactylite, Charizard+Aerodactyl@Focus Sash, Grapploct+Golisopod@Golisopite, Charizard+Garchomp, Golisopod+Grapploct, Pelipper+Raichu@Raichunite X, Archaludon+Raichu@Raichunite X, Aerodactyl+Garchomp@Choice Scarf, Farigiraf+Grimmsnarl, Talonflame+Garchomp@Life Orb, Golisopod+Incineroar@Chople Berry, Golisopod+Sinistcha@Kasib Berry, Swampert+Politoed@Sitrus Berry, Golisopod+Archaludon@Chople Berry, Charizard+Whimsicott@Occa Berry, Scovillain+Pelipper@Focus Sash, Mawile+Farigiraf@Sitrus Berry, Incineroar+Gengar@Gengarite, Farigiraf+Meganium, Farigiraf+Meganium@Meganiumite, Kingambit+Aerodactyl@Aerodactylite, Gengar+Incineroar, Sylveon+Garchomp@Choice Scarf, Garchomp+Volcarona@Grassy Seed, Kingambit+Meowstic-F, Kingambit+Meowstic-F@Meowsticite, Charizard+Whimsicott, Whimsicott+Charizard@Charizardite Y, Glimmora+Garchomp@Choice Scarf, Kommo-o+Politoed@Sitrus Berry, Charizard+Indeedee@Focus Sash, Torkoal+Farigiraf@Colbur Berry, Politoed+Kommo-o@Leftovers, Golisopod+Rillaboom@Leftovers, Farigiraf+Sirfetch’d, Charizard+Archaludon@Leftovers, Lucario+Garchomp@Garchompite Z, Farigiraf+Torkoal@Charcoal, Farigiraf+Charizard@Charizardite Y, Ampharos+Incineroar, Incineroar+Ampharos@Ampharosite, Farigiraf+Primarina, Sableye+Golisopod@Golisopite, Golisopod+Sableye, Politoed+Charizard@Charizardite Y, Farigiraf+Garchomp@Life Orb, Farigiraf+Torkoal, Farigiraf+Incineroar@Chople Berry, Charizard+Farigiraf, Garchomp+Farigiraf@Grassy Seed, Swampert+Gengar@Gengarite, Sylveon+Aerodactyl@Focus Sash, Gardevoir+Venusaur@Focus Sash, Gengar+Swampert, Charizard+Politoed, Garchomp+Whimsicott@Focus Sash, Archaludon+Charizard@Charizardite Y, Grimmsnarl+Pelipper@Focus Sash, Archaludon+Farigiraf@Sitrus Berry, Golisopod+Swampert@Swampertite, Archaludon+Charizard, Pelipper+Annihilape@Choice Scarf, Garchomp+Volcarona@Rocky Helmet, Garchomp+Whimsicott, Froslass+Politoed@Sitrus Berry, Basculegion+Pelipper@Focus Sash, Kingambit+Garchomp@Life Orb, Garchomp+Corviknight@Leftovers, Hatterene+Golisopod@Golisopite, Golisopod+Hatterene, Sylveon+Farigiraf@Sitrus Berry, Metagross+Garchomp@Garchompite Z, Aerodactyl+Kingambit, Kingambit+Garchomp@Garchompite, Swampert+Golisopod@Golisopite, Golisopod+Swampert, Aegislash+Incineroar@Sitrus Berry, Garchomp+Kleavor@Focus Sash, Politoed+Rillaboom@Occa Berry, Politoed+Sableye, Floette-Eternal+Garchomp@Sitrus Berry, Incineroar+Garchomp@Garchompite, Indeedee-F+Aerodactyl@Focus Sash, Archaludon+Volcarona@Sitrus Berry, Archaludon+Annihilape@Choice Scarf, Garchomp+Volcarona, Hydreigon+Pelipper@Sitrus Berry, Sinistcha+Swampert@Swampertite, Farigiraf+Archaludon@Leftovers, Venusaur+Torkoal@Charcoal, Golisopod+Basculegion@Choice Scarf, Garchomp+Glimmora@Focus Sash, Garchomp+Venusaur, Charizard+Politoed@Life Orb, Golisopod+Sinistcha@Sitrus Berry, Archaludon+Incineroar@Chople Berry, Archaludon+Meganium, Archaludon+Meganium@Meganiumite, Annihilape+Pelipper@Sitrus Berry, Farigiraf+Blaziken@Blazikenite, Golisopod+Hatterene@Life Orb, Garchomp+Typhlosion-Hisui@Choice Scarf, Pelipper+Gholdengo@Choice Scarf, Empoleon+Garchomp, Floette-Eternal+Garchomp@Choice Scarf, Golisopod+Indeedee-F@Colbur Berry, Archaludon+Farigiraf, Mawile+Indeedee-F@Colbur Berry, Armarouge+Gengar@Gengarite, Archaludon+Farigiraf@Colbur Berry, Gengar+Swampert@Swampertite, Armarouge+Gengar, Meowstic-F+Sneasler, Sneasler+Meowstic-F@Meowsticite, Garchomp+Talonflame, Sinistcha+Swampert, Sylveon+Charizard@Charizardite Y, Charizard+Sylveon@Fairy Feather, Farigiraf+Indeedee-F@Psychic Seed, Swampert+Rillaboom@Eject Button, Farigiraf+Primarina@Leftovers, Ninetales-Alola+Gengar@Gengarite, Torkoal+Venusaur, Charizard+Annihilape@Choice Scarf, Charizard+Sylveon, Gengar+Ninetales-Alola, Staraptor+Garchomp@Sitrus Berry, Indeedee-F+Meganium, Indeedee-F+Meganium@Meganiumite, Garchomp+Volcarona@Sitrus Berry, Incineroar+Venusaur@Wide Lens, Armarouge+Golisopod@Golisopite, Armarouge+Golisopod, Gardevoir+Venusaur, Venusaur+Gardevoir@Gardevoirite, Pelipper+Indeedee-F@Colbur Berry, Rotom-Heat+Golisopod@Golisopite, Golisopod+Rotom-Heat, Kleavor+Golisopod@Golisopite, Gholdengo+Garchomp@Sitrus Berry, Golisopod+Kleavor, Dragonite+Pelipper@Sitrus Berry, Delphox+Garchomp@Choice Scarf, Garchomp+Farigiraf@Sitrus Berry, Kommo-o+Politoed, Rillaboom+Farigiraf@Grassy Seed, Rillaboom+Swampert@Sitrus Berry, Incineroar+Mawile, Incineroar+Mawile@Mawilite, Charizard+Swampert@Swampertite, Golisopod+Rillaboom@Expert Belt, Venusaur+Indeedee-F@Colbur Berry, Farigiraf+Sylveon@Fairy Feather, Politoed+Pawmot@Focus Sash, Golisopod+Annihilape@Choice Scarf, Incineroar+Garchomp@Garchompite Z, Swampert+Kommo-o@Leftovers, Farigiraf+Sylveon, Politoed+Swampert, Incineroar+Venusaur@Life Orb, Politoed+Swampert@Swampertite, Basculegion+Pelipper, Farigiraf+Pawmot@Focus Sash, Blaziken+Farigiraf@Sitrus Berry, Blaziken+Farigiraf, Pelipper+Charizard@Charizardite Y, Garchomp+Typhlosion-Hisui, Farigiraf+Garchomp, Farigiraf+Garchomp@Garchompite Z, Charizard+Pelipper, Gardevoir+Pelipper@Focus Sash, Kingambit+Garchomp@Choice Scarf, Pawmot+Farigiraf@Sitrus Berry, Garchomp+Glimmora, Venusaur+Sneasler@Psychic Seed, Torkoal+Garchomp@Garchompite Z, Maushold+Gengar@Gengarite, Golisopod+Milotic@Psychic Seed, Swampert+Charizard@Charizardite Y, Archaludon+Rillaboom@Expert Belt, Farigiraf+Kingambit@Black Glasses, Basculegion+Garchomp@Sitrus Berry, Gengar+Maushold, Torkoal+Farigiraf@Sitrus Berry, Sinistcha+Pelipper@Focus Sash, Kingambit+Garchomp@Sitrus Berry, Garchomp+Incineroar@Sitrus Berry, Charizard+Swampert, Talonflame+Garchomp@Garchompite Z, Farigiraf+Pawmot, Espathra+Archaludon@Leftovers, Incineroar+Farigiraf@Colbur Berry, Mawile+Pelipper, Pelipper+Mawile@Mawilite, Golisopod+Indeedee-F@Sitrus Berry, Annihilape+Pelipper@Focus Sash, Annihilape+Pelipper, Kingambit+Meganium, Kingambit+Meganium@Meganiumite, Archaludon+Basculegion@Choice Scarf, Charizard+Golisopod@Golisopite, Incineroar+Garchomp@Choice Scarf, Charizard+Golisopod, Golisopod+Charizard@Charizardite Y, Charizard+Rillaboom@Grassy Seed, Sneasler+Garchomp@Garchompite, Archaludon+Sinistcha@Sitrus Berry, Garchomp+Sylveon@Fairy Feather, Golisopod+Dragapult@Life Orb, Gengar+Indeedee-F@Rocky Helmet, Rillaboom+Politoed@Sitrus Berry, Rillaboom+Vivillon@Focus Sash, Garchomp+Sylveon, Garchomp+Glimmora@Glimmoranite, Dragapult+Golisopod@Golisopite, Farigiraf+Pelipper@Focus Sash, Annihilape+Charizard@Charizardite Y, Garchomp+Lucario@Lucarionite Z, Dragapult+Golisopod, Pelipper+Rillaboom@Life Orb, Indeedee-F+Garchomp@Sitrus Berry, Indeedee-F+Pelipper@Focus Sash, Incineroar+Politoed, Garchomp+Lucario, Rillaboom+Vivillon, Annihilape+Golisopod@Golisopite, Golisopod+Pawmot@Focus Sash, Archaludon+Gholdengo@Choice Scarf, Annihilape+Charizard, Annihilape+Golisopod, Pawmot+Politoed, Swampert+Incineroar@Passho Berry, Archaludon+Espathra, Annihilape+Archaludon, Golisopod+Armarouge@Life Orb, Kingambit+Farigiraf@Sitrus Berry, Farigiraf+Swampert@Swampertite, Pelipper+Scovillain@Scovillainite, Garchomp+Kingambit@Occa Berry, Manectric+Rillaboom, Rillaboom+Manectric@Manectite, Garchomp+Metagross@Metagrossite, Pawmot+Garchomp@Garchompite Z, Garchomp+Incineroar@Chople Berry, Annihilape+Archaludon@Leftovers, Garchomp+Rotom-Heat, Golisopod+Indeedee-F@Psychic Seed, Garchomp+Venusaur@Focus Sash, Venusaur+Indeedee-F@Psychic Seed, Farigiraf+Swampert, Pelipper+Sinistcha, Garchomp+Metagross, Pelipper+Scovillain, Farigiraf+Garchomp@Choice Scarf, Garchomp+Kleavor, Venusaur+Basculegion@Choice Scarf, Indeedee-F+Golisopod@Golisopite, Aegislash+Incineroar, Basculegion+Garchomp@Garchompite Z, Indeedee-F+Mawile, Indeedee-F+Mawile@Mawilite, Golisopod+Indeedee-F, Farigiraf+Pelipper, Golisopod+Politoed@Sitrus Berry, Basculegion+Aerodactyl@Focus Sash, Rillaboom+Pelipper@Choice Scarf, Grimmsnarl+Farigiraf@Colbur Berry, Sylveon+Venusaur, Garchomp+Sneasler@Focus Sash, Rillaboom+Gengar@Gengarite, Garchomp+Incineroar, Pelipper+Sneasler@Psychic Seed, Farigiraf+Sableye, Gengar+Rillaboom, Incineroar+Charizard@Charizardite X, Farigiraf+Kingambit, Kingambit+Aerodactyl@Focus Sash, Camerupt+Golisopod@Golisopite, Garchomp+Kingambit, Camerupt+Golisopod, Golisopod+Camerupt@Cameruptite, Swampert+Incineroar@Chople Berry, Golisopod+Rillaboom@Grassy Seed, Venusaur+Sylveon@Fairy Feather, Pelipper+Indeedee@Focus Sash, Incineroar+Aerodactyl@Focus Sash, Aerodactyl+Incineroar@Sitrus Berry, Primarina+Farigiraf@Sitrus Berry, Incineroar+Farigiraf@Grassy Seed, Charizard+Kingambit@Occa Berry, Sinistcha+Garchomp@Choice Scarf, Pawmot+Golisopod@Golisopite, Farigiraf+Incineroar@Rocky Helmet, Golisopod+Rillaboom@Life Orb, Golisopod+Pawmot, Venusaur+Garchomp@Garchompite Z, Sneasler+Charizard@Charizardite X, Garchomp+Floette-Eternal@Floettite, Floette-Eternal+Garchomp, Garchomp+Basculegion@Life Orb, Basculegion+Sableye, Hydreigon+Charizard@Charizardite Y, Indeedee-F+Venusaur@Focus Sash, Golisopod+Garchomp@Garchompite Z, Garchomp+Rillaboom@Grassy Seed, Charizard+Hydreigon, Golisopod+Primarina@Life Orb, Rillaboom+Archaludon@Chople Berry, Sinistcha+Grimmsnarl@Light Clay, Pelipper+Sinistcha@Colbur Berry, Kleavor+Charizard@Charizardite Y, Mawile+Incineroar@Sitrus Berry, Garchomp+Scovillain@Scovillainite, Sinistcha+Golisopod@Golisopite, Golisopod+Sinistcha, Garchomp+Politoed@Life Orb, Gengar+Incineroar@Sitrus Berry, Arcanine-Hisui+Farigiraf@Grassy Seed, Charizard+Indeedee-F@Sitrus Berry, Swampert+Farigiraf@Sitrus Berry, Indeedee-F+Venusaur, Charizard+Kleavor, Tsareena+Gholdengo@Life Orb, Gengar+Kingambit@Black Glasses, Sneasler+Garchomp@Garchompite Z, Golisopod+Venusaur@Focus Sash, Rillaboom+Garchomp@Garchompite Z, Grimmsnarl+Sinistcha, Gardevoir+Garchomp@Life Orb
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Archaludon | 60.0% | spa-drop 28.4% |
| Farigiraf | 42.0% | priority-blocker 99.5%, trick-room-setter 98.1%, helping-hand 49.8%, trick-room-abuser 33.8%, disruption 6.1%, weather-setter 5.0%, setup 2.9%, ally-switch 1.8%, screens 0.9%, terrain-setter 0.9%, speed-drop 0.2% |
| Golisopod | 40.6% | mega-attacker 99.7%, setup 43.4%, priority-attack 36.3%, trick-room-abuser 32.2%, pivot 2.5%, wide-guard 1.1% |
| Pelipper | 39.4% | weather-setter 100.0%, tailwind 85.9%, wide-guard 67.6%, trick-room-abuser 2.4%, helping-hand 1.4%, pivot 1.2%, speed-drop 0.8% |
| Charizard | 37.4% | mega-attacker 100.0%, weather-setter 98.3%, setup 1.7%, helping-hand 1.5% |
| Garchomp | 29.7% | mega-attacker 49.0%, speed-drop 8.7%, setup 2.2%, weather-setter 0.5% |
| Politoed | 28.7% | weather-setter 100.0%, perish-song 46.0%, disruption 33.5%, trick-room-abuser 8.3%, speed-drop 7.6%, helping-hand 6.8%, status 6.4% |
| Grimmsnarl | 21.0% | prankster 100.0%, screens 98.4%, pivot 96.5%, spa-drop 94.0%, trick-room-abuser 46.8%, fake-out 5.7%, priority-attack 1.6%, disruption 1.6%, speed-drop 0.5% |
| Swampert | 17.0% | mega-attacker 92.1%, wide-guard 6.0%, pivot 5.9%, status 4.6%, trick-room-abuser 4.3%, weather-setter 1.0%, helping-hand 0.7%, setup 0.7% |
| Gengar | 16.2% | mega-attacker 91.9%, perish-song 68.4%, disruption 7.4%, speed-drop 7.2%, status 1.6%, setup 0.7% |
| Venusaur | 13.0% | status 76.5%, mega-attacker 9.1% |
| Aerodactyl | 9.1% | tailwind 99.0%, mega-attacker 75.4%, wide-guard 46.1%, disruption 2.8% |
| Vivillon | 5.5% | status 100.0%, rage-powder 95.1%, tailwind 7.8% |
| Sableye | 3.7% | prankster 91.0%, screens 72.3%, weather-setter 65.7%, disruption 41.4%, status 36.2%, trick-room-abuser 32.8%, fake-out 28.2%, spa-drop 7.5%, setup 5.3%, helping-hand 3.7%, speed-drop 3.7%, mega-attacker 2.6% |
| Toxapex | 1.9% | status 100.0%, wide-guard 95.0%, trick-room-abuser 38.3% |
| Mawile | 1.9% | mega-attacker 100.0%, priority-attack 94.1%, trick-room-abuser 77.5%, intimidate 31.0%, setup 20.3% |
| Meganium | 1.6% | mega-attacker 100.0% |
| Aegislash | 1.2% | wide-guard 59.6%, priority-attack 57.5% |
| Tsareena | 0.8% | priority-blocker 100.0%, disruption 28.1%, helping-hand 9.0%, pivot 6.4% |
| Kangaskhan | 0.6% | fake-out 100.0%, priority-attack 18.5%, mega-attacker 18.5% |
| Ampharos | 0.6% | mega-attacker 100.0%, trick-room-abuser 64.6%, setup 50.0%, speed-drop 14.6% |
| Manectric | 0.5% | intimidate 100.0%, mega-attacker 100.0%, spa-drop 82.8%, pivot 82.8%, screens 17.2% |
| Meowstic-F | 0.1% | mega-attacker 100.0%, fake-out 87.7%, setup 6.0% |
- Representative teams (primary teams with the highest score for this community):
  - [Alessandro Gulino, 136th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0399/teamlist)
  - [Andreas Haumann, 354th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0401/teamlist)
  - [William Passmark, 450th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0674/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Aaron Lupp, 591st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0967/teamlist)
  - [Vincent VILLIERS, 523rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0689/teamlist)
  - [Nedim Thull, 517th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0773/teamlist)
  - [m6hn67, , 11 Sep 2026](https://pokepast.es/bb66ff17a1c4a911)
  - [David Sakulov, 1084th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0645/teamlist)
  - [_hendez_, , 21 Sep 2026](https://pokepast.es/2d99e9105e5f7a58)
  - [Henry Hernandez, 339th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0377/teamlist)
  - [Riley J, 601st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0542/teamlist)
  - [Jordi Martinez, 469th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0729/teamlist)
  - [Kevin Klöckner, 876th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0919/teamlist)
  - [Michala Dennis, 259th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0714/teamlist)
  - [Ryne Morgan, 834th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0836/teamlist)
  - [Will Connor, 174th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0306/teamlist)
  - [Ryan Caldwell, 199th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0325/teamlist)
  - [Anthony Mubiala, 562nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0423/teamlist)
  - [Julian Wanis, 733rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1003/teamlist)
  - [Santino Tarquinio, 173rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0204/teamlist)
  - [Thaddeus Valentine, 258th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0727/teamlist)
  - [Danial Syed, 453rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0658/teamlist)
  - [Daniel Marcelo Reyes, 295th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0227/teamlist)
  - [Mateo Vasic, 961st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0767/teamlist)
  - [Nelson Fonkoua, 209th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0653/teamlist)
  - [Roman Atanasiu Alonso, 1062nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0378/teamlist)
  - [Frederic de Longueville, 859th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0952/teamlist)
  - [Matthew Davis, 987th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0674/teamlist)
  - [Rayan GUEZI, 1035th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0206/teamlist)
  - [Paschalis Dermentzis, 20th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0846/teamlist)
  - [Lazaros Lazaropoulos, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0813/teamlist)
  - [Charalampos Frimas, 185th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0069/teamlist)
  - [m_rada13, Champion, 11 Sep 2026](https://pokepast.es/3ac336df2eb4729b)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/60458cc1a20c2440)
  - [Emery Joseph, 43rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0004/teamlist)
  - [Alex Thompson, 400th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0115/teamlist)
  - [Ryan Gordon, 508th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0410/teamlist)
  - [cbadgg, , 20 Sep 2026](https://pokepast.es/120461d751b66e3a)
  - [Chase Badish, 364th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0876/teamlist)
  - [Audric David, 70th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0667/teamlist)
  - [Max Doebeli, 157th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0064/teamlist)
  - [Michael Cenatiempo, 693rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0202/teamlist)
  - [Felix Renaud-Chartier, 768th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0878/teamlist)
  - [Tz Cogao, , 10 Sep 2026](https://pokepast.es/f828e7b46a771515)
  - [Jairo Contreras, 327th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0215/teamlist)
  - [Matt Francis, 325th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0955/teamlist)
  - [Tyler Coady, 427th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0376/teamlist)
  - [Benjamin Lavigne, 918th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0678/teamlist)
  - [William Pye, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0229/teamlist)
  - [Brandon Harrison, 486th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0185/teamlist)
  - [Teodoro Castellon, 413th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0182/teamlist)
  - [Aden Carver, 926th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0406/teamlist)
  - [Jack Kent, 934th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0725/teamlist)
  - [Wafeeq Khan, 1047th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0687/teamlist)
  - [joshg0tti1, , 11 Sep 2026](https://pokepast.es/4c6eb1d0d2cbb3f3)
  - [Josh Schulster, 792nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0627/teamlist)
  - [Kyle Jones, 935th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0945/teamlist)
  - [Daniel walker, 6th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0315/teamlist)
  - [Andrew Levy, 467th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0601/teamlist)
  - [Ignacio Campos Jimenez, 279th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0476/teamlist)
  - [MeK191817, , 11 Sep 2026](https://pokepast.es/a1338edf35719660)
  - [Tim Beyreuther, 621st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0731/teamlist)
  - [Jason Romero, 915th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0564/teamlist)
  - [rioreumi, , 9 Sep 2026](https://pokepast.es/07e91402b74462ef)
  - [Jordan Goggin, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0089/teamlist)
  - [Michael Koenigsberg, 938th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0368/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/7b073199fc857b04)
  - [Seth Ellsworth, 586th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0058/teamlist)
  - [Braden Hood, 584th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0091/teamlist)
  - [Noel Marquez, 645th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0303/teamlist)
  - [Curtis Ridings, 128th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0184/teamlist)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [ribe88, , 13 Sep 2026](https://pokepast.es/a702bf47f522438b)
  - [Reynaldo Boles, 171st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0064/teamlist)
  - [Yu Xiang, 41st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1043/teamlist)
  - [Sascha Eilts, 372nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0025/teamlist)
  - [Nick Theunis, 677th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0916/teamlist)
  - [Julian Pluta, 925th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0271/teamlist)
  - [Chenyi Tao, 985th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0050/teamlist)
  - [Josh Hamilton, 319th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0021/teamlist)
  - [Alexander Ballin, 355th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0173/teamlist)
  - [Steven Stark, 550th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0074/teamlist)
  - [Gerald Braden, 642nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0488/teamlist)
  - [Clay McGill, 728th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0054/teamlist)
  - [KaSun Thompson, 238th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0906/teamlist)
  - [Shuji ENDO, 10th, 13 Sep 2026](https://pokepast.es/e74e8282d41a3802)
  - [Yuta Ishigaki, , 12 Sep 2026](https://pokepast.es/668502969512b159)
  - [PiyoLily145, , 9 Sep 2026](https://pokepast.es/8e37c3b00cbba6b6)
  - [Nikita Gnatenko, 679th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0997/teamlist)
  - [Michell Osew, 724th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0153/teamlist)
  - [Jeremy Ortiz, 976th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0077/teamlist)
  - [Brandon Ebert, 1029th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0909/teamlist)
  - [Semih Erdogdu, 350th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0313/teamlist)
  - [London Faust, 80th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0495/teamlist)
  - [Lance Lee, 1028th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0478/teamlist)
  - [silversandbag, , 13 Sep 2026](https://pokepast.es/3c4611ccba18d35a)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [Kazeno_shion, , 10 Sep 2026](https://pokepast.es/e638bc044d3cb47f)
  - [Escen Schaferin, 880th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0082/teamlist)
  - [Keigo Tanizawa, 7th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0175/teamlist)
  - [Padrick Moran, 748th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1071/teamlist)
  - [Wesley Brainard, 201st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0114/teamlist)
  - [Vito Jacono, 825th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0175/teamlist)
  - [joserockzvgc, , 16 Sep 2026](https://pokepast.es/eb4962af8162ecf3)
  - [Louis Milich, 304th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0501/teamlist)
  - [Kat Wible, 941st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0080/teamlist)
  - [Logan Hall, 320th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0850/teamlist)
  - [Sophie Corsentino, 734th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0325/teamlist)
  - [Jacob Curnett, 566th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0959/teamlist)
  - [Nate Innocenti, , 10 Sep 2026](https://pokepast.es/af730dd6acb60086)
  - [Ignatius Lee, 159th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0091/teamlist)
  - [Niklas Hauser, 398th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0075/teamlist)
  - [Ross Stewart, 234th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0622/teamlist)
  - [Mario Giuseppe Vincitorio, 252nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0458/teamlist)
  - [Heber Henriquez, 805th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0902/teamlist)
  - [Jetrick Gelacio, 111th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0782/teamlist)
  - [Simon Batty, 812th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0066/teamlist)
  - [mofumofunatsuhi, , 13 Sep 2026](https://pokepast.es/f85b026e5b0e6567)
  - [Ali Pütün, 742nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0084/teamlist)
  - [David Durán, 1012th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0374/teamlist)
  - [clark smith, 1011th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0116/teamlist)
  - [Jayson Lyon, 837th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0516/teamlist)
  - [Duy Thang Nguyen, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0617/teamlist)
  - [Lillian Heath, 767th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1067/teamlist)
  - [kaki, , 13 Sep 2026](https://pokepast.es/a202a04735494175)
  - [Tom de Gruijter, 217th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1093/teamlist)
  - [Ryan Yost, 877th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1042/teamlist)
  - [Joshua Moloney, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0143/teamlist)
  - [gravity030, , 9 Sep 2026](https://pokepast.es/2f512d91f56e0830)
  - [Daniel Miguel mirapeix, 1113th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0806/teamlist)
  - [Michael Mullen, 666th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0282/teamlist)
  - [Joshua Flickinger, 838th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0567/teamlist)
  - [Sebastian Abenza Homberger, 711th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0285/teamlist)
  - [Evan Schulz, 1013th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0394/teamlist)
  - [lj_darkrai, , 18 Sep 2026](https://pokepast.es/817ae21505e679f7)
  - [Arnout Bruijn, 1006th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0236/teamlist)
  - [Avery Dworek, 480th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0228/teamlist)
  - [Mark Morales, 661st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0827/teamlist)
  - [Chloe Bourke, 70th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0262/teamlist)
  - [Cameron McHeyzer, 98th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0079/teamlist)
  - [Alexander Kremer, 923rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0265/teamlist)
  - [Rens Heylen, 296th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0101/teamlist)

- Sub-community pass: 637 primary teams, 153 tokens, modularity 0.51; unconnected tokens: Toxtricity (other item; Life Orb 5/6), Tyranitar (other item; Choice Scarf 2/6), Absol@Absolite Z, Empoleon (other item; Sitrus Berry 3/5), Maushold (other item; Focus Sash 2/5), Rillaboom@Grassy Seed, Glimmora (other item; Focus Sash 3/4), Kangaskhan (other item; Chople Berry 1/4), Kleavor (other item; Choice Scarf 2/4), Ninetales-Alola (other item; Choice Scarf 2/4), Noivern (other item; Focus Sash 4/4), Overqwil (other item; Life Orb 2/4), Scovillain (other item; Scovillainite 4/4), Talonflame (other item; Focus Sash 2/4), Arcanine-Hisui (other item; Focus Sash 3/3), Blaziken (other item; Focus Sash 2/3), Clefable (other item; Sitrus Berry 3/3), Drampa (other item; Drampanite 2/3), Gallade (other item; Focus Sash 1/3), Grapploct (other item; Life Orb 2/3), Hatterene (other item; Focus Sash 2/3), Lycanroc-Dusk (other item; Focus Sash 3/3), Meowscarada (other item; Choice Scarf 1/3), Pincurchin (other item; Air Balloon 1/3), Pyroar (other item; Pyroarite 3/3), Starmie (other item; Starminite 3/3); unassigned within the community: 5 teams (0.9% of its primary weight); hybrid teams of the community left out: 139
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 3 (0.69) | 4 (0.60) | 5 (0.51) | 6 (0.45) | 7 (0.40) |

#### Community 4 / Sub-community 0: Rain (Mega Golisopod) (224 primary teams, 159 distinct builds, top pair on 206/224)
- Megas on member teams: Golisopod 116, Swampert 92, Charizard-Y 41, Salamence 38, Garchomp-Z 14, Metagross 9
- Top species by team share: Archaludon 96%, Pelipper 95%, Golisopod 52%, Swampert 41%, Grimmsnarl 28%, Rillaboom 27%
- Token label: Archaludon / Pelipper
- Mode tags on primary teams: Rain 220, Tailwind 197, Screens 75, Sun 41, Trick Room 37, Psyspam 24, Setup 8, Snow 6, Sand 1
- Primary teams: 224 (34.4% of the community's primary weight), hybrid teams: 96 (16.3%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Indeedee@Choice Scarf+Sneasler@Psychic Seed, Indeedee (other item; Focus Sash 7/7)+Sneasler@Psychic Seed, Indeedee-F (other item; Rocky Helmet 22/41)+Gardevoir@Gardevoirite, Armarouge (other item; Life Orb 4/6)+Indeedee-F (other item; Rocky Helmet 22/41), Dragonite@Dragoninite+Metagross@Metagrossite, Dragonite@Dragoninite+Sneasler@Psychic Seed, Gholdengo (other item; Life Orb 16/25)+Pawmot (other item; Focus Sash 13/15), Indeedee-F (other item; Rocky Helmet 22/41)+Sneasler@Psychic Seed, Indeedee-F (other item; Rocky Helmet 22/41)+Meganium@Meganiumite, Aerodactyl (other item; Focus Sash 11/12)+Indeedee-F (other item; Rocky Helmet 22/41), Pawmot (other item; Focus Sash 13/15)+Salamence@Salamencite, Indeedee-F (other item; Rocky Helmet 22/41)+Metagross@Metagrossite, Basculegion@Choice Scarf+Salamence@Salamencite, Gholdengo (other item; Life Orb 16/25)+Whimsicott (other item; Focus Sash 30/34), Sableye@Light Clay+Swampert@Swampertite, Basculegion@Choice Scarf+Metagross@Metagrossite, Gholdengo (other item; Life Orb 16/25)+Salamence@Salamencite, Sinistcha (other item; Sitrus Berry 9/28)+Swampert@Swampertite, Sableye (other item; Roseli Berry 7/11)+Swampert@Swampertite, Gholdengo (other item; Life Orb 16/25)+Swampert@Swampertite, Venusaur (other item; Focus Sash 48/72)+Annihilape@Choice Scarf, Annihilape@Choice Scarf+Swampert@Swampertite, Rillaboom (other item; Miracle Seed 103/149)+Salamence@Salamencite, Dragapult (other item; Life Orb 7/9)+Pelipper (other item; Focus Sash 134/259), Pelipper (other item; Focus Sash 134/259)+Swampert@Swampertite, Pelipper (other item; Focus Sash 134/259)+Sneasler@Psychic Seed, Kingambit (other item; Focus Sash 52/116)+Gardevoir@Gardevoirite, Pelipper (other item; Focus Sash 134/259)+Indeedee@Choice Scarf, Sneasler (other item; White Herb 38/47)+Basculegion@Choice Scarf, Armarouge (other item; Life Orb 4/6)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 134/259)+Meganium@Meganiumite, Pelipper (other item; Focus Sash 134/259)+Salamence@Salamencite, Pelipper (other item; Focus Sash 134/259)+Raichu@Raichunite X, Indeedee-F (other item; Rocky Helmet 22/41)+Sneasler (other item; White Herb 38/47), Armarouge (other item; Life Orb 4/6)+Pelipper (other item; Focus Sash 134/259), Indeedee (other item; Focus Sash 7/7)+Pelipper (other item; Focus Sash 134/259), Pelipper (other item; Focus Sash 134/259)+Basculegion@Choice Scarf, Venusaur (other item; Focus Sash 48/72)+Swampert@Swampertite, Venusaur (other item; Focus Sash 48/72)+Sneasler@Psychic Seed, Rillaboom (other item; Miracle Seed 103/149)+Annihilape@Choice Scarf, Rillaboom (other item; Miracle Seed 103/149)+Basculegion@Choice Scarf, Gholdengo (other item; Life Orb 16/25)+Garchomp@Garchompite Z, Golisopod@Golisopite+Sableye@Light Clay, Pelipper (other item; Focus Sash 134/259)+Sinistcha (other item; Sitrus Berry 9/28), Archaludon (other item; Leftovers 350/374)+Espathra (other item; Colbur Berry 1/4), Archaludon (other item; Leftovers 350/374)+Manectric (other item; Manectite 4/4), Archaludon (other item; Leftovers 350/374)+Blastoise (other item; Blastoisinite 4/4), Archaludon (other item; Leftovers 350/374)+Raichu@Raichunite X, Pelipper (other item; Focus Sash 134/259)+Sneasler@Grassy Seed, Grimmsnarl@Light Clay+Swampert@Swampertite, Rillaboom (other item; Miracle Seed 103/149)+Sableye (other item; Roseli Berry 7/11), Archaludon (other item; Leftovers 350/374)+Grimmsnarl@Light Clay, Sinistcha (other item; Sitrus Berry 9/28)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 134/259)+Raichu@Raichunite Y, Indeedee-F (other item; Rocky Helmet 22/41)+Pelipper (other item; Focus Sash 134/259), Dragapult (other item; Life Orb 7/9)+Golisopod@Golisopite, Archaludon (other item; Leftovers 350/374)+Pelipper (other item; Focus Sash 134/259), Archaludon (other item; Leftovers 350/374)+Swampert@Swampertite, Gholdengo (other item; Life Orb 16/25)+Pelipper (other item; Focus Sash 134/259), Pelipper (other item; Focus Sash 134/259)+Metagross@Metagrossite, Pelipper (other item; Focus Sash 134/259)+Staraptor@Staraptite, Pelipper (other item; Focus Sash 134/259)+Annihilape@Choice Scarf, Pelipper (other item; Focus Sash 134/259)+Grimmsnarl@Light Clay, Pelipper (other item; Focus Sash 134/259)+Venusaur (other item; Focus Sash 48/72), Archaludon (other item; Leftovers 350/374)+Sinistcha (other item; Sitrus Berry 9/28), Archaludon (other item; Leftovers 350/374)+Dragapult (other item; Life Orb 7/9), Charizard@Charizardite Y+Gardevoir@Gardevoirite, Salamence@Salamencite+Swampert@Swampertite, Archaludon (other item; Leftovers 350/374)+Sneasler@Psychic Seed, Pelipper (other item; Focus Sash 134/259)+Dragonite@Dragoninite, Archaludon (other item; Leftovers 350/374)+Sableye@Light Clay, Archaludon (other item; Leftovers 350/374)+Salamence@Salamencite, Hydreigon (other item; Focus Sash 5/8)+Pelipper (other item; Focus Sash 134/259), Pelipper (other item; Focus Sash 134/259)+Sableye@Light Clay, Basculegion@Choice Scarf+Golisopod@Golisopite, Archaludon (other item; Leftovers 350/374)+Politoed (other item; Sitrus Berry 82/172), Archaludon (other item; Leftovers 350/374)+Armarouge (other item; Life Orb 4/6), Pelipper (other item; Focus Sash 134/259)+Sneasler (other item; White Herb 38/47), Indeedee-F (other item; Rocky Helmet 22/41)+Swampert@Swampertite, Rillaboom (other item; Miracle Seed 103/149)+Dragonite@Dragoninite, Archaludon (other item; Leftovers 350/374)+Indeedee@Choice Scarf, Pelipper (other item; Focus Sash 134/259)+Indeedee-F@Psychic Seed, Indeedee-F (other item; Rocky Helmet 22/41)+Basculegion@Choice Scarf, Annihilape@Choice Scarf+Charizard@Charizardite Y, Archaludon (other item; Leftovers 350/374)+Rillaboom@Eject Button, Archaludon (other item; Leftovers 350/374)+Vivillon (other item; Focus Sash 29/29), Golisopod@Golisopite+Salamence@Salamencite, Sinistcha (other item; Sitrus Berry 9/28)+Grimmsnarl@Light Clay, Pelipper (other item; Focus Sash 134/259)+Sableye (other item; Roseli Berry 7/11), Rillaboom (other item; Miracle Seed 103/149)+Metagross@Metagrossite, Archaludon (other item; Leftovers 350/374)+Basculegion@Choice Scarf, Archaludon (other item; Leftovers 350/374)+Golisopod@Golisopite, Archaludon (other item; Leftovers 350/374)+Sneasler@Grassy Seed, Archaludon (other item; Leftovers 350/374)+Metagross@Metagrossite, Pelipper (other item; Focus Sash 134/259)+Golisopod@Golisopite, Archaludon (other item; Leftovers 350/374)+Indeedee (other item; Focus Sash 7/7), Basculegion (other item; Life Orb 19/28)+Pelipper (other item; Focus Sash 134/259), Archaludon (other item; Leftovers 350/374)+Annihilape@Choice Scarf, Archaludon (other item; Leftovers 350/374)+Gengar@Gengarite, Archaludon (other item; Leftovers 350/374)+Froslass@Froslassite, Archaludon (other item; Leftovers 350/374)+Indeedee-F (other item; Rocky Helmet 22/41), Archaludon (other item; Leftovers 350/374)+Sableye (other item; Roseli Berry 7/11), Archaludon (other item; Leftovers 350/374)+Gardevoir@Gardevoirite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Archaludon (other item; Leftovers 350/374) | 96.8% |
| Pelipper (other item; Focus Sash 134/259) | 94.4% |
| Swampert@Swampertite | 41.1% |
| Basculegion@Choice Scarf | 19.5% |
| Salamence@Salamencite | 16.2% |
| Indeedee-F (other item; Rocky Helmet 22/41) | 12.5% |
| Sinistcha (other item; Sitrus Berry 9/28) | 8.5% |
| Sneasler@Psychic Seed | 8.3% |
| Gholdengo (other item; Life Orb 16/25) | 6.4% |
| Annihilape@Choice Scarf | 4.5% |
| Metagross@Metagrossite | 4.4% |
| Sableye@Light Clay | 4.3% |
| Sableye (other item; Roseli Berry 7/11) | 3.8% |
| Dragonite@Dragoninite | 3.7% |
| Indeedee-F@Psychic Seed | 3.4% |
| Meganium@Meganiumite | 3.3% |
| Dragapult (other item; Life Orb 7/9) | 3.2% |
| Indeedee (other item; Focus Sash 7/7) | 2.5% |
| Indeedee@Choice Scarf | 2.2% |
| Armarouge (other item; Life Orb 4/6) | 2.1% |
| Venusaur@Venusaurite | 2.0% |
| Gardevoir@Gardevoirite | 1.9% |
| Staraptor@Staraptite | 1.9% |
| Raichu@Raichunite X | 1.7% |
| Blastoise (other item; Blastoisinite 4/4) | 1.6% |
| Espathra (other item; Colbur Berry 1/4) | 1.3% |
| Manectric (other item; Manectite 4/4) | 1.1% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Björn Vehlow, 633rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1021/teamlist)
  - [Eddie Gunns, 919th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0294/teamlist)
  - [Trevione Ingram-Stone, 1065th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0934/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Mario Estarlich Homedes, 999th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0662/teamlist)
  - [Thaison Hughes, 128th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0568/teamlist)
  - [Foster Guerin, 219th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0207/teamlist)
  - [Haoran Shan, 377th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1022/teamlist)
  - [Max VanderGast, 414th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0541/teamlist)
  - [Bryan Tong, 502nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0841/teamlist)
  - [Jason Chase, 510th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0746/teamlist)
  - [Shane Hale, 610th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0766/teamlist)
  - [Jonah Williams, 1005th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0630/teamlist)
  - [Jacob Olenick, 269th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0493/teamlist)
  - [Kenny Burggraf, 452nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1031/teamlist)
  - [rowan_stavenow, , 20 Sep 2026](https://pokepast.es/3e44a93665f44983)
  - [Rowan Stavenow, 159th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0363/teamlist)
  - [Jarvis Eayrs, 103rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0195/teamlist)
  - [Jeremy Hollas, 119th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0008/teamlist)
  - [giodudeVGC, 3rd, 14 Sep 2026](https://pokepast.es/a1b7a6d0af006124)
  - [Alex Tu, 110th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0969/teamlist)
  - [Kiran Singh, 1st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0267/teamlist)
  - [Lewis Tan, 25th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0101/teamlist)
  - [Oliver Eskolin, 23rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0393/teamlist)
  - [Bartosz Kuskowski, 32nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0083/teamlist)
  - [Gaetano Di Trapani, 79th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0134/teamlist)
  - [Edmund Kuras, 341st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0173/teamlist)
  - [Jesse Beard, 99th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0290/teamlist)
  - [Finn Seipold, 11th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0016/teamlist)
  - [Benedikt Rohs, 559th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0586/teamlist)
  - [Timothy Lee, 63rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0291/teamlist)
  - [Mike Barnes, 80th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0165/teamlist)
  - [Jessica Bradley, 94th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0095/teamlist)
  - [Zac Sinclair, 154th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0152/teamlist)
  - [Matthew Tattersall, 168th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0268/teamlist)
  - [Jimmy Nguyen, 193rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0102/teamlist)
  - [Corey Penney, 220th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0249/teamlist)
  - [Christian Sciberras, 317th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0208/teamlist)
  - [Radek Sciberras, 320th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0024/teamlist)
  - [Federico Masu, 63rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1128/teamlist)
  - [Stefano De Maria, 87th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0696/teamlist)
  - [Jean CHAZELLE, 129th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0851/teamlist)
  - [Jamie Edwards, 152nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0040/teamlist)
  - [Francesco Magurno, 199th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0671/teamlist)
  - [Leon Haselmeyer, 224th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0874/teamlist)
  - [Lucas Rodrigues Carneiro, 249th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0021/teamlist)
  - [Luca Breitling-Pause, 255th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0269/teamlist)
  - [Vincenzo Giovanni Malagodi, 306th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0732/teamlist)
  - [Paul Steinmüller, 312th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0463/teamlist)
  - [André Soellner, 337th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0112/teamlist)
  - [James Thomas, 406th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0237/teamlist)
  - [Andrea Stefanczyk, 443rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0985/teamlist)
  - [Jannes Büchler, 531st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0184/teamlist)
  - [Paul van der Heijden, 563rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1106/teamlist)
  - [Alex Gabirondo, 625th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1045/teamlist)
  - [Ismael Amador Guillen, 698th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0832/teamlist)
  - [Claudio Gobbetti, 702nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0847/teamlist)
  - [Theo IVCEVIC, 706th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0314/teamlist)
  - [Luca Santinelli, 709th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0462/teamlist)
  - [Jan Schwendtner, 723rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0836/teamlist)
  - [Kevin Guzman, 797th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0652/teamlist)
  - [Sam Marshall-Smith, 799th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0280/teamlist)
  - [Luke Ng, 850th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1084/teamlist)
  - [Liam Stöcker, 887th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0935/teamlist)
  - [Luca Gazzola, 951st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1060/teamlist)
  - [KEISUKE TAKAHASHI, 993rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0906/teamlist)
  - [Wolfgang Wambach, 1001st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1017/teamlist)
  - [Katsutoshi Ogi, 1066th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0063/teamlist)
  - [Jaqueline Eggen, 1129th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0508/teamlist)
  - [Aditya Subramanian, Runner Up, 21 Sep 2026](https://pokepast.es/7c9c0663ef60180e)
  - [Aditya Subramanian, 2nd, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/i4SPzSax7gWzIckK52KU)
  - [Shohei Kimura, , 21 Sep 2026](https://pokepast.es/62aa4ef34ee42f01)
  - [Duc Huy Nguyen, 763rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1040/teamlist)
  - [Andrew McNatt, 435th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0751/teamlist)
  - [Mehmet Yilmaz, 966th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0704/teamlist)
  - [Daniel Hill, 973rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0739/teamlist)
  - [Christopher Pace, 667th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0826/teamlist)
  - [Simon Benedict Stein, 183rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0082/teamlist)
  - [Bruno Griep Fernandes, 230th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0673/teamlist)
  - [Yunus Burneckas, 421st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0920/teamlist)
  - [Joshua Miller, 102nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0709/teamlist)
  - [Georg Lang, 615th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0449/teamlist)
  - [Frank Cordova Centurion, 608th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0409/teamlist)
  - [Dimitri Koziaris, 48th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0158/teamlist)
  - [Andy Brophy, 62nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0277/teamlist)
  - [THORIN MCDONALD, 245th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0233/teamlist)
  - [Lennart Otto, 151st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0003/teamlist)
  - [David R. Pigan-Ewens, 533rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0109/teamlist)
  - [Lukas Zahn, 730th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1047/teamlist)
  - [Tim Maruschewski, 743rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0602/teamlist)
  - [Taevon Ramseur, 559th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0242/teamlist)
  - [Dorean Neron, 504th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0033/teamlist)
  - [Nicholas Faulkner, 184th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0144/teamlist)
  - [Austin Le, 309th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0322/teamlist)
  - [Dorian Luckie, 802nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0459/teamlist)
  - [Liam Clarkson, 27th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0122/teamlist)
  - [Thomas Dervan, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0256/teamlist)
  - [Xena, , 10 Sep 2026](https://pokepast.es/b7d841f59242636e)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Joel Pichardo, 640th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0300/teamlist)

#### Community 4 / Sub-community 1: Trick Room (Mega Golisopod) (121 primary teams, 101 distinct builds, top pair on 71/121)
- Megas on member teams: Golisopod 87, Garchomp-Z 41, Charizard-Y 10, Raichu-Y 9, Salamence 9, Mawile 8
- Top species by team share: Farigiraf 93%, Golisopod 72%, Rillaboom 42%, Incineroar 41%, Garchomp 36%, Politoed 25%
- Token label: Farigiraf / Golisopod@Golisopite
- Mode tags on primary teams: Trick Room 71, Rain 50, Tailwind 34, Sun 23, Setup 17, Screens 9, Sand 4, Snow 3, Psyspam 1
- Primary teams: 121 (18.3% of the community's primary weight), hybrid teams: 100 (16.9%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Pawmot (other item; Focus Sash 13/15)+Staraptor (other item; Choice Scarf 4/4), Torkoal (other item; Charcoal 15/15)+Farigiraf@Grassy Seed, Gholdengo (other item; Life Orb 16/25)+Pawmot (other item; Focus Sash 13/15), Milotic (other item; Leftovers 22/32)+Camerupt@Cameruptite, Farigiraf@Grassy Seed+Garchomp@Garchompite Z, Milotic (other item; Leftovers 22/32)+Farigiraf@Grassy Seed, Torkoal (other item; Charcoal 15/15)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 103/149)+Farigiraf@Grassy Seed, Pawmot (other item; Focus Sash 13/15)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 103/149)+Sneasler@Grassy Seed, Politoed (other item; Sitrus Berry 82/172)+Staraptor (other item; Choice Scarf 4/4), Rillaboom (other item; Miracle Seed 103/149)+Blaziken@Blazikenite, Farigiraf (other item; Sitrus Berry 197/244)+Staraptor (other item; Choice Scarf 4/4), Ampharos (other item; Ampharosite 4/4)+Farigiraf (other item; Sitrus Berry 197/244), Kingambit (other item; Focus Sash 52/116)+Torkoal (other item; Charcoal 15/15), Sneasler (other item; White Herb 38/47)+Garchomp@Garchompite Z, Milotic (other item; Leftovers 22/32)+Garchomp@Garchompite Z, Staraptor (other item; Choice Scarf 4/4)+Golisopod@Golisopite, Milotic (other item; Leftovers 22/32)+Rillaboom (other item; Miracle Seed 103/149), Pawmot (other item; Focus Sash 13/15)+Garchomp@Garchompite Z, Farigiraf (other item; Sitrus Berry 197/244)+Primarina (other item; Life Orb 8/14), Rillaboom (other item; Miracle Seed 103/149)+Lucario@Lucarionite Z, Rillaboom (other item; Miracle Seed 103/149)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 103/149)+Torkoal (other item; Charcoal 15/15), Farigiraf (other item; Sitrus Berry 197/244)+Aerodactyl@Aerodactylite, Kingambit (other item; Focus Sash 52/116)+Camerupt@Cameruptite, Farigiraf (other item; Sitrus Berry 197/244)+Camerupt@Cameruptite, Kingambit (other item; Focus Sash 52/116)+Primarina (other item; Life Orb 8/14), Sneasler (other item; White Herb 38/47)+Basculegion@Choice Scarf, Armarouge (other item; Life Orb 4/6)+Golisopod@Golisopite, Farigiraf (other item; Sitrus Berry 197/244)+Mawile@Mawilite, Kommo-o (other item; Leftovers 18/25)+Rillaboom (other item; Miracle Seed 103/149), Rillaboom (other item; Miracle Seed 103/149)+Camerupt@Cameruptite, Incineroar (other item; Sitrus Berry 68/206)+Mawile@Mawilite, Indeedee-F (other item; Rocky Helmet 22/41)+Sneasler (other item; White Herb 38/47), Baxcalibur (other item; Baxcalibrite 3/6)+Golisopod@Golisopite, Kingambit (other item; Focus Sash 52/116)+Farigiraf@Grassy Seed, Farigiraf (other item; Sitrus Berry 197/244)+Glimmora@Glimmoranite, Rillaboom (other item; Miracle Seed 103/149)+Volcarona (other item; Rocky Helmet 3/9), Rillaboom (other item; Miracle Seed 103/149)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 103/149)+Annihilape@Choice Scarf, Golisopod@Golisopite+Grimmsnarl@Light Clay, Rillaboom (other item; Miracle Seed 103/149)+Basculegion@Choice Scarf, Gholdengo (other item; Life Orb 16/25)+Garchomp@Garchompite Z, Hydreigon (other item; Focus Sash 5/8)+Golisopod@Golisopite, Golisopod@Golisopite+Sableye@Light Clay, Milotic (other item; Leftovers 22/32)+Golisopod@Golisopite, Rillaboom (other item; Miracle Seed 103/149)+Raichu@Raichunite Y, Farigiraf (other item; Sitrus Berry 197/244)+Blaziken@Blazikenite, Incineroar (other item; Sitrus Berry 68/206)+Camerupt@Cameruptite, Farigiraf (other item; Sitrus Berry 197/244)+Sylveon (other item; Fairy Feather 83/84), Pelipper (other item; Focus Sash 134/259)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 103/149)+Sableye (other item; Roseli Berry 7/11), Sinistcha (other item; Sitrus Berry 9/28)+Golisopod@Golisopite, Farigiraf (other item; Sitrus Berry 197/244)+Torkoal (other item; Charcoal 15/15), Pelipper (other item; Focus Sash 134/259)+Raichu@Raichunite Y, Dragapult (other item; Life Orb 7/9)+Golisopod@Golisopite, Farigiraf@Grassy Seed+Golisopod@Golisopite, Golisopod@Golisopite+Raichu@Raichunite Y, Farigiraf (other item; Sitrus Berry 197/244)+Pawmot (other item; Focus Sash 13/15), Politoed (other item; Sitrus Berry 82/172)+Volcarona (other item; Rocky Helmet 3/9), Farigiraf (other item; Sitrus Berry 197/244)+Kingambit (other item; Focus Sash 52/116), Farigiraf (other item; Sitrus Berry 197/244)+Sirfetch’d (other item; Leek 7/9), Farigiraf (other item; Sitrus Berry 197/244)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 68/206)+Primarina (other item; Life Orb 8/14), Kingambit (other item; Focus Sash 52/116)+Raichu@Raichunite Y, Farigiraf (other item; Sitrus Berry 197/244)+Milotic (other item; Leftovers 22/32), Hydreigon (other item; Focus Sash 5/8)+Pelipper (other item; Focus Sash 134/259), Basculegion@Choice Scarf+Golisopod@Golisopite, Glimmora@Glimmoranite+Golisopod@Golisopite, Pelipper (other item; Focus Sash 134/259)+Sneasler (other item; White Herb 38/47), Baxcalibur (other item; Baxcalibrite 3/6)+Farigiraf (other item; Sitrus Berry 197/244), Sirfetch’d (other item; Leek 7/9)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 68/206)+Farigiraf@Grassy Seed, Rillaboom (other item; Miracle Seed 103/149)+Dragonite@Dragoninite, Farigiraf (other item; Sitrus Berry 197/244)+Raichu@Raichunite Y, Kingambit (other item; Focus Sash 52/116)+Pawmot (other item; Focus Sash 13/15), Politoed (other item; Sitrus Berry 82/172)+Golisopod@Golisopite, Farigiraf (other item; Sitrus Berry 197/244)+Garchomp (other item; Life Orb 58/76), Incineroar (other item; Sitrus Berry 68/206)+Raichu@Raichunite Y, Incineroar (other item; Sitrus Berry 68/206)+Rillaboom (other item; Miracle Seed 103/149), Golisopod@Golisopite+Sneasler@Grassy Seed, Golisopod@Golisopite+Salamence@Salamencite, Incineroar (other item; Sitrus Berry 68/206)+Milotic (other item; Leftovers 22/32), Farigiraf (other item; Sitrus Berry 197/244)+Garchomp@Choice Scarf, Farigiraf (other item; Sitrus Berry 197/244)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 68/206)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 103/149)+Golisopod@Golisopite, Farigiraf (other item; Sitrus Berry 197/244)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 103/149)+Metagross@Metagrossite, Archaludon (other item; Leftovers 350/374)+Golisopod@Golisopite, Archaludon (other item; Leftovers 350/374)+Sneasler@Grassy Seed, Pelipper (other item; Focus Sash 134/259)+Golisopod@Golisopite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Farigiraf (other item; Sitrus Berry 197/244) | 80.8% |
| Golisopod@Golisopite | 70.2% |
| Rillaboom (other item; Miracle Seed 103/149) | 39.0% |
| Garchomp@Garchompite Z | 36.1% |
| Milotic (other item; Leftovers 22/32) | 16.3% |
| Torkoal (other item; Charcoal 15/15) | 13.0% |
| Sneasler (other item; White Herb 38/47) | 13.0% |
| Farigiraf@Grassy Seed | 12.5% |
| Raichu@Raichunite Y | 8.5% |
| Primarina (other item; Life Orb 8/14) | 8.4% |
| Pawmot (other item; Focus Sash 13/15) | 6.3% |
| Mawile@Mawilite | 6.0% |
| Camerupt@Cameruptite | 5.7% |
| Ampharos (other item; Ampharosite 4/4) | 3.2% |
| Sneasler@Grassy Seed | 3.1% |
| Staraptor (other item; Choice Scarf 4/4) | 2.9% |
| Volcarona (other item; Rocky Helmet 3/9) | 2.8% |
| Baxcalibur (other item; Baxcalibrite 3/6) | 2.3% |
| Hydreigon (other item; Focus Sash 5/8) | 2.2% |
| Lucario@Lucarionite Z | 1.5% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Wolfgang Wambach, 1001st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1017/teamlist)
  - [Kevin Guzman, 797th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0652/teamlist)
  - [Jesse Beard, 99th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0290/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Emma Schot, 212th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0075/teamlist)
  - [Emilio Gallardo Ávila, 72nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1072/teamlist)
  - [Edhen Soto, 292nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0170/teamlist)
  - [Timothy Lee, 63rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0291/teamlist)
  - [Mike Barnes, 80th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0165/teamlist)
  - [Jessica Bradley, 94th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0095/teamlist)
  - [Zac Sinclair, 154th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0152/teamlist)
  - [Matthew Tattersall, 168th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0268/teamlist)
  - [Jimmy Nguyen, 193rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0102/teamlist)
  - [Corey Penney, 220th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0249/teamlist)
  - [Christian Sciberras, 317th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0208/teamlist)
  - [Radek Sciberras, 320th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0024/teamlist)
  - [Federico Masu, 63rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1128/teamlist)
  - [Stefano De Maria, 87th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0696/teamlist)
  - [Jean CHAZELLE, 129th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0851/teamlist)
  - [Jamie Edwards, 152nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0040/teamlist)
  - [Francesco Magurno, 199th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0671/teamlist)
  - [Leon Haselmeyer, 224th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0874/teamlist)
  - [Lucas Rodrigues Carneiro, 249th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0021/teamlist)
  - [Luca Breitling-Pause, 255th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0269/teamlist)
  - [Vincenzo Giovanni Malagodi, 306th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0732/teamlist)
  - [Paul Steinmüller, 312th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0463/teamlist)
  - [André Soellner, 337th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0112/teamlist)
  - [James Thomas, 406th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0237/teamlist)
  - [Andrea Stefanczyk, 443rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0985/teamlist)
  - [Jannes Büchler, 531st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0184/teamlist)
  - [Paul van der Heijden, 563rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1106/teamlist)
  - [Alex Gabirondo, 625th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1045/teamlist)
  - [Ismael Amador Guillen, 698th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0832/teamlist)
  - [Claudio Gobbetti, 702nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0847/teamlist)
  - [Theo IVCEVIC, 706th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0314/teamlist)
  - [Jan Schwendtner, 723rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0836/teamlist)
  - [Sam Marshall-Smith, 799th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0280/teamlist)
  - [Liam Stöcker, 887th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0935/teamlist)
  - [Luca Gazzola, 951st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1060/teamlist)
  - [KEISUKE TAKAHASHI, 993rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0906/teamlist)
  - [Katsutoshi Ogi, 1066th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0063/teamlist)
  - [Jaqueline Eggen, 1129th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0508/teamlist)
  - [Aditya Subramanian, Runner Up, 21 Sep 2026](https://pokepast.es/7c9c0663ef60180e)
  - [Aditya Subramanian, 2nd, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/i4SPzSax7gWzIckK52KU)
  - [Shohei Kimura, , 21 Sep 2026](https://pokepast.es/62aa4ef34ee42f01)
  - [Jacob Heagerty, 203rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0957/teamlist)
  - [Elisha Smith, 1060th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0419/teamlist)
  - [Parker Sidenstricker, 145th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0005/teamlist)
  - [Nico Schumann, 357th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1109/teamlist)
  - [Emanuel Helmke, 886th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0157/teamlist)
  - [Dominik Bratek, 1008th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0230/teamlist)
  - [Maximilian Schmidt, 860th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0181/teamlist)
  - [Mats Kjellström, 913th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0749/teamlist)
  - [Sara Munk, 109th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0138/teamlist)
  - [Ryan Gluchowski, 353rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0308/teamlist)
  - [Adam Tuohy, 396th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0110/teamlist)
  - [Giovanni Manuel Tonelli, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0257/teamlist)
  - [Jorge Segui Hernandez, 156th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0750/teamlist)
  - [Francisco Martí, 246th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1023/teamlist)
  - [Tom Ratsma, 529th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0663/teamlist)
  - [Daniel Trujillo Canalejo, 803rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1116/teamlist)
  - [devintlegend, , 11 Sep 2026](https://pokepast.es/56d038403d0e2fd5)
  - [Leonardo Lewis, 196th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0050/teamlist)
  - [Luke Tiger Mainholz, 808th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0328/teamlist)
  - [BrokenLegge, , 16 Sep 2026](https://pokepast.es/e39a17eb6c42b52c)
  - [Gabri 301, , 10 Sep 2026](https://pokepast.es/4fce711199944ae0)
  - [Selvin Jacob, 853rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0313/teamlist)
  - [Andreas Zimmermann, 1099th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0482/teamlist)
  - [Ben Coale, 641st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0599/teamlist)
  - [Erick Hernandez Valdivia, 627th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0912/teamlist)
  - [Alexandre Joseph, 902nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0417/teamlist)
  - [Jakob Margetts, 91st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0224/teamlist)
  - [Kay Elst, 492nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0849/teamlist)
  - [Christopher Hamilton, 953rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0362/teamlist)
  - [Luca Santinelli, 709th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0462/teamlist)
  - [Luke Ng, 850th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1084/teamlist)
  - [Mitchell Pennisi, 315th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0071/teamlist)
  - [Tobias Benrath, 675th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1099/teamlist)
  - [Jonah Kassen, 830th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0111/teamlist)
  - [Jaime Blanch Vázquez, 590th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0687/teamlist)
  - [Lily Dellow, 72nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0266/teamlist)
  - [Liam Greenaway, 204th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0132/teamlist)
  - [re_Takeshi_, Runner Up, 12 Sep 2026](https://pokepast.es/4bfce88ce42a966a)
  - [Wes Stembert, 77th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0474/teamlist)
  - [Felix Friese, 313th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1011/teamlist)
  - [Alex Nguyen, 511th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0267/teamlist)
  - [ausma, , 9 Sep 2026](https://pokepast.es/40e7b4f7469d52ae)
  - [Felix Miguel Andres, 504th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0629/teamlist)
  - [Julian Brandhofer, 947th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0147/teamlist)
  - [Evan Graham, 602nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0970/teamlist)
  - [Aram Reichardt, 819th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0598/teamlist)
  - [One An An, 965th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0407/teamlist)
  - [Stefan Specht, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0384/teamlist)
  - [Stefan Filipovski, 733rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0152/teamlist)
  - [Adam Holmyard, 889th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0996/teamlist)
  - [Alex Hoak, 284th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0348/teamlist)
  - [Rawnie Mills, 1089th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0894/teamlist)
  - [Tommy Vo, 512th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0072/teamlist)
  - [beebee10222, , 9 Sep 2026](https://pokepast.es/376176212f88e4cb)
  - [Miguel Gomes, 1003rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0359/teamlist)
  - [Edgar Graf, 1098th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1015/teamlist)
  - [Nikola Zirdum, 281st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0805/teamlist)
  - [Scott Iwafuchi, 27th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0682/teamlist)
  - [banksofdeshawn, , 14 Sep 2026](https://pokepast.es/2106e9c7e469a2dd)

#### Community 4 / Sub-community 2: Sun (Mega Charizard-Y) (184 primary teams, 80 distinct builds, top pair on 68/184)
- Megas on member teams: Charizard-Y 176, Golisopod 54, Aerodactyl 43, Floette 14, Garchomp-Z 13, Gardevoir 5
- Top species by team share: Charizard 96%, Garchomp 61%, Farigiraf 54%, Sylveon 41%, Kingambit 40%, Grimmsnarl 35%
- Token label: Charizard@Charizardite Y / Sylveon
- Mode tags on primary teams: Sun 176, Tailwind 101, Rain 66, Screens 63, Trick Room 46, Psyspam 9, Setup 2, Sand 1
- Primary teams: 184 (30.0% of the community's primary weight), hybrid teams: 58 (8.6%)
- Date range: 2026-09-10 to 2026-09-27
- Core pairs: Toxapex (other item; Leftovers 11/12)+Garchomp@Choice Scarf, Whimsicott (other item; Focus Sash 30/34)+Floette-Eternal@Floettite, Whimsicott (other item; Focus Sash 30/34)+Glimmora@Glimmoranite, Basculegion (other item; Life Orb 19/28)+Floette-Eternal@Floettite, Sylveon (other item; Fairy Feather 83/84)+Aerodactyl@Aerodactylite, Garchomp (other item; Life Orb 58/76)+Aerodactyl@Aerodactylite, Aegislash (other item; Focus Sash 5/9)+Venusaur (other item; Focus Sash 48/72), Basculegion (other item; Life Orb 19/28)+Whimsicott (other item; Focus Sash 30/34), Aegislash (other item; Focus Sash 5/9)+Sylveon (other item; Fairy Feather 83/84), Toxapex (other item; Leftovers 11/12)+Venusaur (other item; Focus Sash 48/72), Kingambit (other item; Focus Sash 52/116)+Aerodactyl@Aerodactylite, Sylveon (other item; Fairy Feather 83/84)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 58/76)+Floette-Eternal@Floettite, Garchomp (other item; Life Orb 58/76)+Whimsicott (other item; Focus Sash 30/34), Aerodactyl (other item; Focus Sash 11/12)+Garchomp (other item; Life Orb 58/76), Kingambit (other item; Focus Sash 52/116)+Blaziken@Blazikenite, Venusaur (other item; Focus Sash 48/72)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 58/76)+Sylveon (other item; Fairy Feather 83/84), Sylveon (other item; Fairy Feather 83/84)+Toxapex (other item; Leftovers 11/12), Garchomp (other item; Life Orb 58/76)+Kingambit (other item; Focus Sash 52/116), Whimsicott (other item; Focus Sash 30/34)+Garchomp@Choice Scarf, Kingambit (other item; Focus Sash 52/116)+Sylveon (other item; Fairy Feather 83/84), Basculegion (other item; Life Orb 19/28)+Garchomp (other item; Life Orb 58/76), Gholdengo (other item; Life Orb 16/25)+Whimsicott (other item; Focus Sash 30/34), Rillaboom (other item; Miracle Seed 103/149)+Blaziken@Blazikenite, Aerodactyl (other item; Focus Sash 11/12)+Sylveon (other item; Fairy Feather 83/84), Incineroar (other item; Sitrus Berry 68/206)+Toxapex (other item; Leftovers 11/12), Kingambit (other item; Focus Sash 52/116)+Whimsicott (other item; Focus Sash 30/34), Kingambit (other item; Focus Sash 52/116)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 68/206)+Sirfetch’d (other item; Leek 7/9), Venusaur (other item; Focus Sash 48/72)+Charizard@Charizardite Y, Charizard@Charizardite Y+Garchomp@Choice Scarf, Venusaur (other item; Focus Sash 48/72)+Grimmsnarl@Light Clay, Kingambit (other item; Focus Sash 52/116)+Torkoal (other item; Charcoal 15/15), Aerodactyl@Aerodactylite+Charizard@Charizardite Y, Venusaur (other item; Focus Sash 48/72)+Annihilape@Choice Scarf, Toxapex (other item; Leftovers 11/12)+Charizard@Charizardite Y, Kingambit (other item; Focus Sash 52/116)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 58/76)+Charizard@Charizardite Y, Sylveon (other item; Fairy Feather 83/84)+Charizard@Charizardite Y, Whimsicott (other item; Focus Sash 30/34)+Charizard@Charizardite Y, Farigiraf (other item; Sitrus Berry 197/244)+Aerodactyl@Aerodactylite, Kingambit (other item; Focus Sash 52/116)+Gardevoir@Gardevoirite, Kingambit (other item; Focus Sash 52/116)+Camerupt@Cameruptite, Basculegion (other item; Life Orb 19/28)+Kingambit (other item; Focus Sash 52/116), Kingambit (other item; Focus Sash 52/116)+Primarina (other item; Life Orb 8/14), Charizard@Charizardite Y+Grimmsnarl@Light Clay, Aegislash (other item; Focus Sash 5/9)+Charizard@Charizardite Y, Kingambit (other item; Focus Sash 52/116)+Farigiraf@Grassy Seed, Aerodactyl@Aerodactylite+Garchomp@Choice Scarf, Venusaur (other item; Focus Sash 48/72)+Swampert@Swampertite, Venusaur (other item; Focus Sash 48/72)+Sneasler@Psychic Seed, Farigiraf (other item; Sitrus Berry 197/244)+Glimmora@Glimmoranite, Aerodactyl (other item; Focus Sash 11/12)+Kingambit (other item; Focus Sash 52/116), Golisopod@Golisopite+Grimmsnarl@Light Clay, Sylveon (other item; Fairy Feather 83/84)+Venusaur (other item; Focus Sash 48/72), Farigiraf (other item; Sitrus Berry 197/244)+Blaziken@Blazikenite, Charizard@Charizardite Y+Floette-Eternal@Floettite, Farigiraf (other item; Sitrus Berry 197/244)+Sylveon (other item; Fairy Feather 83/84), Incineroar (other item; Sitrus Berry 68/206)+Glimmora@Glimmoranite, Grimmsnarl@Light Clay+Swampert@Swampertite, Archaludon (other item; Leftovers 350/374)+Grimmsnarl@Light Clay, Kingambit (other item; Focus Sash 52/116)+Charizard@Charizardite Y, Sylveon (other item; Fairy Feather 83/84)+Whimsicott (other item; Focus Sash 30/34), Incineroar (other item; Sitrus Berry 68/206)+Garchomp@Choice Scarf, Farigiraf (other item; Sitrus Berry 197/244)+Kingambit (other item; Focus Sash 52/116), Farigiraf (other item; Sitrus Berry 197/244)+Sirfetch’d (other item; Leek 7/9), Pelipper (other item; Focus Sash 134/259)+Grimmsnarl@Light Clay, Pelipper (other item; Focus Sash 134/259)+Venusaur (other item; Focus Sash 48/72), Kingambit (other item; Focus Sash 52/116)+Raichu@Raichunite Y, Charizard@Charizardite Y+Gardevoir@Gardevoirite, Politoed (other item; Sitrus Berry 82/172)+Grimmsnarl@Light Clay, Aegislash (other item; Focus Sash 5/9)+Incineroar (other item; Sitrus Berry 68/206), Glimmora@Glimmoranite+Golisopod@Golisopite, Sirfetch’d (other item; Leek 7/9)+Golisopod@Golisopite, Aerodactyl (other item; Focus Sash 11/12)+Charizard@Charizardite Y, Annihilape@Choice Scarf+Charizard@Charizardite Y, Kingambit (other item; Focus Sash 52/116)+Pawmot (other item; Focus Sash 13/15), Farigiraf (other item; Sitrus Berry 197/244)+Garchomp (other item; Life Orb 58/76), Farigiraf (other item; Sitrus Berry 197/244)+Garchomp@Choice Scarf, Farigiraf (other item; Sitrus Berry 197/244)+Charizard@Charizardite Y, Sinistcha (other item; Sitrus Berry 9/28)+Grimmsnarl@Light Clay, Basculegion (other item; Life Orb 19/28)+Pelipper (other item; Focus Sash 134/259)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Charizard@Charizardite Y | 95.7% |
| Sylveon (other item; Fairy Feather 83/84) | 41.4% |
| Kingambit (other item; Focus Sash 52/116) | 39.0% |
| Grimmsnarl@Light Clay | 36.1% |
| Garchomp (other item; Life Orb 58/76) | 33.7% |
| Venusaur (other item; Focus Sash 48/72) | 25.8% |
| Aerodactyl@Aerodactylite | 23.0% |
| Garchomp@Choice Scarf | 19.0% |
| Whimsicott (other item; Focus Sash 30/34) | 14.5% |
| Floette-Eternal@Floettite | 7.9% |
| Basculegion (other item; Life Orb 19/28) | 5.5% |
| Toxapex (other item; Leftovers 11/12) | 5.5% |
| Aegislash (other item; Focus Sash 5/9) | 3.2% |
| Glimmora@Glimmoranite | 1.3% |
| Sirfetch’d (other item; Leek 7/9) | 0.6% |
| Blaziken@Blazikenite | 0.4% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Anton Glagow, 30th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0815/teamlist)
  - [Kensuke Sakata, 108th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0163/teamlist)
  - [Selahattin Sturm, 82nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0494/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Daniel Quek, 8th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0137/teamlist)
  - [Waeylin Pan, 175th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0034/teamlist)
  - [Scott Terry, 273rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0072/teamlist)
  - [Samuel Adzhib, 648th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0412/teamlist)
  - [Nicolas Scapin, 948th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0953/teamlist)
  - [Michael Rempfer, 961st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0272/teamlist)
  - [Max Franklin, 34th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0614/teamlist)
  - [Samuel Thompson, 44th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0785/teamlist)
  - [Ian O'Connor, 48th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0521/teamlist)
  - [Ethan Shaffer, 170th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0711/teamlist)
  - [Clinton Hoffman, 171st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0291/teamlist)
  - [Caleb Hubka, 333rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0164/teamlist)
  - [Aaron Kuo, 387th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0075/teamlist)
  - [Faith Hayden, 475th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0327/teamlist)
  - [Sudhish Rao, 536th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0995/teamlist)
  - [Kyle Lyon, 630th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0477/teamlist)
  - [Nathan Miller, 885th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1019/teamlist)
  - [Rachel Heeter, 1018th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0323/teamlist)
  - [Emily Turowski, 1057th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0372/teamlist)
  - [Emma Schot, 212th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0075/teamlist)
  - [Emilio Gallardo Ávila, 72nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1072/teamlist)
  - [Edhen Soto, 292nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0170/teamlist)
  - [Jimmy Do, 362nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0951/teamlist)
  - [Sam Dioneda, 447th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0921/teamlist)
  - [Ben Coale, 641st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0599/teamlist)
  - [Dustin Brown, 643rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0561/teamlist)
  - [seafoamshores, 765th, 22 Sep 2026](https://pokepast.es/2b117207f8feb1a5)
  - [aricbxxiv, 179th, 20 Sep 2026](https://pokepast.es/1e06e1fb09f95133)
  - [Eric Bartlett, 178th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0972/teamlist)
  - [Lake Spohn, 765th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0109/teamlist)
  - [Dennis Mischitz, 1044th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0491/teamlist)
  - [Gregory Evevsky, 161st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0469/teamlist)
  - [Donovan Mitchell, 1066th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0246/teamlist)
  - [devintlegend, , 11 Sep 2026](https://pokepast.es/56d038403d0e2fd5)
  - [Sara Munk, 109th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0138/teamlist)
  - [Jacob Heagerty, 203rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0957/teamlist)
  - [Pavan Nimmagadda, 608th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0709/teamlist)
  - [Alexandre Joseph, 902nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0417/teamlist)
  - [Jesse Beard, 99th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0290/teamlist)
  - [Kevin Guzman, 797th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0652/teamlist)
  - [Wolfgang Wambach, 1001st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1017/teamlist)
  - [Benjamin Dean, 281st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0059/teamlist)
  - [Daniel Mateo Lindenberg, 840th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0406/teamlist)
  - [Stefan Specht, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0384/teamlist)
  - [Thomas Dervan, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0256/teamlist)
  - [Xena, , 10 Sep 2026](https://pokepast.es/b7d841f59242636e)
  - [Eliseo Torres-Morales, 1036th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0353/teamlist)
  - [Joshua Hoitink, 92nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0053/teamlist)
  - [Patrick Verrelli, 108th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0102/teamlist)
  - [Robert Pamplin, 744th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0562/teamlist)
  - [Jost Malczewski, 942nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0263/teamlist)
  - [JOSEPH SHOWALTER, 944th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0063/teamlist)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Nils von Lengerke, 456th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0950/teamlist)
  - [Nils Frahm, 216th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0529/teamlist)
  - [Eliana Stevens, 326th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0222/teamlist)
  - [Dyllan Taylor, 188th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0157/teamlist)
  - [IAN LUTZ, 588th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1043/teamlist)

#### Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar) (102 primary teams, 56 distinct builds, top pair on 90/102)
- Megas on member teams: Gengar 97, Froslass 13, Swampert 13, Golisopod 12, Garchomp-Z 3, Lucario-Z 3
- Top species by team share: Gengar 95%, Incineroar 92%, Rillaboom 91%, Politoed 86%, Archaludon 71%, Vivillon 28%
- Token label: Gengar@Gengarite / Incineroar / Politoed
- Mode tags on primary teams: Rain 93, Perish Trap 93, Snow 16, Tailwind 5, Setup 4, Trick Room 3, Sun 1
- Primary teams: 102 (16.2% of the community's primary weight), hybrid teams: 14 (2.4%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Vivillon (other item; Focus Sash 29/29)+Rillaboom@Eject Button, Gengar@Gengarite+Rillaboom@Eject Button, Vivillon (other item; Focus Sash 29/29)+Gengar@Gengarite, Kommo-o (other item; Leftovers 18/25)+Gengar@Gengarite, Froslass@Froslassite+Rillaboom@Eject Button, Kommo-o (other item; Leftovers 18/25)+Rillaboom@Eject Button, Politoed (other item; Sitrus Berry 82/172)+Staraptor (other item; Choice Scarf 4/4), Froslass@Froslassite+Gengar@Gengarite, Politoed (other item; Sitrus Berry 82/172)+Vivillon (other item; Focus Sash 29/29), Incineroar (other item; Sitrus Berry 68/206)+Rillaboom@Eject Button, Incineroar (other item; Sitrus Berry 68/206)+Vivillon (other item; Focus Sash 29/29), Politoed (other item; Sitrus Berry 82/172)+Rillaboom@Eject Button, Incineroar (other item; Sitrus Berry 68/206)+Toxapex (other item; Leftovers 11/12), Politoed (other item; Sitrus Berry 82/172)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 68/206)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 68/206)+Sirfetch’d (other item; Leek 7/9), Incineroar (other item; Sitrus Berry 68/206)+Kommo-o (other item; Leftovers 18/25), Politoed (other item; Sitrus Berry 82/172)+Froslass@Froslassite, Kommo-o (other item; Leftovers 18/25)+Politoed (other item; Sitrus Berry 82/172), Incineroar (other item; Sitrus Berry 68/206)+Froslass@Froslassite, Kommo-o (other item; Leftovers 18/25)+Rillaboom (other item; Miracle Seed 103/149), Incineroar (other item; Sitrus Berry 68/206)+Mawile@Mawilite, Incineroar (other item; Sitrus Berry 68/206)+Camerupt@Cameruptite, Incineroar (other item; Sitrus Berry 68/206)+Glimmora@Glimmoranite, Politoed (other item; Sitrus Berry 82/172)+Volcarona (other item; Rocky Helmet 3/9), Incineroar (other item; Sitrus Berry 68/206)+Garchomp@Choice Scarf, Incineroar (other item; Sitrus Berry 68/206)+Politoed (other item; Sitrus Berry 82/172), Incineroar (other item; Sitrus Berry 68/206)+Primarina (other item; Life Orb 8/14), Politoed (other item; Sitrus Berry 82/172)+Grimmsnarl@Light Clay, Aegislash (other item; Focus Sash 5/9)+Incineroar (other item; Sitrus Berry 68/206), Archaludon (other item; Leftovers 350/374)+Politoed (other item; Sitrus Berry 82/172), Incineroar (other item; Sitrus Berry 68/206)+Farigiraf@Grassy Seed, Politoed (other item; Sitrus Berry 82/172)+Golisopod@Golisopite, Archaludon (other item; Leftovers 350/374)+Rillaboom@Eject Button, Incineroar (other item; Sitrus Berry 68/206)+Raichu@Raichunite Y, Archaludon (other item; Leftovers 350/374)+Vivillon (other item; Focus Sash 29/29), Incineroar (other item; Sitrus Berry 68/206)+Rillaboom (other item; Miracle Seed 103/149), Incineroar (other item; Sitrus Berry 68/206)+Milotic (other item; Leftovers 22/32), Incineroar (other item; Sitrus Berry 68/206)+Garchomp@Garchompite Z, Archaludon (other item; Leftovers 350/374)+Gengar@Gengarite, Archaludon (other item; Leftovers 350/374)+Froslass@Froslassite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Gengar@Gengarite | 94.3% |
| Incineroar (other item; Sitrus Berry 68/206) | 92.1% |
| Politoed (other item; Sitrus Berry 82/172) | 86.6% |
| Rillaboom@Eject Button | 63.1% |
| Vivillon (other item; Focus Sash 29/29) | 33.7% |
| Kommo-o (other item; Leftovers 18/25) | 19.4% |
| Froslass@Froslassite | 10.8% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Brady Smith, Top 4, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/27LoUGHWZssTh74nA0Oc)
  - [David Shutlar, 112th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0183/teamlist)
  - [Ethan Page, 116th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0323/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Tim Maruschewski, 743rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0602/teamlist)
  - [Taevon Ramseur, 559th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0242/teamlist)
  - [THORIN MCDONALD, 245th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0233/teamlist)
  - [Lennart Otto, 151st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0003/teamlist)
  - [Yunus Burneckas, 421st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0920/teamlist)
  - [Felix Miguel Andres, 504th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0629/teamlist)
  - [Georg Lang, 615th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0449/teamlist)
  - [Aram Reichardt, 819th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0598/teamlist)
  - [Shoichiro Imai, 1090th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1048/teamlist)
  - [Andrew McNatt, 435th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0751/teamlist)
  - [ausma, , 9 Sep 2026](https://pokepast.es/40e7b4f7469d52ae)
  - [Johannes Dose, 532nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1035/teamlist)
  - [Maximilian Wolff, 680th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0814/teamlist)
  - [Theo Chevis, 388th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1121/teamlist)

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): Sub-community 4: Aerodactyl / Tsareena (1 distinct build, 1 primary team, top pair on 1/1)

#### Community 4 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Rillaboom | Rillaboom (other item; Miracle Seed 103/149) | Sub-community 1: Trick Room (Mega Golisopod) | 149 | 0: 59 · 1: 48 · 3: 28 · 2: 13 · unassigned: 1 |
| Rillaboom | Rillaboom@Eject Button | Sub-community 3: Rain Perish Trap (Mega Gengar) | 65 | 3: 65 · unassigned: 0 |
| Rillaboom | Rillaboom@Grassy Seed | none | 5 | 1: 3 · 0: 2 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 58/76) | Sub-community 2: Sun (Mega Charizard-Y) | 76 | 2: 65 · 0: 8 · 1: 3 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 1: Trick Room (Mega Golisopod) | 72 | 1: 41 · 0: 14 · 2: 13 · 3: 3 · unassigned: 1 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 2: Sun (Mega Charizard-Y) | 36 | 2: 35 · 0: 1 · unassigned: 0 |
| Venusaur | Venusaur (other item; Focus Sash 48/72) | Sub-community 2: Sun (Mega Charizard-Y) | 72 | 2: 49 · 0: 23 · unassigned: 0 |
| Venusaur | Venusaur@Venusaurite | Sub-community 0: Rain (Mega Golisopod) | 9 | 0: 4 · 1: 3 · 2: 1 · unassigned: 1 |
| Basculegion | Basculegion@Choice Scarf | Sub-community 0: Rain (Mega Golisopod) | 62 | 0: 46 · 1: 8 · 2: 5 · 3: 3 · unassigned: 0 |
| Basculegion | Basculegion (other item; Life Orb 19/28) | Sub-community 2: Sun (Mega Charizard-Y) | 28 | 0: 13 · 2: 10 · 1: 3 · 3: 1 · 4: 1 · unassigned: 0 |
| Sneasler | Sneasler (other item; White Herb 38/47) | Sub-community 1: Trick Room (Mega Golisopod) | 47 | 0: 24 · 1: 15 · 2: 5 · 3: 3 · unassigned: 0 |
| Sneasler | Sneasler@Psychic Seed | Sub-community 0: Rain (Mega Golisopod) | 19 | 0: 16 · 2: 3 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 1: Trick Room (Mega Golisopod) | 8 | 0: 3 · 1: 3 · 2: 1 · 3: 1 · unassigned: 0 |
| Aerodactyl | Aerodactyl@Aerodactylite | Sub-community 2: Sun (Mega Charizard-Y) | 45 | 2: 43 · 0: 1 · 3: 1 · unassigned: 0 |
| Aerodactyl | Aerodactyl (other item; Focus Sash 11/12) | Sub-community 4: Aerodactyl / Tsareena | 12 | 2: 7 · 0: 2 · 1: 1 · 3: 1 · 4: 1 · unassigned: 0 |
| Raichu | Raichu@Raichunite Y | Sub-community 1: Trick Room (Mega Golisopod) | 20 | 1: 9 · 0: 8 · 3: 2 · 2: 1 · unassigned: 0 |
| Raichu | Raichu@Raichunite X | Sub-community 0: Rain (Mega Golisopod) | 5 | 0: 4 · 1: 1 · unassigned: 0 |
| Staraptor | Staraptor@Staraptite | Sub-community 0: Rain (Mega Golisopod) | 7 | 0: 4 · 2: 2 · 1: 1 · unassigned: 0 |
| Staraptor | Staraptor (other item; Choice Scarf 4/4) | Sub-community 1: Trick Room (Mega Golisopod) | 4 | 1: 4 · unassigned: 0 |

### Community 5: Pincurchin / Raichu-Alola
- Token label: Pincurchin / Raichu-Alola
- Mode tags on primary teams: none
- Megas on primary teams: none
- Primary teams: 0 (primary share 0.0%), hybrid teams: 0 (hybrid share 0.0%)
- Date range: n/a
- Core pairs: Pincurchin+Raichu-Alola
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Pincurchin | 0.0% | terrain-setter 100.0%, trick-room-abuser 85.5%, priority-attack 76.2% |
| Raichu-Alola | 0.0% | fake-out 81.2% |
- Representative teams (primary teams with the highest score for this community):
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - none

### Replicated builds
Rosters: teams with the same six species, at least buildMinCopies of them and one with a tournament placement, named by those species, with the tags and Megas of their copies and the earliest-dated pilot; 154 rosters. They change no community, assignment, or share.
- **Gholdengo / Rillaboom / Arcanine-Hisui / Sylveon / Raichu / Staraptor**, 122 copies, first parrobot7 (2026-09-14), best Champion; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Sneasler / Raichu / Rillaboom / Salamence / Gholdengo / Arcanine-Hisui**, 58 copies, first Ryan Loseto (2026-09-20), best Top 32; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Milotic / Raichu / Ceruledge / Staraptor / Gholdengo / Rillaboom**, Setup (Mega Raichu-Y, Mega Staraptor), 49 copies, first Lorenzo Arce (2026-09-20), best 5th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Excadrill / Salamence / Tyranitar / Indeedee / Corviknight / Sneasler**, Sand Psyspam (Mega Salamence, Mega Tyranitar), 44 copies, first Joseph Ugarte (2026-09-20), best 1st; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Tyranitar / Milotic / Excadrill / Rillaboom / Salamence / Gholdengo**, Sand (Mega Salamence, Mega Tyranitar), 44 copies, first lovejapanfrombr (2026-09-13), best Champion; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Froslass / Raichu / Rillaboom / Kingambit / Sneasler / Arcanine-Hisui**, Snow (Mega Froslass, Mega Raichu-Y), 39 copies, first Esa Ishaque (2026-09-20), best Top 8; in Community 0 / Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite
- **Golisopod / Charizard / Politoed / Archaludon / Grimmsnarl / Farigiraf**, Sun Rain Screens Trick Room (Mega Charizard-Y, Mega Golisopod), 38 copies, first Aditya Subramanian (2026-09-20), best 2nd; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Rillaboom / Salamence / Sneasler / Incineroar / Gholdengo / Floette-Eternal**, Setup (Mega Floette, Mega Salamence), 37 copies, first Uch (2026-09-09), best Champion; in Community 0 / Sub-community 2: Setup
- **Garchomp / Farigiraf / Aerodactyl / Sylveon / Charizard / Kingambit**, Sun (Mega Aerodactyl, Mega Charizard-Y), 35 copies, first Michael Spinetta-McCarthy (2026-09-20), best Top 16; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Floette-Eternal / Raichu / Rillaboom / Incineroar / Sneasler / Gholdengo**, Setup (Mega Floette, Mega Raichu-Y), 33 copies, first Rod (2026-09-10), best 5th; in Community 0 / Sub-community 2: Setup
- **Salamence / Rillaboom / Sneasler / Arcanine-Hisui / Kingambit / Basculegion**, 24 copies, first Ryan Loseto (2026-09-10), best Champion; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Dragonite / Rillaboom / Floette-Eternal / Incineroar / Sneasler / Gholdengo**, Setup (Mega Dragonite, Mega Floette), 20 copies, first Jeffrey Lehmann (2026-09-20), best Champion; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Salamence / Arcanine-Hisui / Indeedee / Milotic / Metagross / Sneasler**, Psyspam (Mega Metagross, Mega Salamence), 20 copies, first Blaik Thompson (2026-09-20), best Top 4; in Community 1 / Sub-community 2: Psyspam (Mega Salamence)
- **Archaludon / Incineroar / Politoed / Vivillon / Rillaboom / Gengar**, Rain Perish Trap (Mega Gengar), 20 copies, first Brady Smith (2026-09-20), best Top 4; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Sneasler / Sinistcha / Kingambit / Floette-Eternal / Delphox / Incineroar**, Setup (Mega Delphox, Mega Floette), 20 copies, first Justin Tang (2026-09-20), best 10th; in Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette)
- **Arcanine-Hisui / Milotic / Gholdengo / Raichu / Salamence / Rillaboom**, 20 copies, first Tom Ruggiero (2026-09-20), best 35th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Salamence / Raichu / Gholdengo / Incineroar / Sneasler / Rillaboom**, 19 copies, first pokebrosvgc (2026-09-18), best Champion; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Charizard / Swampert / Pelipper / Grimmsnarl / Archaludon / Venusaur**, Sun Rain Screens (Mega Charizard-Y, Mega Swampert), 19 copies, first Max Franklin (2026-09-20), best 8th; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Farigiraf / Hatterene / Incineroar / Camerupt / Kingambit / Indeedee-F**, Psyspam Trick Room (Mega Camerupt), 17 copies, first Advait Budaraju (2026-09-20), best 38th; in Community 3 / Sub-community 2: Trick Room Psyspam (Mega Camerupt)
- **Sylveon / Rillaboom / Gholdengo / Salamence / Arcanine-Hisui / Raichu**, 16 copies, first Zhengle Tu (2026-09-20), best Champion; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Salamence / Excadrill / Tyranitar / Milotic / Sneasler / Indeedee**, Sand Psyspam (Mega Salamence, Mega Tyranitar), 16 copies, first Sanghyeon Na (2026-09-10), best 157th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Kingambit / Rillaboom / Sneasler / Arcanine-Hisui / Salamence / Froslass**, Snow (Mega Froslass, Mega Salamence), 16 copies, first Alex Underhill (2026-09-16), best 282nd; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Kingambit / Salamence / Rillaboom / Sneasler / Incineroar / Floette-Eternal**, Setup (Mega Floette, Mega Salamence), 15 copies, first Wyatt McDonald (2026-09-09), best 277th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Golisopod / Indeedee-F / Gardevoir / Pelipper / Basculegion / Sneasler**, Rain Psyspam (Mega Gardevoir, Mega Golisopod), 14 copies, first Wolfe Glick (2026-09-12), best 6th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Metagross / Garchomp / Incineroar / Volcarona / Rillaboom / Sneasler**, 14 copies, first Yuta Ishigaki (2026-09-12), best 10th; in Community 2 / Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed
- **Armarouge / Dragapult / Gengar / Milotic / Indeedee-F / Staraptor**, Psyspam (Mega Gengar, Mega Staraptor), 14 copies, first Dorian Kang (2026-09-20), best Top 16; in Community 3 / Sub-community 1: Psyspam (Mega Staraptor)
- **Indeedee-F / Whimsicott / Basculegion / Kommo-o / Pyroar / Gardevoir**, Psyspam (Mega Gardevoir, Mega Pyroar), 14 copies, first Mason Cutler (2026-09-20), best 18th; in Community 3 / Sub-community 3: Psyspam · Whimsicott
- **Raichu / Arcanine-Hisui / Kingambit / Salamence / Rillaboom / Sneasler**, 12 copies, first Zachary Weed (2026-09-20), best 9th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Rillaboom / Salamence / Pelipper / Golisopod / Archaludon / Basculegion**, Rain (Mega Golisopod, Mega Salamence), 12 copies, first Joey McNatt (2026-09-20), best 21st; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Dragapult / Arcanine-Hisui / Altaria / Metagross / Indeedee-F / Milotic**, Setup (Mega Metagross), 11 copies, first Dylan Matthews (2026-09-20), best Top 16; in Community 3 / Sub-community 1: Psyspam (Mega Staraptor)
- **Indeedee-F / Armarouge / Kingambit / Gardevoir / Torkoal / Sneasler**, Sun Psyspam Trick Room (Mega Gardevoir), 11 copies, first LosChinganas (2026-09-10), best 53rd; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Salamence / Indeedee / Sneasler / Gholdengo / Tyranitar / Excadrill**, Sand Psyspam (Mega Salamence, Mega Tyranitar), 11 copies, first Owen (2026-09-09), best 90th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Froslass / Politoed / Incineroar / Archaludon / Gengar / Rillaboom**, Rain Snow Perish Trap (Mega Froslass, Mega Gengar), 10 copies, first Marco Silva (2026-09-13), best 5th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Arcanine-Hisui / Gholdengo / Milotic / Rillaboom / Staraptor / Raichu**, Setup (Mega Raichu-Y, Mega Staraptor), 10 copies, first Paul Maccarone (2026-09-20), best 25th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Indeedee-F / Golisopod / Staraptor / Armarouge / Dragapult / Milotic**, Psyspam Setup (Mega Golisopod, Mega Staraptor), 10 copies, first Stefan Mott (2026-09-20), best 25th; in Community 3 / Sub-community 1: Psyspam (Mega Staraptor)
- **Sneasler / Garchomp / Rillaboom / Lucario / Incineroar / Basculegion**, 10 copies, first William Brown (2026-09-20), best 28th; in Community 0 / Sub-community 2: Setup
- **Indeedee-F / Gardevoir / Sneasler / Venusaur / Charizard / Basculegion**, Sun Psyspam (Mega Charizard-Y, Mega Gardevoir), 10 copies, first Justin Tang (2026-09-09), best 209th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Floette-Eternal / Garchomp / Rillaboom / Sneasler / Incineroar / Kingambit**, Setup (Mega Floette, Mega Garchomp-Z), 9 copies, first Ling (2026-09-10), best 3rd; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Gholdengo / Gardevoir / Sneasler / Salamence / Incineroar / Indeedee-F**, Psyspam (Mega Gardevoir, Mega Salamence), 9 copies, first Wolfe Glick (2026-09-20), best Top 16; in Community 1 / Sub-community 2: Psyspam (Mega Salamence)
- **Rillaboom / Sneasler / Incineroar / Gholdengo / Garchomp / Raichu**, 9 copies, first Kshaunish Shaik (2026-09-20), best 32nd; in Community 0 / Sub-community 2: Setup
- **Salamence / Floette-Eternal / Basculegion / Sneasler / Rillaboom / Incineroar**, 9 copies, first Naoyuki Matsuhashi (2026-09-09), best 110th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Archaludon / Pelipper / Charizard / Venusaur / Grimmsnarl / Golisopod**, Sun Rain Screens (Mega Charizard-Y, Mega Golisopod), 9 copies, first Thaison Hughes (2026-09-20), best 128th; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Salamence / Sneasler / Froslass / Rillaboom / Gholdengo / Arcanine-Hisui**, Snow (Mega Froslass, Mega Salamence), 9 copies, first CloverBells (2026-09-13), best 321st; in Community 0 / Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite
- **Salamence / Rillaboom / Floette-Eternal / Basculegion / Sneasler / Kingambit**, 8 copies, first Apex__Dragon (2026-09-09), best 2nd; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Charizard / Floette-Eternal / Kingambit / Garchomp / Basculegion / Whimsicott**, Sun (Mega Charizard-Y, Mega Floette), 8 copies, first Giovanni Piscitelli (2026-09-20), best Top 8; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Floette-Eternal / Kingambit / Rillaboom / Sneasler / Incineroar / Delphox**, Setup (Mega Delphox, Mega Floette), 8 copies, first Cole Basham (2026-09-20), best Top 8; in Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette)
- **Rillaboom / Politoed / Archaludon / Gengar / Incineroar / Golisopod**, Rain Perish Trap (Mega Gengar, Mega Golisopod), 8 copies, first Billy Helm (2026-09-20), best 10th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Salamence / Kingambit / Gholdengo / Rillaboom / Sneasler / Arcanine-Hisui**, 8 copies, first Shohei Kimura (2026-09-16), best 47th; in Community 0 / Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite
- **Salamence / Arcanine-Hisui / Rillaboom / Kingambit / Farigiraf / Sylveon**, Trick Room (Mega Salamence), 8 copies, first Adam Wright (2026-09-17), best 116th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Gholdengo / Volcarona / Garchomp / Incineroar / Rillaboom / Raichu**, 7 copies, first Eric Rios (2026-09-27), best 1st; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Primarina / Raichu / Rillaboom / Staraptor / Arcanine-Hisui / Gholdengo**, Setup (Mega Raichu-Y, Mega Staraptor), 7 copies, first Michael Klymchuk (2026-09-20), best 13th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Klefki / Garchomp / Volcarona / Archaludon / Salamence / Glimmora**, Screens (Mega Glimmora, Mega Salamence), 7 copies, first Nick Navarre (2026-09-20), best Top 16; in Community 3 / Sub-community 4: Rillaboom / Glimmora@Glimmoranite
- **Delphox / Sinistcha / Maushold / Sneasler / Blastoise / Indeedee-F**, Setup (Mega Blastoise, Mega Delphox), 7 copies, first Benjamin van Hoeckel (2026-09-27), best 73rd; in Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette)
- **Salamence / Rillaboom / Floette-Eternal / Gholdengo / Arcanine-Hisui / Sneasler**, Setup (Mega Floette, Mega Salamence), 7 copies, first PhoNoodle (2026-09-10), best 206th; in Community 0 / Sub-community 2: Setup
- **Salamence / Indeedee / Kingambit / Excadrill / Tyranitar / Sneasler**, Sand Psyspam (Mega Salamence), 7 copies, first Sarapoke0914 (2026-09-11), best 291st; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Archaludon / Pelipper / Grimmsnarl / Charizard / Garchomp / Venusaur**, Sun Rain Screens (Mega Charizard-Y, Mega Garchomp-Z), 6 copies, first Kiran Singh (2026-09-27), best 1st; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Swampert / Archaludon / Golisopod / Pelipper / Grimmsnarl / Sinistcha**, Rain Screens (Mega Golisopod, Mega Swampert), 6 copies, first kara (2026-09-10), best 7th; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Froslass / Lycanroc-Dusk / Scovillain / Kingambit / Basculegion / Sneasler**, Snow (Mega Froslass, Mega Scovillain), 6 copies, first a__poke_ (2026-09-16), best 19th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Sneasler / Gardevoir / Kingambit / Basculegion / Indeedee-F / Salamence**, Psyspam (Mega Gardevoir, Mega Salamence), 6 copies, first Q (2026-09-10), best 44th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Arcanine-Hisui / Annihilape / Gholdengo / Salamence / Raichu / Rillaboom**, 6 copies, first Stephen Mea (2026-09-20), best 54th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Salamence / Rillaboom / Raichu / Arcanine-Hisui / Kingambit / Gholdengo**, 6 copies, first Ashanti Kalahari (2026-09-20), best 107th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Farigiraf / Swampert / Pelipper / Grimmsnarl / Archaludon / Golisopod**, Rain Screens Trick Room (Mega Golisopod, Mega Swampert), 6 copies, first Justin Wan (2026-09-20), best 136th; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Archaludon / Garchomp / Farigiraf / Basculegion / Pelipper / Golisopod**, Rain (Mega Golisopod, Mega Garchomp-Z), 6 copies, first Ryan Gluchowski (2026-09-20), best 156th; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Rillaboom / Excadrill / Sneasler / Tyranitar / Salamence / Milotic**, Sand (Mega Salamence, Mega Tyranitar), 6 copies, first spy_anya11 (2026-09-16), best 236th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Milotic / Golisopod / Incineroar / Rillaboom / Garchomp / Farigiraf**, Setup (Mega Garchomp-Z, Mega Golisopod), 5 copies, first Erizabeth (2026-09-13), best 4th; in Community 4 / Sub-community 1: Trick Room (Mega Golisopod)
- **Garchomp / Charizard / Venusaur / Incineroar / Sylveon / Toxapex**, Sun (Mega Charizard-Y), 5 copies, first Owen Murphy (2026-09-20), best 6th; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Salamence / Basculegion / Sneasler / Kingambit / Delphox / Rillaboom**, 5 copies, first Seung Lee (2026-09-20), best 23rd; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Gholdengo / Floette-Eternal / Incineroar / Garchomp / Charizard / Rillaboom**, Sun Setup (Mega Charizard-Y, Mega Floette), 5 copies, first Joey McGinley (2026-09-20), best 52nd; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Salamence / Kingambit / Glimmora / Volcarona / Rillaboom / Basculegion**, 5 copies, first Yuma Kinugawa (2026-09-12), best 53rd; in Community 3 / Sub-community 4: Rillaboom / Glimmora@Glimmoranite
- **Salamence / Charizard / Rillaboom / Kingambit / Sylveon / Sneasler**, Sun (Mega Charizard-Y, Mega Salamence), 5 copies, first Alex Soto (2026-09-10), best 580th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Gengar / Incineroar / Rillaboom / Archaludon / Pelipper / Swampert**, Rain Perish Trap (Mega Gengar, Mega Swampert), 4 copies, first giodudeVGC (2026-09-14), best 3rd; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Sirfetch’d / Golisopod / Farigiraf / Whimsicott / Glimmora / Incineroar**, 4 copies, first Huang Yang-Jie (2026-09-14), best Top 8; in Community 4 / Sub-community 1: Trick Room (Mega Golisopod)
- **Raichu / Floette-Eternal / Rillaboom / Volcarona / Gholdengo / Incineroar**, Setup (Mega Floette, Mega Raichu-Y), 4 copies, first Thomas Gravouille (2026-09-20), best 10th; in Community 0 / Sub-community 2: Setup
- **Maushold / Blastoise / Sinistcha / Sneasler / Delphox / Incineroar**, Setup (Mega Blastoise, Mega Delphox), 4 copies, first Timothy Nurse (2026-09-27), best 17th; in Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette)
- **Glimmora / Kingambit / Salamence / Rillaboom / Volcarona / Garchomp**, 4 copies, first Víctor Medina (2026-09-27), best 29th; in Community 3 / Sub-community 4: Rillaboom / Glimmora@Glimmoranite
- **Sneasler / Salamence / Sylveon / Kingambit / Arcanine-Hisui / Rillaboom**, 4 copies, first Jonathan Martin (2026-09-20), best 30th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Rillaboom / Sylveon / Charizard / Incineroar / Farigiraf / Garchomp**, Sun (Mega Charizard-Y), 4 copies, first Stefano Greppi (2026-09-16), best 40th; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Basculegion / Sylveon / Volcarona / Rillaboom / Garchomp / Raichu**, 4 copies, first Andrea Nesti (2026-09-27), best 45th; in Community 0 / Sub-community 2: Setup
- **Incineroar / Raichu / Rillaboom / Basculegion / Garchomp / Volcarona**, 4 copies, first Nick Schrott (2026-09-27), best 48th; in Community 0 / Sub-community 2: Setup
- **Volcarona / Incineroar / Basculegion / Rillaboom / Garchomp / Floette-Eternal**, 4 copies, first Fabio David (2026-09-27), best 58th; in Community 2 / Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed
- **Gholdengo / Aerodactyl / Incineroar / Milotic / Rillaboom / Charizard**, Sun Setup (Mega Aerodactyl, Mega Charizard-Y), 4 copies, first Ruby McEachern (2026-09-20), best 60th; in Community 0 / Sub-community 2: Setup
- **Gengar / Rillaboom / Incineroar / Kommo-o / Politoed / Swampert**, Rain Perish Trap (Mega Gengar, Mega Swampert), 4 copies, first Shohei Kimura (2026-09-14), best 73rd; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Incineroar / Espathra / Goodra-Hisui / Floette-Eternal / Absol / Rillaboom**, Setup (Mega Absol-Z, Mega Floette), 4 copies, first Rahim Farzan (2026-09-27), best 96th; in Community 2 / Sub-community 4: Absol@Absolite Z / Espathra / Goodra-Hisui
- **Rillaboom / Salamence / Excadrill / Tyranitar / Sneasler / Gholdengo**, Sand (Mega Salamence, Mega Tyranitar), 4 copies, first max_Zi_ma (2026-09-12), best 142nd; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Arcanine-Hisui / Farigiraf / Sylveon / Raichu / Staraptor / Kingambit**, 4 copies, first Bernard Herron (2026-09-20), best 175th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Sneasler / Rillaboom / Basculegion / Kingambit / Froslass / Salamence**, Snow (Mega Froslass, Mega Salamence), 4 copies, first Bazz Coleman (2026-09-20), best 179th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Milotic / Indeedee-F / Staraptor / Armarouge / Metagross / Dragapult**, Psyspam Setup (Mega Metagross, Mega Staraptor), 4 copies, first Peter Espy (2026-09-20), best 186th; in Community 3 / Sub-community 1: Psyspam (Mega Staraptor)
- **Basculegion / Rillaboom / Sneasler / Kingambit / Floette-Eternal / Incineroar**, Setup (Mega Floette), 4 copies, first Will Inabinet (2026-09-20), best 189th; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Indeedee-F / Sneasler / Dragapult / Volcarona / Kingambit / Gardevoir**, Psyspam (Mega Gardevoir), 4 copies, first Ben Grissmer (2026-09-20), best 197th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Aerodactyl / Rillaboom / Incineroar / Sneasler / Charizard / Gholdengo**, Sun Setup (Mega Aerodactyl, Mega Charizard-Y), 4 copies, first Madeline Rose (2026-09-20), best 243rd; in Community 0 / Sub-community 2: Setup
- **Salamence / Absol / Rillaboom / Arcanine-Hisui / Farigiraf / Gholdengo**, 4 copies, first nshf_psm3 (2026-09-12), best 682nd; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Salamence / Rillaboom / Lucario / Incineroar / Basculegion / Sylveon**, 4 copies, first gio_kuma (2026-09-10), best 1031st; in Community 0 / Sub-community 2: Setup
- **Raichu / Salamence / Primarina / Rillaboom / Arcanine-Hisui / Gholdengo**, 3 copies, first drhairychic0 (2026-09-20), best Champion; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Salamence / Gholdengo / Tyranitar / Sneasler / Rillaboom / Milotic**, Sand Setup (Mega Salamence, Mega Tyranitar), 3 copies, first Sidi I. Haidala (2026-09-12), best Champion; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Charizard / Kingambit / Meowstic-F / Sneasler / Indeedee / Kommo-o**, Sun Psyspam (Mega Charizard-Y, Mega Meowstic-F), 3 copies, first Yvar Vlieger (2026-09-27), best 3rd; in Community 1 / Sub-community 2: Psyspam (Mega Salamence)
- **Lucario / Indeedee-F / Armarouge / Sneasler / Salamence / Sylveon**, Psyspam Trick Room (Mega Lucario-Z, Mega Salamence), 3 copies, first Eclair_EqualAir (2026-09-12), best Top 4; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Rillaboom / Sneasler / Incineroar / Floette-Eternal / Blastoise / Indeedee-F**, Setup Trick Room (Mega Blastoise, Mega Floette), 3 copies, first Justin Tang (2026-09-09), best Top 4; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Froslass / Incineroar / Basculegion / Glimmora / Rillaboom / Volcarona**, Snow (Mega Froslass, Mega Glimmora), 3 copies, first Valentijn Visser (2026-09-27), best 7th; in Community 3 / Sub-community 4: Rillaboom / Glimmora@Glimmoranite
- **Sirfetch’d / Indeedee-F / Hatterene / Camerupt / Golisopod / Armarouge**, Psyspam Trick Room (Mega Camerupt, Mega Golisopod), 3 copies, first espertcg (2026-09-19), best 9th; in Community 3 / Sub-community 2: Trick Room Psyspam (Mega Camerupt)
- **Salamence / Gholdengo / Rillaboom / Milotic / Raichu / Ceruledge**, Setup (Mega Raichu-Y, Mega Salamence), 3 copies, first yura (2026-09-13), best 9th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Incineroar / Gengar / Rillaboom / Kommo-o / Ninetales-Alola / Kingambit**, Snow Perish Trap (Mega Gengar), 3 copies, first Minche Chung (2026-09-20), best 12th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Farigiraf / Sylveon / Whimsicott / Garchomp / Kingambit / Charizard**, Sun (Mega Charizard-Y), 3 copies, first Jamie Borenstein (2026-09-27), best 18th; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Gengar / Incineroar / Kingambit / Sneasler / Altaria / Rillaboom**, Perish Trap (Mega Altaria, Mega Gengar), 3 copies, first Paschalis Dermentzis (2026-09-27), best 20th; in Community 0 / Sub-community 2: Setup
- **Incineroar / Floette-Eternal / Raichu / Rillaboom / Volcarona / Basculegion**, 3 copies, first Noah Carney (2026-09-20), best 22nd; in Community 0 / Sub-community 2: Setup
- **Garchomp / Charizard / Froslass / Rillaboom / Kingambit / Sneasler**, Sun Snow (Mega Charizard-Y, Mega Froslass), 3 copies, first Sierra Elsbecker (2026-09-20), best 26th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Staraptor / Garchomp / Raichu / Gholdengo / Arcanine-Hisui / Rillaboom**, 3 copies, first Yan Chak Li (2026-09-20), best 33rd; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Pelipper / Archaludon / Swampert / Sinistcha / Incineroar / Golisopod**, Rain Trick Room (Mega Golisopod, Mega Swampert), 3 copies, first Michael Upton (2026-09-20), best 43rd; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Gengar / Incineroar / Kingambit / Rillaboom / Hippowdon / Sneasler**, Sand (Mega Gengar), 3 copies, first Austin Frank (2026-09-20), best 45th; in Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette)
- **Milotic / Staraptor / Gholdengo / Sinistcha / Excadrill / Tyranitar**, Sand (Mega Staraptor, Mega Tyranitar), 3 copies, first Bart van Doorn (2026-09-27), best 47th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Salamence / Gholdengo / Incineroar / Rillaboom / Basculegion / Floette-Eternal**, Setup (Mega Floette, Mega Salamence), 3 copies, first joserockzvgc (2026-09-16), best 50th; in Community 0 / Sub-community 2: Setup
- **Aegislash / Sylveon / Charizard / Garchomp / Venusaur / Incineroar**, Sun (Mega Charizard-Y), 3 copies, first Jonmarco Lappin (2026-09-20), best 54th; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Basculegion / Whimsicott / Arcanine-Hisui / Gardevoir / Kommo-o / Indeedee-F**, Psyspam (Mega Gardevoir), 3 copies, first Ian Brito (2026-09-20), best 57th; in Community 3 / Sub-community 3: Psyspam · Whimsicott
- **Rillaboom / Excadrill / Milotic / Volcarona / Tyranitar / Salamence**, Sand (Mega Salamence, Mega Tyranitar), 3 copies, first Alessio Umidio (2026-09-27), best 59th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Garchomp / Floette-Eternal / Incineroar / Sinistcha / Kingambit / Delphox**, Setup (Mega Floette, Mega Delphox), 3 copies, first Drew Kendziora (2026-09-20), best 62nd; in Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette)
- **Gardevoir / Dragonite / Arcanine-Hisui / Indeedee-F / Gholdengo / Rillaboom**, Psyspam Setup (Mega Dragonite, Mega Gardevoir), 3 copies, first Alex Landry (2026-09-20), best 63rd; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Venusaur / Pelipper / Annihilape / Swampert / Charizard / Archaludon**, Sun Rain (Mega Charizard-Y, Mega Swampert), 3 copies, first Michael Bianchi (2026-09-27), best 66th; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Incineroar / Kommo-o / Rillaboom / Salamence / Gholdengo / Raichu**, Setup (Mega Raichu-Y, Mega Salamence), 3 copies, first Yosuke Takayanagi (2026-09-20), best 68th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Arcanine-Hisui / Rillaboom / Gholdengo / Kingambit / Staraptor / Raichu**, 3 copies, first Ralf Kaufmann (2026-09-27), best 71st; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Archaludon / Golisopod / Pelipper / Grimmsnarl / Charizard / Farigiraf**, Sun Rain Screens Trick Room (Mega Charizard-Y, Mega Golisopod), 3 copies, first Edhen Soto (2026-09-20), best 72nd; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Archaludon / Incineroar / Swampert / Gengar / Politoed / Rillaboom**, Rain Perish Trap (Mega Gengar, Mega Swampert), 3 copies, first Darius Helmick (2026-09-20), best 79th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Kingambit / Rillaboom / Blaziken / Primarina / Farigiraf / Torkoal**, Sun Trick Room (Mega Blaziken), 3 copies, first Patrick Verrelli (2026-09-20), best 92nd; in Community 4 / Sub-community 1: Trick Room (Mega Golisopod)
- **Rillaboom / Sneasler / Arcanine-Hisui / Salamence / Gholdengo / Milotic**, Setup (Mega Salamence), 3 copies, first Ryan Jackson (2026-09-20), best 100th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Gardevoir / Indeedee-F / Armarouge / Annihilape / Lucario / Sneasler**, Psyspam (Mega Gardevoir, Mega Lucario-Z), 3 copies, first Jens Kassner (2026-09-27), best 116th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Armarouge / Indeedee-F / Salamence / Sneasler / Excadrill / Tyranitar**, Sand Psyspam (Mega Salamence, Mega Tyranitar), 3 copies, first U_DonPM (2026-09-09), best 119th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Primarina / Gholdengo / Rillaboom / Salamence / Raichu / Incineroar**, Setup (Mega Raichu-Y, Mega Salamence), 3 copies, first Thomas Caouette (2026-09-20), best 126th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Rillaboom / Raichu / Garchomp / Gholdengo / Sneasler / Arcanine-Hisui**, 3 copies, first Ben Schipper (2026-09-27), best 127th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Baxcalibur / Incineroar / Rillaboom / Volcarona / Sneasler / Basculegion**, Setup (Mega Baxcalibur), 3 copies, first Justin Tang (2026-09-14), best 132nd; in Community 3 / Sub-community 4: Rillaboom / Glimmora@Glimmoranite
- **Sneasler / Basculegion / Salamence / Indeedee / Gholdengo / Floette-Eternal**, Psyspam (Mega Floette, Mega Salamence), 3 copies, first Ian Larson (2026-09-20), best 147th; in Community 1 / Sub-community 2: Psyspam (Mega Salamence)
- **Incineroar / Kingambit / Gengar / Politoed / Rillaboom / Kommo-o**, Rain Perish Trap Setup (Mega Gengar), 3 copies, first Evelyn Klaus (2026-09-20), best 193rd; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Basculegion / Charizard / Incineroar / Garchomp / Floette-Eternal / Whimsicott**, Sun (Mega Charizard-Y, Mega Floette), 3 copies, first Nelson Fonkoua (2026-09-27), best 209th; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Indeedee-F / Gardevoir / Garchomp / Aerodactyl / Kingambit / Charizard**, Sun Psyspam (Mega Charizard-Y, Mega Gardevoir), 3 copies, first Calvin Tobias (2026-09-20), best 222nd; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Arcanine-Hisui / Rillaboom / Salamence / Gholdengo / Raichu / Kommo-o**, 3 copies, first Sam Trisciani (2026-09-20), best 226th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Rillaboom / Salamence / Kingambit / Sneasler / Incineroar / Basculegion**, Setup (Mega Salamence), 3 copies, first ragimali (2026-09-10), best 250th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Staraptor / Milotic / Rillaboom / Gholdengo / Tyranitar / Excadrill**, Sand (Mega Staraptor, Mega Tyranitar), 3 copies, first Daniel Martinez Camacho (2026-09-20), best 251st; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Tyranitar / Excadrill / Sneasler / Indeedee / Corviknight / Garchomp**, Sand Psyspam (Mega Garchomp-Z, Mega Tyranitar), 3 copies, first luixens (2026-09-12), best 270th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Basculegion / Glimmora / Typhlosion-Hisui / Indeedee / Kommo-o / Whimsicott**, Psyspam (Mega Glimmora), 3 copies, first Fran Martínez Pérez (2026-09-27), best 287th; in Community 3 / Sub-community 3: Psyspam · Whimsicott
- **Milotic / Sneasler / Salamence / Gholdengo / Indeedee / Tyranitar**, Sand Psyspam (Mega Salamence, Mega Tyranitar), 3 copies, first Thomas Campagna (2026-09-20), best 289th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Archaludon / Pelipper / Farigiraf / Sableye / Swampert / Golisopod**, Rain Trick Room Screens (Mega Golisopod, Mega Swampert), 3 copies, first Mitchell Huurdeman (2026-09-27), best 290th; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Archaludon / Golisopod / Incineroar / Pelipper / Rillaboom / Farigiraf**, Rain (Mega Golisopod), 3 copies, first Nico Schumann (2026-09-27), best 357th; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Charizard / Grimmsnarl / Hydreigon / Pelipper / Archaludon / Golisopod**, Sun Rain Screens (Mega Charizard-Y, Mega Golisopod), 3 copies, first Jimmy Do (2026-09-20), best 362nd; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Whimsicott / Floette-Eternal / Garchomp / Incineroar / Gholdengo / Charizard**, Sun (Mega Charizard-Y, Mega Floette), 3 copies, first Chase Badish (2026-09-20), best 364th; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Arcanine-Hisui / Gardevoir / Salamence / Indeedee-F / Basculegion / Sneasler**, Psyspam (Mega Gardevoir, Mega Salamence), 3 copies, first Pedro Feitosa (2026-09-10), best 365th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Rillaboom / Lucario / Salamence / Arcanine-Hisui / Basculegion / Sylveon**, 3 copies, first jonhy (2026-09-10), best 393rd; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Rillaboom / Metagross / Arcanine-Hisui / Salamence / Milotic / Sneasler**, 3 copies, first Alexander Rassael (2026-09-20), best 405th; in Community 0 / Sub-community 2: Setup
- **Dragapult / Staraptor / Armarouge / Whimsicott / Milotic / Indeedee-F**, Psyspam (Mega Staraptor), 3 copies, first Emily Olynick (2026-09-20), best 432nd; in Community 3 / Sub-community 1: Psyspam (Mega Staraptor)
- **Sneasler / Excadrill / Milotic / Tyranitar / Indeedee-F / Salamence**, Sand (Mega Salamence, Mega Tyranitar), 3 copies, first Harley Reno (2026-09-20), best 436th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Salamence / Incineroar / Kingambit / Basculegion / Floette-Eternal / Rillaboom**, 3 copies, first homura_kurenai_ (2026-09-09), best 483rd; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Primarina / Incineroar / Rillaboom / Farigiraf / Salamence / Lucario**, Trick Room (Mega Lucario-Z, Mega Salamence), 3 copies, first starportal_ (2026-09-11), best 540th; in Community 0 / Sub-community 2: Setup
- **Indeedee-F / Excadrill / Salamence / Tyranitar / Gholdengo / Sneasler**, Sand (Mega Salamence, Mega Tyranitar), 3 copies, first Drew Massey (2026-09-20), best 547th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Farigiraf / Kingambit / Pyroar / Rillaboom / Garchomp / Torkoal**, Sun Trick Room (Mega Garchomp-Z, Mega Pyroar), 3 copies, first Ramon Schong (2026-09-27), best 569th; in Community 4 / Sub-community 1: Trick Room (Mega Golisopod)
- **Gholdengo / Rillaboom / Staraptor / Raichu / Sneasler / Arcanine-Hisui**, 3 copies, first Christie Teehan (2026-09-20), best 642nd; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Floette-Eternal / Gholdengo / Sneasler / Rillaboom / Incineroar / Garchomp**, Setup (Mega Floette, Mega Garchomp-Z), 3 copies, first vi0ra_pokemon (2026-09-14), best 732nd; in Community 0 / Sub-community 2: Setup
- **Sneasler / Salamence / Gardevoir / Armarouge / Kingambit / Indeedee-F**, Psyspam Trick Room (Mega Gardevoir, Mega Salamence), 3 copies, first John Watson (2026-09-20), best 800th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Lucario / Gengar / Incineroar / Rillaboom / Politoed / Archaludon**, Rain Perish Trap (Mega Gengar, Mega Lucario-Z), 3 copies, first spy_anya11 (2026-09-19), best 1002nd; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)

## 8. Hybrids and Unassigned
- Hybrid teams: 1078
| Team | Primary | Hybrid |
| :--- | :--- | :--- |
| https://pokepast.es/76a971cc5c997172 | 3 | 1 |
| https://pokepast.es/73e5d6533b781089 | 1 | 0 |
| https://pokepast.es/6534595ea1b752f8 | 2 | 0 |
| https://pokepast.es/e328f6becb7a8d35 | 2 | 1 |
| https://pokepast.es/eb89982a260f186e | 0 | 1 |
| https://pokepast.es/a4fd7f400e9b4e49 | 3 | 0 |
| https://pokepast.es/a719c9e52c55cc43 | 3 | 1 |
| https://pokepast.es/b2d0e7f2f14c59e4 | 1 | 0 |
| https://pokepast.es/199e4fdf392315b2 | 0 | 1 |
| https://pokepast.es/c8f60c5168bd6a83 | 0 | 1 |
| https://pokepast.es/dad21fdac630175b | 0 | 2 |
| https://pokepast.es/4c6eb1d0d2cbb3f3 | 1 | 4 |
| https://pokepast.es/d4087f1527d4e0bc | 1 | 3 |
| https://pokepast.es/d0ee4dbd575e2250 | 3 | 1 |
| https://pokepast.es/4ebc34b995c1cba6 | 3 | 1 |
| https://pokepast.es/a1338edf35719660 | 1 | 4, 2 |
| https://pokepast.es/94cb88bc739ff2af | 0 | 1, 2 |
| https://pokepast.es/983fa284e2d45493 | 1 | 2, 0 |
| https://pokepast.es/961f0667b90b830c | 0 | 1 |
| https://pokepast.es/e8e7cf8271e66481 | 2 | 0 |
| https://pokepast.es/667cb69f9c820c84 | 1 | 0 |
| https://pokepast.es/c12df6cee74a35fe | 0 | 2 |
| https://pokepast.es/a88b8c2cfc274ab2 | 1 | 2, 0 |
| https://pokepast.es/3ac336df2eb4729b | 0 | 4, 2 |
| https://pokepast.es/a96e0b908f220884 | 0 | 2 |
| https://pokepast.es/bb66ff17a1c4a911 | 3 | 4 |
| https://pokepast.es/5d8e24080b6d3200 | 1 | 2, 0 |
| https://pokepast.es/bad1f0554790c541 | 1 | 0 |
| https://pokepast.es/adae5bcd485f81df | 0 | 2 |
| https://pokepast.es/e638bc044d3cb47f | 2 | 4, 0 |
| https://pokepast.es/5d77ccc3504a01b1 | 0 | 2 |
| https://pokepast.es/af730dd6acb60086 | 2 | 4, 0 |
| https://pokepast.es/b230239de70d9692 | 4 | 0 |
| https://pokepast.es/b54467e1aa4327e1 | 0 | 1, 2 |
| https://pokepast.es/fb6e8f3c9d655db6 | 0 | 1 |
| https://pokepast.es/f5b17f03de49b848 | 2 | 0, 1 |
| https://pokepast.es/3ad6655b53208446 | 2 | 1, 0 |
| https://pokepast.es/027fda21958e66de | 2 | 4, 0 |
| https://pokepast.es/81d3f0ffe6ef750c | 0 | 1 |
| https://pokepast.es/59b0a674b59141a2 | 1 | 0, 2 |
| https://pokepast.es/60002baa327ce677 | 1 | 3 |
| https://pokepast.es/7f30345697a16032 | 0 | 2, 1 |
| https://pokepast.es/900d357c0ea74c31 | 2 | 1 |
| https://pokepast.es/ede01efa45931c7c | 1 | 0 |
| https://pokepast.es/134747480705edc0 | 0 | 1, 2 |
| https://pokepast.es/01069fa4762c8613 | 3 | 0 |
| https://pokepast.es/60458cc1a20c2440 | 0 | 4, 2 |
| https://pokepast.es/36cd120de5ec394c | 0 | 1, 2 |
| https://pokepast.es/e2bab80c4e53d89a | 2 | 1 |
| https://pokepast.es/202c514602d9abe9 | 0 | 1, 2 |
| https://pokepast.es/42f46820310783d1 | 1 | 2, 0 |
| https://pokepast.es/595b9623f782bb8f | 3 | 1 |
| https://pokepast.es/24b7e1e280b1088f | 2 | 0 |
| https://pokepast.es/785dbfcd2432c102 | 1 | 2, 0 |
| https://pokepast.es/4ed17394f6517aaf | 3 | 1 |
| https://pokepast.es/f828e7b46a771515 | 3 | 4 |
| https://pokepast.es/9c4f7915b1d3ae07 | 2 | 0, 1 |
| https://pokepast.es/bed443f8a0acbe11 | 3 | 1 |
| https://pokepast.es/8536a2b4f89d3e72 | 1 | 2, 0 |
| https://pokepast.es/a8fc4cfab65cd411 | 2 | 1, 0 |
| https://pokepast.es/2c92e63c0fd4a43c | 1 | 0 |
| https://pokepast.es/db3ce4cbdae73dbe | 1 | 3 |
| https://pokepast.es/6c092ddbec51ef61 | 4 | 1 |
| https://pokepast.es/25ab0498e06c8ffb | 3 | 0, 2 |
| https://pokepast.es/37cf6b6b304aefd6 | 0 | 1 |
| https://pokepast.es/7fb12fe08230d7be | 2 | 1, 0 |
| https://pokepast.es/b18bfd4e8d8129be | 1 | 2 |
| https://pokepast.es/0f1752f2ba99c09f | 3 | 1 |
| https://pokepast.es/016cd16a7d929d4f | 2 | 1, 0 |
| https://pokepast.es/1e6a03dc1764214e | 2 | 1, 0 |
| https://pokepast.es/3020a0b5e5de04ae | 3 | 1 |
| https://pokepast.es/57ef0cda9c81a1c6 | 1 | 2 |
| https://pokepast.es/55029c098a5a309b | 0 | 2 |
| https://pokepast.es/674ac2a18201012a | 1 | 0 |
| https://pokepast.es/9fed7bfc061ad8bb | 0 | 2, 1 |
| https://pokepast.es/376176212f88e4cb | 4 | 3 |
| https://pokepast.es/6f1b0e8df51cc57d | 0 | 2, 1 |
| https://pokepast.es/183a005c8a677937 | 2 | 0 |
| https://pokepast.es/33b3042210ffc178 | 0 | 2, 1 |
| https://pokepast.es/8e37c3b00cbba6b6 | 0 | 4, 1, 2 |
| https://pokepast.es/2f512d91f56e0830 | 1 | 3, 4 |
| https://pokepast.es/5b9fae64b204f805 | 1 | 2, 0 |
| https://pokepast.es/d7be7226c71f3624 | 1 | 0 |
| https://pokepast.es/0071e895c381dd1c | 1 | 2, 0 |
| https://pokepast.es/07e91402b74462ef | 1 | 4 |
| https://pokepast.es/b70d72f44626b60d | 4 | 3 |
| https://pokepast.es/5268ba8166d67ebf | 2 | 0, 1 |
| https://pokepast.es/666b7365ce0b35f6 | 3 | 1 |
| https://pokepast.es/81427a109e744097 | 2 | 0, 1 |
| https://pokepast.es/7b073199fc857b04 | 3 | 4 |
| https://pokepast.es/e6497a1a671dac90 | 3 | 1 |
| https://pokepast.es/8f4c2600a4a63a90 | 2 | 1, 0 |
| https://pokepast.es/243c449b24b74266 | 1 | 2 |
| https://pokepast.es/b915ba990518d865 | 1 | 0, 3 |
| https://pokepast.es/90f7ccc7ab5b3d0b | 1 | 0 |
| https://pokepast.es/97edd96cf0de7f14 | 1 | 3 |
| https://pokepast.es/5a342ea870e2bbe8 | 1 | 0 |
| https://pokepast.es/045cc1b2f27f9315 | 0 | 2, 1 |
| https://pokepast.es/18d8e22d3d9ad026 | 0 | 1, 2 |
| https://pokepast.es/73a1ddc47375195f | 4 | 2 |
| https://pokepast.es/74aea655c757917f | 0 | 2, 1 |
| https://pokepast.es/9cc929d200baed42 | 1 | 3 |
| https://pokepast.es/a5b0fd2dfb4b9c13 | 1 | 2 |
| https://pokepast.es/412d57292e8dbd7e | 4 | 2 |
| https://pokepast.es/e74e8282d41a3802 | 2 | 3, 0, 4 |
| https://pokepast.es/a202a04735494175 | 0 | 2, 4 |
| https://pokepast.es/a630a7a5018325c9 | 0 | 2, 1 |
| https://pokepast.es/93c86d303e85d1da | 4 | 1 |
| https://pokepast.es/43bdff98a519db09 | 1 | 0 |
| https://pokepast.es/ca08bca9b981be5c | 3 | 2, 0 |
| https://pokepast.es/419384c3fd6c9e16 | 1 | 0 |
| https://pokepast.es/3c4611ccba18d35a | 0 | 4 |
| https://pokepast.es/2d9490a4361c8159 | 1 | 0 |
| https://pokepast.es/b6bace50909f3571 | 0 | 1 |
| https://pokepast.es/fdc0b961c0d0ef8c | 0 | 1, 2 |
| https://pokepast.es/ac6967ec268cbdf8 | 1 | 0 |
| https://pokepast.es/a5dfce8794a63b55 | 1 | 0 |
| https://pokepast.es/a702bf47f522438b | 0 | 4 |
| https://pokepast.es/7664efb6099c8d98 | 0 | 3, 1, 2 |
| https://pokepast.es/a4768a9bbf6876de | 2 | 0, 1 |
| https://pokepast.es/369e75b64155b6a1 | 3 | 1 |
| https://pokepast.es/0149eb0e0ddf41fe | 1 | 0 |
| https://pokepast.es/ad11cad6ec6b68f7 | 0 | 1, 2 |
| https://pokepast.es/4e238aef62574bf3 | 2 | 0 |
| https://pokepast.es/5a8934668d366537 | 1 | 0 |
| https://pokepast.es/8ba4c9c260b8e9ae | 1 | 3 |
| https://pokepast.es/f85b026e5b0e6567 | 2 | 1, 0, 4 |
| https://pokepast.es/9144f9e5950aaa23 | 1 | 0 |
| https://pokepast.es/8bc91085c2883385 | 3 | 1 |
| https://pokepast.es/a634bf11f53735ba | 0 | 1 |
| https://pokepast.es/12c7b27a8e53ed9c | 0 | 1 |
| https://pokepast.es/8ef29e6c905c1699 | 0 | 1 |
| https://pokepast.es/668502969512b159 | 2 | 3, 0, 4 |
| https://pokepast.es/eedf279fbc3ad845 | 1 | 0 |
| https://pokepast.es/e3bbfefb252a130d | 1 | 0 |
| https://pokepast.es/37083bbe99a1bf42 | 0 | 1, 2 |
| https://pokepast.es/0d18f72df8bd8d92 | 4 | 0 |
| https://pokepast.es/f6c343c1f47903cc | 1 | 0 |
| https://pokepast.es/b3e468f45c3c9ebb | 3 | 1 |
| https://pokepast.es/eb4962af8162ecf3 | 3 | 4 |
| https://pokepast.es/31945882c8d00260 | 3 | 1 |
| https://pokepast.es/8606abba447d2e2e | 0 | 2 |
| https://pokepast.es/ee07bfede4b559fc | 1 | 0 |
| https://pokepast.es/fd6fe5bb94a024fb | 0 | 1 |
| https://pokepast.es/472a153c121df28d | 1 | 3 |
| https://pokepast.es/348d665e808bb457 | 3 | 1, 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/rD6PrJBrCfzicynLnRAI | 2 | 1 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/10gY7gYrOnPAGK7Nudmq | 3 | 0 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/urXCEsyr6l208Bg4tX4o | 2 | 1 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/bASM2pmAEMjJYJ4ja7yI | 3 | 1 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/Ki07vssP2mbfd4C1zgZz | 1 | 3 |
| https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/tp1xMws30GePDkvo69zC | 3 | 0 |
| https://pokepast.es/e97cb94d7e8d7678 | 3 | 1 |
| https://pokepast.es/1a24e07035a9036e | 3 | 0 |
| https://pokepast.es/ea35d1777f660322 | 3 | 0 |
| https://pokepast.es/3ab405a83e618503 | 0 | 2, 1 |
| https://pokepast.es/dce537b603b784c6 | 2 | 0 |
| https://pokepast.es/097433efcc505367 | 0 | 1 |
| https://pokepast.es/a8154c05ecfa0e20 | 0 | 1 |
| https://pokepast.es/8cc6b2b7139e9db9 | 1 | 3 |
| https://pokepast.es/00c891d2f3287a74 | 4 | 2 |
| https://pokepast.es/2d99e9105e5f7a58 | 0 | 4 |
| https://pokepast.es/120461d751b66e3a | 2 | 4 |
| https://pokepast.es/a566dfb356531ce8 | 0 | 2 |
| https://pokepast.es/c06c65c34f56a094 | 2 | 0 |
| https://pokepast.es/b987727e0339084a | 4 | 0, 2 |
| https://pokepast.es/9e422cab36495fcb | 0 | 3, 1 |
| https://pokepast.es/652a6122d64aa2c1 | 0 | 1 |
| https://pokepast.es/6f1d5b2b15285f2b | 0 | 1, 2 |
| https://pokepast.es/98d6e2fba3995368 | 1 | 0 |
| https://pokepast.es/1c95ff346ce5b161 | 3 | 0, 1 |
| https://pokepast.es/ffe1c04c186b2453 | 4 | 3, 1 |
| https://pokepast.es/cea79bfa59e2fd6b | 0 | 1 |
| https://pokepast.es/93b626e0c928be89 | 3 | 1 |
| https://pokepast.es/817ae21505e679f7 | 1 | 4, 3 |
| https://pokepast.es/f22e21bb4d824590 | 2 | 0 |
| https://pokepast.es/ab47ccee10152485 | 4 | 0, 2 |
| https://pokepast.es/f85133bac58b317f | 0 | 1 |
| https://pokepast.es/78f9ef4e502c61d7 | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0235/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0032/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0029/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/1030/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0320/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/1059/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0652/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0642/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0783/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0790/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0037/player/0430/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0037/player/0556/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0634/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0487/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0037/player/0531/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0344/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0427/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0241/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0037/player/0084/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0989/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0882/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0026/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0108/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/1078/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0130/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0495/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0037/player/0273/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0612/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0890/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0494/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0354/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0904/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0613/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0252/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0102/teamlist | 4 | 1, 3 |
| https://standings.limitlessvgc.com/0037/player/0070/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0782/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/0098/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0260/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0640/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0703/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0321/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0648/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0188/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0178/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0915/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0187/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0893/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0862/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0171/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0134/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0476/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0498/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0037/player/1054/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0037/player/0204/teamlist | 0 | 1, 4, 2 |
| https://standings.limitlessvgc.com/0037/player/0306/teamlist | 1 | 4, 0 |
| https://standings.limitlessvgc.com/0037/player/0106/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0037/player/0903/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/1075/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0828/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0148/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0685/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0445/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0860/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0037/player/0114/teamlist | 1 | 2, 4, 0 |
| https://standings.limitlessvgc.com/0037/player/0318/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0266/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0454/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0037/player/0587/teamlist | 4 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/1006/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0037/player/0607/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/1051/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0551/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0037/player/0936/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0247/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0158/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0906/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0932/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0037/player/0137/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0805/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0554/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0287/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0727/teamlist | 0 | 1, 4, 2 |
| https://standings.limitlessvgc.com/0037/player/0714/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0464/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0792/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0787/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0502/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0500/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0911/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0660/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0086/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0438/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0900/teamlist | 0 | 3, 2 |
| https://standings.limitlessvgc.com/0037/player/0227/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0018/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0645/teamlist | 1 | 3, 2 |
| https://standings.limitlessvgc.com/0037/player/0639/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0501/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/0533/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0677/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0802/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0741/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0021/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0850/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0799/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/1001/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0955/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/0222/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0037/player/0215/teamlist | 1 | 4, 2 |
| https://standings.limitlessvgc.com/0037/player/0444/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0482/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0679/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0278/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0377/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0037/player/0156/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0226/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0988/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/1048/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0518/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/1072/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0173/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0489/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0311/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0876/teamlist | 2 | 4 |
| https://standings.limitlessvgc.com/0037/player/0926/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0663/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0061/teamlist | 1 | 0, 2 |
| https://standings.limitlessvgc.com/0037/player/0135/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0749/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0999/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0037/player/0040/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0065/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0118/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0115/teamlist | 1 | 0, 2, 4 |
| https://standings.limitlessvgc.com/0037/player/1026/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0037/player/0034/teamlist | 4 | 2, 3 |
| https://standings.limitlessvgc.com/0037/player/0796/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0462/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0472/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0182/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0978/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0192/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0288/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0376/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/1025/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0491/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0758/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0879/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0563/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0145/teamlist | 1 | 0, 3 |
| https://standings.limitlessvgc.com/0037/player/0848/teamlist | 3 | 0, 2 |
| https://standings.limitlessvgc.com/0037/player/0624/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0690/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0658/teamlist | 0 | 4, 2 |
| https://standings.limitlessvgc.com/0037/player/0243/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0593/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0536/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0813/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/1058/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/1055/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0211/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0797/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0601/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0037/player/0258/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0866/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0590/teamlist | 4 | 2, 3 |
| https://standings.limitlessvgc.com/0037/player/0415/teamlist | 1 | 2 |
| https://standings.limitlessvgc.com/0037/player/0465/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0228/teamlist | 2 | 4 |
| https://standings.limitlessvgc.com/0037/player/0747/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0635/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0185/teamlist | 1 | 2, 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0016/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0442/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0560/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0975/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0426/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0126/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0067/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0691/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0033/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0037/player/0981/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0410/teamlist | 2 | 4 |
| https://standings.limitlessvgc.com/0037/player/0908/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0938/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0468/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0078/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0702/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0628/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0382/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0019/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0606/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0548/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0101/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0641/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0844/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0458/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0037/player/0219/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0074/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0970/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0037/player/0525/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/1056/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0423/teamlist | 2 | 4, 0 |
| https://standings.limitlessvgc.com/0037/player/0411/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0535/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0959/teamlist | 1 | 3, 4 |
| https://standings.limitlessvgc.com/0037/player/0105/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0450/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0286/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0275/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0976/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0223/teamlist | 0 | 3, 1 |
| https://standings.limitlessvgc.com/0037/player/0250/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0292/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0810/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0091/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0037/player/0058/teamlist | 3 | 4, 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0985/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0811/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0542/teamlist | 0 | 2, 4 |
| https://standings.limitlessvgc.com/0037/player/0214/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0037/player/0263/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0249/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0031/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0296/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0015/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0455/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0732/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0422/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0575/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0420/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0399/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0174/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0488/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0303/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/1079/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0087/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0738/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0901/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0576/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0827/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0740/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/1023/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0037/player/0024/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0762/teamlist | 2 | 3 |
| https://standings.limitlessvgc.com/0037/player/0282/teamlist | 0 | 1, 2, 4 |
| https://standings.limitlessvgc.com/0037/player/0230/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0037/player/0967/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0928/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0617/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0997/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/1070/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0324/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0564/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0698/teamlist | 1 | 3, 0 |
| https://standings.limitlessvgc.com/0037/player/0129/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0202/teamlist | 1 | 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0168/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0877/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0447/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0418/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0667/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0037/player/0335/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0039/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0369/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/1017/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0569/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0874/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/1016/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0224/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0402/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0054/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0653/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/1008/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0150/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0152/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0325/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/1002/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0104/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0163/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0885/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0262/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0562/teamlist | 4 | 1, 3 |
| https://standings.limitlessvgc.com/0037/player/0608/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/1071/teamlist | 2 | 4, 0 |
| https://standings.limitlessvgc.com/0037/player/0221/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0037/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0742/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0006/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0183/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0195/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0429/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0983/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0829/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/1067/teamlist | 3 | 4, 2 |
| https://standings.limitlessvgc.com/0037/player/0878/teamlist | 1 | 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0559/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0092/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0049/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/1003/teamlist | 4 | 2, 0, 3 |
| https://standings.limitlessvgc.com/0037/player/0694/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0385/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0037/player/0332/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0627/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0037/player/1039/teamlist | 1 | 2 |
| https://standings.limitlessvgc.com/0037/player/0014/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0776/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0873/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0413/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0459/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0037/player/0774/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0692/teamlist | 2 | 3, 0 |
| https://standings.limitlessvgc.com/0037/player/0902/teamlist | 0 | 4, 1, 3 |
| https://standings.limitlessvgc.com/0037/player/1010/teamlist | 1 | 3, 0 |
| https://standings.limitlessvgc.com/0037/player/0705/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0103/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0012/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0440/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0132/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/1033/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0977/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0849/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0172/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0175/teamlist | 1 | 2, 4, 0 |
| https://standings.limitlessvgc.com/0037/player/0238/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0404/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0149/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0013/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0836/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0451/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0037/player/0516/teamlist | 1 | 0, 4, 3 |
| https://standings.limitlessvgc.com/0037/player/0567/teamlist | 0 | 1, 2, 4 |
| https://standings.limitlessvgc.com/0037/player/0534/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0205/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0883/teamlist | 4 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0962/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0331/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/1062/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0268/teamlist | 1 | 2 |
| https://standings.limitlessvgc.com/0037/player/1014/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0233/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0046/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0941/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0475/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0958/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0935/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0847/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0629/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0037/player/0897/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0734/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0638/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/1042/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0396/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0082/teamlist | 2 | 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0339/teamlist | 2 | 3 |
| https://standings.limitlessvgc.com/0037/player/0081/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0206/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0007/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0416/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0598/teamlist | 3 | 0, 2 |
| https://standings.limitlessvgc.com/0037/player/0441/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/1021/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0037/player/0159/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0210/teamlist | 4 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0232/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0131/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0919/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/1038/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0678/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0265/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/0406/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0979/teamlist | 4 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0907/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/1061/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0701/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0037/player/0945/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/0368/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0581/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0080/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/0373/teamlist | 4 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0704/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0037/player/0143/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0838/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0272/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0693/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0767/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0914/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0616/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0473/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0216/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0037/player/0297/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0037/player/0775/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0077/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0037/player/0671/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0269/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0037/player/0681/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0809/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0948/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0421/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0461/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0037/player/0674/teamlist | 2 | 4 |
| https://standings.limitlessvgc.com/0037/player/0659/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0676/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0394/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0094/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0037/player/0651/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0284/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/1052/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0499/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/1077/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0116/teamlist | 0 | 1, 4 |
| https://standings.limitlessvgc.com/0037/player/0392/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0717/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0968/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0474/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0037/player/0478/teamlist | 1 | 0, 4 |
| https://standings.limitlessvgc.com/0037/player/0909/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0037/player/0579/teamlist | 4 | 1, 0 |
| https://standings.limitlessvgc.com/0037/player/0552/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0485/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0353/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0037/player/0496/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0090/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0037/player/0753/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0768/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0864/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0687/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0037/player/0456/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0248/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0037/player/0761/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0647/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0099/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0657/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0037/player/0446/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0037/player/0384/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0285/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0194/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0037/player/0123/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0037/player/0804/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0998/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0037/player/0512/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0038/player/0040/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0302/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0038/player/0085/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0038/player/0315/teamlist | 3 | 4, 1 |
| https://standings.limitlessvgc.com/0038/player/0175/teamlist | 1 | 2, 4 |
| https://standings.limitlessvgc.com/0038/player/0118/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0229/teamlist | 2 | 4, 0 |
| https://standings.limitlessvgc.com/0038/player/0275/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0038/player/0260/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0038/player/0234/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0181/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0307/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0124/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0058/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0038/player/0272/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0038/player/0243/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0212/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0180/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0004/teamlist | 0 | 4, 2 |
| https://standings.limitlessvgc.com/0038/player/0237/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0006/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0245/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0038/player/0164/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0038/player/0030/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0092/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0262/teamlist | 1 | 4, 3 |
| https://standings.limitlessvgc.com/0038/player/0228/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0038/player/0156/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0038/player/0053/teamlist | 4 | 1, 3 |
| https://standings.limitlessvgc.com/0038/player/0079/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0038/player/0177/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0108/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0038/player/0231/teamlist | 2 | 0, 4 |
| https://standings.limitlessvgc.com/0038/player/0084/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0078/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0256/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0038/player/0317/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0184/teamlist | 2 | 4, 0 |
| https://standings.limitlessvgc.com/0038/player/0063/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0038/player/0125/teamlist | 3 | 2, 0 |
| https://standings.limitlessvgc.com/0038/player/0035/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0001/teamlist | 4 | 1, 0 |
| https://standings.limitlessvgc.com/0038/player/0173/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0252/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0151/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0038/player/0011/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0123/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0314/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0038/player/0091/teamlist | 3 | 0, 2, 4 |
| https://standings.limitlessvgc.com/0038/player/0089/teamlist | 2 | 4, 0 |
| https://standings.limitlessvgc.com/0038/player/0064/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0038/player/0066/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0038/player/0020/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0044/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0297/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0038/player/0074/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0038/player/0325/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0038/player/0213/teamlist | 4 | 2, 0 |
| https://standings.limitlessvgc.com/0038/player/0230/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0038/player/0135/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0038/player/0222/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0104/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0038/player/0204/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0038/player/0073/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0308/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0292/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0186/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0038/player/0150/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0321/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0038/player/0088/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0155/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0038/player/0027/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0038/player/0251/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0046/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0038/player/0009/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0038/player/0005/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0198/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0038/player/0059/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0038/player/0143/teamlist | 3 | 1, 4 |
| https://standings.limitlessvgc.com/0038/player/0013/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0038/player/0003/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0038/player/0054/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0254/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0038/player/0056/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0093/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0138/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0211/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0253/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0038/player/0120/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0038/player/0294/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0038/player/0121/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0038/player/0086/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0039/player/0423/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0219/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0818/teamlist | 3 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0703/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0655/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0737/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0846/teamlist | 0 | 1, 4, 2 |
| https://standings.limitlessvgc.com/0039/player/0546/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0733/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0039/player/0404/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0751/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0616/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/1013/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/0471/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/1043/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0039/player/0297/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0605/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0204/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0266/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0397/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/0364/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/0899/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0345/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/1054/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/0633/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0667/teamlist | 2 | 4 |
| https://standings.limitlessvgc.com/0039/player/0032/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0965/teamlist | 3 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/1082/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0039/player/0004/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0757/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0741/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0335/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0870/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0039/player/1031/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/1034/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0120/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/0873/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0798/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0722/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0039/player/1006/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/1063/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0752/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0754/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0080/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/1033/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0573/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/0939/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0375/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0064/teamlist | 1 | 4 |
| https://standings.limitlessvgc.com/0039/player/0037/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0538/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0250/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/1113/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0863/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0878/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0702/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/1022/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0813/teamlist | 0 | 1, 4, 2 |
| https://standings.limitlessvgc.com/0039/player/0069/teamlist | 0 | 1, 4, 2 |
| https://standings.limitlessvgc.com/0039/player/1010/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0289/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0018/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0045/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0866/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0362/teamlist | 4 | 0, 1 |
| https://standings.limitlessvgc.com/0039/player/0850/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0030/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/0028/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0039/player/0528/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0617/teamlist | 2 | 0, 4, 3 |
| https://standings.limitlessvgc.com/0039/player/0653/teamlist | 2 | 4, 3 |
| https://standings.limitlessvgc.com/0039/player/1073/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0496/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0087/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/1093/teamlist | 2 | 0, 4, 3 |
| https://standings.limitlessvgc.com/0039/player/0146/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0017/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0869/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0541/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0099/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0984/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0622/teamlist | 0 | 2, 4, 3 |
| https://standings.limitlessvgc.com/0039/player/0590/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0593/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0113/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0688/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0934/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0458/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0039/player/0780/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0039/player/0947/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0203/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/0294/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0235/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0592/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0039/player/0199/teamlist | 2 | 0, 3 |
| https://standings.limitlessvgc.com/0039/player/0641/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0569/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0360/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0476/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0039/player/0278/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0995/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/0902/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0581/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0101/teamlist | 3 | 1, 4 |
| https://standings.limitlessvgc.com/0039/player/0647/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0434/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0417/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0794/teamlist | 2 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/1114/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0432/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0361/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0313/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0039/player/0319/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0307/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0431/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/0485/teamlist | 3 | 2 |
| https://standings.limitlessvgc.com/0039/player/0594/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0116/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0025/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0039/player/0778/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0542/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0487/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0796/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0073/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0129/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0075/teamlist | 3 | 4, 1 |
| https://standings.limitlessvgc.com/0039/player/0946/teamlist | 1 | 0, 3 |
| https://standings.limitlessvgc.com/0039/player/0108/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/1110/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0322/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/1053/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/1098/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0826/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0924/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0819/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0795/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0775/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0614/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0527/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0962/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0521/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0513/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0053/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/1078/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0950/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0140/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0925/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0729/teamlist | 0 | 4, 2 |
| https://standings.limitlessvgc.com/0039/player/0711/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/1070/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0019/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0683/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0455/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0169/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/1123/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0200/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0039/player/0760/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0498/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0629/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/0370/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0784/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0773/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0039/player/0689/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0039/player/0260/teamlist | 1 | 2 |
| https://standings.limitlessvgc.com/0039/player/0932/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/1091/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0090/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0607/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0039/player/0747/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0039/player/0587/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0990/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0385/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0388/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0875/teamlist | 3 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0743/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0948/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0386/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0039/player/0719/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0687/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0039/player/0967/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0039/player/0626/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0343/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0963/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0708/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0367/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0970/teamlist | 4 | 3, 2 |
| https://standings.limitlessvgc.com/0039/player/0739/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0414/teamlist | 4 | 3, 2 |
| https://standings.limitlessvgc.com/0039/player/0155/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0864/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0731/teamlist | 0 | 1, 4, 2 |
| https://standings.limitlessvgc.com/0039/player/1058/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0256/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0357/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0854/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0228/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0457/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0039/player/0185/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0039/player/0994/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0988/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0646/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0562/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0039/player/0366/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0349/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0329/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0809/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0479/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0505/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0916/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0039/player/0351/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0039/player/0997/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0039/player/0426/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0852/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/1039/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0410/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0264/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0803/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0604/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0624/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0922/teamlist | 4 | 1 |
| https://standings.limitlessvgc.com/0039/player/0912/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0987/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0221/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0285/teamlist | 3 | 1, 4 |
| https://standings.limitlessvgc.com/0039/player/0079/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0180/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0065/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0153/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0039/player/1003/teamlist | 1 | 3, 4 |
| https://standings.limitlessvgc.com/0039/player/0908/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0227/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0039/player/0356/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0150/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/0084/teamlist | 1 | 4, 3 |
| https://standings.limitlessvgc.com/0039/player/0127/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0207/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/1074/teamlist | 4 | 1, 3 |
| https://standings.limitlessvgc.com/0039/player/0232/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0071/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0039/player/0639/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0382/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0024/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0677/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0841/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0287/teamlist | 3 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0376/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0481/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/0067/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0160/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0066/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0039/player/0881/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0006/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0211/teamlist | 4 | 3, 1 |
| https://standings.limitlessvgc.com/0039/player/0598/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/1062/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0036/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0493/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/1046/teamlist | 4 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0213/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0137/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0302/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0500/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/0859/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0952/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0039/player/0057/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0543/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0346/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0095/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0428/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0005/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0166/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0824/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0919/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0039/player/0022/teamlist | 3 | 1, 0 |
| https://standings.limitlessvgc.com/0039/player/0143/teamlist | 0 | 2, 3 |
| https://standings.limitlessvgc.com/0039/player/0668/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0159/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0242/teamlist | 1 | 0, 3 |
| https://standings.limitlessvgc.com/0039/player/1008/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0996/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0433/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0039/player/0161/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0867/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0992/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0536/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0555/teamlist | 1 | 3, 2 |
| https://standings.limitlessvgc.com/0039/player/1117/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0612/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0564/teamlist | 3 | 4 |
| https://standings.limitlessvgc.com/0039/player/0734/teamlist | 3 | 2 |
| https://standings.limitlessvgc.com/0039/player/0966/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0271/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0039/player/0068/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0214/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0510/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/1127/teamlist | 1 | 2, 0 |
| https://standings.limitlessvgc.com/0039/player/0420/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0725/teamlist | 2 | 4, 0 |
| https://standings.limitlessvgc.com/0039/player/0915/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0822/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0572/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/1089/teamlist | 4 | 0, 3, 1 |
| https://standings.limitlessvgc.com/0039/player/1090/teamlist | 3 | 2 |
| https://standings.limitlessvgc.com/0039/player/0407/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/1087/teamlist | 1 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0623/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0767/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0599/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0672/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0872/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0468/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0327/teamlist | 0 | 1, 2 |
| https://standings.limitlessvgc.com/0039/player/0050/teamlist | 2 | 3, 0, 4 |
| https://standings.limitlessvgc.com/0039/player/0923/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0812/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/1028/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0236/teamlist | 2 | 3, 4 |
| https://standings.limitlessvgc.com/0039/player/0374/teamlist | 0 | 2, 4 |
| https://standings.limitlessvgc.com/0039/player/0394/teamlist | 3 | 1, 4 |
| https://standings.limitlessvgc.com/0039/player/0975/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/0691/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0983/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/1125/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0910/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0039/player/0206/teamlist | 2 | 4, 3 |
| https://standings.limitlessvgc.com/0039/player/0862/teamlist | 3 | 0 |
| https://standings.limitlessvgc.com/0039/player/0268/teamlist | 2 | 0, 1 |
| https://standings.limitlessvgc.com/0039/player/1094/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/0540/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/0827/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0189/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0133/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0026/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0243/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0475/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0421/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0378/teamlist | 2 | 4, 3 |
| https://standings.limitlessvgc.com/0039/player/0011/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0331/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0093/teamlist | 0 | 1 |
| https://standings.limitlessvgc.com/0039/player/0419/teamlist | 1 | 0, 2 |
| https://standings.limitlessvgc.com/0039/player/0038/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0621/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0317/teamlist | 1 | 3 |
| https://standings.limitlessvgc.com/0039/player/0007/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0645/teamlist | 0 | 4 |
| https://standings.limitlessvgc.com/0039/player/0014/teamlist | 4 | 2, 0 |
| https://standings.limitlessvgc.com/0039/player/0183/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0389/teamlist | 0 | 3 |
| https://standings.limitlessvgc.com/0039/player/0168/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0052/teamlist | 3 | 1 |
| https://standings.limitlessvgc.com/0039/player/0651/teamlist | 0 | 2 |
| https://standings.limitlessvgc.com/0039/player/1015/teamlist | 4 | 3 |
| https://standings.limitlessvgc.com/0039/player/0772/teamlist | 4 | 2 |
| https://standings.limitlessvgc.com/0039/player/0550/teamlist | 0 | 2, 1 |
| https://standings.limitlessvgc.com/0039/player/0379/teamlist | 2 | 1 |
| https://standings.limitlessvgc.com/0039/player/0806/teamlist | 2 | 4, 3 |
| https://standings.limitlessvgc.com/0039/player/0509/teamlist | 4 | 0 |
| https://standings.limitlessvgc.com/0039/player/0648/teamlist | 2 | 0 |
| https://standings.limitlessvgc.com/0039/player/0321/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0039/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0037/player/0987/teamlist | 4 | 1, 3 |
| https://standings.limitlessvgc.com/0039/player/0954/teamlist | 1 | 0 |
| https://standings.limitlessvgc.com/0039/player/0565/teamlist | 1 | 2 |
- Unassigned teams: 8 (share 0.3%)
| Team |
| :--- |
| https://pokepast.es/efddc5627f766a06 |
| https://standings.limitlessvgc.com/0037/player/0025/teamlist |
| https://standings.limitlessvgc.com/0037/player/0345/teamlist |
| https://standings.limitlessvgc.com/0038/player/0201/teamlist |
| https://standings.limitlessvgc.com/0038/player/0304/teamlist |
| https://standings.limitlessvgc.com/0039/player/0698/teamlist |
| https://standings.limitlessvgc.com/0039/player/0092/teamlist |
| https://standings.limitlessvgc.com/0039/player/0736/teamlist |

## 9. Communities vs. Cluster Archetypes
Rows: each team's primary community (or unassigned); columns: its cluster from the cluster CLI.
| Community | cluster-1 | cluster-2 | cluster-3 | cluster-4 | cluster-5 | cluster-6 | cluster-7 | cluster-8 | cluster-9 | cluster-10 | cluster-11 | cluster-12 | cluster-13 | cluster-14 | cluster-15 | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Community 0: Rillaboom / Raichu / Gholdengo | 613 | 51 | 0 | 2 | 0 | 136 | 14 | 6 | 3 | 37 | 41 | 6 | 19 | 1 | 0 | 48 |
| Community 1: Sneasler / Salamence | 94 | 85 | 60 | 239 | 0 | 0 | 1 | 0 | 6 | 9 | 11 | 2 | 30 | 5 | 0 | 45 |
| Community 2: Setup (Mega Floette) | 0 | 167 | 0 | 0 | 0 | 2 | 4 | 6 | 0 | 7 | 0 | 1 | 0 | 7 | 0 | 33 |
| Community 3: Psyspam | 2 | 6 | 178 | 2 | 0 | 1 | 1 | 1 | 93 | 7 | 19 | 0 | 2 | 15 | 41 | 111 |
| Community 4: Rain | 1 | 21 | 8 | 0 | 223 | 1 | 94 | 100 | 3 | 43 | 1 | 52 | 8 | 20 | 3 | 59 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 8 |
- Cluster majority:
| Cluster | Archetype | Majority community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Community 0: Rillaboom / Raichu / Gholdengo | 86.3% |
| cluster-2 | Mega Floette-Eternal Balance | Community 2: Setup (Mega Floette) | 50.6% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Community 3: Psyspam | 72.4% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Community 1: Sneasler / Salamence | 98.4% |
| cluster-5 | Mega Golisopod + Archaludon/Pelipper Rain | Community 4: Rain | 100.0% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Community 0: Rillaboom / Raichu / Gholdengo | 97.1% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Community 4: Rain | 82.5% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Community 4: Rain | 88.5% |
| cluster-9 | Mega Staraptor Psyspam Offense | Community 3: Psyspam | 88.6% |
| cluster-10 | Mega Golisopod + Rillaboom/Incineroar Trick Room | Community 4: Rain | 41.7% |
| cluster-11 | Mega Raichu Balance | Community 0: Rillaboom / Raichu / Gholdengo | 56.9% |
| cluster-12 | Mega Golisopod Rain | Community 4: Rain | 85.2% |
| cluster-13 | Mega Salamence Trick Room | Community 1: Sneasler / Salamence | 50.8% |
| cluster-14 | Mega Charizard + Whimsicott/Garchomp Tailwind Offense | Community 4: Rain | 41.7% |
| cluster-15 | Mega Camerupt + Indeedee-F/Hatterene Psyspam Trick Room | Community 3: Psyspam | 93.2% |

### Sub-communities of Community 0: Rillaboom / Raichu / Gholdengo vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-4 (Mega Salamence + Tyranitar/Excadrill Sand Balance) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-8 (Mega Gengar + Rillaboom/Incineroar Rain) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Golisopod + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Raichu Balance) | cluster-12 (Mega Golisopod Rain) | cluster-13 (Mega Salamence Trick Room) | cluster-14 (Mega Charizard + Whimsicott/Garchomp Tailwind Offense) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 437 | 5 | 1 | 113 | 4 | 0 | 3 | 15 | 12 | 1 | 14 | 1 | 33 |
| Sub-community 2: Setup | 116 | 45 | 0 | 20 | 8 | 0 | 0 | 20 | 17 | 4 | 0 | 0 | 2 |
| Sub-community 4: Volcarona / Glimmora@Glimmoranite | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 4 |
| Sub-community 5: Perish Trap (Mega Gengar) | 0 | 0 | 0 | 1 | 0 | 6 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| minor | 60 | 1 | 0 | 2 | 2 | 0 | 0 | 2 | 8 | 1 | 5 | 0 | 5 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 4 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 71.3% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 2: Setup | 88.2% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 50.0% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 83.1% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 2: Setup | 57.1% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Sub-community 5: Perish Trap (Mega Gengar) | 100.0% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 100.0% |
| cluster-10 | Mega Golisopod + Rillaboom/Incineroar Trick Room | Sub-community 2: Setup | 54.1% |
| cluster-11 | Mega Raichu Balance | Sub-community 2: Setup | 41.5% |
| cluster-12 | Mega Golisopod Rain | Sub-community 2: Setup | 66.7% |
| cluster-13 | Mega Salamence Trick Room | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 73.7% |
| cluster-14 | Mega Charizard + Whimsicott/Garchomp Tailwind Offense | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 100.0% |

### Sub-communities of Community 1: Sneasler / Salamence vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-3 (Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense) | cluster-4 (Mega Salamence + Tyranitar/Excadrill Sand Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Golisopod + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Raichu Balance) | cluster-12 (Mega Golisopod Rain) | cluster-13 (Mega Salamence Trick Room) | cluster-14 (Mega Charizard + Whimsicott/Garchomp Tailwind Offense) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 19 | 1 | 0 | 202 | 0 | 2 | 5 | 0 | 0 | 0 | 0 | 15 |
| Sub-community 1: Kingambit / Rillaboom | 66 | 78 | 38 | 2 | 1 | 0 | 4 | 11 | 1 | 27 | 3 | 8 |
| Sub-community 2: Psyspam (Mega Salamence) | 8 | 6 | 22 | 35 | 0 | 4 | 0 | 0 | 1 | 3 | 2 | 17 |
| unassigned | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 5 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 1: Kingambit / Rillaboom | 70.2% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 1: Kingambit / Rillaboom | 91.8% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Sub-community 1: Kingambit / Rillaboom | 63.3% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 84.5% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 1: Kingambit / Rillaboom | 100.0% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 2: Psyspam (Mega Salamence) | 66.7% |
| cluster-10 | Mega Golisopod + Rillaboom/Incineroar Trick Room | Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 55.6% |
| cluster-11 | Mega Raichu Balance | Sub-community 1: Kingambit / Rillaboom | 100.0% |
| cluster-12 | Mega Golisopod Rain | Sub-community 1: Kingambit / Rillaboom | 50.0% |
| cluster-13 | Mega Salamence Trick Room | Sub-community 1: Kingambit / Rillaboom | 90.0% |
| cluster-14 | Mega Charizard + Whimsicott/Garchomp Tailwind Offense | Sub-community 1: Kingambit / Rillaboom | 60.0% |

### Sub-communities of Community 2: Setup (Mega Floette) vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-2 (Mega Floette-Eternal Balance) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-8 (Mega Gengar + Rillaboom/Incineroar Rain) | cluster-10 (Mega Golisopod + Rillaboom/Incineroar Trick Room) | cluster-12 (Mega Golisopod Rain) | cluster-14 (Mega Charizard + Whimsicott/Garchomp Tailwind Offense) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Setup (Mega Floette) · Incineroar | 83 | 1 | 3 | 1 | 5 | 0 | 7 | 5 |
| Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 35 | 1 | 0 | 2 | 2 | 1 | 0 | 1 |
| Sub-community 2: Setup (Mega Delphox, Mega Floette) | 45 | 0 | 1 | 0 | 0 | 0 | 0 | 26 |
| Sub-community 3: Gengar@Gengarite / Kommo-o | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 |
| minor | 4 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 0: Setup (Mega Floette) · Incineroar | 49.7% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Sub-community 0: Setup (Mega Floette) · Incineroar | 50.0% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 0: Setup (Mega Floette) · Incineroar | 75.0% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Sub-community 3: Gengar@Gengarite / Kommo-o | 50.0% |
| cluster-10 | Mega Golisopod + Rillaboom/Incineroar Trick Room | Sub-community 0: Setup (Mega Floette) · Incineroar | 71.4% |
| cluster-12 | Mega Golisopod Rain | Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 100.0% |
| cluster-14 | Mega Charizard + Whimsicott/Garchomp Tailwind Offense | Sub-community 0: Setup (Mega Floette) · Incineroar | 100.0% |

### Sub-communities of Community 3: Psyspam vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-3 (Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense) | cluster-4 (Mega Salamence + Tyranitar/Excadrill Sand Balance) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-8 (Mega Gengar + Rillaboom/Incineroar Rain) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Golisopod + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Raichu Balance) | cluster-13 (Mega Salamence Trick Room) | cluster-14 (Mega Charizard + Whimsicott/Garchomp Tailwind Offense) | cluster-15 (Mega Camerupt + Indeedee-F/Hatterene Psyspam Trick Room) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Psyspam (Mega Gardevoir) | 2 | 1 | 143 | 2 | 0 | 0 | 0 | 39 | 0 | 0 | 0 | 2 | 1 | 24 |
| Sub-community 1: Psyspam (Mega Staraptor) | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 45 | 0 | 0 | 0 | 0 | 0 | 21 |
| Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 0 | 0 | 10 | 0 | 0 | 1 | 0 | 9 | 0 | 0 | 2 | 1 | 40 | 15 |
| Sub-community 3: Psyspam · Whimsicott | 0 | 0 | 20 | 0 | 0 | 0 | 0 | 0 | 0 | 2 | 0 | 12 | 0 | 18 |
| minor | 0 | 5 | 2 | 0 | 0 | 0 | 1 | 0 | 7 | 17 | 0 | 0 | 0 | 25 |
| unassigned | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 8 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 0: Psyspam (Mega Gardevoir) | 100.0% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 83.3% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Sub-community 0: Psyspam (Mega Gardevoir) | 80.3% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Sub-community 0: Psyspam (Mega Gardevoir) | 100.0% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 100.0% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 100.0% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 1: Psyspam (Mega Staraptor) | 48.4% |
| cluster-10 | Mega Golisopod + Rillaboom/Incineroar Trick Room | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 100.0% |
| cluster-11 | Mega Raichu Balance | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 89.5% |
| cluster-13 | Mega Salamence Trick Room | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 100.0% |
| cluster-14 | Mega Charizard + Whimsicott/Garchomp Tailwind Offense | Sub-community 3: Psyspam · Whimsicott | 80.0% |
| cluster-15 | Mega Camerupt + Indeedee-F/Hatterene Psyspam Trick Room | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 97.6% |

### Sub-communities of Community 4: Rain vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-3 (Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense) | cluster-5 (Mega Golisopod + Archaludon/Pelipper Rain) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-8 (Mega Gengar + Rillaboom/Incineroar Rain) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Golisopod + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Raichu Balance) | cluster-12 (Mega Golisopod Rain) | cluster-13 (Mega Salamence Trick Room) | cluster-14 (Mega Charizard + Whimsicott/Garchomp Tailwind Offense) | cluster-15 (Mega Camerupt + Indeedee-F/Hatterene Psyspam Trick Room) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Rain (Mega Golisopod) | 1 | 1 | 4 | 132 | 0 | 0 | 2 | 3 | 16 | 0 | 47 | 0 | 0 | 0 | 18 |
| Sub-community 1: Trick Room (Mega Golisopod) | 0 | 15 | 0 | 30 | 1 | 16 | 0 | 0 | 27 | 1 | 5 | 5 | 0 | 2 | 19 |
| Sub-community 2: Sun (Mega Charizard-Y) | 0 | 3 | 4 | 61 | 0 | 78 | 1 | 0 | 0 | 0 | 0 | 3 | 20 | 1 | 13 |
| Sub-community 3: Rain Perish Trap (Mega Gengar) | 0 | 2 | 0 | 0 | 0 | 0 | 97 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 |
| minor | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 5 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 0: Rain (Mega Golisopod) | 100.0% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 1: Trick Room (Mega Golisopod) | 71.4% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Sub-community 0: Rain (Mega Golisopod) | 50.0% |
| cluster-5 | Mega Golisopod + Archaludon/Pelipper Rain | Sub-community 0: Rain (Mega Golisopod) | 59.2% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Sub-community 1: Trick Room (Mega Golisopod) | 100.0% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 2: Sun (Mega Charizard-Y) | 83.0% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Sub-community 3: Rain Perish Trap (Mega Gengar) | 97.0% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 0: Rain (Mega Golisopod) | 100.0% |
| cluster-10 | Mega Golisopod + Rillaboom/Incineroar Trick Room | Sub-community 1: Trick Room (Mega Golisopod) | 62.8% |
| cluster-11 | Mega Raichu Balance | Sub-community 1: Trick Room (Mega Golisopod) | 100.0% |
| cluster-12 | Mega Golisopod Rain | Sub-community 0: Rain (Mega Golisopod) | 90.4% |
| cluster-13 | Mega Salamence Trick Room | Sub-community 1: Trick Room (Mega Golisopod) | 62.5% |
| cluster-14 | Mega Charizard + Whimsicott/Garchomp Tailwind Offense | Sub-community 2: Sun (Mega Charizard-Y) | 100.0% |
| cluster-15 | Mega Camerupt + Indeedee-F/Hatterene Psyspam Trick Room | Sub-community 1: Trick Room (Mega Golisopod) | 66.7% |

## 10. Set Variants and Role Tags
### Rillaboom
- Teams: 1602, weighted support: 54.7%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: terrain-setter 99.9%, priority-attack 99.2%, fake-out 99.2%, pivot 28.6%, speed-drop 2.0%, disruption 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Miracle Seed | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | unknown | 709 | 48.2% |
| Miracle Seed | Fake Out, Grassy Glide, U-turn, Wood Hammer | unknown | 194 | 12.8% |
| Sitrus Berry | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | unknown | 64 | 4.3% |
| Eject Button | Fake Out, Grassy Glide, Protect, U-turn | unknown | 56 | 3.8% |
| Life Orb | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | unknown | 49 | 3.3% |
- Other signatures: 27.8%

### Sneasler
- Teams: 1246, weighted support: 41.5%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: seed-unburden 56.9%, fake-out 44.1%, speed-drop 15.4%, ally-boost 11.8%, quick-guard 5.8%, priority-blocker 5.8%, setup 3.8%, disruption 0.4%, pivot 0.2%, weather-setter 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| White Herb | Close Combat, Dire Claw, Fake Out, Protect | unknown | 93 | 8.3% |
| Psychic Seed | Close Combat, Dire Claw, Protect, Rock Slide | unknown | 93 | 7.8% |
| Grassy Seed | Close Combat, Dire Claw, Protect, Rock Slide | unknown | 89 | 7.1% |
| Grassy Seed | Close Combat, Dire Claw, Fake Out, Protect | unknown | 84 | 7.1% |
| Grassy Seed | Close Combat, Dire Claw, Protect, Rock Tomb | unknown | 73 | 5.8% |
- Other signatures: 63.9%

### Salamence
- Teams: 935, weighted support: 30.2%, mega share: 99.9%
- Community: Community 1: Sneasler / Salamence
- Roles: intimidate 99.9%, mega-attacker 98.9%, tailwind 72.9%, setup 0.6%, weather-setter 0.2%, helping-hand 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Salamencite | Draco Meteor, Hyper Voice, Protect, Tailwind | unknown | 369 | 42.0% |
| Salamencite | Draco Meteor, Flamethrower, Hyper Voice, Protect | unknown | 123 | 16.1% |
| Salamencite | Flamethrower, Hyper Voice, Protect, Tailwind | unknown | 82 | 9.3% |
| Salamencite | Double-Edge, Hyper Voice, Protect, Tailwind | unknown | 74 | 7.9% |
| Salamencite | Draco Meteor, Hyper Voice, Protect, Tailwind | fast | 54 | 3.4% |
- Other signatures: 21.4%

### Incineroar
- Teams: 865, weighted support: 29.1%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: intimidate 99.9%, fake-out 99.5%, pivot 93.0%, trick-room-abuser 23.9%, helping-hand 7.7%, spa-drop 6.7%, disruption 4.6%, status 0.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Fake Out, Flare Blitz, Parting Shot, Throat Chop | unknown | 236 | 28.7% |
| Sitrus Berry | Darkest Lariat, Fake Out, Flare Blitz, Parting Shot | unknown | 96 | 12.1% |
| Sitrus Berry | Fake Out, Flare Blitz, Helping Hand, Parting Shot | unknown | 38 | 4.6% |
| Rocky Helmet | Fake Out, Flare Blitz, Parting Shot, Throat Chop | unknown | 25 | 3.1% |
| Sitrus Berry | Fake Out, Flare Blitz, Parting Shot, Taunt | unknown | 21 | 3.0% |
- Other signatures: 48.5%

### Gholdengo
- Teams: 841, weighted support: 28.8%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: setup 94.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | unknown | 638 | 80.4% |
| Grassy Seed | Make It Rain, Nasty Plot, Protect, Shadow Ball | unknown | 51 | 6.3% |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | fast | 52 | 3.0% |
| Life Orb | Make It Rain, Power Gem, Protect, Shadow Ball | unknown | 15 | 1.7% |
| Choice Scarf | Make It Rain, Power Gem, Shadow Ball, Trick | unknown | 12 | 1.7% |
- Other signatures: 7.0%

### Raichu
- Teams: 704, weighted support: 25.7%, mega share: 99.9%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: mega-attacker 99.9%, speed-drop 98.4%, fake-out 80.4%, disruption 16.6%, pivot 2.2%, terrain-setter 1.7%, setup 0.3%, screens 0.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Raichunite Y | Fake Out, Focus Blast, Protect, Zap Cannon | unknown | 511 | 75.6% |
| Raichunite Y | Encore, Focus Blast, Protect, Zap Cannon | unknown | 108 | 15.1% |
| Raichunite Y | Fake Out, Focus Blast, Protect, Zap Cannon | fast | 28 | 1.9% |
| Raichunite Y | Focus Blast, Protect, Volt Switch, Zap Cannon | unknown | 5 | 0.8% |
| Raichunite Y | Electroweb, Focus Blast, Protect, Zap Cannon | unknown | 4 | 0.7% |
- Other signatures: 6.0%

### Kingambit
- Teams: 722, weighted support: 24.5%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: priority-attack 99.3%, setup 26.2%, trick-room-abuser 16.9%, speed-drop 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Chople Berry | Iron Head, Kowtow Cleave, Low Kick, Sucker Punch | unknown | 139 | 19.2% |
| Life Orb | Kowtow Cleave, Protect, Sucker Punch, Swords Dance | unknown | 102 | 16.7% |
| Focus Sash | Iron Head, Kowtow Cleave, Low Kick, Sucker Punch | unknown | 64 | 9.7% |
| Chople Berry | Iron Head, Kowtow Cleave, Protect, Sucker Punch | unknown | 58 | 8.8% |
| Life Orb | Iron Head, Kowtow Cleave, Protect, Sucker Punch | unknown | 41 | 6.2% |
- Other signatures: 39.4%

### Arcanine-Hisui
- Teams: 608, weighted support: 21.1%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: priority-attack 91.1%, intimidate 1.6%, spa-drop 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Extreme Speed, Flare Blitz, Head Smash, Protect | unknown | 481 | 83.1% |
| Focus Sash | Flare Blitz, Head Smash, Protect, Rock Slide | unknown | 43 | 7.0% |
| Focus Sash | Extreme Speed, Flare Blitz, Head Smash, Protect | fast | 39 | 3.1% |
| Focus Sash | Extreme Speed, Flare Blitz, Protect, Rock Slide | unknown | 18 | 2.9% |
| Focus Sash | Close Combat, Flare Blitz, Head Smash, Protect | unknown | 2 | 0.4% |
- Other signatures: 3.5%

### Indeedee-F
- Teams: 550, weighted support: 18.2%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: follow-me 99.4%, terrain-setter 99.4%, priority-blocker 99.4%, helping-hand 85.8%, trick-room-setter 79.9%, disruption 8.3%, spa-drop 2.5%, fake-out 1.5%, status 0.7%, screens 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Rocky Helmet | Follow Me, Helping Hand, Psychic, Trick Room | unknown | 95 | 19.1% |
| Colbur Berry | Follow Me, Helping Hand, Psychic, Trick Room | unknown | 81 | 15.7% |
| Psychic Seed | Follow Me, Helping Hand, Psychic, Trick Room | unknown | 47 | 9.3% |
| Rocky Helmet | Follow Me, Helping Hand, Protect, Psychic | unknown | 46 | 9.2% |
| Colbur Berry | Follow Me, Helping Hand, Protect, Psychic | unknown | 18 | 3.7% |
- Other signatures: 43.2%

### Milotic
- Teams: 471, weighted support: 16.3%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: speed-drop 46.0%, setup 40.3%, status 38.9%, helping-hand 2.7%, weather-setter 0.5%, screens 0.3%, pivot 0.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Ice Beam, Icy Wind, Protect, Scald | unknown | 60 | 13.7% |
| Sitrus Berry | Ice Beam, Icy Wind, Protect, Scald | unknown | 64 | 12.9% |
| Psychic Seed | Coil, Hypnosis, Muddy Water, Recover | unknown | 51 | 11.9% |
| Sitrus Berry | Coil, Hypnosis, Ice Beam, Muddy Water | unknown | 41 | 10.5% |
| Leftovers | Coil, Hypnosis, Muddy Water, Protect | unknown | 28 | 5.6% |
- Other signatures: 45.4%

### Garchomp
- Teams: 447, weighted support: 15.8%, mega share: 49.0%
- Community: Community 4: Rain
- Roles: mega-attacker 49.0%, speed-drop 8.7%, setup 2.2%, weather-setter 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Dragon Claw, Earthquake, Rock Slide, Stomping Tantrum | unknown | 44 | 10.9% |
| Garchompite Z | Dragon Pulse, Earth Power, Power Gem, Protect | unknown | 40 | 10.4% |
| Garchompite Z | Draco Meteor, Flamethrower, Power Gem, Protect | unknown | 45 | 10.4% |
| Life Orb | Dragon Claw, Earthquake, Protect, Stomping Tantrum | unknown | 32 | 7.3% |
| Garchompite Z | Draco Meteor, Earth Power, Power Gem, Protect | unknown | 27 | 7.3% |
- Other signatures: 53.8%

### Basculegion
- Teams: 483, weighted support: 15.7%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: priority-attack 93.7%, pivot 43.1%, speed-drop 1.6%, weather-setter 0.2%, setup 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Aqua Jet, Flip Turn, Last Respects, Wave Crash | unknown | 142 | 32.2% |
| Life Orb | Aqua Jet, Last Respects, Protect, Wave Crash | unknown | 131 | 28.9% |
| Mystic Water | Aqua Jet, Last Respects, Protect, Wave Crash | unknown | 57 | 13.5% |
| Choice Scarf | Aqua Jet, Flip Turn, Last Respects, Wave Crash | fast | 27 | 3.3% |
| Life Orb | Aqua Jet, Last Respects, Protect, Wave Crash | fast | 25 | 3.0% |
- Other signatures: 19.2%

### Farigiraf
- Teams: 407, weighted support: 14.4%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: priority-blocker 99.5%, trick-room-setter 98.1%, helping-hand 49.8%, trick-room-abuser 33.8%, disruption 6.1%, weather-setter 5.0%, setup 2.9%, ally-switch 1.8%, screens 0.9%, terrain-setter 0.9%, speed-drop 0.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Helping Hand, Protect, Psychic, Trick Room | unknown | 41 | 11.4% |
| Sitrus Berry | Protect, Psychic, Thunderbolt, Trick Room | unknown | 42 | 10.4% |
| Sitrus Berry | Helping Hand, Psychic, Thunderbolt, Trick Room | unknown | 24 | 5.5% |
| Sitrus Berry | Expanding Force, Protect, Thunderbolt, Trick Room | unknown | 19 | 4.9% |
| Sitrus Berry | Grass Knot, Protect, Psychic, Trick Room | unknown | 17 | 4.8% |
- Other signatures: 63.0%

### Archaludon
- Teams: 399, weighted support: 14.4%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: spa-drop 28.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Dragon Pulse, Electro Shot, Flash Cannon, Protect | unknown | 181 | 45.9% |
| Leftovers | Dragon Pulse, Electro Shot, Protect, Snarl | unknown | 83 | 23.1% |
| Leftovers | Aura Sphere, Dragon Pulse, Electro Shot, Protect | unknown | 32 | 8.3% |
| Leftovers | Draco Meteor, Electro Shot, Flash Cannon, Protect | unknown | 19 | 4.9% |
| Leftovers | Draco Meteor, Electro Shot, Protect, Snarl | unknown | 9 | 2.6% |
- Other signatures: 15.2%

### Golisopod
- Teams: 403, weighted support: 13.4%, mega share: 99.7%
- Community: Community 4: Rain
- Roles: mega-attacker 99.7%, setup 43.4%, priority-attack 36.3%, trick-room-abuser 32.2%, pivot 2.5%, wide-guard 1.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Golisopite | Drill Run, Iron Head, Leech Life, Protect | unknown | 75 | 23.2% |
| Golisopite | Iron Head, Leech Life, Protect, Swords Dance | unknown | 58 | 14.3% |
| Golisopite | Leech Life, Protect, Sucker Punch, Swords Dance | unknown | 34 | 8.9% |
| Golisopite | Iron Head, Leech Life, Sucker Punch, Swords Dance | unknown | 33 | 8.6% |
| Golisopite | Iron Head, Leech Life, Protect, Sucker Punch | unknown | 27 | 6.7% |
- Other signatures: 38.3%

### Staraptor
- Teams: 358, weighted support: 13.4%, mega share: 98.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: intimidate 99.7%, mega-attacker 98.0%, tailwind 78.7%, pivot 2.9%, weather-setter 0.3%, priority-attack 0.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Staraptite | Brave Bird, Close Combat, Protect, Tailwind | unknown | 257 | 74.2% |
| Staraptite | Brave Bird, Close Combat, Protect, Roost | unknown | 53 | 15.0% |
| Staraptite | Close Combat, Dual Wingbeat, Protect, Tailwind | unknown | 9 | 2.6% |
| Staraptite | Close Combat, Dual Wingbeat, Protect, Roost | unknown | 6 | 1.7% |
| Choice Scarf | Close Combat, Dual Wingbeat, Final Gambit, U-turn | unknown | 4 | 1.0% |
- Other signatures: 5.6%

### Charizard
- Teams: 355, weighted support: 12.4%, mega share: 100.0%
- Community: Community 4: Rain
- Roles: mega-attacker 100.0%, weather-setter 98.3%, setup 1.7%, helping-hand 1.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Charizardite Y | Heat Wave, Protect, Solar Beam, Weather Ball | unknown | 116 | 33.2% |
| Charizardite Y | Heat Wave, Hurricane, Protect, Weather Ball | unknown | 85 | 26.8% |
| Charizardite Y | Ancient Power, Heat Wave, Protect, Weather Ball | unknown | 86 | 24.1% |
| Charizardite Y | Heat Wave, Helping Hand, Protect, Weather Ball | unknown | 5 | 1.5% |
| Charizardite Y | Ancient Power, Heat Wave, Protect, Solar Beam | unknown | 4 | 1.4% |
- Other signatures: 13.1%

### Floette-Eternal
- Teams: 367, weighted support: 12.2%, mega share: 99.7%
- Community: Community 2: Setup (Mega Floette)
- Roles: mega-attacker 99.7%, setup 83.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Floettite | Calm Mind, Dazzling Gleam, Moonblast, Protect | unknown | 147 | 43.4% |
| Floettite | Calm Mind, Dazzling Gleam, Draining Kiss, Protect | unknown | 98 | 31.4% |
| Floettite | Dazzling Gleam, Light of Ruin, Moonblast, Protect | unknown | 45 | 12.4% |
| Floettite | Calm Mind, Dazzling Gleam, Moonblast, Protect | bulky | 16 | 2.1% |
| Floettite | Dazzling Gleam, Light of Ruin, Moonblast, Protect | fast | 13 | 1.8% |
- Other signatures: 8.9%

### Sylveon
- Teams: 340, weighted support: 11.8%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: priority-attack 83.6%, status 10.0%, setup 5.9%, trick-room-abuser 5.7%, spa-drop 3.5%, helping-hand 0.9%, weather-setter 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Quick Attack | unknown | 189 | 58.9% |
| Fairy Feather | Hyper Beam, Hyper Voice, Protect, Quick Attack | unknown | 42 | 13.4% |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Yawn | unknown | 11 | 4.0% |
| Fairy Feather | Calm Mind, Detect, Hyper Beam, Hyper Voice | unknown | 13 | 3.6% |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Mystical Fire | unknown | 7 | 2.3% |
- Other signatures: 17.9%

### Pelipper
- Teams: 318, weighted support: 10.8%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: weather-setter 100.0%, tailwind 85.9%, wide-guard 67.6%, trick-room-abuser 2.4%, helping-hand 1.4%, pivot 1.2%, speed-drop 0.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Hurricane, Tailwind, Weather Ball, Wide Guard | unknown | 80 | 26.2% |
| Focus Sash | Hurricane, Tailwind, Weather Ball, Wide Guard | unknown | 59 | 19.6% |
| Focus Sash | Hurricane, Protect, Tailwind, Weather Ball | unknown | 58 | 19.3% |
| Focus Sash | Hurricane, Protect, Weather Ball, Wide Guard | unknown | 13 | 4.6% |
| Sitrus Berry | Hurricane, Protect, Tailwind, Weather Ball | unknown | 8 | 2.2% |
- Other signatures: 28.1%

### Tyranitar
- Teams: 272, weighted support: 9.2%, mega share: 88.0%
- Community: Community 1: Sneasler / Salamence
- Roles: weather-setter 99.7%, mega-attacker 88.0%, setup 10.4%, trick-room-abuser 1.2%, speed-drop 0.6%, disruption 0.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Tyranitarite | Knock Off, Low Kick, Protect, Rock Slide | unknown | 157 | 63.0% |
| Tyranitarite | Dragon Dance, Knock Off, Protect, Rock Slide | unknown | 22 | 8.7% |
| Tyranitarite | Knock Off, Low Kick, Protect, Rock Slide | fast | 18 | 3.1% |
| Tyranitarite | Fire Punch, Knock Off, Protect, Rock Slide | unknown | 7 | 2.9% |
| Chople Berry | Knock Off, Low Kick, Protect, Rock Slide | unknown | 6 | 2.4% |
- Other signatures: 19.9%

### Excadrill
- Teams: 221, weighted support: 7.5%, mega share: 1.5%
- Community: Community 1: Sneasler / Salamence
- Roles: setup 3.8%, speed-drop 2.5%, mega-attacker 1.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | High Horsepower, Iron Head, Protect, Rock Slide | unknown | 151 | 75.0% |
| Focus Sash | Earthquake, High Horsepower, Iron Head, Protect | unknown | 8 | 3.8% |
| Focus Sash | High Horsepower, Iron Head, Protect, Rock Slide | fast | 20 | 3.7% |
| Life Orb | High Horsepower, Iron Head, Protect, Rock Slide | unknown | 6 | 2.7% |
| Focus Sash | Earthquake, Iron Head, Protect, Rock Slide | unknown | 5 | 2.1% |
- Other signatures: 12.6%

### Gardevoir
- Teams: 228, weighted support: 7.3%, mega share: 100.0%
- Community: Community 3: Psyspam
- Roles: mega-attacker 99.4%, trick-room-setter 35.0%, setup 14.9%, spa-drop 13.3%, disruption 3.4%, priority-attack 0.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Gardevoirite | Expanding Force, Hyper Voice, Protect, Trick Room | unknown | 65 | 28.6% |
| Gardevoirite | Expanding Force, Hyper Voice, Protect, Thunderbolt | unknown | 32 | 14.5% |
| Gardevoirite | Expanding Force, Hyper Voice, Mystical Fire, Protect | unknown | 28 | 13.3% |
| Gardevoirite | Calm Mind, Expanding Force, Hyper Voice, Protect | unknown | 24 | 12.6% |
| Gardevoirite | Expanding Force, Hyper Voice, Moonblast, Protect | unknown | 12 | 5.7% |
- Other signatures: 25.3%

### Volcarona
- Teams: 195, weighted support: 7.2%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: setup 54.1%, rage-powder 49.0%, spa-drop 43.9%, tailwind 27.4%, status 1.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Grassy Seed | Giga Drain, Heat Wave, Protect, Quiver Dance | unknown | 32 | 18.0% |
| Rocky Helmet | Overheat, Rage Powder, Struggle Bug, Tailwind | unknown | 12 | 7.2% |
| Grassy Seed | Flamethrower, Giga Drain, Protect, Quiver Dance | unknown | 12 | 6.3% |
| Sitrus Berry | Overheat, Rage Powder, Struggle Bug, Tailwind | unknown | 10 | 5.3% |
| Rocky Helmet | Overheat, Protect, Rage Powder, Struggle Bug | unknown | 9 | 4.9% |
- Other signatures: 58.4%

### Politoed
- Teams: 182, weighted support: 6.8%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: weather-setter 100.0%, perish-song 46.0%, disruption 33.5%, trick-room-abuser 8.3%, speed-drop 7.6%, helping-hand 6.8%, status 6.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Mystic Water | Ice Beam, Muddy Water, Protect, Weather Ball | unknown | 51 | 30.9% |
| Sitrus Berry | Encore, Perish Song, Protect, Weather Ball | unknown | 52 | 28.0% |
| Life Orb | Ice Beam, Muddy Water, Protect, Weather Ball | unknown | 10 | 5.6% |
| Sitrus Berry | Hypnosis, Perish Song, Protect, Weather Ball | unknown | 8 | 4.9% |
| Sitrus Berry | Ice Beam, Perish Song, Protect, Weather Ball | unknown | 4 | 2.3% |
- Other signatures: 28.3%

### Indeedee
- Teams: 188, weighted support: 6.7%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: terrain-setter 100.0%, priority-blocker 100.0%, spa-drop 65.7%, disruption 27.0%, trick-room-setter 26.9%, helping-hand 10.0%, fake-out 2.3%, setup 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Expanding Force, Mystical Fire, Protect, Trick | unknown | 59 | 35.5% |
| Focus Sash | Expanding Force, Imprison, Protect, Trick Room | unknown | 11 | 5.9% |
| Choice Scarf | Dazzling Gleam, Expanding Force, Mystical Fire, Trick | unknown | 11 | 5.4% |
| Choice Scarf | Expanding Force, Imprison, Trick, Trick Room | unknown | 6 | 3.8% |
| Focus Sash | Expanding Force, Hyper Voice, Imprison, Trick Room | unknown | 4 | 2.1% |
- Other signatures: 47.3%

### Armarouge
- Teams: 191, weighted support: 6.3%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: wide-guard 56.4%, trick-room-setter 49.1%, trick-room-abuser 24.2%, setup 2.3%, weather-setter 1.7%, ally-switch 0.7%, helping-hand 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Armor Cannon, Expanding Force, Protect, Wide Guard | unknown | 27 | 17.4% |
| Life Orb | Armor Cannon, Expanding Force, Protect, Trick Room | unknown | 24 | 12.0% |
| Life Orb | Armor Cannon, Expanding Force, Protect, Wide Guard | unknown | 17 | 9.6% |
| Twisted Spoon | Armor Cannon, Expanding Force, Trick Room, Wide Guard | unknown | 10 | 6.0% |
| Focus Sash | Armor Cannon, Expanding Force, Trick Room, Wide Guard | unknown | 9 | 5.2% |
- Other signatures: 49.8%

### Froslass
- Teams: 171, weighted support: 6.0%, mega share: 100.0%
- Community: Community 1: Sneasler / Salamence
- Roles: weather-setter 100.0%, mega-attacker 95.0%, screens 91.0%, speed-drop 2.6%, setup 1.9%, disruption 0.9%, status 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Froslassite | Aurora Veil, Blizzard, Protect, Shadow Ball | unknown | 132 | 81.1% |
| Froslassite | Aurora Veil, Blizzard, Protect, Rain Dance | unknown | 10 | 4.7% |
| Froslassite | Aurora Veil, Blizzard, Protect, Shadow Ball | fast | 10 | 3.3% |
| Froslassite | Blizzard, Protect, Shadow Ball, Weather Ball | unknown | 3 | 2.1% |
| Froslassite | Blizzard, Nasty Plot, Protect, Shadow Ball | unknown | 3 | 1.9% |
- Other signatures: 6.9%

### Gengar
- Teams: 156, weighted support: 5.7%, mega share: 99.3%
- Community: Community 4: Rain
- Roles: mega-attacker 91.9%, perish-song 68.4%, disruption 7.4%, speed-drop 7.2%, status 1.6%, setup 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Gengarite | Perish Song, Protect, Shadow Ball, Sludge Bomb | unknown | 87 | 57.4% |
| Gengarite | Focus Blast, Protect, Shadow Ball, Sludge Bomb | unknown | 14 | 10.3% |
| Gengarite | Protect, Shadow Ball, Sludge Bomb, Substitute | unknown | 12 | 7.8% |
| Gengarite | Icy Wind, Protect, Shadow Ball, Sludge Bomb | unknown | 9 | 6.2% |
| Gengarite | Disable, Perish Song, Protect, Shadow Ball | unknown | 6 | 4.2% |
- Other signatures: 14.1%

### Whimsicott
- Teams: 159, weighted support: 5.5%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: tailwind 100.0%, prankster 99.5%, disruption 64.0%, weather-setter 16.1%, helping-hand 5.7%, screens 5.1%, terrain-setter 2.3%, trick-room-setter 1.5%, speed-drop 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Encore, Moonblast, Protect, Tailwind | unknown | 53 | 34.1% |
| Focus Sash | Moonblast, Protect, Sunny Day, Tailwind | unknown | 13 | 9.0% |
| Focus Sash | Charm, Encore, Moonblast, Tailwind | unknown | 7 | 4.7% |
| Occa Berry | Encore, Moonblast, Protect, Tailwind | unknown | 7 | 4.0% |
| Fairy Feather | Encore, Moonblast, Protect, Tailwind | unknown | 6 | 3.9% |
- Other signatures: 44.3%

### Grimmsnarl
- Teams: 153, weighted support: 5.4%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: prankster 100.0%, screens 98.4%, pivot 96.5%, spa-drop 94.0%, trick-room-abuser 46.8%, fake-out 5.7%, priority-attack 1.6%, disruption 1.6%, speed-drop 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Light Clay | Light Screen, Parting Shot, Reflect, Spirit Break | unknown | 124 | 85.2% |
| Light Clay | Light Screen, Parting Shot, Reflect, Spirit Break | bulky | 6 | 1.9% |
| Light Clay | Foul Play, Light Screen, Parting Shot, Reflect | unknown | 3 | 1.6% |
| Light Clay | Light Screen, Parting Shot, Reflect, Throat Chop | unknown | 2 | 1.3% |
| Light Clay | Light Screen, Parting Shot, Reflect, Spirit Break | min-speed | 4 | 1.1% |
- Other signatures: 8.9%

### Metagross
- Teams: 159, weighted support: 5.4%, mega share: 97.3%
- Community: Community 3: Psyspam
- Roles: mega-attacker 97.3%, setup 13.0%, priority-attack 8.3%, trick-room-abuser 0.6%, speed-drop 0.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Metagrossite | Iron Head, Protect, Psychic Fangs, Stomping Tantrum | unknown | 24 | 15.8% |
| Metagrossite | Body Press, Protect, Psychic Fangs, Steel Roller | unknown | 24 | 15.8% |
| Metagrossite | Protect, Psychic Fangs, Steel Roller, Stomping Tantrum | unknown | 22 | 14.6% |
| Metagrossite | Body Press, Protect, Psych Up, Psychic Fangs | unknown | 15 | 9.9% |
| Metagrossite | Body Press, Iron Head, Protect, Psychic Fangs | unknown | 10 | 6.9% |
- Other signatures: 37.0%

### Sinistcha
- Teams: 133, weighted support: 4.6%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: rage-powder 97.3%, trick-room-setter 74.1%, trick-room-abuser 15.7%, disruption 1.3%, follow-me 0.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Colbur Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | unknown | 27 | 22.8% |
| Occa Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | unknown | 11 | 10.1% |
| Coba Berry | Life Dew, Matcha Gotcha, Protect, Rage Powder | unknown | 5 | 4.7% |
| Kasib Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | unknown | 6 | 4.7% |
| Sitrus Berry | Life Dew, Matcha Gotcha, Rage Powder, Trick Room | unknown | 6 | 4.6% |
- Other signatures: 53.1%

### Delphox
- Teams: 114, weighted support: 4.4%, mega share: 95.6%
- Community: Community 2: Setup (Mega Floette)
- Roles: mega-attacker 95.6%, setup 82.1%, disruption 4.3%, status 1.0%, terrain-setter 0.4%, priority-blocker 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Delphoxite | Heat Wave, Nasty Plot, Protect, Psychic | unknown | 72 | 66.8% |
| Delphoxite | Heat Wave, Nasty Plot, Protect, Psyshock | unknown | 6 | 5.5% |
| Delphoxite | Expanding Force, Heat Wave, Nasty Plot, Protect | unknown | 4 | 3.7% |
| Delphoxite | Heat Wave, Protect, Psychic, Substitute | unknown | 3 | 2.6% |
| Delphoxite | Calm Mind, Heat Wave, Protect, Psychic | unknown | 2 | 1.6% |
- Other signatures: 19.7%

### Swampert
- Teams: 125, weighted support: 4.3%, mega share: 92.1%
- Community: Community 4: Rain
- Roles: mega-attacker 92.1%, wide-guard 6.0%, pivot 5.9%, status 4.6%, trick-room-abuser 4.3%, weather-setter 1.0%, helping-hand 0.7%, setup 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Swampertite | Earthquake, Ice Punch, Protect, Wave Crash | unknown | 58 | 47.7% |
| Swampertite | High Horsepower, Ice Punch, Protect, Wave Crash | unknown | 30 | 25.5% |
| Swampertite | Earthquake, High Horsepower, Protect, Wave Crash | unknown | 3 | 2.9% |
| Swampertite | Earthquake, Ice Punch, Protect, Wave Crash | fast | 5 | 2.3% |
| Sitrus Berry | Flip Turn, High Horsepower, Protect, Yawn | unknown | 2 | 2.0% |
- Other signatures: 19.7%

### Glimmora
- Teams: 121, weighted support: 4.3%, mega share: 71.6%
- Community: Community 3: Psyspam
- Roles: mega-attacker 71.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Glimmoranite | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | unknown | 68 | 58.7% |
| Focus Sash | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | unknown | 21 | 19.2% |
| Glimmoranite | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | fast | 10 | 5.1% |
| Focus Sash | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | fast | 7 | 3.9% |
| Glimmoranite | Earth Power, Power Gem, Protect, Sludge Bomb | unknown | 3 | 2.7% |
- Other signatures: 10.4%

### Kommo-o
- Teams: 116, weighted support: 4.1%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: setup 67.7%, priority-attack 2.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | unknown | 51 | 46.5% |
| Life Orb | Aura Sphere, Clanging Scales, Flamethrower, Protect | unknown | 31 | 27.0% |
| Leftovers | Clanging Scales, Clangorous Soul, Flamethrower, Protect | unknown | 7 | 6.2% |
| Life Orb | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | unknown | 3 | 3.0% |
| Grassy Seed | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | unknown | 2 | 2.0% |
- Other signatures: 15.3%

### Venusaur
- Teams: 105, weighted support: 3.6%, mega share: 10.3%
- Community: Community 4: Rain
- Roles: status 76.5%, mega-attacker 9.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Leaf Storm, Protect, Sleep Powder, Sludge Bomb | unknown | 35 | 34.9% |
| Focus Sash | Earth Power, Protect, Sleep Powder, Sludge Bomb | unknown | 13 | 13.4% |
| Life Orb | Earth Power, Leaf Storm, Protect, Sludge Bomb | unknown | 11 | 11.4% |
| Focus Sash | Earth Power, Leaf Storm, Sleep Powder, Sludge Bomb | unknown | 5 | 4.7% |
| Venusaurite | Earth Power, Giga Drain, Protect, Sludge Bomb | unknown | 4 | 4.3% |
- Other signatures: 31.3%

### Dragapult
- Teams: 89, weighted support: 3.2%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: status 84.0%, screens 4.5%, disruption 4.2%, pivot 1.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Draco Meteor, Protect, Shadow Ball, Will-O-Wisp | unknown | 55 | 65.2% |
| Life Orb | Draco Meteor, Flamethrower, Protect, Shadow Ball | unknown | 7 | 7.0% |
| Life Orb | Dragon Darts, Phantom Force, Protect, Will-O-Wisp | unknown | 4 | 5.3% |
| Life Orb | Draco Meteor, Protect, Shadow Ball, Thunderbolt | unknown | 2 | 2.7% |
| Life Orb | Disable, Draco Meteor, Protect, Shadow Ball | unknown | 2 | 2.7% |
- Other signatures: 17.2%

### Ceruledge
- Teams: 81, weighted support: 3.1%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: setup 97.7%, priority-attack 96.1%, ally-switch 1.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Grassy Seed | Bitter Blade, Protect, Shadow Sneak, Swords Dance | unknown | 55 | 74.1% |
| Colbur Berry | Bitter Blade, Bulk Up, Protect, Shadow Sneak | unknown | 6 | 5.8% |
| Grassy Seed | Bitter Blade, Bulk Up, Protect, Shadow Sneak | unknown | 4 | 5.0% |
| Colbur Berry | Bitter Blade, Protect, Shadow Sneak, Swords Dance | unknown | 3 | 2.9% |
| Focus Sash | Bitter Blade, Poltergeist, Protect, Swords Dance | unknown | 1 | 1.4% |
- Other signatures: 10.8%

### Aerodactyl
- Teams: 91, weighted support: 3.1%, mega share: 75.4%
- Community: Community 4: Rain
- Roles: tailwind 99.0%, mega-attacker 75.4%, wide-guard 46.1%, disruption 2.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Aerodactylite | Dual Wingbeat, Rock Slide, Tailwind, Wide Guard | unknown | 23 | 28.3% |
| Aerodactylite | Dual Wingbeat, Protect, Rock Slide, Tailwind | unknown | 24 | 26.7% |
| Aerodactylite | Dual Wingbeat, Ice Fang, Rock Slide, Tailwind | unknown | 12 | 13.4% |
| Focus Sash | Dual Wingbeat, Rock Slide, Tailwind, Wide Guard | unknown | 5 | 6.4% |
| Focus Sash | Dual Wingbeat, Protect, Rock Slide, Tailwind | unknown | 5 | 5.1% |
- Other signatures: 20.0%

### Dragonite
- Teams: 85, weighted support: 3.1%, mega share: 87.5%
- Community: Community 2: Setup (Mega Floette)
- Roles: mega-attacker 87.5%, tailwind 64.8%, priority-attack 18.6%, setup 1.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Dragoninite | Dragon Pulse, Heat Wave, Protect, Tailwind | unknown | 37 | 46.8% |
| Dragoninite | Dragon Pulse, Extreme Speed, Heat Wave, Protect | unknown | 7 | 8.8% |
| Dragoninite | Dragon Pulse, Flamethrower, Protect, Tailwind | unknown | 5 | 6.4% |
| Dragoninite | Dragon Pulse, Hurricane, Protect, Weather Ball | unknown | 2 | 3.0% |
| Dragoninite | Dragon Pulse, Haze, Hurricane, Protect | unknown | 2 | 2.7% |
- Other signatures: 32.2%

### Torkoal
- Teams: 97, weighted support: 3.1%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: weather-setter 100.0%, trick-room-abuser 95.3%, helping-hand 29.5%, status 1.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Charcoal | Earth Power, Eruption, Protect, Weather Ball | unknown | 19 | 22.7% |
| Charcoal | Eruption, Helping Hand, Protect, Weather Ball | unknown | 12 | 13.3% |
| Charcoal | Eruption, Heat Wave, Protect, Weather Ball | unknown | 10 | 12.1% |
| Charcoal | Earth Power, Eruption, Heat Wave, Protect | unknown | 5 | 6.8% |
| Charcoal | Earth Power, Eruption, Helping Hand, Protect | unknown | 3 | 4.1% |
- Other signatures: 41.0%

### Corviknight
- Teams: 72, weighted support: 2.8%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: setup 90.7%, tailwind 23.5%, disruption 1.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Psychic Seed | Brave Bird, Bulk Up, Power Trip, Roost | unknown | 41 | 61.2% |
| Leftovers | Brave Bird, Bulk Up, Roost, Tailwind | unknown | 6 | 7.7% |
| Leftovers | Brave Bird, Iron Head, Protect, Tailwind | unknown | 3 | 4.5% |
| Occa Berry | Brave Bird, Roost, Tailwind, Taunt | unknown | 1 | 1.8% |
| Rocky Helmet | Brave Bird, Bulk Up, Iron Head, Roost | unknown | 1 | 1.5% |
- Other signatures: 23.2%

### Primarina
- Teams: 72, weighted support: 2.6%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: setup 49.7%, trick-room-abuser 11.6%, priority-attack 10.2%, speed-drop 5.9%, pivot 1.6%, perish-song 1.6%, disruption 1.1%, helping-hand 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Calm Mind, Hyper Voice, Moonblast, Protect | unknown | 15 | 21.4% |
| Grassy Seed | Calm Mind, Hyper Voice, Moonblast, Protect | unknown | 9 | 13.0% |
| Life Orb | Hyper Voice, Ice Beam, Moonblast, Protect | unknown | 4 | 5.9% |
| Life Orb | Calm Mind, Hyper Voice, Moonblast, Protect | unknown | 4 | 5.9% |
| Life Orb | Dazzling Gleam, Hyper Voice, Moonblast, Protect | unknown | 4 | 5.5% |
- Other signatures: 48.4%

### Lucario
- Teams: 84, weighted support: 2.5%, mega share: 98.8%
- Community: Community 2: Setup (Mega Floette)
- Roles: mega-attacker 98.8%, setup 69.5%, priority-attack 2.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Lucarionite Z | Aura Sphere, Calm Mind, Detect, Flash Cannon | unknown | 22 | 29.5% |
| Lucarionite Z | Aura Sphere, Dark Pulse, Detect, Flash Cannon | unknown | 6 | 8.8% |
| Lucarionite Z | Aura Sphere, Flash Cannon, Nasty Plot, Protect | unknown | 6 | 8.6% |
| Lucarionite Z | Aura Sphere, Detect, Flash Cannon, Nasty Plot | unknown | 5 | 7.4% |
| Lucarionite Z | Aura Sphere, Calm Mind, Flash Cannon, Protect | unknown | 4 | 5.7% |
- Other signatures: 39.9%

### Camerupt
- Teams: 67, weighted support: 2.4%, mega share: 100.0%
- Community: Community 3: Psyspam
- Roles: mega-attacker 100.0%, trick-room-abuser 97.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Cameruptite | Ancient Power, Earth Power, Heat Wave, Protect | unknown | 57 | 86.5% |
| Cameruptite | Ancient Power, Earth Power, Heat Wave, Protect | min-speed | 4 | 3.7% |
| Cameruptite | Earth Power, Eruption, Heat Wave, Protect | unknown | 2 | 3.4% |
| Cameruptite | Ancient Power, Earth Power, Flamethrower, Heat Wave | unknown | 1 | 1.7% |
| Cameruptite | Earth Power, Flamethrower, Heat Wave, Protect | unknown | 1 | 1.7% |
- Other signatures: 2.9%

### Hatterene
- Teams: 58, weighted support: 2.0%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: trick-room-setter 100.0%, trick-room-abuser 97.9%, spa-drop 6.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Dazzling Gleam, Expanding Force, Protect, Trick Room | unknown | 33 | 63.3% |
| Life Orb | Dazzling Gleam, Expanding Force, Protect, Trick Room | min-speed | 8 | 8.0% |
| Life Orb | Draining Kiss, Expanding Force, Protect, Trick Room | unknown | 3 | 5.2% |
| Life Orb | Dazzling Gleam, Expanding Force, Mystical Fire, Trick Room | unknown | 2 | 4.3% |
| Focus Sash | Dazzling Gleam, Expanding Force, Protect, Trick Room | unknown | 2 | 3.6% |
- Other signatures: 15.6%

### Baxcalibur
- Teams: 67, weighted support: 2.0%, mega share: 82.3%
- Community: Community 3: Psyspam
- Roles: priority-attack 86.9%, mega-attacker 82.3%, setup 52.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Baxcalibrite | Glaive Rush, Ice Shard, Protect, Swords Dance | unknown | 13 | 25.4% |
| Baxcalibrite | Glaive Rush, High Horsepower, Ice Shard, Protect | unknown | 8 | 14.0% |
| Baxcalibrite | Dragon Dance, Glaive Rush, Icicle Crash, Protect | unknown | 3 | 4.6% |
| Baxcalibrite | Glaive Rush, Ice Shard, Icicle Spear, Protect | unknown | 2 | 4.3% |
| Life Orb | Glaive Rush, Ice Shard, Icicle Crash, Protect | offensive | 4 | 3.8% |
- Other signatures: 48.0%

### Annihilape
- Teams: 52, weighted support: 2.0%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: pivot 32.5%, speed-drop 15.3%, setup 13.6%, ally-boost 4.9%, disruption 1.5%, weather-setter 1.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Close Combat, Ice Punch, Shadow Claw, U-turn | unknown | 8 | 17.2% |
| Choice Scarf | Close Combat, Ice Punch, Phantom Force, Shadow Claw | unknown | 5 | 10.1% |
| Choice Scarf | Close Combat, Ice Punch, Rock Tomb, Shadow Claw | unknown | 5 | 8.8% |
| Choice Scarf | Close Combat, Ice Punch, Rock Slide, Shadow Claw | unknown | 4 | 8.0% |
| Leftovers | Bulk Up, Drain Punch, Protect, Rage Fist | unknown | 4 | 8.0% |
- Other signatures: 48.0%

### Ninetales-Alola
- Teams: 57, weighted support: 1.9%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: weather-setter 100.0%, screens 61.2%, speed-drop 33.4%, disruption 31.9%, ally-boost 5.4%, helping-hand 2.5%, terrain-setter 2.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Blizzard, Encore, Freeze-Dry, Icy Wind | unknown | 4 | 8.2% |
| Choice Scarf | Blizzard, Freeze-Dry, Icy Wind, Moonblast | unknown | 4 | 8.2% |
| Never-Melt Ice | Aurora Veil, Blizzard, Freeze-Dry, Protect | unknown | 3 | 6.0% |
| Light Clay | Aurora Veil, Blizzard, Moonblast, Protect | unknown | 3 | 5.3% |
| Focus Sash | Aurora Veil, Blizzard, Moonblast, Protect | unknown | 3 | 4.7% |
- Other signatures: 67.6%

### Blastoise
- Teams: 49, weighted support: 1.7%, mega share: 96.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: mega-attacker 96.0%, setup 81.8%, fake-out 12.1%, pivot 4.0%, status 1.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Blastoisinite | Dark Pulse, Protect, Shell Smash, Water Spout | unknown | 14 | 33.4% |
| Blastoisinite | Protect, Shell Smash, Terrain Pulse, Water Spout | unknown | 12 | 24.4% |
| Blastoisinite | Protect, Shell Smash, Terrain Pulse, Water Spout | offensive | 4 | 5.0% |
| Blastoisinite | Ice Beam, Protect, Shell Smash, Water Spout | unknown | 2 | 4.9% |
| Blastoisinite | Aura Sphere, Dark Pulse, Protect, Water Spout | unknown | 2 | 4.9% |
- Other signatures: 27.3%

### Pawmot
- Teams: 57, weighted support: 1.7%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: fake-out 63.9%, ally-boost 20.3%, priority-attack 4.2%, weather-setter 2.5%, disruption 1.8%, pivot 1.2%, speed-drop 1.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Close Combat, Double Shock, Fake Out, Revival Blessing | unknown | 14 | 28.9% |
| Focus Sash | Close Combat, Coaching, Double Shock, Revival Blessing | unknown | 3 | 6.0% |
| Focus Sash | Close Combat, Double Shock, Protect, Revival Blessing | unknown | 3 | 6.0% |
| Focus Sash | Close Combat, Double Shock, Fake Out, Protect | unknown | 3 | 5.8% |
| Focus Sash | Close Combat, Double Shock, Fake Out, Revival Blessing | fast | 5 | 5.8% |
- Other signatures: 47.5%

### Blaziken
- Teams: 49, weighted support: 1.7%, mega share: 67.4%
- Community: Community 3: Psyspam
- Roles: mega-attacker 67.4%, ally-boost 18.7%, setup 2.5%, priority-attack 1.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Blazikenite | Close Combat, Detect, Flare Blitz, Rock Slide | unknown | 14 | 29.5% |
| Blazikenite | Close Combat, Flare Blitz, Protect, Rock Slide | unknown | 8 | 19.1% |
| Focus Sash | Aura Sphere, Coaching, Detect, Heat Wave | unknown | 5 | 10.4% |
| Blazikenite | Close Combat, Flare Blitz, Protect, Thunder Punch | unknown | 2 | 5.6% |
| Blazikenite | Close Combat, Detect, Flare Blitz, Rock Slide | fast | 3 | 4.7% |
- Other signatures: 30.5%

### Maushold
- Teams: 44, weighted support: 1.6%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: follow-me 98.2%, disruption 39.2%, helping-hand 10.7%, speed-drop 2.6%, pivot 1.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Chople Berry | Feint, Follow Me, Protect, Super Fang | unknown | 11 | 29.2% |
| Chople Berry | Encore, Follow Me, Protect, Super Fang | unknown | 2 | 5.2% |
| Chople Berry | Encore, Feint, Follow Me, Taunt | unknown | 2 | 5.2% |
| Focus Sash | Follow Me, Protect, Super Fang, Taunt | unknown | 2 | 4.4% |
| Chople Berry | Follow Me, Helping Hand, Protect, Super Fang | unknown | 2 | 4.4% |
- Other signatures: 51.5%

### Absol
- Teams: 47, weighted support: 1.5%, mega share: 100.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: mega-attacker 100.0%, speed-drop 10.2%, status 7.9%, priority-attack 7.2%, perish-song 2.8%, setup 2.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Absolite Z | Close Combat, Detect, Night Slash, Shadow Claw | unknown | 10 | 23.8% |
| Absolite Z | Close Combat, Icy Wind, Night Slash, Protect | unknown | 3 | 7.6% |
| Absolite Z | Night Slash, Protect, Psycho Cut, Shadow Claw | unknown | 2 | 5.6% |
| Absolite Z | Close Combat, Night Slash, Protect, Shadow Claw | unknown | 2 | 4.8% |
| Absolite Z | Close Combat, Detect, Night Slash, Shadow Claw | fast | 3 | 4.2% |
- Other signatures: 54.1%

### Vivillon
- Teams: 36, weighted support: 1.5%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: status 100.0%, rage-powder 95.1%, tailwind 7.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Hurricane, Protect, Rage Powder, Sleep Powder | unknown | 32 | 90.2% |
| Focus Sash | Hurricane, Protect, Sleep Powder, Tailwind | unknown | 2 | 4.9% |
| Focus Sash | Pollen Puff, Rage Powder, Sleep Powder, Tailwind | unknown | 1 | 2.9% |
| Choice Scarf | Hurricane, Protect, Rage Powder, Sleep Powder | unknown | 1 | 2.0% |
- Other signatures: 0.0%

### Hydreigon
- Teams: 39, weighted support: 1.3%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: spa-drop 67.8%, tailwind 7.8%, pivot 4.7%, disruption 3.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Dark Pulse, Draco Meteor, Earth Power, Snarl | unknown | 10 | 28.6% |
| Choice Scarf | Dark Pulse, Draco Meteor, Flamethrower, Snarl | unknown | 3 | 8.8% |
| Choice Scarf | Dark Pulse, Draco Meteor, Earth Power, Heat Wave | unknown | 2 | 6.5% |
| Focus Sash | Dark Pulse, Draco Meteor, Earth Power, Flamethrower | unknown | 2 | 5.5% |
| Choice Scarf | Dark Pulse, Draco Meteor, Earth Power, Flamethrower | unknown | 2 | 5.5% |
- Other signatures: 45.1%

### Sableye
- Teams: 30, weighted support: 1.1%, mega share: 9.0%
- Community: Community 4: Rain
- Roles: prankster 91.0%, screens 72.3%, weather-setter 65.7%, disruption 41.4%, status 36.2%, trick-room-abuser 32.8%, fake-out 28.2%, spa-drop 7.5%, setup 5.3%, helping-hand 3.7%, speed-drop 3.7%, mega-attacker 2.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Light Clay | Foul Play, Light Screen, Rain Dance, Reflect | unknown | 4 | 13.9% |
| Light Clay | Light Screen, Rain Dance, Taunt, Will-O-Wisp | unknown | 2 | 7.5% |
| Roseli Berry | Fake Out, Light Screen, Rain Dance, Will-O-Wisp | unknown | 1 | 4.5% |
| Roseli Berry | Disable, Encore, Fake Out, Sunny Day | unknown | 1 | 3.7% |
| Focus Sash | Detect, Disable, Encore, Fake Out | unknown | 1 | 3.7% |
- Other signatures: 66.7%

### Talonflame
- Teams: 35, weighted support: 1.1%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: tailwind 100.0%, gale-wings 100.0%, status 16.9%, quick-guard 8.5%, priority-blocker 8.5%, disruption 6.5%, weather-setter 2.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Brave Bird, Flare Blitz, Protect, Tailwind | unknown | 5 | 14.7% |
| Expert Belt | Brave Bird, Flare Blitz, Protect, Tailwind | unknown | 2 | 7.7% |
| Sharp Beak | Brave Bird, Flare Blitz, Protect, Tailwind | unknown | 2 | 5.2% |
| Rocky Helmet | Brave Bird, Flare Blitz, Tailwind, Upper Hand | unknown | 1 | 3.8% |
| Focus Sash | Feather Dance, Flare Blitz, Protect, Tailwind | unknown | 1 | 3.8% |
- Other signatures: 64.8%

### Mawile
- Teams: 27, weighted support: 0.9%, mega share: 100.0%
- Community: Community 4: Rain
- Roles: mega-attacker 100.0%, priority-attack 94.1%, trick-room-abuser 77.5%, intimidate 31.0%, setup 20.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Mawilite | Iron Head, Play Rough, Protect, Sucker Punch | unknown | 12 | 50.4% |
| Mawilite | Play Rough, Protect, Rock Slide, Sucker Punch | unknown | 3 | 12.4% |
| Mawilite | Play Rough, Protect, Sucker Punch, Swords Dance | unknown | 3 | 12.4% |
| Mawilite | Iron Head, Play Rough, Protect, Sucker Punch | min-speed | 4 | 7.6% |
| Mawilite | Play Rough, Rock Slide, Sucker Punch, Swords Dance | unknown | 1 | 4.6% |
- Other signatures: 12.5%

### Kleavor
- Teams: 24, weighted support: 0.9%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: tailwind 22.1%, pivot 20.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Feint, Protect, Stone Axe, X-Scissor | unknown | 4 | 16.7% |
| Focus Sash | Protect, Stone Axe, Tailwind, X-Scissor | unknown | 4 | 13.8% |
| Focus Sash | Close Combat, Protect, Stone Axe, X-Scissor | unknown | 2 | 9.8% |
| Choice Scarf | Stone Axe, Tailwind, U-turn, X-Scissor | unknown | 2 | 8.3% |
| Focus Sash | Night Slash, Protect, Stone Axe, X-Scissor | unknown | 2 | 8.3% |
- Other signatures: 43.1%

### Scovillain
- Teams: 23, weighted support: 0.8%, mega share: 96.4%
- Community: Community 1: Sneasler / Salamence
- Roles: rage-powder 100.0%, mega-attacker 83.1%, trick-room-abuser 12.3%, helping-hand 5.1%, priority-attack 3.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Scovillainite | Giga Drain, Overheat, Protect, Rage Powder | unknown | 11 | 47.6% |
| Scovillainite | Flamethrower, Giga Drain, Protect, Rage Powder | unknown | 3 | 13.7% |
| Scovillainite | Leech Seed, Overheat, Protect, Rage Powder | unknown | 2 | 9.7% |
| Scovillainite | Helping Hand, Leaf Storm, Rage Powder, Super Fang | unknown | 1 | 5.1% |
| Scovillainite | Flamethrower, Leaf Storm, Protect, Rage Powder | unknown | 1 | 5.1% |
- Other signatures: 18.8%

### Pyroar
- Teams: 22, weighted support: 0.8%, mega share: 100.0%
- Community: Community 3: Psyspam
- Roles: mega-attacker 100.0%, spa-drop 21.1%, status 5.3%, disruption 5.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Pyroarite | Heat Wave, Overheat, Protect, Scorching Sands | unknown | 10 | 49.6% |
| Pyroarite | Heat Wave, Overheat, Protect, Snarl | unknown | 3 | 15.8% |
| Pyroarite | Flamethrower, Heat Wave, Overheat, Protect | unknown | 3 | 11.2% |
| Pyroarite | Heat Wave, Protect, Snarl, Yawn | fast | 2 | 5.3% |
| Pyroarite | Flamethrower, Heat Wave, Protect, Scorching Sands | unknown | 1 | 5.3% |
- Other signatures: 12.9%

### Typhlosion-Hisui
- Teams: 20, weighted support: 0.7%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: none
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Eruption, Heat Wave, Overheat, Shadow Ball | unknown | 7 | 39.0% |
| Choice Scarf | Burn Up, Eruption, Heat Wave, Shadow Ball | unknown | 2 | 11.6% |
| Life Orb | Eruption, Heat Wave, Protect, Shadow Ball | unknown | 2 | 9.9% |
| Choice Scarf | Eruption, Flamethrower, Heat Wave, Shadow Ball | unknown | 2 | 8.2% |
| Spell Tag | Eruption, Heat Wave, Protect, Shadow Ball | unknown | 1 | 5.8% |
- Other signatures: 25.4%

### Rotom-Heat
- Teams: 19, weighted support: 0.7%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: pivot 53.9%, speed-drop 47.9%, status 35.7%, screens 6.1%, setup 4.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Passho Berry | Overheat, Protect, Thunderbolt, Will-O-Wisp | unknown | 2 | 12.2% |
| Sitrus Berry | Overheat, Protect, Thunderbolt, Volt Switch | unknown | 2 | 8.6% |
| Sitrus Berry | Overheat, Protect, Thunder, Volt Switch | unknown | 2 | 8.6% |
| Sitrus Berry | Overheat, Thunder Wave, Volt Switch, Will-O-Wisp | unknown | 1 | 6.1% |
| Choice Scarf | Discharge, Electroweb, Overheat, Volt Switch | unknown | 1 | 6.1% |
- Other signatures: 58.3%

### Espathra
- Teams: 17, weighted support: 0.7%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: setup 87.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Grassy Seed | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 6 | 35.0% |
| Sitrus Berry | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 3 | 16.6% |
| Focus Sash | Baton Pass, Calm Mind, Expanding Force, Protect | unknown | 2 | 12.3% |
| Focus Sash | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 1 | 7.4% |
| Colbur Berry | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 1 | 6.1% |
- Other signatures: 22.7%

### Altaria
- Teams: 18, weighted support: 0.6%, mega share: 21.8%
- Community: Community 3: Psyspam
- Roles: status 73.4%, tailwind 67.0%, perish-song 26.1%, mega-attacker 21.8%, setup 4.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Haban Berry | Ice Beam, Protect, Tailwind, Will-O-Wisp | unknown | 10 | 58.2% |
| Altarianite | Draco Meteor, Hyper Voice, Perish Song, Protect | unknown | 3 | 21.8% |
| Haban Berry | Ice Beam, Protect, Safeguard, Will-O-Wisp | unknown | 1 | 6.8% |
| Leftovers | Cotton Guard, Draco Meteor, Hurricane, Tailwind | unknown | 1 | 4.8% |
| Leftovers | Fire Spin, Perish Song, Protect, Will-O-Wisp | bulky | 1 | 4.4% |
- Other signatures: 4.0%

### Lycanroc-Dusk
- Teams: 17, weighted support: 0.6%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: priority-attack 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Accelerock, Close Combat, Protect, Rock Slide | unknown | 13 | 84.7% |
| Focus Sash | Accelerock, Close Combat, Protect, Rock Slide | fast | 3 | 10.3% |
| Wide Lens | Accelerock, Play Rough, Psychic Fangs, Rock Slide | unknown | 1 | 5.0% |
- Other signatures: 0.0%

### Toxapex
- Teams: 17, weighted support: 0.6%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: status 100.0%, wide-guard 95.0%, trick-room-abuser 38.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Baneful Bunker, Infestation, Toxic, Wide Guard | unknown | 13 | 81.7% |
| Leftovers | Baneful Bunker, Infestation, Recover, Toxic | unknown | 1 | 5.0% |
| Sitrus Berry | Baneful Bunker, Infestation, Toxic, Wide Guard | unknown | 1 | 5.0% |
| Sitrus Berry | Baneful Bunker, Infestation, Toxic, Wide Guard | bulky | 1 | 4.6% |
| Leftovers | Baneful Bunker, Infestation, Toxic, Wide Guard | bulky | 1 | 3.7% |
- Other signatures: 0.0%

### Sirfetch’d
- Teams: 20, weighted support: 0.5%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: trick-room-abuser 31.6%, priority-attack 5.5%, quick-guard 5.0%, priority-blocker 5.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leek | Close Combat, Detect, Feint, Meteor Assault | unknown | 4 | 21.5% |
| Focus Sash | Close Combat, Detect, Feint, Meteor Assault | unknown | 2 | 15.5% |
| Black Belt | Close Combat, Detect, Feint, Meteor Assault | unknown | 1 | 7.8% |
| Expert Belt | Close Combat, Detect, Leaf Blade, Meteor Assault | min-speed | 1 | 6.3% |
| Leek | Close Combat, Feint, Meteor Assault, Protect | unknown | 1 | 5.5% |
- Other signatures: 43.4%

### Empoleon
- Teams: 17, weighted support: 0.5%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: priority-attack 18.9%, trick-room-abuser 16.9%, status 7.8%, speed-drop 5.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Flash Cannon, Hydro Pump, Ice Beam, Protect | unknown | 3 | 16.2% |
| Sitrus Berry | Flash Cannon, Hydro Pump, Ice Beam, Protect | unknown | 2 | 13.4% |
| Sitrus Berry | Flash Cannon, Ice Beam, Protect, Surf | unknown | 1 | 7.8% |
| Leftovers | Flash Cannon, Hydro Pump, Protect, Vacuum Wave | unknown | 1 | 7.8% |
| Rocky Helmet | Flash Cannon, Hydro Pump, Ice Beam, Protect | unknown | 1 | 7.8% |
- Other signatures: 46.9%

### Meganium
- Teams: 14, weighted support: 0.5%, mega share: 100.0%
- Community: Community 4: Rain
- Roles: mega-attacker 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Meganiumite | Dazzling Gleam, Protect, Solar Beam, Weather Ball | unknown | 9 | 65.3% |
| Meganiumite | Earth Power, Protect, Solar Beam, Weather Ball | fast | 2 | 10.6% |
| Meganiumite | Dazzling Gleam, Earth Power, Solar Beam, Weather Ball | unknown | 1 | 8.0% |
| Meganiumite | Ancient Power, Protect, Solar Beam, Weather Ball | unknown | 1 | 8.0% |
| Meganiumite | Dazzling Gleam, Giga Drain, Protect, Weather Ball | unknown | 1 | 8.0% |
- Other signatures: 0.0%

### Gallade
- Teams: 16, weighted support: 0.5%, mega share: 14.2%
- Community: Community 3: Psyspam
- Roles: trick-room-setter 61.4%, wide-guard 60.9%, mega-attacker 14.2%, setup 8.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| White Herb | Psycho Cut, Sacred Sword, Trick Room, Wide Guard | unknown | 2 | 11.7% |
| White Herb | Leaf Blade, Protect, Psycho Cut, Sacred Sword | unknown | 1 | 8.3% |
| White Herb | Leaf Blade, Psycho Cut, Sacred Sword, Trick Room | unknown | 1 | 8.3% |
| Sitrus Berry | Psycho Cut, Sacred Sword, Trick Room, Wide Guard | unknown | 1 | 8.3% |
| Galladite | Psycho Cut, Sacred Sword, Trick Room, Wide Guard | unknown | 1 | 8.3% |
- Other signatures: 55.0%

### Rotom-Wash
- Teams: 13, weighted support: 0.5%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: status 84.8%, speed-drop 39.3%, pivot 30.4%, screens 15.2%, weather-setter 8.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Hydro Pump, Light Screen, Thunderbolt, Will-O-Wisp | unknown | 2 | 15.2% |
| Sitrus Berry | Electroweb, Hydro Pump, Protect, Volt Switch | unknown | 1 | 8.9% |
| Leftovers | Electroweb, Protect, Volt Switch, Will-O-Wisp | unknown | 1 | 8.9% |
| Leftovers | Hydro Pump, Protect, Thunderbolt, Will-O-Wisp | unknown | 1 | 8.9% |
| Sitrus Berry | Hydro Pump, Protect, Thunderbolt, Will-O-Wisp | unknown | 1 | 8.9% |
- Other signatures: 49.2%

### Tsareena
- Teams: 14, weighted support: 0.5%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: priority-blocker 100.0%, disruption 28.1%, helping-hand 9.0%, pivot 6.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Wide Lens | High Jump Kick, Power Whip, Protect, Triple Axel | unknown | 3 | 24.4% |
| Wide Lens | Power Whip, Protect, Taunt, Triple Axel | unknown | 2 | 15.4% |
| Wide Lens | Helping Hand, Low Kick, Power Whip, Triple Axel | fast | 2 | 9.0% |
| Wide Lens | Power Whip, Protect, Triple Axel, Zen Headbutt | unknown | 1 | 9.0% |
| Occa Berry | Low Kick, Power Whip, Protect, Triple Axel | unknown | 1 | 9.0% |
- Other signatures: 33.3%

### Vanilluxe
- Teams: 13, weighted support: 0.4%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: weather-setter 100.0%, speed-drop 69.8%, priority-attack 19.9%, screens 16.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Blizzard, Freeze-Dry, Icy Wind, Sheer Cold | unknown | 6 | 49.6% |
| Never-Melt Ice | Blizzard, Freeze-Dry, Ice Shard, Protect | unknown | 2 | 14.5% |
| Choice Scarf | Aurora Veil, Blizzard, Freeze-Dry, Sheer Cold | unknown | 1 | 10.3% |
| Choice Scarf | Blizzard, Freeze-Dry, Ice Beam, Icy Wind | unknown | 1 | 7.3% |
| Choice Scarf | Blizzard, Freeze-Dry, Icy Wind, Sheer Cold | fast | 1 | 6.6% |
- Other signatures: 11.7%

### Aegislash
- Teams: 12, weighted support: 0.4%, mega share: 0.0%
- Community: Community 4: Rain
- Roles: wide-guard 59.6%, priority-attack 57.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Close Combat, Poltergeist, Shadow Sneak, Wide Guard | unknown | 3 | 26.3% |
| Spell Tag | King’s Shield, Poltergeist, Sacred Sword, Shadow Sneak | unknown | 2 | 18.6% |
| Leftovers | Flash Cannon, King’s Shield, Shadow Ball, Substitute | unknown | 1 | 10.9% |
| Focus Sash | Iron Head, King’s Shield, Sacred Sword, Shadow Claw | unknown | 1 | 10.9% |
| Life Orb | Flash Cannon, King’s Shield, Shadow Ball, Wide Guard | unknown | 1 | 7.7% |
- Other signatures: 25.7%

### Weavile
- Teams: 10, weighted support: 0.4%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: fake-out 80.8%, priority-attack 22.5%, speed-drop 5.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Fake Out, Ice Spinner, Knock Off, Protect | unknown | 3 | 33.7% |
| Focus Sash | Fake Out, Ice Shard, Icicle Spear, Knock Off | unknown | 1 | 11.2% |
| Focus Sash | Fake Out, Knock Off, Protect, Triple Axel | unknown | 1 | 11.2% |
| Focus Sash | Fake Out, Knock Off, Reversal, Triple Axel | unknown | 1 | 11.2% |
| Life Orb | Ice Shard, Ice Spinner, Knock Off, Protect | unknown | 1 | 11.2% |
- Other signatures: 21.3%

### Meowscarada
- Teams: 9, weighted support: 0.3%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: pivot 42.6%, priority-attack 12.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Miracle Seed | Flower Trick, Knock Off, Protect, U-turn | unknown | 1 | 12.2% |
| Choice Scarf | Flower Trick, Knock Off, Thunder Punch, Triple Axel | unknown | 1 | 12.2% |
| Life Orb | Flower Trick, Knock Off, Protect, Triple Axel | unknown | 1 | 12.2% |
| Focus Sash | Flower Trick, Knock Off, Protect, Triple Axel | unknown | 1 | 12.2% |
| Life Orb | Brick Break, Flower Trick, Sucker Punch, Triple Axel | unknown | 1 | 12.2% |
- Other signatures: 39.0%

### Toxtricity
- Teams: 11, weighted support: 0.3%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: spa-drop 55.9%, pivot 29.8%, trick-room-abuser 12.3%, speed-drop 8.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Overdrive, Protect, Sludge Bomb, Snarl | unknown | 2 | 17.4% |
| Life Orb | Boomburst, Overdrive, Protect, Sludge Bomb | offensive | 2 | 14.3% |
| Choice Scarf | Overdrive, Psychic Noise, Sludge Bomb, Volt Switch | unknown | 1 | 12.3% |
| Life Orb | Boomburst, Overdrive, Protect, Psychic Noise | unknown | 1 | 12.3% |
| Focus Sash | Overdrive, Protect, Sludge Bomb, Snarl | unknown | 1 | 12.3% |
- Other signatures: 31.5%

### Grapploct
- Teams: 10, weighted support: 0.3%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: trick-room-abuser 57.9%, priority-attack 52.8%, ally-boost 8.7%, disruption 8.7%, setup 7.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Detect, Mach Punch, Stomping Tantrum, Storm Throw | unknown | 2 | 24.6% |
| Psychic Seed | Payback, Protect, Storm Throw, Topsy-Turvy | unknown | 2 | 17.4% |
| Life Orb | Ice Punch, Payback, Protect, Storm Throw | unknown | 1 | 12.3% |
| Life Orb | Mach Punch, Protect, Storm Throw, Sucker Punch | unknown | 1 | 12.3% |
| Focus Sash | Detect, Feint, Ice Punch, Storm Throw | unknown | 1 | 8.7% |
- Other signatures: 24.6%

### Lopunny
- Teams: 9, weighted support: 0.3%, mega share: 100.0%
- Community: Community 3: Psyspam
- Roles: mega-attacker 100.0%, fake-out 74.6%, disruption 50.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Lopunnite | Close Combat, Fake Out, Thunder Punch, Triple Axel | unknown | 2 | 18.1% |
| Lopunnite | Close Combat, Fake Out, Protect, Triple Axel | unknown | 1 | 12.7% |
| Lopunnite | Close Combat, Encore, Protect, Triple Axel | unknown | 1 | 12.7% |
| Lopunnite | Close Combat, Encore, Protect, Thunder Punch | unknown | 1 | 12.7% |
| Lopunnite | Close Combat, Encore, Fake Out, Triple Axel | unknown | 1 | 12.7% |
- Other signatures: 31.1%

### Mamoswine
- Teams: 9, weighted support: 0.3%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: priority-attack 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | High Horsepower, Ice Shard, Icicle Crash, Protect | unknown | 6 | 72.9% |
| Focus Sash | Earthquake, Ice Shard, Icicle Crash, Protect | unknown | 2 | 18.1% |
| Focus Sash | High Horsepower, Ice Shard, Protect, Rock Slide | unknown | 1 | 9.0% |
- Other signatures: 0.0%

### Klefki
- Teams: 10, weighted support: 0.3%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: prankster 100.0%, weather-setter 83.6%, screens 83.6%, speed-drop 27.7%, setup 16.4%, trick-room-setter 5.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Light Clay | Dazzling Gleam, Light Screen, Rain Dance, Reflect | unknown | 4 | 50.2% |
| Light Clay | Light Screen, Rain Dance, Reflect, Thunder Wave | unknown | 3 | 27.7% |
| Sitrus Berry | Calm Mind, Draining Kiss, Psych Up, Substitute | unknown | 1 | 9.2% |
| Sitrus Berry | Calm Mind, Draining Kiss, Psych Up, Substitute | bulky | 1 | 7.2% |
| Focus Sash | Dazzling Gleam, Light Screen, Rain Dance, Trick Room | offensive | 1 | 5.6% |
- Other signatures: 0.0%

### Chandelure
- Teams: 9, weighted support: 0.3%, mega share: 41.3%
- Community: Community 1: Sneasler / Salamence
- Roles: trick-room-setter 45.4%, mega-attacker 41.3%, spa-drop 9.4%, disruption 5.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Kasib Berry | Heat Wave, Protect, Psychic, Trick Room | unknown | 1 | 13.2% |
| Charcoal | Energy Ball, Heat Wave, Protect, Shadow Ball | unknown | 1 | 13.2% |
| Charcoal | Heat Wave, Protect, Shadow Ball, Trick Room | unknown | 1 | 13.2% |
| Chandelurite | Heat Wave, Protect, Shadow Ball, Trick Room | unknown | 1 | 13.2% |
| Focus Sash | Flamethrower, Overheat, Protect, Shadow Ball | unknown | 1 | 13.2% |
- Other signatures: 33.8%

### Zoroark-Hisui
- Teams: 10, weighted support: 0.3%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: speed-drop 70.7%, disruption 28.4%, pivot 13.3%, spa-drop 9.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Fake Tears, Icy Wind, Protect, Shadow Ball | unknown | 1 | 13.3% |
| Focus Sash | Bitter Malice, Flamethrower, Focus Blast, Taunt | unknown | 1 | 13.3% |
| Focus Sash | Flamethrower, Icy Wind, Shadow Ball, U-turn | unknown | 1 | 13.3% |
| Life Orb | Bitter Malice, Hyper Beam, Hyper Voice, Icy Wind | unknown | 1 | 13.3% |
| Choice Scarf | Bitter Malice, Hyper Voice, Icy Wind, Snarl | unknown | 1 | 9.4% |
- Other signatures: 37.5%

### Goodra-Hisui
- Teams: 8, weighted support: 0.3%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: setup 36.5%, trick-room-abuser 9.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Dragon Pulse, Flamethrower, Flash Cannon, Protect | unknown | 4 | 54.0% |
| Leftovers | Body Press, Heavy Slam, Protect, Shelter | unknown | 1 | 13.5% |
| Leftovers | Body Press, Heavy Slam, Life Dew, Shelter | unknown | 1 | 13.5% |
| Leftovers | Body Press, Dragon Pulse, Life Dew, Shelter | unknown | 1 | 9.5% |
| Expert Belt | Flamethrower, Flash Cannon, Protect, Thunderbolt | unknown | 1 | 9.5% |
- Other signatures: 0.0%

### Meowstic-F
- Teams: 9, weighted support: 0.3%, mega share: 100.0%
- Community: Community 4: Rain
- Roles: mega-attacker 100.0%, fake-out 87.7%, setup 6.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Meowsticite | Expanding Force, Fake Out, Protect, Thunderbolt | unknown | 4 | 62.5% |
| Meowsticite | Alluring Voice, Expanding Force, Fake Out, Protect | fast | 2 | 14.4% |
| Meowsticite | Expanding Force, Fake Out, Protect, Thunderbolt | fast | 1 | 10.8% |
| Meowsticite | Alluring Voice, Expanding Force, Protect, Shadow Ball | fast | 1 | 6.3% |
| Meowsticite | Alluring Voice, Expanding Force, Nasty Plot, Protect | fast | 1 | 6.0% |
- Other signatures: 0.0%

### Scizor
- Teams: 8, weighted support: 0.2%, mega share: 15.9%
- Community: none
- Roles: priority-attack 100.0%, setup 34.4%, trick-room-abuser 18.5%, mega-attacker 15.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Bug Bite, Bullet Punch, Knock Off, Protect | unknown | 3 | 39.2% |
| Expert Belt | Bullet Punch, Iron Head, Protect, X-Scissor | unknown | 1 | 18.5% |
| Metal Coat | Bug Bite, Bullet Punch, Protect, Swords Dance | unknown | 1 | 18.5% |
| Scizorite | Bug Bite, Bullet Punch, Protect, Swords Dance | offensive | 2 | 15.9% |
| Life Orb | Assurance, Bug Bite, Bullet Punch, Protect | offensive | 1 | 8.0% |
- Other signatures: 0.0%

### Kangaskhan
- Teams: 6, weighted support: 0.2%, mega share: 18.5%
- Community: Community 4: Rain
- Roles: fake-out 100.0%, priority-attack 18.5%, mega-attacker 18.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Silk Scarf | Fake Out, Last Resort | unknown | 2 | 31.5% |
| Chople Berry | Facade, Fake Out, Hammer Arm, Protect | unknown | 1 | 18.5% |
| Leftovers | Double-Edge, Drain Punch, Fake Out, Sucker Punch | unknown | 1 | 18.5% |
| Kangaskhanite | Crunch, Double-Edge, Fake Out, Hammer Arm | unknown | 1 | 18.5% |
| Life Orb | Brick Break, Double-Edge, Fake Out, Hammer Arm | unknown | 1 | 13.1% |
- Other signatures: 0.0%

### Araquanid
- Teams: 6, weighted support: 0.2%, mega share: 0.0%
- Community: Community 3: Psyspam
- Roles: wide-guard 100.0%, trick-room-abuser 36.9%, weather-setter 18.5%, speed-drop 18.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Never-Melt Ice | Hydro Pump, Ice Beam, Protect, Wide Guard | unknown | 2 | 26.1% |
| Mystic Water | Leech Life, Liquidation, Protect, Wide Guard | unknown | 1 | 18.5% |
| Charti Berry | Entrainment, Liquidation, Rain Dance, Wide Guard | unknown | 1 | 18.5% |
| Life Orb | Leech Life, Liquidation, Protect, Wide Guard | unknown | 1 | 18.5% |
| Sitrus Berry | Icy Wind, Liquidation, Protect, Wide Guard | unknown | 1 | 18.5% |
- Other signatures: 0.0%

### Clefable
- Teams: 6, weighted support: 0.2%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
- Roles: follow-me 100.0%, helping-hand 51.2%, trick-room-abuser 29.9%, speed-drop 18.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Follow Me, Helping Hand, Moonblast, Protect | unknown | 2 | 37.8% |
| Sitrus Berry | Follow Me, Life Dew, Moonblast, Protect | unknown | 1 | 18.9% |
| Leftovers | Follow Me, Moonblast, Skill Swap, Thunder Wave | unknown | 1 | 18.9% |
| Leftovers | Follow Me, Helping Hand, Moonblast, Protect | unknown | 1 | 13.4% |
| Sitrus Berry | Follow Me, Moonblast, Protect, Skill Swap | bulky | 1 | 11.0% |
- Other signatures: 0.0%

### Heracross
- Teams: 6, weighted support: 0.2%, mega share: 66.7%
- Community: none
- Roles: mega-attacker 66.7%, helping-hand 19.5%, ally-boost 19.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Heracronite | Close Combat, Detect, Pin Missile, Rock Blast | unknown | 2 | 33.3% |
| Heracronite | Close Combat, Pin Missile, Protect, Rock Blast | unknown | 2 | 33.3% |
| Coba Berry | Coaching, Helping Hand, Low Kick, Upper Hand | unknown | 1 | 19.5% |
| White Herb | Bullet Seed, Close Combat, Pin Missile, Trailblaze | unknown | 1 | 13.8% |
- Other signatures: 0.0%

### Pincurchin
- Teams: 7, weighted support: 0.2%, mega share: 0.0%
- Community: Community 5: Pincurchin / Raichu-Alola
- Roles: terrain-setter 100.0%, trick-room-abuser 85.5%, priority-attack 76.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Muddy Water, Protect, Rising Voltage, Sucker Punch | unknown | 1 | 20.5% |
| Air Balloon | Acupressure, Protect, Rising Voltage, Sucker Punch | unknown | 1 | 20.5% |
| Eject Button | Protect, Rising Voltage, Scald, Thunderbolt | unknown | 1 | 14.5% |
| Magnet | Protect, Rising Voltage, Scald, Sucker Punch | unknown | 1 | 14.5% |
| Electric Seed | Liquidation, Poison Jab, Sucker Punch, Zing Zap | min-speed | 1 | 10.3% |
- Other signatures: 19.6%

### Hippowdon
- Teams: 6, weighted support: 0.2%, mega share: 0.0%
- Community: Community 1: Sneasler / Salamence
- Roles: weather-setter 100.0%, status 100.0%, trick-room-abuser 29.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | High Horsepower, Protect, Stone Edge, Yawn | unknown | 3 | 49.7% |
| Sitrus Berry | High Horsepower, Protect, Rock Slide, Yawn | unknown | 1 | 20.6% |
| Leftovers | High Horsepower, Ice Fang, Protect, Yawn | min-speed | 1 | 18.4% |
| Leftovers | High Horsepower, Ice Fang, Protect, Yawn | bulky | 1 | 11.4% |
- Other signatures: 0.0%

### Abomasnow
- Teams: 6, weighted support: 0.2%, mega share: 100.0%
- Community: Community 3: Psyspam
- Roles: weather-setter 100.0%, trick-room-abuser 100.0%, mega-attacker 100.0%, screens 41.3%, priority-attack 23.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Abomasite | Aurora Veil, Blizzard, Earth Power, Leaf Storm | unknown | 2 | 41.3% |
| Abomasite | Blizzard, Earth Power, Energy Ball, Protect | unknown | 2 | 35.2% |
| Abomasite | Blizzard, Earth Power, Energy Ball, Ice Shard | unknown | 1 | 14.6% |
| Abomasite | Blizzard, Earth Power, Energy Ball, Ice Shard | min-speed | 1 | 8.9% |
- Other signatures: 0.0%

### Ampharos
- Teams: 6, weighted support: 0.2%, mega share: 100.0%
- Community: Community 4: Rain
- Roles: mega-attacker 100.0%, trick-room-abuser 64.6%, setup 50.0%, speed-drop 14.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Ampharosite | Dragon Pulse, Focus Blast, Protect, Thunderbolt | unknown | 1 | 20.7% |
| Ampharosite | Cotton Guard, Dragon Pulse, Parabolic Charge, Protect | unknown | 1 | 20.7% |
| Ampharosite | Dragon Pulse, Power Gem, Protect, Thunderbolt | unknown | 1 | 14.6% |
| Ampharosite | Charge, Dragon Pulse, Parabolic Charge, Protect | unknown | 1 | 14.6% |
| Ampharosite | Cotton Guard, Dragon Pulse, Protect, Thunderbolt | unknown | 1 | 14.6% |
- Other signatures: 14.6%

### Mimikyu
- Teams: 7, weighted support: 0.2%, mega share: 0.0%
- Community: none
- Roles: trick-room-setter 57.6%, priority-attack 48.5%, setup 21.2%, status 21.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Bulk Up, Drain Punch, Shadow Claw, Thunderbolt | unknown | 1 | 21.2% |
| Spell Tag | Protect, Shadow Claw, Shadow Sneak, Wood Hammer | unknown | 1 | 21.2% |
| White Herb | Destiny Bond, Play Rough, Trick Room, Will-O-Wisp | unknown | 1 | 21.2% |
| Mental Herb | Play Rough, Shadow Sneak, Trick Room, Wood Hammer | unknown | 1 | 15.0% |
| Mental Herb | Play Rough, Protect, Shadow Claw, Trick Room | min-speed | 1 | 9.1% |
- Other signatures: 12.3%

### Overqwil
- Teams: 6, weighted support: 0.2%, mega share: 0.0%
- Community: none
- Roles: intimidate 51.7%, setup 42.1%, priority-attack 21.4%, disruption 21.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Aqua Jet, Barb Barrage, Protect, Throat Chop | unknown | 1 | 21.4% |
| Life Orb | Gunk Shot, Protect, Taunt, Throat Chop | unknown | 1 | 21.4% |
| Leftovers | Barb Barrage, Minimize, Protect, Stockpile | unknown | 1 | 15.1% |
| Life Orb | Liquidation, Poison Jab, Protect, Throat Chop | unknown | 1 | 15.1% |
| Leftovers | Minimize, Stockpile, Swords Dance, Throat Chop | unknown | 1 | 15.1% |
- Other signatures: 11.8%

### Drampa
- Teams: 6, weighted support: 0.2%, mega share: 46.8%
- Community: none
- Roles: mega-attacker 46.8%, trick-room-abuser 15.6%, helping-hand 15.6%, speed-drop 15.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Psychic Seed | Dragon Pulse, Energy Ball, Flamethrower, Protect | unknown | 1 | 22.0% |
| Drampanite | Earth Power, Hurricane, Protect, Thunderbolt | unknown | 1 | 15.6% |
| Drampanite | Earth Power, Energy Ball, Hyper Voice, Thunderbolt | unknown | 1 | 15.6% |
| Expert Belt | Energy Ball, Flamethrower, Helping Hand, Protect | unknown | 1 | 15.6% |
| Quick Claw | Dragon Pulse, Flamethrower, Hyper Voice, Protect | unknown | 1 | 15.6% |
- Other signatures: 15.6%

### Alakazam
- Teams: 7, weighted support: 0.2%, mega share: 77.6%
- Community: Community 3: Psyspam
- Roles: mega-attacker 77.6%, setup 43.8%, disruption 40.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Alakazite | Encore, Expanding Force, Focus Blast, Protect | unknown | 2 | 33.6% |
| Life Orb | Dazzling Gleam, Expanding Force, Nasty Plot, Protect | unknown | 1 | 22.4% |
| Alakazite | Expanding Force, Focus Blast, Protect, Substitute | unknown | 1 | 15.8% |
| Alakazite | Calm Mind, Expanding Force, Focus Blast, Protect | fast | 1 | 11.8% |
| Alakazite | Calm Mind, Dazzling Gleam, Expanding Force, Protect | fast | 1 | 9.6% |
- Other signatures: 6.8%

### Tinkaton
- Teams: 5, weighted support: 0.2%, mega share: 0.0%
- Community: none
- Roles: fake-out 100.0%, helping-hand 45.1%, disruption 32.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Fake Out, Feint, Gigaton Hammer, Protect | unknown | 1 | 22.6% |
| Occa Berry | Encore, Fake Out, Gigaton Hammer, Protect | unknown | 1 | 22.6% |
| Focus Sash | Fake Out, Gigaton Hammer, Helping Hand, Protect | unknown | 1 | 22.6% |
| Metal Coat | Fake Out, Gigaton Hammer, Helping Hand, Protect | unknown | 1 | 22.6% |
| Eject Button | Encore, Fake Out, Gigaton Hammer, Protect | fast | 1 | 9.7% |
- Other signatures: 0.0%

### Ditto
- Teams: 5, weighted support: 0.2%, mega share: 0.0%
- Community: none
- Roles: trick-room-abuser 38.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Transform | unknown | 2 | 45.3% |
| Focus Sash | Transform | unknown | 1 | 22.7% |
| Sitrus Berry | Transform | unknown | 1 | 16.0% |
| Leftovers | Transform | unknown | 1 | 16.0% |
- Other signatures: 0.0%

### Starmie
- Teams: 5, weighted support: 0.2%, mega share: 100.0%
- Community: Community 3: Psyspam
- Roles: mega-attacker 100.0%, setup 16.4%, priority-attack 16.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Starminite | Expanding Force, Ice Beam, Liquidation, Protect | unknown | 2 | 50.9% |
| Starminite | Bulk Up, Ice Spinner, Liquidation, Protect | unknown | 1 | 16.4% |
| Starminite | Aqua Jet, Ice Spinner, Liquidation, Protect | unknown | 1 | 16.4% |
| Starminite | Expanding Force, Ice Spinner, Liquidation, Protect | unknown | 1 | 16.4% |
- Other signatures: 0.0%

### Crabominable
- Teams: 5, weighted support: 0.2%, mega share: 75.8%
- Community: none
- Roles: trick-room-abuser 100.0%, mega-attacker 75.8%, wide-guard 48.3%, priority-attack 24.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Crabominite | Drain Punch, Ice Hammer, Rock Slide, Wide Guard | unknown | 2 | 48.3% |
| Life Orb | Drain Punch, Ice Punch, Mach Punch, Thunder Punch | unknown | 1 | 24.2% |
| Crabominite | Close Combat, Ice Hammer, Protect, Thunder Punch | unknown | 1 | 17.1% |
| Crabominite | Drain Punch, Ice Hammer, Protect, Rock Slide | min-speed | 1 | 10.4% |
- Other signatures: 0.0%

### Arcanine
- Teams: 5, weighted support: 0.2%, mega share: 0.0%
- Community: none
- Roles: priority-attack 100.0%, intimidate 100.0%, ally-boost 58.7%, status 17.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Extreme Speed, Flare Blitz, Howl, Protect | unknown | 2 | 48.3% |
| Sitrus Berry | Extreme Speed, Flare Blitz, Protect, Wild Charge | unknown | 1 | 24.2% |
| Rocky Helmet | Extreme Speed, Flare Blitz, Morning Sun, Will-O-Wisp | unknown | 1 | 17.1% |
| Sitrus Berry | Extreme Speed, Flare Blitz, Howl, Protect | bulky | 1 | 10.4% |
- Other signatures: 0.0%

### Manectric
- Teams: 6, weighted support: 0.2%, mega share: 100.0%
- Community: Community 4: Rain
- Roles: intimidate 100.0%, mega-attacker 100.0%, spa-drop 82.8%, pivot 82.8%, screens 17.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Manectite | Overheat, Protect, Snarl, Volt Switch | unknown | 2 | 34.4% |
| Manectite | Overheat, Protect, Snarl, Thunderbolt | unknown | 1 | 17.2% |
| Manectite | Light Screen, Overheat, Protect, Volt Switch | unknown | 1 | 17.2% |
| Manectite | Protect, Snarl, Thunder, Volt Switch | unknown | 1 | 17.2% |
| Manectite | Overheat, Protect, Snarl, Volt Switch | fast | 1 | 14.1% |
- Other signatures: 0.0%

### Scrafty
- Teams: 6, weighted support: 0.2%, mega share: 60.5%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
- Roles: fake-out 100.0%, intimidate 100.0%, mega-attacker 60.5%, trick-room-abuser 33.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Scraftinite | Drain Punch, Fake Out, Protect, Throat Chop | unknown | 1 | 24.9% |
| Scraftinite | Close Combat, Fake Out, Knock Off, Protect | unknown | 1 | 24.9% |
| Focus Sash | Close Combat, Fake Out, Knock Off, Protect | fast | 1 | 16.9% |
| Sharp Beak | Detect, Drain Punch, Fake Out, Knock Off | offensive | 1 | 11.8% |
| Focus Sash | Close Combat, Fake Out, Knock Off, Protect | bulky | 1 | 10.7% |
- Other signatures: 10.7%

### Gyarados
- Teams: 5, weighted support: 0.2%, mega share: 44.5%
- Community: none
- Roles: intimidate 100.0%, mega-attacker 44.5%, setup 37.9%, speed-drop 26.1%, disruption 26.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Gyaradosite | Crunch, Dragon Dance, Protect, Waterfall | unknown | 1 | 26.1% |
| King's Rock | Protect, Taunt, Thunder Wave, Waterfall | unknown | 1 | 26.1% |
| Gyaradosite | Bite, Earthquake, Protect, Waterfall | unknown | 1 | 18.4% |
| Choice Scarf | Ice Fang, Power Whip, Stone Edge, Waterfall | fast | 1 | 17.6% |
| White Herb | Dragon Dance, Earthquake, Temper Flare, Waterfall | fast | 1 | 11.8% |
- Other signatures: 0.0%

### Gliscor
- Teams: 4, weighted support: 0.2%, mega share: 0.0%
- Community: none
- Roles: tailwind 46.0%, spa-drop 27.0%, pivot 27.0%, setup 27.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | High Horsepower, Knock Off, Struggle Bug, U-turn | unknown | 1 | 27.0% |
| Soft Sand | High Horsepower, Protect, Rock Slide, Tailwind | unknown | 1 | 27.0% |
| Sitrus Berry | Dual Wingbeat, High Horsepower, Protect, Swords Dance | unknown | 1 | 27.0% |
| Life Orb | Dual Wingbeat, High Horsepower, Protect, Tailwind | unknown | 1 | 19.1% |
- Other signatures: 0.0%

### Greninja
- Teams: 4, weighted support: 0.2%, mega share: 73.0%
- Community: none
- Roles: mega-attacker 73.0%, priority-attack 27.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Greninjite | Gunk Shot, Protect, Rock Slide, Water Shuriken | unknown | 1 | 27.0% |
| Choice Scarf | Dark Pulse, Grass Knot, Ice Beam, Surf | unknown | 1 | 27.0% |
| Greninjite | Dark Pulse, Extrasensory, Ice Beam, Surf | unknown | 1 | 27.0% |
| Greninjite | Blizzard, Dark Pulse, Hydro Pump, Protect | unknown | 1 | 19.1% |
- Other signatures: 0.0%

### Steelix
- Teams: 4, weighted support: 0.1%, mega share: 100.0%
- Community: none
- Roles: trick-room-abuser 100.0%, mega-attacker 100.0%, wide-guard 29.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Steelixite | Curse, Heavy Slam, High Horsepower, Protect | unknown | 3 | 70.7% |
| Steelixite | Earthquake, Ice Fang, Stone Edge, Wide Guard | unknown | 1 | 29.3% |
- Other signatures: 0.0%

### Ninetales
- Teams: 4, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: weather-setter 100.0%, helping-hand 41.4%, disruption 20.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Fake Tears, Heat Wave, Overheat, Solar Beam | unknown | 1 | 29.3% |
| Heat Rock | Heat Wave, Protect, Roar, Solar Beam | unknown | 1 | 29.3% |
| Charcoal | Heat Wave, Helping Hand, Protect, Weather Ball | unknown | 1 | 20.7% |
| Focus Sash | Encore, Helping Hand, Protect, Weather Ball | unknown | 1 | 20.7% |
- Other signatures: 0.0%

### Houndoom
- Teams: 4, weighted support: 0.1%, mega share: 100.0%
- Community: Community 3: Psyspam
- Roles: mega-attacker 100.0%, setup 29.3%, status 29.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Houndoominite | Dark Pulse, Heat Wave, Overheat, Protect | unknown | 2 | 41.4% |
| Houndoominite | Dark Pulse, Heat Wave, Nasty Plot, Protect | unknown | 1 | 29.3% |
| Houndoominite | Dark Pulse, Heat Wave, Protect, Will-O-Wisp | unknown | 1 | 29.3% |
- Other signatures: 0.0%

### Torterra
- Teams: 5, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: wide-guard 69.5%, setup 30.5%, trick-room-abuser 26.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| White Herb | Earthquake, Protect, Rock Slide, Shell Smash | unknown | 1 | 30.5% |
| Life Orb | Earthquake, Headlong Rush, Wide Guard, Wood Hammer | bulky | 2 | 26.3% |
| Sitrus Berry | Headlong Rush, Smack Down, Wide Guard, Wood Hammer | unknown | 1 | 21.6% |
| Life Orb | Earthquake, Headlong Rush, Wide Guard, Wood Hammer | unknown | 1 | 21.6% |
- Other signatures: 0.0%

### Bellibolt
- Teams: 5, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: trick-room-abuser 56.8%, speed-drop 21.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Parabolic Charge, Protect, Soak, Thunderbolt | unknown | 2 | 52.1% |
| Sitrus Berry | Electroweb, Protect, Soak, Thunderbolt | unknown | 1 | 21.6% |
| Leftovers | Parabolic Charge, Protect, Soak, Thunderbolt | bulky | 1 | 13.2% |
| Leftovers | Muddy Water, Parabolic Charge, Protect, Thunderbolt | offensive | 1 | 13.2% |
- Other signatures: 0.0%

### Runerigus
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: trick-room-setter 100.0%, status 100.0%, trick-room-abuser 100.0%, ally-switch 66.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Ally Switch, Earthquake, Trick Room, Will-O-Wisp | unknown | 2 | 66.7% |
| Grassy Seed | Shadow Claw, Skill Swap, Trick Room, Will-O-Wisp | unknown | 1 | 33.3% |
- Other signatures: 0.0%

### Palafin
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: priority-attack 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Mystic Water | Close Combat, Jet Punch, Protect, Wave Crash | unknown | 1 | 33.3% |
| Life Orb | Close Combat, Jet Punch, Protect, Wave Crash | unknown | 1 | 33.3% |
| Sitrus Berry | Ice Punch, Jet Punch, Protect, Wave Crash | unknown | 1 | 33.3% |
- Other signatures: 0.0%

### Sceptile
- Teams: 3, weighted support: 0.1%, mega share: 100.0%
- Community: none
- Roles: mega-attacker 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sceptilite | Detect, Dragon Pulse, Earth Power, Grass Knot | unknown | 1 | 33.3% |
| Sceptilite | Dragon Pulse, Earth Power, Giga Drain, Protect | unknown | 1 | 33.3% |
| Sceptilite | Detect, Dragon Pulse, Earth Power, Energy Ball | unknown | 1 | 33.3% |
- Other signatures: 0.0%

### Cofagrigus
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: trick-room-setter 100.0%, trick-room-abuser 100.0%, setup 66.7%, status 66.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Body Press, Iron Defense, Shadow Ball, Trick Room | unknown | 1 | 33.3% |
| Mental Herb | Destiny Bond, Night Shade, Trick Room, Will-O-Wisp | unknown | 1 | 33.3% |
| Leftovers | Body Press, Iron Defense, Trick Room, Will-O-Wisp | unknown | 1 | 33.3% |
- Other signatures: 0.0%

### Azumarill
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: priority-attack 100.0%, setup 100.0%, trick-room-abuser 34.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Aqua Jet, Belly Drum, Play Rough, Protect | unknown | 3 | 100.0% |
- Other signatures: 0.0%

### Noivern
- Teams: 5, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: tailwind 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Defog, Draco Meteor, Super Fang, Tailwind | unknown | 1 | 26.0% |
| Focus Sash | Draco Meteor, Dragon Cheer, Super Fang, Tailwind | unknown | 1 | 26.0% |
| Normal Gem | Boomburst, Draco Meteor, Protect, Tailwind | fast | 1 | 21.3% |
| Focus Sash | Draco Meteor, Protect, Super Fang, Tailwind | fast | 1 | 13.7% |
| Focus Sash | Draco Meteor, Protect, Super Fang, Tailwind | unknown | 1 | 13.0% |
- Other signatures: 0.0%

### Tauros-Paldea-Aqua
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: priority-attack 100.0%, intimidate 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Aqua Jet, Close Combat, Iron Head, Raging Bull | unknown | 1 | 36.9% |
| Focus Sash | Aqua Jet, Close Combat, Protect, Raging Bull | unknown | 1 | 36.9% |
| White Herb | Aqua Jet, Close Combat, Protect, Wave Crash | unknown | 1 | 26.1% |
- Other signatures: 0.0%

### Raichu-Alola
- Teams: 4, weighted support: 0.1%, mega share: 0.0%
- Community: Community 5: Pincurchin / Raichu-Alola
- Roles: fake-out 81.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Expanding Force, Fake Out, Grass Knot, Thunder | unknown | 1 | 37.6% |
| Focus Sash | Expanding Force, Fake Out, Rising Voltage, Thunder | unknown | 1 | 26.6% |
| Life Orb | Alluring Voice, Expanding Force, Protect, Rising Voltage | offensive | 1 | 18.8% |
| Life Orb | Expanding Force, Fake Out, Protect, Rising Voltage | fast | 1 | 17.0% |
- Other signatures: 0.0%

### Dragalge
- Teams: 4, weighted support: 0.1%, mega share: 61.4%
- Community: none
- Roles: trick-room-abuser 61.4%, mega-attacker 61.4%, pivot 34.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Dragon Pulse, Protect, Sludge Bomb, Thunderbolt | unknown | 1 | 38.6% |
| Dragalgite | Draco Meteor, Protect, Sludge Bomb, Thunderbolt | unknown | 1 | 27.3% |
| Dragalgite | Draco Meteor, Flip Turn, Protect, Sludge Bomb | offensive | 1 | 17.5% |
| Dragalgite | Dragon Pulse, Flip Turn, Protect, Sludge Bomb | min-speed | 1 | 16.6% |
- Other signatures: 0.0%

### Aggron
- Teams: 3, weighted support: 0.1%, mega share: 82.3%
- Community: none
- Roles: setup 82.3%, mega-attacker 82.3%, trick-room-abuser 58.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Aggronite | Body Press, Heavy Slam, Iron Defense, Protect | unknown | 2 | 82.3% |
| Life Orb | Blizzard, Fire Blast, Head Smash, Protect | offensive | 1 | 17.7% |
- Other signatures: 0.0%

### Persian-Alola
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: spa-drop 100.0%, fake-out 17.7%, pivot 17.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Charm, Foul Play, Snarl, Switcheroo | unknown | 2 | 82.3% |
| Focus Sash | Fake Out, Foul Play, Parting Shot, Snarl | fast | 1 | 17.7% |
- Other signatures: 0.0%

### Pidgeot
- Teams: 3, weighted support: 0.1%, mega share: 100.0%
- Community: none
- Roles: tailwind 100.0%, mega-attacker 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Pidgeotite | Heat Wave, Hurricane, Protect, Tailwind | unknown | 2 | 58.6% |
| Pidgeotite | Hurricane, Hyper Beam, Protect, Tailwind | unknown | 1 | 41.4% |
- Other signatures: 0.0%

### Krookodile
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: intimidate 100.0%, disruption 41.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Expert Belt | High Horsepower, Knock Off, Rock Slide, Taunt | unknown | 1 | 41.4% |
| Chople Berry | Close Combat, High Horsepower, Knock Off, Protect | unknown | 1 | 29.3% |
| Choice Scarf | Close Combat, High Horsepower, Knock Off, Rock Slide | unknown | 1 | 29.3% |
- Other signatures: 0.0%

### Feraligatr
- Teams: 3, weighted support: 0.1%, mega share: 58.6%
- Community: none
- Roles: mega-attacker 58.6%, priority-attack 41.4%, setup 29.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Aqua Jet, Ice Punch, Liquidation, Protect | unknown | 1 | 41.4% |
| Feraligite | Double-Edge, Dragon Dance, Liquidation, Protect | unknown | 1 | 29.3% |
| Feraligite | Body Slam, Ice Fang, Protect, Waterfall | unknown | 1 | 29.3% |
- Other signatures: 0.0%

### Jolteon
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: speed-drop 29.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Alluring Voice, Detect, Rising Voltage, Shadow Ball | unknown | 1 | 41.4% |
| Focus Sash | Charm, Electroweb, Protect, Thunderbolt | unknown | 1 | 29.3% |
| Bright Powder | Charm, Discharge, Eerie Impulse, Fake Tears | unknown | 1 | 29.3% |
- Other signatures: 0.0%

### Basculegion-F
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: pivot 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Flip Turn, Last Respects, Liquidation, Psychic Fangs | unknown | 1 | 46.8% |
| Choice Scarf | Flip Turn, Ice Beam, Last Respects, Wave Crash | unknown | 1 | 33.1% |
| Choice Scarf | Flip Turn, Ice Beam, Muddy Water, Shadow Ball | fast | 1 | 20.2% |
- Other signatures: 0.0%

### Malamar
- Teams: 3, weighted support: 0.1%, mega share: 75.7%
- Community: none
- Roles: trick-room-setter 100.0%, mega-attacker 75.7%, trick-room-abuser 24.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Malamarite | Night Slash, Protect, Superpower, Trick Room | unknown | 1 | 37.9% |
| Malamarite | Knock Off, Superpower, Topsy-Turvy, Trick Room | unknown | 1 | 37.9% |
| Roseli Berry | Knock Off, Skill Swap, Superpower, Trick Room | min-speed | 1 | 24.3% |
- Other signatures: 0.0%

### Clawitzer
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: trick-room-abuser 61.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Aura Sphere, Dark Pulse, Dragon Pulse, Terrain Pulse | unknown | 1 | 38.3% |
| Choice Scarf | Aura Sphere, Dark Pulse, Terrain Pulse, Water Pulse | unknown | 1 | 38.3% |
| Rindo Berry | Aura Sphere, Dark Pulse, Dragon Pulse, Water Pulse | offensive | 1 | 23.4% |
- Other signatures: 0.0%

### Cinderace
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Community: none
- Roles: spa-drop 59.4%, pivot 40.6%, priority-attack 33.3%, disruption 26.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Pyro Ball, Trailblaze, U-turn, Zen Headbutt | unknown | 1 | 40.6% |
| White Herb | High Jump Kick, Pyro Ball, Snarl, Sucker Punch | fast | 1 | 33.3% |
| White Herb | High Jump Kick, Pyro Ball, Snarl, Taunt | fast | 1 | 26.0% |
- Other signatures: 0.0%

## 11. Conditional Set Table
### Rillaboom
| Partner | Teams | Support | Lift | Ladder teammate rank | Rillaboom given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Gholdengo | 710 | 24.5% | 1.556 | 1 (Rillaboom in Gholdengo's) | items: Miracle Seed 84.5%, Sitrus Berry 6.7%, Occa Berry 3.4%, Life Orb 1.8%, Expert Belt 1.3%, Grassy Seed 0.9%, Kebia Berry 0.7%, Leftovers 0.2%, White Herb 0.2%, Eject Button 0.1%, Muscle Band 0.1%, Quick Claw 0.1%; spreads: unknown 95.4%, bulky 2.4%, offensive 1.9%, fast 0.3% |
| Sneasler | 691 | 22.8% | 1.005 | 2 (Sneasler in Rillaboom's) | items: Miracle Seed 73.6%, Sitrus Berry 13.2%, Life Orb 5.2%, Occa Berry 3.8%, Eject Button 1.2%, Kebia Berry 0.9%, Grassy Seed 0.8%, Leftovers 0.6%, Expert Belt 0.3%, Focus Sash 0.3%, Coba Berry 0.1%; spreads: unknown 91.7%, bulky 4.5%, offensive 3.1%, fast 0.5%, min-speed 0.1% |
| Raichu | 619 | 22.5% | 1.606 | 1 (Rillaboom in Raichu's) | items: Miracle Seed 82.7%, Sitrus Berry 8.5%, Life Orb 3.8%, Occa Berry 2.4%, Grassy Seed 0.9%, Kebia Berry 0.8%, Leftovers 0.3%, Eject Button 0.2%, Coba Berry 0.2%, Expert Belt 0.1%; spreads: unknown 97.4%, offensive 1.4%, bulky 1.1%, fast 0.0%; moves: High Horsepower +18pp, U-turn -16pp |
| Salamence | 638 | 20.3% | 1.233 | 3 (Salamence in Rillaboom's) | items: Miracle Seed 73.9%, Sitrus Berry 13.2%, Life Orb 4.0%, Expert Belt 3.2%, Occa Berry 2.7%, Grassy Seed 0.9%, Kebia Berry 0.9%, Leftovers 0.8%, Coba Berry 0.2%, Eject Button 0.1%, Rocky Helmet 0.1%, Focus Sash 0.1%; spreads: unknown 90.4%, offensive 4.8%, bulky 4.0%, fast 0.6%, min-speed 0.2% |
| Incineroar | 603 | 19.9% | 1.252 | 1 (Rillaboom in Incineroar's) | items: Miracle Seed 66.1%, Eject Button 13.0%, Occa Berry 6.5%, Life Orb 5.5%, Sitrus Berry 4.8%, Grassy Seed 1.3%, Leftovers 1.1%, Expert Belt 0.4%, Kebia Berry 0.4%, Coba Berry 0.4%, Iron Ball 0.4%, Focus Sash 0.2%; spreads: unknown 90.6%, bulky 5.6%, offensive 2.6%, min-speed 0.8%, fast 0.5% |

### Sneasler
| Partner | Teams | Support | Lift | Ladder teammate rank | Sneasler given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 691 | 22.8% | 1.005 | 2 (Sneasler in Rillaboom's) | items: Grassy Seed 59.1%, White Herb 31.1%, Focus Sash 8.3%, Psychic Seed 0.6%, Life Orb 0.4%, (no item) 0.4%, Normal Gem 0.2%; spreads: unknown 91.7%, fast 6.3%, bulky 1.0%, offensive 0.6%, mixed 0.4% |
| Salamence | 578 | 18.6% | 1.488 | 2 (Sneasler in Salamence's) | items: Grassy Seed 39.8%, White Herb 33.6%, Psychic Seed 20.4%, Focus Sash 5.2%, (no item) 0.5%, Life Orb 0.4%, Normal Gem 0.2%; spreads: unknown 91.8%, fast 6.3%, bulky 0.9%, mixed 0.7%, offensive 0.3% |
| Kingambit | 408 | 13.5% | 1.334 | 1 (Sneasler in Kingambit's) | items: Grassy Seed 37.2%, White Herb 27.1%, Focus Sash 18.7%, Psychic Seed 15.4%, Life Orb 1.1%, (no item) 0.3%, Normal Gem 0.2%; spreads: unknown 91.2%, fast 7.6%, bulky 0.6%, offensive 0.4%, mixed 0.2%; moves: Fake Out +20pp |
| Incineroar | 393 | 12.9% | 1.073 | 2 (Sneasler in Incineroar's) | items: Grassy Seed 44.3%, White Herb 27.0%, Focus Sash 19.6%, Psychic Seed 8.0%, Life Orb 0.8%, (no item) 0.3%; spreads: unknown 90.8%, fast 7.3%, bulky 0.7%, offensive 0.7%, mixed 0.4% |
| Gholdengo | 331 | 10.8% | 0.906 | 3 (Sneasler in Gholdengo's) | items: Grassy Seed 56.7%, White Herb 27.0%, Psychic Seed 13.1%, Focus Sash 2.8%, (no item) 0.4%; spreads: unknown 94.3%, fast 4.1%, mixed 0.9%, bulky 0.5%, offensive 0.2%; moves: Rock Tomb +19pp |

### Salamence
| Partner | Teams | Support | Lift | Ladder teammate rank | Salamence given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 638 | 20.3% | 1.233 | 3 (Salamence in Rillaboom's) | items: Salamencite 100.0%; spreads: unknown 90.4%, fast 9.4%, mixed 0.2%, bulky 0.1% |
| Sneasler | 578 | 18.6% | 1.488 | 2 (Sneasler in Salamence's) | items: Salamencite 100.0%; spreads: unknown 91.8%, fast 8.0%, mixed 0.1%, bulky 0.1% |
| Gholdengo | 381 | 12.2% | 1.398 | 2 (Salamence in Gholdengo's) | items: Salamencite 100.0%; spreads: unknown 92.6%, fast 7.3%, bulky 0.1% |
| Arcanine-Hisui | 296 | 9.8% | 1.536 | 2 (Salamence in Arcanine-Hisui's) | items: Salamencite 100.0%; spreads: unknown 94.6%, fast 4.8%, mixed 0.3%, offensive 0.2% |
| Kingambit | 283 | 8.8% | 1.197 | 3 (Salamence in Kingambit's) | items: Salamencite 100.0%; spreads: unknown 85.8%, fast 13.6%, mixed 0.4%, bulky 0.3% |

### Incineroar
| Partner | Teams | Support | Lift | Ladder teammate rank | Incineroar given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 603 | 19.9% | 1.252 | 1 (Rillaboom in Incineroar's) | items: Sitrus Berry 67.9%, Passho Berry 10.4%, Chople Berry 10.0%, Rocky Helmet 7.3%, Leftovers 1.0%, Lum Berry 0.8%, White Herb 0.7%, Expert Belt 0.6%, Life Orb 0.6%, Bright Powder 0.2%, Charcoal 0.1%, Eject Button 0.1%, Grassy Seed 0.1%; spreads: unknown 90.6%, bulky 7.6%, min-speed 1.2%, fast 0.4%, offensive 0.2% |
| Sneasler | 393 | 12.9% | 1.073 | 2 (Sneasler in Incineroar's) | items: Sitrus Berry 80.4%, Rocky Helmet 7.9%, Chople Berry 5.0%, Passho Berry 3.6%, White Herb 1.0%, Leftovers 0.6%, Life Orb 0.6%, Expert Belt 0.2%, Charcoal 0.2%, Black Glasses 0.2%, Eject Button 0.2%; spreads: unknown 90.8%, bulky 7.4%, min-speed 0.7%, fast 0.6%, offensive 0.3%, mixed 0.1% |
| Floette-Eternal | 280 | 9.2% | 2.610 | 3 (Incineroar in Floette-Eternal's) | items: Sitrus Berry 83.6%, Rocky Helmet 8.6%, Chople Berry 3.3%, Passho Berry 2.2%, Leftovers 1.3%, Expert Belt 0.5%, Charcoal 0.3%, Bright Powder 0.2%; spreads: unknown 90.5%, bulky 8.7%, fast 0.5%, mixed 0.2%, min-speed 0.1% |
| Gholdengo | 242 | 7.9% | 0.944 | 4 (Incineroar in Gholdengo's) | items: Sitrus Berry 84.1%, Rocky Helmet 9.4%, Chople Berry 4.4%, Leftovers 1.1%, Life Orb 0.5%, Charcoal 0.4%; spreads: unknown 91.4%, bulky 7.7%, min-speed 0.5%, offensive 0.3%, fast 0.2% |
| Garchomp | 189 | 6.4% | 1.407 | 2 (Incineroar in Garchomp's) | items: Sitrus Berry 78.6%, Chople Berry 10.6%, Rocky Helmet 4.2%, Passho Berry 3.6%, Leftovers 1.3%, Life Orb 0.9%, Charcoal 0.5%, Expert Belt 0.5%; spreads: unknown 92.7%, bulky 6.0%, min-speed 0.6%, fast 0.5%, mixed 0.2% |

### Gholdengo
| Partner | Teams | Support | Lift | Ladder teammate rank | Gholdengo given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 710 | 24.5% | 1.556 | 1 (Rillaboom in Gholdengo's) | items: Life Orb 88.1%, Grassy Seed 7.7%, Choice Scarf 1.6%, Leftovers 1.1%, Sitrus Berry 0.4%, Spell Tag 0.2%, Focus Sash 0.2%, Occa Berry 0.2%, Bright Powder 0.2%, White Herb 0.1%, Kasib Berry 0.1%; spreads: unknown 95.4%, fast 3.5%, bulky 0.7%, offensive 0.3% |
| Raichu | 469 | 16.7% | 2.265 | 5 (Raichu in Gholdengo's) | items: Life Orb 89.8%, Grassy Seed 8.3%, Leftovers 0.7%, Choice Scarf 0.5%, Sitrus Berry 0.4%, Focus Sash 0.2%; spreads: unknown 97.1%, fast 2.5%, bulky 0.4% |
| Salamence | 381 | 12.2% | 1.398 | 2 (Salamence in Gholdengo's) | items: Life Orb 84.3%, Grassy Seed 10.9%, Leftovers 1.3%, Choice Scarf 1.3%, Sitrus Berry 0.6%, Focus Sash 0.4%, Metal Coat 0.3%, Spell Tag 0.2%, Occa Berry 0.2%, Kasib Berry 0.2%, White Herb 0.1%; spreads: unknown 92.6%, fast 5.7%, bulky 0.8%, offensive 0.7%, mixed 0.1% |
| Arcanine-Hisui | 347 | 12.0% | 1.972 | 4 (Gholdengo in Arcanine-Hisui's) | items: Life Orb 86.7%, Grassy Seed 11.0%, Leftovers 1.4%, Choice Scarf 0.6%, Sitrus Berry 0.2%; spreads: unknown 97.1%, fast 2.3%, offensive 0.3%, bulky 0.3% |
| Sneasler | 331 | 10.8% | 0.906 | 3 (Sneasler in Gholdengo's) | items: Life Orb 84.9%, Grassy Seed 7.5%, Choice Scarf 3.2%, Leftovers 1.7%, Focus Sash 1.0%, Psychic Seed 0.5%, Metal Coat 0.4%, Spell Tag 0.3%, Kasib Berry 0.3%, Sitrus Berry 0.3%; spreads: unknown 94.3%, fast 4.8%, offensive 0.5%, bulky 0.4% |

### Raichu
| Partner | Teams | Support | Lift | Ladder teammate rank | Raichu given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 619 | 22.5% | 1.606 | 1 (Rillaboom in Raichu's) | items: Raichunite Y 99.9%, Raichunite X 0.1%; spreads: unknown 97.4%, fast 2.6% |
| Gholdengo | 469 | 16.7% | 2.265 | 5 (Raichu in Gholdengo's) | items: Raichunite Y 99.6%, Raichunite X 0.4%; spreads: unknown 97.1%, fast 2.9% |
| Arcanine-Hisui | 368 | 13.3% | 2.465 | 5 (Raichu in Arcanine-Hisui's) | items: Raichunite Y 99.5%, Raichunite X 0.5%; spreads: unknown 98.2%, fast 1.8% |
| Staraptor | 238 | 9.0% | 2.615 | 5 (Staraptor in Raichu's) | items: Raichunite Y 99.5%, Raichunite X 0.5%; spreads: unknown 99.1%, fast 0.9%; moves: Fake Out +15pp |
| Sneasler | 237 | 8.3% | 0.779 | 6 (Sneasler in Raichu's) | items: Raichunite Y 98.6%, Raichunite X 1.4%; spreads: unknown 97.5%, fast 2.5%; moves: Fake Out -19pp, Encore +17pp |

### Kingambit
| Partner | Teams | Support | Lift | Ladder teammate rank | Kingambit given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sneasler | 408 | 13.5% | 1.334 | 1 (Sneasler in Kingambit's) | items: Chople Berry 38.6%, Life Orb 38.3%, Black Glasses 11.2%, Focus Sash 9.1%, Occa Berry 2.5%, Shuca Berry 0.3%; spreads: unknown 91.2%, offensive 4.1%, fast 2.5%, bulky 1.9%, min-speed 0.2% |
| Rillaboom | 382 | 12.8% | 0.959 | 2 (Rillaboom in Kingambit's) | items: Chople Berry 40.0%, Life Orb 37.9%, Black Glasses 11.4%, Focus Sash 8.6%, Occa Berry 2.1%; spreads: unknown 89.5%, offensive 5.4%, fast 3.1%, bulky 1.7%, min-speed 0.2%, mixed 0.2% |
| Salamence | 283 | 8.8% | 1.197 | 3 (Salamence in Kingambit's) | items: Chople Berry 55.9%, Life Orb 19.9%, Focus Sash 11.0%, Black Glasses 9.7%, Occa Berry 3.6%; spreads: unknown 85.8%, offensive 6.0%, fast 4.9%, bulky 2.8%, min-speed 0.4%, mixed 0.2% |
| Arcanine-Hisui | 164 | 5.8% | 1.118 | 6 (Kingambit in Arcanine-Hisui's) | items: Chople Berry 47.0%, Life Orb 45.8%, Black Glasses 5.2%, Occa Berry 1.2%, Focus Sash 0.7%; spreads: unknown 95.9%, offensive 2.1%, fast 0.7%, bulky 0.7%, mixed 0.3%, min-speed 0.2% |
| Incineroar | 167 | 5.7% | 0.804 | n/a | items: Life Orb 41.3%, Black Glasses 25.2%, Chople Berry 24.7%, Focus Sash 8.5%, Occa Berry 0.4%; spreads: unknown 90.5%, offensive 6.1%, fast 2.3%, bulky 1.1%; moves: Low Kick -21pp, Swords Dance +19pp, Protect +18pp, Iron Head -18pp |

### Arcanine-Hisui
| Partner | Teams | Support | Lift | Ladder teammate rank | Arcanine-Hisui given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 513 | 17.8% | 1.549 | 1 (Rillaboom in Arcanine-Hisui's) | items: Focus Sash 98.3%, Choice Scarf 0.7%, Life Orb 0.6%, Charcoal 0.3%, King's Rock 0.2%; spreads: unknown 96.6%, fast 3.2%, min-speed 0.1% |
| Raichu | 368 | 13.3% | 2.465 | 5 (Raichu in Arcanine-Hisui's) | items: Focus Sash 99.2%, White Herb 0.3%, Choice Scarf 0.2%, King's Rock 0.2%; spreads: unknown 98.2%, fast 1.8% |
| Gholdengo | 347 | 12.0% | 1.972 | 4 (Gholdengo in Arcanine-Hisui's) | items: Focus Sash 98.7%, Life Orb 0.6%, Choice Scarf 0.4%, King's Rock 0.2%; spreads: unknown 97.1%, fast 2.9% |
| Salamence | 296 | 9.8% | 1.536 | 2 (Salamence in Arcanine-Hisui's) | items: Focus Sash 98.3%, Life Orb 1.2%, Choice Scarf 0.5%; spreads: unknown 94.6%, fast 5.4% |
| Sneasler | 280 | 9.5% | 1.090 | 3 (Sneasler in Arcanine-Hisui's) | items: Focus Sash 97.4%, Life Orb 1.5%, Choice Scarf 0.8%, Charcoal 0.3%; spreads: unknown 96.5%, fast 3.5% |

## 12. Sheet vs. Ladder
Ladder: median rank over 15 daily snapshots from 2026-09-16 to 2026-09-30 (M6, M-C, Doubles), those archived within the sheet window (2026-09-09 to 2026-10-02); snapshots are kept 14 days, so a longer window is compared over at most its last 14 days. Teammates and items are from 2026-09-30. Battle data provided by Pokémon Champions Battle Data (https://championsbattledata.com); only figures derived from these snapshots are shown.
Sheet = shared teams (2607 of 2915 placed; of the window's teams, only those with a placement are results; the rest are social shares, videos, and ladder pastes), not only Bo3 open-sheet results.
Ladder teammates: the site's top 8 in its order (metric unstated); absence means not in the top 8, not rare.

### 12.1 Coverage
Ladder top 60 species by median rank: 60 node, 0 thin (fewer than minNodeTeams sheet teams), 0 absent from the sheet.
| Median rank | Ladder species | Sheet species | Sheet teams | Weighted support | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Rillaboom | Rillaboom | 1602 | 54.7% | node |
| 2 | Sneasler | Sneasler | 1246 | 41.5% | node |
| 3 | Salamence | Salamence | 935 | 30.2% | node |
| 4 | Incineroar | Incineroar | 865 | 29.1% | node |
| 5 | Indeedee-F | Indeedee-F | 550 | 18.2% | node |
| 6 | Kingambit | Kingambit | 722 | 24.5% | node |
| 7 | Basculegion | Basculegion | 483 | 15.7% | node |
| 8 | Golisopod | Golisopod | 403 | 13.4% | node |
| 9 | Garchomp | Garchomp | 447 | 15.8% | node |
| 10 | Gholdengo | Gholdengo | 841 | 28.8% | node |
| 11 | Archaludon | Archaludon | 399 | 14.4% | node |
| 11 | Pelipper | Pelipper | 318 | 10.8% | node |
| 13 | Milotic | Milotic | 471 | 16.3% | node |
| 14 | Farigiraf | Farigiraf | 407 | 14.4% | node |
| 15 | Charizard | Charizard | 355 | 12.4% | node |
| 16 | Gardevoir | Gardevoir | 228 | 7.3% | node |
| 17 | Raichu | Raichu | 704 | 25.7% | node |
| 18 | Arcanine-Hisui | Arcanine-Hisui | 608 | 21.1% | node |
| 19 | Sylveon | Sylveon | 340 | 11.8% | node |
| 20 | Tyranitar | Tyranitar | 272 | 9.2% | node |
| 21 | Whimsicott | Whimsicott | 159 | 5.5% | node |
| 22 | Armarouge | Armarouge | 191 | 6.3% | node |
| 23 | Staraptor | Staraptor | 358 | 13.4% | node |
| 24 | Torkoal | Torkoal | 97 | 3.1% | node |
| 26 | Metagross | Metagross | 159 | 5.4% | node |
| 26 | Indeedee | Indeedee | 188 | 6.7% | node |
| 26 | Sinistcha | Sinistcha | 133 | 4.6% | node |
| 27 | Floette-Eternal | Floette-Eternal | 367 | 12.2% | node |
| 29 | Excadrill | Excadrill | 221 | 7.5% | node |
| 30 | Volcarona | Volcarona | 195 | 7.2% | node |
| 31 | Politoed | Politoed | 182 | 6.8% | node |
| 32 | Lucario | Lucario | 84 | 2.5% | node |
| 33 | Grimmsnarl | Grimmsnarl | 153 | 5.4% | node |
| 34 | Swampert | Swampert | 125 | 4.3% | node |
| 35 | Froslass | Froslass | 171 | 6.0% | node |
| 36 | Baxcalibur | Baxcalibur | 67 | 2.0% | node |
| 37 | Gengar | Gengar | 156 | 5.7% | node |
| 38 | Ninetales-Alola | Ninetales-Alola | 57 | 1.9% | node |
| 40 | Dragonite | Dragonite | 85 | 3.1% | node |
| 40 | Glimmora | Glimmora | 121 | 4.3% | node |
| 41 | Pawmot | Pawmot | 57 | 1.7% | node |
| 42 | Aerodactyl | Aerodactyl | 91 | 3.1% | node |
| 42 | Venusaur | Venusaur | 105 | 3.6% | node |
| 43 | Primarina | Primarina | 72 | 2.6% | node |
| 45 | Sableye | Sableye | 30 | 1.1% | node |
| 46 | Hatterene | Hatterene | 58 | 2.0% | node |
| 47 | Blastoise | Blastoise | 49 | 1.7% | node |
| 49 | Delphox | Delphox | 114 | 4.4% | node |
| 49 | Annihilape | Annihilape | 52 | 2.0% | node |
| 50 | Absol | Absol | 47 | 1.5% | node |
| 51 | Corviknight | Corviknight | 72 | 2.8% | node |
| 52 | Maushold-Four | Maushold | 44 | 1.6% | node |
| 53 | Talonflame | Talonflame | 35 | 1.1% | node |
| 54 | Kommo-o | Kommo-o | 116 | 4.1% | node |
| 55 | Ceruledge | Ceruledge | 81 | 3.1% | node |
| 56 | Dragapult | Dragapult | 89 | 3.2% | node |
| 57 | Camerupt | Camerupt | 67 | 2.4% | node |
| 58 | Hydreigon | Hydreigon | 39 | 1.3% | node |
| 59 | Blaziken | Blaziken | 49 | 1.7% | node |
| 60 | Mawile | Mawile | 27 | 0.9% | node |
Sheet-only (not on the ladder, or outside its top 60): Vivillon, Kleavor, Scovillain, Pyroar, Typhlosion-Hisui, Rotom-Heat, Espathra, Altaria, Lycanroc-Dusk, Toxapex, Sirfetch’d, Empoleon, Meganium, Gallade, Rotom-Wash, Tsareena, Vanilluxe, Aegislash, Weavile, Meowscarada, Toxtricity, Grapploct, Lopunny, Mamoswine, Klefki, Chandelure, Zoroark-Hisui, Goodra-Hisui, Meowstic-F, Scizor, Kangaskhan, Araquanid, Clefable, Heracross, Pincurchin, Hippowdon, Abomasnow, Ampharos, Mimikyu, Overqwil, Drampa, Alakazam, Tinkaton, Ditto, Starmie, Crabominable, Arcanine, Manectric, Scrafty, Gyarados, Gliscor, Greninja, Steelix, Ninetales, Houndoom, Torterra, Bellibolt, Runerigus, Palafin, Sceptile, Cofagrigus, Azumarill, Noivern, Tauros-Paldea-Aqua, Raichu-Alola, Dragalge, Aggron, Persian-Alola, Pidgeot, Krookodile, Feraligatr, Jolteon, Basculegion-F, Malamar, Clawitzer, Cinderace

### 12.2 Item Gaps
Ladder items (latest snapshot) on a top-60 species held by at least ladderItemMinShare of the species on the ladder -- a higher share than in the sheet -- that have fewer than minNodeTeams sheet teams.
| Median rank | Species | Item | Ladder share | Sheet share | Sheet teams |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 58 | Hydreigon | Life Orb | 15.9% | 6.5% | 2 |

### 12.3 Bias
#### By usage rank
Over-represented in the sheet vs. the ladder (sheet nodes among the ladder's top 60, score above zero, highest first):
| Species | Sheet rank | Median rank | Sheet teams | Score (log2) |
| :--- | :--- | :--- | :--- | :--- |
| Raichu | 6 | 17 | 704 | 1.50 |
| Arcanine-Hisui | 8 | 18 | 608 | 1.17 |
| Gholdengo | 5 | 10 | 841 | 1.00 |
| Floette-Eternal | 18 | 27 | 367 | 0.58 |
| Kommo-o | 37 | 54 | 116 | 0.55 |
| Delphox | 34 | 49 | 114 | 0.53 |
| Staraptor | 16 | 23 | 358 | 0.52 |
| Dragapult | 39 | 56 | 89 | 0.52 |
| Ceruledge | 40 | 55 | 81 | 0.46 |
| Excadrill | 22 | 29 | 221 | 0.40 |
| Milotic | 10 | 13 | 471 | 0.38 |
| Gengar | 29 | 37 | 156 | 0.35 |
| Froslass | 28 | 35 | 171 | 0.32 |
| Volcarona | 24 | 30 | 195 | 0.32 |
| Politoed | 25 | 31 | 182 | 0.31 |

Under-represented in the sheet (among the ladder's top 60, score below zero, lowest first):
| Species | Sheet rank | Median rank | Sheet teams | Score (log2) |
| :--- | :--- | :--- | :--- | :--- |
| Golisopod | 15 | 8 | 403 | -0.91 |
| Pelipper | 20 | 11 | 318 | -0.86 |
| Indeedee-F | 9 | 5 | 550 | -0.85 |
| Torkoal | 43 | 24 | 97 | -0.84 |
| Basculegion | 12 | 7 | 483 | -0.78 |
| Gardevoir | 23 | 16 | 228 | -0.52 |
| Lucario | 46 | 32 | 84 | -0.52 |
| Whimsicott | 30 | 21 | 159 | -0.51 |
| Baxcalibur | 49 | 36 | 67 | -0.44 |
| Ninetales-Alola | 51 | 38 | 57 | -0.42 |
| Sableye | 59 | 45 | 30 | -0.39 |
| Pawmot | 53 | 41 | 57 | -0.37 |
| Archaludon | 14 | 11 | 399 | -0.35 |
| Sinistcha | 33 | 26 | 133 | -0.34 |
| Metagross | 32 | 26 | 159 | -0.30 |

#### By teammate list
Mean overlap between a species' ladder teammate list and its sheet partners by P(B|A), over the species compared: 80.4%.
- Rillaboom 6/8 shared. Ladder adds Garchomp (#7; sheet P=13%, 197 teams), Basculegion (#8; sheet P=14%, 247 teams). Sheet adds Arcanine-Hisui (P=33%, 513 teams; Arcanine-Hisui's own ladder list includes Rillaboom), Milotic (P=18%, 281 teams; Milotic's own ladder list includes Rillaboom).
- Sneasler 6/8 shared. Ladder adds Basculegion (#6; sheet P=17%, 230 teams), Gardevoir (#7; sheet P=12%, 153 teams). Sheet adds Arcanine-Hisui (P=23%, 280 teams; Arcanine-Hisui's own ladder list includes Sneasler), Raichu (P=20%, 237 teams; Raichu's own ladder list includes Sneasler).
- Salamence 6/8 shared. Ladder adds Incineroar (#5; sheet P=20%, 198 teams), Basculegion (#6; sheet P=18%, 181 teams). Sheet adds Arcanine-Hisui (P=32%, 296 teams; Arcanine-Hisui's own ladder list includes Salamence), Raichu (P=23%, 208 teams; Raichu's own ladder list does not include Salamence).
- Incineroar 6/8 shared. Ladder adds Basculegion (#6; sheet P=15%, 132 teams), Indeedee-F (#8; sheet P=9%, 80 teams). Sheet adds Floette-Eternal (P=32%, 280 teams; Floette-Eternal's own ladder list includes Incineroar), Kingambit (P=20%, 167 teams; Kingambit's own ladder list includes Incineroar).
- Indeedee-F 6/8 shared. Ladder adds Pelipper (#7; sheet P=13%, 73 teams), Torkoal (#8; sheet P=11%, 67 teams). Sheet adds Milotic (P=19%, 92 teams; Milotic's own ladder list includes Indeedee-F), Incineroar (P=15%, 80 teams; Incineroar's own ladder list includes Indeedee-F).
- Kingambit 6/8 shared. Ladder adds Indeedee-F (#6; sheet P=17%, 128 teams), Charizard (#8; sheet P=15%, 103 teams). Sheet adds Arcanine-Hisui (P=24%, 164 teams; Arcanine-Hisui's own ladder list includes Kingambit), Farigiraf (P=20%, 136 teams; Farigiraf's own ladder list includes Kingambit).
- Basculegion 8/8 shared.
- Golisopod 6/8 shared. Ladder adds Incineroar (#7; sheet P=19%, 81 teams), Basculegion (#8; sheet P=18%, 79 teams). Sheet adds Politoed (P=26%, 92 teams; Politoed's own ladder list includes Golisopod), Grimmsnarl (P=25%, 95 teams; Grimmsnarl's own ladder list includes Golisopod).
- Garchomp 6/8 shared. Ladder adds Gholdengo (#6; sheet P=17%, 79 teams), Whimsicott (#8; sheet P=13%, 56 teams). Sheet adds Farigiraf (P=25%, 109 teams; Farigiraf's own ladder list includes Garchomp), Sylveon (P=19%, 80 teams; Sylveon's own ladder list does not include Garchomp).
- Gholdengo 7/8 shared. Ladder adds Garchomp (#8; sheet P=10%, 79 teams). Sheet adds Staraptor (P=32%, 246 teams; Staraptor's own ladder list includes Gholdengo).
- Archaludon 7/8 shared. Ladder adds Swampert (#5; sheet P=24%, 99 teams). Sheet adds Farigiraf (P=29%, 108 teams; Farigiraf's own ladder list includes Archaludon).
- Pelipper 8/8 shared.
- Milotic 5/8 shared. Ladder adds Incineroar (#5; sheet P=13%, 67 teams), Indeedee-F (#7; sheet P=21%, 92 teams), Excadrill (#8; sheet P=19%, 99 teams). Sheet adds Raichu (P=31%, 131 teams; Raichu's own ladder list does not include Milotic), Staraptor (P=30%, 120 teams; Staraptor's own ladder list does not include Milotic), Arcanine-Hisui (P=23%, 110 teams; Arcanine-Hisui's own ladder list does not include Milotic).
- Farigiraf 7/8 shared. Ladder adds Pelipper (#7; sheet P=16%, 62 teams). Sheet adds Charizard (P=30%, 116 teams; Charizard's own ladder list does not include Farigiraf).
- Charizard 4/8 shared. Ladder adds Rillaboom (#3; sheet P=20%, 75 teams), Whimsicott (#5; sheet P=14%, 48 teams), Basculegion (#7; sheet P=12%, 43 teams), Incineroar (#8; sheet P=21%, 79 teams). Sheet adds Farigiraf (P=35%, 116 teams; Farigiraf's own ladder list does not include Charizard), Grimmsnarl (P=28%, 96 teams; Grimmsnarl's own ladder list includes Charizard), Venusaur (P=26%, 92 teams; Venusaur's own ladder list includes Charizard), Sylveon (P=23%, 79 teams; Sylveon's own ladder list does not include Charizard).
- Gardevoir 7/8 shared. Ladder adds Torkoal (#6; sheet P=13%, 35 teams). Sheet adds Whimsicott (P=15%, 31 teams; Whimsicott's own ladder list does not include Gardevoir).
- Raichu 7/8 shared. Ladder adds Garchomp (#8; sheet P=8%, 55 teams). Sheet adds Salamence (P=28%, 208 teams; Salamence's own ladder list does not include Raichu).
- Arcanine-Hisui 7/8 shared. Ladder adds Basculegion (#8; sheet P=8%, 57 teams). Sheet adds Staraptor (P=30%, 172 teams; Staraptor's own ladder list includes Arcanine-Hisui).
- Sylveon 7/8 shared. Ladder adds Salamence (#4; sheet P=23%, 88 teams). Sheet adds Garchomp (P=25%, 80 teams; Garchomp's own ladder list does not include Sylveon).
- Tyranitar 8/8 shared.
- Whimsicott 5/8 shared. Ladder adds Sneasler (#4; sheet P=16%, 27 teams), Rillaboom (#6; sheet P=13%, 20 teams), Staraptor (#8; sheet P=19%, 30 teams). Sheet adds Indeedee-F (P=30%, 47 teams; Indeedee-F's own ladder list does not include Whimsicott), Glimmora (P=20%, 33 teams; Glimmora's own ladder list includes Whimsicott), Gardevoir (P=20%, 31 teams; Gardevoir's own ladder list does not include Whimsicott).
- Armarouge 6/8 shared. Ladder adds Torkoal (#6; sheet P=15%, 31 teams), Salamence (#7; sheet P=18%, 36 teams). Sheet adds Staraptor (P=33%, 55 teams; Staraptor's own ladder list does not include Armarouge), Dragapult (P=22%, 36 teams; Dragapult's own ladder list does not include Armarouge).
- Staraptor 6/8 shared. Ladder adds Kingambit (#6; sheet P=8%, 30 teams), Whimsicott (#8; sheet P=8%, 30 teams). Sheet adds Milotic (P=36%, 120 teams; Milotic's own ladder list does not include Staraptor), Ceruledge (P=16%, 53 teams; Ceruledge's own ladder list includes Staraptor).
- Torkoal 6/8 shared. Ladder adds Golisopod (#7; sheet P=10%, 10 teams), Incineroar (#8; sheet P=18%, 16 teams). Sheet adds Rillaboom (P=21%, 17 teams; Rillaboom's own ladder list does not include Torkoal), Hatterene (P=19%, 19 teams; Hatterene's own ladder list includes Torkoal).
- Metagross 7/8 shared. Ladder adds Incineroar (#3; sheet P=22%, 38 teams). Sheet adds Indeedee (P=24%, 36 teams; Indeedee's own ladder list includes Metagross).
- Indeedee 7/8 shared. Ladder adds Kingambit (#8; sheet P=6%, 12 teams). Sheet adds Arcanine-Hisui (P=16%, 31 teams; Arcanine-Hisui's own ladder list does not include Indeedee).
- Sinistcha 5/8 shared. Ladder adds Archaludon (#2; sheet P=16%, 24 teams), Pelipper (#3; sheet P=16%, 25 teams), Tyranitar (#7; sheet P=15%, 23 teams). Sheet adds Delphox (P=47%, 55 teams; Delphox's own ladder list includes Sinistcha), Floette-Eternal (P=31%, 38 teams; Floette-Eternal's own ladder list does not include Sinistcha), Blastoise (P=17%, 20 teams; Blastoise's own ladder list includes Sinistcha).
- Floette-Eternal 8/8 shared.
- Excadrill 8/8 shared.
- Volcarona 7/8 shared. Ladder adds Gholdengo (#6; sheet P=16%, 30 teams). Sheet adds Basculegion (P=31%, 62 teams; Basculegion's own ladder list does not include Volcarona).
- Politoed 8/8 shared.
- Lucario 8/8 shared.
- Grimmsnarl 6/8 shared. Ladder adds Rillaboom (#6; sheet P=11%, 18 teams), Sinistcha (#8; sheet P=6%, 11 teams). Sheet adds Politoed (P=35%, 47 teams; Politoed's own ladder list includes Grimmsnarl), Venusaur (P=26%, 39 teams; Venusaur's own ladder list includes Grimmsnarl).
- Swampert 6/8 shared. Ladder adds Sinistcha (#7; sheet P=9%, 14 teams), Sneasler (#8; sheet P=11%, 14 teams). Sheet adds Farigiraf (P=21%, 24 teams; Farigiraf's own ladder list does not include Swampert), Charizard (P=21%, 25 teams; Charizard's own ladder list does not include Swampert).
- Froslass 6/8 shared. Ladder adds Archaludon (#6; sheet P=8%, 15 teams), Garchomp (#8; sheet P=9%, 16 teams). Sheet adds Raichu (P=34%, 51 teams; Raichu's own ladder list does not include Froslass), Salamence (P=20%, 40 teams; Salamence's own ladder list does not include Froslass).
- Baxcalibur 7/8 shared. Ladder adds Gholdengo (#6; sheet P=16%, 12 teams). Sheet adds Volcarona (P=38%, 22 teams; Volcarona's own ladder list does not include Baxcalibur).
- Gengar 6/8 shared. Ladder adds Froslass (#5; sheet P=7%, 13 teams), Sneasler (#8; sheet P=16%, 24 teams). Sheet adds Milotic (P=19%, 27 teams; Milotic's own ladder list does not include Gengar), Kingambit (P=18%, 30 teams; Kingambit's own ladder list does not include Gengar).
- Ninetales-Alola 7/8 shared. Ladder adds Milotic (#5; sheet P=13%, 8 teams). Sheet adds Kommo-o (P=16%, 8 teams; Kommo-o's own ladder list does not include Ninetales-Alola).
- Dragonite 6/8 shared. Ladder adds Archaludon (#6; sheet P=10%, 8 teams), Pelipper (#7; sheet P=9%, 7 teams). Sheet adds Floette-Eternal (P=29%, 22 teams; Floette-Eternal's own ladder list does not include Dragonite), Arcanine-Hisui (P=14%, 12 teams; Arcanine-Hisui's own ladder list does not include Dragonite).
- Glimmora 7/8 shared. Ladder adds Golisopod (#8; sheet P=8%, 11 teams). Sheet adds Garchomp (P=27%, 32 teams; Garchomp's own ladder list does not include Glimmora).
- Pawmot 6/8 shared. Ladder adds Politoed (#7; sheet P=11%, 6 teams), Indeedee-F (#8; sheet P=16%, 8 teams). Sheet adds Basculegion (P=23%, 15 teams; Basculegion's own ladder list does not include Pawmot), Gholdengo (P=16%, 9 teams; Gholdengo's own ladder list does not include Pawmot).
- Aerodactyl 7/8 shared. Ladder adds Sneasler (#6; sheet P=12%, 13 teams). Sheet adds Gholdengo (P=15%, 14 teams; Gholdengo's own ladder list does not include Aerodactyl).
- Venusaur 7/8 shared. Ladder adds Basculegion (#7; sheet P=10%, 13 teams). Sheet adds Swampert (P=24%, 24 teams; Swampert's own ladder list does not include Venusaur).
- Primarina 5/8 shared. Ladder adds Golisopod (#6; sheet P=12%, 9 teams), Indeedee-F (#7; <4 sheet teams), Garchomp (#8; sheet P=9%, 6 teams). Sheet adds Raichu (P=41%, 27 teams; Raichu's own ladder list does not include Primarina), Gholdengo (P=33%, 24 teams; Gholdengo's own ladder list does not include Primarina), Arcanine-Hisui (P=26%, 17 teams; Arcanine-Hisui's own ladder list does not include Primarina).
- Sableye 7/8 shared. Ladder adds Sneasler (#8; <4 sheet teams). Sheet adds Farigiraf (P=20%, 6 teams; Farigiraf's own ladder list does not include Sableye).
- Hatterene 8/8 shared.
- Blastoise 6/8 shared. Ladder adds Farigiraf (#5; sheet P=10%, 5 teams), Pelipper (#8; sheet P=10%, 4 teams). Sheet adds Delphox (P=40%, 17 teams; Delphox's own ladder list does not include Blastoise), Maushold (P=31%, 13 teams; Maushold's own ladder list does not include Blastoise).
- Delphox 6/8 shared. Ladder adds Garchomp (#6; sheet P=15%, 18 teams), Whimsicott (#8; sheet P=6%, 7 teams). Sheet adds Floette-Eternal (P=43%, 48 teams; Floette-Eternal's own ladder list does not include Delphox), Blastoise (P=16%, 17 teams; Blastoise's own ladder list does not include Delphox).
- Annihilape 6/8 shared. Ladder adds Incineroar (#3; sheet P=16%, 9 teams), Pelipper (#6; sheet P=18%, 9 teams). Sheet adds Gardevoir (P=22%, 11 teams; Gardevoir's own ladder list does not include Annihilape), Sneasler (P=20%, 10 teams; Sneasler's own ladder list does not include Annihilape).
- Absol 7/8 shared. Ladder adds Milotic (#4; sheet P=13%, 6 teams). Sheet adds Floette-Eternal (P=20%, 8 teams; Floette-Eternal's own ladder list does not include Absol).
- Corviknight 7/8 shared. Ladder adds Rillaboom (#8; <4 sheet teams). Sheet adds Raichu (P=10%, 7 teams; Raichu's own ladder list does not include Corviknight).
- Maushold 5/8 shared. Ladder adds Gardevoir (#6; <4 sheet teams), Annihilape (#7; <4 sheet teams), Archaludon (#8; <4 sheet teams). Sheet adds Delphox (P=42%, 16 teams; Delphox's own ladder list does not include Maushold), Blastoise (P=33%, 13 teams; Blastoise's own ladder list does not include Maushold), Floette-Eternal (P=17%, 7 teams; Floette-Eternal's own ladder list does not include Maushold).
- Talonflame 5/8 shared. Ladder adds Kingambit (#6; sheet P=20%, 8 teams), Gardevoir (#7; sheet P=19%, 8 teams), Milotic (#8; sheet P=12%, 4 teams). Sheet adds Raichu (P=25%, 7 teams; Raichu's own ladder list does not include Talonflame), Glimmora (P=23%, 9 teams; Glimmora's own ladder list does not include Talonflame), Gholdengo (P=22%, 7 teams; Gholdengo's own ladder list does not include Talonflame).
- Kommo-o 7/8 shared. Ladder adds Sneasler (#6; sheet P=13%, 14 teams). Sheet adds Whimsicott (P=24%, 27 teams; Whimsicott's own ladder list does not include Kommo-o).
- Ceruledge 7/8 shared. Ladder adds Indeedee-F (#8; <4 sheet teams). Sheet adds Kingambit (P=13%, 12 teams; Kingambit's own ladder list does not include Ceruledge).
- Dragapult 5/8 shared. Ladder adds Rillaboom (#4; sheet P=18%, 15 teams), Sneasler (#6; sheet P=15%, 14 teams), Incineroar (#8; sheet P=10%, 10 teams). Sheet adds Armarouge (P=44%, 36 teams; Armarouge's own ladder list does not include Dragapult), Golisopod (P=21%, 18 teams; Golisopod's own ladder list does not include Dragapult), Gengar (P=18%, 14 teams; Gengar's own ladder list does not include Dragapult).
- Camerupt 7/8 shared. Ladder adds Sylveon (#6; sheet P=10%, 6 teams). Sheet adds Armarouge (P=14%, 10 teams; Armarouge's own ladder list does not include Camerupt).
- Hydreigon 4/8 shared. Ladder adds Incineroar (#5; sheet P=10%, 4 teams), Charizard (#6; sheet P=16%, 7 teams), Golisopod (#7; sheet P=15%, 8 teams), Salamence (#8; sheet P=13%, 5 teams). Sheet adds Milotic (P=23%, 9 teams; Milotic's own ladder list does not include Hydreigon), Raichu (P=22%, 8 teams; Raichu's own ladder list does not include Hydreigon), Gholdengo (P=22%, 8 teams; Gholdengo's own ladder list does not include Hydreigon), Arcanine-Hisui (P=19%, 6 teams; Arcanine-Hisui's own ladder list does not include Hydreigon).
- Blaziken 6/8 shared. Ladder adds Metagross (#4; sheet P=14%, 8 teams), Basculegion (#7; sheet P=17%, 8 teams). Sheet adds Torkoal (P=23%, 11 teams; Torkoal's own ladder list does not include Blaziken), Gholdengo (P=19%, 9 teams; Gholdengo's own ladder list does not include Blaziken).
- Mawile 6/8 shared. Ladder adds Armarouge (#6; sheet P=18%, 6 teams), Hatterene (#8; <4 sheet teams). Sheet adds Salamence (P=23%, 5 teams; Salamence's own ladder list does not include Mawile), Pelipper (P=18%, 4 teams; Pelipper's own ladder list does not include Mawile).

### 12.4 Ladder-calibrated view (λ = 1)
A model built from the ladder's rank order, not a ladder usage share: among the sheet's species nodes with a ladder entry, the i-th in the ladder order takes the i-th highest sheet support as its rank-matched support, and each team's weight is multiplied once by the geometric mean over its species of (rank-matched support / sheet support)^λ. The sheet's communities, cores, labels, and assignments are unchanged; a species thin or absent on the sheet (outside its species nodes) can't be reweighted. Unlike the coverage table (§12.1, the ladder's top 60), this view reweights every sheet species node the ladder lists, at any rank.
Calibrated support need not land on its rank-matched support: a species' factor is averaged with its teammates' in each team's multiplier and every share is renormalised, so it usually moves only part of the way, and can stop short, overshoot, or even move against its own factor; λ = 1 does not mean the ladder is matched. Mapping the i-th in the ladder order to the i-th sheet support assumes the ladder's usage curve has the sheet's shape; where sheet supports are compressed, one or two ranks swing a factor a lot. Where the two orders agree the factor is exactly one by construction, which does not mean equal usage; such a species still moves through its teammates and the renormalisation. Reweighting scales the sheet's own teams: it cannot add a ladder build the sheet lacks, so it corrects how much of each sheet archetype appears, not which archetypes exist, and sub-community labels keep the sheet's items.
Effective teams (Kish): 2701.46 under the sheet weights, 2638.49 calibrated; team weight multiplier 0.57 to 1.82.

#### Communities
| Community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Rillaboom / Raichu / Gholdengo | 33.9% | 28.5% | 13.5% | 13.1% |
| 1 | Sneasler / Salamence | 19.2% | 19.3% | 15.1% | 15.3% |
| 2 | Setup (Mega Floette) | 8.0% | 7.8% | 7.5% | 7.5% |
| 3 | Psyspam | 16.2% | 19.0% | 5.5% | 6.1% |
| 4 | Rain | 22.5% | 25.1% | 4.6% | 4.9% |
| 5 | Pincurchin / Raichu-Alola | 0.0% | 0.0% | 0.0% | 0.0% |
| — | Unassigned | 0.3% | 0.3% | — | — |

#### Sub-communities of Community 0: Rillaboom / Raichu / Gholdengo (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Rillaboom / Raichu@Raichunite Y / Gholdengo | 67.2% | 63.8% | 13.1% | 13.9% |
| 1 | Kingambit + Sneasler/Salamence@Salamencite | 8.0% | 8.6% | 10.6% | 10.8% |
| 2 | Setup | 22.1% | 24.5% | 6.0% | 6.7% |
| 3 | Golisopod@Golisopite / Farigiraf@Grassy Seed | 0.3% | 0.4% | 1.5% | 1.8% |
| 4 | Volcarona / Glimmora@Glimmoranite | 0.9% | 1.0% | 1.1% | 1.1% |
| 5 | Perish Trap (Mega Gengar) | 0.7% | 0.8% | 0.7% | 0.7% |
| 6 | Garchomp / Whimsicott | 0.0% | 0.0% | 0.2% | 0.2% |
| 7 | Archaludon / Pelipper | 0.2% | 0.3% | 0.2% | 0.3% |
| — | Unassigned | 0.5% | 0.7% | — | — |

#### Sub-communities of Community 1: Sneasler / Salamence (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Sand (Mega Tyranitar, Mega Salamence) | 43.0% | 41.9% | 6.6% | 6.6% |
| 1 | Kingambit / Rillaboom | 38.8% | 39.2% | 2.4% | 2.6% |
| 2 | Psyspam (Mega Salamence) | 17.0% | 17.9% | 2.5% | 2.6% |
| 3 | Farigiraf / Torkoal | 0.0% | 0.0% | 0.7% | 0.7% |
| — | Unassigned | 1.2% | 1.1% | — | — |

#### Sub-communities of Community 2: Setup (Mega Floette) (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Setup (Mega Floette) · Incineroar | 43.5% | 42.9% | 15.3% | 15.3% |
| 1 | Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 17.5% | 19.4% | 16.1% | 16.2% |
| 2 | Setup (Mega Delphox, Mega Floette) | 35.3% | 34.0% | 8.9% | 8.8% |
| 3 | Gengar@Gengarite / Kommo-o | 1.3% | 1.2% | 2.4% | 2.3% |
| 4 | Absol@Absolite Z / Espathra / Goodra-Hisui | 2.1% | 2.0% | 0.0% | 0.0% |
| — | Unassigned | 0.4% | 0.4% | — | — |

#### Sub-communities of Community 3: Psyspam (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Psyspam (Mega Gardevoir) | 41.4% | 46.1% | 6.1% | 6.0% |
| 1 | Psyspam (Mega Staraptor) | 15.7% | 12.8% | 3.6% | 3.7% |
| 2 | Trick Room Psyspam (Mega Camerupt) | 16.7% | 16.7% | 1.8% | 2.1% |
| 3 | Psyspam · Whimsicott | 11.9% | 10.8% | 1.3% | 1.2% |
| 4 | Rillaboom / Glimmora@Glimmoranite | 12.2% | 11.5% | 2.1% | 2.0% |
| 5 | Blastoise@Blastoisinite / Sinistcha | 0.1% | 0.1% | 0.2% | 0.2% |
| — | Unassigned | 2.0% | 2.1% | — | — |

#### Sub-communities of Community 4: Rain (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Rain (Mega Golisopod) | 34.4% | 37.1% | 16.3% | 16.3% |
| 1 | Trick Room (Mega Golisopod) | 18.3% | 18.6% | 16.9% | 17.4% |
| 2 | Sun (Mega Charizard-Y) | 30.0% | 29.6% | 8.6% | 8.7% |
| 3 | Rain Perish Trap (Mega Gengar) | 16.2% | 13.7% | 2.4% | 2.6% |
| 4 | Aerodactyl / Tsareena | 0.1% | 0.1% | 0.2% | 0.2% |
| — | Unassigned | 0.9% | 0.9% | — | — |

#### Species
| Median rank | Species | Sheet support | Rank-matched support | Factor | Calibrated support |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Rillaboom | 54.7% | 54.7% | 1.00 | 49.8% |
| 2 | Sneasler | 41.5% | 41.5% | 1.00 | 42.0% |
| 3 | Salamence | 30.2% | 30.2% | 1.00 | 29.3% |
| 4 | Incineroar | 29.1% | 29.1% | 1.00 | 28.8% |
| 5 | Indeedee-F | 18.2% | 28.8% | 1.59 | 21.5% |
| 6 | Kingambit | 24.5% | 25.7% | 1.05 | 25.0% |
| 7 | Basculegion | 15.7% | 24.5% | 1.56 | 18.0% |
| 8 | Golisopod | 13.4% | 21.1% | 1.57 | 15.9% |
| 9 | Garchomp | 15.8% | 18.2% | 1.15 | 16.8% |
| 10 | Gholdengo | 28.8% | 16.3% | 0.57 | 23.7% |
| 11 | Archaludon | 14.4% | 15.8% | 1.10 | 16.1% |
| 11 | Pelipper | 10.8% | 15.7% | 1.45 | 13.1% |
| 13 | Milotic | 16.3% | 14.4% | 0.88 | 14.8% |
| 14 | Farigiraf | 14.4% | 14.4% | 1.00 | 15.7% |
| 15 | Charizard | 12.4% | 13.4% | 1.08 | 13.8% |
| 16 | Gardevoir | 7.3% | 13.4% | 1.83 | 9.4% |
| 17 | Raichu | 25.7% | 12.4% | 0.48 | 20.6% |
| 18 | Arcanine-Hisui | 21.1% | 12.2% | 0.58 | 17.5% |
| 19 | Sylveon | 11.8% | 11.8% | 1.00 | 10.6% |
| 20 | Tyranitar | 9.2% | 10.8% | 1.18 | 9.1% |
| 21 | Whimsicott | 5.5% | 9.2% | 1.67 | 6.1% |
| 22 | Armarouge | 6.3% | 7.5% | 1.19 | 7.4% |
| 23 | Staraptor | 13.4% | 7.3% | 0.55 | 10.7% |
| 24 | Torkoal | 3.1% | 7.2% | 2.33 | 4.1% |
| 26 | Metagross | 5.4% | 6.8% | 1.26 | 5.8% |
| 26 | Indeedee | 6.7% | 6.7% | 1.00 | 6.7% |
| 26 | Sinistcha | 4.6% | 6.3% | 1.36 | 4.9% |
| 27 | Floette-Eternal | 12.2% | 6.0% | 0.49 | 11.0% |
| 29 | Excadrill | 7.5% | 5.7% | 0.76 | 7.3% |
| 30 | Volcarona | 7.2% | 5.5% | 0.77 | 7.0% |
| 31 | Politoed | 6.8% | 5.4% | 0.80 | 7.0% |
| 32 | Lucario | 2.5% | 5.4% | 2.15 | 3.1% |
| 33 | Grimmsnarl | 5.4% | 4.6% | 0.86 | 6.0% |
| 34 | Swampert | 4.3% | 4.4% | 1.02 | 4.9% |
| 35 | Froslass | 6.0% | 4.3% | 0.72 | 5.4% |
| 36 | Baxcalibur | 2.0% | 4.3% | 2.17 | 2.3% |
| 37 | Gengar | 5.7% | 4.1% | 0.73 | 5.3% |
| 38 | Ninetales-Alola | 1.9% | 3.6% | 1.89 | 2.2% |
| 40 | Dragonite | 3.1% | 3.2% | 1.03 | 3.1% |
| 40 | Glimmora | 4.3% | 3.1% | 0.73 | 4.3% |
| 41 | Pawmot | 1.7% | 3.1% | 1.82 | 2.1% |
| 42 | Aerodactyl | 3.1% | 3.1% | 1.00 | 3.4% |
| 42 | Venusaur | 3.6% | 3.1% | 0.85 | 4.1% |
| 43 | Primarina | 2.6% | 2.8% | 1.06 | 2.6% |
| 45 | Sableye | 1.1% | 2.6% | 2.34 | 1.4% |
| 46 | Hatterene | 2.0% | 2.5% | 1.27 | 2.4% |
| 47 | Blastoise | 1.7% | 2.4% | 1.43 | 2.0% |
| 49 | Delphox | 4.4% | 2.0% | 0.45 | 4.1% |
| 49 | Annihilape | 2.0% | 2.0% | 1.00 | 2.2% |
| 50 | Absol | 1.5% | 2.0% | 1.31 | 1.6% |
| 51 | Corviknight | 2.8% | 1.9% | 0.68 | 2.7% |
| 52 | Maushold | 1.6% | 1.7% | 1.06 | 1.7% |
| 53 | Talonflame | 1.1% | 1.7% | 1.55 | 1.3% |
| 54 | Kommo-o | 4.1% | 1.7% | 0.40 | 3.8% |
| 55 | Ceruledge | 3.1% | 1.6% | 0.52 | 2.3% |
| 56 | Dragapult | 3.2% | 1.5% | 0.47 | 2.9% |
| 57 | Camerupt | 2.4% | 1.5% | 0.60 | 2.6% |
| 58 | Hydreigon | 1.3% | 1.3% | 1.00 | 1.3% |
| 59 | Blaziken | 1.7% | 1.1% | 0.68 | 1.7% |
| 60 | Mawile | 0.9% | 1.1% | 1.20 | 1.1% |
| 61 | Typhlosion-Hisui | 0.7% | 0.9% | 1.27 | 0.9% |
| 62 | Sirfetch’d | 0.5% | 0.9% | 1.59 | 0.6% |
| 63 | Gallade | 0.5% | 0.8% | 1.64 | 0.7% |
| 65 | Vivillon | 1.5% | 0.8% | 0.55 | 1.3% |
| 66 | Zoroark-Hisui | 0.3% | 0.7% | 2.28 | 0.4% |
| 67 | Tsareena | 0.5% | 0.7% | 1.47 | 0.5% |
| 68 | Rotom-Wash | 0.5% | 0.7% | 1.45 | 0.6% |
| 69 | Kleavor | 0.9% | 0.6% | 0.72 | 0.9% |
| 70 | Meowscarada | 0.3% | 0.6% | 1.71 | 0.4% |
| 71 | Alakazam | 0.2% | 0.6% | 3.14 | 0.3% |
| 72 | Empoleon | 0.5% | 0.5% | 1.01 | 0.6% |
| 73 | Scovillain | 0.8% | 0.5% | 0.65 | 0.8% |
| 74 | Weavile | 0.4% | 0.5% | 1.40 | 0.4% |
| 75 | Espathra | 0.7% | 0.5% | 0.74 | 0.7% |
| 76 | Chandelure | 0.3% | 0.5% | 1.49 | 0.4% |
| 78 | Pincurchin | 0.2% | 0.5% | 2.28 | 0.3% |
| 78 | Aegislash | 0.4% | 0.4% | 1.06 | 0.4% |
| 79 | Toxtricity | 0.3% | 0.4% | 1.13 | 0.4% |
| 80 | Scizor | 0.2% | 0.4% | 1.64 | 0.3% |
| 81 | Toxapex | 0.6% | 0.3% | 0.58 | 0.5% |
| 82 | Malamar | 0.1% | 0.3% | 4.36 | 0.1% |
| 83 | Clefable | 0.2% | 0.3% | 1.53 | 0.3% |
| 84 | Cinderace | 0.1% | 0.3% | 4.52 | 0.1% |
| 85 | Mimikyu | 0.2% | 0.3% | 1.66 | 0.2% |
| 86 | Meganium | 0.5% | 0.3% | 0.62 | 0.6% |
| 87 | Mamoswine | 0.3% | 0.3% | 0.96 | 0.3% |
| 89 | Kangaskhan | 0.2% | 0.3% | 1.39 | 0.3% |
| 89 | Goodra-Hisui | 0.3% | 0.3% | 1.00 | 0.3% |
| 90 | Greninja | 0.2% | 0.3% | 1.94 | 0.2% |
| 93 | Grapploct | 0.3% | 0.2% | 0.67 | 0.4% |
| 94 | Basculegion-F | 0.1% | 0.2% | 2.53 | 0.1% |
| 95 | Overqwil | 0.2% | 0.2% | 1.16 | 0.2% |
| 96 | Gyarados | 0.2% | 0.2% | 1.38 | 0.2% |
| 96 | Scrafty | 0.2% | 0.2% | 1.27 | 0.2% |
| 98 | Rotom-Heat | 0.7% | 0.2% | 0.30 | 0.6% |
| 99 | Lycanroc-Dusk | 0.6% | 0.2% | 0.35 | 0.5% |
| 101 | Araquanid | 0.2% | 0.2% | 0.89 | 0.2% |
| 103 | Drampa | 0.2% | 0.2% | 1.06 | 0.2% |
| 104 | Bellibolt | 0.1% | 0.2% | 1.44 | 0.2% |
| 105 | Pyroar | 0.8% | 0.2% | 0.25 | 0.8% |
| 106 | Tinkaton | 0.2% | 0.2% | 1.02 | 0.2% |
| 108 | Klefki | 0.3% | 0.2% | 0.58 | 0.3% |
| 108 | Vanilluxe | 0.4% | 0.2% | 0.46 | 0.4% |
| 110 | Sceptile | 0.1% | 0.2% | 1.47 | 0.1% |
| 112 | Ninetales | 0.1% | 0.2% | 1.27 | 0.2% |
| 113 | Arcanine | 0.2% | 0.2% | 1.00 | 0.2% |
| 114 | Altaria | 0.6% | 0.2% | 0.28 | 0.5% |
| 114 | Starmie | 0.2% | 0.2% | 0.95 | 0.2% |
| 116 | Lopunny | 0.3% | 0.2% | 0.51 | 0.3% |
| 118 | Jolteon | 0.1% | 0.2% | 1.59 | 0.1% |
| 120 | Aggron | 0.1% | 0.2% | 1.52 | 0.1% |
| 122 | Clawitzer | 0.1% | 0.2% | 2.01 | 0.1% |
| 122 | Crabominable | 0.2% | 0.1% | 0.83 | 0.2% |
| 123 | Meowstic-F | 0.3% | 0.1% | 0.47 | 0.3% |
| 128 | Abomasnow | 0.2% | 0.1% | 0.70 | 0.2% |
| 129 | Ampharos | 0.2% | 0.1% | 0.68 | 0.2% |
| 130 | Steelix | 0.1% | 0.1% | 0.96 | 0.2% |
| 131 | Raichu-Alola | 0.1% | 0.1% | 1.13 | 0.2% |
| 132 | Azumarill | 0.1% | 0.1% | 1.03 | 0.1% |
| 134 | Gliscor | 0.2% | 0.1% | 0.81 | 0.2% |
| 138 | Ditto | 0.2% | 0.1% | 0.68 | 0.2% |
| 139 | Houndoom | 0.1% | 0.1% | 0.85 | 0.2% |
| 143 | Palafin | 0.1% | 0.1% | 0.91 | 0.1% |
| 143 | Dragalge | 0.1% | 0.1% | 1.04 | 0.1% |
| 145 | Noivern | 0.1% | 0.1% | 0.98 | 0.1% |
| 148 | Manectric | 0.2% | 0.1% | 0.63 | 0.2% |
| 151 | Krookodile | 0.1% | 0.1% | 1.01 | 0.1% |
| 152 | Feraligatr | 0.1% | 0.1% | 1.01 | 0.1% |
| 156 | Hippowdon | 0.2% | 0.1% | 0.50 | 0.2% |
| 158 | Persian-Alola | 0.1% | 0.1% | 0.99 | 0.1% |
| 162 | Cofagrigus | 0.1% | 0.1% | 0.80 | 0.1% |
| 168 | Torterra | 0.1% | 0.1% | 0.74 | 0.1% |
| 172 | Heracross | 0.2% | 0.1% | 0.42 | 0.2% |
| 177 | Runerigus | 0.1% | 0.1% | 0.62 | 0.1% |
| 184 | Tauros-Paldea-Aqua | 0.1% | 0.1% | 0.68 | 0.1% |
| 188 | Pidgeot | 0.1% | 0.1% | 0.72 | 0.1% |
