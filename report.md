# Meta-graph report: Regulation M-C

## Reading this report
- **Team weight**: each team counts as placement × recency × variant split. Placement gives `placementTopWeight` at or better than rank `placementTopRank`, `placementMidWeight` at or better than rank `placementMidRank`, and `placementDefaultWeight` otherwise (no rank, or a ladder-peak rank). Recency halves every `recencyHalfLifeDays` days before the as-of date. Teams linked by `variantOf` form one variant family and share one team's weight.
- **Support**: the share of the total team weight carried by the teams that contain a species, a species@item, a pair, or a triple.
- **Lift**: a pair's support divided by the product of its two sides' supports. It is one when the two appear together exactly as often as chance predicts, and higher when they appear together more often. Lift cannot exceed one over the more common side's support, so a pair with a very common species always has a low lift; the normalized lift divides lift by that ceiling.
- **Confidence**: Conf(B|A) is the pair's support divided by A's support, the share of A's team weight whose teams also contain B.
- **Core**: a pair (two species, or a species@item with a species) found in at least `coreMinTeams` variant families, whose lift is at least `coreMinLift`, or exceeds `layoutMinLift` with a confidence of at least `coreMinConfidence` in either direction. Two species@item tokens never form a core.
- **Layout**: the archetype map page places each species node by a deterministic force-directed layout, from starting positions seeded by `layoutSeed`, over the species pairs with at least `minPairTeams` teams and lift above `layoutMinLift`, each weighted by its layout weight (`layoutWeightMode`); a species with no such pair keeps a place on a circle. The layout changes no figure in this report.
- **Mode tags**: each team carries a tag for what its sets set up: Sun, Rain, Sand, Snow, Psyspam, Trick Room, Tailwind, Perish Trap, Screens, Setup. A weather: a set whose ability on entry (its own, or its Mega's when it holds its stone) sets it. Psyspam: a set whose ability sets Psychic Terrain, plus an Expanding Force user. Trick Room: a Trick Room setter and another set that is a Trick Room abuser (its Speed in battle at or below `trickRoomAbuserMaxSpeed`: from its stat points whatever its nature, or, for a set that publishes none, only when its nature lowers Speed). Tailwind: a Tailwind user. Perish Trap: a Perish Song user, plus a Shadow Tag holder or a Mean Look or Block user. Screens: a Light Clay holder (the item alone), or a set with both Reflect and Light Screen. Setup: at least `setupModeMinSets` sets with a setup move. Its Megas are the formes its stones resolve to.
- **Sheet vs. ladder**: the tournament sample compared with the ladder's daily snapshots dated from the window's start to the as-of date and recorded under this regulation. A species is on the ladder when at least half of those snapshots list it; its median rank is the lower median of its ranks in the snapshots that list it, and the ladder order is by median rank, then rank in the latest snapshot, then name. Over the ladder's top `ladderTopSpecies` species in that order: the ladder items (latest snapshot) held by at least `ladderItemMinShare` of a species' sets on the ladder, more than in the sheet, on fewer than `minNodeTeams` sheet teams; the species the two rank most differently (each list capped at `ladderBiasListSize`); and each species' ladder teammate list (latest snapshot) against its sheet partners by P(B|A). Since the ladder gives usage and teammates as ranks and lists with no shares, these comparisons are by rank or list membership only.
- **Definition**: up to three Pokémon (a Mega counts as its Pokémon), mode tags and Megas, mined from team_query's records for the same as-of date and window; a team is in a definition exactly when it carries all of them, as team_query matches, every copy of a roster counting. Candidates start from species pairs found together more often than chance (lift at least `coreMinLift`), species triples whose lift over each of their pairs and its third member is at least `coreMinLift`, mode tags, and a mode tag with a Mega whose share among that tag's teams is at least `coreMinLift` times its share of the window; each candidate needs at least `definitionMinCoverage` of the window's teams (the floor). Each is then extended one element at a time by the mode tag, Mega or species on the most of its teams, when that element is on at least `definitionExtendShare` of them, its lift over the window is at least `coreMinLift`, and the extended candidate keeps the floor; no new Pokémon joins past three, and a Mega whose Pokémon is already required replaces it. Candidates are ranked by weighted share (the placement tier weights alone), then teams, distinct rosters, fewer elements, and key. One with fewer than `definitionMinRosters` distinct rosters is refused, as is one on at least `definitionMaxCoverage` of the window (its refinements stay eligible). One of Pokémon alone (no mode tag and no Mega) is refused as a staple unless its lift is at least `definitionBareMinLift` (a pair's lift; a triple's least lift over each of its pairs and its third member): Pokémon found together on many teams but not much more often than chance are not an archetype by themselves; the staples on at least `stapleMinShare` of the window's teams are listed, the rest counted. In rank order, a candidate with at least `definitionMinOwnShare` of its teams new (in no related definition admitted before it: one sharing a Pokémon, a Mega counting as its Pokémon, or a mode tag) is admitted, until there are `definitionMax`; any other becomes the variant of an admitted definition whose every Pokémon, mode tag and Mega it has (at most `definitionVariants` each), or is refused as an overlap, listing the related definitions sharing the most of its teams. Once the set is complete, of the definitions with less than `definitionMinOwnShare` of their teams outside every other related definition (above or below them), the one with the least (the lower-ranked on a tie) is refused as absorbed, listing the related definitions sharing the most of its teams, and admission is redone from the start without it, until a round absorbs none. A definition's name is its mode names, then its Megas and other Pokémon by window teams, joined by +. A definition's own teams are in no other definition; each team of the window is exclusive (in one definition), hybrid (in two or more) or uncovered (in none), a variant counting as its parent. Recent and earlier are its shares of the teams dated in the window's last `definitionRecentDays` days and of the rest. A definition that lists several Megas means a team runs one of them (one Mega per team); modes listed together are the team's options, not simultaneous conditions.
- **Ladder-calibrated view**: with `ladderBlend` above zero, the same teams are weighed a second way. Among the sheet's species nodes with a ladder entry, each takes as its rank-matched support the sheet support found at its own position in the ladder order among them, and every team's weight is multiplied once by the geometric mean over its species of (rank-matched support / sheet support) raised to `ladderBlend`; any other species counts as one. Species support and each definition's share are then shown under both weights, over the unchanged definitions. It is a model built from the ladder's rank order, not a ladder usage share: it moves shares only part of the way, and not always toward the ladder's order, since each factor is averaged with its teammates'; it cannot add a build the sheet lacks; and a species thin or absent on the sheet can't be reweighted.

## 1. Window & Applied Defaults
- Regulation: Regulation M-C
- As of: 2026-10-01, window: 22 days
- Date range: 2026-09-09 to 2026-10-01
- Teams analyzed: 2957 (total weight 1954.21)
- Ladder: 2026-09-16 to 2026-09-30, median rank over 15 daily snapshots (M6, M-C). Battle data provided by Pokémon Champions Battle Data (https://championsbattledata.com); only figures derived from these snapshots are shown.
- Placement: 330 of 2957 teams have no tournament placement and weigh placementDefaultWeight, so the placement tiers move few teams. The tiers ignore event size (a small cup's winner weighs like a large event's top cut), and Seniors teams are not separated.
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
  - layoutMinLift: 1
  - layoutWeightMode: count-lnlift
  - layoutSeed: 1
  - forceAtlas2Iterations: 200
  - trickRoomAbuserMaxSpeed: 85
  - setupModeMinSets: 2
  - topSetSignatures: 5
  - topPairsByLift: 25
  - topPairsBySupport: 25
  - topTriples: 20
  - itemSynergyLiftDelta: 0.3
  - conditionalTableSpeciesCount: 8
  - conditionalTablePartnersCount: 5
  - knownCoreChecks: Salamence+Rillaboom, Sneasler+Rillaboom, Tyranitar+Excadrill, Gardevoir+Indeedee-F
  - ladderTopSpecies: 60
  - ladderItemMinShare: 0.15
  - ladderBiasListSize: 15
  - ladderBlend: 1
  - definitionMinCoverage: 0.015
  - definitionMinRosters: 3
  - definitionMax: 25
  - definitionMaxCoverage: 0.5
  - definitionMinOwnShare: 0.3
  - definitionExtendShare: 0.6
  - definitionBareMinLift: 2
  - definitionVariants: 3
  - definitionRecentDays: 7
  - definitionOverlaps: 3
  - definitionSampleRosters: 5
  - definitionListRows: 10
  - stapleMinShare: 0.05

## 2. Known-Core Check
Species pairs A + B from knownCoreChecks, with their best species@item + species pair (highest lift) beside them: an item can carry a synergy the species-level pair does not show.
| Pair | Present | Teams | Families | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Best item-level pair | Item teams | Item lift | Conf(partner | item) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Salamence + Rillaboom | yes | 641 | 602 | 1.234 | 0.674 | 0.37 | 0.67 | yes | Rillaboom@Expert Belt + Salamence | 23 | 2.817 | 0.85 |
| Sneasler + Rillaboom | yes | 701 | 662 | 1.005 | 0.549 | 0.42 | 0.55 | no | Sneasler@Grassy Seed + Rillaboom | 423 | 1.832 | 1.00 |
| Tyranitar + Excadrill | yes | 218 | 205 | 10.601 | 0.973 | 0.97 | 0.80 | yes | Tyranitar@Tyranitarite + Excadrill | 206 | 11.441 | 0.86 |
| Gardevoir + Indeedee-F | yes | 223 | 215 | 5.333 | 0.971 | 0.39 | 0.97 | yes | Indeedee-F@Colbur Berry + Gardevoir | 94 | 7.669 | 0.56 |

## 3. Top Pairs by Lift and Support
### Species pairs by lift
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Pincurchin | Raichu-Alola | 4 | 0.1% | 489.455 | 1.000 | 1.00 | 0.55 | yes | 2 |
| Rotom-Heat | Sceptile | 4 | 0.1% | 145.428 | 1.000 | 1.00 | 0.18 | no | 7 |
| Espathra | Goodra-Hisui | 4 | 0.2% | 74.794 | 0.540 | 0.54 | 0.23 | yes | n/a |
| Araquanid | Kleavor | 4 | 0.1% | 73.332 | 0.631 | 0.17 | 0.63 | yes | n/a |
| Absol | Goodra-Hisui | 5 | 0.2% | 45.084 | 0.674 | 0.67 | 0.14 | yes | n/a |
| Lycanroc-Dusk | Scovillain | 6 | 0.2% | 39.224 | 0.324 | 0.23 | 0.32 | yes | 6 |
| Houndoom | Torkoal | 4 | 0.1% | 32.683 | 1.000 | 0.05 | 1.00 | yes | 1 |
| Abomasnow | Camerupt | 4 | 0.2% | 31.404 | 0.765 | 0.06 | 0.77 | yes | 3 |
| Camerupt | Hatterene | 33 | 1.2% | 24.846 | 0.605 | 0.61 | 0.49 | yes | 4 |
| Blastoise | Maushold | 13 | 0.5% | 18.840 | 0.328 | 0.33 | 0.30 | yes | n/a |
| Altaria | Dragapult | 12 | 0.4% | 18.655 | 0.589 | 0.12 | 0.59 | yes | 5 |
| Glimmora | Klefki | 7 | 0.3% | 18.407 | 0.779 | 0.78 | 0.06 | yes | n/a |
| Gallade | Hatterene | 5 | 0.2% | 16.229 | 0.319 | 0.08 | 0.32 | yes | n/a |
| Absol | Espathra | 4 | 0.2% | 15.562 | 0.233 | 0.23 | 0.11 | yes | n/a |
| Kommo-o | Pyroar | 14 | 0.5% | 15.346 | 0.631 | 0.63 | 0.12 | yes | n/a |
| Gengar | Vivillon | 31 | 1.2% | 14.730 | 0.840 | 0.84 | 0.21 | yes | 6 |
| Empoleon | Ninetales-Alola | 4 | 0.1% | 14.080 | 0.268 | 0.08 | 0.27 | yes | n/a |
| Aegislash | Venusaur | 6 | 0.2% | 13.705 | 0.498 | 0.05 | 0.50 | yes | n/a |
| Pyroar | Whimsicott | 16 | 0.6% | 13.420 | 0.737 | 0.11 | 0.74 | yes | 2 |
| Aerodactyl | Tsareena | 5 | 0.2% | 12.511 | 0.386 | 0.39 | 0.06 | yes | n/a |
| Politoed | Vivillon | 29 | 1.1% | 11.447 | 0.782 | 0.78 | 0.17 | yes | n/a |
| Kommo-o | Meowstic-F | 7 | 0.1% | 11.438 | 0.471 | 0.47 | 0.03 | no | n/a |
| Corviknight | Indeedee | 55 | 2.1% | 11.152 | 0.751 | 0.31 | 0.75 | yes | 5 |
| Corviknight | Excadrill | 60 | 2.3% | 11.027 | 0.830 | 0.31 | 0.83 | yes | 3 |
| Klefki | Volcarona | 7 | 0.3% | 10.934 | 0.779 | 0.04 | 0.78 | yes | n/a |

### Species pairs by support
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Gholdengo | Rillaboom | 716 | 24.4% | 1.557 | 0.849 | 0.45 | 0.85 | yes | 1 |
| Rillaboom | Sneasler | 701 | 22.8% | 1.005 | 0.549 | 0.55 | 0.42 | no | 2 |
| Raichu | Rillaboom | 623 | 22.5% | 1.607 | 0.877 | 0.41 | 0.88 | yes | 1 |
| Rillaboom | Salamence | 641 | 20.3% | 1.234 | 0.674 | 0.67 | 0.37 | yes | 3 |
| Incineroar | Rillaboom | 614 | 19.8% | 1.252 | 0.683 | 0.36 | 0.68 | yes | 1 |
| Salamence | Sneasler | 580 | 18.6% | 1.483 | 0.616 | 0.45 | 0.62 | yes | 2 |
| Arcanine-Hisui | Rillaboom | 516 | 17.8% | 1.552 | 0.847 | 0.33 | 0.85 | yes | 1 |
| Gholdengo | Raichu | 472 | 16.7% | 2.267 | 0.652 | 0.65 | 0.58 | yes | 5 |
| Kingambit | Sneasler | 421 | 13.6% | 1.332 | 0.553 | 0.33 | 0.55 | yes | 1 |
| Arcanine-Hisui | Raichu | 370 | 13.3% | 2.467 | 0.632 | 0.52 | 0.63 | yes | 5 |
| Incineroar | Sneasler | 403 | 12.9% | 1.073 | 0.446 | 0.31 | 0.45 | no | 2 |
| Kingambit | Rillaboom | 388 | 12.8% | 0.958 | 0.523 | 0.24 | 0.52 | no | 2 |
| Gholdengo | Salamence | 383 | 12.2% | 1.403 | 0.423 | 0.40 | 0.42 | yes | 2 |
| Arcanine-Hisui | Gholdengo | 348 | 11.9% | 1.974 | 0.567 | 0.41 | 0.57 | yes | 4 |
| Gholdengo | Sneasler | 335 | 10.8% | 0.904 | 0.375 | 0.26 | 0.38 | no | 3 |
| Arcanine-Hisui | Salamence | 298 | 9.7% | 1.536 | 0.463 | 0.32 | 0.46 | yes | 2 |
| Milotic | Rillaboom | 283 | 9.7% | 1.093 | 0.597 | 0.18 | 0.60 | no | 1 |
| Arcanine-Hisui | Sneasler | 282 | 9.5% | 1.091 | 0.453 | 0.23 | 0.45 | no | 3 |
| Rillaboom | Staraptor | 250 | 9.4% | 1.289 | 0.703 | 0.70 | 0.17 | yes | n/a |
| Gholdengo | Staraptor | 247 | 9.3% | 2.416 | 0.694 | 0.69 | 0.32 | yes | n/a |
| Floette-Eternal | Incineroar | 287 | 9.2% | 2.609 | 0.757 | 0.32 | 0.76 | yes | 3 |
| Floette-Eternal | Rillaboom | 285 | 9.1% | 1.366 | 0.745 | 0.17 | 0.75 | yes | 1 |
| Raichu | Staraptor | 239 | 9.0% | 2.621 | 0.671 | 0.67 | 0.35 | yes | 5 |
| Kingambit | Salamence | 285 | 8.8% | 1.194 | 0.360 | 0.29 | 0.36 | no | 3 |
| Indeedee-F | Sneasler | 270 | 8.6% | 1.133 | 0.470 | 0.21 | 0.47 | no | 1 |

### Item-level pairs by lift
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sceptile | Rotom-Heat@Choice Scarf | 4 | 0.1% | 642.607 | 1.000 | 0.81 | 1.00 | no | 2 |
| Rotom-Heat@Choice Scarf | Sceptile@Sceptilite | 4 | 0.1% | 642.607 | 1.000 | 1.00 | 0.81 | no | 7 |
| Espathra@Grassy Seed | Goodra-Hisui@Leftovers | 4 | 0.2% | 248.937 | 0.701 | 0.60 | 0.70 | no | n/a |
| Goodra-Hisui | Espathra@Grassy Seed | 4 | 0.2% | 225.195 | 0.701 | 0.70 | 0.54 | yes | n/a |
| Rotom-Heat | Sceptile@Sceptilite | 4 | 0.1% | 145.428 | 1.000 | 1.00 | 0.18 | no | 7 |
| Aegislash@Focus Sash | Venusaur@Life Orb | 4 | 0.1% | 105.596 | 0.653 | 0.19 | 0.65 | no | n/a |
| Klefki@Light Clay | Volcarona@Sitrus Berry | 7 | 0.3% | 92.973 | 1.000 | 0.23 | 1.00 | no | n/a |
| Kleavor@Focus Sash | Whimsicott@Fairy Feather | 4 | 0.1% | 86.449 | 0.387 | 0.39 | 0.32 | no | 3 |
| Espathra | Goodra-Hisui@Leftovers | 4 | 0.2% | 82.680 | 0.596 | 0.60 | 0.23 | yes | n/a |
| Klefki | Volcarona@Sitrus Berry | 7 | 0.3% | 72.459 | 0.779 | 0.23 | 0.78 | yes | n/a |
| Maushold@Chople Berry | Sinistcha@Occa Berry | 8 | 0.3% | 64.709 | 0.558 | 0.56 | 0.40 | no | 5 |
| Politoed@Life Orb | Staraptor@Choice Scarf | 4 | 0.1% | 56.729 | 0.455 | 0.45 | 0.15 | no | n/a |
| Toxapex@Leftovers | Venusaur@Life Orb | 6 | 0.2% | 51.093 | 0.316 | 0.27 | 0.32 | no | n/a |
| Aegislash | Venusaur@Life Orb | 4 | 0.1% | 50.489 | 0.312 | 0.19 | 0.31 | yes | n/a |
| Absol | Goodra-Hisui@Leftovers | 5 | 0.2% | 49.837 | 0.745 | 0.75 | 0.14 | yes | n/a |
| Absol@Absolite Z | Goodra-Hisui@Leftovers | 5 | 0.2% | 49.837 | 0.745 | 0.75 | 0.14 | no | n/a |
| Absol | Espathra@Grassy Seed | 4 | 0.2% | 46.856 | 0.701 | 0.70 | 0.11 | yes | n/a |
| Absol@Absolite Z | Espathra@Grassy Seed | 4 | 0.2% | 46.856 | 0.701 | 0.70 | 0.11 | no | n/a |
| Toxapex | Venusaur@Life Orb | 6 | 0.2% | 46.076 | 0.285 | 0.27 | 0.28 | yes | n/a |
| Goodra-Hisui | Absol@Absolite Z | 5 | 0.2% | 45.084 | 0.674 | 0.14 | 0.67 | yes | n/a |
| Kleavor | Whimsicott@Fairy Feather | 4 | 0.1% | 44.972 | 0.387 | 0.39 | 0.17 | yes | 3 |
| Pyroar | Kommo-o@Life Orb | 14 | 0.5% | 43.214 | 0.631 | 0.34 | 0.63 | yes | n/a |
| Kommo-o@Life Orb | Pyroar@Pyroarite | 14 | 0.5% | 43.214 | 0.631 | 0.63 | 0.34 | no | n/a |
| Lycanroc-Dusk@Focus Sash | Scovillain@Scovillainite | 6 | 0.2% | 42.844 | 0.342 | 0.24 | 0.34 | no | 6 |
| Scovillain | Lycanroc-Dusk@Focus Sash | 6 | 0.2% | 41.307 | 0.342 | 0.34 | 0.23 | yes | 7 |

### Item-level pairs by support
| A | B | Teams | Support | Lift | Norm. lift | Conf(A|B) | Conf(B|A) | Core? | Ladder rank (B in A's teammates) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | Raichu@Raichunite Y | 622 | 22.4% | 1.637 | 0.893 | 0.89 | 0.41 | yes | 6 |
| Rillaboom | Gholdengo@Life Orb | 630 | 21.5% | 1.576 | 0.860 | 0.86 | 0.39 | yes | 4 |
| Gholdengo | Rillaboom@Miracle Seed | 602 | 20.6% | 1.817 | 0.718 | 0.52 | 0.72 | yes | 1 |
| Rillaboom | Salamence@Salamencite | 641 | 20.3% | 1.237 | 0.675 | 0.68 | 0.37 | yes | 3 |
| Gholdengo@Life Orb | Rillaboom@Miracle Seed | 542 | 18.6% | 1.883 | 0.744 | 0.47 | 0.74 | no | 1 |
| Raichu | Rillaboom@Miracle Seed | 518 | 18.6% | 1.836 | 0.725 | 0.47 | 0.73 | yes | 1 |
| Sneasler | Salamence@Salamencite | 580 | 18.6% | 1.487 | 0.617 | 0.62 | 0.45 | yes | 2 |
| Raichu@Raichunite Y | Rillaboom@Miracle Seed | 517 | 18.5% | 1.870 | 0.739 | 0.47 | 0.74 | no | 1 |
| Rillaboom | Arcanine-Hisui@Focus Sash | 505 | 17.5% | 1.561 | 0.852 | 0.85 | 0.32 | yes | n/a |
| Sneasler | Rillaboom@Miracle Seed | 514 | 16.8% | 1.023 | 0.425 | 0.42 | 0.40 | no | 1 |
| Gholdengo | Raichu@Raichunite Y | 470 | 16.6% | 2.305 | 0.663 | 0.66 | 0.58 | yes | 5 |
| Rillaboom@Miracle Seed | Salamence@Salamencite | 469 | 15.0% | 1.263 | 0.499 | 0.50 | 0.38 | no | 3 |
| Salamence | Rillaboom@Miracle Seed | 469 | 15.0% | 1.260 | 0.498 | 0.38 | 0.50 | yes | 1 |
| Raichu | Gholdengo@Life Orb | 423 | 15.0% | 2.342 | 0.600 | 0.60 | 0.59 | yes | 2 |
| Gholdengo@Life Orb | Raichu@Raichunite Y | 422 | 15.0% | 2.385 | 0.599 | 0.60 | 0.60 | no | 5 |
| Arcanine-Hisui | Rillaboom@Miracle Seed | 403 | 13.9% | 1.676 | 0.662 | 0.35 | 0.66 | yes | 1 |
| Arcanine-Hisui@Focus Sash | Rillaboom@Miracle Seed | 398 | 13.7% | 1.697 | 0.671 | 0.35 | 0.67 | no | 1 |
| Rillaboom | Incineroar@Sitrus Berry | 422 | 13.5% | 1.302 | 0.711 | 0.71 | 0.25 | yes | 1 |
| Rillaboom | Sneasler@Grassy Seed | 423 | 13.5% | 1.832 | 1.000 | 1.00 | 0.25 | yes | 2 |
| Arcanine-Hisui | Raichu@Raichunite Y | 368 | 13.2% | 2.503 | 0.628 | 0.53 | 0.63 | yes | 5 |
| Raichu | Arcanine-Hisui@Focus Sash | 367 | 13.2% | 2.509 | 0.642 | 0.64 | 0.51 | yes | 4 |
| Arcanine-Hisui@Focus Sash | Raichu@Raichunite Y | 366 | 13.1% | 2.554 | 0.641 | 0.52 | 0.64 | no | 5 |
| Incineroar | Rillaboom@Miracle Seed | 409 | 13.1% | 1.143 | 0.451 | 0.33 | 0.45 | no | 1 |
| Gholdengo | Salamence@Salamencite | 382 | 12.1% | 1.402 | 0.422 | 0.40 | 0.42 | yes | 2 |
| Gholdengo | Arcanine-Hisui@Focus Sash | 343 | 11.8% | 1.998 | 0.574 | 0.57 | 0.41 | yes | 6 |

## 4. Item Synergies
Species@item pairs whose lift beats the species-level pair's lift by >= 0.3.
| Token | Partner | Item lift | Species lift | Delta | Teams |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rotom-Heat@Choice Scarf | Sceptile | 642.607 | 145.428 | 497.180 | 4 |
| Espathra@Grassy Seed | Goodra-Hisui | 225.195 | 74.794 | 150.401 | 4 |
| Volcarona@Sitrus Berry | Klefki | 72.459 | 10.934 | 61.525 | 7 |
| Whimsicott@Fairy Feather | Kleavor | 44.972 | 5.444 | 39.528 | 4 |
| Venusaur@Life Orb | Aegislash | 50.489 | 13.705 | 36.784 | 4 |
| Venusaur@Life Orb | Toxapex | 46.076 | 10.883 | 35.194 | 6 |
| Incineroar@White Herb | Hatterene | 33.316 | 1.594 | 31.723 | 21 |
| Espathra@Grassy Seed | Absol | 46.856 | 15.562 | 31.293 | 4 |
| Incineroar@White Herb | Camerupt | 32.229 | 2.090 | 30.140 | 25 |
| Kommo-o@Life Orb | Pyroar | 43.214 | 15.346 | 27.868 | 14 |
| Indeedee@Focus Sash | Meowstic-F | 34.716 | 6.985 | 27.731 | 7 |
| Staraptor@Choice Scarf | Pawmot | 26.807 | 0.659 | 26.148 | 4 |
| Sinistcha@Occa Berry | Maushold | 34.628 | 8.543 | 26.085 | 8 |
| Incineroar@Passho Berry | Vivillon | 27.323 | 2.936 | 24.387 | 27 |
| Milotic@Psychic Seed | Altaria | 26.331 | 3.618 | 22.713 | 12 |
| Basculegion@Mystic Water | Pyroar | 26.884 | 5.036 | 21.849 | 14 |
| Rillaboom@Eject Button | Vivillon | 22.951 | 1.561 | 21.390 | 23 |
| Milotic@Psychic Seed | Dragapult | 24.877 | 3.908 | 20.969 | 48 |
| Ninetales-Alola@Light Clay | Baxcalibur | 30.581 | 10.923 | 19.658 | 11 |
| Sinistcha@Occa Berry | Blastoise | 26.482 | 9.755 | 16.726 | 7 |
| Indeedee-F@Focus Sash | Torkoal | 20.148 | 3.493 | 16.656 | 4 |
| Garchomp@Life Orb | Klefki | 21.315 | 4.926 | 16.389 | 7 |
| Garchomp@Choice Scarf | Toxapex | 20.655 | 4.723 | 15.932 | 12 |
| Rillaboom@Eject Button | Gengar | 17.154 | 1.388 | 15.767 | 77 |
| Incineroar@Lum Berry | Gengar | 17.538 | 2.592 | 14.946 | 4 |
| Maushold@Chople Berry | Blastoise | 31.259 | 18.840 | 12.419 | 11 |
| Politoed@Sitrus Berry | Vivillon | 23.623 | 11.447 | 12.176 | 28 |
| Sinistcha@Coba Berry | Maushold | 20.625 | 8.543 | 12.082 | 4 |
| Indeedee-F@Psychic Seed | Alakazam | 15.978 | 4.624 | 11.353 | 4 |
| Kingambit@Occa Berry | Pawmot | 12.059 | 1.053 | 11.006 | 5 |
| Indeedee@Focus Sash | Typhlosion-Hisui | 15.907 | 4.927 | 10.980 | 4 |
| Ninetales-Alola@Never-Melt Ice | Glimmora | 13.315 | 2.374 | 10.942 | 5 |
| Indeedee-F@Psychic Seed | Hatterene | 16.316 | 5.376 | 10.939 | 28 |
| Basculegion@Mystic Water | Typhlosion-Hisui | 12.384 | 1.855 | 10.529 | 5 |
| Armarouge@Twisted Spoon | Dragapult | 17.463 | 6.946 | 10.517 | 15 |
| Altaria@Haban Berry | Dragapult | 28.531 | 18.655 | 9.876 | 12 |
| Indeedee-F@Psychic Seed | Camerupt | 13.177 | 3.574 | 9.603 | 26 |
| Rillaboom@Life Orb | Lycanroc-Dusk | 10.491 | 0.923 | 9.568 | 4 |
| Incineroar@Passho Berry | Gengar | 12.145 | 2.592 | 9.553 | 54 |
| Rillaboom@Eject Button | Politoed | 10.383 | 0.880 | 9.503 | 56 |
| Politoed@Life Orb | Pawmot | 10.975 | 1.543 | 9.432 | 5 |
| Kingambit@Occa Berry | Blaziken | 11.352 | 1.978 | 9.374 | 5 |
| Sinistcha@Coba Berry | Blastoise | 19.077 | 9.755 | 9.321 | 4 |
| Milotic@Psychic Seed | Armarouge | 10.903 | 1.886 | 9.017 | 40 |
| Blaziken@Focus Sash | Metagross | 11.538 | 2.611 | 8.927 | 8 |
| Kingambit@Focus Sash | Aerodactyl | 10.980 | 2.237 | 8.743 | 38 |
| Kommo-o@Leftovers | Meowstic-F | 20.144 | 11.438 | 8.706 | 7 |
| Sinistcha@Occa Berry | Delphox | 19.289 | 10.732 | 8.557 | 12 |
| Rillaboom@Eject Button | Altaria | 9.125 | 0.634 | 8.491 | 4 |
| Sneasler@Focus Sash | Delphox | 10.093 | 1.687 | 8.406 | 50 |
| Garchomp@Life Orb | Aerodactyl | 12.000 | 3.654 | 8.347 | 37 |
| Incineroar@Passho Berry | Politoed | 9.891 | 1.556 | 8.335 | 52 |
| Farigiraf@Colbur Berry | Mawile | 12.629 | 4.411 | 8.218 | 6 |
| Kingambit@Black Glasses | Camerupt | 10.184 | 2.023 | 8.160 | 23 |
| Politoed@Mystic Water | Grimmsnarl | 13.174 | 5.108 | 8.067 | 41 |
| Kingambit@Black Glasses | Hatterene | 9.786 | 1.894 | 7.892 | 18 |
| Goodra-Hisui@Leftovers | Espathra | 82.680 | 74.794 | 7.885 | 4 |
| Kingambit@Black Glasses | Lycanroc-Dusk | 9.407 | 1.873 | 7.534 | 6 |
| Kommo-o@Life Orb | Whimsicott | 11.746 | 4.303 | 7.444 | 26 |
| Maushold@Chople Berry | Sinistcha | 15.964 | 8.543 | 7.421 | 15 |
| Glimmora@Glimmoranite | Klefki | 25.710 | 18.407 | 7.303 | 7 |
| Maushold@Chople Berry | Delphox | 16.896 | 9.636 | 7.259 | 15 |
| Sneasler@Focus Sash | Sinistcha | 8.013 | 1.091 | 6.922 | 42 |
| Incineroar@Chople Berry | Sirfetch’d | 8.593 | 1.744 | 6.849 | 5 |
| Dragonite@Life Orb | Gengar | 7.725 | 0.893 | 6.832 | 4 |
| Politoed@Sitrus Berry | Gengar | 14.349 | 7.542 | 6.808 | 76 |
| Sneasler@Focus Sash | Maushold | 7.727 | 1.112 | 6.615 | 13 |
| Basculegion@Mystic Water | Kommo-o | 8.052 | 1.497 | 6.555 | 22 |
| Staraptor@Choice Scarf | Politoed | 6.653 | 0.164 | 6.490 | 4 |
| Sneasler@Focus Sash | Blastoise | 7.974 | 1.521 | 6.453 | 15 |
| Kingambit@Black Glasses | Scovillain | 8.130 | 1.705 | 6.425 | 7 |
| Whimsicott@Occa Berry | Kommo-o | 10.692 | 4.303 | 6.389 | 7 |
| Whimsicott@Fairy Feather | Metagross | 7.142 | 0.782 | 6.360 | 4 |
| Basculegion@Mystic Water | Whimsicott | 9.082 | 2.767 | 6.315 | 31 |
| Milotic@Sitrus Berry | Ceruledge | 11.228 | 4.975 | 6.253 | 47 |
| Sneasler@Psychic Seed | Meowstic-F | 8.134 | 1.983 | 6.151 | 11 |
| Armarouge@Twisted Spoon | Hatterene | 9.888 | 3.881 | 6.007 | 6 |
| Volcarona@Rocky Helmet | Glimmora | 10.994 | 5.138 | 5.857 | 21 |
| Sableye@Roseli Berry | Sinistcha | 9.411 | 3.580 | 5.832 | 4 |
| Altaria@Haban Berry | Metagross | 16.647 | 10.885 | 5.762 | 12 |
| Torkoal@Life Orb | Armarouge | 10.407 | 4.773 | 5.634 | 4 |
| Sinistcha@Coba Berry | Delphox | 16.362 | 10.732 | 5.630 | 9 |
| Indeedee-F@Colbur Berry | Lopunny | 9.019 | 3.400 | 5.620 | 5 |
| Pelipper@Focus Sash | Starmie | 12.414 | 6.838 | 5.576 | 4 |
| Armarouge@Focus Sash | Dragapult | 12.509 | 6.946 | 5.563 | 19 |
| Sneasler@Psychic Seed | Starmie | 7.348 | 1.791 | 5.557 | 4 |
| Pelipper@Sitrus Berry | Venusaur | 9.582 | 4.097 | 5.485 | 38 |
| Klefki@Light Clay | Glimmora | 23.618 | 18.407 | 5.211 | 7 |
| Glimmora@Focus Sash | Blaziken | 7.504 | 2.504 | 5.000 | 4 |
| Whimsicott@Occa Berry | Glimmora | 9.684 | 4.719 | 4.965 | 6 |
| Indeedee-F@Rocky Helmet | Pyroar | 8.713 | 3.757 | 4.956 | 15 |
| Rillaboom@Occa Berry | Vivillon | 6.474 | 1.561 | 4.913 | 6 |
| Rillaboom@Expert Belt | Excadrill | 5.503 | 0.614 | 4.889 | 12 |
| Glimmora@Focus Sash | Hatterene | 6.443 | 1.646 | 4.797 | 4 |
| Goodra-Hisui@Leftovers | Absol | 49.837 | 45.084 | 4.753 | 5 |
| Indeedee-F@Rocky Helmet | Altaria | 8.330 | 3.592 | 4.738 | 13 |
| Incineroar@White Herb | Farigiraf | 5.716 | 1.038 | 4.678 | 27 |
| Staraptor@Choice Scarf | Farigiraf | 5.042 | 0.397 | 4.645 | 6 |
| Garchomp@Garchompite | Tyranitar | 4.920 | 0.353 | 4.567 | 4 |
| Indeedee@Focus Sash | Kommo-o | 6.239 | 1.696 | 4.543 | 12 |
| Delphox@Life Orb | Indeedee-F | 5.494 | 1.088 | 4.406 | 4 |
| Rillaboom@Expert Belt | Tyranitar | 4.938 | 0.586 | 4.352 | 13 |
| Espathra@Grassy Seed | Floette-Eternal | 7.200 | 2.870 | 4.330 | 5 |
| Aegislash@Focus Sash | Venusaur | 17.966 | 13.705 | 4.261 | 4 |
| Pelipper@Sitrus Berry | Grimmsnarl | 8.823 | 4.617 | 4.205 | 56 |
| Sneasler@Psychic Seed | Gardevoir | 5.766 | 1.585 | 4.181 | 137 |
| Milotic@Psychic Seed | Indeedee-F | 5.288 | 1.135 | 4.153 | 58 |
| Milotic@Psychic Seed | Gengar | 5.213 | 1.134 | 4.079 | 16 |
| Incineroar@White Herb | Indeedee-F | 4.562 | 0.516 | 4.047 | 26 |
| Whimsicott@Focus Sash | Pyroar | 17.419 | 13.420 | 3.999 | 16 |
| Rillaboom@Eject Button | Archaludon | 4.555 | 0.640 | 3.915 | 53 |
| Pelipper@Choice Scarf | Basculegion | 5.672 | 1.764 | 3.908 | 4 |
| Venusaur@Focus Sash | Grimmsnarl | 10.960 | 7.081 | 3.879 | 37 |
| Farigiraf@Grassy Seed | Torkoal | 6.246 | 2.390 | 3.856 | 6 |
| Rillaboom@Eject Button | Kommo-o | 4.702 | 0.878 | 3.824 | 15 |
| Archaludon@Magnet | Pelipper | 9.192 | 5.377 | 3.815 | 4 |
| Indeedee@Focus Sash | Dragonite | 5.197 | 1.392 | 3.805 | 5 |
| Indeedee-F@Rocky Helmet | Dragapult | 7.503 | 3.880 | 3.623 | 51 |
| Politoed@Mystic Water | Charizard | 6.016 | 2.395 | 3.621 | 44 |
| Politoed@Life Orb | Farigiraf | 6.419 | 2.814 | 3.605 | 20 |
| Indeedee@Choice Scarf | Corviknight | 14.706 | 11.152 | 3.554 | 52 |
| Garchomp@Choice Scarf | Venusaur | 5.543 | 2.054 | 3.489 | 19 |
| Kommo-o@Life Orb | Gardevoir | 6.485 | 3.000 | 3.485 | 20 |
| Basculegion@Focus Sash | Lucario | 5.867 | 2.426 | 3.441 | 4 |
| Sableye@Light Clay | Swampert | 11.737 | 8.301 | 3.435 | 6 |
| Kingambit@Occa Berry | Glimmora | 4.964 | 1.545 | 3.419 | 5 |
| Volcarona@Rocky Helmet | Baxcalibur | 8.645 | 5.232 | 3.413 | 8 |
| Sneasler@White Herb | Corviknight | 5.407 | 2.005 | 3.401 | 49 |
| Farigiraf@Grassy Seed | Absol | 4.271 | 0.917 | 3.354 | 4 |
| Garchomp@Sitrus Berry | Gardevoir | 3.918 | 0.599 | 3.318 | 5 |
| Incineroar@White Herb | Torkoal | 3.913 | 0.602 | 3.311 | 4 |
| Farigiraf@Grassy Seed | Primarina | 5.701 | 2.416 | 3.285 | 5 |
| Venusaur@Life Orb | Sylveon | 4.618 | 1.373 | 3.245 | 11 |
| Incineroar@Passho Berry | Archaludon | 4.096 | 0.858 | 3.237 | 46 |
| Incineroar@Life Orb | Farigiraf | 4.201 | 1.038 | 3.163 | 4 |
| Sneasler@Psychic Seed | Indeedee | 5.272 | 2.118 | 3.153 | 115 |
| Armarouge@Focus Sash | Gengar | 5.092 | 1.985 | 3.108 | 14 |
| Politoed@Mystic Water | Golisopod | 6.967 | 3.865 | 3.102 | 54 |
| Klefki@Light Clay | Volcarona | 14.029 | 10.934 | 3.096 | 7 |
| Incineroar@Chople Berry | Politoed | 4.633 | 1.556 | 3.077 | 26 |
| Sinistcha@Colbur Berry | Delphox | 13.802 | 10.732 | 3.070 | 27 |
| Hydreigon@Focus Sash | Golisopod | 4.173 | 1.134 | 3.039 | 4 |
| Rillaboom@Occa Berry | Ninetales-Alola | 4.142 | 1.107 | 3.035 | 5 |
| Corviknight@Psychic Seed | Indeedee | 14.169 | 11.152 | 3.017 | 45 |
| Altaria@Altarianite | Kingambit | 4.069 | 1.055 | 3.014 | 4 |
| Indeedee-F@Colbur Berry | Rotom-Heat | 4.615 | 1.619 | 2.995 | 5 |
| Incineroar@Expert Belt | Farigiraf | 4.016 | 1.038 | 2.978 | 5 |
| Blaziken@Blazikenite | Torkoal | 10.386 | 7.410 | 2.976 | 10 |
| Staraptor@Choice Scarf | Golisopod | 3.397 | 0.428 | 2.969 | 4 |
| Pelipper@Choice Scarf | Golisopod | 6.643 | 3.720 | 2.923 | 4 |
| Raichu@Raichunite X | Archaludon | 3.030 | 0.201 | 2.829 | 6 |
| Aerodactyl@Focus Sash | Lucario | 6.936 | 4.124 | 2.812 | 4 |
| Kingambit@Occa Berry | Whimsicott | 4.168 | 1.376 | 2.793 | 5 |
| Farigiraf@Colbur Berry | Baxcalibur | 3.354 | 0.580 | 2.775 | 5 |
| Politoed@Mystic Water | Farigiraf | 5.575 | 2.814 | 2.761 | 47 |
| Indeedee@Focus Sash | Glimmora | 3.961 | 1.225 | 2.736 | 6 |
| Kingambit@Occa Berry | Volcarona | 3.692 | 0.968 | 2.724 | 6 |
| Milotic@Psychic Seed | Staraptor | 4.927 | 2.209 | 2.718 | 38 |
| Baxcalibur@Life Orb | Arcanine-Hisui | 3.396 | 0.687 | 2.709 | 4 |
| Armarouge@Psychic Seed | Golisopod | 4.583 | 1.886 | 2.697 | 4 |
| Milotic@Psychic Seed | Metagross | 5.328 | 2.631 | 2.696 | 20 |
| Rotom-Heat@Sitrus Berry | Gardevoir | 6.118 | 3.435 | 2.682 | 4 |
| Politoed@Life Orb | Golisopod | 6.528 | 3.865 | 2.663 | 19 |
| Venusaur@Life Orb | Indeedee | 3.442 | 0.795 | 2.647 | 4 |
| Kingambit@Chople Berry | Meowstic-F | 5.126 | 2.497 | 2.629 | 7 |
| Indeedee-F@Psychic Seed | Torkoal | 6.102 | 3.493 | 2.609 | 19 |
| Baxcalibur@Baxcalibrite | Ninetales-Alola | 13.512 | 10.923 | 2.589 | 14 |
| Armarouge@Twisted Spoon | Golisopod | 4.457 | 1.886 | 2.571 | 16 |
| Sinistcha@Sitrus Berry | Swampert | 4.544 | 1.975 | 2.569 | 7 |
| Garchomp@Sitrus Berry | Whimsicott | 4.856 | 2.298 | 2.558 | 5 |
| Blaziken@Blazikenite | Hatterene | 7.935 | 5.394 | 2.542 | 5 |
| Kleavor@Focus Sash | Metagross | 7.142 | 4.617 | 2.525 | 5 |
| Rillaboom@Sitrus Berry | Froslass | 3.933 | 1.410 | 2.523 | 32 |
| Armarouge@Life Orb | Torkoal | 7.288 | 4.773 | 2.516 | 21 |
| Indeedee-F@Rocky Helmet | Meganium | 4.419 | 1.906 | 2.514 | 5 |
| Venusaur@Venusaurite | Farigiraf | 3.236 | 0.764 | 2.473 | 5 |
| Sableye@Light Clay | Golisopod | 4.871 | 2.418 | 2.454 | 8 |
| Kommo-o@Life Orb | Basculegion | 3.930 | 1.497 | 2.433 | 25 |
| Kingambit@Life Orb | Delphox | 4.577 | 2.155 | 2.422 | 35 |
| Sneasler@Psychic Seed | Lopunny | 3.603 | 1.184 | 2.418 | 4 |
| Pelipper@Damp Rock | Farigiraf | 3.842 | 1.428 | 2.413 | 4 |
| Sneasler@Psychic Seed | Indeedee-F | 3.543 | 1.133 | 2.410 | 207 |
| Kingambit@Focus Sash | Charizard | 3.584 | 1.174 | 2.410 | 52 |
| Farigiraf@Twisted Spoon | Incineroar | 3.446 | 1.038 | 2.408 | 5 |
| Aerodactyl@Focus Sash | Gardevoir | 3.435 | 1.029 | 2.406 | 5 |
| Kommo-o@Leftovers | Gengar | 6.860 | 4.457 | 2.403 | 28 |
| Kingambit@Focus Sash | Sylveon | 3.554 | 1.153 | 2.401 | 48 |
| Farigiraf@Colbur Berry | Swampert | 3.855 | 1.457 | 2.398 | 8 |
| Sneasler@Focus Sash | Floette-Eternal | 3.983 | 1.608 | 2.375 | 63 |
| Incineroar@Chople Berry | Gengar | 4.960 | 2.592 | 2.368 | 24 |
| Indeedee-F@Colbur Berry | Gardevoir | 7.669 | 5.333 | 2.336 | 94 |
| Venusaur@Focus Sash | Swampert | 7.826 | 5.518 | 2.308 | 21 |
| Basculegion@Life Orb | Baxcalibur | 3.978 | 1.670 | 2.308 | 15 |
| Glimmora@Glimmoranite | Sirfetch’d | 8.107 | 5.804 | 2.303 | 5 |
| Indeedee@Focus Sash | Venusaur | 3.097 | 0.795 | 2.303 | 4 |
| Primarina@Life Orb | Torkoal | 4.605 | 2.303 | 2.302 | 4 |
| Basculegion@Life Orb | Kleavor | 3.787 | 1.504 | 2.283 | 5 |
| Garchomp@Choice Scarf | Charizard | 5.016 | 2.736 | 2.280 | 59 |
| Corviknight@Psychic Seed | Excadrill | 13.292 | 11.027 | 2.265 | 47 |
| Rillaboom@Life Orb | Ninetales-Alola | 3.361 | 1.107 | 2.254 | 5 |
| Rillaboom@Expert Belt | Pelipper | 2.766 | 0.525 | 2.241 | 8 |
| Raichu@Raichunite X | Pelipper | 2.517 | 0.276 | 2.241 | 4 |
| Sinistcha@Sitrus Berry | Excadrill | 3.651 | 1.411 | 2.240 | 8 |
| Sneasler@Psychic Seed | Armarouge | 3.212 | 0.985 | 2.227 | 67 |
| Volcarona@Sitrus Berry | Glimmora | 7.346 | 5.138 | 2.209 | 9 |
| Garchomp@Garchompite | Floette-Eternal | 3.505 | 1.305 | 2.200 | 4 |
| Ceruledge@Focus Sash | Sneasler | 2.408 | 0.212 | 2.196 | 4 |
| Kingambit@Focus Sash | Farigiraf | 3.586 | 1.394 | 2.192 | 59 |
| Rillaboom@Life Orb | Annihilape | 2.853 | 0.668 | 2.185 | 4 |
| Glimmora@Glimmoranite | Typhlosion-Hisui | 7.674 | 5.494 | 2.180 | 4 |
| Rillaboom@Life Orb | Camerupt | 2.612 | 0.456 | 2.156 | 5 |
| Garchomp@Life Orb | Whimsicott | 4.449 | 2.298 | 2.151 | 25 |
| Garchomp@Life Orb | Charizard | 4.882 | 2.736 | 2.146 | 64 |
| Indeedee-F@Psychic Seed | Blaziken | 3.557 | 1.421 | 2.135 | 6 |
| Volcarona@Rocky Helmet | Swampert | 2.905 | 0.776 | 2.130 | 5 |
| Hatterene@Life Orb | Gallade | 18.351 | 16.229 | 2.122 | 5 |
| Basculegion@Life Orb | Lycanroc-Dusk | 4.378 | 2.256 | 2.122 | 4 |
| Kingambit@Life Orb | Froslass | 4.583 | 2.479 | 2.104 | 47 |
| Kleavor@Focus Sash | Whimsicott | 7.547 | 5.444 | 2.103 | 5 |
| Lycanroc-Dusk@Focus Sash | Scovillain | 41.307 | 39.224 | 2.082 | 6 |
| Sinistcha@Rocky Helmet | Golisopod | 3.307 | 1.239 | 2.068 | 4 |
| Incineroar@Chople Berry | Golisopod | 2.722 | 0.657 | 2.064 | 32 |
| Sinistcha@Sitrus Berry | Grimmsnarl | 3.256 | 1.217 | 2.039 | 7 |
| Rillaboom@Eject Button | Incineroar | 3.280 | 1.252 | 2.029 | 75 |
| Basculegion@Life Orb | Lucario | 4.448 | 2.426 | 2.022 | 22 |
| Pelipper@Sitrus Berry | Charizard | 3.706 | 1.718 | 1.987 | 54 |
| Basculegion@Mystic Water | Gardevoir | 4.451 | 2.473 | 1.978 | 22 |
| Volcarona@Focus Sash | Kingambit | 2.945 | 0.968 | 1.977 | 4 |
| Incineroar@Leftovers | Farigiraf | 3.011 | 1.038 | 1.973 | 5 |
| Sinistcha@Rocky Helmet | Archaludon | 3.067 | 1.095 | 1.973 | 4 |
| Dragapult@Focus Sash | Incineroar | 2.325 | 0.356 | 1.969 | 4 |
| Basculegion@Life Orb | Scovillain | 4.073 | 2.122 | 1.951 | 5 |
| Rillaboom@Leftovers | Golisopod | 2.495 | 0.549 | 1.947 | 4 |
| Talonflame@Life Orb | Indeedee-F | 3.261 | 1.326 | 1.935 | 5 |
| Armarouge@Twisted Spoon | Staraptor | 4.363 | 2.429 | 1.933 | 16 |
| Rillaboom@Expert Belt | Milotic | 3.025 | 1.093 | 1.932 | 14 |
| Armarouge@Focus Sash | Lucario | 3.358 | 1.440 | 1.918 | 4 |
| Altaria@Haban Berry | Milotic | 5.534 | 3.618 | 1.915 | 12 |
| Primarina@Grassy Seed | Raichu | 3.501 | 1.590 | 1.911 | 10 |
| Altaria@Haban Berry | Indeedee-F | 5.494 | 3.592 | 1.902 | 13 |
| Venusaur@Life Orb | Garchomp | 3.944 | 2.054 | 1.890 | 13 |
| Kleavor@Choice Scarf | Golisopod | 3.737 | 1.868 | 1.868 | 4 |
| Sinistcha@Colbur Berry | Floette-Eternal | 4.420 | 2.555 | 1.865 | 25 |
| Indeedee@Focus Sash | Charizard | 2.682 | 0.821 | 1.862 | 16 |
| Armarouge@Twisted Spoon | Metagross | 3.048 | 1.199 | 1.849 | 5 |
| Kommo-o@Life Orb | Glimmora | 4.489 | 2.658 | 1.831 | 7 |
| Ceruledge@Colbur Berry | Salamence | 2.485 | 0.674 | 1.811 | 9 |
| Farigiraf@Colbur Berry | Pelipper | 3.231 | 1.428 | 1.803 | 20 |
| Kingambit@Life Orb | Sinistcha | 3.024 | 1.258 | 1.766 | 24 |
| Incineroar@White Herb | Kingambit | 2.564 | 0.804 | 1.760 | 20 |
| Indeedee@Choice Scarf | Excadrill | 8.465 | 6.706 | 1.759 | 87 |
| Gholdengo@Choice Scarf | Pelipper | 2.052 | 0.293 | 1.759 | 4 |
| Pelipper@Life Orb | Farigiraf | 3.184 | 1.428 | 1.756 | 4 |
| Gallade@White Herb | Indeedee-F | 5.494 | 3.747 | 1.747 | 5 |
| Indeedee-F@Colbur Berry | Scovillain | 3.452 | 1.707 | 1.744 | 4 |
| Rillaboom@Occa Berry | Kommo-o | 2.613 | 0.878 | 1.734 | 7 |
| Whimsicott@Occa Berry | Gardevoir | 4.449 | 2.724 | 1.726 | 6 |
| Volcarona@Sitrus Berry | Archaludon | 2.156 | 0.436 | 1.720 | 9 |
| Rillaboom@Occa Berry | Floette-Eternal | 3.075 | 1.366 | 1.709 | 29 |
| Kingambit@Focus Sash | Garchomp | 3.094 | 1.386 | 1.708 | 55 |
| Armarouge@Life Orb | Gardevoir | 4.715 | 3.011 | 1.704 | 31 |
| Sylveon@Life Orb | Kingambit | 2.853 | 1.153 | 1.700 | 6 |
| Armarouge@Twisted Spoon | Milotic | 3.574 | 1.886 | 1.688 | 16 |
| Milotic@Sitrus Berry | Excadrill | 4.254 | 2.575 | 1.679 | 60 |
| Altaria@Altarianite | Sneasler | 2.408 | 0.734 | 1.674 | 4 |
| Glimmora@Glimmoranite | Volcarona | 6.810 | 5.138 | 1.672 | 41 |
| Primarina@Life Orb | Farigiraf | 4.075 | 2.416 | 1.658 | 16 |
| Venusaur@Focus Sash | Pelipper | 5.746 | 4.097 | 1.650 | 39 |
| Aegislash@Focus Sash | Garchomp | 6.321 | 4.683 | 1.637 | 6 |
| Kommo-o@Leftovers | Ninetales-Alola | 5.528 | 3.898 | 1.630 | 6 |
| Garchomp@Life Orb | Sylveon | 3.201 | 1.585 | 1.616 | 38 |
| Aerodactyl@Focus Sash | Indeedee-F | 2.173 | 0.565 | 1.608 | 8 |
| Aegislash@Focus Sash | Incineroar | 3.034 | 1.451 | 1.583 | 5 |
| Rillaboom@Expert Belt | Salamence | 2.817 | 1.234 | 1.583 | 23 |
| Corviknight@Leftovers | Garchomp | 2.267 | 0.703 | 1.564 | 7 |
| Sinistcha@Sitrus Berry | Gardevoir | 1.949 | 0.386 | 1.562 | 4 |
| Sneasler@Focus Sash | Baxcalibur | 2.716 | 1.163 | 1.553 | 9 |
| Sinistcha@Sitrus Berry | Pelipper | 3.015 | 1.462 | 1.553 | 11 |
| Dragapult@Life Orb | Altaria | 20.207 | 18.655 | 1.552 | 12 |
| Sylveon@Life Orb | Salamence | 2.325 | 0.775 | 1.551 | 6 |
| Rillaboom@Occa Berry | Delphox | 2.168 | 0.621 | 1.548 | 7 |
| Pelipper@Life Orb | Kingambit | 1.869 | 0.327 | 1.542 | 4 |
| Klefki@Light Clay | Archaludon | 6.931 | 5.402 | 1.529 | 7 |
| Garchomp@Sitrus Berry | Staraptor | 1.934 | 0.411 | 1.523 | 4 |
| Rillaboom@Eject Button | Swampert | 1.960 | 0.448 | 1.512 | 8 |
| Sneasler@Psychic Seed | Typhlosion-Hisui | 2.536 | 1.029 | 1.507 | 5 |
| Indeedee-F@Psychic Seed | Farigiraf | 1.959 | 0.454 | 1.505 | 24 |
| Sableye@Light Clay | Archaludon | 5.478 | 3.973 | 1.505 | 9 |
| Incineroar@Chople Berry | Kommo-o | 2.805 | 1.309 | 1.496 | 11 |
| Indeedee-F@Sitrus Berry | Torkoal | 4.969 | 3.493 | 1.477 | 7 |
| Sneasler@Psychic Seed | Annihilape | 1.946 | 0.474 | 1.471 | 10 |
| Kingambit@Chople Berry | Kleavor | 2.348 | 0.877 | 1.471 | 5 |
| Armarouge@Twisted Spoon | Whimsicott | 2.452 | 0.981 | 1.470 | 4 |
| Rotom-Heat@Sitrus Berry | Golisopod | 3.348 | 1.880 | 1.468 | 4 |
| Sinistcha@Kasib Berry | Golisopod | 2.707 | 1.239 | 1.467 | 4 |
| Scovillain@Scovillainite | Lycanroc-Dusk | 40.685 | 39.224 | 1.460 | 6 |
| Indeedee@Choice Scarf | Tyranitar | 7.364 | 5.904 | 1.459 | 93 |
| Rillaboom@Life Orb | Blaziken | 2.466 | 1.008 | 1.458 | 4 |
| Primarina@Grassy Seed | Arcanine-Hisui | 2.680 | 1.222 | 1.458 | 6 |
| Indeedee-F@Rocky Helmet | Maushold | 2.791 | 1.347 | 1.444 | 9 |
| Pelipper@Sitrus Berry | Swampert | 8.642 | 7.205 | 1.436 | 43 |
| Rillaboom@Occa Berry | Gengar | 2.818 | 1.388 | 1.431 | 9 |
| Grapploct@Life Orb | Farigiraf | 5.200 | 3.771 | 1.429 | 4 |
| Farigiraf@Sitrus Berry | Grapploct | 5.197 | 3.771 | 1.426 | 6 |
| Sneasler@Psychic Seed | Rotom-Heat | 2.485 | 1.067 | 1.418 | 6 |
| Aegislash@Focus Sash | Sylveon | 5.667 | 4.252 | 1.415 | 4 |
| Incineroar@Sitrus Berry | Goodra-Hisui | 4.063 | 2.653 | 1.411 | 6 |
| Garchomp@Garchompite Z | Volcarona | 3.560 | 2.155 | 1.405 | 53 |
| Annihilape@Choice Scarf | Venusaur | 4.986 | 3.581 | 1.404 | 6 |
| Sneasler@White Herb | Lycanroc-Dusk | 2.527 | 1.125 | 1.402 | 6 |
| Milotic@Sitrus Berry | Tyranitar | 3.782 | 2.385 | 1.397 | 66 |
| Klefki@Light Clay | Garchomp | 6.321 | 4.926 | 1.395 | 7 |
| Pelipper@Focus Sash | Sableye | 4.937 | 3.549 | 1.387 | 8 |
| Kleavor@Focus Sash | Basculegion | 2.891 | 1.504 | 1.387 | 6 |
| Swampert@Sitrus Berry | Rillaboom | 1.832 | 0.448 | 1.384 | 4 |
| Dragonite@Life Orb | Indeedee-F | 2.115 | 0.733 | 1.382 | 5 |
| Pelipper@Sitrus Berry | Hydreigon | 2.145 | 0.765 | 1.380 | 5 |
| Kingambit@Black Glasses | Blaziken | 3.353 | 1.978 | 1.375 | 6 |
| Hatterene@Life Orb | Sirfetch’d | 11.859 | 10.488 | 1.371 | 4 |
| Volcarona@Rocky Helmet | Kingambit | 2.338 | 0.968 | 1.370 | 27 |
| Gholdengo@Grassy Seed | Arcanine-Hisui | 3.343 | 1.974 | 1.369 | 39 |
| Gholdengo@Choice Scarf | Archaludon | 1.547 | 0.179 | 1.368 | 4 |
| Sneasler@White Herb | Excadrill | 2.731 | 1.377 | 1.354 | 68 |
| Milotic@Leftovers | Baxcalibur | 2.820 | 1.469 | 1.351 | 13 |
| Incineroar@Chople Berry | Farigiraf | 2.388 | 1.038 | 1.350 | 32 |
| Sneasler@Psychic Seed | Metagross | 2.582 | 1.234 | 1.348 | 41 |
| Baxcalibur@Baxcalibrite | Pawmot | 7.031 | 5.684 | 1.347 | 6 |
| Corviknight@Psychic Seed | Tyranitar | 10.892 | 9.561 | 1.331 | 47 |
| Kingambit@Occa Berry | Basculegion | 2.695 | 1.372 | 1.324 | 11 |
| Sinistcha@Sitrus Berry | Tyranitar | 2.992 | 1.673 | 1.319 | 8 |
| Venusaur@Focus Sash | Archaludon | 4.502 | 3.192 | 1.309 | 40 |
| Incineroar@Sitrus Berry | Toxapex | 4.217 | 2.914 | 1.303 | 15 |
| Rillaboom@Expert Belt | Golisopod | 1.833 | 0.549 | 1.284 | 7 |
| Basculegion@Choice Scarf | Pelipper | 3.044 | 1.764 | 1.279 | 72 |
| Indeedee@Focus Sash | Whimsicott | 2.257 | 0.978 | 1.279 | 4 |
| Gholdengo@Grassy Seed | Kommo-o | 1.700 | 0.425 | 1.275 | 4 |
| Rillaboom@Occa Berry | Politoed | 2.154 | 0.880 | 1.274 | 8 |
| Altaria@Haban Berry | Arcanine-Hisui | 4.289 | 3.022 | 1.268 | 12 |
| Garchomp@Sitrus Berry | Gholdengo | 1.867 | 0.602 | 1.265 | 8 |
| Rillaboom@Expert Belt | Basculegion | 2.171 | 0.907 | 1.264 | 9 |
| Rillaboom@Grassy Seed | Charizard | 1.615 | 0.363 | 1.251 | 4 |
| Aegislash@Focus Sash | Charizard | 5.241 | 3.998 | 1.243 | 4 |
| Baxcalibur@Baxcalibrite | Volcarona | 6.472 | 5.232 | 1.240 | 23 |
| Politoed@Sitrus Berry | Incineroar | 2.788 | 1.556 | 1.232 | 75 |
| Farigiraf@Grassy Seed | Rillaboom | 1.832 | 0.601 | 1.232 | 39 |
| Politoed@Sitrus Berry | Froslass | 2.296 | 1.071 | 1.224 | 14 |
| Indeedee-F@Rocky Helmet | Whimsicott | 2.855 | 1.638 | 1.217 | 35 |
| Incineroar@Chople Berry | Archaludon | 2.072 | 0.858 | 1.214 | 24 |
| Maushold@Focus Sash | Incineroar | 2.620 | 1.412 | 1.208 | 6 |
| Indeedee@Focus Sash | Basculegion | 1.614 | 0.412 | 1.201 | 9 |
| Espathra@Focus Sash | Incineroar | 3.446 | 2.246 | 1.200 | 4 |
| Altaria@Altarianite | Rillaboom | 1.832 | 0.634 | 1.198 | 4 |
| Sinistcha@Coba Berry | Indeedee-F | 2.375 | 1.179 | 1.196 | 5 |
| Rillaboom@Sitrus Berry | Arcanine-Hisui | 2.744 | 1.552 | 1.192 | 78 |
| Pelipper@Focus Sash | Scovillain | 2.644 | 1.456 | 1.188 | 4 |
| Toxapex@Leftovers | Venusaur | 12.067 | 10.883 | 1.185 | 8 |
| Blaziken@Blazikenite | Glimmora | 3.684 | 2.504 | 1.180 | 5 |
| Armarouge@Focus Sash | Staraptor | 3.609 | 2.429 | 1.180 | 24 |
| Talonflame@Life Orb | Basculegion | 3.306 | 2.127 | 1.179 | 4 |
| Archaludon@Chople Berry | Pelipper | 6.549 | 5.377 | 1.172 | 10 |
| Volcarona@Rocky Helmet | Dragapult | 2.890 | 1.723 | 1.167 | 4 |
| Garchomp@Garchompite Z | Typhlosion-Hisui | 2.895 | 1.730 | 1.164 | 4 |
| Farigiraf@Sitrus Berry | Aerodactyl | 4.227 | 3.067 | 1.160 | 38 |
| Pawmot@Focus Sash | Mawile | 7.750 | 6.590 | 1.159 | 4 |
| Ceruledge@Colbur Berry | Kingambit | 1.701 | 0.543 | 1.158 | 5 |
| Indeedee-F@Sitrus Berry | Metagross | 2.782 | 1.625 | 1.157 | 6 |
| Farigiraf@Colbur Berry | Primarina | 3.564 | 2.416 | 1.147 | 5 |
| Sneasler@White Herb | Tyranitar | 2.527 | 1.383 | 1.144 | 77 |
| Sneasler@Grassy Seed | Altaria | 1.872 | 0.734 | 1.138 | 4 |
| Empoleon@Life Orb | Sneasler | 1.851 | 0.718 | 1.133 | 4 |
| Garchomp@Life Orb | Glimmora | 2.848 | 1.717 | 1.132 | 13 |
| Sableye@Light Clay | Pelipper | 4.675 | 3.549 | 1.125 | 6 |
| Garchomp@Sitrus Berry | Indeedee-F | 1.576 | 0.456 | 1.120 | 5 |
| Sneasler@Psychic Seed | Venusaur | 1.693 | 0.575 | 1.118 | 20 |
| Kommo-o@Leftovers | Sinistcha | 2.566 | 1.457 | 1.109 | 7 |
| Tyranitar@Tyranitarite | Corviknight | 10.669 | 9.561 | 1.108 | 63 |
| Venusaur@Wide Lens | Garchomp | 3.154 | 2.054 | 1.100 | 7 |
| Sneasler@White Herb | Froslass | 2.822 | 1.732 | 1.090 | 60 |
| Arcanine-Hisui@Life Orb | Sneasler | 2.176 | 1.091 | 1.084 | 5 |
| Sneasler@Psychic Seed | Absol | 2.110 | 1.038 | 1.071 | 11 |
| Kingambit@Occa Berry | Gardevoir | 2.178 | 1.107 | 1.071 | 5 |
| Sneasler@White Herb | Scovillain | 2.195 | 1.132 | 1.063 | 7 |
| Politoed@Mystic Water | Archaludon | 6.588 | 5.528 | 1.060 | 55 |
| Rillaboom@Expert Belt | Archaludon | 1.700 | 0.640 | 1.060 | 7 |
| Excadrill@Life Orb | Milotic | 3.633 | 2.575 | 1.059 | 5 |
| Annihilape@Choice Scarf | Swampert | 3.751 | 2.694 | 1.057 | 6 |
| Glimmora@Glimmoranite | Kommo-o | 3.713 | 2.658 | 1.055 | 12 |
| Sneasler@Life Orb | Kingambit | 2.383 | 1.332 | 1.051 | 4 |
| Rillaboom@Life Orb | Pelipper | 1.572 | 0.525 | 1.047 | 13 |
| Indeedee-F@Rocky Helmet | Armarouge | 6.250 | 5.210 | 1.039 | 90 |
| Ceruledge@Grassy Seed | Staraptor | 6.254 | 5.225 | 1.030 | 52 |
| Volcarona@Grassy Seed | Baxcalibur | 6.261 | 5.232 | 1.029 | 14 |
| Indeedee-F@Rocky Helmet | Blastoise | 4.509 | 3.480 | 1.028 | 17 |
| Indeedee-F@Sitrus Berry | Armarouge | 6.234 | 5.210 | 1.024 | 15 |
| Excadrill@Focus Sash | Corviknight | 12.042 | 11.027 | 1.015 | 60 |
| Sneasler@Focus Sash | Kingambit | 2.340 | 1.332 | 1.007 | 74 |
| Volcarona@Focus Sash | Sneasler | 1.743 | 0.739 | 1.005 | 4 |
| Kommo-o@Leftovers | Delphox | 2.321 | 1.318 | 1.003 | 6 |
| Venusaur@Life Orb | Incineroar | 1.751 | 0.750 | 1.001 | 11 |
| Pawmot@Focus Sash | Baxcalibur | 6.684 | 5.684 | 1.000 | 6 |
| Blaziken@Focus Sash | Raichu | 1.558 | 0.559 | 0.999 | 5 |
| Aerodactyl@Aerodactylite | Farigiraf | 4.065 | 3.067 | 0.998 | 38 |
| Rillaboom@Sitrus Berry | Kingambit | 1.956 | 0.958 | 0.998 | 68 |
| Aerodactyl@Aerodactylite | Charizard | 6.192 | 5.199 | 0.993 | 50 |
| Volcarona@Sitrus Berry | Gardevoir | 1.661 | 0.672 | 0.989 | 4 |
| Kingambit@Focus Sash | Torkoal | 2.758 | 1.772 | 0.987 | 10 |
| Garchomp@Choice Scarf | Sylveon | 2.570 | 1.585 | 0.986 | 28 |
| Pelipper@Sitrus Berry | Dragonite | 1.834 | 0.852 | 0.982 | 5 |
| Kingambit@Focus Sash | Primarina | 1.749 | 0.772 | 0.977 | 5 |
| Blaziken@Blazikenite | Froslass | 3.014 | 2.049 | 0.965 | 5 |
| Farigiraf@Sitrus Berry | Politoed | 3.778 | 2.814 | 0.964 | 70 |
| Rillaboom@Kebia Berry | Kingambit | 1.918 | 0.958 | 0.961 | 5 |
| Politoed@Life Orb | Staraptor | 1.117 | 0.164 | 0.953 | 4 |
| Armarouge@Focus Sash | Annihilape | 4.919 | 3.967 | 0.952 | 5 |
| Primarina@Grassy Seed | Gholdengo | 2.100 | 1.154 | 0.945 | 7 |
| Gholdengo@Grassy Seed | Salamence | 2.341 | 1.403 | 0.938 | 41 |
| Milotic@Leftovers | Absol | 1.717 | 0.781 | 0.935 | 6 |
| Aerodactyl@Focus Sash | Floette-Eternal | 1.252 | 0.319 | 0.934 | 4 |
| Basculegion@Choice Scarf | Golisopod | 2.106 | 1.173 | 0.933 | 63 |
| Empoleon@Leftovers | Rillaboom | 1.832 | 0.903 | 0.930 | 4 |
| Milotic@Sitrus Berry | Hydreigon | 2.345 | 1.416 | 0.929 | 4 |
| Garchomp@Choice Scarf | Delphox | 1.853 | 0.926 | 0.927 | 7 |
| Sirfetch’d@Leek | Golisopod | 4.533 | 3.618 | 0.915 | 8 |
| Gholdengo@Focus Sash | Sneasler | 1.817 | 0.904 | 0.912 | 4 |
| Torkoal@Life Orb | Kingambit | 2.678 | 1.772 | 0.906 | 4 |
| Milotic@Psychic Seed | Golisopod | 1.719 | 0.815 | 0.904 | 13 |
| Glimmora@Focus Sash | Indeedee-F | 1.491 | 0.588 | 0.902 | 9 |
| Venusaur@Wide Lens | Incineroar | 1.652 | 0.750 | 0.901 | 6 |
| Sinistcha@Colbur Berry | Kingambit | 2.159 | 1.258 | 0.901 | 24 |
| Pelipper@Choice Scarf | Rillaboom | 1.425 | 0.525 | 0.900 | 4 |
| Gholdengo@Leftovers | Floette-Eternal | 2.263 | 1.366 | 0.897 | 4 |
| Politoed@Sitrus Berry | Swampert | 2.666 | 1.770 | 0.896 | 12 |
| Baxcalibur@Life Orb | Raichu | 1.703 | 0.808 | 0.894 | 4 |
| Incineroar@Rocky Helmet | Floette-Eternal | 3.500 | 2.609 | 0.891 | 23 |
| Kommo-o@Life Orb | Indeedee-F | 2.767 | 1.879 | 0.888 | 21 |
| Garchomp@Sitrus Berry | Floette-Eternal | 2.190 | 1.305 | 0.886 | 5 |
| Sneasler@Focus Sash | Incineroar | 1.958 | 1.073 | 0.884 | 72 |
| Volcarona@Leftovers | Basculegion | 2.863 | 1.979 | 0.884 | 5 |
| Talonflame@Focus Sash | Raichu | 1.841 | 0.959 | 0.882 | 4 |
| Talonflame@Focus Sash | Basculegion | 3.008 | 2.127 | 0.881 | 4 |
| Basculegion@Mystic Water | Glimmora | 2.956 | 2.076 | 0.880 | 7 |
| Aerodactyl@Aerodactylite | Sylveon | 5.706 | 4.828 | 0.878 | 44 |
| Ninetales-Alola@Focus Sash | Incineroar | 2.075 | 1.202 | 0.873 | 8 |
| Sylveon@Life Orb | Sneasler | 1.225 | 0.353 | 0.872 | 4 |
| Venusaur@Focus Sash | Charizard | 7.964 | 7.093 | 0.871 | 64 |
| Garchomp@Life Orb | Kingambit | 2.252 | 1.386 | 0.865 | 56 |
| Garchomp@Garchompite Z | Lucario | 2.419 | 1.555 | 0.864 | 15 |
| Torkoal@Charcoal | Hatterene | 10.411 | 9.547 | 0.864 | 19 |
| Incineroar@Chople Berry | Baxcalibur | 2.034 | 1.172 | 0.861 | 7 |
| Blaziken@Blazikenite | Primarina | 4.751 | 3.898 | 0.853 | 4 |
| Garchomp@Garchompite Z | Torkoal | 1.675 | 0.824 | 0.851 | 12 |
| Glimmora@Glimmoranite | Dragapult | 2.993 | 2.143 | 0.850 | 8 |
| Sinistcha@Sitrus Berry | Golisopod | 2.088 | 1.239 | 0.849 | 11 |
| Rillaboom@Life Orb | Metagross | 1.525 | 0.676 | 0.849 | 7 |
| Garchomp@Garchompite | Kingambit | 2.231 | 1.386 | 0.845 | 5 |
| Indeedee-F@Rocky Helmet | Kommo-o | 2.723 | 1.879 | 0.844 | 25 |
| Rotom-Heat@Sitrus Berry | Indeedee-F | 2.461 | 1.619 | 0.842 | 4 |
| Gholdengo@Grassy Seed | Froslass | 1.135 | 0.293 | 0.841 | 5 |
| Garchomp@Choice Scarf | Sinistcha | 1.341 | 0.500 | 0.840 | 5 |
| Tyranitar@Tyranitarite | Excadrill | 11.441 | 10.601 | 0.840 | 206 |
| Hippowdon@Leftovers | Kingambit | 4.069 | 3.231 | 0.838 | 5 |
| Indeedee-F@Sitrus Berry | Kingambit | 1.770 | 0.935 | 0.834 | 17 |
| Garchomp@Choice Scarf | Glimmora | 2.551 | 1.717 | 0.834 | 9 |
| Incineroar@Passho Berry | Kommo-o | 2.140 | 1.309 | 0.831 | 7 |
| Kingambit@Life Orb | Raichu | 1.582 | 0.753 | 0.829 | 72 |
| Kingambit@Occa Berry | Indeedee-F | 1.764 | 0.935 | 0.828 | 9 |
| Sneasler@Grassy Seed | Rillaboom | 1.832 | 1.005 | 0.827 | 423 |
| Basculegion@Choice Scarf | Gardevoir | 3.300 | 2.473 | 0.827 | 53 |
| Rillaboom@Grassy Seed | Golisopod | 1.374 | 0.549 | 0.825 | 4 |
| Kommo-o@Life Orb | Indeedee | 2.520 | 1.696 | 0.824 | 6 |
| Ninetales-Alola@Choice Scarf | Kingambit | 1.796 | 0.973 | 0.823 | 7 |
| Indeedee@Focus Sash | Pelipper | 1.314 | 0.493 | 0.820 | 5 |
| Aerodactyl@Focus Sash | Basculegion | 1.425 | 0.608 | 0.818 | 5 |
| Sneasler@Grassy Seed | Floette-Eternal | 2.424 | 1.608 | 0.816 | 131 |
| Sneasler@Grassy Seed | Lucario | 1.604 | 0.791 | 0.812 | 17 |
| Indeedee-F@Rocky Helmet | Gengar | 1.684 | 0.876 | 0.808 | 18 |
| Glimmora@Focus Sash | Staraptor | 1.761 | 0.955 | 0.806 | 7 |
| Sneasler@Psychic Seed | Pelipper | 1.433 | 0.627 | 0.806 | 45 |
| Volcarona@Grassy Seed | Primarina | 1.946 | 1.147 | 0.799 | 5 |
| Politoed@Sitrus Berry | Lucario | 1.495 | 0.698 | 0.798 | 4 |
| Basculegion@Choice Scarf | Venusaur | 1.425 | 0.629 | 0.796 | 13 |
| Kingambit@Chople Berry | Whimsicott | 2.172 | 1.376 | 0.796 | 33 |
| Garchomp@Garchompite | Sneasler | 1.614 | 0.821 | 0.793 | 6 |
| Basculegion@Life Orb | Pawmot | 2.243 | 1.451 | 0.792 | 7 |
| Rillaboom@Life Orb | Delphox | 1.411 | 0.621 | 0.791 | 4 |
| Incineroar@Passho Berry | Swampert | 1.533 | 0.744 | 0.790 | 6 |
| Kingambit@Focus Sash | Volcarona | 1.757 | 0.968 | 0.790 | 16 |
| Sneasler@White Herb | Volcarona | 1.527 | 0.739 | 0.789 | 42 |
| Garchomp@Garchompite | Incineroar | 2.187 | 1.401 | 0.786 | 6 |
| Pelipper@Choice Scarf | Archaludon | 6.161 | 5.377 | 0.784 | 4 |
| Milotic@Sitrus Berry | Staraptor | 2.991 | 2.209 | 0.782 | 54 |
| Rillaboom@Life Orb | Golisopod | 1.329 | 0.549 | 0.780 | 16 |
| Farigiraf@Grassy Seed | Arcanine-Hisui | 1.232 | 0.452 | 0.779 | 11 |
| Sinistcha@Occa Berry | Sneasler | 1.869 | 1.091 | 0.778 | 11 |
| Maushold@Focus Sash | Rillaboom | 1.393 | 0.615 | 0.778 | 6 |
| Kommo-o@Leftovers | Swampert | 1.793 | 1.018 | 0.775 | 6 |
| Indeedee@Focus Sash | Sylveon | 1.036 | 0.261 | 0.775 | 4 |
| Armarouge@Life Orb | Absol | 4.182 | 3.407 | 0.775 | 6 |
| Incineroar@Sitrus Berry | Aegislash | 2.222 | 1.451 | 0.771 | 5 |
| Hydreigon@Choice Scarf | Metagross | 4.184 | 3.413 | 0.771 | 5 |
| Rillaboom@Occa Berry | Lucario | 2.299 | 1.529 | 0.770 | 4 |
| Rillaboom@Eject Button | Froslass | 2.177 | 1.410 | 0.767 | 12 |
| Sneasler@Focus Sash | Ninetales-Alola | 1.605 | 0.838 | 0.767 | 4 |
| Indeedee-F@Colbur Berry | Corviknight | 1.413 | 0.647 | 0.766 | 6 |
| Sirfetch’d@Leek | Glimmora | 6.569 | 5.804 | 0.766 | 4 |
| Garchomp@Garchompite Z | Metagross | 2.216 | 1.451 | 0.765 | 28 |
| Blastoise@Blastoisinite | Maushold | 19.599 | 18.840 | 0.760 | 13 |
| Charizard@Charizardite X | Sneasler | 1.320 | 0.562 | 0.758 | 5 |
| Indeedee-F@Psychic Seed | Incineroar | 1.272 | 0.516 | 0.757 | 31 |
| Sinistcha@Occa Berry | Incineroar | 2.606 | 1.852 | 0.754 | 11 |
| Rillaboom@Occa Berry | Incineroar | 2.005 | 1.252 | 0.754 | 43 |
| Garchomp@Choice Scarf | Floette-Eternal | 2.057 | 1.305 | 0.752 | 24 |
| Whimsicott@Occa Berry | Arcanine-Hisui | 1.067 | 0.315 | 0.752 | 4 |
| Rotom-Heat@Sitrus Berry | Sneasler | 1.818 | 1.067 | 0.751 | 7 |
| Kleavor@Focus Sash | Garchomp | 2.199 | 1.452 | 0.746 | 4 |
| Indeedee-F@Colbur Berry | Annihilape | 2.684 | 1.938 | 0.746 | 8 |
| Corviknight@Leftovers | Raichu | 1.120 | 0.377 | 0.743 | 5 |
| Sneasler@White Herb | Dragonite | 2.304 | 1.562 | 0.742 | 26 |
| Corviknight@Psychic Seed | Salamence | 3.101 | 2.359 | 0.742 | 43 |
| Indeedee@Focus Sash | Archaludon | 1.142 | 0.403 | 0.740 | 6 |
| Milotic@Grassy Seed | Rillaboom | 1.832 | 1.093 | 0.739 | 20 |
| Klefki@Light Clay | Salamence | 3.317 | 2.585 | 0.732 | 7 |
| Volcarona@Grassy Seed | Metagross | 2.383 | 1.652 | 0.732 | 15 |
| Basculegion@Life Orb | Sylveon | 1.309 | 0.578 | 0.732 | 25 |
| Basculegion@Mystic Water | Indeedee-F | 1.987 | 1.256 | 0.731 | 24 |
| Incineroar@Sitrus Berry | Floette-Eternal | 3.340 | 2.609 | 0.731 | 237 |
| Kingambit@Life Orb | Floette-Eternal | 2.118 | 1.387 | 0.731 | 49 |
| Ninetales-Alola@Never-Melt Ice | Kingambit | 1.700 | 0.973 | 0.727 | 4 |
| Basculegion@Focus Sash | Indeedee-F | 1.982 | 1.256 | 0.726 | 9 |
| Armarouge@Life Orb | Blastoise | 2.791 | 2.066 | 0.725 | 4 |
| Sirfetch’d@Leek | Farigiraf | 3.190 | 2.466 | 0.724 | 6 |
| Espathra@Grassy Seed | Rillaboom | 1.832 | 1.111 | 0.722 | 6 |
| Politoed@Life Orb | Garchomp | 1.228 | 0.506 | 0.722 | 4 |
| Indeedee@Focus Sash | Floette-Eternal | 1.010 | 0.291 | 0.720 | 5 |
| Farigiraf@Sitrus Berry | Grimmsnarl | 3.430 | 2.710 | 0.720 | 49 |
| Sneasler@Grassy Seed | Arcanine-Hisui | 1.810 | 1.091 | 0.719 | 155 |
| Baxcalibur@Life Orb | Incineroar | 1.887 | 1.172 | 0.715 | 4 |
| Kommo-o@Leftovers | Incineroar | 2.023 | 1.309 | 0.714 | 42 |
| Swampert@Swampertite | Sableye | 9.010 | 8.301 | 0.709 | 10 |
| Armarouge@Focus Sash | Milotic | 2.592 | 1.886 | 0.706 | 21 |
| Hatterene@Life Orb | Blaziken | 6.099 | 5.394 | 0.705 | 5 |
| Whimsicott@Occa Berry | Garchomp | 3.004 | 2.298 | 0.705 | 7 |
| Kingambit@Life Orb | Arcanine-Hisui | 1.816 | 1.111 | 0.705 | 69 |
| Garchomp@Life Orb | Talonflame | 2.690 | 1.988 | 0.702 | 4 |
| Politoed@Sitrus Berry | Rillaboom | 1.582 | 0.880 | 0.701 | 80 |
| Kingambit@Chople Berry | Basculegion | 2.066 | 1.372 | 0.694 | 94 |
| Glimmora@Glimmoranite | Pawmot | 2.443 | 1.749 | 0.694 | 6 |
| Indeedee-F@Sitrus Berry | Gardevoir | 6.022 | 5.333 | 0.690 | 18 |
| Kingambit@Black Glasses | Indeedee-F | 1.623 | 0.935 | 0.687 | 30 |
| Milotic@Psychic Seed | Whimsicott | 0.966 | 0.279 | 0.687 | 4 |
| Charizard@Charizardite X | Incineroar | 1.395 | 0.710 | 0.686 | 4 |
| Farigiraf@Colbur Berry | Camerupt | 5.931 | 5.253 | 0.679 | 7 |
| Basculegion@Life Orb | Kingambit | 2.050 | 1.372 | 0.678 | 87 |
| Sneasler@Grassy Seed | Gholdengo | 1.581 | 0.904 | 0.677 | 190 |
| Indeedee-F@Colbur Berry | Pelipper | 1.882 | 1.207 | 0.675 | 36 |
| Milotic@Sitrus Berry | Gholdengo | 2.218 | 1.543 | 0.675 | 100 |
| Torkoal@Charcoal | Blaziken | 8.080 | 7.410 | 0.670 | 11 |
| Basculegion@Choice Scarf | Archaludon | 1.651 | 0.986 | 0.665 | 52 |
| Rillaboom@Kebia Berry | Raichu | 2.272 | 1.607 | 0.665 | 6 |
| Incineroar@Passho Berry | Froslass | 1.198 | 0.535 | 0.662 | 7 |
| Kommo-o@Leftovers | Politoed | 2.467 | 1.805 | 0.662 | 14 |
| Volcarona@Grassy Seed | Incineroar | 1.875 | 1.214 | 0.661 | 55 |
| Rillaboom@Sitrus Berry | Ninetales-Alola | 1.766 | 1.107 | 0.659 | 5 |
| Whimsicott@Fairy Feather | Kingambit | 2.034 | 1.376 | 0.658 | 5 |
| Basculegion@Life Orb | Volcarona | 2.632 | 1.979 | 0.653 | 30 |
| Farigiraf@Grassy Seed | Garchomp | 2.386 | 1.733 | 0.653 | 14 |
| Basculegion@Mystic Water | Indeedee | 1.062 | 0.412 | 0.649 | 4 |
| Garchomp@Life Orb | Farigiraf | 2.382 | 1.733 | 0.649 | 35 |
| Pelipper@Damp Rock | Archaludon | 6.026 | 5.377 | 0.649 | 6 |
| Politoed@Sitrus Berry | Kommo-o | 2.450 | 1.805 | 0.645 | 11 |
| Charizard@Charizardite X | Rillaboom | 1.004 | 0.363 | 0.641 | 5 |
| Farigiraf@Grassy Seed | Raichu | 1.083 | 0.442 | 0.641 | 9 |
| Indeedee@Choice Scarf | Metagross | 4.155 | 3.515 | 0.640 | 30 |
| Basculegion@Life Orb | Delphox | 1.092 | 0.453 | 0.639 | 6 |
| Sneasler@Grassy Seed | Raichu | 1.415 | 0.777 | 0.638 | 143 |
| Sinistcha@Sitrus Berry | Staraptor | 1.170 | 0.532 | 0.638 | 4 |
| Rillaboom@Life Orb | Dragonite | 1.718 | 1.081 | 0.637 | 4 |
| Sinistcha@Sitrus Berry | Basculegion | 0.996 | 0.361 | 0.635 | 4 |
| Gholdengo@Grassy Seed | Raichu | 2.901 | 2.267 | 0.634 | 39 |
| Indeedee-F@Rocky Helmet | Gallade | 4.379 | 3.747 | 0.632 | 5 |
| Rillaboom@Sitrus Berry | Dragapult | 0.954 | 0.322 | 0.632 | 4 |
| Volcarona@Leftovers | Sneasler | 1.368 | 0.739 | 0.629 | 6 |
| Indeedee-F@Rocky Helmet | Metagross | 2.254 | 1.625 | 0.629 | 30 |
| Glimmora@Glimmoranite | Ninetales-Alola | 3.002 | 2.374 | 0.628 | 5 |
| Rillaboom@Sitrus Berry | Salamence | 1.860 | 1.234 | 0.626 | 84 |
| Torkoal@Charcoal | Gallade | 7.543 | 6.917 | 0.626 | 4 |
| Annihilape@Choice Scarf | Pelipper | 2.221 | 1.595 | 0.626 | 9 |
| Incineroar@Sitrus Berry | Rotom-Wash | 1.797 | 1.173 | 0.624 | 5 |
| Armarouge@Life Orb | Venusaur | 1.089 | 0.466 | 0.623 | 4 |
| Farigiraf@Colbur Berry | Incineroar | 1.660 | 1.038 | 0.622 | 27 |
| Incineroar@Chople Berry | Swampert | 1.365 | 0.744 | 0.622 | 6 |
| Primarina@Grassy Seed | Rillaboom | 1.832 | 1.211 | 0.622 | 11 |
| Archaludon@Chople Berry | Rillaboom | 1.260 | 0.640 | 0.620 | 10 |
| Incineroar@Rocky Helmet | Whimsicott | 1.405 | 0.785 | 0.620 | 4 |
| Swampert@Swampertite | Pelipper | 7.821 | 7.205 | 0.615 | 100 |
| Sneasler@Psychic Seed | Torkoal | 1.368 | 0.752 | 0.615 | 17 |
| Farigiraf@Sitrus Berry | Charizard | 2.997 | 2.383 | 0.614 | 106 |
| Pawmot@Focus Sash | Talonflame | 4.085 | 3.474 | 0.611 | 4 |
| Indeedee-F@Rocky Helmet | Staraptor | 1.639 | 1.029 | 0.610 | 46 |
| Incineroar@Sitrus Berry | Vanilluxe | 1.755 | 1.146 | 0.609 | 4 |
| Toxapex@Leftovers | Charizard | 6.194 | 5.586 | 0.608 | 13 |
| Indeedee-F@Colbur Berry | Golisopod | 2.047 | 1.442 | 0.605 | 46 |
| Basculegion@Focus Sash | Sylveon | 1.182 | 0.578 | 0.604 | 4 |
| Basculegion@Life Orb | Dragonite | 1.812 | 1.210 | 0.601 | 8 |
| Incineroar@Chople Berry | Pelipper | 1.131 | 0.530 | 0.601 | 11 |
| Indeedee-F@Colbur Berry | Mawile | 2.044 | 1.443 | 0.600 | 4 |
| Incineroar@Rocky Helmet | Salamence | 1.269 | 0.671 | 0.598 | 21 |
| Ceruledge@Grassy Seed | Milotic | 5.572 | 4.975 | 0.597 | 57 |
| Kingambit@Chople Berry | Salamence | 1.790 | 1.194 | 0.597 | 162 |
| Indeedee-F@Colbur Berry | Venusaur | 1.807 | 1.211 | 0.595 | 12 |
| Kingambit@Black Glasses | Incineroar | 1.397 | 0.804 | 0.593 | 41 |
| Incineroar@Chople Berry | Milotic | 1.039 | 0.446 | 0.593 | 16 |
| Garchomp@Life Orb | Gardevoir | 1.191 | 0.599 | 0.592 | 11 |
| Hydreigon@Choice Scarf | Staraptor | 1.803 | 1.213 | 0.590 | 5 |
| Garchomp@Sitrus Berry | Charizard | 3.326 | 2.736 | 0.590 | 8 |
| Indeedee-F@Colbur Berry | Lucario | 1.362 | 0.774 | 0.588 | 6 |
| Annihilape@Choice Scarf | Archaludon | 2.078 | 1.493 | 0.585 | 12 |
| Primarina@Mystic Water | Gholdengo | 1.740 | 1.154 | 0.585 | 4 |
| Incineroar@Sitrus Berry | Blastoise | 2.187 | 1.604 | 0.583 | 20 |
| Rillaboom@Life Orb | Lucario | 2.111 | 1.529 | 0.582 | 4 |
| Incineroar@Lum Berry | Rillaboom | 1.832 | 1.252 | 0.581 | 4 |
| Garchomp@Choice Scarf | Whimsicott | 2.877 | 2.298 | 0.579 | 14 |
| Dragapult@Life Orb | Armarouge | 7.524 | 6.946 | 0.578 | 36 |
| Sneasler@Focus Sash | Garchomp | 1.397 | 0.821 | 0.576 | 30 |
| Garchomp@Garchompite Z | Golisopod | 1.265 | 0.690 | 0.576 | 36 |
| Indeedee-F@Rocky Helmet | Talonflame | 1.897 | 1.326 | 0.571 | 7 |
| Glimmora@Focus Sash | Raichu | 1.025 | 0.455 | 0.570 | 8 |
| Rillaboom@Kebia Berry | Sneasler | 1.572 | 1.005 | 0.567 | 7 |
| Milotic@Grassy Seed | Kingambit | 0.825 | 0.258 | 0.566 | 4 |
| Kingambit@Focus Sash | Armarouge | 1.454 | 0.891 | 0.563 | 13 |
| Indeedee-F@Colbur Berry | Sneasler | 1.695 | 1.133 | 0.562 | 121 |
| Garchomp@Sitrus Berry | Basculegion | 1.701 | 1.140 | 0.561 | 5 |
| Rillaboom@Sitrus Berry | Basculegion | 1.465 | 0.907 | 0.558 | 35 |
| Whimsicott@Occa Berry | Kingambit | 1.934 | 1.376 | 0.558 | 7 |
| Sinistcha@Colbur Berry | Incineroar | 2.409 | 1.852 | 0.557 | 33 |
| Rillaboom@Life Orb | Froslass | 1.967 | 1.410 | 0.557 | 12 |
| Basculegion@Focus Sash | Floette-Eternal | 1.853 | 1.297 | 0.556 | 4 |
| Pelipper@Focus Sash | Gardevoir | 1.714 | 1.163 | 0.551 | 23 |
| Sneasler@Psychic Seed | Milotic | 1.185 | 0.635 | 0.550 | 60 |
| Goodra-Hisui@Leftovers | Floette-Eternal | 5.767 | 5.217 | 0.550 | 5 |
| Glimmora@Focus Sash | Milotic | 0.816 | 0.269 | 0.547 | 4 |
| Kingambit@Chople Berry | Dragonite | 1.315 | 0.770 | 0.545 | 10 |
| Glimmora@Glimmoranite | Baxcalibur | 2.676 | 2.130 | 0.545 | 5 |
| Sinistcha@Colbur Berry | Excadrill | 1.955 | 1.411 | 0.544 | 8 |
| Torkoal@Charcoal | Mawile | 6.523 | 5.982 | 0.541 | 6 |
| Whimsicott@Fairy Feather | Sneasler | 0.931 | 0.392 | 0.540 | 4 |
| Kleavor@Focus Sash | Kingambit | 1.415 | 0.877 | 0.539 | 4 |
| Sneasler@Psychic Seed | Hydreigon | 1.450 | 0.912 | 0.538 | 6 |
| Rillaboom@Miracle Seed | Goodra-Hisui | 1.948 | 1.411 | 0.537 | 6 |
| Sneasler@White Herb | Baxcalibur | 1.699 | 1.163 | 0.536 | 16 |
| Sinistcha@Coba Berry | Sneasler | 1.626 | 1.091 | 0.535 | 9 |
| Incineroar@Chople Berry | Torkoal | 1.136 | 0.602 | 0.534 | 4 |
| Rillaboom@Leftovers | Incineroar | 1.782 | 1.252 | 0.531 | 7 |
| Tyranitar@Tyranitarite | Indeedee | 6.434 | 5.904 | 0.530 | 100 |
| Blaziken@Blazikenite | Kingambit | 2.506 | 1.978 | 0.528 | 20 |
| Maushold@Chople Berry | Indeedee-F | 1.871 | 1.347 | 0.524 | 7 |
| Armarouge@Life Orb | Tyranitar | 1.236 | 0.713 | 0.523 | 9 |
| Garchomp@Garchompite Z | Pawmot | 1.491 | 0.969 | 0.522 | 7 |
| Pawmot@Focus Sash | Primarina | 3.475 | 2.955 | 0.520 | 5 |
| Rillaboom@Kebia Berry | Salamence | 1.753 | 1.234 | 0.519 | 5 |
| Incineroar@Sitrus Berry | Sinistcha | 2.371 | 1.852 | 0.518 | 58 |
| Maushold@Chople Berry | Sneasler | 1.629 | 1.112 | 0.517 | 14 |
| Toxapex@Leftovers | Garchomp | 5.237 | 4.723 | 0.514 | 14 |
| Indeedee-F@Psychic Seed | Kingambit | 1.449 | 0.935 | 0.513 | 33 |
| Whimsicott@Focus Sash | Sirfetch’d | 5.812 | 5.299 | 0.513 | 5 |
| Venusaur@Focus Sash | Gardevoir | 2.377 | 1.866 | 0.511 | 13 |
| Pelipper@Focus Sash | Basculegion | 2.274 | 1.764 | 0.510 | 63 |
| Sinistcha@Sitrus Berry | Archaludon | 1.601 | 1.095 | 0.506 | 8 |
| Baxcalibur@Baxcalibrite | Glimmora | 2.635 | 2.130 | 0.505 | 6 |
| Gholdengo@Choice Scarf | Floette-Eternal | 1.869 | 1.366 | 0.503 | 6 |
| Rillaboom@Sitrus Berry | Sneasler | 1.504 | 1.005 | 0.499 | 90 |
| Basculegion@Focus Sash | Incineroar | 1.440 | 0.942 | 0.498 | 10 |
| Sinistcha@Sitrus Berry | Salamence | 0.911 | 0.415 | 0.496 | 8 |
| Corviknight@Leftovers | Indeedee-F | 1.142 | 0.647 | 0.495 | 4 |
| Sneasler@White Herb | Indeedee | 2.612 | 2.118 | 0.493 | 58 |
| Kingambit@Occa Berry | Floette-Eternal | 1.880 | 1.387 | 0.493 | 5 |
| Milotic@Grassy Seed | Gholdengo | 2.034 | 1.543 | 0.491 | 11 |
| Milotic@Grassy Seed | Arcanine-Hisui | 1.616 | 1.125 | 0.491 | 6 |
| Farigiraf@Grassy Seed | Salamence | 0.950 | 0.461 | 0.489 | 13 |
| Sneasler@White Herb | Blaziken | 0.855 | 0.367 | 0.489 | 5 |
| Indeedee-F@Rocky Helmet | Milotic | 1.619 | 1.135 | 0.485 | 56 |
| Milotic@Leftovers | Maushold | 1.096 | 0.612 | 0.484 | 4 |
| Kingambit@Focus Sash | Glimmora | 2.029 | 1.545 | 0.484 | 10 |
| Sneasler@Psychic Seed | Blastoise | 2.004 | 1.521 | 0.483 | 10 |
| Indeedee-F@Colbur Berry | Basculegion | 1.738 | 1.256 | 0.481 | 46 |
| Talonflame@Focus Sash | Rillaboom | 1.117 | 0.637 | 0.480 | 5 |
| Hydreigon@Choice Scarf | Milotic | 1.896 | 1.416 | 0.480 | 8 |
| Swampert@Swampertite | Grimmsnarl | 6.090 | 5.611 | 0.479 | 39 |
| Farigiraf@Sitrus Berry | Sylveon | 2.290 | 1.812 | 0.478 | 82 |
| Kingambit@Black Glasses | Gengar | 1.214 | 0.737 | 0.478 | 8 |
| Kingambit@Life Orb | Sneasler | 1.808 | 1.332 | 0.476 | 142 |
| Swampert@Swampertite | Archaludon | 6.023 | 5.549 | 0.474 | 101 |
| Lycanroc-Dusk@Focus Sash | Froslass | 9.398 | 8.924 | 0.474 | 9 |
| Volcarona@Sitrus Berry | Indeedee-F | 0.820 | 0.346 | 0.474 | 5 |
| Rillaboom@Grassy Seed | Garchomp | 1.271 | 0.798 | 0.473 | 4 |
| Swampert@Swampertite | Venusaur | 5.989 | 5.518 | 0.471 | 24 |
| Armarouge@Life Orb | Excadrill | 1.217 | 0.746 | 0.471 | 7 |
| Armarouge@Life Orb | Swampert | 1.012 | 0.542 | 0.470 | 5 |
| Indeedee-F@Psychic Seed | Dragonite | 1.201 | 0.733 | 0.469 | 5 |
| Garchomp@Sitrus Berry | Raichu | 1.008 | 0.540 | 0.468 | 4 |
| Incineroar@Rocky Helmet | Sylveon | 1.018 | 0.550 | 0.468 | 7 |
| Incineroar@Sitrus Berry | Volcarona | 1.682 | 1.214 | 0.468 | 65 |
| Excadrill@Life Orb | Sneasler | 1.845 | 1.377 | 0.468 | 8 |
| Incineroar@Sitrus Berry | Dragonite | 2.035 | 1.570 | 0.465 | 34 |
| Indeedee-F@Rocky Helmet | Delphox | 1.552 | 1.088 | 0.465 | 14 |
| Indeedee@Choice Scarf | Salamence | 2.601 | 2.136 | 0.464 | 106 |
| Volcarona@Sitrus Berry | Salamence | 1.306 | 0.844 | 0.462 | 12 |
| Hatterene@Life Orb | Torkoal | 10.006 | 9.547 | 0.459 | 18 |
| Sneasler@Psychic Seed | Golisopod | 0.959 | 0.500 | 0.458 | 40 |
| Kommo-o@Leftovers | Froslass | 1.457 | 0.998 | 0.458 | 6 |
| Indeedee-F@Rocky Helmet | Sinistcha | 1.638 | 1.179 | 0.458 | 17 |
| Sableye@Roseli Berry | Pelipper | 4.008 | 3.549 | 0.458 | 4 |
| Kommo-o@Leftovers | Kingambit | 1.423 | 0.968 | 0.455 | 27 |
| Sneasler@Psychic Seed | Charizard | 1.016 | 0.562 | 0.454 | 42 |
| Torkoal@Charcoal | Annihilape | 5.460 | 5.007 | 0.453 | 8 |
| Incineroar@Sitrus Berry | Delphox | 2.381 | 1.931 | 0.450 | 52 |
| Hydreigon@Choice Scarf | Arcanine-Hisui | 1.374 | 0.925 | 0.450 | 6 |
| Sinistcha@Colbur Berry | Garchomp | 0.950 | 0.500 | 0.449 | 7 |
| Whimsicott@Focus Sash | Floette-Eternal | 2.085 | 1.636 | 0.449 | 31 |
| Garchomp@Life Orb | Indeedee-F | 0.902 | 0.456 | 0.446 | 19 |
| Excadrill@Life Orb | Salamence | 3.032 | 2.586 | 0.446 | 9 |
| Politoed@Choice Scarf | Archaludon | 5.973 | 5.528 | 0.445 | 4 |
| Rillaboom@Miracle Seed | Ceruledge | 2.136 | 1.691 | 0.445 | 67 |
| Armarouge@Life Orb | Annihilape | 4.410 | 3.967 | 0.443 | 6 |
| Delphox@Delphoxite | Maushold | 10.079 | 9.636 | 0.443 | 16 |
| Incineroar@Rocky Helmet | Gholdengo | 1.387 | 0.945 | 0.442 | 22 |
| Vanilluxe@Choice Scarf | Raichu | 2.215 | 1.773 | 0.442 | 5 |
| Milotic@Leftovers | Volcarona | 1.140 | 0.700 | 0.440 | 16 |
| Annihilape@Choice Scarf | Charizard | 2.156 | 1.717 | 0.439 | 10 |
| Incineroar@Chople Berry | Whimsicott | 1.224 | 0.785 | 0.439 | 8 |
| Indeedee@Focus Sash | Kingambit | 0.685 | 0.250 | 0.435 | 10 |
| Basculegion@Mystic Water | Arcanine-Hisui | 0.965 | 0.532 | 0.433 | 16 |
| Indeedee-F@Sitrus Berry | Charizard | 1.226 | 0.794 | 0.432 | 6 |
| Volcarona@Grassy Seed | Floette-Eternal | 1.703 | 1.271 | 0.432 | 19 |
| Politoed@Life Orb | Kingambit | 0.606 | 0.175 | 0.432 | 4 |
| Rillaboom@Life Orb | Farigiraf | 1.030 | 0.601 | 0.429 | 14 |
| Rillaboom@Occa Berry | Charizard | 0.791 | 0.363 | 0.428 | 9 |
| Baxcalibur@Baxcalibrite | Ceruledge | 2.228 | 1.801 | 0.427 | 4 |
| Whimsicott@Fairy Feather | Basculegion | 3.191 | 2.767 | 0.424 | 5 |
| Primarina@Life Orb | Kingambit | 1.196 | 0.772 | 0.423 | 8 |
| Pelipper@Focus Sash | Golisopod | 4.142 | 3.720 | 0.422 | 102 |
| Sneasler@Psychic Seed | Tyranitar | 1.799 | 1.383 | 0.416 | 58 |
| Garchomp@Garchompite Z | Politoed | 0.921 | 0.506 | 0.415 | 12 |
| Garchomp@Garchompite Z | Sneasler | 1.233 | 0.821 | 0.412 | 118 |
| Garchomp@Garchompite Z | Rillaboom | 1.208 | 0.798 | 0.410 | 147 |
| Aerodactyl@Focus Sash | Incineroar | 1.363 | 0.954 | 0.409 | 8 |
| Pelipper@Sitrus Berry | Annihilape | 2.004 | 1.595 | 0.409 | 4 |
| Incineroar@Chople Berry | Froslass | 0.943 | 0.535 | 0.408 | 5 |
| Farigiraf@Sitrus Berry | Sirfetch’d | 2.873 | 2.466 | 0.407 | 6 |
| Torkoal@Charcoal | Typhlosion-Hisui | 4.905 | 4.498 | 0.407 | 4 |
| Archaludon@Leftovers | Klefki | 5.808 | 5.402 | 0.407 | 7 |
| Volcarona@Grassy Seed | Garchomp | 2.562 | 2.155 | 0.407 | 40 |
| Incineroar@Sitrus Berry | Aerodactyl | 1.360 | 0.954 | 0.406 | 26 |
| Farigiraf@Sitrus Berry | Kangaskhan | 4.778 | 4.372 | 0.406 | 4 |
| Ninetales-Alola@Light Clay | Gholdengo | 0.970 | 0.565 | 0.405 | 5 |
| Basculegion@Mystic Water | Kingambit | 1.775 | 1.372 | 0.403 | 29 |
| Volcarona@Grassy Seed | Ninetales-Alola | 2.523 | 2.120 | 0.403 | 5 |
| Pawmot@Focus Sash | Sinistcha | 2.689 | 2.287 | 0.402 | 5 |
| Pelipper@Focus Sash | Gengar | 1.001 | 0.599 | 0.402 | 9 |
| Basculegion@Choice Scarf | Indeedee-F | 1.657 | 1.256 | 0.401 | 66 |
| Basculegion@Life Orb | Blaziken | 1.479 | 1.079 | 0.400 | 4 |
| Milotic@Sitrus Berry | Raichu | 1.597 | 1.199 | 0.398 | 55 |
| Indeedee-F@Sitrus Berry | Sneasler | 1.531 | 1.133 | 0.398 | 26 |
| Blastoise@Blastoisinite | Sinistcha | 10.149 | 9.755 | 0.393 | 20 |
| Sneasler@Psychic Seed | Excadrill | 1.769 | 1.377 | 0.392 | 48 |
| Sneasler@Grassy Seed | Incineroar | 1.465 | 1.073 | 0.391 | 187 |
| Pelipper@Focus Sash | Indeedee-F | 1.596 | 1.207 | 0.389 | 52 |
| Archaludon@Leftovers | Starmie | 5.544 | 5.156 | 0.388 | 4 |
| Glimmora@Focus Sash | Garchomp | 2.103 | 1.717 | 0.386 | 10 |
| Baxcalibur@Baxcalibrite | Sinistcha | 2.010 | 1.625 | 0.385 | 5 |
| Ceruledge@Grassy Seed | Baxcalibur | 2.186 | 1.801 | 0.385 | 4 |
| Garchomp@Garchompite Z | Incineroar | 1.785 | 1.401 | 0.384 | 119 |
| Incineroar@Sitrus Berry | Absol | 1.501 | 1.118 | 0.383 | 12 |
| Volcarona@Grassy Seed | Froslass | 1.948 | 1.566 | 0.382 | 11 |
| Excadrill@Focus Sash | Indeedee | 7.086 | 6.706 | 0.380 | 94 |
| Kingambit@Focus Sash | Blastoise | 1.590 | 1.210 | 0.380 | 4 |
| Venusaur@Life Orb | Sneasler | 0.953 | 0.575 | 0.378 | 7 |
| Aerodactyl@Aerodactylite | Kingambit | 2.615 | 2.237 | 0.378 | 42 |
| Indeedee-F@Rocky Helmet | Aerodactyl | 0.943 | 0.565 | 0.378 | 7 |
| Volcarona@Grassy Seed | Raichu | 1.585 | 1.207 | 0.378 | 37 |
| Excadrill@Focus Sash | Rotom-Heat | 4.431 | 4.057 | 0.373 | 5 |
| Basculegion@Choice Scarf | Ninetales-Alola | 1.069 | 0.696 | 0.373 | 4 |
| Kingambit@Life Orb | Incineroar | 1.177 | 0.804 | 0.373 | 66 |
| Ninetales-Alola@Light Clay | Arcanine-Hisui | 1.029 | 0.656 | 0.373 | 4 |
| Primarina@Life Orb | Golisopod | 1.271 | 0.899 | 0.372 | 5 |
| Milotic@Leftovers | Dragonite | 0.837 | 0.465 | 0.372 | 6 |
| Armarouge@Life Orb | Kingambit | 1.261 | 0.891 | 0.370 | 32 |
| Milotic@Leftovers | Farigiraf | 0.764 | 0.399 | 0.365 | 28 |
| Sirfetch’d@Leek | Gholdengo | 0.921 | 0.556 | 0.365 | 4 |
| Glimmora@Glimmoranite | Whimsicott | 5.082 | 4.719 | 0.362 | 25 |
| Rillaboom@Sitrus Berry | Baxcalibur | 1.502 | 1.140 | 0.362 | 5 |
| Armarouge@Life Orb | Pelipper | 1.103 | 0.741 | 0.361 | 12 |
| Blastoise@Blastoisinite | Delphox | 9.277 | 8.917 | 0.360 | 17 |
| Blaziken@Blazikenite | Farigiraf | 2.198 | 1.839 | 0.359 | 11 |
| Incineroar@Rocky Helmet | Milotic | 0.804 | 0.446 | 0.358 | 7 |
| Archaludon@Leftovers | Grimmsnarl | 6.180 | 5.823 | 0.357 | 126 |
| Rillaboom@Miracle Seed | Staraptor | 1.644 | 1.289 | 0.356 | 230 |
| Volcarona@Grassy Seed | Rillaboom | 1.832 | 1.478 | 0.355 | 99 |
| Sinistcha@Occa Berry | Floette-Eternal | 2.910 | 2.555 | 0.354 | 5 |
| Incineroar@Sitrus Berry | Altaria | 1.430 | 1.076 | 0.354 | 4 |
| Gholdengo@Life Orb | Ceruledge | 2.941 | 2.591 | 0.350 | 57 |
| Milotic@Leftovers | Lucario | 1.098 | 0.749 | 0.349 | 6 |
| Corviknight@Psychic Seed | Sneasler | 2.353 | 2.005 | 0.348 | 46 |
| Tyranitar@Choice Scarf | Rillaboom | 0.934 | 0.586 | 0.348 | 4 |
| Typhlosion-Hisui@Choice Scarf | Basculegion | 2.202 | 1.855 | 0.347 | 5 |
| Incineroar@Sitrus Berry | Primarina | 1.539 | 1.193 | 0.346 | 23 |
| Sinistcha@Kasib Berry | Incineroar | 2.198 | 1.852 | 0.346 | 6 |
| Incineroar@Rocky Helmet | Pelipper | 0.875 | 0.530 | 0.344 | 5 |
| Kingambit@Focus Sash | Blaziken | 2.322 | 1.978 | 0.344 | 5 |
| Milotic@Leftovers | Camerupt | 0.958 | 0.617 | 0.341 | 5 |
| Glimmora@Glimmoranite | Indeedee | 1.565 | 1.225 | 0.341 | 8 |
| Kingambit@Chople Berry | Venusaur | 0.635 | 0.295 | 0.341 | 6 |
| Archaludon@Leftovers | Vivillon | 4.858 | 4.518 | 0.340 | 24 |
| Delphox@Delphoxite | Sinistcha | 11.072 | 10.732 | 0.340 | 55 |
| Garchomp@Choice Scarf | Kingambit | 1.725 | 1.386 | 0.339 | 38 |
| Whimsicott@Focus Sash | Torkoal | 1.468 | 1.131 | 0.337 | 5 |
| Sneasler@Grassy Seed | Salamence | 1.820 | 1.483 | 0.337 | 238 |
| Pawmot@Focus Sash | Froslass | 2.252 | 1.915 | 0.337 | 8 |
| Incineroar@Sitrus Berry | Lucario | 2.385 | 2.050 | 0.335 | 40 |
| Basculegion@Choice Scarf | Metagross | 1.103 | 0.768 | 0.334 | 12 |
| Glimmora@Focus Sash | Gholdengo | 0.723 | 0.391 | 0.332 | 6 |
| Kingambit@Life Orb | Rillaboom | 1.290 | 0.958 | 0.332 | 134 |
| Kingambit@Chople Berry | Gardevoir | 1.438 | 1.107 | 0.331 | 33 |
| Sneasler@White Herb | Talonflame | 1.018 | 0.688 | 0.331 | 4 |
| Toxapex@Leftovers | Sylveon | 3.359 | 3.029 | 0.330 | 7 |
| Aerodactyl@Aerodactylite | Garchomp | 3.983 | 3.654 | 0.330 | 41 |
| Kommo-o@Leftovers | Rillaboom | 1.205 | 0.878 | 0.327 | 47 |
| Ceruledge@Grassy Seed | Raichu | 3.535 | 3.210 | 0.325 | 56 |
| Basculegion@Choice Scarf | Swampert | 0.692 | 0.367 | 0.325 | 7 |
| Sneasler@Psychic Seed | Dragapult | 0.688 | 0.364 | 0.324 | 7 |
| Typhlosion-Hisui@Choice Scarf | Garchomp | 2.054 | 1.730 | 0.323 | 5 |
| Dragapult@Life Orb | Indeedee-F | 4.203 | 3.880 | 0.323 | 61 |
| Venusaur@Wide Lens | Charizard | 7.415 | 7.093 | 0.322 | 11 |
| Milotic@Leftovers | Incineroar | 0.767 | 0.446 | 0.321 | 54 |
| Sinistcha@Sitrus Berry | Indeedee-F | 1.499 | 1.179 | 0.319 | 8 |
| Kingambit@Chople Berry | Swampert | 0.571 | 0.253 | 0.319 | 6 |
| Indeedee-F@Sitrus Berry | Milotic | 1.452 | 1.135 | 0.317 | 8 |
| Alakazam@Alakazite | Sneasler | 1.415 | 1.098 | 0.317 | 4 |
| Indeedee@Choice Scarf | Milotic | 2.014 | 1.700 | 0.314 | 47 |
| Kingambit@Black Glasses | Farigiraf | 1.707 | 1.394 | 0.314 | 24 |
| Pelipper@Sitrus Berry | Archaludon | 5.690 | 5.377 | 0.313 | 98 |
| Sinistcha@Sitrus Berry | Gholdengo | 0.569 | 0.256 | 0.313 | 5 |
| Kingambit@Black Glasses | Golisopod | 0.536 | 0.224 | 0.312 | 8 |
| Indeedee-F@Rocky Helmet | Absol | 2.313 | 2.001 | 0.312 | 10 |
| Sneasler@Psychic Seed | Kommo-o | 0.620 | 0.309 | 0.311 | 11 |
| Whimsicott@Focus Sash | Charizard | 2.857 | 2.545 | 0.311 | 42 |
| Indeedee-F@Sitrus Berry | Tyranitar | 1.129 | 0.819 | 0.310 | 4 |
| Volcarona@Grassy Seed | Basculegion | 2.289 | 1.979 | 0.310 | 35 |
| Sinistcha@Colbur Berry | Milotic | 1.014 | 0.705 | 0.310 | 9 |
| Rillaboom@Life Orb | Archaludon | 0.949 | 0.640 | 0.309 | 10 |
| Volcarona@Leftovers | Salamence | 1.153 | 0.844 | 0.309 | 4 |
| Farigiraf@Grassy Seed | Incineroar | 1.346 | 1.038 | 0.308 | 15 |
| Dragapult@Life Orb | Metagross | 4.011 | 3.703 | 0.308 | 20 |
| Sneasler@Psychic Seed | Whimsicott | 0.700 | 0.392 | 0.308 | 11 |
| Whimsicott@Occa Berry | Staraptor | 1.714 | 1.406 | 0.308 | 4 |
| Vivillon@Focus Sash | Gengar | 15.037 | 14.730 | 0.308 | 31 |
| Garchomp@Garchompite Z | Indeedee | 0.861 | 0.554 | 0.307 | 13 |
| Garchomp@Garchompite Z | Archaludon | 1.137 | 0.833 | 0.304 | 30 |
| Basculegion@Choice Scarf | Armarouge | 0.685 | 0.382 | 0.303 | 10 |
| Garchomp@Garchompite Z | Pelipper | 1.121 | 0.820 | 0.302 | 23 |
| Milotic@Leftovers | Sneasler | 0.936 | 0.635 | 0.301 | 86 |

## 5. Top Triples
| Tokens | Teams | Families | Support | Lift3 | Gain |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Absol + Espathra@Grassy Seed + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 16642.07 | 66.85 |
| Absol@Absolite Z + Espathra@Grassy Seed + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 16642.07 | 66.85 |
| Absol + Espathra@Grassy Seed + Goodra-Hisui | 4 | 4 | 0.2% | 15054.88 | 66.85 |
| Absol@Absolite Z + Espathra@Grassy Seed + Goodra-Hisui | 4 | 4 | 0.2% | 15054.88 | 66.85 |
| Absol + Espathra + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 5527.35 | 66.85 |
| Absol@Absolite Z + Espathra + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 5527.35 | 66.85 |
| Absol + Espathra + Goodra-Hisui | 4 | 4 | 0.2% | 5000.20 | 66.85 |
| Absol@Absolite Z + Espathra + Goodra-Hisui | 4 | 4 | 0.2% | 5000.20 | 66.85 |
| Pawmot@Focus Sash + Politoed@Life Orb + Staraptor@Choice Scarf | 4 | 4 | 0.1% | 3934.05 | 69.35 |
| Pawmot + Politoed@Life Orb + Staraptor@Choice Scarf | 4 | 4 | 0.1% | 3345.56 | 58.97 |
| Glimmora@Glimmoranite + Klefki@Light Clay + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 3067.04 | 32.99 |
| Blastoise@Blastoisinite + Maushold@Chople Berry + Sinistcha@Occa Berry | 6 | 6 | 0.3% | 2919.83 | 45.12 |
| Blastoise + Maushold@Chople Berry + Sinistcha@Occa Berry | 6 | 6 | 0.3% | 2806.66 | 43.37 |
| Garchomp@Life Orb + Klefki@Light Clay + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 2542.74 | 27.35 |
| Glimmora@Glimmoranite + Klefki + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 2390.32 | 32.99 |
| Glimmora + Klefki@Light Clay + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 2195.82 | 23.62 |
| Espathra@Grassy Seed + Floette-Eternal@Floettite + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 2052.85 | 8.25 |
| Espathra@Grassy Seed + Floette-Eternal + Goodra-Hisui@Leftovers | 4 | 4 | 0.2% | 2045.76 | 8.22 |
| Garchomp@Life Orb + Klefki + Volcarona@Sitrus Berry | 7 | 7 | 0.3% | 1981.70 | 27.35 |
| Espathra@Grassy Seed + Floette-Eternal@Floettite + Goodra-Hisui | 4 | 4 | 0.2% | 1857.06 | 8.25 |

## 6. Set Variants and Role Tags
### Rillaboom
- Teams: 1620, weighted support: 54.6%, mega share: 0.0%
- Roles: terrain-setter 99.9%, priority-attack 99.2%, fake-out 99.2%, pivot 28.6%, speed-drop 2.0%, disruption 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Miracle Seed | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | unknown | 709 | 47.8% |
| Miracle Seed | Fake Out, Grassy Glide, U-turn, Wood Hammer | unknown | 194 | 12.5% |
| Sitrus Berry | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | unknown | 64 | 4.3% |
| Eject Button | Fake Out, Grassy Glide, Protect, U-turn | unknown | 56 | 3.7% |
| Life Orb | Fake Out, Grassy Glide, High Horsepower, Wood Hammer | unknown | 49 | 3.3% |
- Other signatures: 28.4%

### Sneasler
- Teams: 1270, weighted support: 41.5%, mega share: 0.0%
- Roles: seed-unburden 56.9%, fake-out 44.1%, speed-drop 15.3%, ally-boost 11.7%, quick-guard 5.7%, priority-blocker 5.7%, setup 3.8%, disruption 0.4%, pivot 0.2%, weather-setter 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| White Herb | Close Combat, Dire Claw, Fake Out, Protect | unknown | 93 | 8.2% |
| Psychic Seed | Close Combat, Dire Claw, Protect, Rock Slide | unknown | 93 | 7.6% |
| Grassy Seed | Close Combat, Dire Claw, Protect, Rock Slide | unknown | 89 | 7.1% |
| Grassy Seed | Close Combat, Dire Claw, Fake Out, Protect | unknown | 84 | 7.0% |
| Grassy Seed | Close Combat, Dire Claw, Protect, Rock Tomb | unknown | 73 | 5.8% |
- Other signatures: 64.3%

### Salamence
- Teams: 940, weighted support: 30.1%, mega share: 99.7%
- Roles: intimidate 99.9%, mega-attacker 98.8%, tailwind 73.0%, setup 0.6%, weather-setter 0.2%, helping-hand 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Salamencite | Draco Meteor, Hyper Voice, Protect, Tailwind | unknown | 369 | 41.7% |
| Salamencite | Draco Meteor, Flamethrower, Hyper Voice, Protect | unknown | 123 | 16.0% |
| Salamencite | Flamethrower, Hyper Voice, Protect, Tailwind | unknown | 82 | 9.3% |
| Salamencite | Double-Edge, Hyper Voice, Protect, Tailwind | unknown | 74 | 7.9% |
| Salamencite | Draco Meteor, Hyper Voice, Protect, Tailwind | fast | 57 | 3.6% |
- Other signatures: 21.5%

### Incineroar
- Teams: 884, weighted support: 29.0%, mega share: 0.0%
- Roles: intimidate 99.9%, fake-out 99.5%, pivot 93.0%, trick-room-abuser 24.6%, helping-hand 7.7%, spa-drop 6.8%, disruption 4.5%, status 0.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Fake Out, Flare Blitz, Parting Shot, Throat Chop | unknown | 236 | 28.4% |
| Sitrus Berry | Darkest Lariat, Fake Out, Flare Blitz, Parting Shot | unknown | 96 | 11.9% |
| Sitrus Berry | Fake Out, Flare Blitz, Helping Hand, Parting Shot | unknown | 38 | 4.5% |
| Rocky Helmet | Fake Out, Flare Blitz, Parting Shot, Throat Chop | unknown | 25 | 3.1% |
| Sitrus Berry | Fake Out, Flare Blitz, Parting Shot, Taunt | unknown | 21 | 3.0% |
- Other signatures: 49.2%

### Gholdengo
- Teams: 849, weighted support: 28.7%, mega share: 0.0%
- Roles: setup 94.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | unknown | 638 | 79.9% |
| Grassy Seed | Make It Rain, Nasty Plot, Protect, Shadow Ball | unknown | 51 | 6.3% |
| Life Orb | Make It Rain, Nasty Plot, Protect, Shadow Ball | fast | 53 | 3.1% |
| Life Orb | Make It Rain, Power Gem, Protect, Shadow Ball | unknown | 15 | 1.7% |
| Choice Scarf | Make It Rain, Power Gem, Shadow Ball, Trick | unknown | 12 | 1.7% |
- Other signatures: 7.4%

### Raichu
- Teams: 711, weighted support: 25.6%, mega share: 99.9%
- Roles: mega-attacker 99.9%, speed-drop 98.3%, fake-out 80.5%, disruption 16.6%, pivot 2.2%, terrain-setter 1.9%, screens 0.3%, setup 0.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Raichunite Y | Fake Out, Focus Blast, Protect, Zap Cannon | unknown | 511 | 75.0% |
| Raichunite Y | Encore, Focus Blast, Protect, Zap Cannon | unknown | 108 | 15.0% |
| Raichunite Y | Fake Out, Focus Blast, Protect, Zap Cannon | fast | 34 | 2.4% |
| Raichunite Y | Focus Blast, Protect, Volt Switch, Zap Cannon | unknown | 5 | 0.8% |
| Raichunite Y | Electroweb, Focus Blast, Protect, Zap Cannon | unknown | 4 | 0.7% |
- Other signatures: 6.2%

### Kingambit
- Teams: 739, weighted support: 24.6%, mega share: 0.0%
- Roles: priority-attack 99.3%, setup 26.0%, trick-room-abuser 18.2%, speed-drop 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Chople Berry | Iron Head, Kowtow Cleave, Low Kick, Sucker Punch | unknown | 139 | 19.0% |
| Life Orb | Kowtow Cleave, Protect, Sucker Punch, Swords Dance | unknown | 102 | 16.3% |
| Focus Sash | Iron Head, Kowtow Cleave, Low Kick, Sucker Punch | unknown | 64 | 9.4% |
| Chople Berry | Iron Head, Kowtow Cleave, Protect, Sucker Punch | unknown | 58 | 8.3% |
| Life Orb | Iron Head, Kowtow Cleave, Protect, Sucker Punch | unknown | 41 | 6.1% |
- Other signatures: 40.9%

### Arcanine-Hisui
- Teams: 611, weighted support: 21.0%, mega share: 0.0%
- Roles: priority-attack 91.1%, intimidate 1.6%, spa-drop 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Extreme Speed, Flare Blitz, Head Smash, Protect | unknown | 481 | 82.7% |
| Focus Sash | Flare Blitz, Head Smash, Protect, Rock Slide | unknown | 43 | 7.0% |
| Focus Sash | Extreme Speed, Flare Blitz, Head Smash, Protect | fast | 41 | 3.3% |
| Focus Sash | Extreme Speed, Flare Blitz, Protect, Rock Slide | unknown | 18 | 2.9% |
| Focus Sash | Close Combat, Flare Blitz, Head Smash, Protect | unknown | 2 | 0.4% |
- Other signatures: 3.7%

### Indeedee-F
- Teams: 553, weighted support: 18.2%, mega share: 0.0%
- Roles: follow-me 99.4%, terrain-setter 99.4%, priority-blocker 99.4%, helping-hand 85.9%, trick-room-setter 79.8%, disruption 8.3%, spa-drop 2.5%, fake-out 1.5%, status 0.7%, screens 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Rocky Helmet | Follow Me, Helping Hand, Psychic, Trick Room | unknown | 95 | 19.0% |
| Colbur Berry | Follow Me, Helping Hand, Psychic, Trick Room | unknown | 81 | 15.6% |
| Psychic Seed | Follow Me, Helping Hand, Psychic, Trick Room | unknown | 47 | 9.3% |
| Rocky Helmet | Follow Me, Helping Hand, Protect, Psychic | unknown | 46 | 9.0% |
| Colbur Berry | Follow Me, Helping Hand, Protect, Psychic | unknown | 18 | 3.6% |
- Other signatures: 43.5%

### Milotic
- Teams: 473, weighted support: 16.3%, mega share: 0.0%
- Roles: speed-drop 45.9%, setup 40.4%, status 39.0%, helping-hand 2.7%, weather-setter 0.5%, screens 0.3%, pivot 0.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Ice Beam, Icy Wind, Protect, Scald | unknown | 60 | 13.7% |
| Sitrus Berry | Ice Beam, Icy Wind, Protect, Scald | unknown | 64 | 12.9% |
| Psychic Seed | Coil, Hypnosis, Muddy Water, Recover | unknown | 51 | 11.9% |
| Sitrus Berry | Coil, Hypnosis, Ice Beam, Muddy Water | unknown | 41 | 10.4% |
| Leftovers | Coil, Hypnosis, Muddy Water, Protect | unknown | 28 | 5.6% |
- Other signatures: 45.5%

### Garchomp
- Teams: 459, weighted support: 15.8%, mega share: 49.3%
- Roles: mega-attacker 49.3%, speed-drop 8.6%, setup 2.2%, weather-setter 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Dragon Claw, Earthquake, Rock Slide, Stomping Tantrum | unknown | 44 | 10.5% |
| Garchompite Z | Dragon Pulse, Earth Power, Power Gem, Protect | unknown | 40 | 10.0% |
| Garchompite Z | Draco Meteor, Flamethrower, Power Gem, Protect | unknown | 45 | 10.0% |
| Life Orb | Dragon Claw, Earthquake, Protect, Stomping Tantrum | unknown | 32 | 7.3% |
| Garchompite Z | Draco Meteor, Earth Power, Power Gem, Protect | unknown | 27 | 7.0% |
- Other signatures: 55.2%

### Basculegion
- Teams: 488, weighted support: 15.7%, mega share: 0.0%
- Roles: priority-attack 93.7%, pivot 43.3%, speed-drop 1.6%, weather-setter 0.2%, setup 0.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Aqua Jet, Flip Turn, Last Respects, Wave Crash | unknown | 142 | 31.7% |
| Life Orb | Aqua Jet, Last Respects, Protect, Wave Crash | unknown | 131 | 28.8% |
| Mystic Water | Aqua Jet, Last Respects, Protect, Wave Crash | unknown | 57 | 13.4% |
| Choice Scarf | Aqua Jet, Flip Turn, Last Respects, Wave Crash | fast | 31 | 4.0% |
| Life Orb | Aqua Jet, Last Respects, Protect, Wave Crash | fast | 25 | 2.9% |
- Other signatures: 19.1%

### Archaludon
- Teams: 409, weighted support: 14.4%, mega share: 0.0%
- Roles: spa-drop 28.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Dragon Pulse, Electro Shot, Flash Cannon, Protect | unknown | 181 | 44.7% |
| Leftovers | Dragon Pulse, Electro Shot, Protect, Snarl | unknown | 83 | 22.6% |
| Leftovers | Aura Sphere, Dragon Pulse, Electro Shot, Protect | unknown | 32 | 8.2% |
| Leftovers | Draco Meteor, Electro Shot, Flash Cannon, Protect | unknown | 19 | 4.9% |
| Leftovers | Dragon Pulse, Electro Shot, Flash Cannon, Protect | bulky | 24 | 3.4% |
- Other signatures: 16.2%

### Farigiraf
- Teams: 415, weighted support: 14.4%, mega share: 0.0%
- Roles: priority-blocker 99.5%, trick-room-setter 98.1%, helping-hand 50.1%, trick-room-abuser 34.4%, disruption 6.1%, weather-setter 5.0%, setup 2.9%, ally-switch 1.8%, screens 0.9%, terrain-setter 0.9%, speed-drop 0.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Helping Hand, Protect, Psychic, Trick Room | unknown | 41 | 11.0% |
| Sitrus Berry | Protect, Psychic, Thunderbolt, Trick Room | unknown | 42 | 10.4% |
| Sitrus Berry | Helping Hand, Psychic, Thunderbolt, Trick Room | unknown | 24 | 5.5% |
| Sitrus Berry | Expanding Force, Protect, Thunderbolt, Trick Room | unknown | 19 | 4.8% |
| Sitrus Berry | Grass Knot, Protect, Psychic, Trick Room | unknown | 17 | 4.8% |
- Other signatures: 63.6%

### Golisopod
- Teams: 405, weighted support: 13.4%, mega share: 99.7%
- Roles: mega-attacker 99.7%, setup 43.4%, priority-attack 36.3%, trick-room-abuser 32.5%, pivot 2.5%, wide-guard 1.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Golisopite | Drill Run, Iron Head, Leech Life, Protect | unknown | 75 | 22.8% |
| Golisopite | Iron Head, Leech Life, Protect, Swords Dance | unknown | 58 | 14.3% |
| Golisopite | Leech Life, Protect, Sucker Punch, Swords Dance | unknown | 34 | 8.9% |
| Golisopite | Iron Head, Leech Life, Sucker Punch, Swords Dance | unknown | 33 | 8.6% |
| Golisopite | Iron Head, Leech Life, Protect, Sucker Punch | unknown | 27 | 6.7% |
- Other signatures: 38.7%

### Staraptor
- Teams: 360, weighted support: 13.3%, mega share: 98.0%
- Roles: intimidate 99.7%, mega-attacker 98.0%, tailwind 78.7%, pivot 2.9%, weather-setter 0.3%, priority-attack 0.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Staraptite | Brave Bird, Close Combat, Protect, Tailwind | unknown | 257 | 73.9% |
| Staraptite | Brave Bird, Close Combat, Protect, Roost | unknown | 53 | 15.0% |
| Staraptite | Close Combat, Dual Wingbeat, Protect, Tailwind | unknown | 9 | 2.6% |
| Staraptite | Close Combat, Dual Wingbeat, Protect, Roost | unknown | 6 | 1.7% |
| Staraptite | Brave Bird, Close Combat, Protect, Tailwind | fast | 12 | 1.2% |
- Other signatures: 5.6%

### Charizard
- Teams: 365, weighted support: 12.5%, mega share: 100.0%
- Roles: mega-attacker 100.0%, weather-setter 98.3%, setup 1.7%, helping-hand 1.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Charizardite Y | Heat Wave, Protect, Solar Beam, Weather Ball | unknown | 116 | 32.8% |
| Charizardite Y | Heat Wave, Hurricane, Protect, Weather Ball | unknown | 85 | 26.3% |
| Charizardite Y | Ancient Power, Heat Wave, Protect, Weather Ball | unknown | 86 | 23.3% |
| Charizardite Y | Ancient Power, Heat Wave, Protect, Weather Ball | bulky | 10 | 1.8% |
| Charizardite Y | Heat Wave, Helping Hand, Protect, Weather Ball | unknown | 5 | 1.5% |
- Other signatures: 14.4%

### Floette-Eternal
- Teams: 376, weighted support: 12.2%, mega share: 99.7%
- Roles: mega-attacker 99.7%, setup 83.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Floettite | Calm Mind, Dazzling Gleam, Moonblast, Protect | unknown | 147 | 42.6% |
| Floettite | Calm Mind, Dazzling Gleam, Draining Kiss, Protect | unknown | 98 | 30.9% |
| Floettite | Dazzling Gleam, Light of Ruin, Moonblast, Protect | unknown | 45 | 12.3% |
| Floettite | Calm Mind, Dazzling Gleam, Moonblast, Protect | bulky | 18 | 2.5% |
| Floettite | Calm Mind, Dazzling Gleam, Draining Kiss, Protect | bulky | 14 | 2.0% |
- Other signatures: 9.7%

### Sylveon
- Teams: 346, weighted support: 11.8%, mega share: 0.0%
- Roles: priority-attack 83.4%, status 10.1%, trick-room-abuser 6.5%, setup 5.9%, spa-drop 3.5%, helping-hand 0.9%, weather-setter 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Quick Attack | unknown | 189 | 58.7% |
| Fairy Feather | Hyper Beam, Hyper Voice, Protect, Quick Attack | unknown | 42 | 13.3% |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Yawn | unknown | 11 | 3.6% |
| Fairy Feather | Calm Mind, Detect, Hyper Beam, Hyper Voice | unknown | 13 | 3.6% |
| Fairy Feather | Detect, Hyper Beam, Hyper Voice, Mystical Fire | unknown | 7 | 2.3% |
- Other signatures: 18.5%

### Pelipper
- Teams: 324, weighted support: 10.9%, mega share: 0.0%
- Roles: weather-setter 100.0%, tailwind 86.0%, wide-guard 67.4%, trick-room-abuser 2.5%, helping-hand 1.4%, pivot 1.2%, speed-drop 0.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Hurricane, Tailwind, Weather Ball, Wide Guard | unknown | 80 | 25.8% |
| Focus Sash | Hurricane, Tailwind, Weather Ball, Wide Guard | unknown | 59 | 19.5% |
| Focus Sash | Hurricane, Protect, Tailwind, Weather Ball | unknown | 58 | 18.9% |
| Focus Sash | Hurricane, Protect, Weather Ball, Wide Guard | unknown | 13 | 4.5% |
| Sitrus Berry | Hurricane, Protect, Tailwind, Weather Ball | unknown | 8 | 2.2% |
- Other signatures: 29.2%

### Tyranitar
- Teams: 274, weighted support: 9.2%, mega share: 88.1%
- Roles: weather-setter 99.7%, mega-attacker 88.1%, setup 10.7%, trick-room-abuser 1.2%, speed-drop 0.6%, disruption 0.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Tyranitarite | Knock Off, Low Kick, Protect, Rock Slide | unknown | 157 | 62.7% |
| Tyranitarite | Dragon Dance, Knock Off, Protect, Rock Slide | unknown | 22 | 8.7% |
| Tyranitarite | Knock Off, Low Kick, Protect, Rock Slide | fast | 19 | 3.1% |
| Tyranitarite | Fire Punch, Knock Off, Protect, Rock Slide | unknown | 7 | 2.9% |
| Chople Berry | Knock Off, Low Kick, Protect, Rock Slide | unknown | 6 | 2.3% |
- Other signatures: 20.2%

### Excadrill
- Teams: 224, weighted support: 7.5%, mega share: 1.5%
- Roles: setup 3.7%, speed-drop 2.5%, mega-attacker 1.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | High Horsepower, Iron Head, Protect, Rock Slide | unknown | 151 | 74.6% |
| Focus Sash | Earthquake, High Horsepower, Iron Head, Protect | unknown | 8 | 3.8% |
| Focus Sash | High Horsepower, Iron Head, Protect, Rock Slide | fast | 21 | 3.8% |
| Life Orb | High Horsepower, Iron Head, Protect, Rock Slide | unknown | 6 | 2.7% |
| Focus Sash | Earthquake, Iron Head, Protect, Rock Slide | unknown | 5 | 2.1% |
- Other signatures: 13.0%

### Gardevoir
- Teams: 229, weighted support: 7.3%, mega share: 100.0%
- Roles: mega-attacker 99.4%, trick-room-setter 34.8%, setup 14.8%, spa-drop 13.2%, disruption 3.9%, priority-attack 0.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Gardevoirite | Expanding Force, Hyper Voice, Protect, Trick Room | unknown | 65 | 28.4% |
| Gardevoirite | Expanding Force, Hyper Voice, Protect, Thunderbolt | unknown | 32 | 14.4% |
| Gardevoirite | Expanding Force, Hyper Voice, Mystical Fire, Protect | unknown | 28 | 13.2% |
| Gardevoirite | Calm Mind, Expanding Force, Hyper Voice, Protect | unknown | 24 | 12.5% |
| Gardevoirite | Expanding Force, Hyper Voice, Moonblast, Protect | unknown | 12 | 5.7% |
- Other signatures: 25.7%

### Volcarona
- Teams: 200, weighted support: 7.1%, mega share: 0.0%
- Roles: setup 54.1%, rage-powder 49.0%, spa-drop 43.9%, tailwind 27.4%, status 1.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Grassy Seed | Giga Drain, Heat Wave, Protect, Quiver Dance | unknown | 32 | 17.6% |
| Rocky Helmet | Overheat, Rage Powder, Struggle Bug, Tailwind | unknown | 12 | 6.6% |
| Grassy Seed | Flamethrower, Giga Drain, Protect, Quiver Dance | unknown | 12 | 6.3% |
| Sitrus Berry | Overheat, Rage Powder, Struggle Bug, Tailwind | unknown | 10 | 5.3% |
| Rocky Helmet | Overheat, Protect, Rage Powder, Struggle Bug | unknown | 9 | 4.9% |
- Other signatures: 59.3%

### Politoed
- Teams: 187, weighted support: 6.8%, mega share: 0.0%
- Roles: weather-setter 100.0%, perish-song 45.7%, disruption 33.3%, trick-room-abuser 8.2%, speed-drop 7.5%, helping-hand 6.7%, status 6.4%, setup 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Mystic Water | Ice Beam, Muddy Water, Protect, Weather Ball | unknown | 51 | 30.5% |
| Sitrus Berry | Encore, Perish Song, Protect, Weather Ball | unknown | 52 | 26.9% |
| Life Orb | Ice Beam, Muddy Water, Protect, Weather Ball | unknown | 10 | 5.6% |
| Sitrus Berry | Hypnosis, Perish Song, Protect, Weather Ball | unknown | 8 | 4.9% |
| Sitrus Berry | Ice Beam, Perish Song, Protect, Weather Ball | unknown | 4 | 2.3% |
- Other signatures: 29.9%

### Indeedee
- Teams: 195, weighted support: 6.7%, mega share: 0.0%
- Roles: terrain-setter 100.0%, priority-blocker 100.0%, spa-drop 65.3%, disruption 27.4%, trick-room-setter 27.4%, helping-hand 9.9%, fake-out 2.3%, setup 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Expanding Force, Mystical Fire, Protect, Trick | unknown | 59 | 35.2% |
| Focus Sash | Expanding Force, Imprison, Protect, Trick Room | unknown | 11 | 5.5% |
| Choice Scarf | Dazzling Gleam, Expanding Force, Mystical Fire, Trick | unknown | 11 | 5.4% |
| Choice Scarf | Expanding Force, Imprison, Trick, Trick Room | unknown | 6 | 3.8% |
| Focus Sash | Expanding Force, Hyper Voice, Imprison, Trick Room | unknown | 4 | 1.8% |
- Other signatures: 48.3%

### Armarouge
- Teams: 193, weighted support: 6.3%, mega share: 0.0%
- Roles: wide-guard 56.0%, trick-room-setter 48.8%, trick-room-abuser 24.0%, setup 2.3%, weather-setter 1.7%, ally-switch 0.7%, helping-hand 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Armor Cannon, Expanding Force, Protect, Wide Guard | unknown | 27 | 17.2% |
| Life Orb | Armor Cannon, Expanding Force, Protect, Trick Room | unknown | 24 | 11.9% |
| Life Orb | Armor Cannon, Expanding Force, Protect, Wide Guard | unknown | 17 | 9.3% |
| Twisted Spoon | Armor Cannon, Expanding Force, Trick Room, Wide Guard | unknown | 10 | 6.0% |
| Focus Sash | Armor Cannon, Expanding Force, Trick Room, Wide Guard | unknown | 9 | 5.2% |
- Other signatures: 50.4%

### Froslass
- Teams: 172, weighted support: 6.0%, mega share: 100.0%
- Roles: weather-setter 100.0%, mega-attacker 95.0%, screens 91.0%, speed-drop 2.6%, setup 1.9%, disruption 0.9%, status 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Froslassite | Aurora Veil, Blizzard, Protect, Shadow Ball | unknown | 132 | 80.7% |
| Froslassite | Aurora Veil, Blizzard, Protect, Rain Dance | unknown | 10 | 4.7% |
| Froslassite | Aurora Veil, Blizzard, Protect, Shadow Ball | fast | 11 | 3.7% |
| Froslassite | Blizzard, Protect, Shadow Ball, Weather Ball | unknown | 3 | 2.1% |
| Froslassite | Blizzard, Nasty Plot, Protect, Shadow Ball | unknown | 3 | 1.9% |
- Other signatures: 6.9%

### Gengar
- Teams: 160, weighted support: 5.7%, mega share: 99.3%
- Roles: mega-attacker 92.0%, perish-song 67.7%, speed-drop 8.1%, disruption 7.3%, status 1.6%, setup 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Gengarite | Perish Song, Protect, Shadow Ball, Sludge Bomb | unknown | 87 | 55.8% |
| Gengarite | Focus Blast, Protect, Shadow Ball, Sludge Bomb | unknown | 14 | 10.2% |
| Gengarite | Protect, Shadow Ball, Sludge Bomb, Substitute | unknown | 12 | 7.7% |
| Gengarite | Icy Wind, Protect, Shadow Ball, Sludge Bomb | unknown | 9 | 6.1% |
| Gengarite | Disable, Perish Song, Protect, Shadow Ball | unknown | 6 | 4.2% |
- Other signatures: 16.0%

### Whimsicott
- Teams: 159, weighted support: 5.5%, mega share: 0.0%
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

### Metagross
- Teams: 160, weighted support: 5.4%, mega share: 97.4%
- Roles: mega-attacker 97.4%, setup 12.9%, priority-attack 8.2%, trick-room-abuser 0.5%, speed-drop 0.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Metagrossite | Iron Head, Protect, Psychic Fangs, Stomping Tantrum | unknown | 24 | 15.7% |
| Metagrossite | Body Press, Protect, Psychic Fangs, Steel Roller | unknown | 24 | 15.7% |
| Metagrossite | Protect, Psychic Fangs, Steel Roller, Stomping Tantrum | unknown | 22 | 14.5% |
| Metagrossite | Body Press, Protect, Psych Up, Psychic Fangs | unknown | 15 | 9.8% |
| Metagrossite | Body Press, Iron Head, Protect, Psychic Fangs | unknown | 10 | 6.8% |
- Other signatures: 37.4%

### Grimmsnarl
- Teams: 153, weighted support: 5.4%, mega share: 0.0%
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

### Sinistcha
- Teams: 134, weighted support: 4.6%, mega share: 0.0%
- Roles: rage-powder 97.3%, trick-room-setter 74.1%, trick-room-abuser 15.7%, disruption 1.3%, follow-me 0.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Colbur Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | unknown | 27 | 22.4% |
| Occa Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | unknown | 11 | 10.1% |
| Coba Berry | Life Dew, Matcha Gotcha, Protect, Rage Powder | unknown | 5 | 4.7% |
| Kasib Berry | Matcha Gotcha, Protect, Rage Powder, Trick Room | unknown | 6 | 4.7% |
| Sitrus Berry | Life Dew, Matcha Gotcha, Rage Powder, Trick Room | unknown | 6 | 4.6% |
- Other signatures: 53.5%

### Delphox
- Teams: 115, weighted support: 4.4%, mega share: 95.6%
- Roles: mega-attacker 95.6%, setup 82.1%, disruption 4.3%, status 1.0%, terrain-setter 0.4%, priority-blocker 0.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Delphoxite | Heat Wave, Nasty Plot, Protect, Psychic | unknown | 72 | 66.4% |
| Delphoxite | Heat Wave, Nasty Plot, Protect, Psyshock | unknown | 6 | 5.5% |
| Delphoxite | Expanding Force, Heat Wave, Nasty Plot, Protect | unknown | 4 | 3.7% |
| Delphoxite | Heat Wave, Protect, Psychic, Substitute | unknown | 3 | 2.6% |
| Delphoxite | Calm Mind, Heat Wave, Protect, Psychic | unknown | 2 | 1.6% |
- Other signatures: 20.2%

### Swampert
- Teams: 127, weighted support: 4.3%, mega share: 92.1%
- Roles: mega-attacker 92.1%, wide-guard 5.9%, pivot 5.8%, status 4.6%, trick-room-abuser 4.3%, weather-setter 1.0%, helping-hand 0.7%, setup 0.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Swampertite | Earthquake, Ice Punch, Protect, Wave Crash | unknown | 58 | 47.3% |
| Swampertite | High Horsepower, Ice Punch, Protect, Wave Crash | unknown | 30 | 24.9% |
| Swampertite | Earthquake, Ice Punch, Protect, Wave Crash | fast | 6 | 3.0% |
| Swampertite | Earthquake, High Horsepower, Protect, Wave Crash | unknown | 3 | 2.9% |
| Sitrus Berry | Flip Turn, High Horsepower, Protect, Yawn | unknown | 2 | 1.9% |
- Other signatures: 19.9%

### Glimmora
- Teams: 122, weighted support: 4.2%, mega share: 71.6%
- Roles: mega-attacker 71.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Glimmoranite | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | unknown | 68 | 58.1% |
| Focus Sash | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | unknown | 21 | 19.2% |
| Glimmoranite | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | fast | 11 | 5.7% |
| Focus Sash | Earth Power, Power Gem, Sludge Bomb, Spiky Shield | fast | 7 | 3.9% |
| Glimmoranite | Earth Power, Power Gem, Protect, Sludge Bomb | unknown | 3 | 2.7% |
- Other signatures: 10.4%

### Kommo-o
- Teams: 121, weighted support: 4.1%, mega share: 0.0%
- Roles: setup 67.6%, priority-attack 2.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | unknown | 51 | 44.3% |
| Life Orb | Aura Sphere, Clanging Scales, Flamethrower, Protect | unknown | 31 | 27.1% |
| Leftovers | Clanging Scales, Clangorous Soul, Flamethrower, Protect | unknown | 7 | 6.2% |
| Leftovers | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | fast | 9 | 4.0% |
| Life Orb | Aura Sphere, Clanging Scales, Clangorous Soul, Protect | unknown | 3 | 3.1% |
- Other signatures: 15.4%

### Venusaur
- Teams: 108, weighted support: 3.6%, mega share: 10.2%
- Roles: status 77.0%, mega-attacker 9.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Leaf Storm, Protect, Sleep Powder, Sludge Bomb | unknown | 35 | 34.5% |
| Focus Sash | Earth Power, Protect, Sleep Powder, Sludge Bomb | unknown | 13 | 13.2% |
| Life Orb | Earth Power, Leaf Storm, Protect, Sludge Bomb | unknown | 11 | 10.1% |
| Focus Sash | Earth Power, Leaf Storm, Sleep Powder, Sludge Bomb | unknown | 5 | 4.7% |
| Venusaurite | Earth Power, Giga Drain, Protect, Sludge Bomb | unknown | 4 | 4.3% |
- Other signatures: 33.2%

### Dragapult
- Teams: 89, weighted support: 3.2%, mega share: 0.0%
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
- Teams: 82, weighted support: 3.1%, mega share: 0.0%
- Roles: setup 97.7%, priority-attack 96.1%, ally-switch 1.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Grassy Seed | Bitter Blade, Protect, Shadow Sneak, Swords Dance | unknown | 55 | 73.3% |
| Colbur Berry | Bitter Blade, Bulk Up, Protect, Shadow Sneak | unknown | 6 | 5.8% |
| Grassy Seed | Bitter Blade, Bulk Up, Protect, Shadow Sneak | unknown | 4 | 5.0% |
| Colbur Berry | Bitter Blade, Protect, Shadow Sneak, Swords Dance | unknown | 3 | 2.9% |
| Grassy Seed | Bitter Blade, Protect, Shadow Sneak, Swords Dance | offensive | 2 | 1.4% |
- Other signatures: 11.6%

### Aerodactyl
- Teams: 91, weighted support: 3.1%, mega share: 75.4%
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
- Teams: 88, weighted support: 3.1%, mega share: 87.5%
- Roles: mega-attacker 87.5%, tailwind 64.8%, priority-attack 18.6%, setup 1.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Dragoninite | Dragon Pulse, Heat Wave, Protect, Tailwind | unknown | 37 | 46.2% |
| Dragoninite | Dragon Pulse, Extreme Speed, Heat Wave, Protect | unknown | 7 | 8.8% |
| Dragoninite | Dragon Pulse, Flamethrower, Protect, Tailwind | unknown | 5 | 6.4% |
| Dragoninite | Dragon Pulse, Hurricane, Protect, Weather Ball | unknown | 2 | 3.0% |
| Dragoninite | Dragon Pulse, Haze, Hurricane, Protect | unknown | 2 | 2.7% |
- Other signatures: 32.8%

### Torkoal
- Teams: 99, weighted support: 3.1%, mega share: 0.0%
- Roles: weather-setter 100.0%, trick-room-abuser 95.3%, helping-hand 29.5%, status 1.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Charcoal | Earth Power, Eruption, Protect, Weather Ball | unknown | 19 | 21.4% |
| Charcoal | Eruption, Helping Hand, Protect, Weather Ball | unknown | 12 | 13.3% |
| Charcoal | Eruption, Heat Wave, Protect, Weather Ball | unknown | 10 | 12.1% |
| Charcoal | Earth Power, Eruption, Heat Wave, Protect | unknown | 5 | 6.9% |
| Charcoal | Earth Power, Eruption, Helping Hand, Protect | unknown | 3 | 4.1% |
- Other signatures: 42.2%

### Corviknight
- Teams: 73, weighted support: 2.8%, mega share: 0.0%
- Roles: setup 90.7%, tailwind 23.5%, disruption 1.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Psychic Seed | Brave Bird, Bulk Up, Power Trip, Roost | unknown | 41 | 61.0% |
| Leftovers | Brave Bird, Bulk Up, Roost, Tailwind | unknown | 6 | 7.7% |
| Leftovers | Brave Bird, Iron Head, Protect, Tailwind | unknown | 3 | 4.5% |
| Occa Berry | Brave Bird, Roost, Tailwind, Taunt | unknown | 1 | 1.8% |
| Rocky Helmet | Brave Bird, Bulk Up, Iron Head, Roost | unknown | 1 | 1.5% |
- Other signatures: 23.5%

### Primarina
- Teams: 72, weighted support: 2.6%, mega share: 0.0%
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

### Annihilape
- Teams: 54, weighted support: 2.0%, mega share: 0.0%
- Roles: pivot 31.7%, speed-drop 14.9%, setup 13.3%, ally-boost 4.8%, disruption 1.5%, weather-setter 1.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Close Combat, Ice Punch, Shadow Claw, U-turn | unknown | 8 | 15.7% |
| Choice Scarf | Close Combat, Ice Punch, Phantom Force, Shadow Claw | unknown | 5 | 9.8% |
| Choice Scarf | Close Combat, Ice Punch, Rock Tomb, Shadow Claw | unknown | 5 | 8.6% |
| Choice Scarf | Close Combat, Ice Punch, Rock Slide, Shadow Claw | unknown | 4 | 7.8% |
| Leftovers | Bulk Up, Drain Punch, Protect, Rage Fist | unknown | 4 | 7.8% |
- Other signatures: 50.3%

### Baxcalibur
- Teams: 69, weighted support: 2.0%, mega share: 80.8%
- Roles: priority-attack 87.1%, mega-attacker 80.8%, setup 51.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Baxcalibrite | Glaive Rush, Ice Shard, Protect, Swords Dance | unknown | 13 | 23.7% |
| Baxcalibrite | Glaive Rush, High Horsepower, Ice Shard, Protect | unknown | 8 | 13.8% |
| Baxcalibrite | Dragon Dance, Glaive Rush, Icicle Crash, Protect | unknown | 3 | 4.5% |
| Baxcalibrite | Glaive Rush, Ice Shard, Icicle Spear, Protect | unknown | 2 | 4.2% |
| Life Orb | Glaive Rush, Ice Shard, Icicle Crash, Protect | offensive | 4 | 3.7% |
- Other signatures: 50.1%

### Hatterene
- Teams: 58, weighted support: 2.0%, mega share: 0.0%
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

### Ninetales-Alola
- Teams: 57, weighted support: 1.9%, mega share: 0.0%
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
- Teams: 50, weighted support: 1.7%, mega share: 96.1%
- Roles: mega-attacker 96.1%, setup 80.2%, fake-out 11.8%, pivot 3.9%, status 1.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Blastoisinite | Dark Pulse, Protect, Shell Smash, Water Spout | unknown | 14 | 32.8% |
| Blastoisinite | Protect, Shell Smash, Terrain Pulse, Water Spout | unknown | 12 | 23.9% |
| Blastoisinite | Protect, Shell Smash, Terrain Pulse, Water Spout | offensive | 4 | 4.9% |
| Blastoisinite | Ice Beam, Protect, Shell Smash, Water Spout | unknown | 2 | 4.8% |
| Blastoisinite | Aura Sphere, Dark Pulse, Protect, Water Spout | unknown | 2 | 4.8% |
- Other signatures: 28.7%

### Pawmot
- Teams: 57, weighted support: 1.7%, mega share: 0.0%
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
- Teams: 50, weighted support: 1.7%, mega share: 68.0%
- Roles: mega-attacker 68.0%, ally-boost 18.4%, setup 2.5%, priority-attack 1.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Blazikenite | Close Combat, Detect, Flare Blitz, Rock Slide | unknown | 14 | 29.0% |
| Blazikenite | Close Combat, Flare Blitz, Protect, Rock Slide | unknown | 8 | 18.8% |
| Focus Sash | Aura Sphere, Coaching, Detect, Heat Wave | unknown | 5 | 10.3% |
| Blazikenite | Close Combat, Detect, Flare Blitz, Rock Slide | fast | 4 | 6.5% |
| Blazikenite | Close Combat, Flare Blitz, Protect, Thunder Punch | unknown | 2 | 5.5% |
- Other signatures: 30.0%

### Maushold
- Teams: 44, weighted support: 1.6%, mega share: 0.0%
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
- Teams: 38, weighted support: 1.5%, mega share: 0.0%
- Roles: status 100.0%, rage-powder 95.1%, tailwind 7.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Hurricane, Protect, Rage Powder, Sleep Powder | unknown | 32 | 86.9% |
| Focus Sash | Hurricane, Protect, Sleep Powder, Tailwind | unknown | 2 | 4.9% |
| Focus Sash | Hurricane, Protect, Rage Powder, Sleep Powder | fast | 2 | 3.2% |
| Focus Sash | Pollen Puff, Rage Powder, Sleep Powder, Tailwind | unknown | 1 | 2.9% |
| Choice Scarf | Hurricane, Protect, Rage Powder, Sleep Powder | unknown | 1 | 2.0% |
- Other signatures: 0.0%

### Hydreigon
- Teams: 39, weighted support: 1.3%, mega share: 0.0%
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
- Teams: 28, weighted support: 0.9%, mega share: 100.0%
- Roles: mega-attacker 100.0%, priority-attack 94.1%, trick-room-abuser 79.8%, intimidate 31.0%, setup 20.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Mawilite | Iron Head, Play Rough, Protect, Sucker Punch | unknown | 12 | 48.1% |
| Mawilite | Play Rough, Protect, Rock Slide, Sucker Punch | unknown | 3 | 12.4% |
| Mawilite | Play Rough, Protect, Sucker Punch, Swords Dance | unknown | 3 | 12.4% |
| Mawilite | Iron Head, Play Rough, Protect, Sucker Punch | min-speed | 4 | 7.6% |
| Mawilite | Play Rough, Rock Slide, Sucker Punch, Swords Dance | unknown | 1 | 4.6% |
- Other signatures: 14.8%

### Kleavor
- Teams: 24, weighted support: 0.9%, mega share: 0.0%
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

### Espathra
- Teams: 18, weighted support: 0.7%, mega share: 0.0%
- Roles: setup 88.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Grassy Seed | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 6 | 33.2% |
| Sitrus Berry | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 3 | 15.8% |
| Focus Sash | Baton Pass, Calm Mind, Expanding Force, Protect | unknown | 2 | 11.6% |
| Focus Sash | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 1 | 7.0% |
| Colbur Berry | Baton Pass, Calm Mind, Lumina Crash, Protect | unknown | 1 | 5.8% |
- Other signatures: 26.6%

### Rotom-Heat
- Teams: 20, weighted support: 0.7%, mega share: 0.0%
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

### Altaria
- Teams: 19, weighted support: 0.7%, mega share: 25.9%
- Roles: status 69.5%, tailwind 63.5%, mega-attacker 25.9%, perish-song 24.8%, setup 4.6%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Haban Berry | Ice Beam, Protect, Tailwind, Will-O-Wisp | unknown | 10 | 55.1% |
| Altarianite | Draco Meteor, Hyper Voice, Perish Song, Protect | unknown | 3 | 20.6% |
| Haban Berry | Ice Beam, Protect, Safeguard, Will-O-Wisp | unknown | 1 | 6.4% |
| Altarianite | Draco Meteor, Hyper Voice, Protect, Roost | bulky | 1 | 5.3% |
| Leftovers | Cotton Guard, Draco Meteor, Hurricane, Tailwind | unknown | 1 | 4.6% |
- Other signatures: 8.0%

### Lycanroc-Dusk
- Teams: 17, weighted support: 0.6%, mega share: 0.0%
- Roles: priority-attack 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Focus Sash | Accelerock, Close Combat, Protect, Rock Slide | unknown | 13 | 84.7% |
| Focus Sash | Accelerock, Close Combat, Protect, Rock Slide | fast | 3 | 10.3% |
| Wide Lens | Accelerock, Play Rough, Psychic Fangs, Rock Slide | unknown | 1 | 5.0% |
- Other signatures: 0.0%

### Toxapex
- Teams: 19, weighted support: 0.6%, mega share: 0.0%
- Roles: status 100.0%, wide-guard 94.8%, trick-room-abuser 36.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Baneful Bunker, Infestation, Toxic, Wide Guard | unknown | 13 | 76.1% |
| Leftovers | Baneful Bunker, Infestation, Toxic, Wide Guard | bulky | 2 | 6.5% |
| Leftovers | Baneful Bunker, Infestation, Recover, Toxic | unknown | 1 | 5.2% |
| Sitrus Berry | Baneful Bunker, Infestation, Toxic, Wide Guard | unknown | 1 | 5.2% |
| Sitrus Berry | Baneful Bunker, Infestation, Toxic, Wide Guard | bulky | 1 | 4.7% |
- Other signatures: 2.4%

### Sirfetch’d
- Teams: 20, weighted support: 0.5%, mega share: 0.0%
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

### Rotom-Wash
- Teams: 14, weighted support: 0.5%, mega share: 0.0%
- Roles: status 85.9%, speed-drop 36.5%, pivot 28.2%, screens 14.1%, weather-setter 8.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Hydro Pump, Light Screen, Thunderbolt, Will-O-Wisp | unknown | 2 | 14.1% |
| Sitrus Berry | Electroweb, Hydro Pump, Protect, Volt Switch | unknown | 1 | 8.3% |
| Leftovers | Electroweb, Protect, Volt Switch, Will-O-Wisp | unknown | 1 | 8.3% |
| Leftovers | Hydro Pump, Protect, Thunderbolt, Will-O-Wisp | unknown | 1 | 8.3% |
| Sitrus Berry | Hydro Pump, Protect, Thunderbolt, Will-O-Wisp | unknown | 1 | 8.3% |
- Other signatures: 52.9%

### Gallade
- Teams: 16, weighted support: 0.5%, mega share: 14.2%
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

### Tsareena
- Teams: 14, weighted support: 0.5%, mega share: 0.0%
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
- Teams: 11, weighted support: 0.3%, mega share: 0.0%
- Roles: trick-room-abuser 64.1%, priority-attack 52.8%, ally-boost 8.7%, disruption 8.7%, setup 7.1%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Detect, Mach Punch, Stomping Tantrum, Storm Throw | unknown | 2 | 18.5% |
| Psychic Seed | Payback, Protect, Storm Throw, Topsy-Turvy | unknown | 2 | 17.4% |
| Life Orb | Ice Punch, Payback, Protect, Storm Throw | unknown | 1 | 12.3% |
| Life Orb | Mach Punch, Protect, Storm Throw, Sucker Punch | unknown | 1 | 12.3% |
| Focus Sash | Detect, Feint, Ice Punch, Storm Throw | unknown | 1 | 8.7% |
- Other signatures: 30.7%

### Lopunny
- Teams: 9, weighted support: 0.3%, mega share: 100.0%
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
- Teams: 13, weighted support: 0.3%, mega share: 100.0%
- Roles: mega-attacker 100.0%, fake-out 87.3%, setup 6.2%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Meowsticite | Expanding Force, Fake Out, Protect, Thunderbolt | unknown | 4 | 41.2% |
| Meowsticite | Expanding Force, Fake Out, Protect, Thunderbolt | fast | 5 | 31.4% |
| Meowsticite | Alluring Voice, Expanding Force, Fake Out, Protect | fast | 2 | 14.8% |
| Meowsticite | Alluring Voice, Expanding Force, Protect, Shadow Ball | fast | 1 | 6.5% |
| Meowsticite | Alluring Voice, Expanding Force, Nasty Plot, Protect | fast | 1 | 6.2% |
- Other signatures: 0.0%

### Starmie
- Teams: 6, weighted support: 0.2%, mega share: 78.3%
- Roles: mega-attacker 78.3%, setup 12.8%, priority-attack 12.8%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Starminite | Expanding Force, Ice Beam, Liquidation, Protect | unknown | 2 | 39.8% |
| Staraptite | Expanding Force, Ice Beam, Liquidation, Protect | fast | 1 | 21.7% |
| Starminite | Bulk Up, Ice Spinner, Liquidation, Protect | unknown | 1 | 12.8% |
| Starminite | Aqua Jet, Ice Spinner, Liquidation, Protect | unknown | 1 | 12.8% |
| Starminite | Expanding Force, Ice Spinner, Liquidation, Protect | unknown | 1 | 12.8% |
- Other signatures: 0.0%

### Scizor
- Teams: 8, weighted support: 0.2%, mega share: 15.9%
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
- Teams: 7, weighted support: 0.2%, mega share: 18.5%
- Roles: fake-out 100.0%, priority-attack 18.5%, mega-attacker 18.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Silk Scarf | Fake Out, Last Resort | unknown | 2 | 31.5% |
| Chople Berry | Facade, Fake Out, Hammer Arm, Protect | unknown | 1 | 18.5% |
| Leftovers | Double-Edge, Drain Punch, Fake Out, Sucker Punch | unknown | 1 | 18.5% |
| Life Orb | Brick Break, Double-Edge, Fake Out, Hammer Arm | unknown | 1 | 13.1% |
| Kangaskhanite | Crunch, Double-Edge, Fake Out, Hammer Arm | unknown | 1 | 9.2% |
- Other signatures: 9.2%

### Araquanid
- Teams: 6, weighted support: 0.2%, mega share: 0.0%
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
- Roles: mega-attacker 66.7%, helping-hand 19.5%, ally-boost 19.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Heracronite | Close Combat, Detect, Pin Missile, Rock Blast | unknown | 2 | 33.3% |
| Heracronite | Close Combat, Pin Missile, Protect, Rock Blast | unknown | 2 | 33.3% |
| Coba Berry | Coaching, Helping Hand, Low Kick, Upper Hand | unknown | 1 | 19.5% |
| White Herb | Bullet Seed, Close Combat, Pin Missile, Trailblaze | unknown | 1 | 13.8% |
- Other signatures: 0.0%

### Gliscor
- Teams: 5, weighted support: 0.2%, mega share: 0.0%
- Roles: tailwind 59.4%, spa-drop 20.3%, pivot 20.3%, setup 20.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Psychic Seed | Acrobatics, High Horsepower, Protect, Tailwind | fast | 1 | 24.7% |
| Choice Scarf | High Horsepower, Knock Off, Struggle Bug, U-turn | unknown | 1 | 20.3% |
| Soft Sand | High Horsepower, Protect, Rock Slide, Tailwind | unknown | 1 | 20.3% |
| Sitrus Berry | Dual Wingbeat, High Horsepower, Protect, Swords Dance | unknown | 1 | 20.3% |
| Life Orb | Dual Wingbeat, High Horsepower, Protect, Tailwind | unknown | 1 | 14.4% |
- Other signatures: 0.0%

### Pincurchin
- Teams: 7, weighted support: 0.2%, mega share: 0.0%
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
- Roles: trick-room-abuser 38.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Transform | unknown | 2 | 45.3% |
| Focus Sash | Transform | unknown | 1 | 22.7% |
| Sitrus Berry | Transform | unknown | 1 | 16.0% |
| Leftovers | Transform | unknown | 1 | 16.0% |
- Other signatures: 0.0%

### Crabominable
- Teams: 5, weighted support: 0.2%, mega share: 75.8%
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

### Greninja
- Teams: 4, weighted support: 0.2%, mega share: 73.0%
- Roles: mega-attacker 73.0%, priority-attack 27.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Greninjite | Gunk Shot, Protect, Rock Slide, Water Shuriken | unknown | 1 | 27.0% |
| Choice Scarf | Dark Pulse, Grass Knot, Ice Beam, Surf | unknown | 1 | 27.0% |
| Greninjite | Dark Pulse, Extrasensory, Ice Beam, Surf | unknown | 1 | 27.0% |
| Greninjite | Blizzard, Dark Pulse, Hydro Pump, Protect | unknown | 1 | 19.1% |
- Other signatures: 0.0%

### Bellibolt
- Teams: 6, weighted support: 0.1%, mega share: 0.0%
- Roles: trick-room-abuser 59.0%, speed-drop 20.5%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Leftovers | Parabolic Charge, Protect, Soak, Thunderbolt | unknown | 2 | 49.5% |
| Sitrus Berry | Electroweb, Protect, Soak, Thunderbolt | unknown | 1 | 20.5% |
| Leftovers | Muddy Water, Parabolic Charge, Protect, Thunderbolt | offensive | 2 | 17.6% |
| Leftovers | Parabolic Charge, Protect, Soak, Thunderbolt | bulky | 1 | 12.5% |
- Other signatures: 0.0%

### Steelix
- Teams: 4, weighted support: 0.1%, mega share: 100.0%
- Roles: trick-room-abuser 100.0%, mega-attacker 100.0%, wide-guard 29.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Steelixite | Curse, Heavy Slam, High Horsepower, Protect | unknown | 3 | 70.7% |
| Steelixite | Earthquake, Ice Fang, Stone Edge, Wide Guard | unknown | 1 | 29.3% |
- Other signatures: 0.0%

### Ninetales
- Teams: 4, weighted support: 0.1%, mega share: 0.0%
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
- Roles: wide-guard 69.5%, setup 30.5%, trick-room-abuser 26.3%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| White Herb | Earthquake, Protect, Rock Slide, Shell Smash | unknown | 1 | 30.5% |
| Life Orb | Earthquake, Headlong Rush, Wide Guard, Wood Hammer | bulky | 2 | 26.3% |
| Sitrus Berry | Headlong Rush, Smack Down, Wide Guard, Wood Hammer | unknown | 1 | 21.6% |
| Life Orb | Earthquake, Headlong Rush, Wide Guard, Wood Hammer | unknown | 1 | 21.6% |
- Other signatures: 0.0%

### Runerigus
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Roles: trick-room-setter 100.0%, status 100.0%, trick-room-abuser 100.0%, ally-switch 66.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Ally Switch, Earthquake, Trick Room, Will-O-Wisp | unknown | 2 | 66.7% |
| Grassy Seed | Shadow Claw, Skill Swap, Trick Room, Will-O-Wisp | unknown | 1 | 33.3% |
- Other signatures: 0.0%

### Palafin
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Roles: priority-attack 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Mystic Water | Close Combat, Jet Punch, Protect, Wave Crash | unknown | 1 | 33.3% |
| Life Orb | Close Combat, Jet Punch, Protect, Wave Crash | unknown | 1 | 33.3% |
| Sitrus Berry | Ice Punch, Jet Punch, Protect, Wave Crash | unknown | 1 | 33.3% |
- Other signatures: 0.0%

### Sceptile
- Teams: 4, weighted support: 0.1%, mega share: 100.0%
- Roles: mega-attacker 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sceptilite | Detect, Dragon Pulse, Earth Power, Grass Knot | unknown | 1 | 33.3% |
| Sceptilite | Dragon Pulse, Earth Power, Giga Drain, Protect | unknown | 1 | 33.3% |
| Sceptilite | Detect, Dragon Pulse, Earth Power, Energy Ball | unknown | 1 | 16.7% |
| Sceptilite | Detect, Dragon Pulse, Earth Power, Energy Ball | fast | 1 | 16.7% |
- Other signatures: 0.0%

### Cofagrigus
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
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
- Roles: priority-attack 100.0%, setup 100.0%, trick-room-abuser 34.4%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Sitrus Berry | Aqua Jet, Belly Drum, Play Rough, Protect | unknown | 3 | 100.0% |
- Other signatures: 0.0%

### Noivern
- Teams: 5, weighted support: 0.1%, mega share: 0.0%
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
- Roles: setup 82.3%, mega-attacker 82.3%, trick-room-abuser 58.9%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Aggronite | Body Press, Heavy Slam, Iron Defense, Protect | unknown | 2 | 82.3% |
| Life Orb | Blizzard, Fire Blast, Head Smash, Protect | offensive | 1 | 17.7% |
- Other signatures: 0.0%

### Persian-Alola
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
- Roles: spa-drop 100.0%, fake-out 17.7%, pivot 17.7%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Choice Scarf | Charm, Foul Play, Snarl, Switcheroo | unknown | 2 | 82.3% |
| Focus Sash | Fake Out, Foul Play, Parting Shot, Snarl | fast | 1 | 17.7% |
- Other signatures: 0.0%

### Pidgeot
- Teams: 3, weighted support: 0.1%, mega share: 100.0%
- Roles: tailwind 100.0%, mega-attacker 100.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Pidgeotite | Heat Wave, Hurricane, Protect, Tailwind | unknown | 2 | 58.6% |
| Pidgeotite | Hurricane, Hyper Beam, Protect, Tailwind | unknown | 1 | 41.4% |
- Other signatures: 0.0%

### Krookodile
- Teams: 3, weighted support: 0.1%, mega share: 0.0%
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
- Roles: spa-drop 59.4%, pivot 40.6%, priority-attack 33.3%, disruption 26.0%
- Top set signatures:
| Signature | Teams | Weighted share |
| :--- | :--- | :--- |
| Life Orb | Pyro Ball, Trailblaze, U-turn, Zen Headbutt | unknown | 1 | 40.6% |
| White Herb | High Jump Kick, Pyro Ball, Snarl, Sucker Punch | fast | 1 | 33.3% |
| White Herb | High Jump Kick, Pyro Ball, Snarl, Taunt | fast | 1 | 26.0% |
- Other signatures: 0.0%

## 7. Conditional Set Table
### Rillaboom
| Partner | Teams | Support | Lift | Ladder teammate rank | Rillaboom given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Gholdengo | 716 | 24.4% | 1.557 | 1 (Rillaboom in Gholdengo's) | items: Miracle Seed 84.5%, Sitrus Berry 6.7%, Occa Berry 3.4%, Life Orb 1.8%, Expert Belt 1.3%, Grassy Seed 0.9%, Kebia Berry 0.7%, Leftovers 0.2%, White Herb 0.2%, Eject Button 0.1%, Muscle Band 0.1%, Quick Claw 0.1%; spreads: unknown 95.1%, bulky 2.4%, offensive 2.3%, fast 0.3% |
| Sneasler | 701 | 22.8% | 1.005 | 2 (Sneasler in Rillaboom's) | items: Miracle Seed 73.7%, Sitrus Berry 13.1%, Life Orb 5.2%, Occa Berry 3.8%, Eject Button 1.2%, Kebia Berry 0.9%, Grassy Seed 0.8%, Leftovers 0.6%, Expert Belt 0.3%, Focus Sash 0.3%, Coba Berry 0.1%; spreads: unknown 90.8%, bulky 4.7%, offensive 3.8%, fast 0.5%, min-speed 0.1% |
| Raichu | 623 | 22.5% | 1.607 | 1 (Rillaboom in Raichu's) | items: Miracle Seed 82.7%, Sitrus Berry 8.5%, Life Orb 3.8%, Occa Berry 2.4%, Grassy Seed 0.9%, Kebia Berry 0.8%, Leftovers 0.3%, Eject Button 0.2%, Coba Berry 0.2%, Expert Belt 0.1%; spreads: unknown 97.1%, offensive 1.7%, bulky 1.2%, fast 0.0%; moves: High Horsepower +18pp, U-turn -16pp |
| Salamence | 641 | 20.3% | 1.234 | 3 (Salamence in Rillaboom's) | items: Miracle Seed 73.9%, Sitrus Berry 13.2%, Life Orb 4.0%, Expert Belt 3.2%, Occa Berry 2.6%, Grassy Seed 0.9%, Kebia Berry 0.9%, Leftovers 0.8%, Coba Berry 0.2%, Eject Button 0.1%, Rocky Helmet 0.1%, Focus Sash 0.1%; spreads: unknown 90.0%, offensive 5.0%, bulky 4.3%, fast 0.6%, min-speed 0.2% |
| Incineroar | 614 | 19.8% | 1.252 | 1 (Rillaboom in Incineroar's) | items: Miracle Seed 66.1%, Eject Button 13.0%, Occa Berry 6.5%, Life Orb 5.5%, Sitrus Berry 4.8%, Grassy Seed 1.3%, Leftovers 1.1%, Expert Belt 0.4%, Kebia Berry 0.4%, Coba Berry 0.4%, Iron Ball 0.4%, Focus Sash 0.2%; spreads: unknown 89.6%, bulky 5.8%, offensive 3.2%, min-speed 0.8%, fast 0.5% |

### Sneasler
| Partner | Teams | Support | Lift | Ladder teammate rank | Sneasler given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 701 | 22.8% | 1.005 | 2 (Sneasler in Rillaboom's) | items: Grassy Seed 59.1%, White Herb 31.0%, Focus Sash 8.4%, Psychic Seed 0.6%, Life Orb 0.4%, (no item) 0.4%, Normal Gem 0.2%; spreads: unknown 90.8%, fast 7.2%, bulky 0.9%, offensive 0.6%, mixed 0.5% |
| Salamence | 580 | 18.6% | 1.483 | 2 (Sneasler in Salamence's) | items: Grassy Seed 39.8%, White Herb 33.6%, Psychic Seed 20.4%, Focus Sash 5.2%, (no item) 0.5%, Life Orb 0.4%, Normal Gem 0.2%; spreads: unknown 91.6%, fast 6.5%, bulky 0.9%, mixed 0.7%, offensive 0.3% |
| Kingambit | 421 | 13.6% | 1.332 | 1 (Sneasler in Kingambit's) | items: Grassy Seed 36.9%, White Herb 27.1%, Focus Sash 18.8%, Psychic Seed 15.5%, Life Orb 1.1%, (no item) 0.3%, Normal Gem 0.2%; spreads: unknown 88.9%, fast 9.4%, bulky 0.7%, offensive 0.6%, mixed 0.4%; moves: Fake Out +21pp |
| Incineroar | 403 | 12.9% | 1.073 | 2 (Sneasler in Incineroar's) | items: Grassy Seed 44.2%, White Herb 27.2%, Focus Sash 19.5%, Psychic Seed 8.0%, Life Orb 0.8%, (no item) 0.3%; spreads: unknown 89.5%, fast 8.3%, bulky 0.8%, mixed 0.8%, offensive 0.7% |
| Gholdengo | 335 | 10.8% | 0.904 | 3 (Sneasler in Gholdengo's) | items: Grassy Seed 56.7%, White Herb 27.0%, Psychic Seed 13.1%, Focus Sash 2.8%, (no item) 0.4%; spreads: unknown 94.0%, fast 4.2%, mixed 1.0%, bulky 0.5%, offensive 0.4%; moves: Rock Tomb +19pp |

### Salamence
| Partner | Teams | Support | Lift | Ladder teammate rank | Salamence given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 641 | 20.3% | 1.234 | 3 (Salamence in Rillaboom's) | items: Salamencite 100.0%; spreads: unknown 90.0%, fast 9.8%, mixed 0.2%, bulky 0.1% |
| Sneasler | 580 | 18.6% | 1.483 | 2 (Sneasler in Salamence's) | items: Salamencite 100.0%; spreads: unknown 91.6%, fast 8.2%, mixed 0.1%, bulky 0.1% |
| Gholdengo | 383 | 12.2% | 1.403 | 2 (Salamence in Gholdengo's) | items: Salamencite 99.7%, Haban Berry 0.3%; spreads: unknown 92.1%, fast 7.5%, offensive 0.3%, bulky 0.1% |
| Arcanine-Hisui | 298 | 9.7% | 1.536 | 2 (Salamence in Arcanine-Hisui's) | items: Salamencite 100.0%; spreads: unknown 94.1%, fast 5.3%, mixed 0.3%, offensive 0.2% |
| Kingambit | 285 | 8.8% | 1.194 | 3 (Salamence in Kingambit's) | items: Salamencite 100.0%; spreads: unknown 85.2%, fast 14.2%, mixed 0.4%, bulky 0.2% |

### Incineroar
| Partner | Teams | Support | Lift | Ladder teammate rank | Incineroar given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 614 | 19.8% | 1.252 | 1 (Rillaboom in Incineroar's) | items: Sitrus Berry 67.9%, Passho Berry 10.4%, Chople Berry 10.0%, Rocky Helmet 7.3%, Leftovers 1.0%, Lum Berry 0.8%, White Herb 0.7%, Expert Belt 0.6%, Life Orb 0.6%, Bright Powder 0.2%, Charcoal 0.1%, Eject Button 0.1%, Grassy Seed 0.1%; spreads: unknown 89.6%, bulky 8.0%, min-speed 1.7%, fast 0.4%, offensive 0.2% |
| Sneasler | 403 | 12.9% | 1.073 | 2 (Sneasler in Incineroar's) | items: Sitrus Berry 80.2%, Rocky Helmet 7.9%, Chople Berry 4.9%, Passho Berry 3.9%, White Herb 1.0%, Leftovers 0.6%, Life Orb 0.6%, Expert Belt 0.2%, Charcoal 0.2%, Black Glasses 0.2%, Eject Button 0.2%; spreads: unknown 89.5%, bulky 8.4%, min-speed 1.0%, fast 0.6%, offensive 0.4%, mixed 0.1% |
| Floette-Eternal | 287 | 9.2% | 2.609 | 3 (Incineroar in Floette-Eternal's) | items: Sitrus Berry 83.6%, Rocky Helmet 8.6%, Chople Berry 3.3%, Passho Berry 2.2%, Leftovers 1.3%, Expert Belt 0.5%, Charcoal 0.3%, Bright Powder 0.2%; spreads: unknown 89.4%, bulky 9.4%, fast 0.6%, min-speed 0.4%, mixed 0.2% |
| Gholdengo | 247 | 7.9% | 0.945 | 4 (Incineroar in Gholdengo's) | items: Sitrus Berry 84.1%, Rocky Helmet 9.5%, Chople Berry 4.4%, Leftovers 1.1%, Life Orb 0.5%, Charcoal 0.4%; spreads: unknown 90.7%, bulky 8.2%, min-speed 0.5%, fast 0.3%, offensive 0.3% |
| Garchomp | 197 | 6.4% | 1.401 | 2 (Incineroar in Garchomp's) | items: Sitrus Berry 78.2%, Chople Berry 10.5%, Rocky Helmet 4.1%, Passho Berry 4.1%, Leftovers 1.3%, Life Orb 0.9%, Charcoal 0.5%, Expert Belt 0.5%; spreads: unknown 90.1%, bulky 7.9%, min-speed 1.1%, fast 0.5%, mixed 0.2%, offensive 0.2% |

### Gholdengo
| Partner | Teams | Support | Lift | Ladder teammate rank | Gholdengo given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 716 | 24.4% | 1.557 | 1 (Rillaboom in Gholdengo's) | items: Life Orb 88.1%, Grassy Seed 7.7%, Choice Scarf 1.6%, Leftovers 1.1%, Sitrus Berry 0.4%, Spell Tag 0.2%, Focus Sash 0.2%, Occa Berry 0.2%, Bright Powder 0.2%, White Herb 0.1%, Kasib Berry 0.1%; spreads: unknown 95.1%, fast 3.6%, bulky 1.0%, offensive 0.3% |
| Raichu | 472 | 16.7% | 2.267 | 5 (Raichu in Gholdengo's) | items: Life Orb 89.8%, Grassy Seed 8.3%, Leftovers 0.7%, Choice Scarf 0.5%, Sitrus Berry 0.4%, Focus Sash 0.2%; spreads: unknown 96.7%, fast 2.7%, bulky 0.6% |
| Salamence | 383 | 12.2% | 1.403 | 2 (Salamence in Gholdengo's) | items: Life Orb 84.0%, Grassy Seed 10.9%, Leftovers 1.3%, Choice Scarf 1.3%, Focus Sash 0.7%, Sitrus Berry 0.6%, Metal Coat 0.3%, Spell Tag 0.2%, Occa Berry 0.2%, Kasib Berry 0.2%, White Herb 0.1%; spreads: unknown 92.1%, fast 6.2%, bulky 0.8%, offensive 0.7%, mixed 0.1% |
| Arcanine-Hisui | 348 | 11.9% | 1.974 | 4 (Gholdengo in Arcanine-Hisui's) | items: Life Orb 86.7%, Grassy Seed 11.0%, Leftovers 1.4%, Choice Scarf 0.6%, Sitrus Berry 0.2%; spreads: unknown 96.9%, fast 2.5%, offensive 0.3%, bulky 0.3% |
| Sneasler | 335 | 10.8% | 0.904 | 3 (Sneasler in Gholdengo's) | items: Life Orb 84.9%, Grassy Seed 7.5%, Choice Scarf 3.2%, Leftovers 1.7%, Focus Sash 1.0%, Psychic Seed 0.5%, Metal Coat 0.4%, Spell Tag 0.3%, Kasib Berry 0.3%, Sitrus Berry 0.3%; spreads: unknown 94.0%, fast 4.6%, bulky 0.9%, offensive 0.5% |

### Raichu
| Partner | Teams | Support | Lift | Ladder teammate rank | Raichu given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 623 | 22.5% | 1.607 | 1 (Rillaboom in Raichu's) | items: Raichunite Y 99.9%, Raichunite X 0.1%; spreads: unknown 97.1%, fast 2.9% |
| Gholdengo | 472 | 16.7% | 2.267 | 5 (Raichu in Gholdengo's) | items: Raichunite Y 99.6%, Raichunite X 0.4%; spreads: unknown 96.7%, fast 3.3% |
| Arcanine-Hisui | 370 | 13.3% | 2.467 | 5 (Raichu in Arcanine-Hisui's) | items: Raichunite Y 99.5%, Raichunite X 0.5%; spreads: unknown 97.8%, fast 2.2% |
| Staraptor | 239 | 9.0% | 2.621 | 5 (Staraptor in Raichu's) | items: Raichunite Y 99.5%, Raichunite X 0.5%; spreads: unknown 98.9%, fast 1.1%; moves: Fake Out +15pp |
| Sneasler | 239 | 8.3% | 0.777 | 6 (Sneasler in Raichu's) | items: Raichunite Y 98.6%, Raichunite X 1.4%; spreads: unknown 97.0%, fast 3.0%; moves: Fake Out -19pp, Encore +17pp |

### Kingambit
| Partner | Teams | Support | Lift | Ladder teammate rank | Kingambit given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sneasler | 421 | 13.6% | 1.332 | 1 (Sneasler in Kingambit's) | items: Chople Berry 39.0%, Life Orb 38.0%, Black Glasses 11.2%, Focus Sash 9.0%, Occa Berry 2.5%, Shuca Berry 0.3%; spreads: unknown 88.9%, offensive 5.2%, fast 2.9%, bulky 2.8%, min-speed 0.2% |
| Rillaboom | 388 | 12.8% | 0.958 | 2 (Rillaboom in Kingambit's) | items: Chople Berry 40.3%, Life Orb 37.7%, Black Glasses 11.3%, Focus Sash 8.6%, Occa Berry 2.1%; spreads: unknown 88.1%, offensive 6.5%, fast 3.1%, bulky 1.9%, min-speed 0.2%, mixed 0.2% |
| Salamence | 285 | 8.8% | 1.194 | 3 (Salamence in Kingambit's) | items: Chople Berry 56.0%, Life Orb 19.9%, Focus Sash 11.0%, Black Glasses 9.6%, Occa Berry 3.5%; spreads: unknown 85.2%, offensive 6.6%, fast 4.9%, bulky 2.8%, min-speed 0.3%, mixed 0.2% |
| Arcanine-Hisui | 165 | 5.7% | 1.111 | 6 (Kingambit in Arcanine-Hisui's) | items: Chople Berry 47.0%, Life Orb 45.8%, Black Glasses 5.2%, Occa Berry 1.2%, Focus Sash 0.7%; spreads: unknown 95.4%, offensive 2.5%, fast 0.7%, bulky 0.7%, mixed 0.3%, min-speed 0.2% |
| Incineroar | 170 | 5.7% | 0.804 | n/a | items: Life Orb 41.0%, Chople Berry 25.1%, Black Glasses 25.1%, Focus Sash 8.4%, Occa Berry 0.4%; spreads: unknown 89.1%, offensive 6.9%, fast 2.3%, bulky 1.7%; moves: Low Kick -21pp, Swords Dance +19pp, Protect +18pp, Iron Head -17pp |

### Arcanine-Hisui
| Partner | Teams | Support | Lift | Ladder teammate rank | Arcanine-Hisui given partner |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Rillaboom | 516 | 17.8% | 1.552 | 1 (Rillaboom in Arcanine-Hisui's) | items: Focus Sash 98.1%, Life Orb 0.8%, Choice Scarf 0.7%, Charcoal 0.3%, King's Rock 0.2%; spreads: unknown 96.2%, fast 3.7%, min-speed 0.1% |
| Raichu | 370 | 13.3% | 2.467 | 5 (Raichu in Arcanine-Hisui's) | items: Focus Sash 99.2%, White Herb 0.3%, Choice Scarf 0.2%, King's Rock 0.2%; spreads: unknown 97.8%, fast 2.2% |
| Gholdengo | 348 | 11.9% | 1.974 | 4 (Gholdengo in Arcanine-Hisui's) | items: Focus Sash 98.7%, Life Orb 0.6%, Choice Scarf 0.4%, King's Rock 0.2%; spreads: unknown 96.9%, fast 3.1% |
| Salamence | 298 | 9.7% | 1.536 | 2 (Salamence in Arcanine-Hisui's) | items: Focus Sash 98.3%, Life Orb 1.2%, Choice Scarf 0.5%; spreads: unknown 94.1%, fast 5.9% |
| Sneasler | 282 | 9.5% | 1.091 | 3 (Sneasler in Arcanine-Hisui's) | items: Focus Sash 97.1%, Life Orb 1.9%, Choice Scarf 0.8%, Charcoal 0.3%; spreads: unknown 95.9%, fast 4.1% |

## 8. Sheet vs. Ladder
Ladder: median rank over 15 daily snapshots from 2026-09-16 to 2026-09-30 (M6, M-C, Doubles), those archived within the sheet window (2026-09-09 to 2026-10-01); snapshots are kept 14 days, so a longer window is compared over at most its last 14 days. Teammates and items are from 2026-09-30. Battle data provided by Pokémon Champions Battle Data (https://championsbattledata.com); only figures derived from these snapshots are shown.
Sheet = shared teams (2627 of 2957 placed; of the window's teams, only those with a placement are results; the rest are social shares, videos, and ladder pastes), not only Bo3 open-sheet results.
Ladder teammates: the site's top 8 in its order (metric unstated); absence means not in the top 8, not rare.

### 8.1 Coverage
Ladder top 60 species by median rank: 60 node, 0 thin (fewer than minNodeTeams sheet teams), 0 absent from the sheet.
| Median rank | Ladder species | Sheet species | Sheet teams | Weighted support | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Rillaboom | Rillaboom | 1620 | 54.6% | node |
| 2 | Sneasler | Sneasler | 1270 | 41.5% | node |
| 3 | Salamence | Salamence | 940 | 30.1% | node |
| 4 | Incineroar | Incineroar | 884 | 29.0% | node |
| 5 | Indeedee-F | Indeedee-F | 553 | 18.2% | node |
| 6 | Kingambit | Kingambit | 739 | 24.6% | node |
| 7 | Basculegion | Basculegion | 488 | 15.7% | node |
| 8 | Golisopod | Golisopod | 405 | 13.4% | node |
| 9 | Garchomp | Garchomp | 459 | 15.8% | node |
| 10 | Gholdengo | Gholdengo | 849 | 28.7% | node |
| 11 | Archaludon | Archaludon | 409 | 14.4% | node |
| 11 | Pelipper | Pelipper | 324 | 10.9% | node |
| 13 | Milotic | Milotic | 473 | 16.3% | node |
| 14 | Farigiraf | Farigiraf | 415 | 14.4% | node |
| 15 | Charizard | Charizard | 365 | 12.5% | node |
| 16 | Gardevoir | Gardevoir | 229 | 7.3% | node |
| 17 | Raichu | Raichu | 711 | 25.6% | node |
| 18 | Arcanine-Hisui | Arcanine-Hisui | 611 | 21.0% | node |
| 19 | Sylveon | Sylveon | 346 | 11.8% | node |
| 20 | Tyranitar | Tyranitar | 274 | 9.2% | node |
| 21 | Whimsicott | Whimsicott | 159 | 5.5% | node |
| 22 | Armarouge | Armarouge | 193 | 6.3% | node |
| 23 | Staraptor | Staraptor | 360 | 13.3% | node |
| 24 | Torkoal | Torkoal | 99 | 3.1% | node |
| 26 | Metagross | Metagross | 160 | 5.4% | node |
| 26 | Indeedee | Indeedee | 195 | 6.7% | node |
| 26 | Sinistcha | Sinistcha | 134 | 4.6% | node |
| 27 | Floette-Eternal | Floette-Eternal | 376 | 12.2% | node |
| 29 | Excadrill | Excadrill | 224 | 7.5% | node |
| 30 | Volcarona | Volcarona | 200 | 7.1% | node |
| 31 | Politoed | Politoed | 187 | 6.8% | node |
| 32 | Lucario | Lucario | 84 | 2.5% | node |
| 33 | Grimmsnarl | Grimmsnarl | 153 | 5.4% | node |
| 34 | Swampert | Swampert | 127 | 4.3% | node |
| 35 | Froslass | Froslass | 172 | 6.0% | node |
| 36 | Baxcalibur | Baxcalibur | 69 | 2.0% | node |
| 37 | Gengar | Gengar | 160 | 5.7% | node |
| 38 | Ninetales-Alola | Ninetales-Alola | 57 | 1.9% | node |
| 40 | Dragonite | Dragonite | 88 | 3.1% | node |
| 40 | Glimmora | Glimmora | 122 | 4.2% | node |
| 41 | Pawmot | Pawmot | 57 | 1.7% | node |
| 42 | Aerodactyl | Aerodactyl | 91 | 3.1% | node |
| 42 | Venusaur | Venusaur | 108 | 3.6% | node |
| 43 | Primarina | Primarina | 72 | 2.6% | node |
| 45 | Sableye | Sableye | 30 | 1.1% | node |
| 46 | Hatterene | Hatterene | 58 | 2.0% | node |
| 47 | Blastoise | Blastoise | 50 | 1.7% | node |
| 49 | Delphox | Delphox | 115 | 4.4% | node |
| 49 | Annihilape | Annihilape | 54 | 2.0% | node |
| 50 | Absol | Absol | 47 | 1.5% | node |
| 51 | Corviknight | Corviknight | 73 | 2.8% | node |
| 52 | Maushold-Four | Maushold | 44 | 1.6% | node |
| 53 | Talonflame | Talonflame | 35 | 1.1% | node |
| 54 | Kommo-o | Kommo-o | 121 | 4.1% | node |
| 55 | Ceruledge | Ceruledge | 82 | 3.1% | node |
| 56 | Dragapult | Dragapult | 89 | 3.2% | node |
| 57 | Camerupt | Camerupt | 67 | 2.4% | node |
| 58 | Hydreigon | Hydreigon | 39 | 1.3% | node |
| 59 | Blaziken | Blaziken | 50 | 1.7% | node |
| 60 | Mawile | Mawile | 28 | 0.9% | node |
Sheet-only (not on the ladder, or outside its top 60): Vivillon, Kleavor, Scovillain, Pyroar, Typhlosion-Hisui, Espathra, Rotom-Heat, Altaria, Lycanroc-Dusk, Toxapex, Sirfetch’d, Empoleon, Meganium, Rotom-Wash, Gallade, Tsareena, Vanilluxe, Aegislash, Weavile, Meowscarada, Toxtricity, Grapploct, Lopunny, Mamoswine, Klefki, Chandelure, Zoroark-Hisui, Goodra-Hisui, Meowstic-F, Starmie, Scizor, Kangaskhan, Araquanid, Clefable, Heracross, Gliscor, Pincurchin, Hippowdon, Abomasnow, Ampharos, Mimikyu, Overqwil, Drampa, Alakazam, Tinkaton, Ditto, Crabominable, Arcanine, Manectric, Scrafty, Gyarados, Greninja, Bellibolt, Steelix, Ninetales, Houndoom, Torterra, Runerigus, Palafin, Sceptile, Cofagrigus, Azumarill, Noivern, Tauros-Paldea-Aqua, Raichu-Alola, Dragalge, Aggron, Persian-Alola, Pidgeot, Krookodile, Feraligatr, Jolteon, Basculegion-F, Malamar, Clawitzer, Cinderace

### 8.2 Item Gaps
Ladder items (latest snapshot) on a top-60 species held by at least ladderItemMinShare of the species on the ladder -- a higher share than in the sheet -- that have fewer than minNodeTeams sheet teams.
| Median rank | Species | Item | Ladder share | Sheet share | Sheet teams |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 58 | Hydreigon | Life Orb | 15.9% | 6.5% | 2 |

### 8.3 Bias
#### By usage rank
Over-represented in the sheet vs. the ladder (sheet nodes among the ladder's top 60, score above zero, highest first):
| Species | Sheet rank | Median rank | Sheet teams | Score (log2) |
| :--- | :--- | :--- | :--- | :--- |
| Raichu | 6 | 17 | 711 | 1.50 |
| Arcanine-Hisui | 8 | 18 | 611 | 1.17 |
| Gholdengo | 5 | 10 | 849 | 1.00 |
| Floette-Eternal | 18 | 27 | 376 | 0.58 |
| Kommo-o | 37 | 54 | 121 | 0.55 |
| Delphox | 34 | 49 | 115 | 0.53 |
| Staraptor | 16 | 23 | 360 | 0.52 |
| Dragapult | 39 | 56 | 89 | 0.52 |
| Ceruledge | 40 | 55 | 82 | 0.46 |
| Excadrill | 22 | 29 | 224 | 0.40 |
| Milotic | 10 | 13 | 473 | 0.38 |
| Gengar | 29 | 37 | 160 | 0.35 |
| Froslass | 28 | 35 | 172 | 0.32 |
| Volcarona | 24 | 30 | 200 | 0.32 |
| Politoed | 25 | 31 | 187 | 0.31 |

Under-represented in the sheet (among the ladder's top 60, score below zero, lowest first):
| Species | Sheet rank | Median rank | Sheet teams | Score (log2) |
| :--- | :--- | :--- | :--- | :--- |
| Golisopod | 15 | 8 | 405 | -0.91 |
| Pelipper | 20 | 11 | 324 | -0.86 |
| Indeedee-F | 9 | 5 | 553 | -0.85 |
| Torkoal | 43 | 24 | 99 | -0.84 |
| Basculegion | 12 | 7 | 488 | -0.78 |
| Gardevoir | 23 | 16 | 229 | -0.52 |
| Lucario | 46 | 32 | 84 | -0.52 |
| Whimsicott | 30 | 21 | 159 | -0.51 |
| Baxcalibur | 49 | 36 | 69 | -0.44 |
| Ninetales-Alola | 51 | 38 | 57 | -0.42 |
| Sableye | 59 | 45 | 30 | -0.39 |
| Pawmot | 53 | 41 | 57 | -0.37 |
| Sinistcha | 33 | 26 | 134 | -0.34 |
| Armarouge | 27 | 22 | 193 | -0.30 |
| Garchomp | 11 | 9 | 459 | -0.29 |

#### By teammate list
Mean overlap between a species' ladder teammate list and its sheet partners by P(B|A), over the species compared: 80.2%.
- Rillaboom 6/8 shared. Ladder adds Garchomp (#7; sheet P=13%, 202 teams), Basculegion (#8; sheet P=14%, 250 teams). Sheet adds Arcanine-Hisui (P=33%, 516 teams; Arcanine-Hisui's own ladder list includes Rillaboom), Milotic (P=18%, 283 teams; Milotic's own ladder list includes Rillaboom).
- Sneasler 6/8 shared. Ladder adds Basculegion (#6; sheet P=17%, 233 teams), Gardevoir (#7; sheet P=12%, 154 teams). Sheet adds Arcanine-Hisui (P=23%, 282 teams; Arcanine-Hisui's own ladder list includes Sneasler), Raichu (P=20%, 239 teams; Raichu's own ladder list includes Sneasler).
- Salamence 6/8 shared. Ladder adds Incineroar (#5; sheet P=19%, 198 teams), Basculegion (#6; sheet P=18%, 181 teams). Sheet adds Arcanine-Hisui (P=32%, 298 teams; Arcanine-Hisui's own ladder list includes Salamence), Raichu (P=23%, 210 teams; Raichu's own ladder list does not include Salamence).
- Incineroar 6/8 shared. Ladder adds Basculegion (#6; sheet P=15%, 136 teams), Indeedee-F (#8; sheet P=9%, 80 teams). Sheet adds Floette-Eternal (P=32%, 287 teams; Floette-Eternal's own ladder list includes Incineroar), Kingambit (P=20%, 170 teams; Kingambit's own ladder list includes Incineroar).
- Indeedee-F 6/8 shared. Ladder adds Pelipper (#7; sheet P=13%, 74 teams), Torkoal (#8; sheet P=11%, 67 teams). Sheet adds Milotic (P=18%, 92 teams; Milotic's own ladder list includes Indeedee-F), Incineroar (P=15%, 80 teams; Incineroar's own ladder list includes Indeedee-F).
- Kingambit 6/8 shared. Ladder adds Indeedee-F (#6; sheet P=17%, 130 teams), Charizard (#8; sheet P=15%, 109 teams). Sheet adds Arcanine-Hisui (P=23%, 165 teams; Arcanine-Hisui's own ladder list includes Kingambit), Farigiraf (P=20%, 140 teams; Farigiraf's own ladder list includes Kingambit).
- Basculegion 8/8 shared.
- Golisopod 6/8 shared. Ladder adds Incineroar (#7; sheet P=19%, 81 teams), Basculegion (#8; sheet P=18%, 79 teams). Sheet adds Politoed (P=26%, 93 teams; Politoed's own ladder list includes Golisopod), Grimmsnarl (P=25%, 95 teams; Grimmsnarl's own ladder list includes Golisopod).
- Garchomp 6/8 shared. Ladder adds Gholdengo (#6; sheet P=17%, 81 teams), Whimsicott (#8; sheet P=13%, 56 teams). Sheet adds Farigiraf (P=25%, 112 teams; Farigiraf's own ladder list includes Garchomp), Sylveon (P=19%, 85 teams; Sylveon's own ladder list does not include Garchomp).
- Gholdengo 7/8 shared. Ladder adds Garchomp (#8; sheet P=10%, 81 teams). Sheet adds Staraptor (P=32%, 247 teams; Staraptor's own ladder list includes Gholdengo).
- Archaludon 7/8 shared. Ladder adds Swampert (#5; sheet P=24%, 101 teams). Sheet adds Farigiraf (P=29%, 112 teams; Farigiraf's own ladder list includes Archaludon).
- Pelipper 8/8 shared.
- Milotic 5/8 shared. Ladder adds Incineroar (#5; sheet P=13%, 67 teams), Indeedee-F (#7; sheet P=21%, 92 teams), Excadrill (#8; sheet P=19%, 99 teams). Sheet adds Raichu (P=31%, 132 teams; Raichu's own ladder list does not include Milotic), Staraptor (P=29%, 120 teams; Staraptor's own ladder list does not include Milotic), Arcanine-Hisui (P=24%, 112 teams; Arcanine-Hisui's own ladder list does not include Milotic).
- Farigiraf 7/8 shared. Ladder adds Pelipper (#7; sheet P=16%, 65 teams). Sheet adds Charizard (P=30%, 118 teams; Charizard's own ladder list does not include Farigiraf).
- Charizard 4/8 shared. Ladder adds Rillaboom (#3; sheet P=20%, 75 teams), Whimsicott (#5; sheet P=14%, 48 teams), Basculegion (#7; sheet P=12%, 43 teams), Incineroar (#8; sheet P=21%, 81 teams). Sheet adds Farigiraf (P=34%, 118 teams; Farigiraf's own ladder list does not include Charizard), Grimmsnarl (P=28%, 96 teams; Grimmsnarl's own ladder list includes Charizard), Venusaur (P=26%, 95 teams; Venusaur's own ladder list includes Charizard), Sylveon (P=23%, 81 teams; Sylveon's own ladder list does not include Charizard).
- Gardevoir 7/8 shared. Ladder adds Torkoal (#6; sheet P=13%, 35 teams). Sheet adds Whimsicott (P=15%, 31 teams; Whimsicott's own ladder list does not include Gardevoir).
- Raichu 7/8 shared. Ladder adds Garchomp (#8; sheet P=9%, 58 teams). Sheet adds Salamence (P=28%, 210 teams; Salamence's own ladder list does not include Raichu).
- Arcanine-Hisui 7/8 shared. Ladder adds Basculegion (#8; sheet P=8%, 57 teams). Sheet adds Staraptor (P=30%, 172 teams; Staraptor's own ladder list includes Arcanine-Hisui).
- Sylveon 7/8 shared. Ladder adds Salamence (#4; sheet P=23%, 89 teams). Sheet adds Garchomp (P=25%, 85 teams; Garchomp's own ladder list does not include Sylveon).
- Tyranitar 8/8 shared.
- Whimsicott 5/8 shared. Ladder adds Sneasler (#4; sheet P=16%, 27 teams), Rillaboom (#6; sheet P=13%, 20 teams), Staraptor (#8; sheet P=19%, 30 teams). Sheet adds Indeedee-F (P=30%, 47 teams; Indeedee-F's own ladder list does not include Whimsicott), Glimmora (P=20%, 33 teams; Glimmora's own ladder list includes Whimsicott), Gardevoir (P=20%, 31 teams; Gardevoir's own ladder list does not include Whimsicott).
- Armarouge 6/8 shared. Ladder adds Torkoal (#6; sheet P=15%, 31 teams), Salamence (#7; sheet P=18%, 36 teams). Sheet adds Staraptor (P=32%, 56 teams; Staraptor's own ladder list does not include Armarouge), Dragapult (P=22%, 36 teams; Dragapult's own ladder list does not include Armarouge).
- Staraptor 6/8 shared. Ladder adds Kingambit (#6; sheet P=8%, 31 teams), Whimsicott (#8; sheet P=8%, 30 teams). Sheet adds Milotic (P=36%, 120 teams; Milotic's own ladder list does not include Staraptor), Ceruledge (P=16%, 53 teams; Ceruledge's own ladder list includes Staraptor).
- Torkoal 6/8 shared. Ladder adds Golisopod (#7; sheet P=10%, 10 teams), Incineroar (#8; sheet P=17%, 18 teams). Sheet adds Rillaboom (P=21%, 17 teams; Rillaboom's own ladder list does not include Torkoal), Hatterene (P=19%, 19 teams; Hatterene's own ladder list includes Torkoal).
- Metagross 7/8 shared. Ladder adds Incineroar (#3; sheet P=22%, 38 teams). Sheet adds Indeedee (P=24%, 36 teams; Indeedee's own ladder list includes Metagross).
- Indeedee 7/8 shared. Ladder adds Kingambit (#8; sheet P=6%, 17 teams). Sheet adds Arcanine-Hisui (P=16%, 31 teams; Arcanine-Hisui's own ladder list does not include Indeedee).
- Sinistcha 5/8 shared. Ladder adds Archaludon (#2; sheet P=16%, 24 teams), Pelipper (#3; sheet P=16%, 25 teams), Tyranitar (#7; sheet P=15%, 23 teams). Sheet adds Delphox (P=47%, 56 teams; Delphox's own ladder list includes Sinistcha), Floette-Eternal (P=31%, 39 teams; Floette-Eternal's own ladder list does not include Sinistcha), Blastoise (P=17%, 20 teams; Blastoise's own ladder list includes Sinistcha).
- Floette-Eternal 8/8 shared.
- Excadrill 8/8 shared.
- Volcarona 7/8 shared. Ladder adds Gholdengo (#6; sheet P=16%, 32 teams). Sheet adds Basculegion (P=31%, 63 teams; Basculegion's own ladder list does not include Volcarona).
- Politoed 8/8 shared.
- Lucario 8/8 shared.
- Grimmsnarl 6/8 shared. Ladder adds Rillaboom (#6; sheet P=11%, 18 teams), Sinistcha (#8; sheet P=6%, 11 teams). Sheet adds Politoed (P=35%, 47 teams; Politoed's own ladder list includes Grimmsnarl), Venusaur (P=26%, 39 teams; Venusaur's own ladder list includes Grimmsnarl).
- Swampert 6/8 shared. Ladder adds Sinistcha (#7; sheet P=9%, 14 teams), Sneasler (#8; sheet P=11%, 14 teams). Sheet adds Farigiraf (P=21%, 24 teams; Farigiraf's own ladder list does not include Swampert), Charizard (P=21%, 25 teams; Charizard's own ladder list does not include Swampert).
- Froslass 6/8 shared. Ladder adds Archaludon (#6; sheet P=8%, 15 teams), Garchomp (#8; sheet P=9%, 16 teams). Sheet adds Raichu (P=34%, 51 teams; Raichu's own ladder list does not include Froslass), Salamence (P=20%, 40 teams; Salamence's own ladder list does not include Froslass).
- Baxcalibur 7/8 shared. Ladder adds Gholdengo (#6; sheet P=16%, 12 teams). Sheet adds Volcarona (P=37%, 23 teams; Volcarona's own ladder list does not include Baxcalibur).
- Gengar 6/8 shared. Ladder adds Froslass (#5; sheet P=7%, 13 teams), Sneasler (#8; sheet P=17%, 25 teams). Sheet adds Milotic (P=18%, 27 teams; Milotic's own ladder list does not include Gengar), Kingambit (P=18%, 30 teams; Kingambit's own ladder list does not include Gengar).
- Ninetales-Alola 7/8 shared. Ladder adds Milotic (#5; sheet P=13%, 8 teams). Sheet adds Kommo-o (P=16%, 8 teams; Kommo-o's own ladder list does not include Ninetales-Alola).
- Dragonite 6/8 shared. Ladder adds Archaludon (#6; sheet P=10%, 8 teams), Pelipper (#7; sheet P=9%, 7 teams). Sheet adds Floette-Eternal (P=29%, 25 teams; Floette-Eternal's own ladder list does not include Dragonite), Arcanine-Hisui (P=14%, 12 teams; Arcanine-Hisui's own ladder list does not include Dragonite).
- Glimmora 7/8 shared. Ladder adds Golisopod (#8; sheet P=8%, 11 teams). Sheet adds Garchomp (P=27%, 32 teams; Garchomp's own ladder list does not include Glimmora).
- Pawmot 6/8 shared. Ladder adds Politoed (#7; sheet P=11%, 6 teams), Indeedee-F (#8; sheet P=16%, 8 teams). Sheet adds Basculegion (P=23%, 15 teams; Basculegion's own ladder list does not include Pawmot), Gholdengo (P=16%, 9 teams; Gholdengo's own ladder list does not include Pawmot).
- Aerodactyl 7/8 shared. Ladder adds Sneasler (#6; sheet P=12%, 13 teams). Sheet adds Gholdengo (P=15%, 14 teams; Gholdengo's own ladder list does not include Aerodactyl).
- Venusaur 6/8 shared. Ladder adds Basculegion (#7; sheet P=10%, 13 teams), Incineroar (#8; sheet P=22%, 25 teams). Sheet adds Swampert (P=24%, 24 teams; Swampert's own ladder list does not include Venusaur), Indeedee-F (P=22%, 27 teams; Indeedee-F's own ladder list does not include Venusaur).
- Primarina 5/8 shared. Ladder adds Golisopod (#6; sheet P=12%, 9 teams), Indeedee-F (#7; <4 sheet teams), Garchomp (#8; sheet P=9%, 6 teams). Sheet adds Raichu (P=41%, 27 teams; Raichu's own ladder list does not include Primarina), Gholdengo (P=33%, 24 teams; Gholdengo's own ladder list does not include Primarina), Arcanine-Hisui (P=26%, 17 teams; Arcanine-Hisui's own ladder list does not include Primarina).
- Sableye 7/8 shared. Ladder adds Sneasler (#8; <4 sheet teams). Sheet adds Farigiraf (P=20%, 6 teams; Farigiraf's own ladder list does not include Sableye).
- Hatterene 8/8 shared.
- Blastoise 6/8 shared. Ladder adds Farigiraf (#5; sheet P=9%, 5 teams), Pelipper (#8; sheet P=10%, 4 teams). Sheet adds Delphox (P=39%, 17 teams; Delphox's own ladder list does not include Blastoise), Maushold (P=30%, 13 teams; Maushold's own ladder list does not include Blastoise).
- Delphox 6/8 shared. Ladder adds Garchomp (#6; sheet P=15%, 18 teams), Whimsicott (#8; sheet P=6%, 7 teams). Sheet adds Floette-Eternal (P=43%, 49 teams; Floette-Eternal's own ladder list does not include Delphox), Blastoise (P=16%, 17 teams; Blastoise's own ladder list does not include Delphox).
- Annihilape 6/8 shared. Ladder adds Incineroar (#3; sheet P=16%, 9 teams), Pelipper (#6; sheet P=17%, 9 teams). Sheet adds Charizard (P=21%, 11 teams; Charizard's own ladder list does not include Annihilape), Gardevoir (P=21%, 11 teams; Gardevoir's own ladder list does not include Annihilape).
- Absol 7/8 shared. Ladder adds Milotic (#4; sheet P=13%, 6 teams). Sheet adds Floette-Eternal (P=20%, 8 teams; Floette-Eternal's own ladder list does not include Absol).
- Corviknight 7/8 shared. Ladder adds Rillaboom (#8; <4 sheet teams). Sheet adds Raichu (P=10%, 7 teams; Raichu's own ladder list does not include Corviknight).
- Maushold 5/8 shared. Ladder adds Gardevoir (#6; <4 sheet teams), Annihilape (#7; <4 sheet teams), Archaludon (#8; <4 sheet teams). Sheet adds Delphox (P=42%, 16 teams; Delphox's own ladder list does not include Maushold), Blastoise (P=33%, 13 teams; Blastoise's own ladder list does not include Maushold), Floette-Eternal (P=17%, 7 teams; Floette-Eternal's own ladder list does not include Maushold).
- Talonflame 5/8 shared. Ladder adds Kingambit (#6; sheet P=20%, 8 teams), Gardevoir (#7; sheet P=19%, 8 teams), Milotic (#8; sheet P=12%, 4 teams). Sheet adds Raichu (P=25%, 7 teams; Raichu's own ladder list does not include Talonflame), Glimmora (P=23%, 9 teams; Glimmora's own ladder list does not include Talonflame), Gholdengo (P=22%, 7 teams; Gholdengo's own ladder list does not include Talonflame).
- Kommo-o 7/8 shared. Ladder adds Sneasler (#6; sheet P=13%, 18 teams). Sheet adds Whimsicott (P=24%, 27 teams; Whimsicott's own ladder list does not include Kommo-o).
- Ceruledge 7/8 shared. Ladder adds Indeedee-F (#8; <4 sheet teams). Sheet adds Kingambit (P=13%, 13 teams; Kingambit's own ladder list does not include Ceruledge).
- Dragapult 5/8 shared. Ladder adds Rillaboom (#4; sheet P=18%, 15 teams), Sneasler (#6; sheet P=15%, 14 teams), Incineroar (#8; sheet P=10%, 10 teams). Sheet adds Armarouge (P=44%, 36 teams; Armarouge's own ladder list does not include Dragapult), Golisopod (P=21%, 18 teams; Golisopod's own ladder list does not include Dragapult), Gengar (P=18%, 14 teams; Gengar's own ladder list does not include Dragapult).
- Camerupt 7/8 shared. Ladder adds Sylveon (#6; sheet P=10%, 6 teams). Sheet adds Armarouge (P=14%, 10 teams; Armarouge's own ladder list does not include Camerupt).
- Hydreigon 4/8 shared. Ladder adds Incineroar (#5; sheet P=10%, 4 teams), Charizard (#6; sheet P=16%, 7 teams), Golisopod (#7; sheet P=15%, 8 teams), Salamence (#8; sheet P=13%, 5 teams). Sheet adds Milotic (P=23%, 9 teams; Milotic's own ladder list does not include Hydreigon), Raichu (P=22%, 8 teams; Raichu's own ladder list does not include Hydreigon), Gholdengo (P=22%, 8 teams; Gholdengo's own ladder list does not include Hydreigon), Arcanine-Hisui (P=19%, 6 teams; Arcanine-Hisui's own ladder list does not include Hydreigon).
- Blaziken 6/8 shared. Ladder adds Metagross (#4; sheet P=14%, 8 teams), Basculegion (#7; sheet P=17%, 8 teams). Sheet adds Torkoal (P=23%, 11 teams; Torkoal's own ladder list does not include Blaziken), Gholdengo (P=18%, 9 teams; Gholdengo's own ladder list does not include Blaziken).
- Mawile 6/8 shared. Ladder adds Armarouge (#6; sheet P=18%, 6 teams), Hatterene (#8; <4 sheet teams). Sheet adds Salamence (P=23%, 5 teams; Salamence's own ladder list does not include Mawile), Pelipper (P=18%, 5 teams; Pelipper's own ladder list does not include Mawile).

### 8.4 Ladder-calibrated view (λ = 1)
A model built from the ladder's rank order, not a ladder usage share: among the sheet's species nodes with a ladder entry, the i-th in the ladder order takes the i-th highest sheet support as its rank-matched support, and each team's weight is multiplied once by the geometric mean over its species of (rank-matched support / sheet support)^λ. The sheet's cores and definitions are unchanged; a species thin or absent on the sheet (outside its species nodes) can't be reweighted. Unlike the coverage table (§8.1, the ladder's top 60), this view reweights every sheet species node the ladder lists, at any rank.
Calibrated support need not land on its rank-matched support: a species' factor is averaged with its teammates' in each team's multiplier and every share is renormalised, so it usually moves only part of the way, and can stop short, overshoot, or even move against its own factor; λ = 1 does not mean the ladder is matched. Mapping the i-th in the ladder order to the i-th sheet support assumes the ladder's usage curve has the sheet's shape; where sheet supports are compressed, one or two ranks swing a factor a lot. Where the two orders agree the factor is exactly one by construction, which does not mean equal usage; such a species still moves through its teammates and the renormalisation. Reweighting scales the sheet's own teams: it cannot add a ladder build the sheet lacks, so it corrects how much of each sheet archetype appears, not which archetypes exist.
Effective teams (Kish): 2735.34 under the sheet weights, 2669.89 calibrated; team weight multiplier 0.57 to 1.82.

#### Definitions
Each archetype definition of §9 by rank, its variants under it. Share = its teams over the window's teams, every roster copy counting one (§9's Share column); calibrated share = the same teams each weighted by its team multiplier above, over every window team so weighted. Each team's base weight is one (no placement weighting), so the difference is the calibration alone. A model built from the ladder's rank order, not a ladder usage share.
| Rank | Definition | Teams | Share | Calibrated share | Difference |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Tailwind Mega Salamence + Rillaboom | 571 | 19.3% | 17.8% | −1.5 pts |
| 1.1 | Tailwind Mega Salamence + Rillaboom + Sneasler | 324 | 11.0% | 10.0% | −1.0 pts |
| 1.2 | Tailwind Mega Salamence + Rillaboom + Gholdengo | 294 | 9.9% | 8.2% | −1.7 pts |
| 1.3 | Tailwind Mega Salamence + Rillaboom + Arcanine-Hisui | 242 | 8.2% | 6.9% | −1.3 pts |
| 2 | Psyspam Indeedee-F | 436 | 14.7% | 17.9% | +3.2 pts |
| 2.1 | Psyspam Indeedee-F + Armarouge | 183 | 6.2% | 7.4% | +1.2 pts |
| 2.2 | Psyspam Mega Gardevoir + Sneasler + Indeedee-F | 150 | 5.1% | 6.7% | +1.6 pts |
| 2.3 | Psyspam Mega Golisopod + Indeedee-F | 87 | 2.9% | 3.8% | +0.9 pts |
| 3 | Trick Room | 420 | 14.2% | 16.7% | +2.5 pts |
| 3.1 | Rain Trick Room Mega Golisopod + Farigiraf + Politoed | 54 | 1.8% | 2.0% | +0.2 pts |
| 3.2 | Trick Room Mega Camerupt + Farigiraf | 51 | 1.7% | 1.8% | +0.1 pts |
| 3.3 | Rain Trick Room Mega Golisopod + Archaludon + Politoed | 50 | 1.7% | 1.8% | +0.1 pts |
| 4 | Tailwind Mega Raichu-Y + Rillaboom + Gholdengo | 383 | 13.0% | 9.4% | −3.6 pts |
| 4.1 | Tailwind Setup Mega Raichu-Y + Rillaboom + Gholdengo | 108 | 3.7% | 2.6% | −1.1 pts |
| 5 | Sun Mega Charizard-Y | 357 | 12.1% | 13.2% | +1.1 pts |
| 5.1 | Sun Mega Charizard-Y + Garchomp | 157 | 5.3% | 5.7% | +0.4 pts |
| 5.2 | Sun Mega Charizard-Y + Farigiraf | 118 | 4.0% | 4.3% | +0.3 pts |
| 5.3 | Sun Rain Screens Mega Charizard-Y + Archaludon + Grimmsnarl | 95 | 3.2% | 3.5% | +0.3 pts |
| 6 | Rain Tailwind Archaludon + Pelipper | 225 | 7.6% | 8.9% | +1.3 pts |
| 6.1 | Rain Tailwind Mega Golisopod + Archaludon + Pelipper | 106 | 3.6% | 4.4% | +0.8 pts |
| 6.2 | Rain Tailwind Mega Swampert + Archaludon + Pelipper | 84 | 2.8% | 3.3% | +0.5 pts |
| 6.3 | Rain Tailwind Basculegion + Archaludon + Pelipper | 52 | 1.8% | 2.2% | +0.4 pts |
| 7 | Setup Mega Floette + Rillaboom + Incineroar | 176 | 6.0% | 5.1% | −0.9 pts |
| 8 | Sand Mega Salamence + Mega Tyranitar + Excadrill | 165 | 5.6% | 5.4% | −0.2 pts |
| 9 | Rain Archaludon + Politoed | 146 | 4.9% | 5.0% | +0.1 pts |
| 9.1 | Rain Perish Trap Mega Gengar + Archaludon + Politoed | 63 | 2.1% | 2.0% | −0.1 pts |
| 9.2 | Rain Perish Trap Incineroar + Archaludon + Politoed | 60 | 2.0% | 1.9% | −0.1 pts |
| 9.3 | Rain Screens Archaludon + Politoed + Grimmsnarl | 47 | 1.6% | 1.7% | +0.1 pts |
| 10 | Mega Garchomp-Z + Rillaboom + Incineroar | 96 | 3.2% | 3.3% | +0.1 pts |
| 11 | Snow Mega Froslass + Rillaboom + Sneasler | 97 | 3.3% | 2.9% | −0.4 pts |
| 12 | Tailwind Basculegion + Whimsicott | 66 | 2.2% | 2.5% | +0.3 pts |
| 13 | Tailwind Mega Glimmora | 65 | 2.2% | 2.3% | +0.1 pts |
| 14 | Tailwind Mega Dragonite | 60 | 2.0% | 2.0% | 0.0 pts |
| 15 | Setup Mega Delphox + Sneasler | 61 | 2.1% | 1.9% | −0.2 pts |
| 16 | Tailwind Garchomp + Whimsicott | 56 | 1.9% | 2.1% | +0.2 pts |
| 17 | Tailwind Kingambit + Farigiraf + Sylveon | 54 | 1.8% | 1.9% | +0.1 pts |
| 18 | Mega Raichu-Y + Rillaboom + Volcarona | 53 | 1.8% | 1.6% | −0.2 pts |
| 19 | Rain Mega Golisopod + Farigiraf + Pelipper | 52 | 1.8% | 2.1% | +0.3 pts |
| 20 | Psyspam Sneasler + Milotic + Indeedee | 51 | 1.7% | 1.7% | 0.0 pts |
| 21 | Mega Metagross + Indeedee-F | 50 | 1.7% | 1.7% | 0.0 pts |
| 22 | Mega Salamence + Mega Floette + Kingambit | 50 | 1.7% | 1.6% | −0.1 pts |
| 23 | Mega Lucario-Z + Rillaboom + Incineroar | 50 | 1.7% | 2.1% | +0.4 pts |
| 24 | Rain Tailwind Mega Golisopod + Basculegion + Pelipper | 49 | 1.7% | 2.3% | +0.6 pts |

#### Species
| Median rank | Species | Sheet support | Rank-matched support | Factor | Calibrated support |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Rillaboom | 54.6% | 54.6% | 1.00 | 49.7% |
| 2 | Sneasler | 41.5% | 41.5% | 1.00 | 42.0% |
| 3 | Salamence | 30.1% | 30.1% | 1.00 | 29.3% |
| 4 | Incineroar | 29.0% | 29.0% | 1.00 | 28.8% |
| 5 | Indeedee-F | 18.2% | 28.7% | 1.58 | 21.6% |
| 6 | Kingambit | 24.6% | 25.6% | 1.04 | 25.1% |
| 7 | Basculegion | 15.7% | 24.6% | 1.57 | 18.0% |
| 8 | Golisopod | 13.4% | 21.0% | 1.57 | 15.8% |
| 9 | Garchomp | 15.8% | 18.2% | 1.15 | 16.8% |
| 10 | Gholdengo | 28.7% | 16.3% | 0.57 | 23.6% |
| 11 | Archaludon | 14.4% | 15.8% | 1.10 | 16.2% |
| 11 | Pelipper | 10.9% | 15.7% | 1.44 | 13.2% |
| 13 | Milotic | 16.3% | 14.4% | 0.89 | 14.8% |
| 14 | Farigiraf | 14.4% | 14.4% | 1.00 | 15.7% |
| 15 | Charizard | 12.5% | 13.4% | 1.07 | 13.7% |
| 16 | Gardevoir | 7.3% | 13.3% | 1.82 | 9.5% |
| 17 | Raichu | 25.6% | 12.5% | 0.49 | 20.5% |
| 18 | Arcanine-Hisui | 21.0% | 12.2% | 0.58 | 17.4% |
| 19 | Sylveon | 11.8% | 11.8% | 1.00 | 10.6% |
| 20 | Tyranitar | 9.2% | 10.9% | 1.18 | 9.1% |
| 21 | Whimsicott | 5.5% | 9.2% | 1.67 | 6.1% |
| 22 | Armarouge | 6.3% | 7.5% | 1.19 | 7.4% |
| 23 | Staraptor | 13.3% | 7.3% | 0.55 | 10.7% |
| 24 | Torkoal | 3.1% | 7.1% | 2.33 | 4.0% |
| 26 | Metagross | 5.4% | 6.8% | 1.26 | 5.8% |
| 26 | Indeedee | 6.7% | 6.7% | 1.00 | 6.7% |
| 26 | Sinistcha | 4.6% | 6.3% | 1.37 | 4.9% |
| 27 | Floette-Eternal | 12.2% | 6.0% | 0.49 | 11.0% |
| 29 | Excadrill | 7.5% | 5.7% | 0.76 | 7.3% |
| 30 | Volcarona | 7.1% | 5.5% | 0.77 | 6.9% |
| 31 | Politoed | 6.8% | 5.4% | 0.79 | 7.0% |
| 32 | Lucario | 2.5% | 5.4% | 2.17 | 3.1% |
| 33 | Grimmsnarl | 5.4% | 4.6% | 0.86 | 6.0% |
| 34 | Swampert | 4.3% | 4.4% | 1.01 | 4.9% |
| 35 | Froslass | 6.0% | 4.3% | 0.73 | 5.4% |
| 36 | Baxcalibur | 2.0% | 4.2% | 2.13 | 2.4% |
| 37 | Gengar | 5.7% | 4.1% | 0.72 | 5.3% |
| 38 | Ninetales-Alola | 1.9% | 3.6% | 1.91 | 2.2% |
| 40 | Dragonite | 3.1% | 3.2% | 1.03 | 3.1% |
| 40 | Glimmora | 4.2% | 3.1% | 0.73 | 4.3% |
| 41 | Pawmot | 1.7% | 3.1% | 1.82 | 2.1% |
| 42 | Aerodactyl | 3.1% | 3.1% | 1.00 | 3.4% |
| 42 | Venusaur | 3.6% | 3.1% | 0.84 | 4.1% |
| 43 | Primarina | 2.6% | 2.8% | 1.06 | 2.6% |
| 45 | Sableye | 1.1% | 2.6% | 2.34 | 1.4% |
| 46 | Hatterene | 2.0% | 2.5% | 1.27 | 2.4% |
| 47 | Blastoise | 1.7% | 2.4% | 1.40 | 2.1% |
| 49 | Delphox | 4.4% | 2.0% | 0.46 | 4.1% |
| 49 | Annihilape | 2.0% | 2.0% | 0.99 | 2.3% |
| 50 | Absol | 1.5% | 2.0% | 1.31 | 1.6% |
| 51 | Corviknight | 2.8% | 1.9% | 0.68 | 2.7% |
| 52 | Maushold | 1.6% | 1.7% | 1.08 | 1.7% |
| 53 | Talonflame | 1.1% | 1.7% | 1.55 | 1.3% |
| 54 | Kommo-o | 4.1% | 1.7% | 0.41 | 3.7% |
| 55 | Ceruledge | 3.1% | 1.6% | 0.52 | 2.3% |
| 56 | Dragapult | 3.2% | 1.5% | 0.47 | 2.8% |
| 57 | Camerupt | 2.4% | 1.5% | 0.60 | 2.6% |
| 58 | Hydreigon | 1.3% | 1.3% | 1.00 | 1.3% |
| 59 | Blaziken | 1.7% | 1.1% | 0.66 | 1.8% |
| 60 | Mawile | 0.9% | 1.1% | 1.20 | 1.1% |
| 61 | Typhlosion-Hisui | 0.7% | 0.9% | 1.27 | 0.9% |
| 62 | Sirfetch’d | 0.5% | 0.9% | 1.59 | 0.6% |
| 63 | Gallade | 0.5% | 0.8% | 1.64 | 0.7% |
| 65 | Vivillon | 1.5% | 0.8% | 0.55 | 1.3% |
| 66 | Zoroark-Hisui | 0.3% | 0.7% | 2.28 | 0.4% |
| 67 | Tsareena | 0.5% | 0.7% | 1.55 | 0.5% |
| 68 | Rotom-Wash | 0.5% | 0.7% | 1.35 | 0.6% |
| 69 | Kleavor | 0.9% | 0.7% | 0.76 | 0.9% |
| 70 | Meowscarada | 0.3% | 0.6% | 1.71 | 0.4% |
| 71 | Alakazam | 0.2% | 0.6% | 3.07 | 0.3% |
| 72 | Empoleon | 0.5% | 0.5% | 1.01 | 0.6% |
| 73 | Scovillain | 0.8% | 0.5% | 0.65 | 0.8% |
| 74 | Weavile | 0.4% | 0.5% | 1.40 | 0.4% |
| 75 | Espathra | 0.7% | 0.5% | 0.70 | 0.7% |
| 76 | Chandelure | 0.3% | 0.5% | 1.59 | 0.4% |
| 78 | Pincurchin | 0.2% | 0.5% | 2.28 | 0.3% |
| 78 | Aegislash | 0.4% | 0.4% | 1.06 | 0.4% |
| 79 | Toxtricity | 0.3% | 0.4% | 1.13 | 0.4% |
| 80 | Scizor | 0.2% | 0.4% | 1.64 | 0.3% |
| 81 | Toxapex | 0.6% | 0.3% | 0.60 | 0.5% |
| 82 | Malamar | 0.1% | 0.3% | 4.36 | 0.1% |
| 83 | Clefable | 0.2% | 0.3% | 1.53 | 0.3% |
| 84 | Cinderace | 0.1% | 0.3% | 4.52 | 0.1% |
| 85 | Mimikyu | 0.2% | 0.3% | 1.66 | 0.2% |
| 86 | Meganium | 0.5% | 0.3% | 0.62 | 0.6% |
| 87 | Mamoswine | 0.3% | 0.3% | 0.96 | 0.3% |
| 89 | Kangaskhan | 0.2% | 0.3% | 1.39 | 0.3% |
| 89 | Goodra-Hisui | 0.3% | 0.3% | 1.00 | 0.3% |
| 90 | Greninja | 0.2% | 0.3% | 1.88 | 0.2% |
| 93 | Grapploct | 0.3% | 0.2% | 0.68 | 0.4% |
| 94 | Basculegion-F | 0.1% | 0.2% | 2.53 | 0.1% |
| 95 | Overqwil | 0.2% | 0.2% | 1.16 | 0.2% |
| 96 | Gyarados | 0.2% | 0.2% | 1.41 | 0.2% |
| 96 | Scrafty | 0.2% | 0.2% | 1.32 | 0.2% |
| 98 | Rotom-Heat | 0.7% | 0.2% | 0.31 | 0.6% |
| 99 | Lycanroc-Dusk | 0.6% | 0.2% | 0.35 | 0.5% |
| 101 | Araquanid | 0.2% | 0.2% | 0.90 | 0.2% |
| 103 | Drampa | 0.2% | 0.2% | 1.07 | 0.2% |
| 104 | Bellibolt | 0.1% | 0.2% | 1.40 | 0.2% |
| 105 | Pyroar | 0.8% | 0.2% | 0.25 | 0.8% |
| 106 | Tinkaton | 0.2% | 0.2% | 1.06 | 0.2% |
| 108 | Klefki | 0.3% | 0.2% | 0.61 | 0.3% |
| 108 | Vanilluxe | 0.4% | 0.2% | 0.47 | 0.4% |
| 110 | Sceptile | 0.1% | 0.2% | 1.49 | 0.1% |
| 112 | Ninetales | 0.1% | 0.2% | 1.30 | 0.2% |
| 113 | Arcanine | 0.2% | 0.2% | 1.07 | 0.2% |
| 114 | Altaria | 0.7% | 0.2% | 0.27 | 0.5% |
| 114 | Starmie | 0.2% | 0.2% | 0.75 | 0.2% |
| 116 | Lopunny | 0.3% | 0.2% | 0.52 | 0.3% |
| 118 | Jolteon | 0.1% | 0.2% | 1.66 | 0.1% |
| 120 | Aggron | 0.1% | 0.2% | 1.58 | 0.1% |
| 122 | Clawitzer | 0.1% | 0.2% | 2.01 | 0.1% |
| 122 | Crabominable | 0.2% | 0.1% | 0.83 | 0.2% |
| 123 | Meowstic-F | 0.3% | 0.1% | 0.49 | 0.3% |
| 128 | Abomasnow | 0.2% | 0.1% | 0.70 | 0.2% |
| 129 | Ampharos | 0.2% | 0.1% | 0.71 | 0.2% |
| 130 | Steelix | 0.1% | 0.1% | 0.96 | 0.2% |
| 131 | Raichu-Alola | 0.1% | 0.1% | 1.13 | 0.1% |
| 132 | Azumarill | 0.1% | 0.1% | 1.03 | 0.1% |
| 134 | Gliscor | 0.2% | 0.1% | 0.61 | 0.2% |
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

## 9. Definitions
Over team_query's 2957 teams shared 2026-09-09 to 2026-10-01: 24 definitions and 19 variants from 218 candidates (346 seeds); floor 45 teams, ceiling 1479 teams; admission ran 4 rounds, 3 absorbed. Teams: exclusive 1353 (45.8%), hybrid 1136 (38.4%), uncovered 468 (15.8%) (a variant counts as its parent). Recent: 1484 teams dated 2026-09-25 to 2026-10-01; earlier: 1473 teams.

### 9.1 Ranked definitions
| Rank | Name | Key | Teams | Share | Weighted | Rosters | Own | New | Recent | Earlier | Variants | Origin |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Tailwind Mega Salamence + Rillaboom | `Rillaboom\|tailwind\|Salamence-Mega` | 571 | 19.3% | 19.3% | 207 | 165 | 100.0% | 14.0% | 24.6% | 3 | mode-mega + Rillaboom (75.8%, lift 1.38) |
| 2 | Psyspam Indeedee-F | `Indeedee-F\|psyspam\|(none)` | 436 | 14.7% | 14.6% | 282 | 163 | 100.0% | 12.7% | 16.8% | 3 | mode + Indeedee-F (69.1%, lift 3.69) |
| 3 | Trick Room | `(none)\|trick-room\|(none)` | 420 | 14.2% | 14.2% | 312 | 112 | 100.0% | 13.2% | 15.2% | 3 | mode |
| 4 | Tailwind Mega Raichu-Y + Rillaboom + Gholdengo | `Gholdengo+Rillaboom\|tailwind\|Raichu-Mega-Y` | 383 | 13.0% | 13.0% | 57 | 214 | 61.1% | 15.2% | 10.7% | 1 | triple + Mega Raichu-Y (99.8%, lift 4.23) + Tailwind (83.8%, lift 1.49) |
| 5 | Sun Mega Charizard-Y | `(none)\|sun\|Charizard-Mega-Y` | 357 | 12.1% | 12.1% | 171 | 79 | 100.0% | 12.7% | 11.5% | 3 | mode-mega |
| 6 | Rain Tailwind Archaludon + Pelipper | `Archaludon+Pelipper\|rain+tailwind\|(none)` | 225 | 7.6% | 7.7% | 142 | 65 | 88.9% | 7.4% | 7.8% | 3 | pair + Rain (100.0%, lift 5.79) + Tailwind (91.1%, lift 1.62) |
| 7 | Setup Mega Floette + Rillaboom + Incineroar | `Incineroar+Rillaboom\|setup\|Floette-Mega` | 176 | 6.0% | 6.1% | 44 | 60 | 68.8% | 5.8% | 6.1% | 0 | triple + Mega Floette (99.6%, lift 7.85) + Setup (76.2%, lift 3.86) |
| 8 | Sand Mega Salamence + Mega Tyranitar + Excadrill | `Excadrill\|sand\|Salamence-Mega+Tyranitar-Mega` | 165 | 5.6% | 5.6% | 33 | 95 | 70.9% | 6.0% | 5.2% | 0 | triple + Sand (99.4%, lift 10.54) + Mega Salamence (99.4%, lift 3.13) + Mega Tyranitar (95.9%, lift 11.77) |
| 9 | Rain Archaludon + Politoed | `Archaludon+Politoed\|rain\|(none)` | 146 | 4.9% | 5.0% | 61 | 69 | 100.0% | 7.1% | 2.7% | 3 | pair + Rain (100.0%, lift 5.79) |
| 10 | Mega Garchomp-Z + Rillaboom + Incineroar | `Incineroar+Rillaboom\|(none)\|Garchomp-Mega-Z` | 96 | 3.2% | 3.3% | 36 | 46 | 80.2% | 3.2% | 3.3% | 0 | pair + Mega Garchomp-Z (60.4%, lift 8.16) + Rillaboom (80.7%, lift 1.47) |
| 11 | Snow Mega Froslass + Rillaboom + Sneasler | `Rillaboom+Sneasler\|snow\|Froslass-Mega` | 97 | 3.3% | 3.3% | 28 | 56 | 63.9% | 3.3% | 3.3% | 0 | triple + Mega Froslass (100.0%, lift 17.19) + Snow (100.0%, lift 11.92) |
| 12 | Tailwind Basculegion + Whimsicott | `Basculegion+Whimsicott\|tailwind\|(none)` | 66 | 2.2% | 2.2% | 35 | 10 | 97.0% | 2.7% | 1.8% | 0 | pair + Tailwind (100.0%, lift 1.78) |
| 13 | Tailwind Mega Glimmora | `(none)\|tailwind\|Glimmora-Mega` | 65 | 2.2% | 2.2% | 42 | 27 | 60.0% | 2.0% | 2.4% | 0 | mode-mega |
| 14 | Tailwind Mega Dragonite | `(none)\|tailwind\|Dragonite-Mega` | 60 | 2.0% | 2.1% | 31 | 15 | 78.3% | 2.9% | 1.2% | 0 | mode-mega |
| 15 | Setup Mega Delphox + Sneasler | `Sneasler\|setup\|Delphox-Mega` | 61 | 2.1% | 2.1% | 22 | 47 | 85.2% | 3.0% | 1.2% | 0 | mode-mega + Sneasler (77.2%, lift 1.80) |
| 16 | Tailwind Garchomp + Whimsicott | `Garchomp+Whimsicott\|tailwind\|(none)` | 56 | 1.9% | 1.9% | 37 | 6 | 55.4% | 2.0% | 1.8% | 0 | pair + Tailwind (100.0%, lift 1.78) |
| 17 | Tailwind Kingambit + Farigiraf + Sylveon | `Farigiraf+Kingambit+Sylveon\|tailwind\|(none)` | 54 | 1.8% | 1.8% | 9 | 5 | 77.8% | 1.8% | 1.8% | 0 | triple + Tailwind (85.7%, lift 1.53) |
| 18 | Mega Raichu-Y + Rillaboom + Volcarona | `Rillaboom+Volcarona\|(none)\|Raichu-Mega-Y` | 53 | 1.8% | 1.8% | 31 | 23 | 49.1% | 2.9% | 0.7% | 0 | triple + Mega Raichu-Y (100.0%, lift 4.24) |
| 19 | Rain Mega Golisopod + Farigiraf + Pelipper | `Farigiraf+Pelipper\|rain\|Golisopod-Mega` | 52 | 1.8% | 1.8% | 31 | 9 | 36.5% | 2.0% | 1.6% | 0 | triple + Mega Golisopod (100.0%, lift 7.32) + Rain (100.0%, lift 5.79) |
| 20 | Psyspam Sneasler + Milotic + Indeedee | `Indeedee+Milotic+Sneasler\|psyspam\|(none)` | 51 | 1.7% | 1.7% | 13 | 34 | 100.0% | 1.2% | 2.2% | 0 | triple + Psyspam (100.0%, lift 4.69) |
| 21 | Mega Metagross + Indeedee-F | `Indeedee-F\|(none)\|Metagross-Mega` | 50 | 1.7% | 1.7% | 33 | 26 | 70.0% | 1.1% | 2.3% | 0 | pair + Mega Metagross (98.0%, lift 18.58) |
| 22 | Mega Salamence + Mega Floette + Kingambit | `Kingambit\|(none)\|Floette-Mega+Salamence-Mega` | 50 | 1.7% | 1.7% | 24 | 10 | 32.0% | 0.9% | 2.4% | 0 | triple + Mega Floette (100.0%, lift 7.89) + Mega Salamence (100.0%, lift 3.15) |
| 23 | Mega Lucario-Z + Rillaboom + Incineroar | `Incineroar+Rillaboom\|(none)\|Lucario-Mega-Z` | 50 | 1.7% | 1.7% | 30 | 15 | 40.0% | 1.0% | 2.4% | 0 | triple + Mega Lucario-Z (100.0%, lift 35.63) |
| 24 | Rain Tailwind Mega Golisopod + Basculegion + Pelipper | `Basculegion+Pelipper\|rain+tailwind\|Golisopod-Mega` | 49 | 1.7% | 1.7% | 19 | 2 | 36.7% | 0.9% | 2.4% | 0 | triple + Mega Golisopod (100.0%, lift 7.32) + Rain (100.0%, lift 5.79) + Tailwind (89.1%, lift 1.59) |

Staple cores (Pokémon found together on many teams but less than definitionBareMinLift times chance; not archetypes by themselves): Rillaboom + Incineroar 614 teams (20.8%, lift 1.27), Sneasler + Kingambit 421 teams (14.2%, lift 1.33), Rillaboom + Volcarona 160 teams (5.4%, lift 1.46), Kingambit + Garchomp 155 teams (5.2%, lift 1.35) and 7 more below 5%.

### 9.2 Definitions in detail
New counts a definition's teams in no related definition ranked above it (sharing a Pokémon or a mode); Overlaps and Own count every definition, so a definition can be mostly new yet share most of its teams with an unrelated one.

#### 1. Tailwind Mega Salamence + Rillaboom
- Key: `Rillaboom|tailwind|Salamence-Mega`
- Requires: Pokémon Rillaboom, Mega Salamence; modes Tailwind; Megas Mega Salamence
- 571 teams (19.3%), weighted 19.3%, 207 rosters, 165 in no other definition; recent 14.0% of 1484 teams vs. earlier 24.6% of 1473; 100.0% of its teams new at admission (in no related definition ranked above it)
- Origin: mode-mega seed `(none)|tailwind|Salamence-Mega`, then Rillaboom (75.8%, lift 1.38)
- Overlaps: Tailwind Mega Raichu-Y + Rillaboom + Gholdengo (149 teams); Setup Mega Floette + Rillaboom + Incineroar (53 teams); Sand Mega Salamence + Mega Tyranitar + Excadrill (48 teams)
- Variants:
  - Tailwind Mega Salamence + Rillaboom + Sneasler `Rillaboom+Sneasler|tailwind|Salamence-Mega`: 324 teams (11.0%), weighted 11.0%, 92 rosters, 118 in no other definition; recent 7.1% of 1484 teams vs. earlier 14.9% of 1473; origin pair + Mega Salamence (100.0%, lift 3.15) + Tailwind (75.7%, lift 1.35) + Rillaboom (73.8%, lift 1.35)
  - Tailwind Mega Salamence + Rillaboom + Gholdengo `Gholdengo+Rillaboom|tailwind|Salamence-Mega`: 294 teams (9.9%), weighted 9.9%, 65 rosters, 51 in no other definition; recent 7.5% of 1484 teams vs. earlier 12.4% of 1473; origin triple + Mega Salamence (100.0%, lift 3.15) + Tailwind (91.6%, lift 1.63)
  - Tailwind Mega Salamence + Rillaboom + Arcanine-Hisui `Arcanine-Hisui+Rillaboom|tailwind|Salamence-Mega`: 242 teams (8.2%), weighted 8.2%, 53 rosters, 89 in no other definition; recent 6.2% of 1484 teams vs. earlier 10.2% of 1473; origin triple + Mega Salamence (100.0%, lift 3.15) + Tailwind (92.7%, lift 1.65)
- Sample rosters:
  - Arcanine-Hisui / Gholdengo / Raichu / Rillaboom / Salamence / Sneasler: 55 copies, best 32, first 2026-09-20, https://pokepast.es/ecdecc58b9116d68
  - Floette-Eternal / Gholdengo / Incineroar / Rillaboom / Salamence / Sneasler: 36 copies, best 1, first 2026-09-09, https://pokepast.es/6f1d5b2b15285f2b
  - Excadrill / Gholdengo / Milotic / Rillaboom / Salamence / Tyranitar: 34 copies, best 1, first 2026-09-13, https://pokepast.es/98d6e2fba3995368
  - Arcanine-Hisui / Basculegion / Kingambit / Rillaboom / Salamence / Sneasler: 23 copies, best 1, first 2026-09-10, https://pokepast.es/2c92e63c0fd4a43c
  - Arcanine-Hisui / Gholdengo / Milotic / Raichu / Rillaboom / Salamence: 20 copies, best 35, first 2026-09-20, https://standings.limitlessvgc.com/0038/player/0250/teamlist

#### 2. Psyspam Indeedee-F
- Key: `Indeedee-F|psyspam|(none)`
- Requires: Pokémon Indeedee-F; modes Psyspam; Megas none
- 436 teams (14.7%), weighted 14.6%, 282 rosters, 163 in no other definition; recent 12.7% of 1484 teams vs. earlier 16.8% of 1473; 100.0% of its teams new at admission (in no related definition ranked above it)
- Origin: mode seed `(none)|psyspam|(none)`, then Indeedee-F (69.1%, lift 3.69)
- Overlaps: Trick Room (173 teams); Sun Mega Charizard-Y (41 teams); Tailwind Basculegion + Whimsicott (19 teams)
- Variants:
  - Psyspam Indeedee-F + Armarouge `Armarouge+Indeedee-F|psyspam|(none)`: 183 teams (6.2%), weighted 6.1%, 125 rosters, 77 in no other definition; recent 5.5% of 1484 teams vs. earlier 6.9% of 1473; origin pair + Psyspam (100.0%, lift 4.69)
  - Psyspam Mega Gardevoir + Sneasler + Indeedee-F `Indeedee-F+Sneasler|psyspam|Gardevoir-Mega`: 150 teams (5.1%), weighted 5.0%, 84 rosters, 73 in no other definition; recent 3.4% of 1484 teams vs. earlier 6.8% of 1473; origin triple + Mega Gardevoir (100.0%, lift 12.91) + Psyspam (100.0%, lift 4.69)
  - Psyspam Mega Golisopod + Indeedee-F `Indeedee-F|psyspam|Golisopod-Mega`: 87 teams (2.9%), weighted 2.9%, 56 rosters, 21 in no other definition; recent 2.5% of 1484 teams vs. earlier 3.4% of 1473; origin pair + Mega Golisopod (100.0%, lift 7.32) + Psyspam (81.3%, lift 3.81)
- Sample rosters:
  - Camerupt / Farigiraf / Hatterene / Incineroar / Indeedee-F / Kingambit: 17 copies, best 38, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0940/teamlist
  - Basculegion / Gardevoir / Golisopod / Indeedee-F / Pelipper / Sneasler: 14 copies, best 6, first 2026-09-12, https://pokepast.es/1f4da9800a6ad851
  - Armarouge / Dragapult / Gengar / Indeedee-F / Milotic / Staraptor: 14 copies, best 16, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/10gY7gYrOnPAGK7Nudmq
  - Basculegion / Gardevoir / Indeedee-F / Kommo-o / Pyroar / Whimsicott: 14 copies, best 18, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/1040/teamlist
  - Armarouge / Gardevoir / Indeedee-F / Kingambit / Sneasler / Torkoal: 11 copies, best 53, first 2026-09-10, https://standings.limitlessvgc.com/0037/player/0218/teamlist

#### 3. Trick Room
- Key: `(none)|trick-room|(none)`
- Requires: Pokémon none; modes Trick Room; Megas none
- 420 teams (14.2%), weighted 14.2%, 312 rosters, 112 in no other definition; recent 13.2% of 1484 teams vs. earlier 15.2% of 1473; 100.0% of its teams new at admission (in no related definition ranked above it)
- Origin: mode seed `(none)|trick-room|(none)`
- Overlaps: Psyspam Indeedee-F (173 teams); Sun Mega Charizard-Y (66 teams); Rain Archaludon + Politoed (53 teams)
- Variants:
  - Rain Trick Room Mega Golisopod + Farigiraf + Politoed `Farigiraf+Politoed|rain+trick-room|Golisopod-Mega`: 54 teams (1.8%), weighted 1.8%, 20 rosters, 9 in no other definition; recent 2.9% of 1484 teams vs. earlier 0.7% of 1473; origin pair + Rain (100.0%, lift 5.79) + Mega Golisopod (95.8%, lift 7.01) + Trick Room (78.3%, lift 5.51)
  - Trick Room Mega Camerupt + Farigiraf `Farigiraf|trick-room|Camerupt-Mega`: 51 teams (1.7%), weighted 1.7%, 32 rosters, 19 in no other definition; recent 2.0% of 1484 teams vs. earlier 1.4% of 1473; origin mode-mega + Farigiraf (77.3%, lift 5.51)
  - Rain Trick Room Mega Golisopod + Archaludon + Politoed `Archaludon+Politoed|rain+trick-room|Golisopod-Mega`: 50 teams (1.7%), weighted 1.7%, 17 rosters, 0 in no other definition; recent 2.9% of 1484 teams vs. earlier 0.5% of 1473; origin triple + Mega Golisopod (100.0%, lift 7.32) + Rain (100.0%, lift 5.79) + Trick Room (65.8%, lift 4.63)
- Sample rosters:
  - Archaludon / Charizard / Farigiraf / Golisopod / Grimmsnarl / Politoed: 33 copies, best 2, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/i4SPzSax7gWzIckK52KU
  - Camerupt / Farigiraf / Hatterene / Incineroar / Indeedee-F / Kingambit: 17 copies, best 38, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0940/teamlist
  - Armarouge / Gardevoir / Indeedee-F / Kingambit / Sneasler / Torkoal: 11 copies, best 53, first 2026-09-10, https://standings.limitlessvgc.com/0037/player/0218/teamlist
  - Arcanine-Hisui / Farigiraf / Kingambit / Rillaboom / Salamence / Sylveon: 7 copies, best 116, first 2026-09-17, https://standings.limitlessvgc.com/0037/player/0640/teamlist
  - Charizard / Indeedee / Kingambit / Kommo-o / Meowstic-F / Sneasler: 4 copies, best 3, first 2026-09-22, https://pokepast.es/0ae371a15057851a

#### 4. Tailwind Mega Raichu-Y + Rillaboom + Gholdengo
- Key: `Gholdengo+Rillaboom|tailwind|Raichu-Mega-Y`
- Requires: Pokémon Gholdengo, Rillaboom, Mega Raichu-Y; modes Tailwind; Megas Mega Raichu-Y
- 383 teams (13.0%), weighted 13.0%, 57 rosters, 214 in no other definition; recent 15.2% of 1484 teams vs. earlier 10.7% of 1473; 61.1% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Gholdengo+Raichu+Rillaboom|(none)|(none)`, then Mega Raichu-Y (99.8%, lift 4.23), Tailwind (83.8%, lift 1.49)
- Overlaps: Tailwind Mega Salamence + Rillaboom (149 teams); Mega Raichu-Y + Rillaboom + Volcarona (13 teams); Mega Garchomp-Z + Rillaboom + Incineroar (8 teams)
- Variants:
  - Tailwind Setup Mega Raichu-Y + Rillaboom + Gholdengo `Gholdengo+Rillaboom|tailwind+setup|Raichu-Mega-Y`: 108 teams (3.7%), weighted 3.7%, 27 rosters, 77 in no other definition; recent 5.3% of 1484 teams vs. earlier 2.0% of 1473; origin mode-mega + Rillaboom (96.2%, lift 1.76) + Gholdengo (86.4%, lift 3.01) + Tailwind (70.6%, lift 1.26)
- Sample rosters:
  - Arcanine-Hisui / Gholdengo / Raichu / Rillaboom / Staraptor / Sylveon: 122 copies, best 1, first 2026-09-14, https://pokepast.es/2d234b4ec11a9aca
  - Arcanine-Hisui / Gholdengo / Raichu / Rillaboom / Salamence / Sneasler: 55 copies, best 32, first 2026-09-20, https://pokepast.es/ecdecc58b9116d68
  - Ceruledge / Gholdengo / Milotic / Raichu / Rillaboom / Staraptor: 49 copies, best 5, first 2026-09-20, https://pokepast.es/470a6ec2468af8a4
  - Arcanine-Hisui / Gholdengo / Milotic / Raichu / Rillaboom / Salamence: 20 copies, best 35, first 2026-09-20, https://standings.limitlessvgc.com/0038/player/0250/teamlist
  - Gholdengo / Incineroar / Raichu / Rillaboom / Salamence / Sneasler: 19 copies, best 1, first 2026-09-18, https://pokepast.es/d6b2f01ddf4fee52

#### 5. Sun Mega Charizard-Y
- Key: `(none)|sun|Charizard-Mega-Y`
- Requires: Pokémon Mega Charizard-Y; modes Sun; Megas Mega Charizard-Y
- 357 teams (12.1%), weighted 12.1%, 171 rosters, 79 in no other definition; recent 12.7% of 1484 teams vs. earlier 11.5% of 1473; 100.0% of its teams new at admission (in no related definition ranked above it)
- Origin: mode-mega seed `(none)|sun|Charizard-Mega-Y`
- Overlaps: Trick Room (66 teams); Rain Tailwind Archaludon + Pelipper (62 teams); Rain Archaludon + Politoed (51 teams)
- Variants:
  - Sun Mega Charizard-Y + Garchomp `Garchomp|sun|Charizard-Mega-Y`: 157 teams (5.3%), weighted 5.3%, 78 rosters, 47 in no other definition; recent 5.1% of 1484 teams vs. earlier 5.6% of 1473; origin pair + Mega Charizard-Y (100.0%, lift 8.28) + Sun (100.0%, lift 6.51)
  - Sun Mega Charizard-Y + Farigiraf `Farigiraf|sun|Charizard-Mega-Y`: 118 teams (4.0%), weighted 4.0%, 36 rosters, 13 in no other definition; recent 5.3% of 1484 teams vs. earlier 2.6% of 1473; origin pair + Mega Charizard-Y (100.0%, lift 8.28) + Sun (100.0%, lift 6.51)
  - Sun Rain Screens Mega Charizard-Y + Archaludon + Grimmsnarl `Archaludon+Grimmsnarl|sun+rain+screens|Charizard-Mega-Y`: 95 teams (3.2%), weighted 3.2%, 18 rosters, 0 in no other definition; recent 3.8% of 1484 teams vs. earlier 2.6% of 1473; origin pair + Rain (100.0%, lift 5.79) + Screens (99.2%, lift 15.12) + Mega Charizard-Y (74.8%, lift 6.20) + Sun (100.0%, lift 6.51)
- Sample rosters:
  - Archaludon / Charizard / Farigiraf / Golisopod / Grimmsnarl / Politoed: 38 copies, best 2, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/i4SPzSax7gWzIckK52KU
  - Aerodactyl / Charizard / Farigiraf / Garchomp / Kingambit / Sylveon: 35 copies, best 16, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/0lbS1fGaeHdeq74HLZWq
  - Archaludon / Charizard / Grimmsnarl / Pelipper / Swampert / Venusaur: 19 copies, best 8, first 2026-09-20, https://standings.limitlessvgc.com/0038/player/0137/teamlist
  - Basculegion / Charizard / Gardevoir / Indeedee-F / Sneasler / Venusaur: 10 copies, best 209, first 2026-09-09, https://standings.limitlessvgc.com/0038/player/0135/teamlist
  - Archaludon / Charizard / Golisopod / Grimmsnarl / Pelipper / Venusaur: 9 copies, best 128, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0568/teamlist

#### 6. Rain Tailwind Archaludon + Pelipper
- Key: `Archaludon+Pelipper|rain+tailwind|(none)`
- Requires: Pokémon Archaludon, Pelipper; modes Rain, Tailwind; Megas none
- 225 teams (7.6%), weighted 7.7%, 142 rosters, 65 in no other definition; recent 7.4% of 1484 teams vs. earlier 7.8% of 1473; 88.9% of its teams new at admission (in no related definition ranked above it)
- Origin: pair seed `Archaludon+Pelipper|(none)|(none)`, then Rain (100.0%, lift 5.79), Tailwind (91.1%, lift 1.62)
- Overlaps: Sun Mega Charizard-Y (62 teams); Rain Mega Golisopod + Farigiraf + Pelipper (33 teams); Rain Tailwind Mega Golisopod + Basculegion + Pelipper (28 teams)
- Variants:
  - Rain Tailwind Mega Golisopod + Archaludon + Pelipper `Archaludon+Pelipper|rain+tailwind|Golisopod-Mega`: 106 teams (3.6%), weighted 3.6%, 59 rosters, 18 in no other definition; recent 2.5% of 1484 teams vs. earlier 4.7% of 1473; origin triple + Mega Golisopod (100.0%, lift 7.32) + Rain (100.0%, lift 5.79) + Tailwind (89.8%, lift 1.60)
  - Rain Tailwind Mega Swampert + Archaludon + Pelipper `Archaludon+Pelipper|rain+tailwind|Swampert-Mega`: 84 teams (2.8%), weighted 2.8%, 49 rosters, 29 in no other definition; recent 2.8% of 1484 teams vs. earlier 2.9% of 1473; origin triple + Mega Swampert (100.0%, lift 25.06) + Rain (100.0%, lift 5.79) + Tailwind (89.4%, lift 1.59)
  - Rain Tailwind Basculegion + Archaludon + Pelipper `Archaludon+Basculegion+Pelipper|rain+tailwind|(none)`: 52 teams (1.8%), weighted 1.8%, 33 rosters, 14 in no other definition; recent 1.3% of 1484 teams vs. earlier 2.2% of 1473; origin triple + Rain (100.0%, lift 5.79) + Tailwind (85.2%, lift 1.52)
- Sample rosters:
  - Archaludon / Charizard / Grimmsnarl / Pelipper / Swampert / Venusaur: 19 copies, best 8, first 2026-09-20, https://standings.limitlessvgc.com/0038/player/0137/teamlist
  - Archaludon / Basculegion / Golisopod / Pelipper / Rillaboom / Salamence: 12 copies, best 21, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0950/teamlist
  - Archaludon / Charizard / Golisopod / Grimmsnarl / Pelipper / Venusaur: 9 copies, best 128, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0568/teamlist
  - Archaludon / Charizard / Garchomp / Grimmsnarl / Pelipper / Venusaur: 6 copies, best 1, first 2026-09-27, https://standings.limitlessvgc.com/0038/player/0267/teamlist
  - Archaludon / Golisopod / Grimmsnarl / Pelipper / Sinistcha / Swampert: 6 copies, best 7, first 2026-09-10, https://pokepast.es/5cdc704aa8f08294

#### 7. Setup Mega Floette + Rillaboom + Incineroar
- Key: `Incineroar+Rillaboom|setup|Floette-Mega`
- Requires: Pokémon Incineroar, Rillaboom, Mega Floette; modes Setup; Megas Mega Floette
- 176 teams (6.0%), weighted 6.1%, 44 rosters, 60 in no other definition; recent 5.8% of 1484 teams vs. earlier 6.1% of 1473; 68.8% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Floette-Eternal+Incineroar+Rillaboom|(none)|(none)`, then Mega Floette (99.6%, lift 7.85), Setup (76.2%, lift 3.86)
- Overlaps: Tailwind Mega Salamence + Rillaboom (53 teams); Tailwind Mega Dragonite (23 teams); Mega Garchomp-Z + Rillaboom + Incineroar (11 teams)
- Variants: none
- Sample rosters:
  - Floette-Eternal / Gholdengo / Incineroar / Rillaboom / Salamence / Sneasler: 35 copies, best 1, first 2026-09-09, https://pokepast.es/6f1d5b2b15285f2b
  - Floette-Eternal / Gholdengo / Incineroar / Raichu / Rillaboom / Sneasler: 31 copies, best 5, first 2026-09-13, https://pokepast.es/a0582a49f5490809
  - Dragonite / Floette-Eternal / Gholdengo / Incineroar / Rillaboom / Sneasler: 23 copies, best 1, first 2026-09-20, https://pokepast.es/c06c65c34f56a094
  - Delphox / Floette-Eternal / Incineroar / Kingambit / Rillaboom / Sneasler: 8 copies, best 8, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/rD6PrJBrCfzicynLnRAI
  - Floette-Eternal / Incineroar / Kingambit / Rillaboom / Salamence / Sneasler: 8 copies, best 445, first 2026-09-09, https://standings.limitlessvgc.com/0037/player/0624/teamlist

#### 8. Sand Mega Salamence + Mega Tyranitar + Excadrill
- Key: `Excadrill|sand|Salamence-Mega+Tyranitar-Mega`
- Requires: Pokémon Excadrill, Mega Salamence, Mega Tyranitar; modes Sand; Megas Mega Salamence, Mega Tyranitar
- 165 teams (5.6%), weighted 5.6%, 33 rosters, 95 in no other definition; recent 6.0% of 1484 teams vs. earlier 5.2% of 1473; 70.9% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Excadrill+Salamence+Tyranitar|(none)|(none)`, then Sand (99.4%, lift 10.54), Mega Salamence (99.4%, lift 3.13), Mega Tyranitar (95.9%, lift 11.77)
- Overlaps: Tailwind Mega Salamence + Rillaboom (48 teams); Psyspam Sneasler + Milotic + Indeedee (16 teams); Psyspam Indeedee-F (5 teams)
- Variants: none
- Sample rosters:
  - Corviknight / Excadrill / Indeedee / Salamence / Sneasler / Tyranitar: 45 copies, best 1, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/DqQtlduXE6DDLOKZXBHD
  - Excadrill / Gholdengo / Milotic / Rillaboom / Salamence / Tyranitar: 43 copies, best 1, first 2026-09-13, https://pokepast.es/43bdff98a519db09
  - Excadrill / Indeedee / Milotic / Salamence / Sneasler / Tyranitar: 16 copies, best 157, first 2026-09-10, https://standings.limitlessvgc.com/0037/player/0196/teamlist
  - Excadrill / Gholdengo / Indeedee / Salamence / Sneasler / Tyranitar: 11 copies, best 90, first 2026-09-09, https://standings.limitlessvgc.com/0037/player/0600/teamlist
  - Excadrill / Milotic / Rillaboom / Salamence / Sneasler / Tyranitar: 6 copies, best 236, first 2026-09-16, https://standings.limitlessvgc.com/0038/player/0107/teamlist

#### 9. Rain Archaludon + Politoed
- Key: `Archaludon+Politoed|rain|(none)`
- Requires: Pokémon Archaludon, Politoed; modes Rain; Megas none
- 146 teams (4.9%), weighted 5.0%, 61 rosters, 69 in no other definition; recent 7.1% of 1484 teams vs. earlier 2.7% of 1473; 100.0% of its teams new at admission (in no related definition ranked above it)
- Origin: pair seed `Archaludon+Politoed|(none)|(none)`, then Rain (100.0%, lift 5.79)
- Overlaps: Trick Room (53 teams); Sun Mega Charizard-Y (51 teams); Psyspam Indeedee-F (5 teams)
- Variants:
  - Rain Perish Trap Mega Gengar + Archaludon + Politoed `Archaludon+Politoed|rain+perish-trap|Gengar-Mega`: 63 teams (2.1%), weighted 2.2%, 20 rosters, 58 in no other definition; recent 2.5% of 1484 teams vs. earlier 1.8% of 1473; origin triple + Mega Gengar (100.0%, lift 18.60) + Rain (100.0%, lift 5.79) + Perish Trap (95.5%, lift 24.98)
  - Rain Perish Trap Incineroar + Archaludon + Politoed `Archaludon+Incineroar+Politoed|rain+perish-trap|(none)`: 60 teams (2.0%), weighted 2.1%, 17 rosters, 56 in no other definition; recent 2.4% of 1484 teams vs. earlier 1.7% of 1473; origin triple + Rain (100.0%, lift 5.79) + Perish Trap (90.9%, lift 23.79)
  - Rain Screens Archaludon + Politoed + Grimmsnarl `Archaludon+Grimmsnarl+Politoed|rain+screens|(none)`: 47 teams (1.6%), weighted 1.6%, 8 rosters, 0 in no other definition; recent 3.0% of 1484 teams vs. earlier 0.2% of 1473; origin triple + Screens (100.0%, lift 15.24) + Rain (100.0%, lift 5.79)
- Sample rosters:
  - Archaludon / Charizard / Farigiraf / Golisopod / Grimmsnarl / Politoed: 38 copies, best 2, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/i4SPzSax7gWzIckK52KU
  - Archaludon / Gengar / Incineroar / Politoed / Rillaboom / Vivillon: 21 copies, best 3, first 2026-09-20, https://pokepast.es/d8e589c174266e5f
  - Archaludon / Froslass / Gengar / Incineroar / Politoed / Rillaboom: 10 copies, best 5, first 2026-09-13, https://pokepast.es/8782bc837073b808
  - Archaludon / Gengar / Golisopod / Incineroar / Politoed / Rillaboom: 8 copies, best 10, first 2026-09-20, https://pokepast.es/2f1c6631010c3644
  - Archaludon / Gengar / Incineroar / Politoed / Rillaboom / Swampert: 4 copies, best 79, first 2026-09-20, https://standings.limitlessvgc.com/0038/player/0188/teamlist

#### 10. Mega Garchomp-Z + Rillaboom + Incineroar
- Key: `Incineroar+Rillaboom|(none)|Garchomp-Mega-Z`
- Requires: Pokémon Incineroar, Rillaboom, Mega Garchomp-Z; modes none; Megas Mega Garchomp-Z
- 96 teams (3.2%), weighted 3.3%, 36 rosters, 46 in no other definition; recent 3.2% of 1484 teams vs. earlier 3.3% of 1473; 80.2% of its teams new at admission (in no related definition ranked above it)
- Origin: pair seed `Garchomp+Incineroar|(none)|(none)`, then Mega Garchomp-Z (60.4%, lift 8.16), Rillaboom (80.7%, lift 1.47)
- Overlaps: Mega Raichu-Y + Rillaboom + Volcarona (13 teams); Mega Lucario-Z + Rillaboom + Incineroar (12 teams); Setup Mega Floette + Rillaboom + Incineroar (11 teams)
- Variants: none
- Sample rosters:
  - Garchomp / Incineroar / Metagross / Rillaboom / Sneasler / Volcarona: 14 copies, best 10, first 2026-09-12, https://pokepast.es/e74e8282d41a3802
  - Basculegion / Garchomp / Incineroar / Lucario / Rillaboom / Sneasler: 10 copies, best 28, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0029/teamlist
  - Garchomp / Gholdengo / Incineroar / Raichu / Rillaboom / Volcarona: 9 copies, best 1, first 2026-09-27, https://pokepast.es/0e35fd9cd52d7552
  - Garchomp / Gholdengo / Incineroar / Raichu / Rillaboom / Sneasler: 9 copies, best 32, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0656/teamlist
  - Floette-Eternal / Garchomp / Incineroar / Kingambit / Rillaboom / Sneasler: 7 copies, best 21, first 2026-09-10, https://standings.limitlessvgc.com/0039/player/0546/teamlist

#### 11. Snow Mega Froslass + Rillaboom + Sneasler
- Key: `Rillaboom+Sneasler|snow|Froslass-Mega`
- Requires: Pokémon Rillaboom, Sneasler, Mega Froslass; modes Snow; Megas Mega Froslass
- 97 teams (3.3%), weighted 3.3%, 28 rosters, 56 in no other definition; recent 3.3% of 1484 teams vs. earlier 3.3% of 1473; 63.9% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Froslass+Rillaboom+Sneasler|(none)|(none)`, then Mega Froslass (100.0%, lift 17.19), Snow (100.0%, lift 11.92)
- Overlaps: Tailwind Mega Salamence + Rillaboom (34 teams); Sun Mega Charizard-Y (3 teams); Trick Room (1 teams)
- Variants: none
- Sample rosters:
  - Arcanine-Hisui / Froslass / Kingambit / Raichu / Rillaboom / Sneasler: 39 copies, best 8, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/xwGrkGvg8PXbgIxsRNlv
  - Arcanine-Hisui / Froslass / Kingambit / Rillaboom / Salamence / Sneasler: 16 copies, best 282, first 2026-09-16, https://standings.limitlessvgc.com/0037/player/0911/teamlist
  - Arcanine-Hisui / Froslass / Gholdengo / Rillaboom / Salamence / Sneasler: 9 copies, best 321, first 2026-09-13, https://standings.limitlessvgc.com/0037/player/0799/teamlist
  - Basculegion / Froslass / Kingambit / Rillaboom / Salamence / Sneasler: 4 copies, best 179, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0003/teamlist
  - Charizard / Froslass / Garchomp / Kingambit / Rillaboom / Sneasler: 3 copies, best 26, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0381/teamlist

#### 12. Tailwind Basculegion + Whimsicott
- Key: `Basculegion+Whimsicott|tailwind|(none)`
- Requires: Pokémon Basculegion, Whimsicott; modes Tailwind; Megas none
- 66 teams (2.2%), weighted 2.2%, 35 rosters, 10 in no other definition; recent 2.7% of 1484 teams vs. earlier 1.8% of 1473; 97.0% of its teams new at admission (in no related definition ranked above it)
- Origin: pair seed `Basculegion+Whimsicott|(none)|(none)`, then Tailwind (100.0%, lift 1.78)
- Overlaps: Sun Mega Charizard-Y (20 teams); Tailwind Garchomp + Whimsicott (20 teams); Psyspam Indeedee-F (19 teams)
- Variants: none
- Sample rosters:
  - Basculegion / Gardevoir / Indeedee-F / Kommo-o / Pyroar / Whimsicott: 14 copies, best 18, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/1040/teamlist
  - Basculegion / Charizard / Floette-Eternal / Garchomp / Kingambit / Whimsicott: 8 copies, best 8, first 2026-09-20, https://pokepast.es/ffe1c04c186b2453
  - Arcanine-Hisui / Basculegion / Gardevoir / Indeedee-F / Kommo-o / Whimsicott: 3 copies, best 57, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0696/teamlist
  - Basculegion / Charizard / Floette-Eternal / Garchomp / Incineroar / Whimsicott: 3 copies, best 209, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0653/teamlist
  - Basculegion / Glimmora / Indeedee / Kommo-o / Typhlosion-Hisui / Whimsicott: 3 copies, best 287, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0589/teamlist

#### 13. Tailwind Mega Glimmora
- Key: `(none)|tailwind|Glimmora-Mega`
- Requires: Pokémon Mega Glimmora; modes Tailwind; Megas Mega Glimmora
- 65 teams (2.2%), weighted 2.2%, 42 rosters, 27 in no other definition; recent 2.0% of 1484 teams vs. earlier 2.4% of 1473; 60.0% of its teams new at admission (in no related definition ranked above it)
- Origin: mode-mega seed `(none)|tailwind|Glimmora-Mega`
- Overlaps: Tailwind Mega Salamence + Rillaboom (15 teams); Tailwind Basculegion + Whimsicott (11 teams); Sun Mega Charizard-Y (8 teams)
- Variants: none
- Sample rosters:
  - Archaludon / Garchomp / Glimmora / Klefki / Salamence / Volcarona: 7 copies, best 16, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/Dyf3yhlUQmb6KQ05E4RR
  - Basculegion / Glimmora / Kingambit / Rillaboom / Salamence / Volcarona: 5 copies, best 53, first 2026-09-12, https://standings.limitlessvgc.com/0039/player/0364/teamlist
  - Farigiraf / Glimmora / Golisopod / Incineroar / Sirfetch’d / Whimsicott: 4 copies, best 8, first 2026-09-14, https://pokepast.es/ecf978d5f23a5b2f
  - Garchomp / Glimmora / Kingambit / Rillaboom / Salamence / Volcarona: 4 copies, best 29, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/1013/teamlist
  - Basculegion / Glimmora / Indeedee / Kommo-o / Typhlosion-Hisui / Whimsicott: 3 copies, best 287, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0589/teamlist

#### 14. Tailwind Mega Dragonite
- Key: `(none)|tailwind|Dragonite-Mega`
- Requires: Pokémon Mega Dragonite; modes Tailwind; Megas Mega Dragonite
- 60 teams (2.0%), weighted 2.1%, 31 rosters, 15 in no other definition; recent 2.9% of 1484 teams vs. earlier 1.2% of 1473; 78.3% of its teams new at admission (in no related definition ranked above it)
- Origin: mode-mega seed `(none)|tailwind|Dragonite-Mega`
- Overlaps: Setup Mega Floette + Rillaboom + Incineroar (23 teams); Psyspam Indeedee-F (6 teams); Rain Tailwind Archaludon + Pelipper (6 teams)
- Variants: none
- Sample rosters:
  - Dragonite / Floette-Eternal / Gholdengo / Incineroar / Rillaboom / Sneasler: 23 copies, best 1, first 2026-09-20, https://pokepast.es/c06c65c34f56a094
  - Arcanine-Hisui / Dragonite / Gardevoir / Gholdengo / Indeedee-F / Rillaboom: 3 copies, best 63, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0683/teamlist
  - Basculegion / Dragonite / Incineroar / Indeedee / Sneasler / Sylveon: 2 copies, best 1, first 2026-09-13, https://pokepast.es/d4ace2bc38fdbd71
  - Archaludon / Dragonite / Golisopod / Indeedee / Pelipper / Sneasler: 2 copies, best 26, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0681/teamlist
  - Dragonite / Gholdengo / Incineroar / Raichu / Rillaboom / Sneasler: 2 copies, best 258, first 2026-09-20, https://standings.limitlessvgc.com/0039/player/0167/teamlist

#### 15. Setup Mega Delphox + Sneasler
- Key: `Sneasler|setup|Delphox-Mega`
- Requires: Pokémon Sneasler, Mega Delphox; modes Setup; Megas Mega Delphox
- 61 teams (2.1%), weighted 2.1%, 22 rosters, 47 in no other definition; recent 3.0% of 1484 teams vs. earlier 1.2% of 1473; 85.2% of its teams new at admission (in no related definition ranked above it)
- Origin: mode-mega seed `(none)|setup|Delphox-Mega`, then Sneasler (77.2%, lift 1.80)
- Overlaps: Setup Mega Floette + Rillaboom + Incineroar (9 teams); Trick Room (2 teams); Tailwind Mega Salamence + Rillaboom (1 teams)
- Variants: none
- Sample rosters:
  - Delphox / Floette-Eternal / Incineroar / Kingambit / Sinistcha / Sneasler: 21 copies, best 10, first 2026-09-20, https://standings.limitlessvgc.com/0038/player/0118/teamlist
  - Delphox / Floette-Eternal / Incineroar / Kingambit / Rillaboom / Sneasler: 8 copies, best 8, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/rD6PrJBrCfzicynLnRAI
  - Blastoise / Delphox / Indeedee-F / Maushold / Sinistcha / Sneasler: 7 copies, best 73, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0078/teamlist
  - Blastoise / Delphox / Incineroar / Maushold / Sinistcha / Sneasler: 4 copies, best 17, first 2026-09-27, https://standings.limitlessvgc.com/0038/player/0288/teamlist
  - Delphox / Floette-Eternal / Incineroar / Maushold / Sinistcha / Sneasler: 2 copies, best 130, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0339/teamlist

#### 16. Tailwind Garchomp + Whimsicott
- Key: `Garchomp+Whimsicott|tailwind|(none)`
- Requires: Pokémon Garchomp, Whimsicott; modes Tailwind; Megas none
- 56 teams (1.9%), weighted 1.9%, 37 rosters, 6 in no other definition; recent 2.0% of 1484 teams vs. earlier 1.8% of 1473; 55.4% of its teams new at admission (in no related definition ranked above it)
- Origin: pair seed `Garchomp+Whimsicott|(none)|(none)`, then Tailwind (100.0%, lift 1.78)
- Overlaps: Sun Mega Charizard-Y (37 teams); Tailwind Basculegion + Whimsicott (20 teams); Psyspam Indeedee-F (6 teams)
- Variants: none
- Sample rosters:
  - Basculegion / Charizard / Floette-Eternal / Garchomp / Kingambit / Whimsicott: 8 copies, best 8, first 2026-09-20, https://pokepast.es/ffe1c04c186b2453
  - Charizard / Farigiraf / Garchomp / Kingambit / Sylveon / Whimsicott: 3 copies, best 18, first 2026-09-27, https://standings.limitlessvgc.com/0038/player/0049/teamlist
  - Basculegion / Charizard / Floette-Eternal / Garchomp / Incineroar / Whimsicott: 3 copies, best 209, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0653/teamlist
  - Charizard / Floette-Eternal / Garchomp / Gholdengo / Incineroar / Whimsicott: 3 copies, best 364, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0876/teamlist
  - Charizard / Garchomp / Gholdengo / Incineroar / Pawmot / Whimsicott: 2 copies, best 27, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0682/teamlist

#### 17. Tailwind Kingambit + Farigiraf + Sylveon
- Key: `Farigiraf+Kingambit+Sylveon|tailwind|(none)`
- Requires: Pokémon Farigiraf, Kingambit, Sylveon; modes Tailwind; Megas none
- 54 teams (1.8%), weighted 1.8%, 9 rosters, 5 in no other definition; recent 1.8% of 1484 teams vs. earlier 1.8% of 1473; 77.8% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Farigiraf+Kingambit+Sylveon|(none)|(none)`, then Tailwind (85.7%, lift 1.53)
- Overlaps: Sun Mega Charizard-Y (40 teams); Tailwind Mega Salamence + Rillaboom (9 teams); Trick Room (8 teams)
- Variants: none
- Sample rosters:
  - Aerodactyl / Charizard / Farigiraf / Garchomp / Kingambit / Sylveon: 35 copies, best 16, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/0lbS1fGaeHdeq74HLZWq
  - Arcanine-Hisui / Farigiraf / Kingambit / Rillaboom / Salamence / Sylveon: 8 copies, best 116, first 2026-09-17, https://standings.limitlessvgc.com/0037/player/0640/teamlist
  - Charizard / Farigiraf / Garchomp / Kingambit / Sylveon / Whimsicott: 3 copies, best 18, first 2026-09-27, https://standings.limitlessvgc.com/0038/player/0049/teamlist
  - Arcanine-Hisui / Farigiraf / Kingambit / Raichu / Staraptor / Sylveon: 3 copies, best 175, first 2026-09-20, https://standings.limitlessvgc.com/0039/player/0350/teamlist
  - Aerodactyl / Charizard / Farigiraf / Kingambit / Rillaboom / Sylveon: 1 copy, best 143, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0765/teamlist

#### 18. Mega Raichu-Y + Rillaboom + Volcarona
- Key: `Rillaboom+Volcarona|(none)|Raichu-Mega-Y`
- Requires: Pokémon Rillaboom, Volcarona, Mega Raichu-Y; modes none; Megas Mega Raichu-Y
- 53 teams (1.8%), weighted 1.8%, 31 rosters, 23 in no other definition; recent 2.9% of 1484 teams vs. earlier 0.7% of 1473; 49.1% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Raichu+Rillaboom+Volcarona|(none)|(none)`, then Mega Raichu-Y (100.0%, lift 4.24)
- Overlaps: Tailwind Mega Raichu-Y + Rillaboom + Gholdengo (13 teams); Mega Garchomp-Z + Rillaboom + Incineroar (13 teams); Tailwind Mega Salamence + Rillaboom (6 teams)
- Variants: none
- Sample rosters:
  - Garchomp / Gholdengo / Incineroar / Raichu / Rillaboom / Volcarona: 9 copies, best 1, first 2026-09-27, https://pokepast.es/0e35fd9cd52d7552
  - Floette-Eternal / Gholdengo / Incineroar / Raichu / Rillaboom / Volcarona: 4 copies, best 10, first 2026-09-20, https://pokepast.es/f1837233cdf5dcda
  - Basculegion / Garchomp / Raichu / Rillaboom / Sylveon / Volcarona: 4 copies, best 45, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0930/teamlist
  - Basculegion / Garchomp / Incineroar / Raichu / Rillaboom / Volcarona: 4 copies, best 48, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/0676/teamlist
  - Basculegion / Floette-Eternal / Incineroar / Raichu / Rillaboom / Volcarona: 3 copies, best 22, first 2026-09-20, https://standings.limitlessvgc.com/0039/player/0733/teamlist

#### 19. Rain Mega Golisopod + Farigiraf + Pelipper
- Key: `Farigiraf+Pelipper|rain|Golisopod-Mega`
- Requires: Pokémon Farigiraf, Pelipper, Mega Golisopod; modes Rain; Megas Mega Golisopod
- 52 teams (1.8%), weighted 1.8%, 31 rosters, 9 in no other definition; recent 2.0% of 1484 teams vs. earlier 1.6% of 1473; 36.5% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Farigiraf+Golisopod+Pelipper|(none)|(none)`, then Mega Golisopod (100.0%, lift 7.32), Rain (100.0%, lift 5.79)
- Overlaps: Rain Tailwind Archaludon + Pelipper (33 teams); Trick Room (21 teams); Rain Tailwind Mega Golisopod + Basculegion + Pelipper (7 teams)
- Variants: none
- Sample rosters:
  - Archaludon / Farigiraf / Golisopod / Grimmsnarl / Pelipper / Swampert: 6 copies, best 136, first 2026-09-20, https://standings.limitlessvgc.com/0039/player/0399/teamlist
  - Archaludon / Basculegion / Farigiraf / Garchomp / Golisopod / Pelipper: 6 copies, best 156, first 2026-09-20, https://standings.limitlessvgc.com/0039/player/0750/teamlist
  - Archaludon / Charizard / Farigiraf / Golisopod / Grimmsnarl / Pelipper: 3 copies, best 72, first 2026-09-20, https://standings.limitlessvgc.com/0039/player/1072/teamlist
  - Archaludon / Farigiraf / Golisopod / Pelipper / Sableye / Swampert: 3 copies, best 290, first 2026-09-27, https://standings.limitlessvgc.com/0038/player/0148/teamlist
  - Archaludon / Farigiraf / Golisopod / Incineroar / Pelipper / Rillaboom: 3 copies, best 357, first 2026-09-27, https://standings.limitlessvgc.com/0039/player/1109/teamlist

#### 20. Psyspam Sneasler + Milotic + Indeedee
- Key: `Indeedee+Milotic+Sneasler|psyspam|(none)`
- Requires: Pokémon Indeedee, Milotic, Sneasler; modes Psyspam; Megas none
- 51 teams (1.7%), weighted 1.7%, 13 rosters, 34 in no other definition; recent 1.2% of 1484 teams vs. earlier 2.2% of 1473; 100.0% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Indeedee+Milotic+Sneasler|(none)|(none)`, then Psyspam (100.0%, lift 4.69)
- Overlaps: Sand Mega Salamence + Mega Tyranitar + Excadrill (16 teams); Sun Mega Charizard-Y (1 teams)
- Variants: none
- Sample rosters:
  - Arcanine-Hisui / Indeedee / Metagross / Milotic / Salamence / Sneasler: 20 copies, best 4, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/VW1m8TfHx0RIVs4aGHGl
  - Excadrill / Indeedee / Milotic / Salamence / Sneasler / Tyranitar: 16 copies, best 157, first 2026-09-10, https://standings.limitlessvgc.com/0037/player/0196/teamlist
  - Gholdengo / Indeedee / Milotic / Salamence / Sneasler / Tyranitar: 3 copies, best 289, first 2026-09-20, https://standings.limitlessvgc.com/0038/player/0279/teamlist
  - Excadrill / Froslass / Indeedee / Milotic / Sneasler / Tyranitar: 2 copies, best 262, first 2026-09-27, https://standings.limitlessvgc.com/0038/player/0100/teamlist
  - Arcanine-Hisui / Garchomp / Indeedee / Metagross / Milotic / Sneasler: 2 copies, best 833, first 2026-09-15, https://standings.limitlessvgc.com/0037/player/0013/teamlist

#### 21. Mega Metagross + Indeedee-F
- Key: `Indeedee-F|(none)|Metagross-Mega`
- Requires: Pokémon Indeedee-F, Mega Metagross; modes none; Megas Mega Metagross
- 50 teams (1.7%), weighted 1.7%, 33 rosters, 26 in no other definition; recent 1.1% of 1484 teams vs. earlier 2.3% of 1473; 70.0% of its teams new at admission (in no related definition ranked above it)
- Origin: pair seed `Indeedee-F+Metagross|(none)|(none)`, then Mega Metagross (98.0%, lift 18.58)
- Overlaps: Psyspam Indeedee-F (15 teams); Trick Room (3 teams); Sun Mega Charizard-Y (2 teams)
- Variants: none
- Sample rosters:
  - Altaria / Arcanine-Hisui / Dragapult / Indeedee-F / Metagross / Milotic: 11 copies, best 16, first 2026-09-20, https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/tp1xMws30GePDkvo69zC
  - Armarouge / Dragapult / Indeedee-F / Metagross / Milotic / Staraptor: 4 copies, best 186, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0903/teamlist
  - Arcanine-Hisui / Dragapult / Indeedee-F / Metagross / Milotic / Raichu: 2 copies, best 210, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0454/teamlist
  - Blastoise / Indeedee-F / Maushold / Metagross / Talonflame / Tyranitar: 2 copies, best 665, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0762/teamlist
  - Arcanine-Hisui / Indeedee-F / Metagross / Milotic / Salamence / Sneasler: 2 copies, best 809, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0103/teamlist

#### 22. Mega Salamence + Mega Floette + Kingambit
- Key: `Kingambit|(none)|Floette-Mega+Salamence-Mega`
- Requires: Pokémon Kingambit, Mega Floette, Mega Salamence; modes none; Megas Mega Floette, Mega Salamence
- 50 teams (1.7%), weighted 1.7%, 24 rosters, 10 in no other definition; recent 0.9% of 1484 teams vs. earlier 2.4% of 1473; 32.0% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Floette-Eternal+Kingambit+Salamence|(none)|(none)`, then Mega Floette (100.0%, lift 7.89), Mega Salamence (100.0%, lift 3.15)
- Overlaps: Tailwind Mega Salamence + Rillaboom (32 teams); Setup Mega Floette + Rillaboom + Incineroar (11 teams); Tailwind Basculegion + Whimsicott (3 teams)
- Variants: none
- Sample rosters:
  - Floette-Eternal / Incineroar / Kingambit / Rillaboom / Salamence / Sneasler: 15 copies, best 277, first 2026-09-09, https://standings.limitlessvgc.com/0038/player/0009/teamlist
  - Basculegion / Floette-Eternal / Kingambit / Rillaboom / Salamence / Sneasler: 8 copies, best 2, first 2026-09-09, https://standings.limitlessvgc.com/0038/player/0141/teamlist
  - Basculegion / Floette-Eternal / Incineroar / Kingambit / Rillaboom / Salamence: 3 copies, best 483, first 2026-09-09, https://standings.limitlessvgc.com/0037/player/0635/teamlist
  - Arcanine-Hisui / Floette-Eternal / Kingambit / Rillaboom / Salamence / Sneasler: 2 copies, best 293, first 2026-09-27, https://standings.limitlessvgc.com/0038/player/0054/teamlist
  - Basculegion / Floette-Eternal / Garchomp / Kingambit / Salamence / Sneasler: 2 copies, best 368, first 2026-09-09, https://standings.limitlessvgc.com/0039/player/0883/teamlist

#### 23. Mega Lucario-Z + Rillaboom + Incineroar
- Key: `Incineroar+Rillaboom|(none)|Lucario-Mega-Z`
- Requires: Pokémon Incineroar, Rillaboom, Mega Lucario-Z; modes none; Megas Mega Lucario-Z
- 50 teams (1.7%), weighted 1.7%, 30 rosters, 15 in no other definition; recent 1.0% of 1484 teams vs. earlier 2.4% of 1473; 40.0% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Incineroar+Lucario+Rillaboom|(none)|(none)`, then Mega Lucario-Z (100.0%, lift 35.63)
- Overlaps: Tailwind Mega Salamence + Rillaboom (15 teams); Mega Garchomp-Z + Rillaboom + Incineroar (12 teams); Trick Room (3 teams)
- Variants: none
- Sample rosters:
  - Basculegion / Garchomp / Incineroar / Lucario / Rillaboom / Sneasler: 10 copies, best 28, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0029/teamlist
  - Basculegion / Incineroar / Lucario / Rillaboom / Salamence / Sylveon: 4 copies, best 1031, first 2026-09-10, https://standings.limitlessvgc.com/0037/player/0552/teamlist
  - Farigiraf / Incineroar / Lucario / Primarina / Rillaboom / Salamence: 3 copies, best 540, first 2026-09-11, https://standings.limitlessvgc.com/0039/player/0607/teamlist
  - Archaludon / Gengar / Incineroar / Lucario / Politoed / Rillaboom: 3 copies, best 1002, first 2026-09-19, https://standings.limitlessvgc.com/0039/player/0511/teamlist
  - Aerodactyl / Basculegion / Floette-Eternal / Incineroar / Lucario / Rillaboom: 2 copies, best 232, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0166/teamlist

#### 24. Rain Tailwind Mega Golisopod + Basculegion + Pelipper
- Key: `Basculegion+Pelipper|rain+tailwind|Golisopod-Mega`
- Requires: Pokémon Basculegion, Pelipper, Mega Golisopod; modes Rain, Tailwind; Megas Mega Golisopod
- 49 teams (1.7%), weighted 1.7%, 19 rosters, 2 in no other definition; recent 0.9% of 1484 teams vs. earlier 2.4% of 1473; 36.7% of its teams new at admission (in no related definition ranked above it)
- Origin: triple seed `Basculegion+Golisopod+Pelipper|(none)|(none)`, then Mega Golisopod (100.0%, lift 7.32), Rain (100.0%, lift 5.79), Tailwind (89.1%, lift 1.59)
- Overlaps: Rain Tailwind Archaludon + Pelipper (28 teams); Psyspam Indeedee-F (17 teams); Tailwind Mega Salamence + Rillaboom (14 teams)
- Variants: none
- Sample rosters:
  - Basculegion / Gardevoir / Golisopod / Indeedee-F / Pelipper / Sneasler: 14 copies, best 6, first 2026-09-12, https://pokepast.es/1f4da9800a6ad851
  - Archaludon / Basculegion / Golisopod / Pelipper / Rillaboom / Salamence: 12 copies, best 21, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0950/teamlist
  - Archaludon / Basculegion / Farigiraf / Garchomp / Golisopod / Pelipper: 4 copies, best 246, first 2026-09-20, https://standings.limitlessvgc.com/0039/player/1023/teamlist
  - Archaludon / Basculegion / Golisopod / Pelipper / Salamence / Sneasler: 2 copies, best 267, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0637/teamlist
  - Basculegion / Golisopod / Indeedee-F / Pelipper / Salamence / Sneasler: 2 copies, best 456, first 2026-09-20, https://standings.limitlessvgc.com/0037/player/0987/teamlist

### 9.3 Refused candidates
Refused: 9 with fewer than definitionMinRosters distinct rosters (rosters), 1 on at least definitionMaxCoverage of the window (ceiling), 11 of Pokémon alone below definitionBareMinLift (staple), 116 with less than definitionMinOwnShare of their teams new against related definitions and refining no definition (overlap), 35 refining a definition that already has definitionVariants variants (variant-cap), 0 past definitionMax definitions (cap), 3 admitted but with less than definitionMinOwnShare of their teams outside the other related definitions once the set was complete (absorbed). The first by rank:
| Name | Key | Teams | Share | Weighted | Rosters | New | Reason |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Tailwind | `(none)\|tailwind\|(none)` | 1659 | 56.1% | 56.1% | 801 | — | ceiling |
| Rillaboom + Incineroar | `Incineroar+Rillaboom\|(none)\|(none)` | 614 | 20.8% | 20.9% | 281 | — | staple (lift 1.27) |
| Sneasler + Kingambit | `Kingambit+Sneasler\|(none)\|(none)` | 421 | 14.2% | 14.4% | 181 | — | staple (lift 1.33) |
| Tailwind Rillaboom + Gholdengo + Arcanine-Hisui | `Arcanine-Hisui+Gholdengo+Rillaboom\|tailwind\|(none)` | 312 | 10.6% | 10.5% | 41 | 1.6% | overlap (would rank 6; shares teams with Tailwind Mega Raichu-Y + Rillaboom + Gholdengo 269, Tailwind Mega Salamence + Rillaboom 150) |
| Tailwind Mega Raichu-Y + Rillaboom + Arcanine-Hisui | `Arcanine-Hisui+Rillaboom\|tailwind\|Raichu-Mega-Y` | 292 | 9.9% | 9.9% | 37 | 1.0% | overlap (would rank 6; shares teams with Tailwind Mega Raichu-Y + Rillaboom + Gholdengo 269, Tailwind Mega Salamence + Rillaboom 132) |
| Tailwind Mega Raichu-Y + Gholdengo + Arcanine-Hisui | `Arcanine-Hisui+Gholdengo\|tailwind\|Raichu-Mega-Y` | 271 | 9.2% | 9.1% | 29 | 0.7% | overlap (would rank 6; shares teams with Tailwind Mega Raichu-Y + Rillaboom + Gholdengo 269, Tailwind Mega Salamence + Rillaboom 112) |
| Tailwind Mega Staraptor + Rillaboom + Gholdengo | `Gholdengo+Rillaboom\|tailwind\|Staraptor-Mega` | 222 | 7.5% | 7.5% | 29 | 3.6% | overlap (would rank 7; shares teams with Tailwind Mega Raichu-Y + Rillaboom + Gholdengo 214, Rain Tailwind Archaludon + Pelipper 1) |
| Tailwind Mega Raichu-Y + Mega Staraptor + Rillaboom | `Rillaboom\|tailwind\|Raichu-Mega-Y+Staraptor-Mega` | 219 | 7.4% | 7.4% | 28 | 2.3% | overlap (would rank 7; shares teams with Tailwind Mega Raichu-Y + Rillaboom + Gholdengo 214, Rain Tailwind Archaludon + Pelipper 1) |
| Tailwind Mega Raichu-Y + Mega Staraptor + Gholdengo | `Gholdengo\|tailwind\|Raichu-Mega-Y+Staraptor-Mega` | 218 | 7.4% | 7.4% | 27 | 1.8% | overlap (would rank 7; shares teams with Tailwind Mega Raichu-Y + Rillaboom + Gholdengo 214, Rain Tailwind Archaludon + Pelipper 1) |
| Tailwind Setup Rillaboom + Gholdengo | `Gholdengo+Rillaboom\|tailwind+setup\|(none)` | 204 | 6.9% | 7.0% | 49 | 19.1% | overlap (would rank 7; shares teams with Tailwind Mega Raichu-Y + Rillaboom + Gholdengo 108, Tailwind Mega Salamence + Rillaboom 84) |

Absorbed, by round (New: the share of its teams outside every other related definition, above or below it):
| Round | Name | Key | Teams | Share | Weighted | Rosters | New | Reason |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Indeedee-F + Milotic + Dragapult | `Dragapult+Indeedee-F+Milotic\|(none)\|(none)` | 52 | 1.8% | 1.8% | 14 | 5.8% | absorbed (by Psyspam Indeedee-F 35, Mega Metagross + Indeedee-F 19) |
| 2 | Mega Salamence + Arcanine-Hisui + Milotic | `Arcanine-Hisui+Milotic\|(none)\|Salamence-Mega` | 59 | 2.0% | 2.0% | 14 | 13.6% | absorbed (by Tailwind Mega Salamence + Rillaboom 29, Psyspam Sneasler + Milotic + Indeedee 21, Sand Mega Salamence + Mega Tyranitar + Excadrill 1) |
| 3 | Psyspam Mega Metagross | `(none)\|psyspam\|Metagross-Mega` | 51 | 1.7% | 1.7% | 28 | 19.6% | absorbed (by Psyspam Sneasler + Milotic + Indeedee 26, Psyspam Indeedee-F 15, Mega Metagross + Indeedee-F 15) |

### 9.4 Mode extension
Per mode tag, over the extension of every seed: how many seeds' extensions it reached definitionExtendShare of the teams of, how many it joined, and how many its lift (below coreMinLift) kept it out of.
| Mode | Reached | Added | Refused by lift |
| :--- | :--- | :--- | :--- |
| Sun | 64 | 45 | 0 |
| Rain | 76 | 75 | 0 |
| Sand | 47 | 47 | 0 |
| Snow | 13 | 13 | 0 |
| Psyspam | 61 | 53 | 0 |
| Trick Room | 24 | 8 | 0 |
| Tailwind | 162 | 132 | 12 |
| Perish Trap | 14 | 14 | 0 |
| Screens | 37 | 26 | 0 |
| Setup | 43 | 39 | 0 |

### 9.5 Uncovered teams
468 teams are in no definition. Their most common rosters:
| Roster | Copies | Best | First date | Sample |
| :--- | :--- | :--- | :--- | :--- |
| Basculegion / Froslass / Kingambit / Lycanroc-Dusk / Scovillain / Sneasler | 6 | 19 | 2026-09-16 | https://standings.limitlessvgc.com/0037/player/0059/teamlist |
| Archaludon / Gengar / Incineroar / Pelipper / Rillaboom / Swampert | 4 | 3 | 2026-09-14 | https://pokepast.es/a1b7a6d0af006124 |
| Basculegion / Froslass / Glimmora / Incineroar / Rillaboom / Volcarona | 4 | 7 | 2026-09-27 | https://standings.limitlessvgc.com/0039/player/0818/teamlist |
| Gengar / Incineroar / Kommo-o / Politoed / Rillaboom / Swampert | 4 | 73 | 2026-09-14 | https://standings.limitlessvgc.com/0037/player/0026/teamlist |
| Excadrill / Indeedee / Kingambit / Salamence / Sneasler / Tyranitar | 4 | 438 | 2026-09-11 | https://standings.limitlessvgc.com/0037/player/0008/teamlist |
| Gengar / Incineroar / Kommo-o / Politoed / Rillaboom / Vivillon | 3 | 5 | 2026-09-27 | https://standings.limitlessvgc.com/0039/player/0219/teamlist |
| Gengar / Incineroar / Kingambit / Kommo-o / Ninetales-Alola / Rillaboom | 3 | 12 | 2026-09-20 | https://pokepast.es/b987727e0339084a |
| Altaria / Gengar / Incineroar / Kingambit / Rillaboom / Sneasler | 3 | 20 | 2026-09-27 | https://standings.limitlessvgc.com/0039/player/0846/teamlist |
| Gengar / Hippowdon / Incineroar / Kingambit / Rillaboom / Sneasler | 3 | 45 | 2026-09-20 | https://standings.limitlessvgc.com/0037/player/0430/teamlist |
| Excadrill / Gholdengo / Milotic / Sinistcha / Staraptor / Tyranitar | 3 | 47 | 2026-09-27 | https://standings.limitlessvgc.com/0039/player/0266/teamlist |
Their most common species (share of the uncovered teams; lift over the window):
| Species | Teams | Share | Lift |
| :--- | :--- | :--- | :--- |
| Rillaboom | 259 | 55.3% | 1.01 |
| Sneasler | 191 | 40.8% | 0.95 |
| Incineroar | 174 | 37.2% | 1.24 |
| Kingambit | 126 | 26.9% | 1.08 |
| Milotic | 105 | 22.4% | 1.40 |
| Gholdengo | 102 | 21.8% | 0.76 |
| Raichu | 97 | 20.7% | 0.86 |
| Salamence | 89 | 19.0% | 0.60 |
| Garchomp | 82 | 17.5% | 1.13 |
| Basculegion | 81 | 17.3% | 1.05 |
