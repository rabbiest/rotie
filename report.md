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
- **Definition**: up to three Pokémon (a Mega counts as its Pokémon), mode tags and Megas, mined from team_query's records for the same as-of date and window; a team is in a definition exactly when it carries all of them, as team_query matches, every copy of a roster counting. Candidates start from species pairs found together more often than chance (lift at least `coreMinLift`), species triples whose lift over each of their pairs and its third member is at least `coreMinLift`, mode tags, and a mode tag with a Mega whose share among that tag's teams is at least `coreMinLift` times its share of the window; each candidate needs at least `definitionMinCoverage` of the window's teams (the floor). Each is then extended one element at a time by the mode tag, Mega or species on the most of its teams, when that element is on at least `definitionExtendShare` of them, its lift over the window is at least `coreMinLift`, and the extended candidate keeps the floor; no new Pokémon joins past three, and a Mega whose Pokémon is already required replaces it. Candidates are ranked by weighted share (the placement tier weights alone), then teams, distinct rosters, fewer elements, and key. One with fewer than `definitionMinRosters` distinct rosters is refused, as is one on at least `definitionMaxCoverage` of the window (its refinements stay eligible). One of Pokémon alone (no mode tag and no Mega) is refused as a staple unless its lift is at least `definitionBareMinLift` (a pair's lift; a triple's least lift over each of its pairs and its third member): Pokémon found together on many teams but not much more often than chance are not an archetype by themselves; the staples on at least `stapleMinShare` of the window's teams are listed, the rest counted. In rank order, a candidate with at least `definitionMinOwnShare` of its teams new (in no related definition admitted before it: one sharing a Pokémon, a Mega counting as its Pokémon, or a mode tag) is admitted, until there are `definitionMax`; any other becomes the variant of an admitted definition whose every Pokémon, mode tag and Mega it has (at most `definitionVariants` each), or is refused as an overlap, listing the related definitions sharing the most of its teams. Once the set is complete, of the definitions with less than `definitionMinOwnShare` of their teams outside every other related definition (above or below them), the one with the least (the lower-ranked on a tie) is refused as absorbed, listing the related definitions sharing the most of its teams, and admission is redone from the start without it, until a round absorbs none. A definition's name is its mode names, then its Megas and other Pokémon by window teams, joined by +. A definition's own teams are in no other definition; each team of the window is exclusive (in one definition), hybrid (in two or more) or uncovered (in none), a variant counting as its parent. Recent and earlier are its shares of the teams dated in the window's last `definitionRecentDays` days and of the rest. A definition that lists several Megas means a team runs one of them (one Mega per team); modes listed together are the team's options, not simultaneous conditions.
- **Ladder-calibrated view**: with `ladderBlend` above zero, the same teams are weighed a second way. Among the sheet's species nodes with a ladder entry, each takes as its rank-matched support the sheet support found at its own position in the ladder order among them, and every team's weight is multiplied once by the geometric mean over its species of (rank-matched support / sheet support) raised to `ladderBlend`; any other species counts as one. Species support and the community and sub-community shares are then shown under both weights, over the unchanged communities and assignments. It is a model built from the ladder's rank order, not a ladder usage share: it moves shares only part of the way, and not always toward the ladder's order, since each factor is averaged with its teammates'; it cannot add a build the sheet lacks; and a species thin or absent on the sheet can't be reweighted.
- **Tags that name nothing in this window** (on at least `labelDominantTagShare` of its teams): Tailwind.

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
- Unconnected species (no pair with at least minPairTeams teams and lift above communityMinLift, so in no community): Scizor, Heracross, Gliscor, Mimikyu, Overqwil, Drampa, Tinkaton, Ditto, Crabominable, Arcanine, Gyarados, Greninja, Bellibolt, Steelix, Ninetales, Torterra, Runerigus, Palafin, Cofagrigus, Azumarill, Noivern, Tauros-Paldea-Aqua, Dragalge, Aggron, Persian-Alola, Pidgeot, Krookodile, Feraligatr, Jolteon, Basculegion-F, Malamar, Clawitzer, Cinderace

### Community 0: Rillaboom / Raichu / Gholdengo
- Token label: Rillaboom / Raichu / Gholdengo
- Mode tags on primary teams: Tailwind 678, Setup 299, Snow 96, Trick Room 32, Sun 29, Screens 20, Psyspam 18, Rain 17, Sand 17, Perish Trap 13
- Megas on primary teams: Raichu-Y 636, Salamence 376, Staraptor 267, Floette 129, Froslass 69, Garchomp-Z 64, Lucario-Z 48, Golisopod 30, Metagross 28, Charizard-Y 26, Baxcalibur 20, Dragonite 20, Gengar 19, Glimmora 16, Absol-Z 14, Aerodactyl 14, Tyranitar 13, Delphox 10, Gardevoir 9, Blaziken 5, Camerupt 4, Altaria 3, Mawile 3, Raichu-X 3, Swampert 3, Charizard-X 2, Houndoom 2, Sceptile 2, Scrafty 2, Abomasnow 1, Aggron 1, Blastoise 1, Chandelure 1, Crabominable 1, Dragalge 1, Emboar 1, Gallade 1, Greninja 1, Manectric 1, Pidgeot 1, Pyroar 1, Scovillain 1, Starmie 1
- Primary teams: 984 (primary share 33.9%), hybrid teams: 419 (hybrid share 13.5%)
- Date range: 2026-09-09 to 2026-09-30
- Core pairs: Pawmot+Staraptor@Choice Scarf, Altaria+Milotic@Psychic Seed, Dragapult+Milotic@Psychic Seed, Vivillon+Rillaboom@Eject Button, Gengar+Rillaboom@Eject Button, Ceruledge+Milotic@Sitrus Berry, Armarouge+Milotic@Psychic Seed, Lycanroc-Dusk+Rillaboom@Life Orb, Politoed+Rillaboom@Eject Button, Altaria+Rillaboom@Eject Button, Politoed+Staraptor@Choice Scarf, Vivillon+Rillaboom@Occa Berry, Staraptor+Ceruledge@Grassy Seed, Sylveon+Aerodactyl@Aerodactylite, Primarina+Farigiraf@Grassy Seed, Sylveon+Aegislash@Focus Sash, Milotic+Ceruledge@Grassy Seed, Milotic+Altaria@Haban Berry, Excadrill+Rillaboom@Expert Belt, Ceruledge+Staraptor@Staraptite, Metagross+Milotic@Psychic Seed, Indeedee-F+Milotic@Psychic Seed, Ceruledge+Staraptor, Gengar+Milotic@Psychic Seed, Farigiraf+Staraptor@Choice Scarf, Aerodactyl+Sylveon@Fairy Feather, Ceruledge+Milotic, Tyranitar+Rillaboom@Expert Belt, Staraptor+Milotic@Psychic Seed, Aerodactyl+Sylveon, Primarina+Blaziken@Blazikenite, Kommo-o+Rillaboom@Eject Button, Sylveon+Venusaur@Life Orb, Torkoal+Primarina@Life Orb, Archaludon+Rillaboom@Eject Button, Aegislash+Sylveon@Fairy Feather, Staraptor+Armarouge@Twisted Spoon, Arcanine-Hisui+Altaria@Haban Berry, Excadrill+Milotic@Sitrus Berry, Aegislash+Sylveon, Milotic+Dragapult@Life Orb, Ninetales-Alola+Rillaboom@Occa Berry, Farigiraf+Primarina@Life Orb, Froslass+Rillaboom@Sitrus Berry, Dragapult+Milotic, Blaziken+Primarina, Tyranitar+Milotic@Sitrus Berry, Milotic+Excadrill@Life Orb, Altaria+Milotic, Staraptor+Armarouge@Focus Sash, Milotic+Armarouge@Twisted Spoon, Staraptor+Dragapult@Life Orb, Primarina+Farigiraf@Colbur Berry, Sylveon+Kingambit@Focus Sash, Raichu+Ceruledge@Grassy Seed, Raichu+Primarina@Grassy Seed, Primarina+Pawmot@Focus Sash, Golisopod+Staraptor@Choice Scarf, Arcanine-Hisui+Baxcalibur@Life Orb, Ninetales-Alola+Rillaboom@Life Orb, Milotic+Ceruledge@Colbur Berry, Sylveon+Toxapex@Leftovers, Dragapult+Staraptor@Staraptite, Arcanine-Hisui+Gholdengo@Grassy Seed, Dragapult+Staraptor, Incineroar+Rillaboom@Eject Button, Ceruledge+Raichu@Raichunite Y, Ceruledge+Raichu, Sylveon+Garchomp@Life Orb, Staraptor+Sylveon@Fairy Feather, Toxapex+Sylveon@Fairy Feather, Sylveon+Staraptor@Staraptite, Staraptor+Sylveon, Floette-Eternal+Rillaboom@Occa Berry, Archaludon+Raichu@Raichunite X, Sylveon+Toxapex, Milotic+Rillaboom@Expert Belt, Altaria+Arcanine-Hisui, Staraptor+Milotic@Sitrus Berry, Pawmot+Primarina, Ceruledge+Gholdengo@Life Orb, Raichu+Gholdengo@Grassy Seed, Altaria+Arcanine-Hisui@Focus Sash, Gholdengo+Ceruledge@Grassy Seed, Annihilape+Rillaboom@Life Orb, Kingambit+Sylveon@Life Orb, Baxcalibur+Milotic@Leftovers, Gengar+Rillaboom@Occa Berry, Salamence+Rillaboom@Expert Belt, Toxtricity+Sylveon@Fairy Feather, Pelipper+Rillaboom@Expert Belt, Arcanine-Hisui+Rillaboom@Sitrus Berry, Sylveon+Toxtricity, Arcanine-Hisui+Primarina@Grassy Seed, Raichu+Staraptor@Staraptite, Staraptor+Raichu@Raichunite Y, Staraptor+Gholdengo@Life Orb, Metagross+Milotic, Milotic+Excadrill@Focus Sash, Raichu+Staraptor, Milotic+Metagross@Metagrossite, Kommo-o+Rillaboom@Occa Berry, Camerupt+Rillaboom@Life Orb, Raichu+Ceruledge@Colbur Berry, Milotic+Armarouge@Focus Sash, Ceruledge+Gholdengo, Excadrill+Milotic, Sylveon+Garchomp@Choice Scarf, Milotic+Tyranitar@Tyranitarite, Milotic+Weavile, Sylveon+Arcanine-Hisui@Focus Sash, Pelipper+Raichu@Raichunite X, Primarina+Torkoal@Charcoal, Raichu+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Sylveon@Fairy Feather, Arcanine-Hisui+Raichu@Raichunite Y, Golisopod+Rillaboom@Leftovers, Salamence+Ceruledge@Colbur Berry, Arcanine-Hisui+Sylveon, Arcanine-Hisui+Raichu, Blaziken+Rillaboom@Life Orb, Gholdengo+Staraptor@Staraptite, Ceruledge+Milotic@Leftovers, Armarouge+Staraptor, Armarouge+Staraptor@Staraptite, Gholdengo+Staraptor, Farigiraf+Primarina, Milotic+Tyranitar, Sylveon+Aerodactyl@Focus Sash, Hydreigon+Milotic@Sitrus Berry, Raichu+Gholdengo@Life Orb, Salamence+Gholdengo@Grassy Seed, Salamence+Sylveon@Life Orb, Primarina+Lucario@Lucarionite Z, Froslass+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Froslass, Arcanine-Hisui+Froslass@Froslassite, Gholdengo+Raichu@Raichunite Y, Primarina+Torkoal, Lucario+Rillaboom@Occa Berry, Lucario+Primarina, Sylveon+Farigiraf@Sitrus Berry, Arcanine-Hisui+Staraptor@Staraptite, Raichu+Rillaboom@Kebia Berry, Gholdengo+Raichu, Floette-Eternal+Gholdengo@Leftovers, Staraptor+Arcanine-Hisui@Focus Sash, Milotic+Staraptor@Staraptite, Excadrill+Milotic@Leftovers, Arcanine-Hisui+Staraptor, Gholdengo+Milotic@Sitrus Berry, Raichu+Vanilluxe@Choice Scarf, Milotic+Staraptor, Metagross+Milotic@Leftovers, Metagross+Milotic@Sitrus Berry, Froslass+Rillaboom@Eject Button, Sneasler+Arcanine-Hisui@Life Orb, Basculegion+Rillaboom@Expert Belt, Delphox+Rillaboom@Occa Berry, Raichu+Sylveon@Fairy Feather, Politoed+Rillaboom@Occa Berry, Sylveon+Raichu@Raichunite Y, Ceruledge+Rillaboom@Miracle Seed, Tyranitar+Milotic@Leftovers, Lucario+Rillaboom@Life Orb, Raichu+Sylveon, Gholdengo+Primarina@Grassy Seed, Pelipper+Gholdengo@Choice Scarf, Gholdengo+Milotic@Grassy Seed, Milotic+Indeedee@Choice Scarf, Incineroar+Rillaboom@Occa Berry, Gholdengo+Arcanine-Hisui@Focus Sash, Indeedee+Milotic@Leftovers, Arcanine-Hisui+Gholdengo, Arcanine-Hisui+Gholdengo@Leftovers, Arcanine-Hisui+Gholdengo@Life Orb, Froslass+Rillaboom@Life Orb, Swampert+Rillaboom@Eject Button, Kingambit+Rillaboom@Sitrus Berry, Sylveon+Charizard@Charizardite Y, Farigiraf+Primarina@Leftovers, Goodra-Hisui+Rillaboom@Miracle Seed, Indeedee+Milotic@Sitrus Berry, Primarina+Volcarona@Grassy Seed, Charizard+Sylveon@Fairy Feather, Staraptor+Garchomp@Sitrus Berry, Charizard+Sylveon, Kingambit+Rillaboom@Kebia Berry, Milotic+Hydreigon@Choice Scarf, Armarouge+Milotic, Floette-Eternal+Gholdengo@Choice Scarf, Gholdengo+Garchomp@Sitrus Berry, Salamence+Rillaboom@Sitrus Berry, Farigiraf+Sylveon@Fairy Feather, Raichu+Talonflame@Focus Sash, Gholdengo+Rillaboom@Kebia Berry, Raichu+Rillaboom@Miracle Seed, Golisopod+Rillaboom@Expert Belt, Rillaboom+Gholdengo@Grassy Seed, Hippowdon+Rillaboom, Rillaboom+Sneasler@Grassy Seed, Rillaboom+Farigiraf@Grassy Seed, Rillaboom+Ceruledge@Grassy Seed, Rillaboom+Milotic@Grassy Seed, Rillaboom+Volcarona@Grassy Seed, Rillaboom+Hippowdon@Leftovers, Rillaboom+Primarina@Grassy Seed, Rillaboom+Incineroar@Lum Berry, Rillaboom+Swampert@Sitrus Berry, Rillaboom+Espathra@Grassy Seed, Rillaboom+Empoleon@Leftovers, Rillaboom+Altaria@Altarianite, Gholdengo+Rillaboom@Miracle Seed, Sneasler+Gholdengo@Focus Sash, Arcanine-Hisui+Kingambit@Life Orb, Farigiraf+Sylveon, Arcanine-Hisui+Sneasler@Grassy Seed, Vanilluxe+Raichu@Raichunite Y, Staraptor+Hydreigon@Choice Scarf, Incineroar+Rillaboom@Leftovers, Hippowdon+Rillaboom@Miracle Seed, Raichu+Vanilluxe, Ninetales-Alola+Rillaboom@Sitrus Berry, Staraptor+Glimmora@Focus Sash, Salamence+Rillaboom@Kebia Berry, Primarina+Kingambit@Focus Sash, Gholdengo+Primarina@Mystic Water, Sylveon+Gholdengo@Life Orb, Metagross+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Rillaboom@Kebia Berry, Golisopod+Milotic@Psychic Seed, Dragonite+Rillaboom@Life Orb, Absol+Milotic@Leftovers, Staraptor+Whimsicott@Occa Berry, Arcanine-Hisui+Metagross@Metagrossite, Kingambit+Ceruledge@Colbur Berry, Indeedee+Milotic, Archaludon+Rillaboom@Expert Belt, Kommo-o+Gholdengo@Grassy Seed, Milotic+Gholdengo@Life Orb, Ceruledge+Rillaboom, Arcanine-Hisui+Metagross, Gholdengo+Sylveon@Fairy Feather, Arcanine-Hisui+Rillaboom@Miracle Seed, Volcarona+Rillaboom@Miracle Seed, Gholdengo+Dragonite@Dragoninite, Milotic+Baxcalibur@Baxcalibrite, Staraptor+Rillaboom@Miracle Seed, Staraptor+Indeedee-F@Rocky Helmet, Rillaboom+Raichu@Raichunite Y, Rillaboom+Scrafty, Gholdengo+Sylveon, Lucario+Rillaboom@Miracle Seed, Primarina+Raichu@Raichunite Y, Milotic+Indeedee-F@Rocky Helmet, Arcanine-Hisui+Milotic@Grassy Seed, Charizard+Rillaboom@Grassy Seed, Gholdengo+Ceruledge@Colbur Berry, Raichu+Rillaboom, Garchomp+Sylveon@Fairy Feather, Raichu+Milotic@Sitrus Berry, Rillaboom+Vivillon@Focus Sash, Primarina+Raichu, Raichu+Volcarona@Grassy Seed, Garchomp+Sylveon, Raichu+Kingambit@Life Orb, Rillaboom+Politoed@Sitrus Berry, Gholdengo+Sneasler@Grassy Seed, Rillaboom+Gholdengo@Life Orb, Sneasler+Rillaboom@Kebia Berry, Pelipper+Rillaboom@Life Orb, Raichu+Rillaboom@Sitrus Berry, Rillaboom+Vivillon, Rillaboom+Arcanine-Hisui@Focus Sash, Salamence+Milotic@Sitrus Berry, Rillaboom+Goodra-Hisui@Leftovers, Raichu+Blaziken@Focus Sash, Gholdengo+Rillaboom, Raichu+Primarina@Leftovers, Arcanine-Hisui+Rillaboom, Staraptor+Gholdengo@Choice Scarf, Salamence+Arcanine-Hisui@Focus Sash, Rillaboom+Lucario@Lucarionite Z, Archaludon+Gholdengo@Choice Scarf, Gholdengo+Milotic, Arcanine-Hisui+Salamence@Salamencite, Primarina+Incineroar@Sitrus Berry, Arcanine-Hisui+Salamence, Rillaboom+Volcarona@Rocky Helmet, Lucario+Rillaboom, Metagross+Rillaboom@Life Orb, Manectric+Rillaboom, Rillaboom+Manectric@Manectite, Sneasler+Rillaboom@Sitrus Berry, Primarina+Staraptor@Staraptite, Baxcalibur+Rillaboom@Sitrus Berry, Dragonite+Gholdengo@Life Orb, Dragonite+Gholdengo, Raichu+Milotic@Grassy Seed, Indeedee-F+Toxtricity, Sylveon+Rillaboom@Miracle Seed, Floette-Eternal+Rillaboom@Sitrus Berry, Incineroar+Primarina@Grassy Seed, Blaziken+Milotic@Leftovers, Rillaboom+Volcarona, Gholdengo+Rillaboom@Expert Belt, Primarina+Staraptor, Baxcalibur+Milotic, Salamence+Primarina@Grassy Seed, Basculegion+Rillaboom@Sitrus Berry, Gholdengo+Milotic@Leftovers, Salamence+Milotic@Leftovers, Milotic+Indeedee-F@Sitrus Berry, Floette-Eternal+Rillaboom@Life Orb, Incineroar+Weavile, Whimsicott+Staraptor@Staraptite, Rillaboom+Pelipper@Choice Scarf, Hydreigon+Milotic, Raichu+Sneasler@Grassy Seed, Rillaboom+Incineroar@Rocky Helmet, Delphox+Rillaboom@Life Orb, Goodra-Hisui+Rillaboom, Froslass+Rillaboom, Rillaboom+Froslass@Froslassite, Sylveon+Gholdengo@Grassy Seed, Staraptor+Whimsicott, Rillaboom+Incineroar@Passho Berry, Gholdengo+Salamence, Gholdengo+Salamence@Salamencite, Arcanine-Hisui+Kingambit@Chople Berry, Rillaboom+Gengar@Gengarite, Incineroar+Primarina@Leftovers, Rillaboom+Primarina@Leftovers, Incineroar+Rillaboom@Life Orb, Rillaboom+Maushold@Focus Sash, Salamence+Primarina@Leftovers, Floette-Eternal+Gholdengo@Life Orb, Gengar+Rillaboom, Gholdengo+Incineroar@Rocky Helmet, Arcanine-Hisui+Hydreigon@Choice Scarf, Golisopod+Rillaboom@Grassy Seed, Sylveon+Venusaur, Gholdengo+Floette-Eternal@Floettite, Froslass+Raichu@Raichunite Y, Floette-Eternal+Gholdengo, Floette-Eternal+Rillaboom, Rillaboom+Floette-Eternal@Floettite, Hydreigon+Milotic@Leftovers, Raichu+Kleavor@Focus Sash, Salamence+Gholdengo@Life Orb, Floette-Eternal+Rillaboom@Miracle Seed, Froslass+Rillaboom@Occa Berry, Primarina+Farigiraf@Sitrus Berry, Primarina+Rillaboom@Miracle Seed, Froslass+Raichu, Raichu+Froslass@Froslassite, Volcarona+Rillaboom@Life Orb, Golisopod+Rillaboom@Life Orb, Salamence+Primarina@Life Orb, Rillaboom+Milotic@Sitrus Berry, Venusaur+Sylveon@Fairy Feather, Gholdengo+Tyranitar@Tyranitarite, Rillaboom+Staraptor@Staraptite, Sylveon+Basculegion@Life Orb, Gholdengo+Rillaboom@Occa Berry, Rillaboom+Incineroar@Sitrus Berry, Excadrill+Gholdengo@Life Orb, Tyranitar+Gholdengo@Life Orb, Rillaboom+Kingambit@Life Orb, Rillaboom+Staraptor, Arcanine-Hisui+Milotic@Leftovers, Clefable+Rillaboom, Staraptor+Whimsicott@Focus Sash, Salamence+Gholdengo@Leftovers, Milotic+Salamence@Salamencite, Milotic+Salamence, Rillaboom+Baxcalibur@Baxcalibrite, Rillaboom+Ninetales-Alola@Choice Scarf, Gholdengo+Excadrill@Focus Sash, Garchomp+Rillaboom@Grassy Seed, Golisopod+Primarina@Life Orb, Primarina+Salamence@Salamencite, Primarina+Salamence, Rillaboom+Incineroar@Chople Berry, Blaziken+Milotic, Rillaboom+Arcanine-Hisui@Life Orb, Rillaboom+Gholdengo@Leftovers, Rillaboom+Archaludon@Chople Berry, Salamence+Rillaboom@Miracle Seed, Salamence+Rillaboom@Leftovers, Primarina+Arcanine-Hisui@Focus Sash, Milotic+Blaziken@Blazikenite, Incineroar+Rillaboom, Raichu+Rillaboom@Life Orb, Hydreigon+Staraptor@Staraptite, Rillaboom+Salamence@Salamencite, Milotic+Rillaboom@Grassy Seed, Rillaboom+Salamence, Rillaboom+Sylveon@Fairy Feather, Arcanine-Hisui+Farigiraf@Grassy Seed, Raichu+Ninetales-Alola@Light Clay, Rillaboom+Sylveon, Tsareena+Gholdengo@Life Orb, Sneasler+Sylveon@Life Orb, Incineroar+Rillaboom@Grassy Seed, Arcanine-Hisui+Primarina, Gholdengo+Incineroar@Sitrus Berry, Hydreigon+Staraptor, Lucario+Sylveon@Fairy Feather, Rillaboom+Ninetales-Alola@Focus Sash, Primarina+Metagross@Metagrossite, Primarina+Rillaboom, Rillaboom+Garchomp@Garchompite Z, Gholdengo+Tyranitar, Raichu+Volcarona, Rillaboom+Sylveon@Life Orb, Rillaboom+Kommo-o@Leftovers, Froslass+Primarina, Primarina+Froslass@Froslassite, Milotic+Raichu@Raichunite Y, Excadrill+Gholdengo, Rillaboom+Milotic@Leftovers, Rillaboom+Basculegion@Life Orb, Rillaboom+Arcanine-Hisui@Choice Scarf, Rillaboom+Ninetales-Alola@Light Clay, Baxcalibur+Rillaboom, Rillaboom+Dragonite@Dragoninite, Rillaboom+Talonflame@Focus Sash, Espathra+Rillaboom, Ninetales-Alola+Rillaboom, Rillaboom+Dragonite@Life Orb, Mamoswine+Rillaboom, Rillaboom+Mamoswine@Focus Sash
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Rillaboom | 94.6% | terrain-setter 99.9%, priority-attack 99.2%, fake-out 99.2%, pivot 28.6%, speed-drop 2.0%, disruption 0.4% |
| Raichu | 67.8% | mega-attacker 99.9%, speed-drop 98.3%, fake-out 80.5%, disruption 16.6%, pivot 2.2%, terrain-setter 1.9%, screens 0.3%, setup 0.3% |
| Gholdengo | 67.2% | setup 94.4% |
| Arcanine-Hisui | 49.2% | priority-attack 91.1%, intimidate 1.6%, spa-drop 0.1% |
| Staraptor | 29.6% | intimidate 99.7%, mega-attacker 98.0%, tailwind 78.7%, pivot 2.9%, weather-setter 0.3%, priority-attack 0.3% |
| Sylveon | 22.7% | priority-attack 83.4%, status 10.1%, trick-room-abuser 6.5%, setup 5.9%, spa-drop 3.5%, helping-hand 0.9%, weather-setter 0.4% |
| Milotic | 21.9% | speed-drop 45.9%, setup 40.4%, status 39.0%, helping-hand 2.7%, weather-setter 0.5%, screens 0.3%, pivot 0.2% |
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
  - [Christian Walloschek, 866th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0543/teamlist)
  - [Declan Lomboy, 572nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0976/teamlist)
  - [Jan-Philipp Gnaß, 314th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1114/teamlist)
  - [Niek Fenijn, 409th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1110/teamlist)
  - [Amelius van Etten, 924th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0966/teamlist)
  - [Nate Timms, 192nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0828/teamlist)
  - [Kyle Morris, 332nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0278/teamlist)
  - [Sean Mondor, 482nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0747/teamlist)
  - [Constanza Castro, 1032nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0485/teamlist)
  - [Chris Iwaskiw, 561st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1056/teamlist)
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
  - [max_Zi_ma, , 12 Sep 2026](https://pokepast.es/73e5d6533b781089)
  - [Adam Naish, 134th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0035/teamlist)
  - [Jan-Luca Heinrich, 159th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0538/teamlist)
  - [Phönix Meschkat, 586th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0719/teamlist)
  - [Matteo DiRende, 473rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0258/teamlist)
  - [Alexander Hobson, 653rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0738/teamlist)
  - [punihina1334, Champion, 19 Sep 2026](https://pokepast.es/98d6e2fba3995368)
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
  - [Brian Kem, 91st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0004/teamlist)
  - [Nathan Rouby, 1036th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0862/teamlist)
  - [Jordan Schumann, 749th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0207/teamlist)
  - [James McKinley, 424th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0192/teamlist)
  - [Jarrod Rose, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0044/teamlist)
  - [Mats Schrader, 769th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0382/teamlist)
  - [Rocco Hauboldt, 969th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0623/teamlist)
  - [Kyle Brynteson, 154th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0134/teamlist)
  - [Anthony Glover, 363rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0311/teamlist)
  - [Hunter Wellens, 537th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0606/teamlist)
  - [Nathaniel Ledford, 713th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1017/teamlist)
  - [Rielly Chambers, 66th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0092/teamlist)
  - [Ville-Veikko Vähäaho, 781st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0841/teamlist)
  - [Leonard Laatz, 995th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1028/teamlist)
  - [Rodney van den Velden, 1119th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0321/teamlist)
  - [Zach Franks, 829th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0404/teamlist)
  - [Ryan Loseto, Champion, 10 Sep 2026](https://pokepast.es/2c92e63c0fd4a43c)
  - [Michael Garnsey, 319th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0294/teamlist)
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
  - [Benjamin Saracevic, 500th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0498/teamlist)
  - [David Benjamin King, 973rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0672/teamlist)
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
  - [Eden Batchelor, 4th, 29 Sep 2026](https://pokepast.es/50398ef3664d4646)
  - [Eden Batchelor, 4th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0423/teamlist)
  - [DidiVGC, Champion, 21 Sep 2026](https://pokepast.es/c06c65c34f56a094)
  - [Alex Soto, , 29 Sep 2026](https://pokepast.es/65dcc792999cfb88)
  - [Alex Soto, , 29 Sep 2026](https://pokepast.es/f04e9a505bca4531)
  - [yozora_952, , 15 Sep 2026](https://pokepast.es/90f7ccc7ab5b3d0b)
  - [Dylan Bower, 183rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0297/teamlist)
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
  - [Thomas Parzuchowski, 721st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0065/teamlist)
  - [Amir Harris, 672nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0617/teamlist)
  - [Roman Manukjan, 212th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1073/teamlist)
  - [Dylan Coleman, 1117th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0648/teamlist)
  - [Emily Olynick, 432nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0758/teamlist)
  - [Christopher Carcamo, 979th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0269/teamlist)
  - [David Grigoleit, 579th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0954/teamlist)
  - [Trent Rose, 695th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0168/teamlist)
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
  - [Bayley Moore, 277th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0009/teamlist)
  - [Fabian Mayer, 873rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0005/teamlist)
  - [Tommy Chov, 1086th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0183/teamlist)
  - [joej_live, 52nd, 21 Sep 2026](https://pokepast.es/dce537b603b784c6)
  - [Joey McGinley, 52nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0487/teamlist)
  - [Skylar Simonds, 775th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0049/teamlist)
  - [Matthew Irwin, 658th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0901/teamlist)
  - [Alexa Belgard, 1010th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1077/teamlist)
  - [Darryl Brice, 498th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0126/teamlist)
  - [Hisashiro Egashira, 836th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1046/teamlist)
  - [Giovanni Cabrera, 964th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0914/teamlist)
  - [Lorenzo Bucci, , 10 Sep 2026](https://pokepast.es/42f46820310783d1)
  - [Charles Hsiao, 625th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0399/teamlist)
  - [Naoyuki Matsuhashi, , 9 Sep 2026](https://pokepast.es/0071e895c381dd1c)
  - [Joshua Fitzhardy, 110th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0108/teamlist)
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
  - [Amrit Mann, 444th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0962/teamlist)
  - [Jesse Trevino, 155th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0476/teamlist)
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
  - [Deonté Hughes, 737th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0163/teamlist)
  - [Evan Carpenter, 580th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0292/teamlist)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/ede01efa45931c7c)
  - [Amar Curic, 188th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0289/teamlist)
  - [Marc Hooijenga, 759th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0071/teamlist)
  - [Matthew Molnar, 555th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0525/teamlist)
  - [David Mainato, 795th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0014/teamlist)
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
  - [Timo Florian, 384th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0796/teamlist)
  - [Matthew Herndon, 999th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0094/teamlist)
  - [Adam Warren, 1056th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0657/teamlist)
  - [Motochika Nabeshima, , 9 Sep 2026](https://pokepast.es/5268ba8166d67ebf)
  - [Alex Thompson, 400th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0115/teamlist)
  - [Jonathan Lin, 123rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0648/teamlist)
  - [joserockzvgc, , 16 Sep 2026](https://pokepast.es/ee07bfede4b559fc)
  - [Florian Hoffmann, 174th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0702/teamlist)
  - [Marco Metelli, 205th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0028/teamlist)
  - [Luka Rüeger, 929th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0214/teamlist)
  - [Brett Saguid, 708th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0667/teamlist)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/3ad6655b53208446)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/7fb12fe08230d7be)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/016cd16a7d929d4f)
  - [Juan Francisco Alcaraz, 106th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0870/teamlist)
  - [Roi Gómez García, 1037th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0268/teamlist)
  - [Andrew Krebs, Top 4, 13 Sep 2026](https://pokepast.es/a4768a9bbf6876de)
  - [Jon Huntley, 650th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0087/teamlist)
  - [Dane Bodamer, 863rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0475/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/81427a109e744097)
  - [Pasty, , 22 Sep 2026](https://pokepast.es/bc36f6f2b942701f)
  - [Pasty, , 10 Sep 2026](https://pokepast.es/9c4f7915b1d3ae07)
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
  - [Adonis Watford, 272nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0792/teamlist)
  - [Hunter Jones, 479th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0465/teamlist)
  - [Philemon Knafo, 140th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0752/teamlist)
  - [Thomas Wall, 768th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0639/teamlist)
  - [Minche Chung, 12th, 20 Sep 2026](https://pokepast.es/b987727e0339084a)
  - [Anthony Mubiala, 562nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0423/teamlist)
  - [Justin Tobias, 297th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0647/teamlist)
  - [Aspen Leahy, 735th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1002/teamlist)
  - [Victor Bo, 445th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0624/teamlist)
  - [Fang Yu-Yang, , 17 Sep 2026](https://pokepast.es/f22e21bb4d824590)
  - [Xena, , 11 Sep 2026](https://pokepast.es/983fa284e2d45493)
  - [Justin Cerioni, , 11 Sep 2026](https://pokepast.es/a88b8c2cfc274ab2)
  - [homura_kurenai_, , 9 Sep 2026](https://pokepast.es/183a005c8a677937)
  - [Eric Partelow, 884th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0206/teamlist)
  - [Stefano Greppi, 40th, 16 Sep 2026](https://pokepast.es/0d18f72df8bd8d92)
  - [Lucas MacKenzie, 971st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0216/teamlist)
  - [Albert Kinas, 703rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0987/teamlist)
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
  - [meme guy, , 21 Sep 2026](https://pokepast.es/4f38b7f69d069b67)
  - [poke_sky39, 9th, 13 Sep 2026](https://pokepast.es/eedf279fbc3ad845)
  - [Athan Mallios, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0074/teamlist)
  - [Luke Weiland, 875th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0638/teamlist)
  - [Matthew Coldhill-Smink, 278th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0005/teamlist)
  - [Dorean Neron, 504th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0033/teamlist)
  - [Lance Lee, 1028th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0478/teamlist)
  - [drexrg, , 12 Sep 2026](https://pokepast.es/a4fd7f400e9b4e49)
  - [KingYabber, , 10 Sep 2026](https://pokepast.es/b230239de70d9692)
  - [Jeremiah Paul, 212th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0587/teamlist)
  - [Murphy Hartzenberg, 135th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0001/teamlist)
  - [Livio Sandberg, 83rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0965/teamlist)
  - [Valentijn Visser, 7th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0818/teamlist)
  - [Alex Soto, , 29 Sep 2026](https://pokepast.es/098c5931bc868979)
  - [Amethyst Leine, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0362/teamlist)
  - [Ivan Radosevic, 535th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1091/teamlist)
  - [Roel Egberts, 555th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0875/teamlist)
  - [Ignatius Lee, 159th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0091/teamlist)
  - [krlos_cb, , 13 Sep 2026](https://pokepast.es/5a8934668d366537)
  - [Víctor Medina, 29th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1013/teamlist)
  - [David Peralta Bozada, 145th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0080/teamlist)
  - [Carlos Cabal, 148th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0573/teamlist)
  - [Samuel Pereira, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0995/teamlist)
  - [Timo Jonker, 365th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0116/teamlist)
  - [Jordan Goggin, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0089/teamlist)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [Marcos Perez, 906th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0210/teamlist)
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
  - [alexandre FRIZZO, 755th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0232/teamlist)
  - [Nathaniel Spann, 132nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0125/teamlist)
  - [Justin Tang, , 14 Sep 2026](https://pokepast.es/ca08bca9b981be5c)
  - [Matthew Laughlin, 806th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1010/teamlist)
  - [ikorin_poke, , 13 Sep 2026](https://pokepast.es/ac6967ec268cbdf8)
  - [Rani De Schoenmacker, 740th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0150/teamlist)
  - [Matteo Paviza, 877th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0022/teamlist)
  - [Tyler Norton, 546th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0458/teamlist)
  - [Gabe Baum, 53rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0364/teamlist)
  - [Yuma Kinugawa, , 12 Sep 2026](https://pokepast.es/348d665e808bb457)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)
  - [Andrew Gouck, 74th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0108/teamlist)
  - [George Caddell, 804th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0692/teamlist)
  - [jessditta, , 10 Sep 2026](https://pokepast.es/25ab0498e06c8ffb)
  - [Óscar Martínez Rosell, 600th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0708/teamlist)
  - [mofumofunatsuhi, , 13 Sep 2026](https://pokepast.es/f85b026e5b0e6567)
  - [Martin Steinbaron, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1006/teamlist)
  - [Ronan Kitchen, 551st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0970/teamlist)
  - [oshio_pokemon, , 11 Sep 2026](https://pokepast.es/667cb69f9c820c84)
  - [Padrick Moran, 748th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1071/teamlist)
  - [Prncessdiana, , 20 Sep 2026](https://pokepast.es/1c95ff346ce5b161)
  - [Damahni Palmer, , 15 Sep 2026](https://pokepast.es/b915ba990518d865)
  - [Max Hofmann, 954th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1089/teamlist)
  - [Wesley Brainard, 201st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0114/teamlist)
  - [Vito Jacono, 825th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0175/teamlist)
  - [Curtis Ridings, 128th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0184/teamlist)
  - [Chern Yean Sim, 200th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0213/teamlist)
  - [William Pye, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0229/teamlist)
  - [Victor Bonfili, 929th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0979/teamlist)
  - [Kazeno_shion, , 10 Sep 2026](https://pokepast.es/e638bc044d3cb47f)
  - [Nate Innocenti, , 10 Sep 2026](https://pokepast.es/af730dd6acb60086)
  - [Tom de Gruijter, 217th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1093/teamlist)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/01069fa4762c8613)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [Duy Nguyen, 892nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0598/teamlist)
  - [Nick Smith, 780th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1003/teamlist)
  - [Jayson Lyon, 837th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0516/teamlist)
  - [Leon-Máxim Seul, 447th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0513/teamlist)
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
  - [BlazeG1798, 21st, 27 Sep 2026](https://pokepast.es/856c378ecfafa498)
  - [Duy Thang Nguyen, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0617/teamlist)
  - [Nils von Lengerke, 456th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0950/teamlist)
  - [Arnout Bruijn, 1006th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0236/teamlist)
  - [Noah Gelman, 706th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0418/teamlist)

- Sub-community pass: 984 primary teams, 151 tokens, modularity 0.49; unconnected tokens: Ninetales-Alola (other item; Focus Sash 6/12), Indeedee-F@Psychic Seed, Maushold (other item; Chople Berry 5/9), Talonflame (other item; Focus Sash 5/9), Indeedee (other item; Choice Scarf 4/6), Weavile (other item; Focus Sash 4/6), Dragonite (other item; Life Orb 4/5), Scrafty (other item; Focus Sash 2/5), Mamoswine (other item; Focus Sash 4/4), Politoed (other item; Sitrus Berry 3/4), Aerodactyl (other item; Focus Sash 2/3), Annihilape (other item; Leftovers 2/3), Armarouge (other item; Life Orb 2/3), Chandelure (other item; Chandelurite 1/3), Corviknight (other item; Leftovers 3/3), Espathra (other item; Focus Sash 2/3), Mawile (other item; Mawilite 3/3), Raichu (other item; Raichunite X 3/3), Torkoal (other item; Charcoal 2/3), Toxapex (other item; Leftovers 3/3), Tsareena (other item; Wide Lens 3/3); unassigned within the community: 5 teams (0.5% of its primary weight); hybrid teams of the community left out: 419
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 4 (0.65) | 6 (0.57) | 7 (0.49) | 8 (0.42) | 10 (0.38) |

#### Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo (645 primary teams, 275 distinct builds, top pair on 479/645)
- Megas on member teams: Raichu-Y 508, Staraptor 264, Salamence 246, Froslass 30, Golisopod 24, Garchomp-Z 22
- Top species by team share: Rillaboom 93%, Raichu 79%, Gholdengo 74%, Arcanine-Hisui 65%, Staraptor 41%, Salamence 38%
- Token label: Rillaboom / Raichu@Raichunite Y / Gholdengo
- Mode tags on primary teams: Tailwind 517, Setup 166, Snow 47, Trick Room 22, Psyspam 13, Screens 13, Sand 11, Sun 9, Rain 6, Perish Trap 1
- Primary teams: 645 (67.2% of the community's primary weight), hybrid teams: 125 (12.9%)
- Date range: 2026-09-09 to 2026-09-30
- Core pairs: Excadrill (other item; Focus Sash 3/4)+Milotic (other item; Leftovers 110/187), Milotic (other item; Leftovers 110/187)+Ceruledge@Grassy Seed, Milotic (other item; Leftovers 110/187)+Hydreigon@Choice Scarf, Ceruledge@Grassy Seed+Staraptor@Staraptite, Ceruledge (other item; Colbur Berry 10/10)+Milotic (other item; Leftovers 110/187), Milotic (other item; Leftovers 110/187)+Grimmsnarl@Light Clay, Sylveon (other item; Fairy Feather 215/221)+Staraptor@Staraptite, Milotic (other item; Leftovers 110/187)+Tyranitar@Tyranitarite, Milotic (other item; Leftovers 110/187)+Metagross@Metagrossite, Farigiraf (other item; Sitrus Berry 41/51)+Sylveon (other item; Fairy Feather 215/221), Garchomp@Choice Scarf+Staraptor@Staraptite, Whimsicott (other item; Focus Sash 13/18)+Staraptor@Staraptite, Hydreigon@Choice Scarf+Staraptor@Staraptite, Milotic (other item; Leftovers 110/187)+Baxcalibur@Baxcalibrite, Milotic (other item; Leftovers 110/187)+Absol@Absolite Z, Garchomp (other item; Sitrus Berry 6/15)+Staraptor@Staraptite, Milotic (other item; Leftovers 110/187)+Aerodactyl@Aerodactylite, Milotic (other item; Leftovers 110/187)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 244/302)+Primarina@Grassy Seed, Arcanine-Hisui (other item; Focus Sash 467/478)+Gardevoir@Gardevoirite, Glimmora (other item; Focus Sash 10/12)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 467/478)+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 467/478)+Sylveon (other item; Fairy Feather 215/221), Arcanine-Hisui (other item; Focus Sash 467/478)+Annihilape@Choice Scarf, Milotic (other item; Leftovers 110/187)+Staraptor@Staraptite, Baxcalibur (other item; Life Orb 4/5)+Raichu@Raichunite Y, Primarina@Grassy Seed+Raichu@Raichunite Y, Incineroar (other item; Sitrus Berry 244/302)+Garchomp@Choice Scarf, Arcanine-Hisui (other item; Focus Sash 467/478)+Kingambit (other item; Life Orb 75/152), Arcanine-Hisui (other item; Focus Sash 467/478)+Gholdengo@Grassy Seed, Milotic (other item; Leftovers 110/187)+Charizard@Charizardite Y, Gholdengo (other item; Life Orb 579/599)+Ceruledge@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Staraptor@Staraptite, Ceruledge@Grassy Seed+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 579/599)+Blaziken@Blazikenite, Primarina (other item; Leftovers 12/28)+Staraptor@Staraptite, Gholdengo@Choice Scarf+Staraptor@Staraptite, Gholdengo (other item; Life Orb 579/599)+Aerodactyl@Aerodactylite, Gholdengo (other item; Life Orb 579/599)+Floette-Eternal@Floettite, Gholdengo (other item; Life Orb 579/599)+Absol@Absolite Z, Raichu@Raichunite Y+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 467/478)+Sneasler (other item; White Herb 95/113), Salamence@Salamencite+Tyranitar@Tyranitarite, Arcanine-Hisui (other item; Focus Sash 467/478)+Primarina@Grassy Seed, Basculegion (other item; Life Orb 43/63)+Sylveon (other item; Fairy Feather 215/221), Arcanine-Hisui (other item; Focus Sash 467/478)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 467/478)+Indeedee-F (other item; Rocky Helmet 8/17), Glimmora (other item; Focus Sash 10/12)+Incineroar (other item; Sitrus Berry 244/302), Gholdengo (other item; Life Orb 579/599)+Milotic@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Tyranitar@Tyranitarite, Annihilape@Choice Scarf+Raichu@Raichunite Y, Sylveon (other item; Fairy Feather 215/221)+Raichu@Raichunite Y, Arcanine-Hisui (other item; Focus Sash 467/478)+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 579/599)+Milotic (other item; Leftovers 110/187), Raichu@Raichunite Y+Volcarona@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Garchomp@Choice Scarf, Gholdengo (other item; Life Orb 579/599)+Annihilape@Choice Scarf, Froslass@Froslassite+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 579/599)+Primarina@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Sneasler@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Sylveon (other item; Fairy Feather 215/221), Gholdengo@Grassy Seed+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 579/599)+Raichu@Raichunite Y, Rillaboom (other item; Miracle Seed 747/907)+Primarina@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 747/907)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 747/907)+Farigiraf@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Ceruledge@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Gholdengo@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 747/907)+Aerodactyl@Aerodactylite, Blaziken (other item; Focus Sash 6/8)+Rillaboom (other item; Miracle Seed 747/907), Pelipper (other item; Focus Sash 6/13)+Rillaboom (other item; Miracle Seed 747/907), Rillaboom (other item; Miracle Seed 747/907)+Glimmora@Glimmoranite, Rillaboom (other item; Miracle Seed 747/907)+Volcarona@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Milotic@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Blaziken@Blazikenite, Lycanroc-Dusk (other item; Focus Sash 4/4)+Rillaboom (other item; Miracle Seed 747/907), Rillaboom (other item; Miracle Seed 747/907)+Vanilluxe (other item; Choice Scarf 4/4), Camerupt (other item; Cameruptite 4/4)+Rillaboom (other item; Miracle Seed 747/907), Rillaboom (other item; Miracle Seed 747/907)+Froslass@Froslassite, Rillaboom (other item; Miracle Seed 747/907)+Sneasler@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Dragonite@Dragoninite, Kingambit (other item; Life Orb 75/152)+Raichu@Raichunite Y, Rillaboom (other item; Miracle Seed 747/907)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 747/907)+Lucario@Lucarionite Z, Gholdengo (other item; Life Orb 579/599)+Gardevoir@Gardevoirite, Rillaboom (other item; Miracle Seed 747/907)+Salamence@Salamencite, Arcanine-Hisui (other item; Focus Sash 467/478)+Rillaboom (other item; Miracle Seed 747/907), Arcanine-Hisui (other item; Focus Sash 467/478)+Gholdengo (other item; Life Orb 579/599), Gholdengo (other item; Life Orb 579/599)+Rillaboom (other item; Miracle Seed 747/907), Rillaboom (other item; Miracle Seed 747/907)+Raichu@Raichunite Y, Ceruledge (other item; Colbur Berry 10/10)+Raichu@Raichunite Y, Gholdengo (other item; Life Orb 579/599)+Salamence@Salamencite, Sneasler (other item; White Herb 95/113)+Raichu@Raichunite Y, Rillaboom (other item; Miracle Seed 747/907)+Sneasler (other item; White Herb 95/113), Rillaboom (other item; Miracle Seed 747/907)+Tyranitar@Tyranitarite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Rillaboom (other item; Miracle Seed 747/907) | 92.4% |
| Raichu@Raichunite Y | 80.3% |
| Gholdengo (other item; Life Orb 579/599) | 67.8% |
| Arcanine-Hisui (other item; Focus Sash 467/478) | 64.2% |
| Staraptor@Staraptite | 43.5% |
| Sylveon (other item; Fairy Feather 215/221) | 30.2% |
| Milotic (other item; Leftovers 110/187) | 25.6% |
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
  - [Patrick Daglas, 148th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0007/teamlist)
  - [Rehan Ahmed, 753rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0037/teamlist)
  - [Gerry Thompson, 1037th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0496/teamlist)
  - [Ifeanyi Okafor, 1048th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0456/teamlist)
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
  - [Mikal Mahoney, 117th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0703/teamlist)
  - [Joseph Calderon, 771st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0092/teamlist)
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
  - [John Polzin, 1071st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0093/teamlist)
  - [Edoardo Bertani, 113th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0560/teamlist)
  - [Mary Cook, 167th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0498/teamlist)
  - [Chris Meikle, 160th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0045/teamlist)
  - [Jonte Schwedler, 353rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0650/teamlist)
  - [Dylan Morgan, 711th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0369/teamlist)
  - [Jannek Brödling, 375th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0542/teamlist)
  - [Radu Troasca, 634th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0046/teamlist)
  - [Andres Jacobo, 951st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0704/teamlist)
  - [Patrick Gabbett, 662nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0740/teamlist)
  - [Aidan Junker, 526th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0383/teamlist)
  - [Jack Geronime, 260th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0220/teamlist)
  - [Micah Campbell, 922nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0326/teamlist)
  - [Hiroto Kamazawa, , 21 Sep 2026](https://pokepast.es/652a6122d64aa2c1)
  - [Richard Wan, 433rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0879/teamlist)
  - [Salvatore Maira, 597th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0626/teamlist)
  - [Guilherme Martins, 659th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0941/teamlist)
  - [Sandro Pocrnja, 98th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0886/teamlist)
  - [Astrid Gurski, 983rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0809/teamlist)
  - [Wyatt McDonald, , 14 Sep 2026](https://pokepast.es/7c4bc48a6a1906e6)
  - [Vincent VILLIERS, 523rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0689/teamlist)
  - [pathogenvgc, , 14 Sep 2026](https://pokepast.es/a630a7a5018325c9)
  - [Yuta Ishigaki, , 10 Sep 2026](https://pokepast.es/57c82d09be532ce2)
  - [Emanuel Ruf, 938th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0283/teamlist)
  - [Robin Peter, 944th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1104/teamlist)
  - [Gabriel Buchta, 1038th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1094/teamlist)
  - [Ryan Caldwell, 199th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0325/teamlist)
  - [_hendez_, , 21 Sep 2026](https://pokepast.es/2d99e9105e5f7a58)
  - [Henry Hernandez, 339th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0377/teamlist)
  - [Stephen Morris, 791st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0780/teamlist)
  - [Robbie Van der Raaf, 179th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0244/teamlist)
  - [Michael Lewis, 615th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0015/teamlist)

#### Community 0 / Sub-community 2: Setup (242 primary teams, 112 distinct builds, top pair on 136/242)
- Megas on member teams: Floette 111, Raichu-Y 92, Salamence 83, Garchomp-Z 42, Lucario-Z 39, Charizard-Y 16
- Top species by team share: Rillaboom 99%, Incineroar 88%, Sneasler 67%, Gholdengo 64%, Floette-Eternal 46%, Raichu 38%
- Token label: Incineroar / Sneasler@Grassy Seed
- Mode tags on primary teams: Setup 121, Tailwind 113, Sun 16, Snow 14, Rain 9, Screens 7, Trick Room 6, Perish Trap 5, Psyspam 3, Sand 2
- Primary teams: 242 (23.0% of the community's primary weight), hybrid teams: 62 (6.1%)
- Date range: 2026-09-09 to 2026-09-29
- Core pairs: Archaludon (other item; Leftovers 6/7)+Pelipper (other item; Focus Sash 6/13), Baxcalibur@Baxcalibrite+Ninetales-Alola@Light Clay, Aerodactyl@Aerodactylite+Charizard@Charizardite Y, Aerodactyl@Aerodactylite+Lucario@Lucarionite Z, Basculegion (other item; Life Orb 43/63)+Lucario@Lucarionite Z, Pelipper (other item; Focus Sash 6/13)+Lucario@Lucarionite Z, Basculegion@Choice Scarf+Volcarona@Grassy Seed, Baxcalibur@Baxcalibrite+Volcarona@Grassy Seed, Basculegion@Choice Scarf+Garchomp@Garchompite Z, Volcarona (other item; Rocky Helmet 16/26)+Garchomp@Garchompite Z, Garchomp@Garchompite Z+Volcarona@Grassy Seed, Garchomp@Garchompite Z+Lucario@Lucarionite Z, Sneasler (other item; White Herb 95/113)+Ninetales-Alola@Light Clay, Basculegion (other item; Life Orb 43/63)+Garchomp@Garchompite Z, Sneasler (other item; White Herb 95/113)+Baxcalibur@Baxcalibrite, Basculegion (other item; Life Orb 43/63)+Volcarona@Grassy Seed, Basculegion (other item; Life Orb 43/63)+Baxcalibur@Baxcalibrite, Floette-Eternal@Floettite+Gholdengo@Choice Scarf, Incineroar (other item; Sitrus Berry 244/302)+Aerodactyl@Aerodactylite, Primarina (other item; Leftovers 12/28)+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Rillaboom@Eject Button, Altaria (other item; Altarianite 3/5)+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 244/302)+Lucario@Lucarionite Z, Incineroar (other item; Sitrus Berry 244/302)+Gengar@Gengarite, Primarina (other item; Leftovers 12/28)+Lucario@Lucarionite Z, Floette-Eternal@Floettite+Sneasler@Grassy Seed, Dragonite@Dragoninite+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 244/302)+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 244/302)+Basculegion@Choice Scarf, Incineroar (other item; Sitrus Berry 244/302)+Pawmot (other item; Focus Sash 11/12), Incineroar (other item; Sitrus Berry 244/302)+Dragonite@Dragoninite, Milotic (other item; Leftovers 110/187)+Baxcalibur@Baxcalibrite, Rillaboom@Eject Button+Sneasler@Grassy Seed, Dragonite@Dragoninite+Sneasler@Grassy Seed, Milotic (other item; Leftovers 110/187)+Aerodactyl@Aerodactylite, Garchomp@Garchompite Z+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Gholdengo@Choice Scarf, Pawmot (other item; Focus Sash 11/12)+Salamence@Salamencite, Basculegion (other item; Life Orb 43/63)+Incineroar (other item; Sitrus Berry 244/302), Incineroar (other item; Sitrus Berry 244/302)+Pelipper (other item; Focus Sash 6/13), Basculegion@Choice Scarf+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 244/302)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 244/302)+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Kommo-o (other item; Leftovers 21/27), Incineroar (other item; Sitrus Berry 244/302)+Primarina@Grassy Seed, Farigiraf (other item; Sitrus Berry 41/51)+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Delphox@Delphoxite, Incineroar (other item; Sitrus Berry 244/302)+Absol@Absolite Z, Floette-Eternal@Floettite+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Baxcalibur@Baxcalibrite, Incineroar (other item; Sitrus Berry 244/302)+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Garchomp@Choice Scarf, Basculegion (other item; Life Orb 43/63)+Farigiraf (other item; Sitrus Berry 41/51), Aerodactyl@Aerodactylite+Sneasler@Grassy Seed, Milotic (other item; Leftovers 110/187)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 244/302)+Volcarona (other item; Rocky Helmet 16/26), Gholdengo@Choice Scarf+Sneasler@Grassy Seed, Kingambit (other item; Life Orb 75/152)+Sneasler@Grassy Seed, Froslass@Froslassite+Volcarona@Grassy Seed, Salamence@Salamencite+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Ninetales-Alola@Light Clay, Gholdengo@Choice Scarf+Staraptor@Staraptite, Gholdengo (other item; Life Orb 579/599)+Aerodactyl@Aerodactylite, Gholdengo (other item; Life Orb 579/599)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 244/302)+Farigiraf@Grassy Seed, Gengar@Gengarite+Sneasler@Grassy Seed, Charizard@Charizardite Y+Sneasler@Grassy Seed, Basculegion (other item; Life Orb 43/63)+Sylveon (other item; Fairy Feather 215/221), Kommo-o (other item; Leftovers 21/27)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 244/302)+Golisopod@Golisopite, Glimmora (other item; Focus Sash 10/12)+Incineroar (other item; Sitrus Berry 244/302), Lucario@Lucarionite Z+Sneasler@Grassy Seed, Floette-Eternal@Floettite+Salamence@Salamencite, Raichu@Raichunite Y+Volcarona@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 747/907)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 747/907)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 747/907)+Aerodactyl@Aerodactylite, Pelipper (other item; Focus Sash 6/13)+Rillaboom (other item; Miracle Seed 747/907), Rillaboom (other item; Miracle Seed 747/907)+Volcarona@Grassy Seed, Rillaboom (other item; Miracle Seed 747/907)+Sneasler@Grassy Seed, Gholdengo (other item; Life Orb 579/599)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 747/907)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 747/907)+Lucario@Lucarionite Z
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Incineroar (other item; Sitrus Berry 244/302) | 87.4% |
| Sneasler@Grassy Seed | 61.1% |
| Floette-Eternal@Floettite | 45.6% |
| Garchomp@Garchompite Z | 17.5% |
| Basculegion (other item; Life Orb 43/63) | 14.8% |
| Lucario@Lucarionite Z | 14.4% |
| Volcarona@Grassy Seed | 12.9% |
| Basculegion@Choice Scarf | 7.5% |
| Charizard@Charizardite Y | 7.1% |
| Aerodactyl@Aerodactylite | 5.4% |
| Baxcalibur@Baxcalibrite | 4.4% |
| Dragonite@Dragoninite | 4.2% |
| Pelipper (other item; Focus Sash 6/13) | 4.2% |
| Gholdengo@Choice Scarf | 2.7% |
| Ninetales-Alola@Light Clay | 2.2% |
| Archaludon (other item; Leftovers 6/7) | 2.0% |
| Altaria (other item; Altarianite 3/5) | 1.7% |
| Pawmot (other item; Focus Sash 11/12) | 1.7% |
| Rillaboom@Grassy Seed | 1.3% |
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
  - [Sean Chalungsooth, 1067th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0285/teamlist)
  - [Yan Yuen, 669th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0967/teamlist)
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
  - [James Berkley, 752nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0867/teamlist)
  - [Emery Joseph, 43rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0004/teamlist)
  - [Adrián Lozano Navarro, 101st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0450/teamlist)
  - [Luis Miguel Montesdeoca Martínez, 107th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0640/teamlist)
  - [Alex Gascon Bononad, 318th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0781/teamlist)
  - [David Perez, 858th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0383/teamlist)
  - [Jordi Casado Rejas, 976th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1002/teamlist)
  - [Eric Rios, 1st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1025/teamlist)
  - [Eric Ríos, Champion, 27 Sep 2026](https://pokepast.es/0e35fd9cd52d7552)
  - [Alex Soto, , 29 Sep 2026](https://pokepast.es/9065edc9c36b149a)
  - [Deondre Cutler, 794th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0943/teamlist)
  - [Avery Cambero, 146th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0010/teamlist)
  - [Sahen Rai, 301st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1028/teamlist)
  - [Roman Carfi, 173rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0002/teamlist)
  - [Matthew Suarez, 224th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0096/teamlist)
  - [Jannek Brödling, 375th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0542/teamlist)
  - [Dylan Morgan, 711th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0369/teamlist)
  - [Adam Azaiez, 244th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1057/teamlist)
  - [Shane De Silva, 200th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0538/teamlist)
  - [Pelle Becker, 1005th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0855/teamlist)
  - [Charlie Hall, 984th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0948/teamlist)
  - [Alejandro Tovar, 331st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0251/teamlist)
  - [Sam Sperl, 117th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0873/teamlist)
  - [Tomas Scherf, 623rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0537/teamlist)
  - [Michael Lewis, 615th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0015/teamlist)
  - [Daniel Harris, 975th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0775/teamlist)
  - [Alexander Miller, 655th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0718/teamlist)
  - [sectoniaservant, , 11 Sep 2026](https://pokepast.es/c8f60c5168bd6a83)
  - [Leon Drescher, 67th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1016/teamlist)
  - [Nicholas Woodhouse, 226th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0292/teamlist)
  - [clark smith, 1011th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0116/teamlist)
  - [Aiden Lafferty, 261st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0305/teamlist)
  - [Anke Zhang, 1100th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0486/teamlist)

#### Community 0 / Sub-community 4: Volcarona / Glimmora@Glimmoranite (8 primary teams, 6 distinct builds, top pair on 5/8)
- Megas on member teams: Glimmora 5, Raichu-Y 3, Staraptor 2, Charizard-Y 1, Gengar 1, Metagross 1
- Top species by team share: Rillaboom 88%, Volcarona 75%, Glimmora 63%, Kingambit 38%, Raichu 38%, Swampert 38%
- Token label: Volcarona / Glimmora@Glimmoranite
- Mode tags on primary teams: Tailwind 6, Sand 2, Setup 2, Sun 1
- Primary teams: 8 (0.9% of the community's primary weight), hybrid teams: 11 (1.1%)
- Date range: 2026-09-15 to 2026-09-27
- Core pairs: Indeedee-F (other item; Rocky Helmet 8/17)+Milotic@Psychic Seed, Indeedee-F (other item; Rocky Helmet 8/17)+Gardevoir@Gardevoirite, Blaziken (other item; Focus Sash 6/8)+Metagross@Metagrossite, Dragapult (other item; Life Orb 11/14)+Glimmora@Glimmoranite, Swampert (other item; Sitrus Berry 3/9)+Volcarona (other item; Rocky Helmet 16/26), Volcarona (other item; Rocky Helmet 16/26)+Glimmora@Glimmoranite, Dragapult (other item; Life Orb 11/14)+Indeedee-F (other item; Rocky Helmet 8/17), Indeedee-F (other item; Rocky Helmet 8/17)+Metagross@Metagrossite, Volcarona (other item; Rocky Helmet 16/26)+Garchomp@Garchompite Z, Kingambit (other item; Life Orb 75/152)+Glimmora@Glimmoranite, Milotic (other item; Leftovers 110/187)+Metagross@Metagrossite, Kingambit (other item; Life Orb 75/152)+Volcarona (other item; Rocky Helmet 16/26), Arcanine-Hisui (other item; Focus Sash 467/478)+Gardevoir@Gardevoirite, Incineroar (other item; Sitrus Berry 244/302)+Volcarona (other item; Rocky Helmet 16/26), Arcanine-Hisui (other item; Focus Sash 467/478)+Indeedee-F (other item; Rocky Helmet 8/17), Blaziken (other item; Focus Sash 6/8)+Rillaboom (other item; Miracle Seed 747/907), Rillaboom (other item; Miracle Seed 747/907)+Glimmora@Glimmoranite, Gholdengo (other item; Life Orb 579/599)+Gardevoir@Gardevoirite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Volcarona (other item; Rocky Helmet 16/26) | 77.0% |
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
- Core pairs: Gengar@Gengarite+Rillaboom@Eject Button, Kommo-o (other item; Leftovers 21/27)+Rillaboom@Eject Button, Kommo-o (other item; Leftovers 21/27)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 244/302)+Rillaboom@Eject Button, Kingambit (other item; Life Orb 75/152)+Rillaboom@Eject Button, Kingambit (other item; Life Orb 75/152)+Gengar@Gengarite, Kommo-o (other item; Leftovers 21/27)+Gholdengo@Grassy Seed, Incineroar (other item; Sitrus Berry 244/302)+Gengar@Gengarite, Kommo-o (other item; Leftovers 21/27)+Froslass@Froslassite, Rillaboom@Eject Button+Sneasler@Grassy Seed, Milotic (other item; Leftovers 110/187)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 244/302)+Kommo-o (other item; Leftovers 21/27), Kingambit (other item; Life Orb 75/152)+Kommo-o (other item; Leftovers 21/27), Gengar@Gengarite+Sneasler@Grassy Seed, Kommo-o (other item; Leftovers 21/27)+Floette-Eternal@Floettite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Gengar@Gengarite | 100.0% |
| Rillaboom@Eject Button | 69.1% |
| Kommo-o (other item; Leftovers 21/27) | 61.8% |
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

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite (38 distinct builds, 74 primary teams, top pair on 27/74); Sub-community 3: Golisopod@Golisopite / Farigiraf@Grassy Seed (3 distinct builds, 3 primary teams, top pair on 0/3); Sub-community 6: Garchomp / Whimsicott (0 distinct builds, 0 primary teams, top pair on 0/0)

#### Community 0 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Rillaboom | Rillaboom (other item; Miracle Seed 747/907) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 907 | 0: 593 · 2: 232 · 1: 71 · 4: 7 · 3: 2 · 5: 2 · unassigned: 0 |
| Rillaboom | Rillaboom@Eject Button | Sub-community 5: Perish Trap (Mega Gengar) | 11 | 2: 5 · 5: 5 · 0: 1 · unassigned: 0 |
| Rillaboom | Rillaboom@Grassy Seed | Sub-community 2: Setup | 11 | 0: 7 · 2: 3 · 1: 1 · unassigned: 0 |
| Gholdengo | Gholdengo (other item; Life Orb 579/599) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 599 | 0: 440 · 2: 140 · 1: 15 · 4: 1 · 5: 1 · unassigned: 2 |
| Gholdengo | Gholdengo@Grassy Seed | Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite | 54 | 0: 34 · 1: 12 · 2: 7 · 5: 1 · unassigned: 0 |
| Gholdengo | Gholdengo@Choice Scarf | Sub-community 2: Setup | 12 | 2: 7 · 0: 4 · 1: 1 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 2: Setup | 269 | 2: 150 · 0: 108 · 1: 11 · unassigned: 0 |
| Sneasler | Sneasler (other item; White Herb 95/113) | Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite | 113 | 0: 56 · 1: 44 · 2: 12 · 4: 1 · unassigned: 0 |
| Milotic | Milotic (other item; Leftovers 110/187) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 187 | 0: 161 · 2: 16 · 1: 6 · 5: 2 · unassigned: 2 |
| Milotic | Milotic@Grassy Seed | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 14 | 0: 9 · 2: 2 · 1: 1 · 3: 1 · 4: 1 · unassigned: 0 |
| Milotic | Milotic@Psychic Seed | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 6 | 0: 4 · 4: 1 · unassigned: 1 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 2: Setup | 64 | 2: 42 · 0: 22 · unassigned: 0 |
| Garchomp | Garchomp (other item; Sitrus Berry 6/15) | Sub-community 6: Garchomp / Whimsicott | 15 | 0: 14 · 2: 1 · unassigned: 0 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 9 | 0: 8 · 2: 1 · unassigned: 0 |
| Ceruledge | Ceruledge@Grassy Seed | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 60 | 0: 60 · unassigned: 0 |
| Ceruledge | Ceruledge (other item; Colbur Berry 10/10) | Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite | 10 | 0: 7 · 1: 3 · unassigned: 0 |
| Volcarona | Volcarona@Grassy Seed | Sub-community 2: Setup | 48 | 2: 25 · 0: 21 · 1: 2 · unassigned: 0 |
| Volcarona | Volcarona (other item; Rocky Helmet 16/26) | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 26 | 0: 11 · 4: 6 · 1: 4 · 2: 3 · 5: 1 · unassigned: 1 |
| Primarina | Primarina (other item; Leftovers 12/28) | Sub-community 3: Golisopod@Golisopite / Farigiraf@Grassy Seed | 28 | 0: 19 · 2: 7 · 1: 1 · 3: 1 · unassigned: 0 |
| Primarina | Primarina@Grassy Seed | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 10 | 0: 10 · unassigned: 0 |
| Baxcalibur | Baxcalibur@Baxcalibrite | Sub-community 2: Setup | 20 | 2: 11 · 0: 8 · 1: 1 · unassigned: 0 |
| Baxcalibur | Baxcalibur (other item; Life Orb 4/5) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 5 | 0: 3 · 2: 2 · unassigned: 0 |
| Glimmora | Glimmora@Glimmoranite | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 16 | 0: 7 · 4: 5 · 1: 2 · 2: 2 · unassigned: 0 |
| Glimmora | Glimmora (other item; Focus Sash 10/12) | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 12 | 0: 10 · 1: 1 · 3: 1 · unassigned: 0 |
| Blaziken | Blaziken (other item; Focus Sash 6/8) | Sub-community 4: Volcarona / Glimmora@Glimmoranite | 8 | 0: 6 · 1: 1 · 4: 1 · unassigned: 0 |
| Blaziken | Blaziken@Blazikenite | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 5 | 0: 3 · 1: 2 · unassigned: 0 |

### Community 1: Sneasler / Salamence
- Token label: Sneasler / Salamence
- Mode tags on primary teams: Tailwind 348, Sand 240, Psyspam 206, Snow 84, Setup 62, Sun 47, Trick Room 35, Rain 8, Screens 4
- Megas on primary teams: Salamence 433, Tyranitar 219, Froslass 72, Floette 60, Metagross 46, Charizard-Y 39, Garchomp-Z 30, Delphox 25, Gardevoir 17, Dragonite 15, Golisopod 14, Raichu-Y 12, Glimmora 11, Staraptor 11, Blaziken 10, Scovillain 10, Meowstic-F 8, Baxcalibur 6, Blastoise 6, Gengar 6, Absol-Z 5, Lucario-Z 5, Alakazam 3, Excadrill 3, Garchomp 3, Meganium 3, Steelix 3, Camerupt 2, Mawile 2, Pidgeot 2, Raichu-X 2, Aerodactyl 1, Altaria 1, Chandelure 1, Eelektross 1, Feraligatr 1, Heracross 1, Lopunny 1, Manectric 1, Pyroar 1, Sableye 1, Sceptile 1, Starmie 1, Swampert 1, Venusaur 1
- Primary teams: 597 (primary share 19.2%), hybrid teams: 464 (hybrid share 15.2%)
- Date range: 2026-09-09 to 2026-09-29
- Core pairs: Scovillain+Lycanroc-Dusk@Focus Sash, Lycanroc-Dusk+Scovillain@Scovillainite, Lycanroc-Dusk+Scovillain, Typhlosion-Hisui+Indeedee@Focus Sash, Corviknight+Indeedee@Choice Scarf, Indeedee+Corviknight@Psychic Seed, Excadrill+Corviknight@Psychic Seed, Pawmot+Kingambit@Occa Berry, Corviknight+Excadrill@Focus Sash, Excadrill+Tyranitar@Tyranitarite, Blaziken+Kingambit@Occa Berry, Corviknight+Indeedee, Corviknight+Excadrill, Aerodactyl+Kingambit@Focus Sash, Tyranitar+Excadrill@Focus Sash, Tyranitar+Excadrill@Life Orb, Tyranitar+Corviknight@Psychic Seed, Corviknight+Tyranitar@Tyranitarite, Excadrill+Tyranitar, Lycanroc-Dusk+Rillaboom@Life Orb, Camerupt+Kingambit@Black Glasses, Delphox+Sneasler@Focus Sash, Hatterene+Kingambit@Black Glasses, Corviknight+Tyranitar, Lycanroc-Dusk+Kingambit@Black Glasses, Froslass+Lycanroc-Dusk@Focus Sash, Froslass+Lycanroc-Dusk, Lycanroc-Dusk+Froslass@Froslassite, Excadrill+Indeedee@Choice Scarf, Meowstic-F+Sneasler@Psychic Seed, Scovillain+Kingambit@Black Glasses, Sinistcha+Sneasler@Focus Sash, Blastoise+Sneasler@Focus Sash, Maushold+Sneasler@Focus Sash, Excadrill+Tyranitar@Choice Scarf, Tyranitar+Indeedee@Choice Scarf, Starmie+Sneasler@Psychic Seed, Excadrill+Corviknight@Leftovers, Tyranitar+Corviknight@Leftovers, Indeedee+Excadrill@Focus Sash, Excadrill+Indeedee, Indeedee+Tyranitar@Tyranitarite, Kommo-o+Indeedee@Focus Sash, Gardevoir+Rotom-Heat@Sitrus Berry, Indeedee+Tyranitar, Froslass+Scovillain@Scovillainite, Gardevoir+Sneasler@Psychic Seed, Indeedee+Excadrill@Life Orb, Excadrill+Tyranitar@Chople Berry, Froslass+Scovillain, Scovillain+Froslass@Froslassite, Excadrill+Rillaboom@Expert Belt, Corviknight+Sneasler@White Herb, Indeedee+Sneasler@Psychic Seed, Dragonite+Indeedee@Focus Sash, Rotom-Heat+Tyranitar@Tyranitarite, Glimmora+Kingambit@Occa Berry, Tyranitar+Rillaboom@Expert Belt, Rotom-Heat+Tyranitar, Indeedee+Typhlosion-Hisui, Tyranitar+Garchomp@Garchompite, Indeedee+Typhlosion-Hisui@Choice Scarf, Indeedee+Corviknight@Leftovers, Rotom-Heat+Indeedee-F@Colbur Berry, Froslass+Kingambit@Life Orb, Delphox+Kingambit@Life Orb, Rotom-Heat+Excadrill@Focus Sash, Lycanroc-Dusk+Basculegion@Life Orb, Excadrill+Milotic@Sitrus Berry, Whimsicott+Kingambit@Occa Berry, Metagross+Indeedee@Choice Scarf, Scovillain+Basculegion@Life Orb, Kingambit+Altaria@Altarianite, Kingambit+Hippowdon@Leftovers, Excadrill+Rotom-Heat, Floette-Eternal+Sneasler@Focus Sash, Glimmora+Indeedee@Focus Sash, Froslass+Rillaboom@Sitrus Berry, Tyranitar+Milotic@Sitrus Berry, Volcarona+Kingambit@Occa Berry, Excadrill+Sinistcha@Sitrus Berry, Milotic+Excadrill@Life Orb, Indeedee+Metagross@Metagrossite, Farigiraf+Kingambit@Focus Sash, Charizard+Kingambit@Focus Sash, Sylveon+Kingambit@Focus Sash, Indeedee-F+Sneasler@Psychic Seed, Indeedee+Metagross, Scovillain+Indeedee-F@Colbur Berry, Indeedee+Venusaur@Life Orb, Gardevoir+Rotom-Heat, Rotom-Heat+Gardevoir@Gardevoirite, Blaziken+Kingambit@Black Glasses, Golisopod+Rotom-Heat@Sitrus Berry, Salamence+Klefki@Light Clay, Hippowdon+Kingambit, Armarouge+Sneasler@Psychic Seed, Salamence+Corviknight@Psychic Seed, Venusaur+Indeedee@Focus Sash, Garchomp+Kingambit@Focus Sash, Salamence+Excadrill@Life Orb, Sinistcha+Kingambit@Life Orb, Froslass+Blaziken@Blazikenite, Tyranitar+Sinistcha@Sitrus Berry, Kingambit+Volcarona@Focus Sash, Kingambit+Sylveon@Life Orb, Froslass+Sneasler@White Herb, Salamence+Rillaboom@Expert Belt, Torkoal+Kingambit@Focus Sash, Excadrill+Sneasler@White Herb, Baxcalibur+Sneasler@Focus Sash, Basculegion+Kingambit@Occa Berry, Charizard+Indeedee@Focus Sash, Kingambit+Torkoal@Life Orb, Scovillain+Pelipper@Focus Sash, Salamence+Excadrill@Focus Sash, Milotic+Excadrill@Focus Sash, Kingambit+Aerodactyl@Aerodactylite, Indeedee+Sneasler@White Herb, Salamence+Indeedee@Choice Scarf, Klefki+Salamence@Salamencite, Excadrill+Salamence, Klefki+Salamence, Metagross+Sneasler@Psychic Seed, Excadrill+Salamence@Salamencite, Excadrill+Milotic, Milotic+Tyranitar@Tyranitarite, Kingambit+Incineroar@White Herb, Typhlosion-Hisui+Sneasler@Psychic Seed, Lycanroc-Dusk+Sneasler@White Herb, Tyranitar+Sneasler@White Herb, Indeedee+Kommo-o@Life Orb, Kingambit+Blaziken@Blazikenite, Kingambit+Meowstic-F, Kingambit+Meowstic-F@Meowsticite, Rotom-Heat+Sneasler@Psychic Seed, Salamence+Ceruledge@Colbur Berry, Froslass+Kingambit, Kingambit+Froslass@Froslassite, Indeedee-F+Rotom-Heat@Sitrus Berry, Salamence+Tyranitar@Tyranitarite, Froslass+Kingambit@Black Glasses, Floette-Eternal+Sneasler@Grassy Seed, Hippowdon+Incineroar, Sneasler+Indeedee@Colbur Berry, Sneasler+Altaria@Altarianite, Delphox+Kingambit@Black Glasses, Milotic+Tyranitar, Kingambit+Sneasler@Life Orb, Basculegion+Lycanroc-Dusk@Focus Sash, Corviknight+Salamence@Salamencite, Corviknight+Salamence, Talonflame+Tyranitar, Sneasler+Corviknight@Psychic Seed, Kleavor+Kingambit@Chople Berry, Salamence+Tyranitar, Salamence+Gholdengo@Grassy Seed, Kingambit+Sneasler@Focus Sash, Kingambit+Volcarona@Rocky Helmet, Tyranitar+Salamence@Salamencite, Salamence+Sylveon@Life Orb, Blaziken+Kingambit@Focus Sash, Froslass+Arcanine-Hisui@Focus Sash, Arcanine-Hisui+Froslass, Arcanine-Hisui+Froslass@Froslassite, Dragonite+Sneasler@White Herb, Froslass+Politoed@Sitrus Berry, Garchomp+Corviknight@Leftovers, Whimsicott+Indeedee@Focus Sash, Basculegion+Lycanroc-Dusk, Froslass+Pawmot@Focus Sash, Excadrill+Milotic@Leftovers, Kingambit+Garchomp@Life Orb, Aerodactyl+Kingambit, Sneasler+Indeedee@Choice Scarf, Kingambit+Garchomp@Garchompite, Kingambit+Delphox@Delphoxite, Basculegion+Scovillain@Scovillainite, Scovillain+Sneasler@White Herb, Gardevoir+Kingambit@Occa Berry, Froslass+Rillaboom@Eject Button, Sneasler+Arcanine-Hisui@Life Orb, Whimsicott+Kingambit@Chople Berry, Salamence+Indeedee@Twisted Spoon, Kingambit+Sinistcha@Colbur Berry, Delphox+Kingambit, Indeedee+Salamence@Salamencite, Indeedee+Salamence, Tyranitar+Milotic@Leftovers, Basculegion+Scovillain, Indeedee+Sneasler, Floette-Eternal+Kingambit@Life Orb, Absol+Sneasler@Psychic Seed, Basculegion+Kingambit@Chople Berry, Kingambit+Basculegion@Life Orb, Blaziken+Froslass, Blaziken+Froslass@Froslassite, Kingambit+Whimsicott@Fairy Feather, Glimmora+Kingambit@Focus Sash, Camerupt+Kingambit, Kingambit+Camerupt@Cameruptite, Milotic+Indeedee@Choice Scarf, Corviknight+Sneasler, Blastoise+Sneasler@Psychic Seed, Froslass+Sneasler@Grassy Seed, Indeedee+Milotic@Leftovers, Meowstic-F+Sneasler, Sneasler+Meowstic-F@Meowsticite, Blaziken+Kingambit, Kingambit+Hatterene@Life Orb, Kingambit+Lycanroc-Dusk@Focus Sash, Froslass+Rillaboom@Life Orb, Incineroar+Sneasler@Focus Sash, Kingambit+Rillaboom@Sitrus Berry, Excadrill+Sinistcha@Colbur Berry, Froslass+Volcarona@Grassy Seed, Indeedee+Milotic@Sitrus Berry, Annihilape+Sneasler@Psychic Seed, Kingambit+Whimsicott@Occa Berry, Kingambit+Rillaboom@Kebia Berry, Froslass+Pawmot, Pawmot+Froslass@Froslassite, Sneasler+Indeedee@Focus Sash, Hatterene+Kingambit, Rotom-Heat+Golisopod@Golisopite, Froslass+Kingambit@Chople Berry, Golisopod+Rotom-Heat, Floette-Eternal+Kingambit@Occa Berry, Kingambit+Lycanroc-Dusk, Altaria+Sneasler@Grassy Seed, Sneasler+Sinistcha@Occa Berry, Salamence+Rillaboom@Sitrus Berry, Sneasler+Excadrill@Life Orb, Hippowdon+Rillaboom, Rillaboom+Sneasler@Grassy Seed, Rillaboom+Hippowdon@Leftovers, Salamence+Sneasler@Grassy Seed, Sneasler+Rotom-Heat@Sitrus Berry, Sneasler+Gholdengo@Focus Sash, Arcanine-Hisui+Kingambit@Life Orb, Arcanine-Hisui+Sneasler@Grassy Seed, Sneasler+Kingambit@Life Orb, Tyranitar+Sneasler@Psychic Seed, Kingambit+Ninetales-Alola@Choice Scarf, Sneasler+Starmie, Salamence+Kingambit@Chople Berry, Hippowdon+Rillaboom@Miracle Seed, Kingambit+Basculegion@Mystic Water, Kingambit+Torkoal, Kingambit+Indeedee-F@Sitrus Berry, Kingambit+Scovillain@Scovillainite, Excadrill+Sneasler@Psychic Seed, Indeedee-F+Kingambit@Occa Berry, Volcarona+Kingambit@Focus Sash, Salamence+Rillaboom@Kebia Berry, Primarina+Kingambit@Focus Sash, Sneasler+Volcarona@Focus Sash, Kingambit+Zoroark-Hisui, Froslass+Sneasler, Sneasler+Froslass@Froslassite, Kingambit+Torkoal@Charcoal, Kingambit+Garchomp@Choice Scarf, Tyranitar+Sinistcha@Colbur Berry, Sneasler+Delphox@Delphoxite, Sinistcha+Tyranitar@Tyranitarite, Farigiraf+Kingambit@Black Glasses, Indeedee-F+Scovillain, Kingambit+Scovillain, Kingambit+Ceruledge@Colbur Berry, Kingambit+Ninetales-Alola@Never-Melt Ice, Indeedee+Milotic, Baxcalibur+Sneasler@White Herb, Indeedee+Kommo-o, Sneasler+Indeedee-F@Colbur Berry, Venusaur+Sneasler@Psychic Seed, Sneasler+Dragonite@Dragoninite, Delphox+Sneasler, Kingambit+Garchomp@Sitrus Berry, Sinistcha+Tyranitar, Talonflame+Tyranitar@Tyranitarite, Torkoal+Kingambit@Black Glasses, Kingambit+Meganium, Kingambit+Meganium@Meganiumite, Sneasler+Maushold@Chople Berry, Salamence+Tyranitar@Choice Scarf, Sneasler+Sinistcha@Coba Berry, Indeedee-F+Kingambit@Black Glasses, Indeedee-F+Rotom-Heat, Metagross+Indeedee@Focus Sash, Sneasler+Garchomp@Garchompite, Basculegion+Indeedee@Focus Sash, Salamence+Sneasler@White Herb, Floette-Eternal+Sneasler, Sneasler+Floette-Eternal@Floettite, Ninetales-Alola+Sneasler@Focus Sash, Lucario+Sneasler@Grassy Seed, Indeedee+Dragonite@Dragoninite, Blastoise+Kingambit@Focus Sash, Blaziken+Kingambit@Chople Berry, Basculegion+Kingambit@Black Glasses, Gardevoir+Sneasler, Sneasler+Gardevoir@Gardevoirite, Sneasler+Blastoise@Blastoisinite, Raichu+Kingambit@Life Orb, Gholdengo+Sneasler@Grassy Seed, Sneasler+Indeedee@Twisted Spoon, Mamoswine+Salamence@Salamencite, Sneasler+Rillaboom@Kebia Berry, Mamoswine+Salamence, Salamence+Mamoswine@Focus Sash, Indeedee-F+Scovillain@Scovillainite, Froslass+Volcarona, Volcarona+Froslass@Froslassite, Indeedee+Glimmora@Glimmoranite, Dragonite+Sneasler, Salamence+Milotic@Sitrus Berry, Kingambit+Farigiraf@Sitrus Berry, Salamence+Arcanine-Hisui@Focus Sash, Glimmora+Kingambit, Kingambit+Glimmora@Glimmoranite, Arcanine-Hisui+Salamence@Salamencite, Arcanine-Hisui+Salamence, Kingambit+Glimmora@Focus Sash, Sneasler+Indeedee-F@Sitrus Berry, Baxcalibur+Froslass, Baxcalibur+Froslass@Froslassite, Torkoal+Kingambit@Chople Berry, Volcarona+Sneasler@White Herb, Corviknight+Delphox, Blastoise+Sneasler, Kingambit+Sneasler@Grassy Seed, Pelipper+Scovillain@Scovillainite, Garchomp+Kingambit@Occa Berry, Sneasler+Rillaboom@Sitrus Berry, Sneasler+Salamence@Salamencite, Garchomp+Rotom-Heat, Salamence+Sneasler, Salamence+Primarina@Grassy Seed, Glimmora+Kingambit@Life Orb, Sneasler+Tyranitar@Tyranitarite, Incineroar+Sneasler@Grassy Seed, Salamence+Milotic@Leftovers, Froslass+Kommo-o@Leftovers, Pelipper+Scovillain, Armarouge+Kingambit@Focus Sash, Hydreigon+Sneasler@Psychic Seed, Kingambit+Indeedee-F@Psychic Seed, Sinistcha+Excadrill@Focus Sash, Gardevoir+Kingambit@Chople Berry, Pelipper+Sneasler@Psychic Seed, Kingambit+Kommo-o@Leftovers, Kingambit+Kleavor@Focus Sash, Raichu+Sneasler@Grassy Seed, Salamence+Basculegion@Life Orb, Corviknight+Indeedee-F@Colbur Berry, Excadrill+Sinistcha, Indeedee+Kommo-o@Leftovers, Froslass+Rillaboom, Rillaboom+Froslass@Froslassite, Dragonite+Froslass, Dragonite+Froslass@Froslassite, Gholdengo+Salamence, Gholdengo+Salamence@Salamencite, Sneasler+Excadrill@Focus Sash, Arcanine-Hisui+Kingambit@Chople Berry, Incineroar+Kingambit@Black Glasses, Garchomp+Sneasler@Focus Sash, Farigiraf+Kingambit, Salamence+Primarina@Leftovers, Dragonite+Indeedee, Sneasler+Kingambit@Chople Berry, Kingambit+Floette-Eternal@Floettite, Froslass+Basculegion@Life Orb, Floette-Eternal+Kingambit, Garchomp+Kingambit, Sinistcha+Kingambit@Black Glasses, Sneasler+Tyranitar, Kingambit+Aerodactyl@Focus Sash, Floette-Eternal+Kingambit@Black Glasses, Excadrill+Sneasler, Kingambit+Whimsicott, Basculegion+Kingambit, Dragonite+Sneasler@Psychic Seed, Froslass+Raichu@Raichunite Y, Sneasler+Volcarona@Leftovers, Torkoal+Sneasler@Psychic Seed, Hydreigon+Indeedee, Dragonite+Sneasler@Focus Sash, Salamence+Gholdengo@Life Orb, Froslass+Rillaboom@Occa Berry, Froslass+Dragonite@Dragoninite, Froslass+Raichu, Raichu+Froslass@Froslassite, Kingambit+Sneasler, Charizard+Kingambit@Occa Berry, Basculegion+Rotom-Heat, Kingambit+Basculegion@Focus Sash, Salamence+Primarina@Life Orb, Sneasler+Corviknight@Leftovers, Sneasler+Charizard@Charizardite X, Sneasler+Incineroar@Rocky Helmet, Sneasler+Incineroar@Sitrus Berry, Gholdengo+Tyranitar@Tyranitarite, Dragonite+Kingambit@Chople Berry, Sneasler+Baxcalibur@Baxcalibrite, Pelipper+Indeedee@Focus Sash, Glimmora+Kingambit@Chople Berry, Salamence+Volcarona@Sitrus Berry, Basculegion+Sneasler@Psychic Seed, Excadrill+Gholdengo@Life Orb, Tyranitar+Gholdengo@Life Orb, Rillaboom+Kingambit@Life Orb, Salamence+Gholdengo@Leftovers, Milotic+Salamence@Salamencite, Milotic+Salamence, Gholdengo+Excadrill@Focus Sash, Primarina+Salamence@Salamencite, Salamence+Incineroar@Rocky Helmet, Primarina+Salamence, Metagross+Sneasler@White Herb, Kingambit+Armarouge@Life Orb, Salamence+Rillaboom@Miracle Seed, Kingambit+Sinistcha, Salamence+Rillaboom@Leftovers, Sneasler+Sinistcha@Colbur Berry, Salamence+Sneasler@Psychic Seed, Delphox+Kingambit@Chople Berry, Rillaboom+Salamence@Salamencite, Tyranitar+Armarouge@Life Orb, Rillaboom+Salamence, Kommo-o+Kingambit@Chople Berry, Metagross+Sneasler, Garchomp+Scovillain@Scovillainite, Corviknight+Delphox@Delphoxite, Sneasler+Garchomp@Garchompite Z, Sneasler+Metagross@Metagrossite, Sneasler+Sylveon@Life Orb, Glimmora+Indeedee, Blastoise+Kingambit@Chople Berry, Sneasler+Armarouge@Life Orb, Excadrill+Armarouge@Life Orb, Gengar+Kingambit@Black Glasses, Sneasler+Basculegion@Life Orb, Blastoise+Kingambit, Gholdengo+Tyranitar, Salamence+Kingambit@Occa Berry, Froslass+Primarina, Primarina+Froslass@Froslassite, Excadrill+Gholdengo, Mamoswine+Rillaboom, Rillaboom+Mamoswine@Focus Sash
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Sneasler | 79.3% | seed-unburden 56.9%, fake-out 44.1%, speed-drop 15.3%, ally-boost 11.7%, quick-guard 5.7%, priority-blocker 5.7%, setup 3.8%, disruption 0.4%, pivot 0.2%, weather-setter 0.1% |
| Salamence | 72.1% | intimidate 99.9%, mega-attacker 98.8%, tailwind 73.0%, setup 0.6%, weather-setter 0.2%, helping-hand 0.1% |
| Tyranitar | 42.1% | weather-setter 99.7%, mega-attacker 88.1%, setup 10.7%, trick-room-abuser 1.2%, speed-drop 0.6%, disruption 0.3% |
| Kingambit | 38.7% | priority-attack 99.3%, setup 26.0%, trick-room-abuser 18.2%, speed-drop 0.5% |
| Excadrill | 37.6% | setup 3.7%, speed-drop 2.5%, mega-attacker 1.5% |
| Indeedee | 29.0% | terrain-setter 100.0%, priority-blocker 100.0%, spa-drop 65.3%, disruption 27.4%, trick-room-setter 27.4%, helping-hand 9.9%, fake-out 2.3%, setup 0.4% |
| Corviknight | 13.2% | setup 90.7%, tailwind 23.5%, disruption 1.8% |
| Froslass | 12.2% | weather-setter 100.0%, mega-attacker 95.0%, screens 91.0%, speed-drop 2.6%, setup 1.9%, disruption 0.9%, status 0.7% |
| Rotom-Heat | 1.9% | pivot 53.9%, speed-drop 47.9%, status 35.7%, screens 6.1%, setup 4.3% |
| Scovillain | 1.8% | rage-powder 100.0%, mega-attacker 83.1%, trick-room-abuser 12.3%, helping-hand 5.1%, priority-attack 3.6% |
| Lycanroc-Dusk | 1.5% | priority-attack 100.0% |
| Chandelure | 0.7% | trick-room-setter 45.4%, mega-attacker 41.3%, spa-drop 9.4%, disruption 5.7% |
| Mamoswine | 0.5% | priority-attack 100.0% |
| Zoroark-Hisui | 0.4% | speed-drop 70.7%, disruption 28.4%, pivot 13.3%, spa-drop 9.4% |
| Sceptile | 0.2% | mega-attacker 100.0% |
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
  - [Mark Mullender, 24th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0404/teamlist)
  - [Mateus De Moura Pimentel, 243rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0113/teamlist)
  - [Oguz Salbacak, 259th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0947/teamlist)
  - [Akram Hamdi, 684th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0852/teamlist)
  - [Lennard Messerschmidt, 914th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0612/teamlist)
  - [Koen van Cann, 9th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0655/teamlist)
  - [Koen van Cann, 9th, 27 Sep 2026](https://pokepast.es/7e97a19e8093f20b)
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
  - [Hyeonseung Lee, 975th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0872/teamlist)
  - [Yan Yuen, 669th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0967/teamlist)
  - [Blake Silver, 204th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0318/teamlist)
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
  - [Joseph Eckhart, 100th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0904/teamlist)
  - [Nick Donato, 434th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0563/teamlist)
  - [Nate Curl, 745th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0608/teamlist)
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
  - [Ria Shanmugam, 354th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1072/teamlist)
  - [Violet Mendez, 873rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0734/teamlist)
  - [Q, , 10 Sep 2026](https://pokepast.es/4ed17394f6517aaf)
  - [Marcus Fussell, 955th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0838/teamlist)
  - [Joshua Ketz, 1051st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0761/teamlist)
  - [Ethan Tyssen, 726th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0402/teamlist)
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
  - [Alex Moreno, 44th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0605/teamlist)
  - [Xavier Vazquez Ripoll, 689th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0264/teamlist)
  - [Alexander Rassael, 520th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0078/teamlist)
  - [Jacob Zlotnitsky, 736th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0104/teamlist)
  - [Daniel Jensen, 811th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0440/teamlist)
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
  - [Omar ZIYANI, 681st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0426/teamlist)
  - [Jack Sturmer, 311th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0253/teamlist)
  - [zerosiki0909, , 27 Sep 2026](https://pokepast.es/1916aa3a980dc964)
  - [Lorenzo D'Ambrosio, 251st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0934/teamlist)
  - [Daniel Martinez Camacho, 499th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0067/teamlist)
  - [Ivan Martinez, 1054th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0099/teamlist)
  - [Nicolas Colella, 430th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0491/teamlist)
  - [David Markin, 960th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0693/teamlist)
  - [Fletcher Dean, 815th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1033/teamlist)
  - [Jules Büchler-Lecler, 895th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0161/teamlist)
  - [Jonathan Martin, 30th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0320/teamlist)
  - [Cayden Owens, 31st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1059/teamlist)
  - [Jacob Youn, 85th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0612/teamlist)
  - [Ethan Regnart, 297th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0056/teamlist)
  - [Yang Yuhao, 496th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0760/teamlist)
  - [Taylor Sprouse, 1014th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0392/teamlist)
  - [PhoNoodle, , 10 Sep 2026](https://pokepast.es/81d3f0ffe6ef750c)
  - [Chirantan Joshi, 570th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0286/teamlist)
  - [Adam Warren, 1056th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0657/teamlist)
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
  - [Joshua Lorcy, 55th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0531/teamlist)
  - [Ben Wolf, 346th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1048/teamlist)
  - [Ates Serifsoy, 563rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0411/teamlist)
  - [Justin Tang, 12th, 21 Sep 2026](https://pokepast.es/753cf48da749168a)
  - [Justin Tang, Top 16, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/urXCEsyr6l208Bg4tX4o)
  - [Bo Quel, 1043rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0753/teamlist)
  - [Daniel Anselm, 537th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0090/teamlist)
  - [Luca Santelli, 411th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0462/teamlist)
  - [zerosiki0909, , 12 Sep 2026](https://pokepast.es/a719c9e52c55cc43)
  - [Yannick Mach, 874th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0166/teamlist)
  - [Lorenzo Pugliese, 624th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1058/teamlist)
  - [Pascal Weih, 1111th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0550/teamlist)
  - [tempo777, , 10 Sep 2026](https://pokepast.es/9fed7bfc061ad8bb)
  - [Seowon Kim, , 9 Sep 2026](https://pokepast.es/6f1b0e8df51cc57d)
  - [Zhiyuan Zhang, 587th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0985/teamlist)
  - [Uch, , 9 Sep 2026](https://pokepast.es/33b3042210ffc178)
  - [Roman Grcic, 239th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0186/teamlist)
  - [Aren Moy, 764th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0829/teamlist)
  - [Patou Makkinje, 1082nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0007/teamlist)
  - [Brett Saguid, 708th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0667/teamlist)
  - [Leo Kutschki, 904th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0536/teamlist)
  - [Alex Greenawalt, 463rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0211/teamlist)
  - [Alfredo Chang-Gonzalez, 10th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0118/teamlist)
  - [Nicholas Kan, 37th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0212/teamlist)
  - [Anna Aurelia, 776th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0677/teamlist)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/3ad6655b53208446)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/7fb12fe08230d7be)
  - [Andrew Jenkins, 264th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0464/teamlist)
  - [Patrick Heinicke, 490th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0442/teamlist)
  - [Matthew Henry, 5th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0085/teamlist)
  - [Saelyn Turner, 985th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0421/teamlist)
  - [YUWUNA, , 10 Sep 2026](https://pokepast.es/595b9623f782bb8f)
  - [Cosmo, , 10 Sep 2026](https://pokepast.es/a8fc4cfab65cd411)
  - [Matthew Kendall, 988th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0659/teamlist)
  - [Ben Grissmer, 197th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0445/teamlist)
  - [Matt Bruno, 426th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0288/teamlist)
  - [Shoma Honami, , 11 Sep 2026](https://pokepast.es/199e4fdf392315b2)
  - [Bas Bauer, 748th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0127/teamlist)
  - [Alexander Balic, 1112th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0379/teamlist)
  - [Ping, , 10 Sep 2026](https://pokepast.es/bed443f8a0acbe11)
  - [Gustavo, , 10 Sep 2026](https://pokepast.es/e2bab80c4e53d89a)
  - [Erik Holmstrom, 340th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0156/teamlist)
  - [Jonathan Tran, 565th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0535/teamlist)
  - [Sarina Compagnino, 474th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0866/teamlist)
  - [reydegonnet, , 10 Sep 2026](https://pokepast.es/016cd16a7d929d4f)
  - [Oliver Marek, 546th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0990/teamlist)
  - [Joe Carlino, , 10 Sep 2026](https://pokepast.es/1e6a03dc1764214e)
  - [koyuki, , 9 Sep 2026](https://pokepast.es/8f4c2600a4a63a90)
  - [Alejandro Rodríguez Revidiego, 223rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0017/teamlist)
  - [Luis Medina, 388th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0040/teamlist)
  - [Matthew Neeson, 775th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0024/teamlist)
  - [Bruce Bermel, 814th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0132/teamlist)
  - [Adam Noschese, 454th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0243/teamlist)
  - [Jonas Birarda, 307th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0794/teamlist)
  - [Caleb Floyd, 867th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0935/teamlist)
  - [Cole Basham, Top 8, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/rD6PrJBrCfzicynLnRAI)
  - [Sam Badenach, 149th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0011/teamlist)
  - [Alexander Moor, 285th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0581/teamlist)
  - [Richard Wan, 433rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0879/teamlist)
  - [Ignacio Marquez Albes, 81st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0032/teamlist)
  - [Sam Tabner, 261st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0294/teamlist)
  - [Hsuan-Chih Kuo, 424th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0826/teamlist)
  - [Thomas Cooleen, 33rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0652/teamlist)
  - [Tobias Gleixner, 864th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0057/teamlist)
  - [Pablo Carro, 548th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0385/teamlist)
  - [Aitor Otegi, 870th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0428/teamlist)
  - [Adnan Mohammed, 861st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0941/teamlist)
  - [vi0ra_pokemon, , 14 Sep 2026](https://pokepast.es/74aea655c757917f)
  - [Christopher Epps, 732nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0150/teamlist)
  - [Stefano Greppi, , 15 Sep 2026](https://pokepast.es/045cc1b2f27f9315)
  - [David Herms, 1077th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0038/teamlist)
  - [Michał Chyra, 187th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1010/teamlist)
  - [Stefan Brandt, 405th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0108/teamlist)
  - [Xingjian Mao, 377th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0487/teamlist)
  - [Ben Kirch, 331st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0679/teamlist)
  - [Eclair_EqualAir, Top 4, 12 Sep 2026](https://pokepast.es/76a971cc5c997172)
  - [Kiernan Maloney, 851st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0331/teamlist)
  - [Anton Meßner, 454th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1078/teamlist)
  - [Jeffrey Lehmann, 986th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0461/teamlist)
  - [John Polzin, 1071st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0093/teamlist)
  - [Ben Alexander, 265th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0027/teamlist)
  - [Chris Santalis, 101st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0613/teamlist)
  - [Jean-Ulysses Serrano Albuerne, 807th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0705/teamlist)
  - [Edward Chan, 704th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0221/teamlist)
  - [Marius Wels, 274th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0569/teamlist)
  - [Ethan Lam, 345th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0988/teamlist)
  - [Alexander Baumann, 668th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0230/teamlist)
  - [Jonas Sørensen, 436th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0614/teamlist)
  - [Maia Merriman, 622nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0575/teamlist)
  - [Albert Tagliaferri, 716th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0874/teamlist)
  - [Jake Tagliaferri, 763rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0983/teamlist)
  - [Nikolai Herrmann, 629th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0854/teamlist)
  - [Chu Jian Hua, 690th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0803/teamlist)
  - [Andres Wilkerson, 13th, 21 Sep 2026](https://pokepast.es/e97cb94d7e8d7678)
  - [Andres Wilkerson, Top 16, 20 Sep 2026](https://rk9.gg/teamlist/public/BA002-JL3KVbvivVKNAc/bASM2pmAEMjJYJ4ja7yI)
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
  - [Paschalis Dermentzis, 20th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0846/teamlist)
  - [Lazaros Lazaropoulos, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0813/teamlist)
  - [Charalampos Frimas, 185th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0069/teamlist)
  - [Florian Hoffmann, 174th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0702/teamlist)
  - [Simon Carmichael, 177th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0020/teamlist)
  - [Jannek Brödling, 375th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0542/teamlist)
  - [Raphael Stallhofer, 244th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0688/teamlist)
  - [Mantrel Whitaker, 239th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0932/teamlist)
  - [Malcolm Nolasco, 1053rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0647/teamlist)
  - [Megumi Natsu, , 10 Sep 2026](https://pokepast.es/d8faaf4f72f6aab5)
  - [Jason Stewart, 619th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0732/teamlist)
  - [Nicholas Johnson, , 12 Sep 2026](https://pokepast.es/8bc91085c2883385)
  - [Patrick Gabbett, 662nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0740/teamlist)
  - [Dylan Morgan, 711th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0369/teamlist)
  - [Alex Hikmat, 759th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0183/teamlist)
  - [Eirik Ødegård, 530th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0932/teamlist)
  - [Mark Vestbo Olsen, 1093rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0052/teamlist)
  - [Nathan Soo, 286th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0003/teamlist)
  - [Julian Hernandez-Perocier, 418th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0978/teamlist)
  - [tanukiaieo, , 10 Sep 2026](https://pokepast.es/b9b3d0ee2ee1b26a)
  - [DJSanders, , 23 Sep 2026](https://pokepast.es/0fd7f8e614d0b6c5)
  - [Christopher Edwards, 718th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1016/teamlist)
  - [Joan Garcia, 472nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1070/teamlist)
  - [Corey Okonowitz, 98th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0354/teamlist)
  - [Eric Dolan, 769th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0559/teamlist)
  - [Kathryn Aplin, 646th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1079/teamlist)
  - [Matthew Herndon, 999th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0094/teamlist)
  - [David Ibeneme, 978th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0468/teamlist)
  - [Andrew Krebs, Top 4, 13 Sep 2026](https://pokepast.es/a4768a9bbf6876de)
  - [Dane Bodamer, 863rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0475/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/81427a109e744097)
  - [Stanisław Piotrowski, 198th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0850/teamlist)
  - [Jude Gerard Lee Wei Cong, 3rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0040/teamlist)
  - [Jude Lee, 3rd, 27 Sep 2026](https://pokepast.es/32857b1c3f3763e0)
  - [tchagelado, , 13 Sep 2026](https://pokepast.es/369e75b64155b6a1)
  - [Santino Tarquinio, 173rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0204/teamlist)
  - [Thaddeus Valentine, 258th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0727/teamlist)
  - [Amy White, 729th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0653/teamlist)
  - [horsea_tatsuomi, , 12 Sep 2026](https://pokepast.es/e328f6becb7a8d35)
  - [Thomas Duggan, 306th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0138/teamlist)
  - [Motochika Nabeshima, , 9 Sep 2026](https://pokepast.es/5268ba8166d67ebf)
  - [Will Inabinet, 189th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1075/teamlist)
  - [Zoe Anderson, 1068th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0194/teamlist)
  - [Juan Francisco Alcaraz, 106th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0870/teamlist)
  - [Roi Gómez García, 1037th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0268/teamlist)
  - [Jon Huntley, 650th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0087/teamlist)
  - [Pasty, , 22 Sep 2026](https://pokepast.es/bc36f6f2b942701f)
  - [Pasty, , 10 Sep 2026](https://pokepast.es/9c4f7915b1d3ae07)
  - [Eduardo Araújo, 1049th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0189/teamlist)
  - [Grant Rohlfing, 760th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0195/teamlist)
  - [sectoniaservant, , 11 Sep 2026](https://pokepast.es/c8f60c5168bd6a83)
  - [itsplebian, , 22 Sep 2026](https://pokepast.es/e2b7674a54debee7)
  - [Ian Kormos, 36th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0783/teamlist)
  - [Daniel Soler Sanchez, 21st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0546/teamlist)
  - [Dani Pardo, 834th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0493/teamlist)
  - [Ling, , 10 Sep 2026](https://pokepast.es/900d357c0ea74c31)
  - [Brian Compere, 367th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0663/teamlist)
  - [Shohei Kimura, , 12 Sep 2026](https://pokepast.es/8ef29e6c905c1699)
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
  - [Kamal Saab, 752nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1074/teamlist)
  - [Andrew Wilson, 29th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1030/teamlist)
  - [Frank Kovacs, 457th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0536/teamlist)
  - [KingYabber, , 10 Sep 2026](https://pokepast.es/6c092ddbec51ef61)
  - [Hiroto Kamazawa, , 21 Sep 2026](https://pokepast.es/652a6122d64aa2c1)
  - [Rudra Kansara, 1000th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0651/teamlist)
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
  - [Jonathan Todd, 730th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1008/teamlist)
  - [Thomas Dervan, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0256/teamlist)
  - [Spencer Verdoni, 500th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0691/teamlist)
  - [DjSanders, , 13 Sep 2026](https://pokepast.es/ad11cad6ec6b68f7)
  - [Salvatore Maira, 597th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0626/teamlist)
  - [Aediion_, , 11 Sep 2026](https://pokepast.es/94cb88bc739ff2af)
  - [Daniel Harris, 975th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0775/teamlist)
  - [Maxwell Richards, 483rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0635/teamlist)
  - [Maximilian Seitz, 1061st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0421/teamlist)
  - [joserockzvgc, , 16 Sep 2026](https://pokepast.es/31945882c8d00260)
  - [Tyler Deacy, 127th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0188/teamlist)
  - [Kevin Holzmann, 807th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0067/teamlist)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/36cd120de5ec394c)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/134747480705edc0)
  - [Tukibiya_Moeru, , 11 Sep 2026](https://pokepast.es/78f9ef4e502c61d7)
  - [Raphael But, 1088th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0168/teamlist)
  - [Tim Beyreuther, 621st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0731/teamlist)
  - [etc25269248, , 13 Sep 2026](https://pokepast.es/7664efb6099c8d98)
  - [Benjamin Dean, 281st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0059/teamlist)
  - [Daniel Boyer, 542nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0844/teamlist)
  - [Michael Kelsch, , 16 Sep 2026](https://pokepast.es/b3e468f45c3c9ebb)
  - [Apollo Hageman, 275th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0360/teamlist)
  - [Dominic Plume, 652nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0349/teamlist)
  - [Rod, , 15 Sep 2026](https://pokepast.es/18d8e22d3d9ad026)
  - [Michael Anastasio, 709th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0335/teamlist)
  - [Jermaine Mcleod, , 10 Sep 2026](https://pokepast.es/b54467e1aa4327e1)
  - [Dorian Luckie, 802nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0459/teamlist)
  - [Magnus Wallgren, 638th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0185/teamlist)
  - [albin jepping, 817th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0211/teamlist)
  - [toshiniki, , 18 Sep 2026](https://pokepast.es/93b626e0c928be89)
  - [Lily Ellsasser, 280th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0502/teamlist)
  - [Matin Moradi, 66th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0084/teamlist)
  - [Alyssa Smith, 374th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0749/teamlist)
  - [Xena, , 9 Sep 2026](https://pokepast.es/666b7365ce0b35f6)
  - [Devlin Ursu, 456th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0987/teamlist)
  - [Astrid Gurski, 983rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0809/teamlist)
  - [Germán Francisco Tenza Rubio, 270th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0641/teamlist)
  - [Murphy Hartzenberg, 135th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0001/teamlist)
  - [homura_kurenai_, , 10 Sep 2026](https://pokepast.es/7f30345697a16032)
  - [Daniel Schäfer, 632nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0457/teamlist)
  - [Trista Medine, 404th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1026/teamlist)
  - [axolodyl, , 10 Sep 2026](https://pokepast.es/37cf6b6b304aefd6)
  - [Víctor Medina, 29th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1013/teamlist)
  - [David Peralta Bozada, 145th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0080/teamlist)
  - [Carlos Cabal, 148th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0573/teamlist)
  - [Samuel Pereira, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0995/teamlist)
  - [hattington360, , 10 Sep 2026](https://pokepast.es/0f1752f2ba99c09f)
  - [Jack Presland, 123rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0078/teamlist)
  - [Rani De Schoenmacker, 740th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0150/teamlist)
  - [Matteo Paviza, 877th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0022/teamlist)
  - [Tyler Norton, 546th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0458/teamlist)
  - [Gabe Baum, 53rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0364/teamlist)
  - [Yuma Kinugawa, , 12 Sep 2026](https://pokepast.es/348d665e808bb457)
  - [William Clements, 1071st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0123/teamlist)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)
  - [FranDrawer03, , 11 Sep 2026](https://pokepast.es/961f0667b90b830c)
  - [Tristan Brissette, 826th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0238/teamlist)
  - [Colin Cain, 847th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0205/teamlist)
  - [Di Smith, 1017th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0717/teamlist)
  - [Ko Tsukide, , 23 Sep 2026](https://pokepast.es/175ee651eef60e92)
  - [Nicholas Woodhouse, 226th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0292/teamlist)
  - [Amethyst Leine, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0362/teamlist)
  - [clark smith, 1011th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0116/teamlist)
  - [Dom Mori, 757th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0006/teamlist)
  - [Thomas Schultz, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1054/teamlist)
  - [Felix Althaus, 994th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0812/teamlist)
  - [Daniel walker, 6th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0315/teamlist)
  - [Mark Cotter, 542nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0747/teamlist)
  - [Sam Sperl, 117th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0873/teamlist)
  - [Jeremiah Paul, 212th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0587/teamlist)
  - [kayato_vgc, , 18 Sep 2026](https://pokepast.es/cea79bfa59e2fd6b)
  - [Damahni Palmer, 27th, 21 Sep 2026](https://pokepast.es/9e422cab36495fcb)
  - [Giovanni Piscitelli, Top 8, 20 Sep 2026](https://pokepast.es/ffe1c04c186b2453)
  - [Luke Owen, 242nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0593/teamlist)
  - [Steven Van, 495th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0200/teamlist)
  - [Demitrios Kaguras, 220th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1006/teamlist)
  - [Basil Hawley, 228th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0551/teamlist)
  - [Morgan Carter, 900th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1021/teamlist)
  - [Heinz Heckmann, 154th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0375/teamlist)
  - [H1VGC, 154th, 27 Sep 2026](https://pokepast.es/80bf9f59ac2d2460)
  - [Noah Sim, 1030th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0579/teamlist)
  - [Jobe McDermott, 251st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0155/teamlist)
  - [Jason Hookens, 138th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0173/teamlist)
  - [Elias Pacheco Coelho, 626th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0256/teamlist)
  - [Marcos Perez, 906th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0210/teamlist)
  - [Alejandro, , 12 Sep 2026](https://pokepast.es/12c7b27a8e53ed9c)
  - [Ramon Schong, 569th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0386/teamlist)
  - [Kevin Hagen, 678th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0351/teamlist)
  - [Hendrik Förster, 700th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0922/teamlist)
  - [Sebastian Abenza Homberger, 711th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0285/teamlist)
  - [Eliseo Torres-Morales, 1036th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0353/teamlist)
  - [PiyoLily145, , 9 Sep 2026](https://pokepast.es/8e37c3b00cbba6b6)
  - [Joshua Moloney, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0143/teamlist)
  - [Evan Schulz, 1013th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0394/teamlist)
  - [Antonio Galotta, 121st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0798/teamlist)
  - [Christopher Gibson, 21st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0275/teamlist)
  - [BlazeG1798, 21st, 27 Sep 2026](https://pokepast.es/856c378ecfafa498)
  - [Michael Mullen, 666th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0282/teamlist)
  - [Joshua Flickinger, 838th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0567/teamlist)
  - [Seth Ellsworth, 586th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0058/teamlist)
  - [Niklas Hauser, 398th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0075/teamlist)
  - [Darcy Willis, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0254/teamlist)
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
  - [Max Hofmann, 954th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1089/teamlist)
  - [Yuta Ishigaki, , 10 Sep 2026](https://pokepast.es/f5b17f03de49b848)
  - [Noah Gelman, 706th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0418/teamlist)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Daniel Medina, 574th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0223/teamlist)
  - [pathogenvgc, , 14 Sep 2026](https://pokepast.es/a630a7a5018325c9)
  - [Castorbrown, , 13 Sep 2026](https://pokepast.es/93c86d303e85d1da)
  - [Eliana Stevens, 326th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0222/teamlist)

- Sub-community pass: 597 primary teams, 139 tokens, modularity 0.45; unconnected tokens: Tyranitar (other item; Chople Berry 7/13), Hydreigon@Choice Scarf, Gengar@Gengarite, Absol@Absolite Z, Lucario@Lucarionite Z, Tsareena (other item; Wide Lens 2/5), Typhlosion-Hisui@Choice Scarf, Archaludon (other item; Leftovers 3/4), Baxcalibur (other item; Life Orb 2/4), Delphox (other item; Life Orb 3/4), Dragapult (other item; Life Orb 3/4), Maushold (other item; Chople Berry 1/4), Alakazam (other item; Alakazite 3/3), Annihilape (other item; Choice Scarf 2/3), Ceruledge (other item; Focus Sash 2/3), Chandelure (other item; Chandelurite 1/3), Kleavor (other item; Choice Scarf 2/3), Mamoswine (other item; Focus Sash 3/3), Meganium (other item; Meganiumite 3/3), Steelix (other item; Steelixite 3/3), Torterra (other item; Life Orb 3/3), Venusaur (other item; Life Orb 1/3), Zoroark-Hisui (other item; Choice Scarf 2/3); unassigned within the community: 6 teams (1.2% of its primary weight); hybrid teams of the community left out: 464
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 3 (0.64) | 3 (0.54) | 3 (0.45) | 5 (0.37) | 7 (0.30) |

#### Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) (242 primary teams, 115 distinct builds, top pair on 201/242)
- Megas on member teams: Tyranitar 216, Salamence 193, Staraptor 9, Garchomp-Z 6, Golisopod 6, Froslass 5
- Top species by team share: Tyranitar 92%, Excadrill 86%, Salamence 80%, Sneasler 62%, Milotic 46%, Indeedee 44%
- Token label: Tyranitar@Tyranitarite / Excadrill / Salamence@Salamencite
- Mode tags on primary teams: Sand 222, Tailwind 131, Psyspam 119, Setup 27, Trick Room 11, Snow 6, Screens 3, Sun 1
- Primary teams: 242 (42.2% of the community's primary weight), hybrid teams: 36 (6.6%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Sinistcha (other item; Colbur Berry 11/28)+Staraptor@Staraptite, Corviknight (other item; Leftovers 15/19)+Garchomp@Garchompite Z, Corviknight@Psychic Seed+Indeedee@Choice Scarf, Gholdengo (other item; Life Orb 101/110)+Gardevoir@Gardevoirite, Indeedee@Choice Scarf+Metagross@Metagrossite, Excadrill (other item; Focus Sash 198/215)+Corviknight@Psychic Seed, Sneasler (other item; White Herb 158/207)+Corviknight@Psychic Seed, Corviknight@Psychic Seed+Tyranitar@Tyranitarite, Incineroar (other item; Sitrus Berry 65/78)+Sinistcha (other item; Colbur Berry 11/28), Milotic (other item; Sitrus Berry 81/154)+Metagross@Metagrossite, Excadrill (other item; Focus Sash 198/215)+Tyranitar@Tyranitarite, Excadrill (other item; Focus Sash 198/215)+Staraptor@Staraptite, Indeedee@Choice Scarf+Sneasler@Psychic Seed, Sneasler (other item; White Herb 158/207)+Scovillain@Scovillainite, Gholdengo (other item; Life Orb 101/110)+Staraptor@Staraptite, Gholdengo (other item; Life Orb 101/110)+Tyranitar@Tyranitarite, Gholdengo (other item; Life Orb 101/110)+Milotic (other item; Sitrus Berry 81/154), Corviknight (other item; Leftovers 15/19)+Tyranitar@Tyranitarite, Sneasler (other item; White Herb 158/207)+Garchomp@Choice Scarf, Corviknight (other item; Leftovers 15/19)+Indeedee@Choice Scarf, Excadrill (other item; Focus Sash 198/215)+Tyranitar@Choice Scarf, Sinistcha (other item; Colbur Berry 11/28)+Tyranitar@Tyranitarite, Excadrill (other item; Focus Sash 198/215)+Gholdengo (other item; Life Orb 101/110), Talonflame (other item; Expert Belt 2/7)+Tyranitar@Tyranitarite, Indeedee@Choice Scarf+Tyranitar@Tyranitarite, Excadrill (other item; Focus Sash 198/215)+Indeedee@Choice Scarf, Milotic (other item; Sitrus Berry 81/154)+Staraptor@Staraptite, Lycanroc-Dusk (other item; Focus Sash 8/8)+Sneasler (other item; White Herb 158/207), Corviknight (other item; Leftovers 15/19)+Excadrill (other item; Focus Sash 198/215), Excadrill (other item; Focus Sash 198/215)+Indeedee-F@Psychic Seed, Excadrill (other item; Focus Sash 198/215)+Volcarona@Grassy Seed, Milotic (other item; Sitrus Berry 81/154)+Tyranitar@Tyranitarite, Indeedee-F@Psychic Seed+Tyranitar@Tyranitarite, Milotic (other item; Sitrus Berry 81/154)+Sinistcha (other item; Colbur Berry 11/28), Excadrill (other item; Focus Sash 198/215)+Milotic (other item; Sitrus Berry 81/154), Staraptor@Staraptite+Tyranitar@Tyranitarite, Sneasler (other item; White Herb 158/207)+Indeedee-F@Psychic Seed, Milotic (other item; Sitrus Berry 81/154)+Sneasler@Psychic Seed, Excadrill (other item; Focus Sash 198/215)+Rotom-Heat (other item; Sitrus Berry 4/10), Rotom-Heat (other item; Sitrus Berry 4/10)+Tyranitar@Tyranitarite, Gholdengo (other item; Life Orb 101/110)+Sinistcha (other item; Colbur Berry 11/28), Excadrill (other item; Focus Sash 198/215)+Sinistcha (other item; Colbur Berry 11/28), Gholdengo (other item; Life Orb 101/110)+Primarina (other item; Life Orb 7/15), Corviknight (other item; Leftovers 15/19)+Sneasler@Psychic Seed, Sneasler (other item; White Herb 158/207)+Golisopod@Golisopite, Sneasler (other item; White Herb 158/207)+Basculegion@Choice Scarf, Gholdengo (other item; Life Orb 101/110)+Indeedee-F (other item; Rocky Helmet 24/56), Arcanine-Hisui (other item; Focus Sash 96/99)+Milotic (other item; Sitrus Berry 81/154), Sneasler (other item; White Herb 158/207)+Raichu@Raichunite Y, Sneasler (other item; White Herb 158/207)+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 96/99)+Indeedee@Choice Scarf, Sneasler (other item; White Herb 158/207)+Volcarona (other item; Sitrus Berry 7/18), Corviknight@Psychic Seed+Salamence@Salamencite, Tyranitar@Tyranitarite+Volcarona@Grassy Seed, Milotic (other item; Sitrus Berry 81/154)+Indeedee@Choice Scarf, Sneasler (other item; White Herb 158/207)+Indeedee@Choice Scarf, Salamence@Salamencite+Volcarona@Grassy Seed, Sneasler (other item; White Herb 158/207)+Dragonite@Dragoninite, Farigiraf (other item; Sitrus Berry 20/29)+Sneasler (other item; White Herb 158/207), Gholdengo (other item; Life Orb 101/110)+Sneasler@Psychic Seed, Sneasler (other item; White Herb 158/207)+Garchomp@Garchompite Z, Basculegion (other item; Life Orb 72/97)+Sneasler (other item; White Herb 158/207), Gholdengo (other item; Life Orb 101/110)+Salamence@Salamencite, Indeedee@Choice Scarf+Salamence@Salamencite, Arcanine-Hisui (other item; Focus Sash 96/99)+Salamence@Salamencite, Salamence@Salamencite+Sneasler@Grassy Seed, Pelipper (other item; Focus Sash 5/7)+Salamence@Salamencite, Milotic (other item; Sitrus Berry 81/154)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 175/275)+Salamence@Salamencite, Excadrill (other item; Focus Sash 198/215)+Salamence@Salamencite, Floette-Eternal@Floettite+Salamence@Salamencite, Primarina (other item; Life Orb 7/15)+Salamence@Salamencite, Salamence@Salamencite+Tyranitar@Tyranitarite, Sylveon (other item; Fairy Feather 25/29)+Salamence@Salamencite, Metagross@Metagrossite+Salamence@Salamencite, Basculegion (other item; Life Orb 72/97)+Salamence@Salamencite, Armarouge (other item; Life Orb 13/23)+Salamence@Salamencite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Tyranitar@Tyranitarite | 89.9% |
| Excadrill (other item; Focus Sash 198/215) | 87.2% |
| Salamence@Salamencite | 78.8% |
| Milotic (other item; Sitrus Berry 81/154) | 43.0% |
| Indeedee@Choice Scarf | 41.8% |
| Sneasler (other item; White Herb 158/207) | 41.4% |
| Gholdengo (other item; Life Orb 101/110) | 36.7% |
| Corviknight@Psychic Seed | 22.7% |
| Sinistcha (other item; Colbur Berry 11/28) | 9.0% |
| Corviknight (other item; Leftovers 15/19) | 6.3% |
| Staraptor@Staraptite | 4.5% |
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

#### Community 1 / Sub-community 1: Kingambit / Rillaboom (250 primary teams, 159 distinct builds, top pair on 165/250)
- Megas on member teams: Salamence 179, Froslass 65, Floette 54, Delphox 20, Charizard-Y 17, Garchomp-Z 17
- Top species by team share: Sneasler 89%, Kingambit 85%, Rillaboom 79%, Salamence 72%, Basculegion 48%, Froslass 26%
- Token label: Kingambit / Rillaboom
- Mode tags on primary teams: Tailwind 173, Snow 70, Setup 29, Sun 22, Trick Room 15, Rain 5, Sand 4, Psyspam 4, Screens 1
- Primary teams: 250 (40.3% of the community's primary weight), hybrid teams: 11 (1.8%)
- Date range: 2026-09-09 to 2026-09-28
- Core pairs: Lycanroc-Dusk (other item; Focus Sash 8/8)+Scovillain@Scovillainite, Farigiraf (other item; Sitrus Berry 20/29)+Torkoal (other item; Charcoal 6/6), Pelipper (other item; Focus Sash 5/7)+Basculegion@Choice Scarf, Lycanroc-Dusk (other item; Focus Sash 8/8)+Froslass@Froslassite, Farigiraf (other item; Sitrus Berry 20/29)+Blaziken@Blazikenite, Froslass@Froslassite+Scovillain@Scovillainite, Pawmot (other item; Focus Sash 8/8)+Froslass@Froslassite, Basculegion@Choice Scarf+Golisopod@Golisopite, Corviknight (other item; Leftovers 15/19)+Garchomp@Garchompite Z, Basculegion (other item; Life Orb 72/97)+Lycanroc-Dusk (other item; Focus Sash 8/8), Basculegion (other item; Life Orb 72/97)+Scovillain@Scovillainite, Arcanine-Hisui (other item; Focus Sash 96/99)+Metagross@Metagrossite, Blaziken@Blazikenite+Froslass@Froslassite, Dragonite@Dragoninite+Froslass@Froslassite, Incineroar (other item; Sitrus Berry 65/78)+Floette-Eternal@Floettite, Basculegion@Choice Scarf+Garchomp@Garchompite Z, Basculegion (other item; Life Orb 72/97)+Dragonite@Dragoninite, Farigiraf (other item; Sitrus Berry 20/29)+Sylveon (other item; Fairy Feather 25/29), Kingambit (other item; Chople Berry 141/248)+Meowstic-F@Meowsticite, Volcarona (other item; Sitrus Berry 7/18)+Froslass@Froslassite, Garchomp@Garchompite Z+Metagross@Metagrossite, Garchomp (other item; Life Orb 9/13)+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 96/99)+Sneasler@Grassy Seed, Basculegion@Choice Scarf+Floette-Eternal@Floettite, Floette-Eternal@Floettite+Sneasler@Grassy Seed, Kingambit (other item; Chople Berry 141/248)+Blaziken@Blazikenite, Whimsicott (other item; Focus Sash 15/21)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 175/275)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 175/275)+Volcarona@Grassy Seed, Basculegion (other item; Life Orb 72/97)+Kingambit (other item; Chople Berry 141/248), Kingambit (other item; Chople Berry 141/248)+Lycanroc-Dusk (other item; Focus Sash 8/8), Kingambit (other item; Chople Berry 141/248)+Garchomp@Choice Scarf, Kingambit (other item; Chople Berry 141/248)+Baxcalibur@Baxcalibrite, Sneasler (other item; White Herb 158/207)+Scovillain@Scovillainite, Glimmora (other item; Focus Sash 5/5)+Kingambit (other item; Chople Berry 141/248), Kingambit (other item; Chople Berry 141/248)+Froslass@Froslassite, Kingambit (other item; Chople Berry 141/248)+Pawmot (other item; Focus Sash 8/8), Basculegion (other item; Life Orb 72/97)+Floette-Eternal@Floettite, Basculegion (other item; Life Orb 72/97)+Delphox@Delphoxite, Kingambit (other item; Chople Berry 141/248)+Scovillain@Scovillainite, Arcanine-Hisui (other item; Focus Sash 96/99)+Froslass@Froslassite, Basculegion (other item; Life Orb 72/97)+Sneasler@Grassy Seed, Kingambit (other item; Chople Berry 141/248)+Delphox@Delphoxite, Kingambit (other item; Chople Berry 141/248)+Blastoise@Blastoisinite, Rillaboom (other item; Miracle Seed 175/275)+Baxcalibur@Baxcalibrite, Kingambit (other item; Chople Berry 141/248)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 175/275)+Volcarona (other item; Sitrus Berry 7/18), Kingambit (other item; Chople Berry 141/248)+Vanilluxe (other item; Choice Scarf 4/5), Lycanroc-Dusk (other item; Focus Sash 8/8)+Sneasler (other item; White Herb 158/207), Kingambit (other item; Chople Berry 141/248)+Raichu@Raichunite Y, Kingambit (other item; Chople Berry 141/248)+Torkoal (other item; Charcoal 6/6), Froslass@Froslassite+Sneasler@Grassy Seed, Basculegion@Choice Scarf+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 96/99)+Basculegion (other item; Life Orb 72/97), Kingambit (other item; Chople Berry 141/248)+Glimmora@Glimmoranite, Kingambit (other item; Chople Berry 141/248)+Charizard@Charizardite Y, Rillaboom (other item; Miracle Seed 175/275)+Floette-Eternal@Floettite, Excadrill (other item; Focus Sash 198/215)+Volcarona@Grassy Seed, Charizard@Charizardite Y+Sneasler@Grassy Seed, Basculegion (other item; Life Orb 72/97)+Froslass@Froslassite, Delphox@Delphoxite+Sneasler@Grassy Seed, Basculegion (other item; Life Orb 72/97)+Whimsicott (other item; Focus Sash 15/21), Farigiraf (other item; Sitrus Berry 20/29)+Kingambit (other item; Chople Berry 141/248), Kingambit (other item; Chople Berry 141/248)+Floette-Eternal@Floettite, Garchomp (other item; Life Orb 9/13)+Kingambit (other item; Chople Berry 141/248), Kingambit (other item; Chople Berry 141/248)+Volcarona (other item; Sitrus Berry 7/18), Incineroar (other item; Sitrus Berry 65/78)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 175/275)+Blaziken@Blazikenite, Arcanine-Hisui (other item; Focus Sash 96/99)+Dragonite@Dragoninite, Kingambit (other item; Chople Berry 141/248)+Dragonite@Dragoninite, Arcanine-Hisui (other item; Focus Sash 96/99)+Sneasler@Psychic Seed, Kingambit (other item; Chople Berry 141/248)+Rillaboom (other item; Miracle Seed 175/275), Basculegion (other item; Life Orb 72/97)+Rillaboom (other item; Miracle Seed 175/275), Rillaboom (other item; Miracle Seed 175/275)+Delphox@Delphoxite, Basculegion (other item; Life Orb 72/97)+Sylveon (other item; Fairy Feather 25/29), Rillaboom (other item; Miracle Seed 175/275)+Basculegion@Choice Scarf, Garchomp (other item; Life Orb 9/13)+Sneasler@Grassy Seed, Basculegion (other item; Life Orb 72/97)+Volcarona (other item; Sitrus Berry 7/18), Garchomp@Garchompite Z+Sneasler@Psychic Seed, Froslass@Froslassite+Garchomp@Garchompite Z, Kingambit (other item; Chople Berry 141/248)+Whimsicott (other item; Focus Sash 15/21), Kingambit (other item; Chople Berry 141/248)+Sylveon (other item; Fairy Feather 25/29), Arcanine-Hisui (other item; Focus Sash 96/99)+Kingambit (other item; Chople Berry 141/248), Sneasler (other item; White Herb 158/207)+Basculegion@Choice Scarf, Kingambit (other item; Chople Berry 141/248)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 175/275)+Glimmora@Glimmoranite, Arcanine-Hisui (other item; Focus Sash 96/99)+Milotic (other item; Sitrus Berry 81/154), Rillaboom (other item; Miracle Seed 175/275)+Froslass@Froslassite, Sneasler (other item; White Herb 158/207)+Raichu@Raichunite Y, Sneasler (other item; White Herb 158/207)+Froslass@Froslassite, Arcanine-Hisui (other item; Focus Sash 96/99)+Indeedee@Choice Scarf, Sneasler (other item; White Herb 158/207)+Volcarona (other item; Sitrus Berry 7/18), Tyranitar@Tyranitarite+Volcarona@Grassy Seed, Sylveon (other item; Fairy Feather 25/29)+Sneasler@Grassy Seed, Farigiraf (other item; Sitrus Berry 20/29)+Incineroar (other item; Sitrus Berry 65/78), Salamence@Salamencite+Volcarona@Grassy Seed, Sneasler (other item; White Herb 158/207)+Dragonite@Dragoninite, Farigiraf (other item; Sitrus Berry 20/29)+Sneasler (other item; White Herb 158/207), Kingambit (other item; Chople Berry 141/248)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 175/275)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 65/78)+Basculegion@Choice Scarf, Sneasler (other item; White Herb 158/207)+Garchomp@Garchompite Z, Arcanine-Hisui (other item; Focus Sash 96/99)+Rillaboom (other item; Miracle Seed 175/275), Dragonite@Dragoninite+Sneasler@Psychic Seed, Basculegion (other item; Life Orb 72/97)+Sneasler (other item; White Herb 158/207), Volcarona (other item; Sitrus Berry 7/18)+Sneasler@Grassy Seed, Arcanine-Hisui (other item; Focus Sash 96/99)+Salamence@Salamencite, Salamence@Salamencite+Sneasler@Grassy Seed, Pelipper (other item; Focus Sash 5/7)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 175/275)+Salamence@Salamencite, Floette-Eternal@Floettite+Salamence@Salamencite, Basculegion (other item; Life Orb 72/97)+Salamence@Salamencite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Kingambit (other item; Chople Berry 141/248) | 84.6% |
| Rillaboom (other item; Miracle Seed 175/275) | 78.2% |
| Sneasler@Grassy Seed | 45.0% |
| Basculegion (other item; Life Orb 72/97) | 36.4% |
| Froslass@Froslassite | 26.6% |
| Arcanine-Hisui (other item; Focus Sash 96/99) | 25.7% |
| Floette-Eternal@Floettite | 18.8% |
| Basculegion@Choice Scarf | 10.1% |
| Farigiraf (other item; Sitrus Berry 20/29) | 10.0% |
| Delphox@Delphoxite | 9.4% |
| Garchomp@Garchompite Z | 7.0% |
| Volcarona (other item; Sitrus Berry 7/18) | 5.5% |
| Dragonite@Dragoninite | 4.8% |
| Whimsicott (other item; Focus Sash 15/21) | 4.6% |
| Blaziken@Blazikenite | 3.9% |
| Lycanroc-Dusk (other item; Focus Sash 8/8) | 3.7% |
| Scovillain@Scovillainite | 3.5% |
| Raichu@Raichunite Y | 3.3% |
| Glimmora@Glimmoranite | 2.9% |
| Pawmot (other item; Focus Sash 8/8) | 2.5% |
| Pelipper (other item; Focus Sash 5/7) | 2.1% |
| Volcarona@Grassy Seed | 2.0% |
| Glimmora (other item; Focus Sash 5/5) | 1.8% |
| Baxcalibur@Baxcalibrite | 1.7% |
| Torkoal (other item; Charcoal 6/6) | 1.5% |
| Blastoise@Blastoisinite | 1.5% |
| Ninetales-Alola@Choice Scarf | 0.8% |
| Vanilluxe (other item; Choice Scarf 4/5) | 0.7% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Rielly Chambers, 66th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0092/teamlist)
  - [Jarrod Rose, 182nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0044/teamlist)
  - [Adrian van Dijk, 717th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0180/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Maura Hazen, 398th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0237/teamlist)
  - [Amar Curic, 188th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0289/teamlist)
  - [Davide Carrer, 104th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0335/teamlist)
  - [Omar Trejo, 137th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0187/teamlist)
  - [Jaden Streber, 136th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0504/teamlist)
  - [Laura Craig, 968th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1087/teamlist)
  - [oshio_pokemon, , 11 Sep 2026](https://pokepast.es/667cb69f9c820c84)
  - [gastrodon, , 10 Sep 2026](https://pokepast.es/59b0a674b59141a2)
  - [Tyler Coady, 427th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0376/teamlist)
  - [Matt Francis, 325th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0955/teamlist)
  - [Brandon Ebert, 1029th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0909/teamlist)

#### Community 1 / Sub-community 2: Psyspam (Mega Salamence) (99 primary teams, 58 distinct builds, top pair on 34/99)
- Megas on member teams: Salamence 60, Metagross 34, Charizard-Y 22, Gardevoir 12, Meowstic-F 8, Garchomp-Z 7
- Top species by team share: Sneasler 97%, Salamence 61%, Indeedee 58%, Indeedee-F 36%, Metagross 35%, Arcanine-Hisui 31%
- Token label: Sneasler@Psychic Seed + Metagross@Metagrossite/Indeedee-F
- Mode tags on primary teams: Psyspam 83, Tailwind 43, Sun 24, Sand 11, Trick Room 8, Snow 7, Setup 5, Rain 3
- Primary teams: 99 (16.4% of the community's primary weight), hybrid teams: 12 (2.2%)
- Date range: 2026-09-10 to 2026-09-29
- Core pairs: Charizard@Charizardite Y+Meowstic-F@Meowsticite, Armarouge (other item; Life Orb 13/23)+Indeedee-F@Psychic Seed, Kommo-o (other item; Leftovers 12/16)+Charizard@Charizardite Y, Indeedee-F (other item; Rocky Helmet 24/56)+Gardevoir@Gardevoirite, Garchomp (other item; Life Orb 9/13)+Charizard@Charizardite Y, Indeedee (other item; Focus Sash 27/40)+Kommo-o (other item; Leftovers 12/16), Charizard@Charizardite Y+Garchomp@Choice Scarf, Armarouge (other item; Life Orb 13/23)+Indeedee-F (other item; Rocky Helmet 24/56), Incineroar (other item; Sitrus Berry 65/78)+Gardevoir@Gardevoirite, Basculegion@Choice Scarf+Golisopod@Golisopite, Indeedee-F (other item; Rocky Helmet 24/56)+Golisopod@Golisopite, Indeedee (other item; Focus Sash 27/40)+Charizard@Charizardite Y, Meowstic-F@Meowsticite+Sneasler@Psychic Seed, Sylveon (other item; Fairy Feather 25/29)+Charizard@Charizardite Y, Arcanine-Hisui (other item; Focus Sash 96/99)+Metagross@Metagrossite, Gardevoir@Gardevoirite+Sneasler@Psychic Seed, Gholdengo (other item; Life Orb 101/110)+Gardevoir@Gardevoirite, Metagross@Metagrossite+Sneasler@Psychic Seed, Indeedee (other item; Focus Sash 27/40)+Sneasler@Psychic Seed, Indeedee (other item; Focus Sash 27/40)+Sylveon (other item; Fairy Feather 25/29), Indeedee-F (other item; Rocky Helmet 24/56)+Sneasler@Psychic Seed, Incineroar (other item; Sitrus Berry 65/78)+Sylveon (other item; Fairy Feather 25/29), Incineroar (other item; Sitrus Berry 65/78)+Floette-Eternal@Floettite, Indeedee-F (other item; Rocky Helmet 24/56)+Kommo-o (other item; Leftovers 12/16), Indeedee@Choice Scarf+Metagross@Metagrossite, Farigiraf (other item; Sitrus Berry 20/29)+Sylveon (other item; Fairy Feather 25/29), Incineroar (other item; Sitrus Berry 65/78)+Sinistcha (other item; Colbur Berry 11/28), Milotic (other item; Sitrus Berry 81/154)+Metagross@Metagrossite, Kingambit (other item; Chople Berry 141/248)+Meowstic-F@Meowsticite, Kommo-o (other item; Leftovers 12/16)+Sneasler@Psychic Seed, Garchomp@Garchompite Z+Metagross@Metagrossite, Garchomp (other item; Life Orb 9/13)+Froslass@Froslassite, Indeedee@Choice Scarf+Sneasler@Psychic Seed, Kingambit (other item; Chople Berry 141/248)+Garchomp@Choice Scarf, Armarouge (other item; Life Orb 13/23)+Sneasler@Psychic Seed, Sneasler (other item; White Herb 158/207)+Garchomp@Choice Scarf, Charizard@Charizardite Y+Sneasler@Psychic Seed, Kingambit (other item; Chople Berry 141/248)+Charizard@Charizardite Y, Excadrill (other item; Focus Sash 198/215)+Indeedee-F@Psychic Seed, Indeedee-F@Psychic Seed+Tyranitar@Tyranitarite, Charizard@Charizardite Y+Sneasler@Grassy Seed, Garchomp (other item; Life Orb 9/13)+Kingambit (other item; Chople Berry 141/248), Sneasler (other item; White Herb 158/207)+Indeedee-F@Psychic Seed, Incineroar (other item; Sitrus Berry 65/78)+Sneasler@Grassy Seed, Milotic (other item; Sitrus Berry 81/154)+Sneasler@Psychic Seed, Arcanine-Hisui (other item; Focus Sash 96/99)+Sneasler@Psychic Seed, Basculegion (other item; Life Orb 72/97)+Sylveon (other item; Fairy Feather 25/29), Incineroar (other item; Sitrus Berry 65/78)+Indeedee-F (other item; Rocky Helmet 24/56), Garchomp (other item; Life Orb 9/13)+Sneasler@Grassy Seed, Garchomp@Garchompite Z+Sneasler@Psychic Seed, Corviknight (other item; Leftovers 15/19)+Sneasler@Psychic Seed, Kingambit (other item; Chople Berry 141/248)+Sylveon (other item; Fairy Feather 25/29), Sneasler (other item; White Herb 158/207)+Golisopod@Golisopite, Gholdengo (other item; Life Orb 101/110)+Indeedee-F (other item; Rocky Helmet 24/56), Sylveon (other item; Fairy Feather 25/29)+Sneasler@Grassy Seed, Farigiraf (other item; Sitrus Berry 20/29)+Incineroar (other item; Sitrus Berry 65/78), Rillaboom (other item; Miracle Seed 175/275)+Golisopod@Golisopite, Gholdengo (other item; Life Orb 101/110)+Sneasler@Psychic Seed, Incineroar (other item; Sitrus Berry 65/78)+Basculegion@Choice Scarf, Indeedee (other item; Focus Sash 27/40)+Metagross@Metagrossite, Dragonite@Dragoninite+Sneasler@Psychic Seed, Sylveon (other item; Fairy Feather 25/29)+Salamence@Salamencite, Metagross@Metagrossite+Salamence@Salamencite, Armarouge (other item; Life Orb 13/23)+Salamence@Salamencite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Sneasler@Psychic Seed | 88.7% |
| Metagross@Metagrossite | 38.1% |
| Indeedee-F (other item; Rocky Helmet 24/56) | 36.1% |
| Indeedee (other item; Focus Sash 27/40) | 24.1% |
| Charizard@Charizardite Y | 18.5% |
| Incineroar (other item; Sitrus Berry 65/78) | 14.5% |
| Armarouge (other item; Life Orb 13/23) | 13.5% |
| Gardevoir@Gardevoirite | 11.8% |
| Kommo-o (other item; Leftovers 12/16) | 10.5% |
| Sylveon (other item; Fairy Feather 25/29) | 7.1% |
| Meowstic-F@Meowsticite | 5.7% |
| Golisopod@Golisopite | 4.0% |
| Garchomp@Choice Scarf | 3.8% |
| Garchomp (other item; Life Orb 9/13) | 3.5% |
| Indeedee-F@Psychic Seed | 1.1% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Anthony Rodriguez, 833rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0013/teamlist)
  - [Wyatt McDonald, , 15 Sep 2026](https://pokepast.es/97edd96cf0de7f14)
  - [Alexander Solimene, 281st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0500/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
  - [Jeremy Shepherd, 198th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0090/teamlist)
  - [Perry Gallo, 924th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0071/teamlist)
  - [Daniel Cunningham, 851st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0240/teamlist)
  - [Kristopher Horsey, 758th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0800/teamlist)
  - [yozora_952, , 15 Sep 2026](https://pokepast.es/90f7ccc7ab5b3d0b)
  - [Christina Bacino, 773rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0596/teamlist)
  - [Liam Good, 263rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1037/teamlist)
  - [Kais Arjai, 946th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0678/teamlist)
  - [Lennex Drummond, 233rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0278/teamlist)
  - [Jairo Contreras, 327th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0215/teamlist)
  - [Lorenz Mirow, 789th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0565/teamlist)
  - [Dimitri Kuster, 605th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0755/teamlist)

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): none

#### Community 1 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Sneasler | Sneasler (other item; White Herb 158/207) | Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 207 | 1: 104 · 0: 90 · 2: 9 · unassigned: 4 |
| Sneasler | Sneasler@Psychic Seed | Sub-community 2: Psyspam (Mega Salamence) | 142 | 2: 87 · 0: 53 · 1: 2 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 1: Kingambit / Rillaboom | 125 | 1: 117 · 0: 8 · unassigned: 0 |
| Indeedee | Indeedee@Choice Scarf | Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 126 | 0: 97 · 2: 29 · unassigned: 0 |
| Indeedee | Indeedee (other item; Focus Sash 27/40) | Sub-community 2: Psyspam (Mega Salamence) | 40 | 2: 28 · 0: 10 · 1: 2 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 1: Kingambit / Rillaboom | 30 | 1: 17 · 2: 7 · 0: 6 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 9/13) | Sub-community 2: Psyspam (Mega Salamence) | 13 | 1: 6 · 2: 4 · 0: 2 · unassigned: 1 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 2: Psyspam (Mega Salamence) | 13 | 1: 9 · 2: 4 · unassigned: 0 |

### Community 2: Setup (Mega Floette)
- Token label: Incineroar + Floette-Eternal/Delphox
- Mode tags on primary teams: Setup 132, Tailwind 60, Sun 22, Trick Room 15, Snow 10, Sand 7, Psyspam 6, Perish Trap 4, Screens 4, Rain 3
- Megas on primary teams: Floette 155, Delphox 68, Garchomp-Z 37, Blastoise 23, Metagross 22, Salamence 22, Dragonite 20, Charizard-Y 18, Gengar 13, Lucario-Z 13, Absol-Z 5, Baxcalibur 5, Garchomp 5, Golisopod 5, Aerodactyl 3, Camerupt 3, Charizard-X 3, Froslass 3, Staraptor 3, Glimmora 2, Raichu-Y 2, Abomasnow 1, Ampharos 1, Chandelure 1, Golurk 1, Lopunny 1, Mawile 1, Raichu-X 1, Scovillain 1, Tyranitar 1
- Primary teams: 232 (primary share 7.9%), hybrid teams: 243 (hybrid share 7.5%)
- Date range: 2026-09-09 to 2026-09-29
- Core pairs: Goodra-Hisui+Espathra@Grassy Seed, Espathra+Goodra-Hisui@Leftovers, Espathra+Goodra-Hisui, Absol+Goodra-Hisui@Leftovers, Absol+Espathra@Grassy Seed, Absol+Goodra-Hisui, Goodra-Hisui+Absol@Absolite Z, Maushold+Sinistcha@Occa Berry, Hatterene+Incineroar@White Herb, Camerupt+Incineroar@White Herb, Blastoise+Maushold@Chople Berry, Vivillon+Incineroar@Passho Berry, Blastoise+Sinistcha@Occa Berry, Maushold+Sinistcha@Coba Berry, Maushold+Blastoise@Blastoisinite, Delphox+Sinistcha@Occa Berry, Blastoise+Sinistcha@Coba Berry, Blastoise+Maushold, Gengar+Incineroar@Lum Berry, Delphox+Maushold@Chople Berry, Delphox+Sinistcha@Coba Berry, Sinistcha+Maushold@Chople Berry, Absol+Espathra, Espathra+Absol@Absolite Z, Delphox+Sinistcha@Colbur Berry, Gengar+Incineroar@Passho Berry, Sinistcha+Delphox@Delphoxite, Delphox+Sinistcha, Sinistcha+Blastoise@Blastoisinite, Delphox+Sneasler@Focus Sash, Maushold+Delphox@Delphoxite, Politoed+Incineroar@Passho Berry, Blastoise+Sinistcha, Delphox+Maushold, Sinistcha+Sableye@Roseli Berry, Delphox+Blastoise@Blastoisinite, Blastoise+Delphox@Delphoxite, Blastoise+Delphox, Sirfetch’d+Incineroar@Chople Berry, Maushold+Sinistcha, Sinistcha+Sneasler@Focus Sash, Blastoise+Sneasler@Focus Sash, Maushold+Sneasler@Focus Sash, Gengar+Dragonite@Life Orb, Floette-Eternal+Espathra@Grassy Seed, Lucario+Basculegion@Focus Sash, Floette-Eternal+Goodra-Hisui@Leftovers, Farigiraf+Incineroar@White Herb, Indeedee-F+Delphox@Life Orb, Goodra-Hisui+Floette-Eternal@Floettite, Floette-Eternal+Goodra-Hisui, Dragonite+Indeedee@Focus Sash, Gengar+Incineroar@Chople Berry, Politoed+Incineroar@Chople Berry, Delphox+Kingambit@Life Orb, Indeedee-F+Incineroar@White Herb, Swampert+Sinistcha@Sitrus Berry, Blastoise+Indeedee-F@Rocky Helmet, Lucario+Basculegion@Life Orb, Floette-Eternal+Sinistcha@Colbur Berry, Toxapex+Incineroar@Sitrus Berry, Farigiraf+Incineroar@Life Orb, Absol+Armarouge@Life Orb, Aerodactyl+Lucario@Lucarionite Z, Aerodactyl+Lucario, Archaludon+Incineroar@Passho Berry, Goodra-Hisui+Incineroar@Sitrus Berry, Farigiraf+Incineroar@Expert Belt, Floette-Eternal+Sneasler@Focus Sash, Torkoal+Incineroar@White Herb, Excadrill+Sinistcha@Sitrus Berry, Indeedee-F+Blastoise@Blastoisinite, Floette-Eternal+Delphox@Delphoxite, Sableye+Sinistcha, Delphox+Floette-Eternal@Floettite, Floette-Eternal+Incineroar@Rocky Helmet, Delphox+Floette-Eternal, Blastoise+Indeedee-F, Incineroar+Farigiraf@Twisted Spoon, Incineroar+Espathra@Focus Sash, Absol+Armarouge, Armarouge+Absol@Absolite Z, Lucario+Armarouge@Focus Sash, Blastoise+Indeedee-F@Colbur Berry, Floette-Eternal+Incineroar@Sitrus Berry, Incineroar+Rillaboom@Eject Button, Grimmsnarl+Sinistcha@Sitrus Berry, Annihilape+Lucario@Lucarionite Z, Annihilape+Lucario, Floette-Eternal+Rillaboom@Occa Berry, Incineroar+Toxapex@Leftovers, Incineroar+Aegislash@Focus Sash, Sinistcha+Kingambit@Life Orb, Pelipper+Sinistcha@Sitrus Berry, Farigiraf+Incineroar@Leftovers, Camerupt+Blastoise@Blastoisinite, Tyranitar+Sinistcha@Sitrus Berry, Pawmot+Delphox@Delphoxite, Abomasnow+Incineroar, Incineroar+Abomasnow@Abomasite, Incineroar+Vivillon, Incineroar+Goodra-Hisui@Leftovers, Absol+Aerodactyl, Aerodactyl+Absol@Absolite Z, Incineroar+Vivillon@Focus Sash, Incineroar+Toxapex, Floette-Eternal+Sinistcha@Occa Berry, Blastoise+Camerupt, Blastoise+Camerupt@Cameruptite, Espathra+Floette-Eternal@Floettite, Pawmot+Lucario@Lucarionite Z, Espathra+Floette-Eternal, Lucario+Pawmot, Delphox+Pawmot, Kommo-o+Incineroar@Chople Berry, Maushold+Indeedee-F@Rocky Helmet, Blastoise+Armarouge@Life Orb, Incineroar+Politoed@Sitrus Berry, Lucario+Aerodactyl@Aerodactylite, Golisopod+Incineroar@Chople Berry, Golisopod+Sinistcha@Kasib Berry, Sinistcha+Pawmot@Focus Sash, Floette-Eternal+Sinistcha@Coba Berry, Goodra-Hisui+Incineroar, Incineroar+Maushold@Focus Sash, Incineroar+Gengar@Gengarite, Floette-Eternal+Incineroar, Incineroar+Sinistcha@Occa Berry, Incineroar+Floette-Eternal@Floettite, Gengar+Incineroar, Sinistcha+Kommo-o@Leftovers, Kingambit+Incineroar@White Herb, Floette-Eternal+Sinistcha, Blastoise+Indeedee-F@Psychic Seed, Floette-Eternal+Dragonite@Dragoninite, Sinistcha+Floette-Eternal@Floettite, Floette-Eternal+Incineroar@Leftovers, Basculegion+Lucario@Lucarionite Z, Ampharos+Incineroar, Incineroar+Ampharos@Ampharosite, Basculegion+Lucario, Floette-Eternal+Sneasler@Grassy Seed, Hippowdon+Incineroar, Lucario+Garchomp@Garchompite Z, Clefable+Incineroar, Incineroar+Espathra@Grassy Seed, Incineroar+Sinistcha@Colbur Berry, Dragonite+Floette-Eternal@Floettite, Delphox+Kingambit@Black Glasses, Farigiraf+Incineroar@Chople Berry, Lucario+Incineroar@Sitrus Berry, Dragonite+Floette-Eternal, Delphox+Incineroar@Sitrus Berry, Indeedee-F+Sinistcha@Coba Berry, Sinistcha+Incineroar@Sitrus Berry, Ninetales-Alola+Delphox@Delphoxite, Primarina+Lucario@Lucarionite Z, Delphox+Kommo-o@Leftovers, Absol+Indeedee-F@Rocky Helmet, Dragonite+Sneasler@White Herb, Lucario+Rillaboom@Occa Berry, Lucario+Primarina, Pawmot+Sinistcha, Floette-Eternal+Gholdengo@Leftovers, Espathra+Incineroar, Dragonite+Metagross@Metagrossite, Delphox+Ninetales-Alola, Aegislash+Incineroar@Sitrus Berry, Espathra+Incineroar@Sitrus Berry, Kingambit+Delphox@Delphoxite, Incineroar+Sinistcha@Kasib Berry, Floette-Eternal+Garchomp@Sitrus Berry, Incineroar+Lopunny, Incineroar+Lopunny@Lopunnite, Blastoise+Incineroar@Sitrus Berry, Incineroar+Garchomp@Garchompite, Delphox+Pawmot@Focus Sash, Dragonite+Metagross, Delphox+Rillaboom@Occa Berry, Kingambit+Sinistcha@Colbur Berry, Delphox+Kingambit, Armarouge+Blastoise@Blastoisinite, Sinistcha+Swampert@Swampertite, Kommo-o+Incineroar@Passho Berry, Floette-Eternal+Kingambit@Life Orb, Lucario+Dragapult@Life Orb, Lucario+Rillaboom@Life Orb, Absol+Sneasler@Psychic Seed, Camerupt+Incineroar, Incineroar+Camerupt@Cameruptite, Golisopod+Sinistcha@Sitrus Berry, Floette-Eternal+Whimsicott@Focus Sash, Incineroar+Lucario@Lucarionite Z, Incineroar+Ninetales-Alola@Focus Sash, Archaludon+Incineroar@Chople Berry, Armarouge+Blastoise, Floette-Eternal+Garchomp@Choice Scarf, Incineroar+Lucario, Dragonite+Incineroar@Sitrus Berry, Baxcalibur+Incineroar@Chople Berry, Incineroar+Kommo-o@Leftovers, Metagross+Dragonite@Dragoninite, Sinistcha+Baxcalibur@Baxcalibrite, Incineroar+Rillaboom@Occa Berry, Blastoise+Sneasler@Psychic Seed, Incineroar+Sirfetch’d@Leek, Absol+Indeedee-F, Indeedee-F+Absol@Absolite Z, Incineroar+Delphox@Delphoxite, Dragapult+Lucario@Lucarionite Z, Sinistcha+Swampert, Incineroar+Sneasler@Focus Sash, Excadrill+Sinistcha@Colbur Berry, Dragapult+Lucario, Gardevoir+Sinistcha@Sitrus Berry, Goodra-Hisui+Rillaboom@Miracle Seed, Espathra+Archaludon@Leftovers, Delphox+Incineroar, Lucario+Incineroar@Chople Berry, Floette-Eternal+Kingambit@Occa Berry, Incineroar+Volcarona@Grassy Seed, Indeedee-F+Maushold@Chople Berry, Sneasler+Sinistcha@Occa Berry, Floette-Eternal+Gholdengo@Choice Scarf, Delphox+Incineroar@Rocky Helmet, Floette-Eternal+Basculegion@Focus Sash, Delphox+Garchomp@Choice Scarf, Incineroar+Sinistcha, Espathra+Charizard@Charizardite Y, Dragonite+Pelipper@Sitrus Berry, Rillaboom+Incineroar@Lum Berry, Rillaboom+Espathra@Grassy Seed, Incineroar+Mawile, Incineroar+Mawile@Mawilite, Dragonite+Basculegion@Life Orb, Archaludon+Espathra, Charizard+Espathra, Rotom-Wash+Incineroar@Sitrus Berry, Incineroar+Garchomp@Garchompite Z, Incineroar+Rillaboom@Leftovers, Incineroar+Sinistcha@Rocky Helmet, Vanilluxe+Incineroar@Sitrus Berry, Gardevoir+Blastoise@Blastoisinite, Incineroar+Venusaur@Life Orb, Incineroar+Sirfetch’d, Dragonite+Rillaboom@Life Orb, Tyranitar+Sinistcha@Colbur Berry, Absol+Milotic@Leftovers, Sneasler+Delphox@Delphoxite, Sinistcha+Tyranitar@Tyranitarite, Delphox+Incineroar@Passho Berry, Maushold+Gengar@Gengarite, Floette-Eternal+Volcarona@Grassy Seed, Incineroar+Dragonite@Dragoninite, Gengar+Maushold, Sneasler+Dragonite@Dragoninite, Delphox+Sneasler, Sinistcha+Pelipper@Focus Sash, Blastoise+Gardevoir, Blastoise+Gardevoir@Gardevoirite, Volcarona+Incineroar@Sitrus Berry, Garchomp+Incineroar@Sitrus Berry, Sinistcha+Tyranitar, Gholdengo+Dragonite@Dragoninite, Absol+Floette-Eternal, Floette-Eternal+Absol@Absolite Z, Incineroar+Farigiraf@Colbur Berry, Incineroar+Venusaur@Wide Lens, Whimsicott+Floette-Eternal@Floettite, Sinistcha+Indeedee-F@Rocky Helmet, Floette-Eternal+Whimsicott, Sneasler+Maushold@Chople Berry, Incineroar+Garchomp@Choice Scarf, Lucario+Rillaboom@Miracle Seed, Sneasler+Sinistcha@Coba Berry, Baxcalibur+Sinistcha, Floette-Eternal+Sneasler, Sneasler+Floette-Eternal@Floettite, Lucario+Sneasler@Grassy Seed, Blastoise+Incineroar, Archaludon+Sinistcha@Sitrus Berry, Floette-Eternal+Maushold@Chople Berry, Maushold+Incineroar@Sitrus Berry, Hatterene+Incineroar, Indeedee+Dragonite@Dragoninite, Blastoise+Kingambit@Focus Sash, Absol+Indeedee-F@Colbur Berry, Sneasler+Blastoise@Blastoisinite, Incineroar+Hatterene@Life Orb, Garchomp+Lucario@Lucarionite Z, Dragonite+Incineroar, Dragonite+Sneasler, Rillaboom+Goodra-Hisui@Leftovers, Incineroar+Politoed, Garchomp+Lucario, Delphox+Indeedee-F@Rocky Helmet, Rillaboom+Lucario@Lucarionite Z, Primarina+Incineroar@Sitrus Berry, Swampert+Incineroar@Passho Berry, Incineroar+Blastoise@Blastoisinite, Lucario+Rillaboom, Incineroar+Sinistcha@Coba Berry, Corviknight+Delphox, Blastoise+Sneasler, Dragonite+Gholdengo@Life Orb, Absol+Incineroar@Sitrus Berry, Indeedee-F+Sinistcha@Sitrus Berry, Dragonite+Gholdengo, Incineroar+Maushold@Chople Berry, Floette-Eternal+Rillaboom@Sitrus Berry, Garchomp+Incineroar@Chople Berry, Incineroar+Primarina@Grassy Seed, Incineroar+Sneasler@Grassy Seed, Pelipper+Sinistcha, Armarouge+Lucario@Lucarionite Z, Kommo-o+Sinistcha, Aegislash+Incineroar, Floette-Eternal+Rillaboom@Life Orb, Sinistcha+Excadrill@Focus Sash, Absol+Floette-Eternal@Floettite, Armarouge+Lucario, Incineroar+Basculegion@Focus Sash, Incineroar+Weavile, Altaria+Incineroar@Sitrus Berry, Rillaboom+Incineroar@Rocky Helmet, Incineroar+Maushold, Delphox+Rillaboom@Life Orb, Excadrill+Sinistcha, Goodra-Hisui+Rillaboom, Rillaboom+Incineroar@Passho Berry, Dragonite+Froslass, Dragonite+Froslass@Froslassite, Whimsicott+Incineroar@Rocky Helmet, Garchomp+Incineroar, Incineroar+Kingambit@Black Glasses, Incineroar+Primarina@Leftovers, Incineroar+Charizard@Charizardite X, Incineroar+Rillaboom@Life Orb, Rillaboom+Maushold@Focus Sash, Dragonite+Indeedee, Kingambit+Floette-Eternal@Floettite, Floette-Eternal+Gholdengo@Life Orb, Floette-Eternal+Kingambit, Gholdengo+Incineroar@Rocky Helmet, Sinistcha+Kingambit@Black Glasses, Maushold+Floette-Eternal@Floettite, Floette-Eternal+Kingambit@Black Glasses, Floette-Eternal+Maushold, Gholdengo+Floette-Eternal@Floettite, Dragonite+Sneasler@Psychic Seed, Floette-Eternal+Gholdengo, Floette-Eternal+Rillaboom, Swampert+Incineroar@Chople Berry, Rillaboom+Floette-Eternal@Floettite, Incineroar+Aerodactyl@Focus Sash, Lucario+Indeedee-F@Colbur Berry, Pawmot+Incineroar@Sitrus Berry, Aerodactyl+Incineroar@Sitrus Berry, Floette-Eternal+Basculegion@Life Orb, Dragonite+Sneasler@Focus Sash, Floette-Eternal+Rillaboom@Miracle Seed, Indeedee-F+Maushold, Incineroar+Farigiraf@Grassy Seed, Floette-Eternal+Basculegion@Choice Scarf, Froslass+Dragonite@Dragoninite, Sinistcha+Garchomp@Choice Scarf, Indeedee-F+Sinistcha@Occa Berry, Sneasler+Incineroar@Rocky Helmet, Sneasler+Incineroar@Sitrus Berry, Delphox+Kommo-o, Dragonite+Kingambit@Chople Berry, Garchomp+Floette-Eternal@Floettite, Incineroar+Kommo-o, Floette-Eternal+Garchomp, Rillaboom+Incineroar@Sitrus Berry, Basculegion+Floette-Eternal@Floettite, Farigiraf+Incineroar@Rocky Helmet, Basculegion+Floette-Eternal, Clefable+Rillaboom, Volcarona+Floette-Eternal@Floettite, Incineroar+Indeedee-F@Psychic Seed, Floette-Eternal+Volcarona, Salamence+Incineroar@Rocky Helmet, Kommo-o+Delphox@Delphoxite, Rillaboom+Incineroar@Chople Berry, Kingambit+Sinistcha, Sinistcha+Grimmsnarl@Light Clay, Lucario+Basculegion@Choice Scarf, Incineroar+Rillaboom, Mawile+Incineroar@Sitrus Berry, Sinistcha+Golisopod@Golisopite, Sneasler+Sinistcha@Colbur Berry, Pelipper+Sinistcha@Colbur Berry, Delphox+Kingambit@Chople Berry, Golisopod+Sinistcha, Corviknight+Delphox@Delphoxite, Gengar+Incineroar@Sitrus Berry, Blastoise+Kingambit@Chople Berry, Whimsicott+Incineroar@Chople Berry, Incineroar+Rillaboom@Grassy Seed, Gholdengo+Incineroar@Sitrus Berry, Grimmsnarl+Sinistcha, Incineroar+Volcarona, Lucario+Sylveon@Fairy Feather, Basculegion+Dragonite@Dragoninite, Basculegion+Dragonite, Blastoise+Kingambit, Incineroar+Ninetales-Alola, Dragonite+Indeedee-F@Psychic Seed, Rillaboom+Dragonite@Dragoninite, Espathra+Rillaboom, Rillaboom+Dragonite@Life Orb
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Incineroar | 91.9% | intimidate 99.9%, fake-out 99.5%, pivot 93.0%, trick-room-abuser 24.6%, helping-hand 7.7%, spa-drop 6.8%, disruption 4.5%, status 0.9% |
| Floette-Eternal | 65.6% | mega-attacker 99.7%, setup 83.6% |
| Delphox | 34.1% | mega-attacker 95.6%, setup 82.1%, disruption 4.3%, status 1.0%, terrain-setter 0.4%, priority-blocker 0.4% |
| Sinistcha | 30.6% | rage-powder 97.3%, trick-room-setter 74.1%, trick-room-abuser 15.7%, disruption 1.3%, follow-me 0.9% |
| Blastoise | 11.1% | mega-attacker 96.1%, setup 80.2%, fake-out 11.8%, pivot 3.9%, status 1.5% |
| Maushold | 10.6% | follow-me 98.2%, disruption 39.2%, helping-hand 10.7%, speed-drop 2.6%, pivot 1.8% |
| Dragonite | 9.8% | mega-attacker 87.5%, tailwind 64.8%, priority-attack 18.6%, setup 1.2% |
| Lucario | 4.8% | mega-attacker 98.8%, setup 69.5%, priority-attack 2.7% |
| Espathra | 3.7% | setup 88.4% |
| Absol | 2.7% | mega-attacker 100.0%, speed-drop 10.2%, status 7.9%, priority-attack 7.2%, perish-song 2.8%, setup 2.0% |
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
  - [Christopher Epps, 732nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0150/teamlist)
  - [Stefano Greppi, , 15 Sep 2026](https://pokepast.es/045cc1b2f27f9315)
  - [vi0ra_pokemon, , 14 Sep 2026](https://pokepast.es/74aea655c757917f)
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
  - [Joseph Eckhart, 100th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0904/teamlist)
  - [Nick Donato, 434th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0563/teamlist)
  - [Nathan Couto, 533rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0019/teamlist)
  - [Nate Curl, 745th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0608/teamlist)
  - [Alec Pineda, 1050th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0248/teamlist)
  - [shynessalex, Champion, 20 Sep 2026](https://pokepast.es/6f1d5b2b15285f2b)
  - [Wyatt McDonald, Top 4, 16 Sep 2026](https://pokepast.es/37083bbe99a1bf42)
  - [Angelina Huynh, 249th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0554/teamlist)
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
  - [Hippolyte Bernard, 28th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0616/teamlist)
  - [Anthony Liuzzo, 5th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0219/teamlist)
  - [Alex Soto, , 29 Sep 2026](https://pokepast.es/7af5d780689b5a8b)
  - [Moonstone, , 10 Sep 2026](https://pokepast.es/adae5bcd485f81df)
  - [Joshua Sutherland-Smith, 4th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0302/teamlist)
  - [llefface, 4th, 27 Sep 2026](https://pokepast.es/297320b831fb73b8)
  - [Jay Carson, 710th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0039/teamlist)
  - [Hisashiro Egashira, 836th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1046/teamlist)
  - [Adonis Watford, 272nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0792/teamlist)
  - [Hunter Jones, 479th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0465/teamlist)
  - [Tyler Coleman, 378th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0999/teamlist)
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
  - [Kevin Holzmann, 807th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0067/teamlist)
  - [Minche Chung, 12th, 20 Sep 2026](https://pokepast.es/b987727e0339084a)
  - [m_rada13, Champion, 11 Sep 2026](https://pokepast.es/3ac336df2eb4729b)
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/60458cc1a20c2440)
  - [v1nzvgc, , 18 Sep 2026](https://pokepast.es/ab47ccee10152485)
  - [Jan-Philipp Schmitz, 905th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0555/teamlist)
  - [Jonathan Todd, 730th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1008/teamlist)
  - [Danial Syed, 453rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0658/teamlist)
  - [Lucas MacKenzie, 971st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0216/teamlist)
  - [Spencer Verdoni, 500th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0691/teamlist)
  - [Ren Sandfry, 860th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0046/teamlist)
  - [Kevin Guzman, 407th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0034/teamlist)
  - [Ethan Partelow, 476th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0590/teamlist)
  - [homura_kurenai_, , 10 Sep 2026](https://pokepast.es/7f30345697a16032)
  - [gastrodon, , 10 Sep 2026](https://pokepast.es/59b0a674b59141a2)
  - [takiapoke, 7th, 28 Sep 2026](https://pokepast.es/3ad14d3332150d81)
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
  - [Alex Soto, , 10 Sep 2026](https://pokepast.es/134747480705edc0)
  - [Matthew Bachman, 1031st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0552/teamlist)
  - [Adryan Sutantoso, , 11 Sep 2026](https://pokepast.es/a96e0b908f220884)
  - [starportal_, , 11 Sep 2026](https://pokepast.es/dad21fdac630175b)
  - [Laura Craig, 968th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1087/teamlist)
  - [Aediion_, , 11 Sep 2026](https://pokepast.es/94cb88bc739ff2af)
  - [Joshua Wong, 738th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0885/teamlist)
  - [kaki, , 13 Sep 2026](https://pokepast.es/a202a04735494175)
  - [mofumofunatsuhi, , 13 Sep 2026](https://pokepast.es/f85b026e5b0e6567)
  - [Daniel Halm, 540th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0607/teamlist)
  - [Lucia Scalies, 675th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0324/teamlist)
  - [Lukas Kiefl, 1110th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0772/teamlist)
  - [Jermaine Mcleod, , 10 Sep 2026](https://pokepast.es/b54467e1aa4327e1)
  - [David Durán, 1012th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0374/teamlist)
  - [Arnau Sánchez, 363rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0485/teamlist)
  - [Marc Torralba Brosa, 959th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1090/teamlist)
  - [Joseph Frontera, 848th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0883/teamlist)
  - [Majd Serhan, 891st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0416/teamlist)
  - [Ellie Homen, 297th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0645/teamlist)
  - [Nikhil Rajbhandary, 443rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0848/teamlist)
  - [etc25269248, , 13 Sep 2026](https://pokepast.es/7664efb6099c8d98)
  - [Chuck Yin Sin, 919th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0734/teamlist)
  - [Marielle Lynnsen, 1072nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0419/teamlist)
  - [Ross Stewart, 234th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0622/teamlist)
  - [Athan Mallios, 194th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0074/teamlist)
  - [John Masters, 571st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0275/teamlist)
  - [Dorean Neron, 504th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0033/teamlist)
  - [Herbert Herrmann, 950th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0373/teamlist)
  - [jessditta, , 10 Sep 2026](https://pokepast.es/25ab0498e06c8ffb)
  - [Evan Graham, 602nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0970/teamlist)
  - [Livio Sandberg, 83rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0965/teamlist)
  - [Roel Egberts, 555th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0875/teamlist)
  - [Valentijn Visser, 7th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0818/teamlist)
  - [Alex Soto, , 29 Sep 2026](https://pokepast.es/098c5931bc868979)
  - [Benjamin Kurtzemann, 782nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0287/teamlist)
  - [MeK191817, , 11 Sep 2026](https://pokepast.es/a1338edf35719660)
  - [Ryosuke Hamasato, , 9 Sep 2026](https://pokepast.es/243c449b24b74266)
  - [Lamberto Pio Traverso, 266th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0592/teamlist)
  - [Lorenz Mirow, 789th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0565/teamlist)
  - [Kavin Gnana, 854th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0268/teamlist)
  - [francesco tomasino, 617th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0155/teamlist)
  - [David Kramer, 755th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0742/teamlist)
  - [Florian Klaus, 612th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0414/teamlist)
  - [Nils von Lengerke, 456th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0950/teamlist)
  - [Darcy Willis, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0254/teamlist)
  - [Jeremy Boyd, 294th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0900/teamlist)
  - [Quinn Prabhakar, 477th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0415/teamlist)
  - [Hushabye White, 966th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0616/teamlist)
  - [Miguel Stelmann Frias, 541st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0641/teamlist)
  - [Adrian Illert, 598th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0343/teamlist)
  - [pathogenvgc, , 14 Sep 2026](https://pokepast.es/a630a7a5018325c9)
  - [Michael Mullen, 666th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0282/teamlist)
  - [Joshua Flickinger, 838th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0567/teamlist)
  - [Lillian Heath, 767th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1067/teamlist)
  - [Duy Nguyen, 892nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0598/teamlist)
  - [Luisa Klöckner, 528th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0260/teamlist)
  - [Deduo Qiang, 879th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0668/teamlist)
  - [Sirhat Renklitepe, 878th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0143/teamlist)
  - [Nicole Burgy, 809th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0160/teamlist)
  - [Charlie Hall, 984th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0948/teamlist)
  - [Kristian Guimond, 671st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0928/teamlist)
  - [mie_swagger, , 14 Sep 2026](https://pokepast.es/a5b0fd2dfb4b9c13)

- Sub-community pass: 232 primary teams, 89 tokens, modularity 0.52; unconnected tokens: Annihilape (other item; Choice Scarf 2/5), Milotic (other item; Grassy Seed 2/5), Tyranitar (other item; Focus Sash 2/4), Bellibolt (other item; Leftovers 3/3), Camerupt (other item; Cameruptite 3/3), Charizard (other item; Charizardite X 3/3), Froslass (other item; Froslassite 3/3), Hippowdon (other item; Leftovers 3/3), Raichu (other item; Raichunite Y 2/3), Staraptor (other item; Staraptite 3/3), Talonflame (other item; Life Orb 2/3), Torkoal (other item; Life Orb 3/3), Volcarona (other item; Rocky Helmet 2/3); unassigned within the community: 1 teams (0.4% of its primary weight); hybrid teams of the community left out: 243
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 3 (0.68) | 4 (0.59) | 5 (0.52) | 7 (0.44) | 7 (0.40) |

#### Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar (110 primary teams, 65 distinct builds, top pair on 99/110)
- Megas on member teams: Floette 99, Dragonite 20, Charizard-Y 17, Salamence 17, Garchomp-Z 11, Blastoise 5
- Top species by team share: Incineroar 100%, Floette-Eternal 90%, Rillaboom 80%, Sneasler 57%, Garchomp 34%, Kingambit 32%
- Token label: Incineroar + Floette-Eternal@Floettite/Gholdengo
- Mode tags on primary teams: Setup 57, Tailwind 47, Sun 20, Trick Room 8, Snow 4, Screens 3, Psyspam 2, Perish Trap 2, Rain 1, Sand 1
- Primary teams: 110 (43.6% of the community's primary weight), hybrid teams: 33 (15.1%)
- Date range: 2026-09-09 to 2026-09-29
- Core pairs: Charizard@Charizardite Y+Garchomp@Choice Scarf, Whimsicott (other item; Focus Sash 10/11)+Charizard@Charizardite Y, Gholdengo (other item; Life Orb 32/34)+Dragonite@Dragoninite, Whimsicott (other item; Focus Sash 10/11)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 8/11)+Salamence@Salamencite, Basculegion (other item; Life Orb 9/12)+Salamence@Salamencite, Gholdengo (other item; Life Orb 32/34)+Garchomp@Choice Scarf, Gholdengo (other item; Life Orb 32/34)+Charizard@Charizardite Y, Gholdengo (other item; Life Orb 32/34)+Whimsicott (other item; Focus Sash 10/11), Basculegion@Choice Scarf+Salamence@Salamencite, Sneasler (other item; Focus Sash 66/122)+Dragonite@Dragoninite, Rillaboom (other item; Miracle Seed 104/149)+Sneasler@Grassy Seed, Goodra-Hisui (other item; Leftovers 5/5)+Floette-Eternal@Floettite, Floette-Eternal@Floettite+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 104/149)+Dragonite@Dragoninite, Charizard@Charizardite Y+Floette-Eternal@Floettite, Gholdengo (other item; Life Orb 32/34)+Floette-Eternal@Floettite, Dragonite@Dragoninite+Floette-Eternal@Floettite, Whimsicott (other item; Focus Sash 10/11)+Floette-Eternal@Floettite, Floette-Eternal@Floettite+Garchomp@Choice Scarf, Kingambit (other item; Life Orb 44/78)+Floette-Eternal@Floettite, Basculegion@Choice Scarf+Floette-Eternal@Floettite, Dragapult (other item; Life Orb 3/5)+Floette-Eternal@Floettite, Basculegion (other item; Life Orb 9/12)+Floette-Eternal@Floettite, Kingambit (other item; Life Orb 44/78)+Salamence@Salamencite, Absol@Absolite Z+Floette-Eternal@Floettite, Floette-Eternal@Floettite+Salamence@Salamencite, Gholdengo (other item; Life Orb 32/34)+Sneasler (other item; Focus Sash 66/122), Gholdengo (other item; Life Orb 32/34)+Rillaboom (other item; Miracle Seed 104/149), Rillaboom (other item; Miracle Seed 104/149)+Charizard@Charizardite Y, Espathra (other item; Grassy Seed 4/7)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 176/215)+Garchomp@Garchompite Z, Aerodactyl (other item; Aerodactylite 3/7)+Incineroar (other item; Sitrus Berry 176/215), Farigiraf (other item; Colbur Berry 3/8)+Incineroar (other item; Sitrus Berry 176/215), Incineroar (other item; Sitrus Berry 176/215)+Garchomp@Garchompite, Incineroar (other item; Sitrus Berry 176/215)+Whimsicott (other item; Focus Sash 10/11), Gholdengo (other item; Life Orb 32/34)+Incineroar (other item; Sitrus Berry 176/215), Incineroar (other item; Sitrus Berry 176/215)+Baxcalibur@Baxcalibrite, Basculegion (other item; Life Orb 9/12)+Incineroar (other item; Sitrus Berry 176/215), Incineroar (other item; Sitrus Berry 176/215)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 176/215)+Basculegion@Choice Scarf, Incineroar (other item; Sitrus Berry 176/215)+Sneasler@Grassy Seed, Incineroar (other item; Sitrus Berry 176/215)+Garchomp@Choice Scarf, Incineroar (other item; Sitrus Berry 176/215)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 176/215)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 176/215)+Volcarona@Grassy Seed, Incineroar (other item; Sitrus Berry 176/215)+Dragonite@Dragoninite, Goodra-Hisui (other item; Leftovers 5/5)+Incineroar (other item; Sitrus Berry 176/215), Incineroar (other item; Sitrus Berry 176/215)+Rotom-Wash (other item; Leftovers 3/4), Incineroar (other item; Sitrus Berry 176/215)+Absol@Absolite Z, Espathra (other item; Grassy Seed 4/7)+Incineroar (other item; Sitrus Berry 176/215), Incineroar (other item; Sitrus Berry 176/215)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 104/149)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 176/215)+Rillaboom (other item; Miracle Seed 104/149), Incineroar (other item; Sitrus Berry 176/215)+Kingambit (other item; Life Orb 44/78), Incineroar (other item; Sitrus Berry 176/215)+Salamence@Salamencite, Farigiraf (other item; Colbur Berry 3/8)+Rillaboom (other item; Miracle Seed 104/149)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Incineroar (other item; Sitrus Berry 176/215) | 100.0% |
| Floette-Eternal@Floettite | 87.1% |
| Gholdengo (other item; Life Orb 32/34) | 32.2% |
| Dragonite@Dragoninite | 20.4% |
| Sneasler@Grassy Seed | 17.4% |
| Charizard@Charizardite Y | 16.1% |
| Garchomp@Choice Scarf | 14.9% |
| Salamence@Salamencite | 14.9% |
| Whimsicott (other item; Focus Sash 10/11) | 10.1% |
| Basculegion (other item; Life Orb 9/12) | 8.6% |
| Farigiraf (other item; Colbur Berry 3/8) | 7.8% |
| Garchomp (other item; Life Orb 8/11) | 6.8% |
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
  - [Escen Schaferin, 880th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0082/teamlist)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [Thomas Schultz, 61st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1054/teamlist)
  - [Austin Frank, 45th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0430/teamlist)
  - [Rishi Gupta, 172nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1054/teamlist)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [Aryan Shah, 963rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0388/teamlist)
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

#### Community 2 / Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed (41 primary teams, 23 distinct builds, top pair on 25/41)
- Megas on member teams: Garchomp-Z 25, Metagross 17, Floette 8, Lucario-Z 7, Baxcalibur 3, Gengar 3
- Top species by team share: Rillaboom 100%, Incineroar 98%, Garchomp 68%, Sneasler 63%, Volcarona 59%, Metagross 41%
- Token label: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed
- Mode tags on primary teams: Setup 8, Tailwind 7, Rain 2, Snow 2, Sun 1, Sand 1, Trick Room 1, Perish Trap 1
- Primary teams: 41 (17.2% of the community's primary weight), hybrid teams: 43 (16.3%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Aerodactyl (other item; Aerodactylite 3/7)+Lucario@Lucarionite Z, Garchomp@Garchompite Z+Volcarona@Grassy Seed, Metagross@Metagrossite+Volcarona@Grassy Seed, Garchomp@Garchompite Z+Metagross@Metagrossite, Basculegion@Choice Scarf+Volcarona@Grassy Seed, Basculegion@Choice Scarf+Salamence@Salamencite, Basculegion@Choice Scarf+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 104/149)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 104/149)+Volcarona@Grassy Seed, Goodra-Hisui (other item; Leftovers 5/5)+Rillaboom (other item; Miracle Seed 104/149), Rillaboom (other item; Miracle Seed 104/149)+Golisopod@Golisopite, Rillaboom (other item; Miracle Seed 104/149)+Absol@Absolite Z, Rillaboom (other item; Miracle Seed 104/149)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 104/149)+Gengar@Gengarite, Rillaboom (other item; Miracle Seed 104/149)+Lucario@Lucarionite Z, Rillaboom (other item; Miracle Seed 104/149)+Dragonite@Dragoninite, Sneasler (other item; Focus Sash 66/122)+Metagross@Metagrossite, Espathra (other item; Grassy Seed 4/7)+Rillaboom (other item; Miracle Seed 104/149), Sneasler (other item; Focus Sash 66/122)+Volcarona@Grassy Seed, Aerodactyl (other item; Aerodactylite 3/7)+Rillaboom (other item; Miracle Seed 104/149), Sneasler (other item; Focus Sash 66/122)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 104/149)+Metagross@Metagrossite, Ninetales-Alola (other item; Choice Scarf 2/6)+Rillaboom (other item; Miracle Seed 104/149), Basculegion@Choice Scarf+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 104/149)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 104/149)+Baxcalibur@Baxcalibrite, Gholdengo (other item; Life Orb 32/34)+Rillaboom (other item; Miracle Seed 104/149), Rillaboom (other item; Miracle Seed 104/149)+Charizard@Charizardite Y, Incineroar (other item; Sitrus Berry 176/215)+Garchomp@Garchompite Z, Aerodactyl (other item; Aerodactylite 3/7)+Incineroar (other item; Sitrus Berry 176/215), Incineroar (other item; Sitrus Berry 176/215)+Basculegion@Choice Scarf, Incineroar (other item; Sitrus Berry 176/215)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 176/215)+Volcarona@Grassy Seed, Rillaboom (other item; Miracle Seed 104/149)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 176/215)+Rillaboom (other item; Miracle Seed 104/149), Farigiraf (other item; Colbur Berry 3/8)+Rillaboom (other item; Miracle Seed 104/149)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Rillaboom (other item; Miracle Seed 104/149) | 100.0% |
| Garchomp@Garchompite Z | 62.3% |
| Volcarona@Grassy Seed | 60.1% |
| Metagross@Metagrossite | 39.2% |
| Basculegion@Choice Scarf | 18.0% |
| Lucario@Lucarionite Z | 16.1% |
| Aerodactyl (other item; Aerodactylite 3/7) | 8.7% |
| Golisopod@Golisopite | 7.4% |
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
  - [Pasty, , 22 Sep 2026](https://pokepast.es/bc36f6f2b942701f)
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

#### Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette) (73 primary teams, 30 distinct builds, top pair on 55/73)
- Megas on member teams: Delphox 65, Floette 44, Blastoise 18, Gengar 3, Salamence 3, Metagross 2
- Top species by team share: Delphox 89%, Incineroar 79%, Sinistcha 78%, Sneasler 74%, Floette-Eternal 60%, Kingambit 56%
- Token label: Delphox@Delphoxite / Sinistcha / Sneasler
- Mode tags on primary teams: Setup 63, Trick Room 6, Tailwind 6, Sand 5, Psyspam 4, Sun 1, Snow 1
- Primary teams: 73 (35.3% of the community's primary weight), hybrid teams: 22 (8.9%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Indeedee-F (other item; Rocky Helmet 17/22)+Blastoise@Blastoisinite, Maushold (other item; Chople Berry 15/21)+Blastoise@Blastoisinite, Indeedee-F (other item; Rocky Helmet 17/22)+Maushold (other item; Chople Berry 15/21), Sinistcha (other item; Colbur Berry 29/63)+Delphox@Delphoxite, Indeedee-F (other item; Rocky Helmet 17/22)+Sinistcha (other item; Colbur Berry 29/63), Maushold (other item; Chople Berry 15/21)+Sinistcha (other item; Colbur Berry 29/63), Sinistcha (other item; Colbur Berry 29/63)+Blastoise@Blastoisinite, Maushold (other item; Chople Berry 15/21)+Delphox@Delphoxite, Indeedee-F (other item; Rocky Helmet 17/22)+Delphox@Delphoxite, Blastoise@Blastoisinite+Delphox@Delphoxite, Kingambit (other item; Life Orb 44/78)+Kommo-o (other item; Leftovers 9/10), Sneasler (other item; Focus Sash 66/122)+Dragonite@Dragoninite, Kingambit (other item; Life Orb 44/78)+Gengar@Gengarite, Pawmot (other item; Focus Sash 7/9)+Delphox@Delphoxite, Kommo-o (other item; Leftovers 9/10)+Sinistcha (other item; Colbur Berry 29/63), Pawmot (other item; Focus Sash 7/9)+Sinistcha (other item; Colbur Berry 29/63), Kingambit (other item; Life Orb 44/78)+Delphox@Delphoxite, Sneasler (other item; Focus Sash 66/122)+Baxcalibur@Baxcalibrite, Kommo-o (other item; Leftovers 9/10)+Delphox@Delphoxite, Kingambit (other item; Life Orb 44/78)+Sinistcha (other item; Colbur Berry 29/63), Sneasler (other item; Focus Sash 66/122)+Metagross@Metagrossite, Sneasler (other item; Focus Sash 66/122)+Volcarona@Grassy Seed, Sneasler (other item; Focus Sash 66/122)+Garchomp@Garchompite Z, Sneasler (other item; Focus Sash 66/122)+Blastoise@Blastoisinite, Sneasler (other item; Focus Sash 66/122)+Delphox@Delphoxite, Maushold (other item; Chople Berry 15/21)+Sneasler (other item; Focus Sash 66/122), Kingambit (other item; Life Orb 44/78)+Floette-Eternal@Floettite, Sinistcha (other item; Colbur Berry 29/63)+Sneasler (other item; Focus Sash 66/122), Kingambit (other item; Life Orb 44/78)+Sneasler (other item; Focus Sash 66/122), Kingambit (other item; Life Orb 44/78)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 104/149)+Baxcalibur@Baxcalibrite, Gholdengo (other item; Life Orb 32/34)+Sneasler (other item; Focus Sash 66/122), Incineroar (other item; Sitrus Berry 176/215)+Garchomp@Garchompite, Incineroar (other item; Sitrus Berry 176/215)+Baxcalibur@Baxcalibrite, Incineroar (other item; Sitrus Berry 176/215)+Kingambit (other item; Life Orb 44/78)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Delphox@Delphoxite | 91.4% |
| Sinistcha (other item; Colbur Berry 29/63) | 78.9% |
| Sneasler (other item; Focus Sash 66/122) | 75.0% |
| Kingambit (other item; Life Orb 44/78) | 57.8% |
| Maushold (other item; Chople Berry 15/21) | 26.4% |
| Blastoise@Blastoisinite | 25.4% |
| Indeedee-F (other item; Rocky Helmet 17/22) | 23.5% |
| Pawmot (other item; Focus Sash 7/9) | 4.9% |
| Garchomp@Garchompite | 1.5% |
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
  - [Stanisław Piotrowski, 198th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0850/teamlist)
  - [Jude Gerard Lee Wei Cong, 3rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0040/teamlist)
  - [Jude Lee, 3rd, 27 Sep 2026](https://pokepast.es/32857b1c3f3763e0)
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
- Core pairs: Kommo-o (other item; Leftovers 9/10)+Gengar@Gengarite, Kingambit (other item; Life Orb 44/78)+Kommo-o (other item; Leftovers 9/10), Kingambit (other item; Life Orb 44/78)+Gengar@Gengarite, Kommo-o (other item; Leftovers 9/10)+Sinistcha (other item; Colbur Berry 29/63), Kommo-o (other item; Leftovers 9/10)+Delphox@Delphoxite, Rillaboom (other item; Miracle Seed 104/149)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 176/215)+Gengar@Gengarite
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
| Sneasler | Sneasler (other item; Focus Sash 66/122) | Sub-community 2: Setup (Mega Delphox, Mega Floette) | 122 | 2: 54 · 0: 42 · 1: 26 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 0: Setup (Mega Floette) · Incineroar | 21 | 0: 21 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 37 | 1: 25 · 0: 11 · 2: 1 · unassigned: 0 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 0: Setup (Mega Floette) · Incineroar | 20 | 0: 16 · 2: 3 · 1: 1 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 8/11) | Sub-community 0: Setup (Mega Floette) · Incineroar | 11 | 0: 6 · 2: 3 · 1: 2 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite | Sub-community 2: Setup (Mega Delphox, Mega Floette) | 5 | 0: 4 · 2: 1 · unassigned: 0 |
| Basculegion | Basculegion@Choice Scarf | Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 23 | 0: 16 · 1: 7 · unassigned: 0 |
| Basculegion | Basculegion (other item; Life Orb 9/12) | Sub-community 0: Setup (Mega Floette) · Incineroar | 12 | 0: 11 · 1: 1 · unassigned: 0 |

### Community 3: Psyspam
- Token label: Indeedee-F + Gardevoir/Armarouge
- Mode tags on primary teams: Psyspam 365, Tailwind 225, Trick Room 171, Sun 103, Setup 58, Rain 41, Snow 32, Screens 16, Sand 8
- Megas on primary teams: Gardevoir 194, Golisopod 82, Staraptor 63, Salamence 56, Camerupt 46, Glimmora 46, Metagross 45, Charizard-Y 36, Raichu-Y 25, Baxcalibur 22, Absol-Z 18, Pyroar 17, Gengar 16, Blastoise 14, Garchomp-Z 13, Blaziken 11, Mawile 9, Floette 8, Froslass 7, Lucario-Z 7, Dragonite 6, Lopunny 6, Scovillain 6, Tyranitar 6, Abomasnow 4, Delphox 4, Meowstic-F 4, Aerodactyl 3, Alakazam 3, Crabominable 3, Heracross 3, Houndoom 2, Malamar 2, Skarmory 2, Swampert 2, Aggron 1, Ampharos 1, Charizard-X 1, Dragalge 1, Drampa 1, Gallade 1, Glalie 1, Golurk 1, Gyarados 1, Meganium 1, Raichu-X 1, Sceptile 1, Scrafty 1, Slowbro 1
- Primary teams: 482 (primary share 16.1%), hybrid teams: 159 (hybrid share 5.4%)
- Date range: 2026-09-09 to 2026-09-29
- Core pairs: Araquanid+Kleavor, Klefki+Volcarona@Sitrus Berry, Kleavor+Whimsicott@Fairy Feather, Pyroar+Kommo-o@Life Orb, Hatterene+Incineroar@White Herb, Houndoom+Torkoal, Torkoal+Houndoom@Houndoominite, Camerupt+Incineroar@White Herb, Abomasnow+Camerupt, Abomasnow+Camerupt@Cameruptite, Camerupt+Abomasnow@Abomasite, Baxcalibur+Ninetales-Alola@Light Clay, Dragapult+Altaria@Haban Berry, Pyroar+Basculegion@Mystic Water, Pawmot+Staraptor@Choice Scarf, Altaria+Milotic@Psychic Seed, Klefki+Glimmora@Glimmoranite, Dragapult+Milotic@Psychic Seed, Camerupt+Hatterene, Hatterene+Camerupt@Cameruptite, Camerupt+Hatterene@Life Orb, Glimmora+Klefki@Light Clay, Klefki+Garchomp@Life Orb, Altaria+Dragapult@Life Orb, Altaria+Dragapult, Glimmora+Klefki, Gallade+Hatterene@Life Orb, Dragapult+Armarouge@Twisted Spoon, Pyroar+Whimsicott@Focus Sash, Metagross+Altaria@Haban Berry, Hatterene+Indeedee-F@Psychic Seed, Gallade+Hatterene, Typhlosion-Hisui+Indeedee@Focus Sash, Kommo-o+Pyroar, Kommo-o+Pyroar@Pyroarite, Empoleon+Ninetales-Alola, Volcarona+Klefki@Light Clay, Ninetales-Alola+Baxcalibur@Baxcalibrite, Pyroar+Whimsicott, Whimsicott+Pyroar@Pyroarite, Glimmora+Ninetales-Alola@Never-Melt Ice, Camerupt+Indeedee-F@Psychic Seed, Dragapult+Armarouge@Focus Sash, Typhlosion-Hisui+Basculegion@Mystic Water, Pawmot+Kingambit@Occa Berry, Sirfetch’d+Hatterene@Life Orb, Whimsicott+Kommo-o@Life Orb, Metagross+Blaziken@Focus Sash, Blaziken+Kingambit@Occa Berry, Altaria+Metagross@Metagrossite, Glimmora+Volcarona@Rocky Helmet, Pawmot+Politoed@Life Orb, Klefki+Volcarona, Baxcalibur+Ninetales-Alola, Armarouge+Milotic@Psychic Seed, Altaria+Metagross, Camerupt+Gallade, Gallade+Camerupt@Cameruptite, Kommo-o+Whimsicott@Occa Berry, Hatterene+Sirfetch’d, Hatterene+Torkoal@Charcoal, Armarouge+Torkoal@Life Orb, Torkoal+Blaziken@Blazikenite, Camerupt+Kingambit@Black Glasses, Torkoal+Hatterene@Life Orb, Hatterene+Armarouge@Twisted Spoon, Hatterene+Kingambit@Black Glasses, Glimmora+Whimsicott@Occa Berry, Hatterene+Torkoal, Typhlosion-Hisui+Whimsicott, Camerupt+Sirfetch’d, Sirfetch’d+Camerupt@Cameruptite, Gardevoir+Pyroar, Gardevoir+Pyroar@Pyroarite, Pyroar+Gardevoir@Gardevoirite, Altaria+Rillaboom@Eject Button, Whimsicott+Typhlosion-Hisui@Choice Scarf, Whimsicott+Basculegion@Mystic Water, Lopunny+Indeedee-F@Colbur Berry, Araquanid+Volcarona, Pyroar+Indeedee-F@Rocky Helmet, Baxcalibur+Volcarona@Rocky Helmet, Sirfetch’d+Incineroar@Chople Berry, Altaria+Indeedee-F@Rocky Helmet, Meowstic-F+Sneasler@Psychic Seed, Sirfetch’d+Glimmora@Glimmoranite, Blaziken+Torkoal@Charcoal, Kommo-o+Basculegion@Mystic Water, Hatterene+Blaziken@Blazikenite, Typhlosion-Hisui+Glimmora@Glimmoranite, Gardevoir+Indeedee-F@Colbur Berry, Rotom-Wash+Metagross@Metagrossite, Whimsicott+Kleavor@Focus Sash, Gallade+Torkoal@Charcoal, Armarouge+Dragapult@Life Orb, Blaziken+Glimmora@Focus Sash, Dragapult+Indeedee-F@Rocky Helmet, Metagross+Rotom-Wash, Blaziken+Torkoal, Glimmora+Volcarona@Sitrus Berry, Torkoal+Armarouge@Life Orb, Typhlosion-Hisui+Whimsicott@Focus Sash, Metagross+Whimsicott@Fairy Feather, Metagross+Kleavor@Focus Sash, Pawmot+Baxcalibur@Baxcalibrite, Armarouge+Dragapult, Archaludon+Klefki@Light Clay, Gallade+Torkoal, Gengar+Kommo-o@Leftovers, Volcarona+Glimmora@Glimmoranite, Armarouge+Gallade, Gardevoir+Lopunny, Lopunny+Gardevoir@Gardevoirite, Gardevoir+Lopunny@Lopunnite, Baxcalibur+Pawmot@Focus Sash, Mawile+Torkoal@Charcoal, Gardevoir+Kommo-o@Life Orb, Volcarona+Baxcalibur@Baxcalibrite, Hatterene+Glimmora@Focus Sash, Garchomp+Klefki@Light Clay, Baxcalibur+Volcarona@Grassy Seed, Armarouge+Indeedee-F@Rocky Helmet, Torkoal+Farigiraf@Grassy Seed, Kommo-o+Indeedee@Focus Sash, Armarouge+Indeedee-F@Sitrus Berry, Gardevoir+Rotom-Heat@Sitrus Berry, Torkoal+Indeedee-F@Psychic Seed, Blaziken+Hatterene@Life Orb, Gardevoir+Indeedee-F@Sitrus Berry, Mawile+Torkoal, Torkoal+Mawile@Mawilite, Camerupt+Farigiraf@Colbur Berry, Lucario+Basculegion@Focus Sash, Sirfetch’d+Whimsicott@Focus Sash, Klefki+Archaludon@Leftovers, Glimmora+Sirfetch’d, Gardevoir+Sneasler@Psychic Seed, Baxcalibur+Pawmot, Basculegion+Pelipper@Choice Scarf, Milotic+Altaria@Haban Berry, Ninetales-Alola+Kommo-o@Leftovers, Glimmora+Typhlosion-Hisui, Indeedee-F+Armarouge@Psychic Seed, Indeedee-F+Gallade@White Herb, Indeedee-F+Altaria@Haban Berry, Indeedee-F+Hatterene@Focus Sash, Indeedee-F+Delphox@Life Orb, Annihilape+Torkoal@Charcoal, Kleavor+Whimsicott, Indeedee-F+Armarouge@Focus Sash, Archaludon+Klefki, Blaziken+Hatterene, Glimmora+Talonflame, Hatterene+Indeedee-F, Indeedee-F+Hatterene@Life Orb, Indeedee-F+Armarouge@Life Orb, Gardevoir+Indeedee-F, Indeedee-F+Gardevoir@Gardevoirite, Metagross+Milotic@Psychic Seed, Abomasnow+Farigiraf, Farigiraf+Abomasnow@Abomasite, Sirfetch’d+Whimsicott, Indeedee-F+Milotic@Psychic Seed, Armarouge+Grapploct, Indeedee-F+Armarouge@Twisted Spoon, Camerupt+Farigiraf, Farigiraf+Camerupt@Cameruptite, Baxcalibur+Volcarona, Armarouge+Indeedee-F, Grapploct+Farigiraf@Sitrus Berry, Glimmora+Volcarona, Camerupt+Farigiraf@Sitrus Berry, Gengar+Armarouge@Focus Sash, Whimsicott+Glimmora@Glimmoranite, Basculegion+Pyroar, Basculegion+Pyroar@Pyroarite, Meowstic-F+Charizard@Charizardite Y, Annihilape+Torkoal, Talonflame+Glimmora@Glimmoranite, Venusaur+Annihilape@Choice Scarf, Torkoal+Indeedee-F@Sitrus Berry, Glimmora+Kingambit@Occa Berry, Charizard+Meowstic-F, Charizard+Meowstic-F@Meowsticite, Indeedee+Typhlosion-Hisui, Garchomp+Klefki, Annihilape+Armarouge@Focus Sash, Armarouge+Vanilluxe, Typhlosion-Hisui+Torkoal@Charcoal, Whimsicott+Garchomp@Sitrus Berry, Indeedee+Typhlosion-Hisui@Choice Scarf, Indeedee-F+Starmie, Armarouge+Torkoal, Primarina+Blaziken@Blazikenite, Kleavor+Metagross@Metagrossite, Gardevoir+Indeedee-F@Rocky Helmet, Araquanid+Golisopod@Golisopite, Glimmora+Whimsicott, Gardevoir+Armarouge@Life Orb, Araquanid+Golisopod, Kommo-o+Rillaboom@Eject Button, Alakazam+Indeedee-F, Kleavor+Metagross, Rotom-Heat+Indeedee-F@Colbur Berry, Torkoal+Primarina@Life Orb, Indeedee-F+Starmie@Starminite, Golisopod+Armarouge@Psychic Seed, Indeedee-F+Incineroar@White Herb, Golisopod+Sirfetch’d@Leek, Armarouge+Indeedee-F@Colbur Berry, Blastoise+Indeedee-F@Rocky Helmet, Torkoal+Typhlosion-Hisui, Kommo-o+Gengar@Gengarite, Glimmora+Kommo-o@Life Orb, Ninetales-Alola+Pawmot, Golisopod+Armarouge@Twisted Spoon, Gengar+Kommo-o, Gardevoir+Basculegion@Mystic Water, Gardevoir+Whimsicott@Occa Berry, Whimsicott+Garchomp@Life Orb, Lucario+Basculegion@Life Orb, Gardevoir+Torkoal@Charcoal, Meganium+Indeedee-F@Rocky Helmet, Armarouge+Torkoal@Charcoal, Annihilape+Armarouge@Life Orb, Gallade+Indeedee-F@Rocky Helmet, Lycanroc-Dusk+Basculegion@Life Orb, Altaria+Gengar@Gengarite, Indeedee-F+Alakazam@Alakazite, Staraptor+Armarouge@Twisted Spoon, Altaria+Gengar, Gardevoir+Torkoal, Torkoal+Gardevoir@Gardevoirite, Kommo-o+Whimsicott, Arcanine-Hisui+Altaria@Haban Berry, Glimmora+Hydreigon, Indeedee-F+Dragapult@Life Orb, Metagross+Hydreigon@Choice Scarf, Absol+Armarouge@Life Orb, Whimsicott+Kingambit@Occa Berry, Metagross+Indeedee@Choice Scarf, Milotic+Dragapult@Life Orb, Ninetales-Alola+Rillaboom@Occa Berry, Glimmora+Whimsicott@Focus Sash, Scovillain+Basculegion@Life Orb, Kingambit+Altaria@Altarianite, Metagross+Dragapult@Life Orb, Blaziken+Ninetales-Alola, Baxcalibur+Basculegion@Life Orb, Annihilape+Armarouge, Glimmora+Indeedee@Focus Sash, Hydreigon+Glimmora@Glimmoranite, Basculegion+Kommo-o@Life Orb, Gardevoir+Garchomp@Sitrus Berry, Torkoal+Incineroar@White Herb, Dragapult+Milotic, Kommo-o+Ninetales-Alola, Blaziken+Primarina, Armarouge+Hatterene, Dragapult+Indeedee-F, Armarouge+Annihilape@Choice Scarf, Dragapult+Metagross@Metagrossite, Kleavor+Basculegion@Life Orb, Farigiraf+Grapploct, Annihilape+Venusaur@Focus Sash, Indeedee-F+Pyroar, Indeedee-F+Pyroar@Pyroarite, Swampert+Annihilape@Choice Scarf, Gallade+Indeedee-F, Golisopod+Kleavor@Choice Scarf, Armarouge+Hatterene@Life Orb, Talonflame+Metagross@Metagrossite, Kommo-o+Glimmora@Glimmoranite, Dragapult+Metagross, Kommo-o+Whimsicott@Focus Sash, Volcarona+Kingambit@Occa Berry, Torkoal+Annihilape@Choice Scarf, Glimmora+Blaziken@Blazikenite, Sirfetch’d+Golisopod@Golisopite, Metagross+Talonflame, Indeedee-F+Blastoise@Blastoisinite, Altaria+Milotic, Golisopod+Sirfetch’d, Indeedee-F+Torkoal@Life Orb, Indeedee+Metagross@Metagrossite, Staraptor+Armarouge@Focus Sash, Altaria+Indeedee-F, Dragapult+Indeedee-F@Sitrus Berry, Annihilape+Venusaur, Camerupt+Indeedee-F, Indeedee-F+Camerupt@Cameruptite, Milotic+Armarouge@Twisted Spoon, Staraptor+Dragapult@Life Orb, Volcarona+Garchomp@Garchompite Z, Blaziken+Indeedee-F@Psychic Seed, Glimmora+Hydreigon@Choice Scarf, Indeedee-F+Sneasler@Psychic Seed, Indeedee-F+Torkoal@Charcoal, Indeedee+Metagross, Hydreigon+Metagross@Metagrossite, Gengar+Dragapult@Life Orb, Indeedee-F+Torkoal, Blastoise+Indeedee-F, Armarouge+Indeedee-F@Psychic Seed, Primarina+Pawmot@Focus Sash, Scovillain+Indeedee-F@Colbur Berry, Armarouge+Sirfetch’d, Gardevoir+Rotom-Heat, Rotom-Heat+Gardevoir@Gardevoirite, Gardevoir+Aerodactyl@Focus Sash, Hydreigon+Metagross, Absol+Armarouge, Armarouge+Absol@Absolite Z, Indeedee-F+Lopunny, Indeedee-F+Lopunny@Lopunnite, Hatterene+Indeedee-F@Rocky Helmet, Arcanine-Hisui+Baxcalibur@Life Orb, Ninetales-Alola+Rillaboom@Life Orb, Lucario+Armarouge@Focus Sash, Dragapult+Staraptor@Staraptite, Baxcalibur+Farigiraf@Colbur Berry, Blaziken+Kingambit@Black Glasses, Blastoise+Indeedee-F@Colbur Berry, Salamence+Klefki@Light Clay, Basculegion+Talonflame@Life Orb, Gardevoir+Basculegion@Choice Scarf, Dragapult+Staraptor, Gardevoir+Typhlosion-Hisui, Typhlosion-Hisui+Gardevoir@Gardevoirite, Indeedee-F+Talonflame@Life Orb, Dragapult+Gengar@Gengarite, Whimsicott+Glimmora@Focus Sash, Dragapult+Gengar, Armarouge+Sneasler@Psychic Seed, Basculegion+Whimsicott@Fairy Feather, Farigiraf+Sirfetch’d@Leek, Annihilape+Lucario@Lucarionite Z, Annihilape+Lucario, Hatterene+Farigiraf@Sitrus Berry, Metagross+Armarouge@Twisted Spoon, Pelipper+Basculegion@Choice Scarf, Farigiraf+Hatterene@Life Orb, Altaria+Arcanine-Hisui, Froslass+Blaziken@Blazikenite, Armarouge+Gardevoir, Armarouge+Gardevoir@Gardevoirite, Basculegion+Talonflame@Focus Sash, Garchomp+Whimsicott@Occa Berry, Ninetales-Alola+Glimmora@Glimmoranite, Gardevoir+Kommo-o, Kommo-o+Gardevoir@Gardevoirite, Camerupt+Blastoise@Blastoisinite, Dragapult+Glimmora@Glimmoranite, Glimmora+Basculegion@Mystic Water, Pawmot+Primarina, Pawmot+Delphox@Delphoxite, Kingambit+Volcarona@Focus Sash, Abomasnow+Incineroar, Incineroar+Abomasnow@Abomasite, Farigiraf+Hatterene, Gardevoir+Armarouge@Focus Sash, Basculegion+Meganium, Basculegion+Meganium@Meganiumite, Annihilape+Swampert@Swampertite, Indeedee-F+Meowstic-F, Indeedee-F+Meowstic-F@Meowsticite, Swampert+Volcarona@Rocky Helmet, Basculegion+Whimsicott@Focus Sash, Typhlosion-Hisui+Garchomp@Garchompite Z, Glimmora+Volcarona@Grassy Seed, Annihilape+Gardevoir, Annihilape+Gardevoir@Gardevoirite, Basculegion+Kleavor@Focus Sash, Dragapult+Volcarona@Rocky Helmet, Blastoise+Camerupt, Blastoise+Camerupt@Cameruptite, Whimsicott+Garchomp@Choice Scarf, Altaria+Arcanine-Hisui@Focus Sash, Sirfetch’d+Farigiraf@Sitrus Berry, Pawmot+Lucario@Lucarionite Z, Basculegion+Volcarona@Leftovers, Charizard+Whimsicott@Focus Sash, Whimsicott+Indeedee-F@Rocky Helmet, Annihilape+Rillaboom@Life Orb, Glimmora+Garchomp@Life Orb, Lucario+Pawmot, Garchomp+Rotom-Wash, Gardevoir+Whimsicott@Focus Sash, Kleavor+Volcarona, Delphox+Pawmot, Baxcalibur+Milotic@Leftovers, Kommo-o+Incineroar@Chople Berry, Armarouge+Mawile, Armarouge+Mawile@Mawilite, Maushold+Indeedee-F@Rocky Helmet, Blastoise+Armarouge@Life Orb, Metagross+Indeedee-F@Sitrus Berry, Indeedee-F+Kommo-o@Life Orb, Basculegion+Whimsicott, Grapploct+Golisopod@Golisopite, Torkoal+Kingambit@Focus Sash, Golisopod+Grapploct, Gardevoir+Whimsicott, Whimsicott+Gardevoir@Gardevoirite, Kommo-o+Indeedee-F@Rocky Helmet, Baxcalibur+Sneasler@Focus Sash, Whimsicott+Basculegion@Life Orb, Torkoal+Indeedee-F@Colbur Berry, Basculegion+Kingambit@Occa Berry, Annihilape+Swampert, Talonflame+Garchomp@Life Orb, Sinistcha+Pawmot@Focus Sash, Annihilape+Indeedee-F@Colbur Berry, Gardevoir+Indeedee-F@Psychic Seed, Blaziken+Metagross@Metagrossite, Kingambit+Torkoal@Life Orb, Baxcalibur+Glimmora@Glimmoranite, Charizard+Whimsicott@Occa Berry, Glimmora+Kommo-o, Glimmora+Baxcalibur@Baxcalibrite, Volcarona+Basculegion@Life Orb, Metagross+Milotic, Milotic+Metagross@Metagrossite, Kommo-o+Rillaboom@Occa Berry, Camerupt+Rillaboom@Life Orb, Blaziken+Metagross, Milotic+Armarouge@Focus Sash, Klefki+Salamence@Salamencite, Klefki+Salamence, Metagross+Sneasler@Psychic Seed, Sinistcha+Kommo-o@Leftovers, Garchomp+Volcarona@Grassy Seed, Gallade+Gardevoir, Gallade+Gardevoir@Gardevoirite, Gardevoir+Talonflame, Talonflame+Gardevoir@Gardevoirite, Glimmora+Garchomp@Choice Scarf, Blastoise+Indeedee-F@Psychic Seed, Charizard+Whimsicott, Whimsicott+Charizard@Charizardite Y, Typhlosion-Hisui+Sneasler@Psychic Seed, Ninetales-Alola+Volcarona@Grassy Seed, Indeedee+Kommo-o@Life Orb, Torkoal+Farigiraf@Colbur Berry, Primarina+Torkoal@Charcoal, Kingambit+Blaziken@Blazikenite, Grapploct+Indeedee-F, Blaziken+Glimmora, Kingambit+Meowstic-F, Kingambit+Meowstic-F@Meowsticite, Basculegion+Gardevoir, Basculegion+Gardevoir@Gardevoirite, Politoed+Kommo-o@Leftovers, Blaziken+Rillaboom@Life Orb, Farigiraf+Sirfetch’d, Indeedee-F+Rotom-Heat@Sitrus Berry, Basculegion+Lucario@Lucarionite Z, Whimsicott+Armarouge@Twisted Spoon, Kommo-o+Politoed@Sitrus Berry, Hatterene+Indeedee-F@Colbur Berry, Pawmot+Glimmora@Glimmoranite, Armarouge+Staraptor, Armarouge+Staraptor@Staraptite, Farigiraf+Torkoal@Charcoal, Basculegion+Lucario, Ninetales-Alola+Metagross@Metagrossite, Sneasler+Altaria@Altarianite, Farigiraf+Torkoal, Metagross+Volcarona@Grassy Seed, Gardevoir+Venusaur@Focus Sash, Basculegion+Lycanroc-Dusk@Focus Sash, Indeedee-F+Sinistcha@Coba Berry, Glimmora+Ninetales-Alola, Talonflame+Tyranitar, Metagross+Ninetales-Alola, Basculegion+Whimsicott@Occa Berry, Kleavor+Kingambit@Chople Berry, Hydreigon+Milotic@Sitrus Berry, Garchomp+Whimsicott@Focus Sash, Kingambit+Volcarona@Rocky Helmet, Indeedee-F+Meowscarada, Ninetales-Alola+Delphox@Delphoxite, Blaziken+Kingambit@Focus Sash, Glimmora+Dragapult@Life Orb, Delphox+Kommo-o@Leftovers, Absol+Indeedee-F@Rocky Helmet, Primarina+Torkoal, Garchomp+Whimsicott, Garchomp+Volcarona@Rocky Helmet, Basculegion+Volcarona@Grassy Seed, Pawmot+Sinistcha, Basculegion+Pelipper@Focus Sash, Hatterene+Golisopod@Golisopite, Gardevoir+Annihilape@Choice Scarf, Torkoal+Indeedee-F@Rocky Helmet, Golisopod+Hatterene, Whimsicott+Indeedee@Focus Sash, Basculegion+Lycanroc-Dusk, Metagross+Indeedee-F@Rocky Helmet, Froslass+Pawmot@Focus Sash, Pawmot+Basculegion@Life Orb, Dragonite+Metagross@Metagrossite, Delphox+Ninetales-Alola, Pelipper+Annihilape@Choice Scarf, Metagross+Garchomp@Garchompite Z, Raichu+Vanilluxe@Choice Scarf, Glimmora+Basculegion@Life Orb, Basculegion+Typhlosion-Hisui@Choice Scarf, Basculegion+Scovillain@Scovillainite, Garchomp+Kleavor@Focus Sash, Farigiraf+Blaziken@Blazikenite, Armarouge+Camerupt, Armarouge+Camerupt@Cameruptite, Metagross+Milotic@Leftovers, Incineroar+Lopunny, Incineroar+Lopunny@Lopunnite, Delphox+Pawmot@Focus Sash, Metagross+Milotic@Sitrus Berry, Dragonite+Metagross, Gardevoir+Kingambit@Occa Berry, Indeedee-F+Aerodactyl@Focus Sash, Whimsicott+Kingambit@Chople Berry, Basculegion+Rillaboom@Expert Belt, Indeedee-F+Typhlosion-Hisui, Basculegion+Glimmora@Glimmoranite, Archaludon+Volcarona@Sitrus Berry, Charizard+Annihilape@Choice Scarf, Garchomp+Volcarona, Armarouge+Blastoise@Blastoisinite, Volcarona+Basculegion@Choice Scarf, Hydreigon+Pelipper@Sitrus Berry, Dragapult+Glimmora, Kommo-o+Incineroar@Passho Berry, Baxcalibur+Glimmora, Basculegion+Talonflame, Basculegion+Scovillain, Ninetales-Alola+Volcarona, Lucario+Dragapult@Life Orb, Venusaur+Torkoal@Charcoal, Golisopod+Basculegion@Choice Scarf, Garchomp+Glimmora@Focus Sash, Annihilape+Indeedee-F@Rocky Helmet, Camerupt+Incineroar, Incineroar+Camerupt@Cameruptite, Floette-Eternal+Whimsicott@Focus Sash, Archaludon+Annihilape@Choice Scarf, Basculegion+Glimmora, Incineroar+Ninetales-Alola@Focus Sash, Golisopod+Hatterene@Life Orb, Basculegion+Kingambit@Chople Berry, Armarouge+Blastoise, Garchomp+Typhlosion-Hisui@Choice Scarf, Kingambit+Basculegion@Life Orb, Blaziken+Froslass, Blaziken+Froslass@Froslassite, Golisopod+Indeedee-F@Colbur Berry, Mawile+Indeedee-F@Colbur Berry, Empoleon+Garchomp, Kingambit+Whimsicott@Fairy Feather, Baxcalibur+Incineroar@Chople Berry, Glimmora+Kingambit@Focus Sash, Camerupt+Kingambit, Kingambit+Camerupt@Cameruptite, Incineroar+Kommo-o@Leftovers, Metagross+Dragonite@Dragoninite, Sinistcha+Baxcalibur@Baxcalibrite, Annihilape+Pelipper@Sitrus Berry, Incineroar+Sirfetch’d@Leek, Absol+Indeedee-F, Indeedee-F+Absol@Absolite Z, Armarouge+Gengar@Gengarite, Garchomp+Talonflame, Indeedee-F+Basculegion@Mystic Water, Armarouge+Gengar, Meowstic-F+Sneasler, Sneasler+Meowstic-F@Meowsticite, Indeedee-F+Basculegion@Focus Sash, Basculegion+Volcarona, Blaziken+Kingambit, Dragapult+Lucario@Lucarionite Z, Kingambit+Hatterene@Life Orb, Farigiraf+Indeedee-F@Psychic Seed, Dragapult+Lucario, Gardevoir+Sinistcha@Sitrus Berry, Froslass+Volcarona@Grassy Seed, Primarina+Volcarona@Grassy Seed, Annihilape+Sneasler@Psychic Seed, Ninetales-Alola+Gengar@Gengarite, Torkoal+Venusaur, Annihilape+Indeedee-F, Kingambit+Whimsicott@Occa Berry, Gengar+Ninetales-Alola, Indeedee-F+Typhlosion-Hisui@Choice Scarf, Froslass+Pawmot, Pawmot+Froslass@Froslassite, Indeedee-F+Meganium, Indeedee-F+Meganium@Meganiumite, Talonflame+Indeedee-F@Rocky Helmet, Milotic+Hydreigon@Choice Scarf, Hatterene+Kingambit, Garchomp+Volcarona@Sitrus Berry, Armarouge+Golisopod@Golisopite, Blaziken+Farigiraf@Sitrus Berry, Armarouge+Milotic, Armarouge+Golisopod, Pelipper+Indeedee-F@Colbur Berry, Indeedee-F+Kommo-o, Incineroar+Volcarona@Grassy Seed, Kleavor+Golisopod@Golisopite, Altaria+Sneasler@Grassy Seed, Indeedee-F+Maushold@Chople Berry, Golisopod+Kleavor, Gardevoir+Venusaur, Venusaur+Gardevoir@Gardevoirite, Glimmora+Hatterene@Life Orb, Kommo-o+Indeedee-F@Psychic Seed, Basculegion+Typhlosion-Hisui, Floette-Eternal+Basculegion@Focus Sash, Raichu+Talonflame@Focus Sash, Indeedee-F+Whimsicott@Focus Sash, Blaziken+Farigiraf, Pawmot+Metagross@Metagrossite, Rillaboom+Volcarona@Grassy Seed, Rillaboom+Empoleon@Leftovers, Rillaboom+Altaria@Altarianite, Politoed+Pawmot@Focus Sash, Basculegion+Baxcalibur@Baxcalibrite, Dragonite+Basculegion@Life Orb, Vanilluxe+Raichu@Raichunite Y, Venusaur+Indeedee-F@Colbur Berry, Kommo-o+Politoed, Staraptor+Hydreigon@Choice Scarf, Rotom-Wash+Incineroar@Sitrus Berry, Kingambit+Ninetales-Alola@Choice Scarf, Swampert+Kommo-o@Leftovers, Indeedee-F+Whimsicott@Occa Berry, Metagross+Pawmot, Kingambit+Basculegion@Mystic Water, Raichu+Vanilluxe, Kingambit+Torkoal, Kingambit+Indeedee-F@Sitrus Berry, Ninetales-Alola+Rillaboom@Sitrus Berry, Basculegion+Pelipper, Indeedee-F+Kingambit@Occa Berry, Staraptor+Glimmora@Focus Sash, Volcarona+Kingambit@Focus Sash, Farigiraf+Pawmot@Focus Sash, Vanilluxe+Incineroar@Sitrus Berry, Gardevoir+Blastoise@Blastoisinite, Glimmora+Pawmot, Annihilape+Charizard@Charizardite Y, Incineroar+Sirfetch’d, Sneasler+Volcarona@Focus Sash, Golisopod+Annihilape@Choice Scarf, Basculegion+Indeedee-F@Colbur Berry, Metagross+Arcanine-Hisui@Focus Sash, Garchomp+Typhlosion-Hisui, Kingambit+Torkoal@Charcoal, Baxcalibur+Metagross, Dragapult+Volcarona, Pawmot+Farigiraf@Sitrus Berry, Garchomp+Glimmora, Annihilape+Charizard, Gardevoir+Pelipper@Focus Sash, Staraptor+Whimsicott@Occa Berry, Basculegion+Volcarona@Rocky Helmet, Arcanine-Hisui+Metagross@Metagrossite, Indeedee-F+Vanilluxe, Indeedee-F+Scovillain, Floette-Eternal+Volcarona@Grassy Seed, Basculegion+Garchomp@Sitrus Berry, Kingambit+Ninetales-Alola@Never-Melt Ice, Basculegion+Glimmora@Focus Sash, Kommo-o+Gholdengo@Grassy Seed, Baxcalibur+Sneasler@White Herb, Gardevoir+Basculegion@Focus Sash, Volcarona+Metagross@Metagrossite, Indeedee+Kommo-o, Sneasler+Indeedee-F@Colbur Berry, Glimmora+Basculegion@Choice Scarf, Arcanine-Hisui+Metagross, Gengar+Indeedee-F@Rocky Helmet, Blastoise+Gardevoir, Blastoise+Gardevoir@Gardevoirite, Volcarona+Incineroar@Sitrus Berry, Torkoal+Farigiraf@Sitrus Berry, Camerupt+Indeedee-F@Rocky Helmet, Torkoal+Garchomp@Garchompite Z, Volcarona+Rillaboom@Miracle Seed, Basculegion+Baxcalibur, Talonflame+Tyranitar@Tyranitarite, Farigiraf+Pawmot, Golisopod+Indeedee-F@Sitrus Berry, Torkoal+Armarouge@Focus Sash, Talonflame+Garchomp@Garchompite Z, Gardevoir+Volcarona@Sitrus Berry, Torkoal+Kingambit@Black Glasses, Indeedee-F+Basculegion@Choice Scarf, Milotic+Baxcalibur@Baxcalibrite, Metagross+Volcarona, Archaludon+Basculegion@Choice Scarf, Glimmora+Hatterene, Whimsicott+Floette-Eternal@Floettite, Staraptor+Indeedee-F@Rocky Helmet, Indeedee-F+Metagross@Metagrossite, Indeedee-F+Whimsicott, Sinistcha+Indeedee-F@Rocky Helmet, Floette-Eternal+Whimsicott, Glimmora+Torkoal, Basculegion+Volcarona@Sitrus Berry, Indeedee-F+Metagross, Baxcalibur+Sinistcha, Indeedee-F+Kingambit@Black Glasses, Milotic+Indeedee-F@Rocky Helmet, Indeedee-F+Rotom-Heat, Metagross+Indeedee@Focus Sash, Basculegion+Indeedee@Focus Sash, Golisopod+Dragapult@Life Orb, Ninetales-Alola+Sneasler@Focus Sash, Indeedee-F+Annihilape@Choice Scarf, Annihilape+Pelipper@Focus Sash, Indeedee-F+Pelipper@Focus Sash, Annihilape+Pelipper, Hatterene+Incineroar, Dragapult+Golisopod@Golisopite, Blaziken+Kingambit@Chople Berry, Basculegion+Kingambit@Black Glasses, Garchomp+Glimmora@Glimmoranite, Dragapult+Golisopod, Raichu+Volcarona@Grassy Seed, Gardevoir+Sneasler, Sneasler+Gardevoir@Gardevoirite, Absol+Indeedee-F@Colbur Berry, Incineroar+Hatterene@Life Orb, Indeedee-F+Garchomp@Sitrus Berry, Metagross+Pawmot@Focus Sash, Indeedee-F+Scovillain@Scovillainite, Froslass+Volcarona, Volcarona+Froslass@Froslassite, Indeedee+Glimmora@Glimmoranite, Golisopod+Pawmot@Focus Sash, Raichu+Blaziken@Focus Sash, Delphox+Indeedee-F@Rocky Helmet, Indeedee-F+Rotom-Wash, Glimmora+Kingambit, Golisopod+Armarouge@Life Orb, Kingambit+Glimmora@Glimmoranite, Pawmot+Politoed, Kingambit+Glimmora@Focus Sash, Sneasler+Indeedee-F@Sitrus Berry, Rillaboom+Volcarona@Rocky Helmet, Baxcalibur+Froslass, Baxcalibur+Froslass@Froslassite, Torkoal+Kingambit@Chople Berry, Volcarona+Sneasler@White Herb, Metagross+Rillaboom@Life Orb, Annihilape+Golisopod@Golisopite, Annihilape+Golisopod, Basculegion+Pawmot@Focus Sash, Basculegion+Kleavor, Baxcalibur+Rillaboom@Sitrus Berry, Indeedee-F+Sinistcha@Sitrus Berry, Basculegion+Kommo-o, Golisopod+Indeedee-F@Psychic Seed, Annihilape+Archaludon, Pawmot+Garchomp@Garchompite Z, Indeedee-F+Glimmora@Focus Sash, Garchomp+Metagross@Metagrossite, Indeedee-F+Toxtricity, Blaziken+Milotic@Leftovers, Blaziken+Basculegion@Life Orb, Rillaboom+Volcarona, Baxcalibur+Milotic, Torkoal+Whimsicott@Focus Sash, Glimmora+Kingambit@Life Orb, Basculegion+Rillaboom@Sitrus Berry, Venusaur+Indeedee-F@Psychic Seed, Volcarona+Dragapult@Life Orb, Armarouge+Lucario@Lucarionite Z, Kommo-o+Sinistcha, Froslass+Kommo-o@Leftovers, Armarouge+Kingambit@Focus Sash, Garchomp+Kleavor, Milotic+Indeedee-F@Sitrus Berry, Basculegion+Pawmot, Garchomp+Metagross, Hydreigon+Sneasler@Psychic Seed, Annihilape+Archaludon@Leftovers, Kingambit+Indeedee-F@Psychic Seed, Indeedee-F+Golisopod@Golisopite, Indeedee-F+Mawile, Indeedee-F+Mawile@Mawilite, Golisopod+Indeedee-F, Armarouge+Lucario, Incineroar+Basculegion@Focus Sash, Gardevoir+Kingambit@Chople Berry, Whimsicott+Staraptor@Staraptite, Altaria+Incineroar@Sitrus Berry, Basculegion+Garchomp@Garchompite Z, Basculegion+Aerodactyl@Focus Sash, Venusaur+Basculegion@Choice Scarf, Kingambit+Kommo-o@Leftovers, Talonflame+Basculegion@Choice Scarf, Blaziken+Indeedee-F, Hydreigon+Milotic, Kingambit+Kleavor@Focus Sash, Salamence+Basculegion@Life Orb, Corviknight+Indeedee-F@Colbur Berry, Indeedee+Kommo-o@Leftovers, Staraptor+Whimsicott, Whimsicott+Incineroar@Rocky Helmet, Indeedee-F+Blaziken@Blazikenite, Camerupt+Golisopod@Golisopite, Pawmot+Volcarona, Froslass+Basculegion@Life Orb, Camerupt+Golisopod, Golisopod+Camerupt@Cameruptite, Kingambit+Whimsicott, Arcanine-Hisui+Hydreigon@Choice Scarf, Baxcalibur+Metagross@Metagrossite, Basculegion+Kingambit, Glimmora+Pawmot@Focus Sash, Sneasler+Volcarona@Leftovers, Torkoal+Sneasler@Psychic Seed, Hydreigon+Milotic@Leftovers, Lucario+Indeedee-F@Colbur Berry, Pawmot+Incineroar@Sitrus Berry, Glimmora+Torkoal@Charcoal, Floette-Eternal+Basculegion@Life Orb, Raichu+Kleavor@Focus Sash, Hydreigon+Indeedee, Indeedee-F+Maushold, Floette-Eternal+Basculegion@Choice Scarf, Indeedee-F+Sinistcha@Occa Berry, Volcarona+Rillaboom@Life Orb, Pawmot+Golisopod@Golisopite, Basculegion+Rotom-Heat, Golisopod+Pawmot, Kingambit+Basculegion@Focus Sash, Indeedee-F+Talonflame, Delphox+Kommo-o, Sneasler+Baxcalibur@Baxcalibrite, Glimmora+Kingambit@Chople Berry, Sylveon+Basculegion@Life Orb, Incineroar+Kommo-o, Salamence+Volcarona@Sitrus Berry, Basculegion+Sneasler@Psychic Seed, Basculegion+Floette-Eternal@Floettite, Basculegion+Floette-Eternal, Basculegion+Sableye, Garchomp+Basculegion@Life Orb, Hydreigon+Charizard@Charizardite Y, Staraptor+Whimsicott@Focus Sash, Indeedee-F+Venusaur@Focus Sash, Volcarona+Floette-Eternal@Floettite, Metagross+Armarouge@Life Orb, Rillaboom+Baxcalibur@Baxcalibrite, Incineroar+Indeedee-F@Psychic Seed, Rillaboom+Ninetales-Alola@Choice Scarf, Floette-Eternal+Volcarona, Kommo-o+Delphox@Delphoxite, Metagross+Sneasler@White Herb, Charizard+Hydreigon, Blaziken+Milotic, Basculegion+Indeedee-F@Sitrus Berry, Kingambit+Armarouge@Life Orb, Basculegion+Indeedee-F, Milotic+Blaziken@Blazikenite, Lucario+Basculegion@Choice Scarf, Kleavor+Charizard@Charizardite Y, Hydreigon+Staraptor@Staraptite, Tyranitar+Armarouge@Life Orb, Kommo-o+Metagross, Kommo-o+Kingambit@Chople Berry, Metagross+Sneasler, Armarouge+Metagross@Metagrossite, Raichu+Ninetales-Alola@Light Clay, Sneasler+Metagross@Metagrossite, Gardevoir+Kommo-o@Leftovers, Charizard+Indeedee-F@Sitrus Berry, Glimmora+Indeedee, Whimsicott+Incineroar@Chople Berry, Charizard+Kleavor, Sneasler+Armarouge@Life Orb, Excadrill+Armarouge@Life Orb, Incineroar+Volcarona, Hydreigon+Staraptor, Sneasler+Basculegion@Life Orb, Basculegion+Dragonite@Dragoninite, Rillaboom+Ninetales-Alola@Focus Sash, Indeedee-F+Kommo-o@Leftovers, Indeedee-F+Venusaur, Primarina+Metagross@Metagrossite, Basculegion+Dragonite, Raichu+Volcarona, Indeedee-F+Pelipper, Basculegion+Blaziken@Blazikenite, Rillaboom+Kommo-o@Leftovers, Incineroar+Ninetales-Alola, Dragonite+Indeedee-F@Psychic Seed, Rillaboom+Basculegion@Life Orb, Rillaboom+Ninetales-Alola@Light Clay, Baxcalibur+Rillaboom, Rillaboom+Talonflame@Focus Sash, Ninetales-Alola+Rillaboom
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Indeedee-F | 78.2% | follow-me 99.4%, terrain-setter 99.4%, priority-blocker 99.4%, helping-hand 85.9%, trick-room-setter 79.8%, disruption 8.3%, spa-drop 2.5%, fake-out 1.5%, status 0.7%, screens 0.7% |
| Gardevoir | 38.5% | mega-attacker 99.4%, trick-room-setter 34.8%, setup 14.8%, spa-drop 13.2%, disruption 3.9%, priority-attack 0.6% |
| Armarouge | 32.9% | wide-guard 56.0%, trick-room-setter 48.8%, trick-room-abuser 24.0%, setup 2.3%, weather-setter 1.7%, ally-switch 0.7%, helping-hand 0.7% |
| Basculegion | 28.9% | priority-attack 93.7%, pivot 43.3%, speed-drop 1.6%, weather-setter 0.2%, setup 0.1% |
| Whimsicott | 16.4% | tailwind 100.0%, prankster 99.5%, disruption 64.0%, weather-setter 16.1%, helping-hand 5.7%, screens 5.1%, terrain-setter 2.3%, trick-room-setter 1.5%, speed-drop 0.5% |
| Volcarona | 13.2% | setup 54.1%, rage-powder 49.0%, spa-drop 43.9%, tailwind 27.4%, status 1.2% |
| Torkoal | 13.0% | weather-setter 100.0%, trick-room-abuser 95.3%, helping-hand 29.5%, status 1.0% |
| Glimmora | 13.0% | mega-attacker 71.6% |
| Dragapult | 13.0% | status 84.0%, screens 4.5%, disruption 4.2%, pivot 1.9% |
| Hatterene | 11.7% | trick-room-setter 100.0%, trick-room-abuser 97.9%, spa-drop 6.4% |
| Camerupt | 10.4% | mega-attacker 100.0%, trick-room-abuser 97.1% |
| Kommo-o | 9.7% | setup 67.6%, priority-attack 2.9% |
| Metagross | 9.5% | mega-attacker 97.4%, setup 12.9%, priority-attack 8.2%, trick-room-abuser 0.5%, speed-drop 0.5% |
| Baxcalibur | 4.1% | priority-attack 87.1%, mega-attacker 80.8%, setup 51.8% |
| Pyroar | 3.9% | mega-attacker 100.0%, spa-drop 21.1%, status 5.3%, disruption 5.3% |
| Ninetales-Alola | 3.8% | weather-setter 100.0%, screens 61.2%, speed-drop 33.4%, disruption 31.9%, ally-boost 5.4%, helping-hand 2.5%, terrain-setter 2.2% |
| Annihilape | 3.8% | pivot 31.7%, speed-drop 14.9%, setup 13.3%, ally-boost 4.8%, disruption 1.5%, weather-setter 1.2% |
| Blaziken | 3.2% | mega-attacker 68.0%, ally-boost 18.4%, setup 2.5%, priority-attack 1.8% |
| Typhlosion-Hisui | 2.8% | none |
| Kleavor | 2.8% | tailwind 22.1%, pivot 20.1% |
| Pawmot | 2.8% | fake-out 63.9%, ally-boost 20.3%, priority-attack 4.2%, weather-setter 2.5%, disruption 1.8%, pivot 1.2%, speed-drop 1.1% |
| Altaria | 2.4% | status 69.5%, tailwind 63.5%, mega-attacker 25.9%, perish-song 24.8%, setup 4.6% |
| Gallade | 2.2% | trick-room-setter 61.4%, wide-guard 60.9%, mega-attacker 14.2%, setup 8.3% |
| Talonflame | 2.2% | tailwind 100.0%, gale-wings 100.0%, status 16.9%, quick-guard 8.5%, priority-blocker 8.5%, disruption 6.5%, weather-setter 2.7% |
| Hydreigon | 2.0% | spa-drop 67.8%, tailwind 7.8%, pivot 4.7%, disruption 3.2% |
| Klefki | 1.6% | prankster 100.0%, weather-setter 83.6%, screens 83.6%, speed-drop 27.7%, setup 16.4%, trick-room-setter 5.6% |
| Sirfetch’d | 1.3% | trick-room-abuser 31.6%, priority-attack 5.5%, quick-guard 5.0%, priority-blocker 5.0% |
| Lopunny | 1.3% | mega-attacker 100.0%, fake-out 74.6%, disruption 50.8% |
| Empoleon | 1.1% | priority-attack 18.9%, trick-room-abuser 16.9%, status 7.8%, speed-drop 5.5% |
| Rotom-Wash | 1.1% | status 85.9%, speed-drop 36.5%, pivot 28.2%, screens 14.1%, weather-setter 8.3% |
| Grapploct | 1.0% | trick-room-abuser 64.1%, priority-attack 52.8%, ally-boost 8.7%, disruption 8.7%, setup 7.1% |
| Meowscarada | 0.9% | pivot 42.6%, priority-attack 12.2% |
| Abomasnow | 0.9% | weather-setter 100.0%, trick-room-abuser 100.0%, mega-attacker 100.0%, screens 41.3%, priority-attack 23.5% |
| Araquanid | 0.9% | wide-guard 100.0%, trick-room-abuser 36.9%, weather-setter 18.5%, speed-drop 18.5% |
| Vanilluxe | 0.8% | weather-setter 100.0%, speed-drop 69.8%, priority-attack 19.9%, screens 16.6% |
| Alakazam | 0.8% | mega-attacker 77.6%, setup 43.8%, disruption 40.4% |
| Meowstic-F | 0.6% | mega-attacker 100.0%, fake-out 87.3%, setup 6.2% |
| Houndoom | 0.4% | mega-attacker 100.0%, setup 29.3%, status 29.3% |
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
  - [Michael Appelgate, 871st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0629/teamlist)
  - [Steven Oehler, 831st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0149/teamlist)
  - [Daniel Tautz, 49th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0397/teamlist)
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
  - [Harriet Day, 822nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1062/teamlist)
  - [Gabriel Buchta, 1038th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1094/teamlist)
  - [James Watts, 283rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0013/teamlist)
  - [kimkomsu, , 10 Sep 2026](https://pokepast.es/60002baa327ce677)
  - [etc25269248, , 13 Sep 2026](https://pokepast.es/7664efb6099c8d98)
  - [Dorian Luckie, 802nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0459/teamlist)
  - [Sarapoke0914, , 11 Sep 2026](https://pokepast.es/d4087f1527d4e0bc)
  - [Sahil, , 9 Sep 2026](https://pokepast.es/b70d72f44626b60d)
  - [humidori, , 16 Sep 2026](https://pokepast.es/472a153c121df28d)
  - [Jan-Philipp Schmitz, 905th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0555/teamlist)
  - [Kamal Saab, 752nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1074/teamlist)
  - [albin jepping, 817th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0211/teamlist)
  - [Demitrios Kaguras, 220th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1006/teamlist)
  - [Basil Hawley, 228th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0551/teamlist)
  - [Morgan Carter, 900th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1021/teamlist)
  - [Magnus Wallgren, 638th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0185/teamlist)
  - [Motochika Nabeshima, , 13 Sep 2026](https://pokepast.es/8ba4c9c260b8e9ae)
  - [Giovanni Piscitelli, Top 8, 20 Sep 2026](https://pokepast.es/ffe1c04c186b2453)
  - [David Mackinnon, 283rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0902/teamlist)
  - [Steven Van, 495th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0200/teamlist)
  - [Nikita Gnatenko, 679th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0997/teamlist)
  - [Michell Osew, 724th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0153/teamlist)
  - [Rinya Kobayashi, 322nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0121/teamlist)
  - [Mihir Desai, 152nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0171/teamlist)
  - [Brian Salyerds Jr, 440th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0145/teamlist)
  - [Omar Trejo, 137th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0187/teamlist)
  - [Michele Mattia Renda, 1041st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0540/teamlist)
  - [Niklas Margaritaru, 1078th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0621/teamlist)
  - [Mary Cook, 167th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0498/teamlist)
  - [Max Doebeli, 157th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0064/teamlist)
  - [Christoph Bley, 476th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0019/teamlist)
  - [Wolfgang Behrens, 362nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0431/teamlist)
  - [Devlin Ursu, 456th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0987/teamlist)
  - [David Leonardi, 413th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1053/teamlist)
  - [Ellie Homen, 297th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0645/teamlist)
  - [Nick-Donovan Gelhorn, 853rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0302/teamlist)
  - [Jacob Curnett, 566th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0959/teamlist)
  - [Rubén Gómez, 401st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0946/teamlist)
  - [Nikhil Rajbhandary, 443rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0848/teamlist)
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
  - [Evan Graham, 602nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0970/teamlist)
  - [Nolan Parker, 275th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0787/teamlist)
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
  - [beebee10222, , 9 Sep 2026](https://pokepast.es/376176212f88e4cb)
  - [Edgar Graf, 1098th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1015/teamlist)
  - [gravity030, , 9 Sep 2026](https://pokepast.es/2f512d91f56e0830)
  - [Angstrom Sharrard, 56th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0344/teamlist)
  - [Jake Murray, 88th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0156/teamlist)
  - [Arnout Bruijn, 1006th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0236/teamlist)
  - [Diego Müller, 854th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0500/teamlist)
  - [Ali Pütün, 742nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0084/teamlist)
  - [Adam Kemmer, 390th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0065/teamlist)
  - [thepostmanp, 8th, 10 Sep 2026](https://pokepast.es/db3ce4cbdae73dbe)
  - [Jeremy Boyd, 294th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0900/teamlist)
  - [One An An, 965th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0407/teamlist)
  - [Nick Smith, 780th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1003/teamlist)
  - [Duy Thang Nguyen, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0617/teamlist)
  - [Jayson Lyon, 837th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0516/teamlist)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Tom de Gruijter, 217th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1093/teamlist)
  - [Eric Uada, 268th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0199/teamlist)
  - [Daniel Medina, 574th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0223/teamlist)
  - [Joshua Hoitink, 92nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0053/teamlist)
  - [Patrick Verrelli, 108th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0102/teamlist)
  - [Robert Pamplin, 744th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0562/teamlist)
  - [Max Hofmann, 954th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1089/teamlist)
  - [Jean van Roij, 798th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0481/teamlist)
  - [Marcus Daniels-Days, 903rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0159/teamlist)
  - [lj_darkrai, , 18 Sep 2026](https://pokepast.es/817ae21505e679f7)
  - [Qiyuan Sun, 651st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0366/teamlist)
  - [Will Schultheis, 1075th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0512/teamlist)
  - [Heber Henriquez, 805th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0902/teamlist)
  - [Chloe Bourke, 70th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0262/teamlist)
  - [Kevin Swastek, 665th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0762/teamlist)
  - [Benjamin Wallace, 881st, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0339/teamlist)
  - [Nikita Stoller, 201st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0030/teamlist)
  - [Alec Moran, 1020th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0474/teamlist)
  - [Sirhat Renklitepe, 878th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0143/teamlist)
  - [Daniel Miguel mirapeix, 1113th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0806/teamlist)

- Sub-community pass: 482 primary teams, 157 tokens, modularity 0.62; unconnected tokens: Ceruledge (other item; Grassy Seed 3/6), Empoleon (other item; Life Orb 2/6), Scovillain@Scovillainite, Blaziken (other item; Focus Sash 3/5), Hydreigon@Choice Scarf, Rotom-Wash (other item; Sitrus Berry 2/5), Vivillon (other item; Focus Sash 4/5), Abomasnow (other item; Abomasite 4/4), Alakazam (other item; Alakazite 3/4), Corviknight (other item; Sitrus Berry 2/4), Heracross (other item; Heracronite 3/4), Hydreigon (other item; Expert Belt 2/4), Maushold (other item; Wide Lens 3/4), Politoed (other item; Choice Scarf 1/4), Sableye (other item; Black Glasses 1/4), Vanilluxe (other item; Choice Scarf 2/4), Crabominable (other item; Crabominite 3/3), Dragonite (other item; Life Orb 2/3), Drampa (other item; Drampanite 1/3), Grimmsnarl (other item; Light Clay 2/3), Persian-Alola (other item; Choice Scarf 2/3), Primarina (other item; Mystic Water 2/3), Scizor (other item; Life Orb 2/3), Staraptor (other item; Choice Scarf 3/3), Toxtricity (other item; Choice Scarf 2/3), Typhlosion-Hisui (other item; Life Orb 2/3), Zoroark-Hisui (other item; Focus Sash 2/3); unassigned within the community: 9 teams (2.0% of its primary weight); hybrid teams of the community left out: 159
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 3 (0.73) | 5 (0.67) | 6 (0.62) | 7 (0.57) | 7 (0.52) |

#### Community 3 / Sub-community 0: Psyspam (Mega Gardevoir) (216 primary teams, 156 distinct builds, top pair on 159/216)
- Megas on member teams: Gardevoir 161, Golisopod 48, Charizard-Y 28, Salamence 28, Staraptor 12, Absol-Z 10
- Top species by team share: Indeedee-F 100%, Sneasler 75%, Gardevoir 75%, Armarouge 39%, Basculegion 34%, Kingambit 31%
- Token label: Indeedee-F / Gardevoir@Gardevoirite / Sneasler@Psychic Seed
- Mode tags on primary teams: Psyspam 207, Tailwind 93, Trick Room 81, Sun 58, Rain 37, Setup 11, Sand 6, Snow 6, Screens 1
- Primary teams: 216 (41.6% of the community's primary weight), hybrid teams: 27 (5.8%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Venusaur (other item; Focus Sash 17/21)+Charizard@Charizardite Y, Araquanid (other item; Never-Melt Ice 2/4)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 27/37)+Golisopod@Golisopite, Annihilape (other item; Focus Sash 3/8)+Torkoal (other item; Charcoal 66/70), Rotom-Heat (other item; Sitrus Berry 4/6)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 27/37)+Basculegion@Choice Scarf, Venusaur (other item; Focus Sash 17/21)+Basculegion@Choice Scarf, Basculegion@Choice Scarf+Volcarona@Grassy Seed, Armarouge (other item; Life Orb 70/155)+Lucario@Lucarionite Z, Sirfetch’d (other item; Leek 3/8)+Golisopod@Golisopite, Lucario@Lucarionite Z+Sneasler@Psychic Seed, Sinistcha (other item; Sitrus Berry 7/13)+Golisopod@Golisopite, Armarouge (other item; Life Orb 70/155)+Annihilape@Choice Scarf, Basculegion@Choice Scarf+Charizard@Charizardite Y, Annihilape@Choice Scarf+Sneasler@Psychic Seed, Venusaur (other item; Focus Sash 17/21)+Sneasler@Psychic Seed, Pelipper (other item; Focus Sash 27/37)+Sneasler@Psychic Seed, Kleavor (other item; Focus Sash 8/13)+Golisopod@Golisopite, Gardevoir@Gardevoirite+Pyroar@Pyroarite, Basculegion@Choice Scarf+Sneasler@Psychic Seed, Rotom-Heat (other item; Sitrus Berry 4/6)+Gardevoir@Gardevoirite, Gardevoir@Gardevoirite+Lopunny@Lopunnite, Rotom-Heat (other item; Sitrus Berry 4/6)+Sneasler@Psychic Seed, Gardevoir@Gardevoirite+Sneasler@Psychic Seed, Venusaur (other item; Focus Sash 17/21)+Gardevoir@Gardevoirite, Gholdengo (other item; Life Orb 8/14)+Sneasler@Psychic Seed, Armarouge (other item; Life Orb 70/155)+Tyranitar@Tyranitarite, Annihilape@Choice Scarf+Gardevoir@Gardevoirite, Basculegion@Choice Scarf+Gardevoir@Gardevoirite, Whimsicott (other item; Focus Sash 54/75)+Charizard@Charizardite Y, Milotic (other item; Leftovers 17/22)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 27/37)+Gardevoir@Gardevoirite, Gardevoir@Gardevoirite+Tyranitar@Tyranitarite, Annihilape (other item; Focus Sash 3/8)+Armarouge (other item; Life Orb 70/155), Charizard@Charizardite Y+Sneasler@Psychic Seed, Annihilape (other item; Focus Sash 3/8)+Gardevoir@Gardevoirite, Basculegion@Choice Scarf+Golisopod@Golisopite, Hatterene (other item; Life Orb 51/55)+Golisopod@Golisopite, Garchomp (other item; Life Orb 25/28)+Charizard@Charizardite Y, Indeedee-F (other item; Rocky Helmet 162/323)+Sneasler@Psychic Seed, Indeedee-F (other item; Rocky Helmet 162/323)+Sylveon (other item; Fairy Feather 6/6), Indeedee-F (other item; Rocky Helmet 162/323)+Tyranitar@Tyranitarite, Indeedee-F (other item; Rocky Helmet 162/323)+Armarouge@Psychic Seed, Indeedee-F (other item; Rocky Helmet 162/323)+Gengar@Gengarite, Indeedee-F (other item; Rocky Helmet 162/323)+Milotic@Psychic Seed, Altaria (other item; Haban Berry 12/12)+Indeedee-F (other item; Rocky Helmet 162/323), Indeedee-F (other item; Rocky Helmet 162/323)+Meowstic-F (other item; Meowsticite 4/4), Indeedee-F (other item; Rocky Helmet 162/323)+Meowscarada (other item; Choice Scarf 2/4), Annihilape (other item; Focus Sash 3/8)+Indeedee-F (other item; Rocky Helmet 162/323), Indeedee-F (other item; Rocky Helmet 162/323)+Lucario@Lucarionite Z, Indeedee-F (other item; Rocky Helmet 162/323)+Lopunny@Lopunnite, Talonflame (other item; Charcoal 4/12)+Gardevoir@Gardevoirite, Armarouge (other item; Life Orb 70/155)+Golisopod@Golisopite, Charizard@Charizardite Y+Gardevoir@Gardevoirite, Absol@Absolite Z+Sneasler@Psychic Seed, Charizard@Charizardite Y+Glimmora@Glimmoranite, Dragapult (other item; Life Orb 57/57)+Indeedee-F (other item; Rocky Helmet 162/323), Kommo-o (other item; Life Orb 29/42)+Charizard@Charizardite Y, Indeedee-F (other item; Rocky Helmet 162/323)+Mawile@Mawilite, Indeedee-F (other item; Rocky Helmet 162/323)+Gardevoir@Gardevoirite, Salamence@Salamencite+Sneasler@Psychic Seed, Aerodactyl (other item; Aerodactylite 3/7)+Gardevoir@Gardevoirite, Kommo-o (other item; Life Orb 29/42)+Gardevoir@Gardevoirite, Garchomp@Garchompite Z+Sneasler@Psychic Seed, Golisopod@Golisopite+Milotic@Psychic Seed, Gholdengo (other item; Life Orb 8/14)+Indeedee-F (other item; Rocky Helmet 162/323), Armarouge (other item; Life Orb 70/155)+Indeedee-F (other item; Rocky Helmet 162/323), Indeedee-F (other item; Rocky Helmet 162/323)+Venusaur (other item; Focus Sash 17/21), Indeedee-F (other item; Rocky Helmet 162/323)+Pyroar@Pyroarite, Indeedee-F (other item; Rocky Helmet 162/323)+Staraptor@Staraptite, Indeedee-F (other item; Rocky Helmet 162/323)+Pelipper (other item; Focus Sash 27/37), Gholdengo (other item; Life Orb 8/14)+Gardevoir@Gardevoirite, Arcanine-Hisui (other item; Focus Sash 29/31)+Gardevoir@Gardevoirite, Torkoal (other item; Charcoal 66/70)+Venusaur (other item; Focus Sash 17/21), Arcanine-Hisui (other item; Focus Sash 29/31)+Indeedee-F (other item; Rocky Helmet 162/323), Golisopod@Golisopite+Staraptor@Staraptite, Sneasler (other item; White Herb 28/36)+Golisopod@Golisopite, Blastoise@Blastoisinite+Gardevoir@Gardevoirite, Dragapult (other item; Life Orb 57/57)+Golisopod@Golisopite, Indeedee-F (other item; Rocky Helmet 162/323)+Rotom-Heat (other item; Sitrus Berry 4/6), Golisopod@Golisopite+Sneasler@Psychic Seed, Indeedee-F (other item; Rocky Helmet 162/323)+Charizard@Charizardite Y, Gallade (other item; White Herb 5/11)+Indeedee-F (other item; Rocky Helmet 162/323), Torkoal (other item; Charcoal 66/70)+Gardevoir@Gardevoirite, Indeedee-F (other item; Rocky Helmet 162/323)+Golisopod@Golisopite, Indeedee-F (other item; Rocky Helmet 162/323)+Basculegion@Choice Scarf, Indeedee-F (other item; Rocky Helmet 162/323)+Annihilape@Choice Scarf, Indeedee-F (other item; Rocky Helmet 162/323)+Blastoise@Blastoisinite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Indeedee-F (other item; Rocky Helmet 162/323) | 97.9% |
| Gardevoir@Gardevoirite | 75.4% |
| Sneasler@Psychic Seed | 69.8% |
| Basculegion@Choice Scarf | 25.3% |
| Golisopod@Golisopite | 22.7% |
| Pelipper (other item; Focus Sash 27/37) | 16.7% |
| Charizard@Charizardite Y | 13.3% |
| Venusaur (other item; Focus Sash 17/21) | 7.8% |
| Gholdengo (other item; Life Orb 8/14) | 5.7% |
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

#### Community 3 / Sub-community 1: Psyspam (Mega Staraptor) (69 primary teams, 33 distinct builds, top pair on 38/69)
- Megas on member teams: Staraptor 43, Metagross 22, Gengar 15, Golisopod 14, Raichu-Y 5, Absol-Z 4
- Top species by team share: Indeedee-F 97%, Armarouge 77%, Milotic 77%, Dragapult 68%, Staraptor 62%, Metagross 32%
- Token label: Armarouge / Milotic@Psychic Seed / Dragapult
- Mode tags on primary teams: Psyspam 55, Tailwind 32, Setup 27, Trick Room 11, Sun 4, Snow 2, Screens 1
- Primary teams: 69 (15.7% of the community's primary weight), hybrid teams: 19 (3.6%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Altaria (other item; Haban Berry 12/12)+Arcanine-Hisui (other item; Focus Sash 29/31), Altaria (other item; Haban Berry 12/12)+Metagross@Metagrossite, Altaria (other item; Haban Berry 12/12)+Milotic@Psychic Seed, Gengar@Gengarite+Milotic@Psychic Seed, Altaria (other item; Haban Berry 12/12)+Dragapult (other item; Life Orb 57/57), Dragapult (other item; Life Orb 57/57)+Gengar@Gengarite, Dragapult (other item; Life Orb 57/57)+Milotic@Psychic Seed, Gengar@Gengarite+Staraptor@Staraptite, Milotic@Psychic Seed+Staraptor@Staraptite, Kleavor (other item; Focus Sash 8/13)+Metagross@Metagrossite, Arcanine-Hisui (other item; Focus Sash 29/31)+Metagross@Metagrossite, Dragapult (other item; Life Orb 57/57)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 29/31)+Milotic@Psychic Seed, Metagross@Metagrossite+Milotic@Psychic Seed, Arcanine-Hisui (other item; Focus Sash 29/31)+Dragapult (other item; Life Orb 57/57), Armarouge (other item; Life Orb 70/155)+Lucario@Lucarionite Z, Armarouge (other item; Life Orb 70/155)+Gengar@Gengarite, Dragapult (other item; Life Orb 57/57)+Metagross@Metagrossite, Armarouge (other item; Life Orb 70/155)+Annihilape@Choice Scarf, Arcanine-Hisui (other item; Focus Sash 29/31)+Kommo-o (other item; Life Orb 29/42), Armarouge (other item; Life Orb 70/155)+Staraptor@Staraptite, Armarouge (other item; Life Orb 70/155)+Milotic@Psychic Seed, Armarouge (other item; Life Orb 70/155)+Grapploct (other item; Psychic Seed 2/5), Armarouge (other item; Life Orb 70/155)+Mawile@Mawilite, Armarouge (other item; Life Orb 70/155)+Dragapult (other item; Life Orb 57/57), Armarouge (other item; Life Orb 70/155)+Tyranitar@Tyranitarite, Armarouge (other item; Life Orb 70/155)+Sirfetch’d (other item; Leek 3/8), Annihilape (other item; Focus Sash 3/8)+Armarouge (other item; Life Orb 70/155), Armarouge (other item; Life Orb 70/155)+Blastoise@Blastoisinite, Indeedee-F (other item; Rocky Helmet 162/323)+Gengar@Gengarite, Indeedee-F (other item; Rocky Helmet 162/323)+Milotic@Psychic Seed, Altaria (other item; Haban Berry 12/12)+Indeedee-F (other item; Rocky Helmet 162/323), Dragapult (other item; Life Orb 57/57)+Milotic (other item; Leftovers 17/22), Armarouge (other item; Life Orb 70/155)+Golisopod@Golisopite, Armarouge (other item; Life Orb 70/155)+Absol@Absolite Z, Armarouge (other item; Life Orb 70/155)+Sinistcha (other item; Sitrus Berry 7/13), Absol@Absolite Z+Sneasler@Psychic Seed, Dragapult (other item; Life Orb 57/57)+Indeedee-F (other item; Rocky Helmet 162/323), Armarouge (other item; Life Orb 70/155)+Gallade (other item; White Herb 5/11), Golisopod@Golisopite+Milotic@Psychic Seed, Armarouge (other item; Life Orb 70/155)+Indeedee-F (other item; Rocky Helmet 162/323), Indeedee-F (other item; Rocky Helmet 162/323)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 29/31)+Gardevoir@Gardevoirite, Garchomp (other item; Life Orb 25/28)+Staraptor@Staraptite, Arcanine-Hisui (other item; Focus Sash 29/31)+Indeedee-F (other item; Rocky Helmet 162/323), Golisopod@Golisopite+Staraptor@Staraptite, Armarouge (other item; Life Orb 70/155)+Torkoal (other item; Charcoal 66/70), Whimsicott (other item; Focus Sash 54/75)+Staraptor@Staraptite, Sneasler (other item; White Herb 28/36)+Metagross@Metagrossite, Dragapult (other item; Life Orb 57/57)+Golisopod@Golisopite, Armarouge (other item; Life Orb 70/155)+Raichu@Raichunite Y
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Armarouge (other item; Life Orb 70/155) | 78.2% |
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
  - [itsplebian, , 22 Sep 2026](https://pokepast.es/e2b7674a54debee7)
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
- Primary teams: 78 (16.7% of the community's primary weight), hybrid teams: 7 (1.6%)
- Date range: 2026-09-09 to 2026-09-27
- Core pairs: Glimmora (other item; Focus Sash 12/13)+Blaziken@Blazikenite, Farigiraf (other item; Sitrus Berry 31/40)+Camerupt@Cameruptite, Hatterene (other item; Life Orb 51/55)+Camerupt@Cameruptite, Sirfetch’d (other item; Leek 3/8)+Camerupt@Cameruptite, Farigiraf (other item; Sitrus Berry 31/40)+Hatterene (other item; Life Orb 51/55), Torkoal (other item; Charcoal 66/70)+Blaziken@Blazikenite, Farigiraf (other item; Sitrus Berry 31/40)+Indeedee-F@Psychic Seed, Hatterene (other item; Life Orb 51/55)+Sirfetch’d (other item; Leek 3/8), Camerupt@Cameruptite+Indeedee-F@Psychic Seed, Farigiraf (other item; Sitrus Berry 31/40)+Incineroar (other item; Sitrus Berry 29/73), Hatterene (other item; Life Orb 51/55)+Blaziken@Blazikenite, Incineroar (other item; Sitrus Berry 29/73)+Camerupt@Cameruptite, Hatterene (other item; Life Orb 51/55)+Indeedee-F@Psychic Seed, Gallade (other item; White Herb 5/11)+Hatterene (other item; Life Orb 51/55), Annihilape (other item; Focus Sash 3/8)+Torkoal (other item; Charcoal 66/70), Gallade (other item; White Herb 5/11)+Camerupt@Cameruptite, Torkoal (other item; Charcoal 66/70)+Mawile@Mawilite, Incineroar (other item; Sitrus Berry 29/73)+Volcarona@Grassy Seed, Blaziken@Blazikenite+Indeedee-F@Psychic Seed, Sirfetch’d (other item; Leek 3/8)+Golisopod@Golisopite, Hatterene (other item; Life Orb 51/55)+Incineroar (other item; Sitrus Berry 29/73), Incineroar (other item; Sitrus Berry 29/73)+Indeedee-F@Psychic Seed, Glimmora (other item; Focus Sash 12/13)+Hatterene (other item; Life Orb 51/55), Kingambit (other item; Chople Berry 48/140)+Dragonite@Dragoninite, Kingambit (other item; Chople Berry 48/140)+Garchomp@Choice Scarf, Gallade (other item; White Herb 5/11)+Torkoal (other item; Charcoal 66/70), Sneasler (other item; White Herb 28/36)+Indeedee-F@Psychic Seed, Hatterene (other item; Life Orb 51/55)+Torkoal (other item; Charcoal 66/70), Incineroar (other item; Sitrus Berry 29/73)+Blastoise@Blastoisinite, Farigiraf (other item; Sitrus Berry 31/40)+Kingambit (other item; Chople Berry 48/140), Armarouge (other item; Life Orb 70/155)+Mawile@Mawilite, Glimmora (other item; Focus Sash 12/13)+Whimsicott (other item; Focus Sash 54/75), Kingambit (other item; Chople Berry 48/140)+Camerupt@Cameruptite, Glimmora (other item; Focus Sash 12/13)+Kingambit (other item; Chople Berry 48/140), Torkoal (other item; Charcoal 66/70)+Indeedee-F@Psychic Seed, Sneasler (other item; White Herb 28/36)+Torkoal (other item; Charcoal 66/70), Armarouge (other item; Life Orb 70/155)+Sirfetch’d (other item; Leek 3/8), Kingambit (other item; Chople Berry 48/140)+Blastoise@Blastoisinite, Hatterene (other item; Life Orb 51/55)+Kingambit (other item; Chople Berry 48/140), Kingambit (other item; Chople Berry 48/140)+Indeedee-F@Psychic Seed, Kingambit (other item; Chople Berry 48/140)+Salamence@Salamencite, Kingambit (other item; Chople Berry 48/140)+Talonflame (other item; Charcoal 4/12), Kingambit (other item; Chople Berry 48/140)+Garchomp@Garchompite Z, Hatterene (other item; Life Orb 51/55)+Golisopod@Golisopite, Kingambit (other item; Chople Berry 48/140)+Volcarona (other item; Rocky Helmet 19/47), Kingambit (other item; Chople Berry 48/140)+Torkoal (other item; Charcoal 66/70), Incineroar (other item; Sitrus Berry 29/73)+Kingambit (other item; Chople Berry 48/140), Indeedee-F (other item; Rocky Helmet 162/323)+Mawile@Mawilite, Incineroar (other item; Sitrus Berry 29/73)+Rillaboom (other item; Miracle Seed 36/45), Armarouge (other item; Life Orb 70/155)+Gallade (other item; White Herb 5/11), Kingambit (other item; Chople Berry 48/140)+Rillaboom (other item; Miracle Seed 36/45), Farigiraf (other item; Sitrus Berry 31/40)+Torkoal (other item; Charcoal 66/70), Hatterene (other item; Life Orb 51/55)+Raichu@Raichunite Y, Kingambit (other item; Chople Berry 48/140)+Glimmora@Glimmoranite, Torkoal (other item; Charcoal 66/70)+Venusaur (other item; Focus Sash 17/21), Armarouge (other item; Life Orb 70/155)+Torkoal (other item; Charcoal 66/70), Kingambit (other item; Chople Berry 48/140)+Blaziken@Blazikenite, Basculegion (other item; Mystic Water 33/74)+Kingambit (other item; Chople Berry 48/140), Kingambit (other item; Chople Berry 48/140)+Baxcalibur@Baxcalibrite, Incineroar (other item; Sitrus Berry 29/73)+Glimmora@Glimmoranite, Gallade (other item; White Herb 5/11)+Indeedee-F (other item; Rocky Helmet 162/323), Kingambit (other item; Chople Berry 48/140)+Ninetales-Alola (other item; Never-Melt Ice 7/11), Torkoal (other item; Charcoal 66/70)+Gardevoir@Gardevoirite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Hatterene (other item; Life Orb 51/55) | 63.9% |
| Camerupt@Cameruptite | 59.6% |
| Indeedee-F@Psychic Seed | 58.6% |
| Kingambit (other item; Chople Berry 48/140) | 50.5% |
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
  - [Diego Ferreira, 12th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0061/teamlist)
  - [John Talbot, 169th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0060/teamlist)
  - [Christopher Chau, 685th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0540/teamlist)

#### Community 3 / Sub-community 3: Psyspam · Whimsicott (52 primary teams, 33 distinct builds, top pair on 34/52)
- Megas on member teams: Gardevoir 19, Pyroar 17, Glimmora 12, Charizard-Y 6, Metagross 6, Golisopod 5
- Top species by team share: Whimsicott 85%, Basculegion 73%, Kommo-o 52%, Indeedee-F 44%, Gardevoir 37%, Pyroar 33%
- Token label: Whimsicott / Basculegion / Kommo-o
- Mode tags on primary teams: Tailwind 47, Psyspam 27, Sun 9, Setup 3, Snow 2, Rain 1, Sand 1, Trick Room 1
- Primary teams: 52 (11.8% of the community's primary weight), hybrid teams: 6 (1.3%)
- Date range: 2026-09-10 to 2026-09-27
- Core pairs: Araquanid (other item; Never-Melt Ice 2/4)+Kleavor (other item; Focus Sash 8/13), Araquanid (other item; Never-Melt Ice 2/4)+Volcarona (other item; Rocky Helmet 19/47), Kommo-o (other item; Life Orb 29/42)+Pyroar@Pyroarite, Indeedee (other item; Focus Sash 4/8)+Glimmora@Glimmoranite, Indeedee (other item; Focus Sash 4/8)+Kommo-o (other item; Life Orb 29/42), Basculegion (other item; Mystic Water 33/74)+Pyroar@Pyroarite, Araquanid (other item; Never-Melt Ice 2/4)+Golisopod@Golisopite, Whimsicott (other item; Focus Sash 54/75)+Pyroar@Pyroarite, Kleavor (other item; Focus Sash 8/13)+Metagross@Metagrossite, Delphox (other item; Delphoxite 4/5)+Whimsicott (other item; Focus Sash 54/75), Indeedee (other item; Focus Sash 4/8)+Whimsicott (other item; Focus Sash 54/75), Whimsicott (other item; Focus Sash 54/75)+Typhlosion-Hisui@Choice Scarf, Kommo-o (other item; Life Orb 29/42)+Whimsicott (other item; Focus Sash 54/75), Basculegion (other item; Mystic Water 33/74)+Floette-Eternal@Floettite, Rillaboom (other item; Miracle Seed 36/45)+Garchomp@Garchompite Z, Basculegion (other item; Mystic Water 33/74)+Kommo-o (other item; Life Orb 29/42), Basculegion (other item; Mystic Water 33/74)+Indeedee (other item; Focus Sash 4/8), Kleavor (other item; Focus Sash 8/13)+Volcarona (other item; Rocky Helmet 19/47), Basculegion (other item; Mystic Water 33/74)+Whimsicott (other item; Focus Sash 54/75), Basculegion (other item; Mystic Water 33/74)+Kleavor (other item; Focus Sash 8/13), Arcanine-Hisui (other item; Focus Sash 29/31)+Kommo-o (other item; Life Orb 29/42), Kleavor (other item; Focus Sash 8/13)+Golisopod@Golisopite, Basculegion (other item; Mystic Water 33/74)+Baxcalibur@Baxcalibrite, Gardevoir@Gardevoirite+Pyroar@Pyroarite, Basculegion (other item; Mystic Water 33/74)+Talonflame (other item; Charcoal 4/12), Basculegion (other item; Mystic Water 33/74)+Glimmora@Glimmoranite, Whimsicott (other item; Focus Sash 54/75)+Glimmora@Glimmoranite, Glimmora (other item; Focus Sash 12/13)+Whimsicott (other item; Focus Sash 54/75), Garchomp (other item; Life Orb 25/28)+Whimsicott (other item; Focus Sash 54/75), Basculegion (other item; Mystic Water 33/74)+Volcarona@Grassy Seed, Kommo-o (other item; Life Orb 29/42)+Glimmora@Glimmoranite, Kleavor (other item; Focus Sash 8/13)+Whimsicott (other item; Focus Sash 54/75), Whimsicott (other item; Focus Sash 54/75)+Charizard@Charizardite Y, Whimsicott (other item; Focus Sash 54/75)+Garchomp@Garchompite Z, Basculegion (other item; Mystic Water 33/74)+Garchomp@Garchompite Z, Whimsicott (other item; Focus Sash 54/75)+Raichu@Raichunite Y, Basculegion (other item; Mystic Water 33/74)+Rillaboom (other item; Miracle Seed 36/45), Kingambit (other item; Chople Berry 48/140)+Garchomp@Garchompite Z, Kommo-o (other item; Life Orb 29/42)+Charizard@Charizardite Y, Basculegion (other item; Mystic Water 33/74)+Volcarona (other item; Rocky Helmet 19/47), Basculegion (other item; Mystic Water 33/74)+Salamence@Salamencite, Kommo-o (other item; Life Orb 29/42)+Gardevoir@Gardevoirite, Garchomp@Garchompite Z+Sneasler@Psychic Seed, Basculegion (other item; Mystic Water 33/74)+Raichu@Raichunite Y, Indeedee-F (other item; Rocky Helmet 162/323)+Pyroar@Pyroarite, Basculegion (other item; Mystic Water 33/74)+Kingambit (other item; Chople Berry 48/140), Whimsicott (other item; Focus Sash 54/75)+Staraptor@Staraptite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Whimsicott (other item; Focus Sash 54/75) | 84.8% |
| Basculegion (other item; Mystic Water 33/74) | 67.5% |
| Kommo-o (other item; Life Orb 29/42) | 50.9% |
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

- Minor sub-communities (fewer than subMinDistinctBuilds distinct builds, or no top pair and no mode tag on subMinSharedCoverage of their primary teams): Sub-community 4: Rillaboom / Glimmora@Glimmoranite (38 distinct builds, 57 primary teams, top pair on 20/57); Sub-community 5: Blastoise@Blastoisinite / Sinistcha (1 distinct build, 1 primary team, top pair on 1/1)

#### Community 3 / Token homes and where their teams go
Species whose variants fall in at least two sub-communities: each variant's home (the sub-community its token belongs to), its team count, and the primary sub-community of each of those teams (id: teams).
| Species | Variant | Home sub-community | Teams | Teams by sub-community |
| :--- | :--- | :--- | :--- | :--- |
| Indeedee-F | Indeedee-F (other item; Rocky Helmet 162/323) | Sub-community 0: Psyspam (Mega Gardevoir) | 323 | 0: 212 · 1: 64 · 2: 23 · 3: 22 · 4: 1 · unassigned: 1 |
| Indeedee-F | Indeedee-F@Psychic Seed | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 60 | 2: 45 · 0: 4 · 1: 3 · 4: 2 · 3: 1 · 5: 1 · unassigned: 4 |
| Armarouge | Armarouge (other item; Life Orb 70/155) | Sub-community 1: Psyspam (Mega Staraptor) | 155 | 0: 80 · 1: 53 · 2: 17 · 4: 3 · 5: 1 · unassigned: 1 |
| Armarouge | Armarouge@Psychic Seed | Sub-community 0: Psyspam (Mega Gardevoir) | 6 | 0: 4 · 2: 2 · unassigned: 0 |
| Sneasler | Sneasler@Psychic Seed | Sub-community 0: Psyspam (Mega Gardevoir) | 155 | 0: 153 · 2: 2 · unassigned: 0 |
| Sneasler | Sneasler (other item; White Herb 28/36) | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 36 | 4: 13 · 0: 10 · 2: 6 · 1: 5 · 3: 2 · unassigned: 0 |
| Basculegion | Basculegion (other item; Mystic Water 33/74) | Sub-community 3: Psyspam · Whimsicott | 74 | 3: 35 · 0: 18 · 4: 16 · 2: 3 · 1: 2 · unassigned: 0 |
| Basculegion | Basculegion@Choice Scarf | Sub-community 0: Psyspam (Mega Gardevoir) | 70 | 0: 55 · 4: 9 · 3: 3 · 1: 1 · 2: 1 · unassigned: 1 |
| Milotic | Milotic@Psychic Seed | Sub-community 1: Psyspam (Mega Staraptor) | 51 | 1: 51 · unassigned: 0 |
| Milotic | Milotic (other item; Leftovers 17/22) | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 22 | 0: 11 · 2: 4 · 4: 4 · 1: 2 · unassigned: 1 |
| Glimmora | Glimmora@Glimmoranite | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 46 | 4: 30 · 3: 12 · 0: 3 · 2: 1 · unassigned: 0 |
| Glimmora | Glimmora (other item; Focus Sash 12/13) | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 13 | 2: 5 · 1: 3 · 3: 3 · 0: 1 · 4: 1 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 25/28) | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 28 | 0: 14 · 4: 9 · 3: 3 · 1: 1 · 2: 1 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 3: Psyspam · Whimsicott | 13 | 0: 5 · 3: 4 · 4: 4 · unassigned: 0 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 10 | 4: 6 · 2: 3 · 0: 1 · unassigned: 0 |

### Community 4: Rain
- Token label: Archaludon / Farigiraf
- Mode tags on primary teams: Rain 442, Tailwind 346, Sun 251, Trick Room 165, Screens 149, Perish Trap 96, Psyspam 36, Setup 32, Snow 25, Sand 6
- Megas on primary teams: Golisopod 272, Charizard-Y 236, Swampert 112, Gengar 105, Garchomp-Z 75, Salamence 51, Aerodactyl 45, Floette 23, Froslass 21, Raichu-Y 21, Metagross 15, Dragonite 13, Mawile 13, Camerupt 12, Glimmora 12, Meganium 10, Gardevoir 9, Lucario-Z 9, Venusaur 9, Staraptor 7, Blaziken 6, Raichu-X 6, Absol-Z 5, Ampharos 4, Blastoise 4, Manectric 4, Scovillain 4, Baxcalibur 3, Pyroar 3, Starmie 3, Charizard-X 2, Delphox 2, Drampa 2, Kangaskhan 2, Scizor 2, Tyranitar 2, Audino 1, Beedrill 1, Chandelure 1, Dragalge 1, Feraligatr 1, Garchomp 1, Greninja 1, Lopunny 1, Meowstic-F 1, Sableye 1, Steelix 1
- Primary teams: 654 (primary share 22.6%), hybrid teams: 139 (hybrid share 4.5%)
- Date range: 2026-09-09 to 2026-10-01
- Core pairs: Aegislash+Venusaur@Life Orb, Toxapex+Venusaur@Life Orb, Vivillon+Incineroar@Passho Berry, Vivillon+Politoed@Sitrus Berry, Vivillon+Rillaboom@Eject Button, Klefki+Garchomp@Life Orb, Toxapex+Garchomp@Choice Scarf, Venusaur+Aegislash@Focus Sash, Gengar+Incineroar@Lum Berry, Gengar+Rillaboom@Eject Button, Gengar+Vivillon@Focus Sash, Vivillon+Gengar@Gengarite, Gengar+Vivillon, Gengar+Politoed@Sitrus Berry, Aegislash+Venusaur, Grimmsnarl+Politoed@Mystic Water, Mawile+Farigiraf@Colbur Berry, Aerodactyl+Tsareena, Starmie+Pelipper@Focus Sash, Gengar+Incineroar@Passho Berry, Venusaur+Toxapex@Leftovers, Aerodactyl+Garchomp@Life Orb, Swampert+Sableye@Light Clay, Politoed+Vivillon@Focus Sash, Politoed+Vivillon, Aerodactyl+Kingambit@Focus Sash, Pawmot+Politoed@Life Orb, Grimmsnarl+Venusaur@Focus Sash, Toxapex+Venusaur, Politoed+Rillaboom@Eject Button, Politoed+Incineroar@Passho Berry, Venusaur+Pelipper@Sitrus Berry, Sinistcha+Sableye@Roseli Berry, Pelipper+Archaludon@Magnet, Sableye+Swampert@Swampertite, Grimmsnarl+Pelipper@Sitrus Berry, Swampert+Pelipper@Sitrus Berry, Sableye+Swampert, Charizard+Venusaur@Focus Sash, Swampert+Venusaur@Focus Sash, Pelipper+Swampert@Swampertite, Gengar+Dragonite@Life Orb, Politoed+Gengar@Gengarite, Gengar+Politoed, Charizard+Venusaur@Wide Lens, Starmie+Sneasler@Psychic Seed, Venusaur+Grimmsnarl@Light Clay, Venusaur+Charizard@Charizardite Y, Pelipper+Swampert, Charizard+Venusaur, Grimmsnarl+Venusaur, Golisopod+Politoed@Mystic Water, Archaludon+Klefki@Light Clay, Charizard+Venusaur@Life Orb, Gengar+Kommo-o@Leftovers, Pelipper+Starmie, Politoed+Staraptor@Choice Scarf, Golisopod+Pelipper@Choice Scarf, Archaludon+Politoed@Mystic Water, Pelipper+Archaludon@Chople Berry, Golisopod+Politoed@Life Orb, Mawile+Torkoal@Charcoal, Vivillon+Rillaboom@Occa Berry, Meganium+Pelipper, Pelipper+Meganium@Meganiumite, Farigiraf+Politoed@Life Orb, Swampert+Pelipper@Focus Sash, Garchomp+Aegislash@Focus Sash, Garchomp+Klefki@Light Clay, Torkoal+Farigiraf@Grassy Seed, Charizard+Toxapex@Leftovers, Charizard+Aerodactyl@Aerodactylite, Grimmsnarl+Archaludon@Leftovers, Archaludon+Pelipper@Choice Scarf, Grimmsnarl+Swampert@Swampertite, Archaludon+Pelipper@Damp Rock, Archaludon+Swampert@Swampertite, Charizard+Politoed@Mystic Water, Venusaur+Swampert@Swampertite, Mawile+Torkoal, Torkoal+Mawile@Mawilite, Archaludon+Politoed@Choice Scarf, Archaludon+Grimmsnarl@Light Clay, Camerupt+Farigiraf@Colbur Berry, Archaludon+Grimmsnarl, Klefki+Archaludon@Leftovers, Politoed+Archaludon@Leftovers, Pelipper+Venusaur@Focus Sash, Farigiraf+Incineroar@White Herb, Sylveon+Aerodactyl@Aerodactylite, Primarina+Farigiraf@Grassy Seed, Swampert+Archaludon@Leftovers, Archaludon+Pelipper@Sitrus Berry, Toxapex+Charizard@Charizardite Y, Basculegion+Pelipper@Choice Scarf, Sylveon+Aegislash@Focus Sash, Grimmsnarl+Swampert, Swampert+Grimmsnarl@Light Clay, Charizard+Toxapex, Farigiraf+Politoed@Mystic Water, Archaludon+Swampert, Starmie+Archaludon@Leftovers, Venusaur+Garchomp@Choice Scarf, Archaludon+Politoed, Swampert+Venusaur, Archaludon+Sableye@Light Clay, Archaludon+Klefki, Archaludon+Politoed@Sitrus Berry, Archaludon+Pelipper, Archaludon+Pelipper@Focus Sash, Abomasnow+Farigiraf, Farigiraf+Abomasnow@Abomasite, Aerodactyl+Charizard@Charizardite Y, Charizard+Grimmsnarl@Light Clay, Politoed+Grimmsnarl@Light Clay, Grimmsnarl+Charizard@Charizardite Y, Camerupt+Farigiraf, Farigiraf+Camerupt@Cameruptite, Charizard+Aegislash@Focus Sash, Garchomp+Toxapex@Leftovers, Pelipper+Archaludon@Leftovers, Gengar+Milotic@Psychic Seed, Aerodactyl+Charizard, Grapploct+Farigiraf@Sitrus Berry, Charizard+Grimmsnarl, Archaludon+Starmie, Grimmsnarl+Politoed, Camerupt+Farigiraf@Sitrus Berry, Gengar+Armarouge@Focus Sash, Farigiraf+Staraptor@Choice Scarf, Charizard+Garchomp@Choice Scarf, Meowstic-F+Charizard@Charizardite Y, Aerodactyl+Sylveon@Fairy Feather, Venusaur+Annihilape@Choice Scarf, Gengar+Incineroar@Chople Berry, Sableye+Pelipper@Focus Sash, Charizard+Meowstic-F, Charizard+Meowstic-F@Meowsticite, Garchomp+Klefki, Tyranitar+Garchomp@Garchompite, Charizard+Garchomp@Life Orb, Golisopod+Sableye@Light Clay, Vivillon+Archaludon@Leftovers, Whimsicott+Garchomp@Sitrus Berry, Grimmsnarl+Politoed@Life Orb, Aerodactyl+Sylveon, Indeedee-F+Starmie, Araquanid+Golisopod@Golisopite, Garchomp+Toxapex, Araquanid+Golisopod, Pelipper+Grimmsnarl@Light Clay, Aegislash+Garchomp, Golisopod+Grimmsnarl@Light Clay, Pelipper+Sableye@Light Clay, Politoed+Incineroar@Chople Berry, Sylveon+Venusaur@Life Orb, Grimmsnarl+Pelipper, Archaludon+Vivillon@Focus Sash, Indeedee-F+Starmie@Starminite, Grimmsnarl+Golisopod@Golisopite, Archaludon+Pelipper@Life Orb, Golisopod+Armarouge@Psychic Seed, Golisopod+Grimmsnarl, Archaludon+Rillaboom@Eject Button, Archaludon+Manectric, Archaludon+Manectric@Manectite, Swampert+Sinistcha@Sitrus Berry, Golisopod+Sirfetch’d@Leek, Archaludon+Vivillon, Archaludon+Venusaur@Focus Sash, Kommo-o+Gengar@Gengarite, Ampharos+Farigiraf, Farigiraf+Ampharos@Ampharosite, Golisopod+Armarouge@Twisted Spoon, Gengar+Kommo-o, Whimsicott+Garchomp@Life Orb, Meganium+Indeedee-F@Rocky Helmet, Farigiraf+Mawile, Farigiraf+Mawile@Mawilite, Aegislash+Sylveon@Fairy Feather, Altaria+Gengar@Gengarite, Farigiraf+Kangaskhan, Altaria+Gengar, Aegislash+Sylveon, Aerodactyl+Farigiraf@Sitrus Berry, Toxapex+Incineroar@Sitrus Berry, Politoed+Archaludon@Chople Berry, Farigiraf+Incineroar@Life Orb, Aerodactyl+Lucario@Lucarionite Z, Golisopod+Pelipper@Focus Sash, Aerodactyl+Lucario, Pelipper+Venusaur, Archaludon+Incineroar@Passho Berry, Farigiraf+Primarina@Life Orb, Aegislash+Charizard@Charizardite Y, Farigiraf+Aerodactyl@Aerodactylite, Farigiraf+Incineroar@Expert Belt, Pelipper+Sableye@Roseli Berry, Aegislash+Charizard, Sableye+Archaludon@Leftovers, Garchomp+Aerodactyl@Aerodactylite, Archaludon+Sableye, Garchomp+Venusaur@Life Orb, Golisopod+Pelipper@Life Orb, Archaludon+Politoed@Life Orb, Aegislash+Garchomp@Garchompite Z, Gardevoir+Garchomp@Sitrus Berry, Politoed+Golisopod@Golisopite, Golisopod+Politoed, Pelipper+Venusaur@Venusaurite, Swampert+Farigiraf@Colbur Berry, Archaludon+Sableye@Roseli Berry, Politoed+Farigiraf@Sitrus Berry, Farigiraf+Grapploct, Annihilape+Venusaur@Focus Sash, Swampert+Annihilape@Choice Scarf, Golisopod+Kleavor@Choice Scarf, Pelipper+Golisopod@Golisopite, Golisopod+Pelipper, Charizard+Pelipper@Sitrus Berry, Aerodactyl+Garchomp, Sirfetch’d+Golisopod@Golisopite, Golisopod+Sirfetch’d, Farigiraf+Kingambit@Focus Sash, Charizard+Kingambit@Focus Sash, Annihilape+Venusaur, Sableye+Sinistcha, Golisopod+Archaludon@Leftovers, Primarina+Farigiraf@Colbur Berry, Volcarona+Garchomp@Garchompite Z, Pelipper+Sableye, Archaludon+Golisopod@Golisopite, Archaludon+Golisopod, Gengar+Dragapult@Life Orb, Incineroar+Farigiraf@Twisted Spoon, Indeedee+Venusaur@Life Orb, Gardevoir+Aerodactyl@Focus Sash, Venusaur+Archaludon@Leftovers, Grimmsnarl+Farigiraf@Sitrus Berry, Golisopod+Staraptor@Choice Scarf, Garchomp+Aerodactyl@Focus Sash, Golisopod+Farigiraf@Sitrus Berry, Sylveon+Toxapex@Leftovers, Baxcalibur+Farigiraf@Colbur Berry, Golisopod+Rotom-Heat@Sitrus Berry, Charizard+Garchomp@Sitrus Berry, Gengar+Archaludon@Leftovers, Golisopod+Farigiraf@Grassy Seed, Grimmsnarl+Sinistcha@Sitrus Berry, Dragapult+Gengar@Gengarite, Farigiraf+Venusaur@Venusaurite, Pelipper+Farigiraf@Colbur Berry, Dragapult+Gengar, Archaludon+Gengar@Gengarite, Farigiraf+Golisopod, Farigiraf+Golisopod@Golisopite, Archaludon+Gengar, Sylveon+Garchomp@Life Orb, Archaludon+Venusaur, Farigiraf+Sirfetch’d@Leek, Garchomp+Venusaur@Wide Lens, Toxapex+Sylveon@Fairy Feather, Venusaur+Indeedee@Focus Sash, Garchomp+Kingambit@Focus Sash, Hatterene+Farigiraf@Sitrus Berry, Aerodactyl+Farigiraf, Incineroar+Toxapex@Leftovers, Pelipper+Basculegion@Choice Scarf, Golisopod+Farigiraf@Colbur Berry, Incineroar+Aegislash@Focus Sash, Archaludon+Raichu@Raichunite X, Farigiraf+Hatterene@Life Orb, Sylveon+Toxapex, Pelipper+Sinistcha@Sitrus Berry, Golisopod+Pelipper@Sitrus Berry, Farigiraf+Incineroar@Leftovers, Garchomp+Whimsicott@Occa Berry, Charizard+Farigiraf@Sitrus Berry, Incineroar+Vivillon, Farigiraf+Hatterene, Absol+Aerodactyl, Aerodactyl+Absol@Absolite Z, Basculegion+Meganium, Basculegion+Meganium@Meganiumite, Incineroar+Vivillon@Focus Sash, Annihilape+Swampert@Swampertite, Incineroar+Toxapex, Archaludon+Venusaur@Venusaurite, Swampert+Volcarona@Rocky Helmet, Typhlosion-Hisui+Garchomp@Garchompite Z, Whimsicott+Garchomp@Choice Scarf, Sirfetch’d+Farigiraf@Sitrus Berry, Charizard+Whimsicott@Focus Sash, Glimmora+Garchomp@Life Orb, Garchomp+Rotom-Wash, Gengar+Rillaboom@Occa Berry, Farigiraf+Politoed, Armarouge+Mawile, Armarouge+Mawile@Mawilite, Farigiraf+Grimmsnarl@Light Clay, Incineroar+Politoed@Sitrus Berry, Garchomp+Charizard@Charizardite Y, Lucario+Aerodactyl@Aerodactylite, Grapploct+Golisopod@Golisopite, Pelipper+Rillaboom@Expert Belt, Charizard+Aerodactyl@Focus Sash, Golisopod+Grapploct, Charizard+Garchomp, Aerodactyl+Garchomp@Choice Scarf, Golisopod+Incineroar@Chople Berry, Farigiraf+Grimmsnarl, Golisopod+Sinistcha@Kasib Berry, Annihilape+Swampert, Talonflame+Garchomp@Life Orb, Charizard+Indeedee@Focus Sash, Golisopod+Archaludon@Chople Berry, Swampert+Politoed@Sitrus Berry, Charizard+Whimsicott@Occa Berry, Scovillain+Pelipper@Focus Sash, Mawile+Farigiraf@Sitrus Berry, Farigiraf+Meganium, Farigiraf+Meganium@Meganiumite, Kingambit+Aerodactyl@Aerodactylite, Incineroar+Gengar@Gengarite, Gengar+Incineroar, Sylveon+Garchomp@Choice Scarf, Garchomp+Volcarona@Grassy Seed, Glimmora+Garchomp@Choice Scarf, Charizard+Whimsicott, Whimsicott+Charizard@Charizardite Y, Pelipper+Raichu@Raichunite X, Torkoal+Farigiraf@Colbur Berry, Golisopod+Rillaboom@Leftovers, Politoed+Kommo-o@Leftovers, Farigiraf+Sirfetch’d, Kommo-o+Politoed@Sitrus Berry, Charizard+Archaludon@Leftovers, Ampharos+Incineroar, Incineroar+Ampharos@Ampharosite, Politoed+Charizard@Charizardite Y, Farigiraf+Torkoal@Charcoal, Sableye+Golisopod@Golisopite, Farigiraf+Charizard@Charizardite Y, Lucario+Garchomp@Garchompite Z, Golisopod+Sableye, Farigiraf+Primarina, Charizard+Politoed, Farigiraf+Torkoal, Farigiraf+Incineroar@Chople Berry, Garchomp+Farigiraf@Grassy Seed, Sylveon+Aerodactyl@Focus Sash, Charizard+Farigiraf, Farigiraf+Garchomp@Life Orb, Gardevoir+Venusaur@Focus Sash, Swampert+Gengar@Gengarite, Archaludon+Charizard@Charizardite Y, Gengar+Swampert, Garchomp+Whimsicott@Focus Sash, Grimmsnarl+Pelipper@Focus Sash, Archaludon+Farigiraf@Sitrus Berry, Golisopod+Swampert@Swampertite, Archaludon+Charizard, Garchomp+Whimsicott, Froslass+Politoed@Sitrus Berry, Garchomp+Volcarona@Rocky Helmet, Sylveon+Farigiraf@Sitrus Berry, Basculegion+Pelipper@Focus Sash, Hatterene+Golisopod@Golisopite, Garchomp+Corviknight@Leftovers, Golisopod+Hatterene, Kingambit+Garchomp@Life Orb, Aerodactyl+Kingambit, Kingambit+Garchomp@Garchompite, Aegislash+Incineroar@Sitrus Berry, Pelipper+Annihilape@Choice Scarf, Metagross+Garchomp@Garchompite Z, Swampert+Golisopod@Golisopite, Golisopod+Swampert, Garchomp+Kleavor@Focus Sash, Farigiraf+Blaziken@Blazikenite, Politoed+Sableye, Floette-Eternal+Garchomp@Sitrus Berry, Incineroar+Garchomp@Garchompite, Indeedee-F+Aerodactyl@Focus Sash, Archaludon+Volcarona@Sitrus Berry, Charizard+Annihilape@Choice Scarf, Garchomp+Volcarona, Politoed+Rillaboom@Occa Berry, Hydreigon+Pelipper@Sitrus Berry, Sinistcha+Swampert@Swampertite, Farigiraf+Archaludon@Leftovers, Venusaur+Torkoal@Charcoal, Golisopod+Basculegion@Choice Scarf, Charizard+Politoed@Life Orb, Garchomp+Glimmora@Focus Sash, Golisopod+Sinistcha@Sitrus Berry, Archaludon+Annihilape@Choice Scarf, Golisopod+Hatterene@Life Orb, Archaludon+Incineroar@Chople Berry, Archaludon+Meganium, Archaludon+Meganium@Meganiumite, Floette-Eternal+Garchomp@Choice Scarf, Garchomp+Venusaur, Garchomp+Typhlosion-Hisui@Choice Scarf, Pelipper+Gholdengo@Choice Scarf, Golisopod+Indeedee-F@Colbur Berry, Mawile+Indeedee-F@Colbur Berry, Empoleon+Garchomp, Archaludon+Farigiraf, Archaludon+Farigiraf@Colbur Berry, Annihilape+Pelipper@Sitrus Berry, Armarouge+Gengar@Gengarite, Gengar+Swampert@Swampertite, Garchomp+Talonflame, Armarouge+Gengar, Sinistcha+Swampert, Swampert+Rillaboom@Eject Button, Farigiraf+Indeedee-F@Psychic Seed, Sylveon+Charizard@Charizardite Y, Farigiraf+Primarina@Leftovers, Ninetales-Alola+Gengar@Gengarite, Espathra+Archaludon@Leftovers, Charizard+Sylveon@Fairy Feather, Torkoal+Venusaur, Staraptor+Garchomp@Sitrus Berry, Gengar+Ninetales-Alola, Charizard+Sylveon, Indeedee-F+Meganium, Indeedee-F+Meganium@Meganiumite, Garchomp+Volcarona@Sitrus Berry, Armarouge+Golisopod@Golisopite, Blaziken+Farigiraf@Sitrus Berry, Rotom-Heat+Golisopod@Golisopite, Armarouge+Golisopod, Pelipper+Indeedee-F@Colbur Berry, Golisopod+Rotom-Heat, Kleavor+Golisopod@Golisopite, Golisopod+Kleavor, Gholdengo+Garchomp@Sitrus Berry, Gardevoir+Venusaur, Venusaur+Gardevoir@Gardevoirite, Delphox+Garchomp@Choice Scarf, Farigiraf+Sylveon@Fairy Feather, Blaziken+Farigiraf, Garchomp+Farigiraf@Sitrus Berry, Espathra+Charizard@Charizardite Y, Dragonite+Pelipper@Sitrus Berry, Golisopod+Rillaboom@Expert Belt, Rillaboom+Farigiraf@Grassy Seed, Rillaboom+Swampert@Sitrus Berry, Incineroar+Mawile, Incineroar+Mawile@Mawilite, Politoed+Pawmot@Focus Sash, Farigiraf+Sylveon, Venusaur+Indeedee-F@Colbur Berry, Charizard+Swampert@Swampertite, Archaludon+Espathra, Kommo-o+Politoed, Charizard+Espathra, Swampert+Kommo-o@Leftovers, Sneasler+Starmie, Incineroar+Garchomp@Garchompite Z, Politoed+Swampert, Politoed+Swampert@Swampertite, Basculegion+Pelipper, Farigiraf+Pawmot@Focus Sash, Incineroar+Venusaur@Life Orb, Annihilape+Charizard@Charizardite Y, Golisopod+Annihilape@Choice Scarf, Farigiraf+Garchomp@Garchompite Z, Farigiraf+Garchomp, Garchomp+Typhlosion-Hisui, Kingambit+Garchomp@Choice Scarf, Pelipper+Charizard@Charizardite Y, Golisopod+Milotic@Psychic Seed, Charizard+Pelipper, Pawmot+Farigiraf@Sitrus Berry, Garchomp+Glimmora, Annihilape+Charizard, Gardevoir+Pelipper@Focus Sash, Farigiraf+Kingambit@Black Glasses, Maushold+Gengar@Gengarite, Basculegion+Garchomp@Sitrus Berry, Archaludon+Rillaboom@Expert Belt, Swampert+Charizard@Charizardite Y, Gengar+Maushold, Venusaur+Sneasler@Psychic Seed, Sinistcha+Pelipper@Focus Sash, Kingambit+Garchomp@Sitrus Berry, Gengar+Indeedee-F@Rocky Helmet, Torkoal+Farigiraf@Sitrus Berry, Garchomp+Incineroar@Sitrus Berry, Torkoal+Garchomp@Garchompite Z, Farigiraf+Pawmot, Charizard+Swampert, Golisopod+Indeedee-F@Sitrus Berry, Talonflame+Garchomp@Garchompite Z, Incineroar+Farigiraf@Colbur Berry, Incineroar+Venusaur@Wide Lens, Archaludon+Basculegion@Choice Scarf, Mawile+Pelipper, Pelipper+Mawile@Mawilite, Kingambit+Meganium, Kingambit+Meganium@Meganiumite, Charizard+Golisopod@Golisopite, Incineroar+Garchomp@Choice Scarf, Charizard+Golisopod, Golisopod+Charizard@Charizardite Y, Charizard+Rillaboom@Grassy Seed, Sneasler+Garchomp@Garchompite, Golisopod+Dragapult@Life Orb, Garchomp+Sylveon@Fairy Feather, Archaludon+Sinistcha@Sitrus Berry, Annihilape+Pelipper@Focus Sash, Indeedee-F+Pelipper@Focus Sash, Annihilape+Pelipper, Rillaboom+Vivillon@Focus Sash, Dragapult+Golisopod@Golisopite, Garchomp+Glimmora@Glimmoranite, Dragapult+Golisopod, Garchomp+Sylveon, Rillaboom+Politoed@Sitrus Berry, Indeedee-F+Garchomp@Sitrus Berry, Garchomp+Lucario@Lucarionite Z, Pelipper+Rillaboom@Life Orb, Farigiraf+Pelipper@Focus Sash, Golisopod+Pawmot@Focus Sash, Rillaboom+Vivillon, Incineroar+Politoed, Garchomp+Lucario, Kingambit+Farigiraf@Sitrus Berry, Archaludon+Gholdengo@Choice Scarf, Golisopod+Armarouge@Life Orb, Pawmot+Politoed, Swampert+Incineroar@Passho Berry, Annihilape+Golisopod@Golisopite, Annihilape+Golisopod, Manectric+Rillaboom, Rillaboom+Manectric@Manectite, Pelipper+Scovillain@Scovillainite, Garchomp+Kingambit@Occa Berry, Farigiraf+Swampert@Swampertite, Golisopod+Indeedee-F@Psychic Seed, Annihilape+Archaludon, Pawmot+Garchomp@Garchompite Z, Garchomp+Metagross@Metagrossite, Garchomp+Incineroar@Chople Berry, Garchomp+Rotom-Heat, Garchomp+Venusaur@Focus Sash, Farigiraf+Garchomp@Choice Scarf, Venusaur+Indeedee-F@Psychic Seed, Pelipper+Sinistcha, Farigiraf+Swampert, Pelipper+Scovillain, Garchomp+Kleavor, Garchomp+Metagross, Aegislash+Incineroar, Annihilape+Archaludon@Leftovers, Indeedee-F+Golisopod@Golisopite, Indeedee-F+Mawile, Indeedee-F+Mawile@Mawilite, Golisopod+Indeedee-F, Pelipper+Sneasler@Psychic Seed, Golisopod+Politoed@Sitrus Berry, Farigiraf+Pelipper, Basculegion+Garchomp@Garchompite Z, Rillaboom+Pelipper@Choice Scarf, Basculegion+Aerodactyl@Focus Sash, Venusaur+Basculegion@Choice Scarf, Grimmsnarl+Farigiraf@Colbur Berry, Farigiraf+Sableye, Garchomp+Incineroar, Rillaboom+Gengar@Gengarite, Garchomp+Sneasler@Focus Sash, Incineroar+Charizard@Charizardite X, Farigiraf+Kingambit, Camerupt+Golisopod@Golisopite, Gengar+Rillaboom, Garchomp+Kingambit, Camerupt+Golisopod, Golisopod+Camerupt@Cameruptite, Kingambit+Aerodactyl@Focus Sash, Golisopod+Rillaboom@Grassy Seed, Sylveon+Venusaur, Swampert+Incineroar@Chople Berry, Incineroar+Aerodactyl@Focus Sash, Aerodactyl+Incineroar@Sitrus Berry, Primarina+Farigiraf@Sitrus Berry, Incineroar+Farigiraf@Grassy Seed, Sinistcha+Garchomp@Choice Scarf, Pawmot+Golisopod@Golisopite, Charizard+Kingambit@Occa Berry, Golisopod+Rillaboom@Life Orb, Golisopod+Pawmot, Venusaur+Sylveon@Fairy Feather, Sneasler+Charizard@Charizardite X, Pelipper+Indeedee@Focus Sash, Garchomp+Floette-Eternal@Floettite, Floette-Eternal+Garchomp, Farigiraf+Incineroar@Rocky Helmet, Venusaur+Garchomp@Garchompite Z, Basculegion+Sableye, Garchomp+Basculegion@Life Orb, Hydreigon+Charizard@Charizardite Y, Indeedee-F+Venusaur@Focus Sash, Garchomp+Rillaboom@Grassy Seed, Golisopod+Primarina@Life Orb, Golisopod+Garchomp@Garchompite Z, Charizard+Hydreigon, Rillaboom+Archaludon@Chople Berry, Sinistcha+Grimmsnarl@Light Clay, Mawile+Incineroar@Sitrus Berry, Kleavor+Charizard@Charizardite Y, Sinistcha+Golisopod@Golisopite, Pelipper+Sinistcha@Colbur Berry, Golisopod+Sinistcha, Garchomp+Scovillain@Scovillainite, Sneasler+Garchomp@Garchompite Z, Arcanine-Hisui+Farigiraf@Grassy Seed, Tsareena+Gholdengo@Life Orb, Garchomp+Politoed@Life Orb, Charizard+Indeedee-F@Sitrus Berry, Gengar+Incineroar@Sitrus Berry, Charizard+Kleavor, Golisopod+Venusaur@Focus Sash, Grimmsnarl+Sinistcha, Swampert+Farigiraf@Sitrus Berry, Gengar+Kingambit@Black Glasses, Indeedee-F+Venusaur, Rillaboom+Garchomp@Garchompite Z, Indeedee-F+Pelipper
- Members:
| Species | In-community support | Roles |
| :--- | :--- | :--- |
| Archaludon | 60.0% | spa-drop 28.2% |
| Farigiraf | 41.8% | priority-blocker 99.5%, trick-room-setter 98.1%, helping-hand 50.1%, trick-room-abuser 34.4%, disruption 6.1%, weather-setter 5.0%, setup 2.9%, ally-switch 1.8%, screens 0.9%, terrain-setter 0.9%, speed-drop 0.2% |
| Golisopod | 40.3% | mega-attacker 99.7%, setup 43.4%, priority-attack 36.3%, trick-room-abuser 32.5%, pivot 2.5%, wide-guard 1.1% |
| Pelipper | 39.4% | weather-setter 100.0%, tailwind 86.0%, wide-guard 67.4%, trick-room-abuser 2.5%, helping-hand 1.4%, pivot 1.2%, speed-drop 0.8% |
| Charizard | 37.4% | mega-attacker 100.0%, weather-setter 98.3%, setup 1.7%, helping-hand 1.5% |
| Garchomp | 29.7% | mega-attacker 49.3%, speed-drop 8.6%, setup 2.2%, weather-setter 0.5% |
| Politoed | 28.6% | weather-setter 100.0%, perish-song 45.7%, disruption 33.3%, trick-room-abuser 8.2%, speed-drop 7.5%, helping-hand 6.7%, status 6.4%, setup 0.5% |
| Grimmsnarl | 20.8% | prankster 100.0%, screens 98.4%, pivot 96.5%, spa-drop 94.0%, trick-room-abuser 46.8%, fake-out 5.7%, priority-attack 1.6%, disruption 1.6%, speed-drop 0.5% |
| Swampert | 17.0% | mega-attacker 92.1%, wide-guard 5.9%, pivot 5.8%, status 4.6%, trick-room-abuser 4.3%, weather-setter 1.0%, helping-hand 0.7%, setup 0.7% |
| Gengar | 16.3% | mega-attacker 92.0%, perish-song 67.7%, speed-drop 8.1%, disruption 7.3%, status 1.6%, setup 0.7% |
| Venusaur | 13.0% | status 77.0%, mega-attacker 9.0% |
| Aerodactyl | 9.0% | tailwind 99.0%, mega-attacker 75.4%, wide-guard 46.1%, disruption 2.8% |
| Vivillon | 5.4% | status 100.0%, rage-powder 95.1%, tailwind 7.8% |
| Sableye | 3.7% | prankster 91.0%, screens 72.3%, weather-setter 65.7%, disruption 41.4%, status 36.2%, trick-room-abuser 32.8%, fake-out 28.2%, spa-drop 7.5%, setup 5.3%, helping-hand 3.7%, speed-drop 3.7%, mega-attacker 2.6% |
| Mawile | 1.9% | mega-attacker 100.0%, priority-attack 94.1%, trick-room-abuser 79.8%, intimidate 31.0%, setup 20.3% |
| Toxapex | 1.8% | status 100.0%, wide-guard 94.8%, trick-room-abuser 36.9% |
| Meganium | 1.6% | mega-attacker 100.0% |
| Aegislash | 1.2% | wide-guard 59.6%, priority-attack 57.5% |
| Tsareena | 0.8% | priority-blocker 100.0%, disruption 28.1%, helping-hand 9.0%, pivot 6.4% |
| Starmie | 0.8% | mega-attacker 78.3%, setup 12.8%, priority-attack 12.8% |
| Kangaskhan | 0.6% | fake-out 100.0%, priority-attack 18.5%, mega-attacker 18.5% |
| Ampharos | 0.6% | mega-attacker 100.0%, trick-room-abuser 64.6%, setup 50.0%, speed-drop 14.6% |
| Manectric | 0.5% | intimidate 100.0%, mega-attacker 100.0%, spa-drop 82.8%, pivot 82.8%, screens 17.2% |
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
  - [Will Connor, 174th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0306/teamlist)
  - [Ryne Morgan, 834th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0836/teamlist)
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
  - [Michael Cenatiempo, 693rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0202/teamlist)
  - [Felix Renaud-Chartier, 768th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0878/teamlist)
  - [Tz Cogao, , 10 Sep 2026](https://pokepast.es/f828e7b46a771515)
  - [Jairo Contreras, 327th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0215/teamlist)
  - [Matt Francis, 325th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0955/teamlist)
  - [Tyler Coady, 427th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0376/teamlist)
  - [Benjamin Lavigne, 918th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0678/teamlist)
  - [Teodoro Castellon, 413th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0182/teamlist)
  - [Aden Carver, 926th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0406/teamlist)
  - [Max Doebeli, 157th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0064/teamlist)
  - [William Pye, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0229/teamlist)
  - [Brandon Harrison, 486th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0185/teamlist)
  - [Jack Kent, 934th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0725/teamlist)
  - [Wafeeq Khan, 1047th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0687/teamlist)
  - [Kyle Jones, 935th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0945/teamlist)
  - [joshg0tti1, , 11 Sep 2026](https://pokepast.es/4c6eb1d0d2cbb3f3)
  - [Josh Schulster, 792nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0627/teamlist)
  - [Daniel walker, 6th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0315/teamlist)
  - [Andrew Levy, 467th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0601/teamlist)
  - [Jason Romero, 915th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0564/teamlist)
  - [Ignacio Campos Jimenez, 279th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0476/teamlist)
  - [MeK191817, , 11 Sep 2026](https://pokepast.es/a1338edf35719660)
  - [Tim Beyreuther, 621st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0731/teamlist)
  - [rioreumi, , 9 Sep 2026](https://pokepast.es/07e91402b74462ef)
  - [Michael Koenigsberg, 938th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0368/teamlist)
  - [Jordan Goggin, 161st, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0089/teamlist)
  - [Seth Ellsworth, 586th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0058/teamlist)
  - [Justin Tang, , 9 Sep 2026](https://pokepast.es/7b073199fc857b04)
  - [Noel Marquez, 645th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0303/teamlist)
  - [Braden Hood, 584th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0091/teamlist)
  - [Curtis Ridings, 128th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0184/teamlist)
  - [ribe88, , 13 Sep 2026](https://pokepast.es/a702bf47f522438b)
  - [Guilherme Schilling, 115th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0231/teamlist)
  - [PiyoLily145, , 9 Sep 2026](https://pokepast.es/8e37c3b00cbba6b6)
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
  - [Jeremy Ortiz, 976th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0077/teamlist)
  - [Nikita Gnatenko, 679th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0997/teamlist)
  - [Michell Osew, 724th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0153/teamlist)
  - [Brandon Ebert, 1029th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0909/teamlist)
  - [London Faust, 80th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0495/teamlist)
  - [Lance Lee, 1028th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0478/teamlist)
  - [silversandbag, , 13 Sep 2026](https://pokepast.es/3c4611ccba18d35a)
  - [Semih Erdogdu, 350th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0313/teamlist)
  - [Lily, Peak 24th, 10 Sep 2026](https://pokepast.es/027fda21958e66de)
  - [takiapoke, 7th, 28 Sep 2026](https://pokepast.es/3ad14d3332150d81)
  - [Keigo Tanizawa, 7th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0175/teamlist)
  - [Kazeno_shion, , 10 Sep 2026](https://pokepast.es/e638bc044d3cb47f)
  - [Escen Schaferin, 880th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0082/teamlist)
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
  - [Heber Henriquez, 805th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0902/teamlist)
  - [Mario Giuseppe Vincitorio, 252nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0458/teamlist)
  - [Simon Batty, 812th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0066/teamlist)
  - [Jetrick Gelacio, 111th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0782/teamlist)
  - [Ali Pütün, 742nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0084/teamlist)
  - [mofumofunatsuhi, , 13 Sep 2026](https://pokepast.es/f85b026e5b0e6567)
  - [David Durán, 1012th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0374/teamlist)
  - [clark smith, 1011th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0116/teamlist)
  - [Jayson Lyon, 837th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0516/teamlist)
  - [Duy Thang Nguyen, 208th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0617/teamlist)
  - [Lillian Heath, 767th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1067/teamlist)
  - [Tom de Gruijter, 217th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1093/teamlist)
  - [kaki, , 13 Sep 2026](https://pokepast.es/a202a04735494175)
  - [Joshua Moloney, 282nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0143/teamlist)
  - [gravity030, , 9 Sep 2026](https://pokepast.es/2f512d91f56e0830)
  - [Ryan Yost, 877th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1042/teamlist)
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
  - [Rens Heylen, 296th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0101/teamlist)
  - [Alexander Kremer, 923rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0265/teamlist)

- Sub-community pass: 654 primary teams, 153 tokens, modularity 0.51; unconnected tokens: Toxtricity (other item; Life Orb 5/6), Tyranitar (other item; Choice Scarf 2/6), Absol@Absolite Z, Empoleon (other item; Sitrus Berry 3/5), Maushold (other item; Focus Sash 2/5), Rillaboom@Grassy Seed, Glimmora (other item; Focus Sash 3/4), Kleavor (other item; Choice Scarf 2/4), Ninetales-Alola (other item; Choice Scarf 2/4), Noivern (other item; Focus Sash 4/4), Overqwil (other item; Life Orb 2/4), Scovillain (other item; Scovillainite 4/4), Talonflame (other item; Focus Sash 2/4), Arcanine-Hisui (other item; Focus Sash 3/3), Blaziken (other item; Focus Sash 2/3), Clefable (other item; Sitrus Berry 3/3), Drampa (other item; Drampanite 2/3), Excadrill (other item; Choice Scarf 2/3), Gallade (other item; Focus Sash 1/3), Gliscor (other item; Choice Scarf 1/3), Hatterene (other item; Focus Sash 2/3), Lycanroc-Dusk (other item; Focus Sash 3/3), Meowscarada (other item; Choice Scarf 1/3), Pincurchin (other item; Air Balloon 1/3), Pyroar (other item; Pyroarite 3/3); unassigned within the community: 5 teams (0.9% of its primary weight); hybrid teams of the community left out: 139
- Sub-community resolution sweep:
| Resolution | 0.50 | 0.75 | 1.00 | 1.25 | 1.50 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-communities (modularity) | 3 (0.69) | 4 (0.60) | 5 (0.51) | 6 (0.45) | 7 (0.41) |

#### Community 4 / Sub-community 0: Rain (Mega Golisopod) (230 primary teams, 161 distinct builds, top pair on 211/230)
- Megas on member teams: Golisopod 116, Swampert 93, Charizard-Y 42, Salamence 38, Garchomp-Z 14, Metagross 9
- Top species by team share: Archaludon 96%, Pelipper 94%, Golisopod 50%, Swampert 40%, Grimmsnarl 27%, Basculegion 27%
- Token label: Archaludon / Pelipper
- Mode tags on primary teams: Rain 225, Tailwind 202, Screens 75, Sun 42, Trick Room 38, Psyspam 25, Setup 8, Snow 6, Sand 1
- Primary teams: 230 (34.7% of the community's primary weight), hybrid teams: 99 (16.3%)
- Date range: 2026-09-09 to 2026-09-30
- Core pairs: Indeedee@Choice Scarf+Sneasler@Psychic Seed, Indeedee (other item; Focus Sash 8/8)+Sneasler@Psychic Seed, Indeedee-F (other item; Rocky Helmet 23/42)+Gardevoir@Gardevoirite, Armarouge (other item; Life Orb 4/6)+Indeedee-F (other item; Rocky Helmet 23/42), Dragonite@Dragoninite+Metagross@Metagrossite, Dragonite@Dragoninite+Sneasler@Psychic Seed, Gholdengo (other item; Life Orb 16/25)+Pawmot (other item; Focus Sash 13/15), Indeedee-F (other item; Rocky Helmet 23/42)+Sneasler@Psychic Seed, Indeedee-F (other item; Rocky Helmet 23/42)+Meganium@Meganiumite, Aerodactyl (other item; Focus Sash 11/12)+Indeedee-F (other item; Rocky Helmet 23/42), Pawmot (other item; Focus Sash 13/15)+Salamence@Salamencite, Indeedee-F (other item; Rocky Helmet 23/42)+Metagross@Metagrossite, Basculegion@Choice Scarf+Salamence@Salamencite, Gholdengo (other item; Life Orb 16/25)+Whimsicott (other item; Focus Sash 30/34), Sableye@Light Clay+Swampert@Swampertite, Basculegion@Choice Scarf+Metagross@Metagrossite, Gholdengo (other item; Life Orb 16/25)+Salamence@Salamencite, Sinistcha (other item; Sitrus Berry 9/28)+Swampert@Swampertite, Pelipper (other item; Focus Sash 136/265)+Starmie (other item; Starminite 3/4), Sableye (other item; Roseli Berry 7/11)+Swampert@Swampertite, Gholdengo (other item; Life Orb 16/25)+Swampert@Swampertite, Rillaboom (other item; Miracle Seed 103/150)+Salamence@Salamencite, Dragapult (other item; Life Orb 7/9)+Pelipper (other item; Focus Sash 136/265), Pelipper (other item; Focus Sash 136/265)+Swampert@Swampertite, Pelipper (other item; Focus Sash 136/265)+Sneasler@Psychic Seed, Annihilape@Choice Scarf+Swampert@Swampertite, Kingambit (other item; Focus Sash 54/120)+Gardevoir@Gardevoirite, Pelipper (other item; Focus Sash 136/265)+Indeedee@Choice Scarf, Armarouge (other item; Life Orb 4/6)+Golisopod@Golisopite, Sneasler (other item; White Herb 42/51)+Basculegion@Choice Scarf, Pelipper (other item; Focus Sash 136/265)+Meganium@Meganiumite, Pelipper (other item; Focus Sash 136/265)+Salamence@Salamencite, Armarouge (other item; Life Orb 4/6)+Pelipper (other item; Focus Sash 136/265), Pelipper (other item; Focus Sash 136/265)+Basculegion@Choice Scarf, Indeedee-F (other item; Rocky Helmet 23/42)+Sneasler (other item; White Herb 42/51), Venusaur (other item; Focus Sash 48/75)+Swampert@Swampertite, Golisopod@Golisopite+Sableye@Light Clay, Rillaboom (other item; Miracle Seed 103/150)+Basculegion@Choice Scarf, Gholdengo (other item; Life Orb 16/25)+Garchomp@Garchompite Z, Pelipper (other item; Focus Sash 136/265)+Sinistcha (other item; Sitrus Berry 9/28), Venusaur (other item; Focus Sash 48/75)+Sneasler@Psychic Seed, Archaludon (other item; Leftovers 360/384)+Manectric (other item; Manectite 4/4), Archaludon (other item; Leftovers 360/384)+Starmie (other item; Starminite 3/4), Archaludon (other item; Leftovers 360/384)+Raichu@Raichunite X, Archaludon (other item; Leftovers 360/384)+Blastoise (other item; Blastoisinite 4/4), Archaludon (other item; Leftovers 360/384)+Espathra (other item; Electric Seed 2/5), Rillaboom (other item; Miracle Seed 103/150)+Sableye (other item; Roseli Berry 7/11), Pelipper (other item; Focus Sash 136/265)+Sneasler@Grassy Seed, Grimmsnarl@Light Clay+Swampert@Swampertite, Sinistcha (other item; Sitrus Berry 9/28)+Golisopod@Golisopite, Indeedee-F (other item; Rocky Helmet 23/42)+Pelipper (other item; Focus Sash 136/265), Archaludon (other item; Leftovers 360/384)+Grimmsnarl@Light Clay, Dragapult (other item; Life Orb 7/9)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 136/265)+Raichu@Raichunite Y, Pelipper (other item; Focus Sash 136/265)+Raichu@Raichunite X, Indeedee (other item; Focus Sash 8/8)+Pelipper (other item; Focus Sash 136/265), Archaludon (other item; Leftovers 360/384)+Pelipper (other item; Focus Sash 136/265), Archaludon (other item; Leftovers 360/384)+Swampert@Swampertite, Gholdengo (other item; Life Orb 16/25)+Pelipper (other item; Focus Sash 136/265), Pelipper (other item; Focus Sash 136/265)+Metagross@Metagrossite, Pelipper (other item; Focus Sash 136/265)+Staraptor@Staraptite, Pelipper (other item; Focus Sash 136/265)+Grimmsnarl@Light Clay, Archaludon (other item; Leftovers 360/384)+Sneasler@Psychic Seed, Archaludon (other item; Leftovers 360/384)+Sinistcha (other item; Sitrus Berry 9/28), Pelipper (other item; Focus Sash 136/265)+Venusaur (other item; Focus Sash 48/75), Archaludon (other item; Leftovers 360/384)+Dragapult (other item; Life Orb 7/9), Charizard@Charizardite Y+Gardevoir@Gardevoirite, Salamence@Salamencite+Swampert@Swampertite, Archaludon (other item; Leftovers 360/384)+Sableye@Light Clay, Pelipper (other item; Focus Sash 136/265)+Dragonite@Dragoninite, Archaludon (other item; Leftovers 360/384)+Salamence@Salamencite, Hydreigon (other item; Focus Sash 5/8)+Pelipper (other item; Focus Sash 136/265), Pelipper (other item; Focus Sash 136/265)+Sableye@Light Clay, Archaludon (other item; Leftovers 360/384)+Politoed (other item; Sitrus Berry 86/177), Basculegion@Choice Scarf+Golisopod@Golisopite, Archaludon (other item; Leftovers 360/384)+Armarouge (other item; Life Orb 4/6), Pelipper (other item; Focus Sash 136/265)+Annihilape@Choice Scarf, Rillaboom (other item; Miracle Seed 103/150)+Dragonite@Dragoninite, Archaludon (other item; Leftovers 360/384)+Indeedee@Choice Scarf, Pelipper (other item; Focus Sash 136/265)+Sneasler (other item; White Herb 42/51), Pelipper (other item; Focus Sash 136/265)+Indeedee-F@Psychic Seed, Indeedee-F (other item; Rocky Helmet 23/42)+Swampert@Swampertite, Archaludon (other item; Leftovers 360/384)+Vivillon (other item; Focus Sash 31/31), Archaludon (other item; Leftovers 360/384)+Rillaboom@Eject Button, Golisopod@Golisopite+Salamence@Salamencite, Sinistcha (other item; Sitrus Berry 9/28)+Grimmsnarl@Light Clay, Indeedee-F (other item; Rocky Helmet 23/42)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 103/150)+Metagross@Metagrossite, Pelipper (other item; Focus Sash 136/265)+Sableye (other item; Roseli Berry 7/11), Archaludon (other item; Leftovers 360/384)+Basculegion@Choice Scarf, Archaludon (other item; Leftovers 360/384)+Golisopod@Golisopite, Archaludon (other item; Leftovers 360/384)+Sneasler@Grassy Seed, Archaludon (other item; Leftovers 360/384)+Metagross@Metagrossite, Pelipper (other item; Focus Sash 136/265)+Golisopod@Golisopite, Archaludon (other item; Leftovers 360/384)+Gengar@Gengarite, Basculegion (other item; Life Orb 19/28)+Pelipper (other item; Focus Sash 136/265), Archaludon (other item; Leftovers 360/384)+Froslass@Froslassite, Archaludon (other item; Leftovers 360/384)+Indeedee-F (other item; Rocky Helmet 23/42), Archaludon (other item; Leftovers 360/384)+Sableye (other item; Roseli Berry 7/11), Archaludon (other item; Leftovers 360/384)+Annihilape@Choice Scarf, Archaludon (other item; Leftovers 360/384)+Indeedee (other item; Focus Sash 8/8), Archaludon (other item; Leftovers 360/384)+Gardevoir@Gardevoirite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Archaludon (other item; Leftovers 360/384) | 96.9% |
| Pelipper (other item; Focus Sash 136/265) | 94.0% |
| Swampert@Swampertite | 40.9% |
| Basculegion@Choice Scarf | 19.6% |
| Salamence@Salamencite | 15.9% |
| Indeedee-F (other item; Rocky Helmet 23/42) | 13.0% |
| Sneasler@Psychic Seed | 8.8% |
| Sinistcha (other item; Sitrus Berry 9/28) | 8.4% |
| Gholdengo (other item; Life Orb 16/25) | 6.3% |
| Metagross@Metagrossite | 4.3% |
| Sableye@Light Clay | 4.3% |
| Sableye (other item; Roseli Berry 7/11) | 3.7% |
| Dragonite@Dragoninite | 3.6% |
| Indeedee-F@Psychic Seed | 3.3% |
| Meganium@Meganiumite | 3.2% |
| Dragapult (other item; Life Orb 7/9) | 3.1% |
| Indeedee (other item; Focus Sash 8/8) | 2.5% |
| Raichu@Raichunite X | 2.2% |
| Starmie (other item; Starminite 3/4) | 2.2% |
| Indeedee@Choice Scarf | 2.1% |
| Armarouge (other item; Life Orb 4/6) | 2.1% |
| Venusaur@Venusaurite | 2.0% |
| Gardevoir@Gardevoirite | 1.8% |
| Staraptor@Staraptite | 1.8% |
| Blastoise (other item; Blastoisinite 4/4) | 1.6% |
| Espathra (other item; Electric Seed 2/5) | 1.3% |
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
  - [FWIEFISCH, 11th, 27 Sep 2026](https://pokepast.es/3fb800fbaa57c1b5)
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
  - [Frank Cordova Centurion, 608th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0409/teamlist)
  - [Georg Lang, 615th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0449/teamlist)
  - [Joshua Miller, 102nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0709/teamlist)
  - [Joshua Miller, 102nd, 27 Sep 2026](https://pokepast.es/58c15a8bd78b30ab)
  - [Dimitri Koziaris, 48th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0158/teamlist)
  - [Andy Brophy, 62nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0277/teamlist)
  - [THORIN MCDONALD, 245th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0233/teamlist)
  - [Lennart Otto, 151st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0003/teamlist)
  - [David R. Pigan-Ewens, 533rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0109/teamlist)
  - [Lukas Zahn, 730th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1047/teamlist)
  - [Tim Maruschewski, 743rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0602/teamlist)
  - [Taevon Ramseur, 559th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0242/teamlist)
  - [Dorean Neron, 504th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0033/teamlist)
  - [Dorian Luckie, 802nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0459/teamlist)
  - [Nicholas Faulkner, 184th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0144/teamlist)
  - [Austin Le, 309th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0322/teamlist)
  - [Liam Clarkson, 27th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0122/teamlist)
  - [Thomas Dervan, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0256/teamlist)
  - [Xena, , 10 Sep 2026](https://pokepast.es/b7d841f59242636e)
  - [Ryota Otsubo, , 24 Sep 2026](https://pokepast.es/b244e8068886b33d)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Joel Pichardo, 640th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0300/teamlist)

#### Community 4 / Sub-community 1: Trick Room (Mega Golisopod) (124 primary teams, 100 distinct builds, top pair on 73/124)
- Megas on member teams: Golisopod 89, Garchomp-Z 43, Charizard-Y 11, Raichu-Y 10, Salamence 9, Mawile 8
- Top species by team share: Farigiraf 94%, Golisopod 72%, Incineroar 41%, Rillaboom 41%, Garchomp 37%, Politoed 25%
- Token label: Farigiraf / Golisopod@Golisopite
- Mode tags on primary teams: Trick Room 75, Rain 52, Tailwind 35, Sun 26, Setup 17, Screens 9, Sand 4, Snow 3, Psyspam 1
- Primary teams: 124 (18.0% of the community's primary weight), hybrid teams: 100 (16.5%)
- Date range: 2026-09-09 to 2026-09-30
- Core pairs: Pawmot (other item; Focus Sash 13/15)+Staraptor (other item; Choice Scarf 4/4), Torkoal (other item; Charcoal 17/17)+Farigiraf@Grassy Seed, Gholdengo (other item; Life Orb 16/25)+Pawmot (other item; Focus Sash 13/15), Milotic (other item; Leftovers 22/32)+Camerupt@Cameruptite, Farigiraf@Grassy Seed+Garchomp@Garchompite Z, Milotic (other item; Leftovers 22/32)+Farigiraf@Grassy Seed, Torkoal (other item; Charcoal 17/17)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 103/150)+Farigiraf@Grassy Seed, Pawmot (other item; Focus Sash 13/15)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 103/150)+Sneasler@Grassy Seed, Politoed (other item; Sitrus Berry 86/177)+Staraptor (other item; Choice Scarf 4/4), Rillaboom (other item; Miracle Seed 103/150)+Blaziken@Blazikenite, Sneasler (other item; White Herb 42/51)+Garchomp@Garchompite Z, Ampharos (other item; Ampharosite 4/4)+Farigiraf (other item; Sitrus Berry 203/251), Farigiraf (other item; Sitrus Berry 203/251)+Staraptor (other item; Choice Scarf 4/4), Kingambit (other item; Focus Sash 54/120)+Torkoal (other item; Charcoal 17/17), Staraptor (other item; Choice Scarf 4/4)+Golisopod@Golisopite, Milotic (other item; Leftovers 22/32)+Garchomp@Garchompite Z, Milotic (other item; Leftovers 22/32)+Rillaboom (other item; Miracle Seed 103/150), Farigiraf (other item; Sitrus Berry 203/251)+Primarina (other item; Life Orb 8/14), Pawmot (other item; Focus Sash 13/15)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 103/150)+Lucario@Lucarionite Z, Rillaboom (other item; Miracle Seed 103/150)+Salamence@Salamencite, Rillaboom (other item; Miracle Seed 103/150)+Torkoal (other item; Charcoal 17/17), Farigiraf (other item; Sitrus Berry 203/251)+Aerodactyl@Aerodactylite, Kingambit (other item; Focus Sash 54/120)+Camerupt@Cameruptite, Farigiraf (other item; Sitrus Berry 203/251)+Camerupt@Cameruptite, Armarouge (other item; Life Orb 4/6)+Golisopod@Golisopite, Kingambit (other item; Focus Sash 54/120)+Primarina (other item; Life Orb 8/14), Farigiraf (other item; Sitrus Berry 203/251)+Mawile@Mawilite, Sneasler (other item; White Herb 42/51)+Basculegion@Choice Scarf, Rillaboom (other item; Miracle Seed 103/150)+Camerupt@Cameruptite, Kommo-o (other item; Leftovers 19/26)+Rillaboom (other item; Miracle Seed 103/150), Incineroar (other item; Sitrus Berry 71/216)+Mawile@Mawilite, Kingambit (other item; Focus Sash 54/120)+Farigiraf@Grassy Seed, Farigiraf (other item; Sitrus Berry 203/251)+Glimmora@Glimmoranite, Indeedee-F (other item; Rocky Helmet 23/42)+Sneasler (other item; White Herb 42/51), Rillaboom (other item; Miracle Seed 103/150)+Volcarona (other item; Rocky Helmet 3/9), Rillaboom (other item; Miracle Seed 103/150)+Garchomp@Garchompite Z, Golisopod@Golisopite+Grimmsnarl@Light Clay, Hydreigon (other item; Focus Sash 5/8)+Golisopod@Golisopite, Golisopod@Golisopite+Sableye@Light Clay, Milotic (other item; Leftovers 22/32)+Golisopod@Golisopite, Rillaboom (other item; Miracle Seed 103/150)+Raichu@Raichunite Y, Rillaboom (other item; Miracle Seed 103/150)+Basculegion@Choice Scarf, Gholdengo (other item; Life Orb 16/25)+Garchomp@Garchompite Z, Farigiraf (other item; Sitrus Berry 203/251)+Blaziken@Blazikenite, Incineroar (other item; Sitrus Berry 71/216)+Camerupt@Cameruptite, Farigiraf (other item; Sitrus Berry 203/251)+Sylveon (other item; Fairy Feather 88/89), Rillaboom (other item; Miracle Seed 103/150)+Sableye (other item; Roseli Berry 7/11), Pelipper (other item; Focus Sash 136/265)+Sneasler@Grassy Seed, Rillaboom (other item; Miracle Seed 103/150)+Annihilape@Choice Scarf, Sinistcha (other item; Sitrus Berry 9/28)+Golisopod@Golisopite, Farigiraf (other item; Sitrus Berry 203/251)+Torkoal (other item; Charcoal 17/17), Dragapult (other item; Life Orb 7/9)+Golisopod@Golisopite, Baxcalibur (other item; Baxcalibrite 3/7)+Golisopod@Golisopite, Pelipper (other item; Focus Sash 136/265)+Raichu@Raichunite Y, Farigiraf@Grassy Seed+Golisopod@Golisopite, Golisopod@Golisopite+Raichu@Raichunite Y, Farigiraf (other item; Sitrus Berry 203/251)+Pawmot (other item; Focus Sash 13/15), Politoed (other item; Sitrus Berry 86/177)+Volcarona (other item; Rocky Helmet 3/9), Farigiraf (other item; Sitrus Berry 203/251)+Sirfetch’d (other item; Leek 7/9), Farigiraf (other item; Sitrus Berry 203/251)+Kingambit (other item; Focus Sash 54/120), Farigiraf (other item; Sitrus Berry 203/251)+Golisopod@Golisopite, Incineroar (other item; Sitrus Berry 71/216)+Primarina (other item; Life Orb 8/14), Baxcalibur (other item; Baxcalibrite 3/7)+Incineroar (other item; Sitrus Berry 71/216), Kingambit (other item; Focus Sash 54/120)+Raichu@Raichunite Y, Farigiraf (other item; Sitrus Berry 203/251)+Milotic (other item; Leftovers 22/32), Hydreigon (other item; Focus Sash 5/8)+Pelipper (other item; Focus Sash 136/265), Glimmora@Glimmoranite+Golisopod@Golisopite, Basculegion@Choice Scarf+Golisopod@Golisopite, Sirfetch’d (other item; Leek 7/9)+Golisopod@Golisopite, Rillaboom (other item; Miracle Seed 103/150)+Dragonite@Dragoninite, Incineroar (other item; Sitrus Berry 71/216)+Farigiraf@Grassy Seed, Pelipper (other item; Focus Sash 136/265)+Sneasler (other item; White Herb 42/51), Farigiraf (other item; Sitrus Berry 203/251)+Raichu@Raichunite Y, Politoed (other item; Sitrus Berry 86/177)+Golisopod@Golisopite, Golisopod@Golisopite+Sneasler@Grassy Seed, Golisopod@Golisopite+Salamence@Salamencite, Incineroar (other item; Sitrus Berry 71/216)+Raichu@Raichunite Y, Incineroar (other item; Sitrus Berry 71/216)+Rillaboom (other item; Miracle Seed 103/150), Kingambit (other item; Focus Sash 54/120)+Pawmot (other item; Focus Sash 13/15), Farigiraf (other item; Sitrus Berry 203/251)+Garchomp (other item; Life Orb 59/77), Farigiraf (other item; Sitrus Berry 203/251)+Garchomp@Choice Scarf, Incineroar (other item; Sitrus Berry 71/216)+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 71/216)+Milotic (other item; Leftovers 22/32), Farigiraf (other item; Sitrus Berry 203/251)+Garchomp@Garchompite Z, Rillaboom (other item; Miracle Seed 103/150)+Golisopod@Golisopite, Rillaboom (other item; Miracle Seed 103/150)+Metagross@Metagrossite, Farigiraf (other item; Sitrus Berry 203/251)+Charizard@Charizardite Y, Archaludon (other item; Leftovers 360/384)+Golisopod@Golisopite, Archaludon (other item; Leftovers 360/384)+Sneasler@Grassy Seed, Pelipper (other item; Focus Sash 136/265)+Golisopod@Golisopite, Basculegion (other item; Life Orb 19/28)+Rillaboom (other item; Miracle Seed 103/150)
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Farigiraf (other item; Sitrus Berry 203/251) | 81.6% |
| Golisopod@Golisopite | 71.0% |
| Rillaboom (other item; Miracle Seed 103/150) | 39.5% |
| Garchomp@Garchompite Z | 36.4% |
| Milotic (other item; Leftovers 22/32) | 15.5% |
| Torkoal (other item; Charcoal 17/17) | 13.0% |
| Sneasler (other item; White Herb 42/51) | 13.0% |
| Farigiraf@Grassy Seed | 12.7% |
| Raichu@Raichunite Y | 8.6% |
| Primarina (other item; Life Orb 8/14) | 8.5% |
| Pawmot (other item; Focus Sash 13/15) | 6.4% |
| Mawile@Mawilite | 6.0% |
| Camerupt@Cameruptite | 5.7% |
| Ampharos (other item; Ampharosite 4/4) | 3.2% |
| Sneasler@Grassy Seed | 3.1% |
| Staraptor (other item; Choice Scarf 4/4) | 2.9% |
| Volcarona (other item; Rocky Helmet 3/9) | 2.8% |
| Baxcalibur (other item; Baxcalibrite 3/7) | 2.3% |
| Hydreigon (other item; Focus Sash 5/8) | 2.2% |
| Grapploct (other item; Life Orb 3/4) | 1.8% |
| Lucario@Lucarionite Z | 1.5% |
| Kangaskhan (other item; Kangaskhanite 2/5) | 1.5% |
- Representative teams (primary teams carrying its top two tokens first, then by their score for this sub-community):
  - [Wolfgang Wambach, 1001st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1017/teamlist)
  - [Kevin Guzman, 797th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0652/teamlist)
  - [Jesse Beard, 99th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0290/teamlist)
- Hybrid teams (teams for which this is a hybrid, by their score for it):
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
  - [Emma Schot, 212th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0075/teamlist)
  - [Emilio Gallardo Ávila, 72nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1072/teamlist)
  - [Edhen Soto, 292nd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0170/teamlist)
  - [Jacob Heagerty, 203rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0957/teamlist)
  - [Elisha Smith, 1060th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0419/teamlist)
  - [Parker Sidenstricker, 145th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0005/teamlist)
  - [Nico Schumann, 357th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1109/teamlist)
  - [Emanuel Helmke, 886th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0157/teamlist)
  - [Dominik Bratek, 1008th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0230/teamlist)
  - [Maximilian Schmidt, 860th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0181/teamlist)
  - [Mats Kjellström, 913th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0749/teamlist)
  - [Sara Munk, 109th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0138/teamlist)
  - [Giovanni Manuel Tonelli, 14th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0257/teamlist)
  - [Ryan Gluchowski, 353rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0308/teamlist)
  - [Adam Tuohy, 396th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0110/teamlist)
  - [Jorge Segui Hernandez, 156th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0750/teamlist)
  - [Francisco Martí, 246th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1023/teamlist)
  - [Tom Ratsma, 529th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0663/teamlist)
  - [Daniel Trujillo Canalejo, 803rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1116/teamlist)
  - [devintlegend, , 11 Sep 2026](https://pokepast.es/56d038403d0e2fd5)
  - [Leonardo Lewis, 196th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0050/teamlist)
  - [Luke Tiger Mainholz, 808th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0328/teamlist)
  - [BrokenLegge, , 16 Sep 2026](https://pokepast.es/e39a17eb6c42b52c)
  - [Gabri 301, , 10 Sep 2026](https://pokepast.es/4fce711199944ae0)
  - [Andreas Zimmermann, 1099th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0482/teamlist)
  - [Selvin Jacob, 853rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0313/teamlist)
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
  - [Jaime Blanch Vázquez, 590th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0687/teamlist)
  - [Jonah Kassen, 830th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0111/teamlist)
  - [Lily Dellow, 72nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0266/teamlist)
  - [Liam Greenaway, 204th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0132/teamlist)
  - [Wes Stembert, 77th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0474/teamlist)
  - [Felix Friese, 313th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/1011/teamlist)
  - [re_Takeshi_, Runner Up, 12 Sep 2026](https://pokepast.es/4bfce88ce42a966a)
  - [Alex Nguyen, 511th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0267/teamlist)
  - [Ko Tsukide, , 23 Sep 2026](https://pokepast.es/175ee651eef60e92)
  - [ausma, , 9 Sep 2026](https://pokepast.es/40e7b4f7469d52ae)
  - [Julian Brandhofer, 947th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0147/teamlist)
  - [Felix Miguel Andres, 504th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0629/teamlist)
  - [Evan Graham, 602nd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0970/teamlist)
  - [One An An, 965th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0407/teamlist)
  - [Dracador1, 295th, 30 Sep 2026](https://pokepast.es/d8dfe6b89e5dc9ab)
  - [Stefan Specht, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0384/teamlist)
  - [Adam Holmyard, 889th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0996/teamlist)
  - [Stefan Filipovski, 733rd, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0152/teamlist)
  - [Alex Hoak, 284th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0348/teamlist)
  - [Rawnie Mills, 1089th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0894/teamlist)
  - [Tommy Vo, 512th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0072/teamlist)
  - [beebee10222, , 9 Sep 2026](https://pokepast.es/376176212f88e4cb)
  - [Miguel Gomes, 1003rd, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0359/teamlist)
  - [Nikola Zirdum, 281st, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0805/teamlist)
  - [Scott Iwafuchi, 27th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0682/teamlist)
  - [banksofdeshawn, , 14 Sep 2026](https://pokepast.es/2106e9c7e469a2dd)

#### Community 4 / Sub-community 2: Sun (Mega Charizard-Y) (189 primary teams, 83 distinct builds, top pair on 70/189)
- Megas on member teams: Charizard-Y 180, Golisopod 54, Aerodactyl 43, Floette 14, Garchomp-Z 14, Gardevoir 5
- Top species by team share: Charizard 95%, Garchomp 62%, Farigiraf 53%, Sylveon 42%, Kingambit 40%, Archaludon 34%
- Token label: Charizard@Charizardite Y / Sylveon
- Mode tags on primary teams: Sun 180, Tailwind 102, Rain 67, Screens 64, Trick Room 48, Psyspam 10, Setup 3, Sand 1
- Primary teams: 189 (30.3% of the community's primary weight), hybrid teams: 58 (8.4%)
- Date range: 2026-09-10 to 2026-10-01
- Core pairs: Toxapex (other item; Leftovers 13/14)+Garchomp@Choice Scarf, Whimsicott (other item; Focus Sash 30/34)+Floette-Eternal@Floettite, Whimsicott (other item; Focus Sash 30/34)+Glimmora@Glimmoranite, Basculegion (other item; Life Orb 19/28)+Floette-Eternal@Floettite, Sylveon (other item; Fairy Feather 88/89)+Aerodactyl@Aerodactylite, Garchomp (other item; Life Orb 59/77)+Aerodactyl@Aerodactylite, Aegislash (other item; Focus Sash 5/9)+Venusaur (other item; Focus Sash 48/75), Basculegion (other item; Life Orb 19/28)+Whimsicott (other item; Focus Sash 30/34), Aegislash (other item; Focus Sash 5/9)+Sylveon (other item; Fairy Feather 88/89), Sylveon (other item; Fairy Feather 88/89)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 59/77)+Floette-Eternal@Floettite, Kingambit (other item; Focus Sash 54/120)+Aerodactyl@Aerodactylite, Toxapex (other item; Leftovers 13/14)+Venusaur (other item; Focus Sash 48/75), Garchomp (other item; Life Orb 59/77)+Whimsicott (other item; Focus Sash 30/34), Aerodactyl (other item; Focus Sash 11/12)+Garchomp (other item; Life Orb 59/77), Kingambit (other item; Focus Sash 54/120)+Blaziken@Blazikenite, Venusaur (other item; Focus Sash 48/75)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 59/77)+Sylveon (other item; Fairy Feather 88/89), Whimsicott (other item; Focus Sash 30/34)+Garchomp@Choice Scarf, Sylveon (other item; Fairy Feather 88/89)+Toxapex (other item; Leftovers 13/14), Garchomp (other item; Life Orb 59/77)+Kingambit (other item; Focus Sash 54/120), Kingambit (other item; Focus Sash 54/120)+Sylveon (other item; Fairy Feather 88/89), Gholdengo (other item; Life Orb 16/25)+Whimsicott (other item; Focus Sash 30/34), Basculegion (other item; Life Orb 19/28)+Garchomp (other item; Life Orb 59/77), Rillaboom (other item; Miracle Seed 103/150)+Blaziken@Blazikenite, Aerodactyl (other item; Focus Sash 11/12)+Sylveon (other item; Fairy Feather 88/89), Venusaur (other item; Focus Sash 48/75)+Annihilape@Choice Scarf, Incineroar (other item; Sitrus Berry 71/216)+Toxapex (other item; Leftovers 13/14), Kingambit (other item; Focus Sash 54/120)+Whimsicott (other item; Focus Sash 30/34), Kingambit (other item; Focus Sash 54/120)+Floette-Eternal@Floettite, Incineroar (other item; Sitrus Berry 71/216)+Sirfetch’d (other item; Leek 7/9), Venusaur (other item; Focus Sash 48/75)+Charizard@Charizardite Y, Charizard@Charizardite Y+Garchomp@Choice Scarf, Venusaur (other item; Focus Sash 48/75)+Grimmsnarl@Light Clay, Aerodactyl@Aerodactylite+Charizard@Charizardite Y, Kingambit (other item; Focus Sash 54/120)+Torkoal (other item; Charcoal 17/17), Toxapex (other item; Leftovers 13/14)+Charizard@Charizardite Y, Kingambit (other item; Focus Sash 54/120)+Garchomp@Choice Scarf, Garchomp (other item; Life Orb 59/77)+Charizard@Charizardite Y, Sylveon (other item; Fairy Feather 88/89)+Charizard@Charizardite Y, Whimsicott (other item; Focus Sash 30/34)+Charizard@Charizardite Y, Farigiraf (other item; Sitrus Berry 203/251)+Aerodactyl@Aerodactylite, Annihilape@Choice Scarf+Swampert@Swampertite, Kingambit (other item; Focus Sash 54/120)+Camerupt@Cameruptite, Kingambit (other item; Focus Sash 54/120)+Gardevoir@Gardevoirite, Basculegion (other item; Life Orb 19/28)+Kingambit (other item; Focus Sash 54/120), Kingambit (other item; Focus Sash 54/120)+Primarina (other item; Life Orb 8/14), Charizard@Charizardite Y+Grimmsnarl@Light Clay, Aegislash (other item; Focus Sash 5/9)+Charizard@Charizardite Y, Aerodactyl@Aerodactylite+Garchomp@Choice Scarf, Kingambit (other item; Focus Sash 54/120)+Farigiraf@Grassy Seed, Farigiraf (other item; Sitrus Berry 203/251)+Glimmora@Glimmoranite, Venusaur (other item; Focus Sash 48/75)+Swampert@Swampertite, Golisopod@Golisopite+Grimmsnarl@Light Clay, Aerodactyl (other item; Focus Sash 11/12)+Kingambit (other item; Focus Sash 54/120), Farigiraf (other item; Sitrus Berry 203/251)+Blaziken@Blazikenite, Venusaur (other item; Focus Sash 48/75)+Sneasler@Psychic Seed, Farigiraf (other item; Sitrus Berry 203/251)+Sylveon (other item; Fairy Feather 88/89), Charizard@Charizardite Y+Floette-Eternal@Floettite, Sylveon (other item; Fairy Feather 88/89)+Venusaur (other item; Focus Sash 48/75), Incineroar (other item; Sitrus Berry 71/216)+Glimmora@Glimmoranite, Grimmsnarl@Light Clay+Swampert@Swampertite, Rillaboom (other item; Miracle Seed 103/150)+Annihilape@Choice Scarf, Archaludon (other item; Leftovers 360/384)+Grimmsnarl@Light Clay, Kingambit (other item; Focus Sash 54/120)+Charizard@Charizardite Y, Sylveon (other item; Fairy Feather 88/89)+Whimsicott (other item; Focus Sash 30/34), Farigiraf (other item; Sitrus Berry 203/251)+Sirfetch’d (other item; Leek 7/9), Farigiraf (other item; Sitrus Berry 203/251)+Kingambit (other item; Focus Sash 54/120), Incineroar (other item; Sitrus Berry 71/216)+Garchomp@Choice Scarf, Pelipper (other item; Focus Sash 136/265)+Grimmsnarl@Light Clay, Kingambit (other item; Focus Sash 54/120)+Raichu@Raichunite Y, Politoed (other item; Sitrus Berry 86/177)+Grimmsnarl@Light Clay, Annihilape@Choice Scarf+Charizard@Charizardite Y, Pelipper (other item; Focus Sash 136/265)+Venusaur (other item; Focus Sash 48/75), Charizard@Charizardite Y+Gardevoir@Gardevoirite, Aegislash (other item; Focus Sash 5/9)+Incineroar (other item; Sitrus Berry 71/216), Glimmora@Glimmoranite+Golisopod@Golisopite, Pelipper (other item; Focus Sash 136/265)+Annihilape@Choice Scarf, Sirfetch’d (other item; Leek 7/9)+Golisopod@Golisopite, Aerodactyl (other item; Focus Sash 11/12)+Charizard@Charizardite Y, Kingambit (other item; Focus Sash 54/120)+Pawmot (other item; Focus Sash 13/15), Farigiraf (other item; Sitrus Berry 203/251)+Garchomp (other item; Life Orb 59/77), Farigiraf (other item; Sitrus Berry 203/251)+Garchomp@Choice Scarf, Sinistcha (other item; Sitrus Berry 9/28)+Grimmsnarl@Light Clay, Farigiraf (other item; Sitrus Berry 203/251)+Charizard@Charizardite Y, Basculegion (other item; Life Orb 19/28)+Rillaboom (other item; Miracle Seed 103/150), Basculegion (other item; Life Orb 19/28)+Pelipper (other item; Focus Sash 136/265), Archaludon (other item; Leftovers 360/384)+Annihilape@Choice Scarf
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Charizard@Charizardite Y | 95.2% |
| Sylveon (other item; Fairy Feather 88/89) | 41.1% |
| Kingambit (other item; Focus Sash 54/120) | 39.7% |
| Grimmsnarl@Light Clay | 35.5% |
| Garchomp (other item; Life Orb 59/77) | 33.7% |
| Venusaur (other item; Focus Sash 48/75) | 26.0% |
| Aerodactyl@Aerodactylite | 22.6% |
| Garchomp@Choice Scarf | 18.5% |
| Whimsicott (other item; Focus Sash 30/34) | 14.2% |
| Floette-Eternal@Floettite | 7.7% |
| Basculegion (other item; Life Orb 19/28) | 5.5% |
| Toxapex (other item; Leftovers 13/14) | 5.2% |
| Aegislash (other item; Focus Sash 5/9) | 3.1% |
| Annihilape@Choice Scarf | 1.4% |
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
  - [Dracador1, 295th, 30 Sep 2026](https://pokepast.es/d8dfe6b89e5dc9ab)
  - [Stefan Specht, 295th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0384/teamlist)
  - [Thomas Dervan, 124th, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0256/teamlist)
  - [Xena, , 10 Sep 2026](https://pokepast.es/b7d841f59242636e)
  - [Eliseo Torres-Morales, 1036th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0353/teamlist)
  - [Joshua Hoitink, 92nd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0053/teamlist)
  - [Patrick Verrelli, 108th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0102/teamlist)
  - [Robert Pamplin, 744th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0562/teamlist)
  - [Nils von Lengerke, 456th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0950/teamlist)
  - [JOSEPH SHOWALTER, 944th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0063/teamlist)
  - [Ken Arnie Tulmo, 323rd, 27 Sep 2026](https://standings.limitlessvgc.com/0038/player/0086/teamlist)
  - [Nils Frahm, 216th, 27 Sep 2026](https://standings.limitlessvgc.com/0039/player/0529/teamlist)
  - [Eliana Stevens, 326th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0222/teamlist)
  - [Dyllan Taylor, 188th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/0157/teamlist)
  - [IAN LUTZ, 588th, 20 Sep 2026](https://standings.limitlessvgc.com/0037/player/1043/teamlist)

#### Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar) (105 primary teams, 56 distinct builds, top pair on 93/105)
- Megas on member teams: Gengar 100, Swampert 14, Froslass 13, Golisopod 12, Garchomp-Z 3, Lucario-Z 3
- Top species by team share: Gengar 95%, Incineroar 92%, Rillaboom 91%, Politoed 87%, Archaludon 70%, Vivillon 30%
- Token label: Gengar@Gengarite / Incineroar / Politoed
- Mode tags on primary teams: Rain 96, Perish Trap 96, Snow 16, Tailwind 5, Setup 4, Trick Room 3, Sun 1
- Primary teams: 105 (16.1% of the community's primary weight), hybrid teams: 14 (2.4%)
- Date range: 2026-09-09 to 2026-09-29
- Core pairs: Vivillon (other item; Focus Sash 31/31)+Rillaboom@Eject Button, Gengar@Gengarite+Rillaboom@Eject Button, Vivillon (other item; Focus Sash 31/31)+Gengar@Gengarite, Froslass@Froslassite+Rillaboom@Eject Button, Kommo-o (other item; Leftovers 19/26)+Gengar@Gengarite, Kommo-o (other item; Leftovers 19/26)+Rillaboom@Eject Button, Politoed (other item; Sitrus Berry 86/177)+Staraptor (other item; Choice Scarf 4/4), Froslass@Froslassite+Gengar@Gengarite, Politoed (other item; Sitrus Berry 86/177)+Vivillon (other item; Focus Sash 31/31), Incineroar (other item; Sitrus Berry 71/216)+Rillaboom@Eject Button, Incineroar (other item; Sitrus Berry 71/216)+Vivillon (other item; Focus Sash 31/31), Politoed (other item; Sitrus Berry 86/177)+Rillaboom@Eject Button, Incineroar (other item; Sitrus Berry 71/216)+Toxapex (other item; Leftovers 13/14), Politoed (other item; Sitrus Berry 86/177)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 71/216)+Sirfetch’d (other item; Leek 7/9), Incineroar (other item; Sitrus Berry 71/216)+Gengar@Gengarite, Incineroar (other item; Sitrus Berry 71/216)+Kommo-o (other item; Leftovers 19/26), Politoed (other item; Sitrus Berry 86/177)+Froslass@Froslassite, Kommo-o (other item; Leftovers 19/26)+Politoed (other item; Sitrus Berry 86/177), Incineroar (other item; Sitrus Berry 71/216)+Froslass@Froslassite, Kommo-o (other item; Leftovers 19/26)+Rillaboom (other item; Miracle Seed 103/150), Incineroar (other item; Sitrus Berry 71/216)+Mawile@Mawilite, Incineroar (other item; Sitrus Berry 71/216)+Camerupt@Cameruptite, Incineroar (other item; Sitrus Berry 71/216)+Glimmora@Glimmoranite, Politoed (other item; Sitrus Berry 86/177)+Volcarona (other item; Rocky Helmet 3/9), Incineroar (other item; Sitrus Berry 71/216)+Garchomp@Choice Scarf, Incineroar (other item; Sitrus Berry 71/216)+Primarina (other item; Life Orb 8/14), Baxcalibur (other item; Baxcalibrite 3/7)+Incineroar (other item; Sitrus Berry 71/216), Incineroar (other item; Sitrus Berry 71/216)+Politoed (other item; Sitrus Berry 86/177), Politoed (other item; Sitrus Berry 86/177)+Grimmsnarl@Light Clay, Aegislash (other item; Focus Sash 5/9)+Incineroar (other item; Sitrus Berry 71/216), Archaludon (other item; Leftovers 360/384)+Politoed (other item; Sitrus Berry 86/177), Incineroar (other item; Sitrus Berry 71/216)+Farigiraf@Grassy Seed, Politoed (other item; Sitrus Berry 86/177)+Golisopod@Golisopite, Archaludon (other item; Leftovers 360/384)+Vivillon (other item; Focus Sash 31/31), Archaludon (other item; Leftovers 360/384)+Rillaboom@Eject Button, Incineroar (other item; Sitrus Berry 71/216)+Raichu@Raichunite Y, Incineroar (other item; Sitrus Berry 71/216)+Rillaboom (other item; Miracle Seed 103/150), Incineroar (other item; Sitrus Berry 71/216)+Garchomp@Garchompite Z, Incineroar (other item; Sitrus Berry 71/216)+Milotic (other item; Leftovers 22/32), Archaludon (other item; Leftovers 360/384)+Gengar@Gengarite, Archaludon (other item; Leftovers 360/384)+Froslass@Froslassite
- Members:
| Token | In-sub-community support |
| :--- | :--- |
| Gengar@Gengarite | 94.3% |
| Incineroar (other item; Sitrus Berry 71/216) | 92.1% |
| Politoed (other item; Sitrus Berry 86/177) | 86.5% |
| Rillaboom@Eject Button | 63.3% |
| Vivillon (other item; Focus Sash 31/31) | 33.5% |
| Kommo-o (other item; Leftovers 19/26) | 19.2% |
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
| Rillaboom | Rillaboom (other item; Miracle Seed 103/150) | Sub-community 1: Trick Room (Mega Golisopod) | 150 | 0: 59 · 1: 48 · 3: 29 · 2: 13 · unassigned: 1 |
| Rillaboom | Rillaboom@Eject Button | Sub-community 3: Rain Perish Trap (Mega Gengar) | 67 | 3: 67 · unassigned: 0 |
| Rillaboom | Rillaboom@Grassy Seed | none | 5 | 1: 3 · 0: 2 · unassigned: 0 |
| Garchomp | Garchomp (other item; Life Orb 59/77) | Sub-community 2: Sun (Mega Charizard-Y) | 77 | 2: 66 · 0: 8 · 1: 3 · unassigned: 0 |
| Garchomp | Garchomp@Garchompite Z | Sub-community 1: Trick Room (Mega Golisopod) | 75 | 1: 43 · 0: 14 · 2: 14 · 3: 3 · unassigned: 1 |
| Garchomp | Garchomp@Choice Scarf | Sub-community 2: Sun (Mega Charizard-Y) | 38 | 2: 37 · 0: 1 · unassigned: 0 |
| Venusaur | Venusaur (other item; Focus Sash 48/75) | Sub-community 2: Sun (Mega Charizard-Y) | 75 | 2: 52 · 0: 23 · unassigned: 0 |
| Venusaur | Venusaur@Venusaurite | Sub-community 0: Rain (Mega Golisopod) | 9 | 0: 4 · 1: 3 · 2: 1 · unassigned: 1 |
| Basculegion | Basculegion@Choice Scarf | Sub-community 0: Rain (Mega Golisopod) | 64 | 0: 48 · 1: 8 · 2: 5 · 3: 3 · unassigned: 0 |
| Basculegion | Basculegion (other item; Life Orb 19/28) | Sub-community 2: Sun (Mega Charizard-Y) | 28 | 0: 13 · 2: 10 · 1: 3 · 3: 1 · 4: 1 · unassigned: 0 |
| Sneasler | Sneasler (other item; White Herb 42/51) | Sub-community 1: Trick Room (Mega Golisopod) | 51 | 0: 25 · 1: 17 · 2: 6 · 3: 3 · unassigned: 0 |
| Sneasler | Sneasler@Psychic Seed | Sub-community 0: Rain (Mega Golisopod) | 20 | 0: 17 · 2: 3 · unassigned: 0 |
| Sneasler | Sneasler@Grassy Seed | Sub-community 1: Trick Room (Mega Golisopod) | 8 | 0: 3 · 1: 3 · 2: 1 · 3: 1 · unassigned: 0 |
| Aerodactyl | Aerodactyl@Aerodactylite | Sub-community 2: Sun (Mega Charizard-Y) | 45 | 2: 43 · 0: 1 · 3: 1 · unassigned: 0 |
| Aerodactyl | Aerodactyl (other item; Focus Sash 11/12) | Sub-community 4: Aerodactyl / Tsareena | 12 | 2: 7 · 0: 2 · 1: 1 · 3: 1 · 4: 1 · unassigned: 0 |
| Raichu | Raichu@Raichunite Y | Sub-community 1: Trick Room (Mega Golisopod) | 21 | 1: 10 · 0: 8 · 3: 2 · 2: 1 · unassigned: 0 |
| Raichu | Raichu@Raichunite X | Sub-community 0: Rain (Mega Golisopod) | 6 | 0: 5 · 2: 1 · unassigned: 0 |
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
Rosters: teams with the same six species, at least buildMinCopies of them and one with a tournament placement, named by those species, with the tags and Megas of their copies and the earliest-dated pilot; 157 rosters. They change no community, assignment, or share.
- **Gholdengo / Rillaboom / Arcanine-Hisui / Sylveon / Raichu / Staraptor**, 122 copies, first parrobot7 (2026-09-14), best Champion; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Sneasler / Raichu / Rillaboom / Salamence / Gholdengo / Arcanine-Hisui**, 58 copies, first Ryan Loseto (2026-09-20), best Top 32; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Milotic / Raichu / Ceruledge / Staraptor / Gholdengo / Rillaboom**, Setup (Mega Raichu-Y, Mega Staraptor), 49 copies, first Lorenzo Arce (2026-09-20), best 5th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Excadrill / Salamence / Tyranitar / Indeedee / Corviknight / Sneasler**, Sand Psyspam (Mega Salamence, Mega Tyranitar), 45 copies, first Joseph Ugarte (2026-09-20), best 1st; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Tyranitar / Milotic / Excadrill / Rillaboom / Salamence / Gholdengo**, Sand (Mega Salamence, Mega Tyranitar), 44 copies, first lovejapanfrombr (2026-09-13), best Champion; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Froslass / Raichu / Rillaboom / Kingambit / Sneasler / Arcanine-Hisui**, Snow (Mega Froslass, Mega Raichu-Y), 39 copies, first Esa Ishaque (2026-09-20), best Top 8; in Community 0 / Sub-community 1: Kingambit + Sneasler/Salamence@Salamencite
- **Golisopod / Charizard / Politoed / Archaludon / Grimmsnarl / Farigiraf**, Sun Rain Screens Trick Room (Mega Charizard-Y, Mega Golisopod), 38 copies, first Aditya Subramanian (2026-09-20), best 2nd; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Rillaboom / Salamence / Sneasler / Incineroar / Gholdengo / Floette-Eternal**, Setup (Mega Floette, Mega Salamence), 37 copies, first Uch (2026-09-09), best Champion; in Community 0 / Sub-community 2: Setup
- **Garchomp / Farigiraf / Aerodactyl / Sylveon / Charizard / Kingambit**, Sun (Mega Aerodactyl, Mega Charizard-Y), 35 copies, first Michael Spinetta-McCarthy (2026-09-20), best Top 16; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Floette-Eternal / Raichu / Rillaboom / Incineroar / Sneasler / Gholdengo**, Setup (Mega Floette, Mega Raichu-Y), 33 copies, first Rod (2026-09-10), best 5th; in Community 0 / Sub-community 2: Setup
- **Salamence / Rillaboom / Sneasler / Arcanine-Hisui / Kingambit / Basculegion**, 24 copies, first Ryan Loseto (2026-09-10), best Champion; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Dragonite / Rillaboom / Floette-Eternal / Incineroar / Sneasler / Gholdengo**, Setup (Mega Dragonite, Mega Floette), 23 copies, first Jeffrey Lehmann (2026-09-20), best Champion; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Archaludon / Incineroar / Politoed / Vivillon / Rillaboom / Gengar**, Rain Perish Trap (Mega Gengar), 21 copies, first Brady Smith (2026-09-20), best 3rd; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Sneasler / Sinistcha / Kingambit / Floette-Eternal / Delphox / Incineroar**, Setup (Mega Delphox, Mega Floette), 21 copies, first Justin Tang (2026-09-20), best 10th; in Community 2 / Sub-community 2: Setup (Mega Delphox, Mega Floette)
- **Arcanine-Hisui / Milotic / Gholdengo / Raichu / Salamence / Rillaboom**, 21 copies, first Tom Ruggiero (2026-09-20), best 35th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Salamence / Arcanine-Hisui / Indeedee / Milotic / Metagross / Sneasler**, Psyspam (Mega Metagross, Mega Salamence), 20 copies, first Blaik Thompson (2026-09-20), best Top 4; in Community 1 / Sub-community 2: Psyspam (Mega Salamence)
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
- **Raichu / Arcanine-Hisui / Kingambit / Salamence / Rillaboom / Sneasler**, 13 copies, first Zachary Weed (2026-09-20), best 9th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Rillaboom / Salamence / Pelipper / Golisopod / Archaludon / Basculegion**, Rain (Mega Golisopod, Mega Salamence), 12 copies, first Joey McNatt (2026-09-20), best 21st; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
- **Dragapult / Arcanine-Hisui / Altaria / Metagross / Indeedee-F / Milotic**, Setup (Mega Metagross), 11 copies, first Dylan Matthews (2026-09-20), best Top 16; in Community 3 / Sub-community 1: Psyspam (Mega Staraptor)
- **Indeedee-F / Armarouge / Kingambit / Gardevoir / Torkoal / Sneasler**, Sun Psyspam Trick Room (Mega Gardevoir), 11 copies, first LosChinganas (2026-09-10), best 53rd; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Salamence / Indeedee / Sneasler / Gholdengo / Tyranitar / Excadrill**, Sand Psyspam (Mega Salamence, Mega Tyranitar), 11 copies, first Owen (2026-09-09), best 90th; in Community 1 / Sub-community 0: Sand (Mega Tyranitar, Mega Salamence)
- **Floette-Eternal / Garchomp / Rillaboom / Sneasler / Incineroar / Kingambit**, Setup (Mega Floette, Mega Garchomp-Z), 10 copies, first Ling (2026-09-10), best 3rd; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Froslass / Politoed / Incineroar / Archaludon / Gengar / Rillaboom**, Rain Snow Perish Trap (Mega Froslass, Mega Gengar), 10 copies, first Marco Silva (2026-09-13), best 5th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Arcanine-Hisui / Gholdengo / Milotic / Rillaboom / Staraptor / Raichu**, Setup (Mega Raichu-Y, Mega Staraptor), 10 copies, first Paul Maccarone (2026-09-20), best 25th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Indeedee-F / Golisopod / Staraptor / Armarouge / Dragapult / Milotic**, Psyspam Setup (Mega Golisopod, Mega Staraptor), 10 copies, first Stefan Mott (2026-09-20), best 25th; in Community 3 / Sub-community 1: Psyspam (Mega Staraptor)
- **Sneasler / Garchomp / Rillaboom / Lucario / Incineroar / Basculegion**, 10 copies, first William Brown (2026-09-20), best 28th; in Community 0 / Sub-community 2: Setup
- **Indeedee-F / Gardevoir / Sneasler / Venusaur / Charizard / Basculegion**, Sun Psyspam (Mega Charizard-Y, Mega Gardevoir), 10 copies, first Justin Tang (2026-09-09), best 209th; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Raichu / Rillaboom / Volcarona / Incineroar / Gholdengo / Garchomp**, 9 copies, first Eric Ríos (2026-09-27), best Champion; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
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
- **Kingambit / Kommo-o / Charizard / Sneasler / Indeedee / Meowstic-F**, Sun Psyspam Trick Room (Mega Charizard-Y, Mega Meowstic-F), 7 copies, first Chompy (2026-09-22), best 3rd; in Community 1 / Sub-community 2: Psyspam (Mega Salamence)
- **Garchomp / Charizard / Venusaur / Incineroar / Sylveon / Toxapex**, Sun (Mega Charizard-Y), 7 copies, first Owen Murphy (2026-09-20), best 6th; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
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
- **Salamence / Basculegion / Sneasler / Kingambit / Delphox / Rillaboom**, 5 copies, first Seung Lee (2026-09-20), best 23rd; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Gholdengo / Floette-Eternal / Incineroar / Garchomp / Charizard / Rillaboom**, Sun Setup (Mega Charizard-Y, Mega Floette), 5 copies, first Joey McGinley (2026-09-20), best 52nd; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Salamence / Kingambit / Glimmora / Volcarona / Rillaboom / Basculegion**, 5 copies, first Yuma Kinugawa (2026-09-12), best 53rd; in Community 3 / Sub-community 4: Rillaboom / Glimmora@Glimmoranite
- **Salamence / Charizard / Rillaboom / Kingambit / Sylveon / Sneasler**, Sun (Mega Charizard-Y, Mega Salamence), 5 copies, first Alex Soto (2026-09-10), best 580th; in Community 1 / Sub-community 1: Kingambit / Rillaboom
- **Gengar / Incineroar / Rillaboom / Archaludon / Pelipper / Swampert**, Rain Perish Trap (Mega Gengar, Mega Swampert), 4 copies, first giodudeVGC (2026-09-14), best 3rd; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Froslass / Incineroar / Basculegion / Glimmora / Rillaboom / Volcarona**, Snow (Mega Froslass, Mega Glimmora), 4 copies, first Valentijn Visser (2026-09-27), best 7th; in Community 3 / Sub-community 4: Rillaboom / Glimmora@Glimmoranite
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
- **Archaludon / Incineroar / Swampert / Gengar / Politoed / Rillaboom**, Rain Perish Trap (Mega Gengar, Mega Swampert), 4 copies, first Darius Helmick (2026-09-20), best 79th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
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
- **Farigiraf / Incineroar / Torkoal / Sneasler / Sylveon / Garchomp**, Sun Trick Room (Mega Garchomp-Z), 3 copies, first Sebastian Li (2026-09-27), best Runner Up; in Community 4 / Sub-community 1: Trick Room (Mega Golisopod)
- **Lucario / Indeedee-F / Armarouge / Sneasler / Salamence / Sylveon**, Psyspam Trick Room (Mega Lucario-Z, Mega Salamence), 3 copies, first Eclair_EqualAir (2026-09-12), best Top 4; in Community 3 / Sub-community 0: Psyspam (Mega Gardevoir)
- **Rillaboom / Sneasler / Incineroar / Floette-Eternal / Blastoise / Indeedee-F**, Setup Trick Room (Mega Blastoise, Mega Floette), 3 copies, first Justin Tang (2026-09-09), best Top 4; in Community 2 / Sub-community 0: Setup (Mega Floette) · Incineroar
- **Vivillon / Politoed / Gengar / Incineroar / Rillaboom / Kommo-o**, Rain Perish Trap (Mega Gengar), 3 copies, first Anthony Liuzzo (2026-09-27), best 5th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Sirfetch’d / Indeedee-F / Hatterene / Camerupt / Golisopod / Armarouge**, Psyspam Trick Room (Mega Camerupt, Mega Golisopod), 3 copies, first espertcg (2026-09-19), best 9th; in Community 3 / Sub-community 2: Trick Room Psyspam (Mega Camerupt)
- **Salamence / Gholdengo / Rillaboom / Milotic / Raichu / Ceruledge**, Setup (Mega Raichu-Y, Mega Salamence), 3 copies, first yura (2026-09-13), best 9th; in Community 0 / Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo
- **Incineroar / Gengar / Rillaboom / Kommo-o / Ninetales-Alola / Kingambit**, Snow Perish Trap (Mega Gengar), 3 copies, first Minche Chung (2026-09-20), best 12th; in Community 4 / Sub-community 3: Rain Perish Trap (Mega Gengar)
- **Farigiraf / Sylveon / Whimsicott / Garchomp / Kingambit / Charizard**, Sun (Mega Charizard-Y), 3 copies, first Jamie Borenstein (2026-09-27), best 18th; in Community 4 / Sub-community 2: Sun (Mega Charizard-Y)
- **Gengar / Incineroar / Kingambit / Sneasler / Altaria / Rillaboom**, Perish Trap (Mega Altaria, Mega Gengar), 3 copies, first Paschalis Dermentzis (2026-09-27), best 20th; in Community 0 / Sub-community 2: Setup
- **Gengar / Starmie / Archaludon / Indeedee-F / Sneasler / Pelipper**, Rain Psyspam (Mega Gengar, Mega Starmie), 3 copies, first clearmomdotcom (2026-09-27), best 22nd; in Community 4 / Sub-community 0: Rain (Mega Golisopod)
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
- Hybrid teams: 1093
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
| https://pokepast.es/b9b3d0ee2ee1b26a | 3 | 1 |
| https://pokepast.es/e2bab80c4e53d89a | 2 | 1 |
| https://pokepast.es/202c514602d9abe9 | 0 | 1, 2 |
| https://pokepast.es/42f46820310783d1 | 1 | 2, 0 |
| https://pokepast.es/595b9623f782bb8f | 3 | 1 |
| https://pokepast.es/24b7e1e280b1088f | 2 | 0 |
| https://pokepast.es/785dbfcd2432c102 | 1 | 2, 0 |
| https://pokepast.es/d8faaf4f72f6aab5 | 3 | 1 |
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
| https://pokepast.es/55029c098a5a309b | 0 | 2 |
| https://pokepast.es/674ac2a18201012a | 1 | 0 |
| https://pokepast.es/9fed7bfc061ad8bb | 0 | 2, 1 |
| https://pokepast.es/376176212f88e4cb | 4 | 3 |
| https://pokepast.es/6f1b0e8df51cc57d | 0 | 2, 1 |
| https://pokepast.es/183a005c8a677937 | 2 | 0 |
| https://pokepast.es/33b3042210ffc178 | 0 | 2, 1 |
| https://pokepast.es/8e37c3b00cbba6b6 | 0 | 4, 1 |
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
| https://pokepast.es/f85b026e5b0e6567 | 1 | 2, 0, 4 |
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
| https://standings.limitlessvgc.com/0037/player/0173/teamlist | 2 | 0, 3, 4 |
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
| https://standings.limitlessvgc.com/0037/player/0848/teamlist | 0 | 3, 2 |
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
| https://standings.limitlessvgc.com/0037/player/0185/teamlist | 1 | 0, 2, 4 |
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
| https://standings.limitlessvgc.com/0037/player/0418/teamlist | 2 | 0, 1 |
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
| https://standings.limitlessvgc.com/0039/player/1054/teamlist | 2 | 1 |
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
| https://standings.limitlessvgc.com/0039/player/0064/teamlist | 1 | 4, 3 |
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
| https://standings.limitlessvgc.com/0039/player/0617/teamlist | 2 | 0, 3, 4 |
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
| https://standings.limitlessvgc.com/0039/player/0622/teamlist | 0 | 2, 4 |
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
| https://standings.limitlessvgc.com/0039/player/0950/teamlist | 4 | 2, 0 |
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
| https://standings.limitlessvgc.com/0039/player/0236/teamlist | 2 | 3, 0, 4 |
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
| https://pokepast.es/50398ef3664d4646 | 2 | 0 |
| https://pokepast.es/7e97a19e8093f20b | 0 | 1 |
| https://pokepast.es/80bf9f59ac2d2460 | 0 | 1 |
| https://pokepast.es/32857b1c3f3763e0 | 2 | 1 |
| https://pokepast.es/297320b831fb73b8 | 4 | 2 |
| https://pokepast.es/3ad14d3332150d81 | 1 | 2, 4 |
| https://pokepast.es/856c378ecfafa498 | 3 | 1, 0 |
| https://pokepast.es/1916aa3a980dc964 | 3 | 1 |
| https://pokepast.es/65dcc792999cfb88 | 2 | 0 |
| https://pokepast.es/7af5d780689b5a8b | 4 | 2 |
| https://pokepast.es/098c5931bc868979 | 3 | 0, 2 |
| https://pokepast.es/f04e9a505bca4531 | 2 | 0 |
| https://pokepast.es/bc36f6f2b942701f | 2 | 0, 1 |
| https://pokepast.es/0fd7f8e614d0b6c5 | 0 | 1 |
| https://pokepast.es/4f38b7f69d069b67 | 1 | 0 |
| https://pokepast.es/175ee651eef60e92 | 4 | 1 |
| https://pokepast.es/e2b7674a54debee7 | 3 | 1 |
| https://pokepast.es/753cf48da749168a | 2 | 1 |
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
| Community 0: Rillaboom / Raichu / Gholdengo | 623 | 50 | 0 | 2 | 0 | 138 | 15 | 7 | 3 | 36 | 20 | 6 | 1 | 28 | 0 | 55 |
| Community 1: Sneasler / Salamence | 95 | 89 | 64 | 240 | 0 | 0 | 1 | 0 | 6 | 10 | 33 | 1 | 5 | 0 | 0 | 53 |
| Community 2: Setup (Mega Floette) | 0 | 169 | 0 | 0 | 0 | 2 | 4 | 6 | 0 | 7 | 0 | 1 | 7 | 3 | 0 | 33 |
| Community 3: Psyspam | 2 | 3 | 179 | 2 | 0 | 1 | 1 | 0 | 94 | 3 | 15 | 0 | 17 | 17 | 42 | 106 |
| Community 4: Rain | 1 | 22 | 8 | 0 | 230 | 1 | 99 | 103 | 3 | 43 | 1 | 54 | 20 | 0 | 4 | 65 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 8 |
- Cluster majority:
| Cluster | Archetype | Majority community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Community 0: Rillaboom / Raichu / Gholdengo | 86.4% |
| cluster-2 | Mega Floette-Eternal Balance | Community 2: Setup (Mega Floette) | 50.8% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Community 3: Psyspam | 71.3% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Community 1: Sneasler / Salamence | 98.4% |
| cluster-5 | Mega Golisopod + Archaludon/Pelipper Rain | Community 4: Rain | 100.0% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Community 0: Rillaboom / Raichu / Gholdengo | 97.2% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Community 4: Rain | 82.5% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Community 4: Rain | 88.8% |
| cluster-9 | Mega Staraptor Psyspam Offense | Community 3: Psyspam | 88.7% |
| cluster-10 | Mega Salamence + Rillaboom/Incineroar Trick Room | Community 4: Rain | 43.4% |
| cluster-11 | Mega Salamence + Rillaboom/Kingambit Balance | Community 1: Sneasler / Salamence | 47.8% |
| cluster-12 | Mega Golisopod Rain | Community 4: Rain | 87.1% |
| cluster-13 | Mega Charizard Tailwind Offense | Community 4: Rain | 40.0% |
| cluster-14 | Mega Raichu Snow Offense | Community 0: Rillaboom / Raichu / Gholdengo | 58.3% |
| cluster-15 | Mega Camerupt + Indeedee-F/Farigiraf Psyspam Trick Room | Community 3: Psyspam | 91.3% |

### Sub-communities of Community 0: Rillaboom / Raichu / Gholdengo vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-4 (Mega Salamence + Tyranitar/Excadrill Sand Balance) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-8 (Mega Gengar + Rillaboom/Incineroar Rain) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Salamence + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Salamence + Rillaboom/Kingambit Balance) | cluster-12 (Mega Golisopod Rain) | cluster-13 (Mega Charizard Tailwind Offense) | cluster-14 (Mega Raichu Snow Offense) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 442 | 4 | 1 | 115 | 4 | 0 | 3 | 15 | 12 | 1 | 1 | 6 | 41 |
| Sub-community 2: Setup | 119 | 45 | 0 | 21 | 8 | 1 | 0 | 19 | 0 | 5 | 0 | 20 | 4 |
| Sub-community 4: Volcarona / Glimmora@Glimmoranite | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 1 | 3 |
| Sub-community 5: Perish Trap (Mega Gengar) | 0 | 0 | 0 | 1 | 0 | 6 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| minor | 62 | 1 | 0 | 1 | 3 | 0 | 0 | 2 | 5 | 0 | 0 | 0 | 3 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 4 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 70.9% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 2: Setup | 90.0% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 50.0% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 83.3% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 2: Setup | 53.3% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Sub-community 5: Perish Trap (Mega Gengar) | 85.7% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 100.0% |
| cluster-10 | Mega Salamence + Rillaboom/Incineroar Trick Room | Sub-community 2: Setup | 52.8% |
| cluster-11 | Mega Salamence + Rillaboom/Kingambit Balance | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 60.0% |
| cluster-12 | Mega Golisopod Rain | Sub-community 2: Setup | 83.3% |
| cluster-13 | Mega Charizard Tailwind Offense | Sub-community 0: Rillaboom / Raichu@Raichunite Y / Gholdengo | 100.0% |
| cluster-14 | Mega Raichu Snow Offense | Sub-community 2: Setup | 71.4% |

### Sub-communities of Community 1: Sneasler / Salamence vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-3 (Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense) | cluster-4 (Mega Salamence + Tyranitar/Excadrill Sand Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Salamence + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Salamence + Rillaboom/Kingambit Balance) | cluster-12 (Mega Golisopod Rain) | cluster-13 (Mega Charizard Tailwind Offense) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 19 | 1 | 0 | 204 | 0 | 2 | 1 | 0 | 0 | 0 | 15 |
| Sub-community 1: Kingambit / Rillaboom | 66 | 83 | 43 | 1 | 1 | 0 | 9 | 31 | 0 | 3 | 13 |
| Sub-community 2: Psyspam (Mega Salamence) | 9 | 5 | 21 | 35 | 0 | 4 | 0 | 2 | 1 | 2 | 20 |
| unassigned | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 5 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 1: Kingambit / Rillaboom | 69.5% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 1: Kingambit / Rillaboom | 93.3% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Sub-community 1: Kingambit / Rillaboom | 67.2% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Sub-community 0: Sand (Mega Tyranitar, Mega Salamence) | 85.0% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 1: Kingambit / Rillaboom | 100.0% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 2: Psyspam (Mega Salamence) | 66.7% |
| cluster-10 | Mega Salamence + Rillaboom/Incineroar Trick Room | Sub-community 1: Kingambit / Rillaboom | 90.0% |
| cluster-11 | Mega Salamence + Rillaboom/Kingambit Balance | Sub-community 1: Kingambit / Rillaboom | 93.9% |
| cluster-12 | Mega Golisopod Rain | Sub-community 2: Psyspam (Mega Salamence) | 100.0% |
| cluster-13 | Mega Charizard Tailwind Offense | Sub-community 1: Kingambit / Rillaboom | 60.0% |

### Sub-communities of Community 2: Setup (Mega Floette) vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-2 (Mega Floette-Eternal Balance) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-8 (Mega Gengar + Rillaboom/Incineroar Rain) | cluster-10 (Mega Salamence + Rillaboom/Incineroar Trick Room) | cluster-12 (Mega Golisopod Rain) | cluster-13 (Mega Charizard Tailwind Offense) | cluster-14 (Mega Raichu Snow Offense) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Setup (Mega Floette) · Incineroar | 88 | 1 | 3 | 1 | 5 | 0 | 7 | 0 | 5 |
| Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 31 | 1 | 0 | 2 | 2 | 1 | 0 | 3 | 1 |
| Sub-community 2: Setup (Mega Delphox, Mega Floette) | 46 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 26 |
| Sub-community 3: Gengar@Gengarite / Kommo-o | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 0 |
| minor | 4 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 0: Setup (Mega Floette) · Incineroar | 52.1% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Sub-community 0: Setup (Mega Floette) · Incineroar | 50.0% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 0: Setup (Mega Floette) · Incineroar | 75.0% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Sub-community 3: Gengar@Gengarite / Kommo-o | 50.0% |
| cluster-10 | Mega Salamence + Rillaboom/Incineroar Trick Room | Sub-community 0: Setup (Mega Floette) · Incineroar | 71.4% |
| cluster-12 | Mega Golisopod Rain | Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 100.0% |
| cluster-13 | Mega Charizard Tailwind Offense | Sub-community 0: Setup (Mega Floette) · Incineroar | 100.0% |
| cluster-14 | Mega Raichu Snow Offense | Sub-community 1: Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 100.0% |

### Sub-communities of Community 3: Psyspam vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-3 (Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense) | cluster-4 (Mega Salamence + Tyranitar/Excadrill Sand Balance) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Salamence + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Salamence + Rillaboom/Kingambit Balance) | cluster-13 (Mega Charizard Tailwind Offense) | cluster-14 (Mega Raichu Snow Offense) | cluster-15 (Mega Camerupt + Indeedee-F/Farigiraf Psyspam Trick Room) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Psyspam (Mega Gardevoir) | 2 | 1 | 144 | 2 | 0 | 0 | 40 | 0 | 0 | 2 | 0 | 1 | 24 |
| Sub-community 1: Psyspam (Mega Staraptor) | 0 | 0 | 3 | 0 | 0 | 0 | 45 | 0 | 0 | 0 | 0 | 0 | 21 |
| Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 0 | 0 | 10 | 0 | 0 | 1 | 9 | 0 | 0 | 1 | 0 | 41 | 16 |
| Sub-community 3: Psyspam · Whimsicott | 0 | 0 | 20 | 0 | 0 | 0 | 0 | 0 | 0 | 14 | 1 | 0 | 17 |
| minor | 0 | 2 | 2 | 0 | 0 | 0 | 0 | 3 | 15 | 0 | 16 | 0 | 20 |
| unassigned | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 8 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 0: Psyspam (Mega Gardevoir) | 100.0% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 66.7% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Sub-community 0: Psyspam (Mega Gardevoir) | 80.4% |
| cluster-4 | Mega Salamence + Tyranitar/Excadrill Sand Balance | Sub-community 0: Psyspam (Mega Gardevoir) | 100.0% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 100.0% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 1: Psyspam (Mega Staraptor) | 47.9% |
| cluster-10 | Mega Salamence + Rillaboom/Incineroar Trick Room | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 100.0% |
| cluster-11 | Mega Salamence + Rillaboom/Kingambit Balance | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 100.0% |
| cluster-13 | Mega Charizard Tailwind Offense | Sub-community 3: Psyspam · Whimsicott | 82.4% |
| cluster-14 | Mega Raichu Snow Offense | Sub-community 4: Rillaboom / Glimmora@Glimmoranite | 94.1% |
| cluster-15 | Mega Camerupt + Indeedee-F/Farigiraf Psyspam Trick Room | Sub-community 2: Trick Room Psyspam (Mega Camerupt) | 97.6% |

### Sub-communities of Community 4: Rain vs. cluster archetypes
Rows: each of the community's primary teams by its primary sub-community (minor sub-communities pooled, or unassigned); columns: its cluster from the cluster CLI. The majority is over each cluster's teams inside the community.
| Sub-community | cluster-1 (Mega Raichu Balance) | cluster-2 (Mega Floette-Eternal Balance) | cluster-3 (Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense) | cluster-5 (Mega Golisopod + Archaludon/Pelipper Rain) | cluster-6 (Mega Raichu + Rillaboom/Gholdengo Balance) | cluster-7 (Mega Charizard + Garchomp/Farigiraf Sun-Room) | cluster-8 (Mega Gengar + Rillaboom/Incineroar Rain) | cluster-9 (Mega Staraptor Psyspam Offense) | cluster-10 (Mega Salamence + Rillaboom/Incineroar Trick Room) | cluster-11 (Mega Salamence + Rillaboom/Kingambit Balance) | cluster-12 (Mega Golisopod Rain) | cluster-13 (Mega Charizard Tailwind Offense) | cluster-15 (Mega Camerupt + Indeedee-F/Farigiraf Psyspam Trick Room) | unclustered |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Sub-community 0: Rain (Mega Golisopod) | 1 | 1 | 4 | 135 | 1 | 0 | 2 | 3 | 16 | 0 | 49 | 0 | 0 | 18 |
| Sub-community 1: Trick Room (Mega Golisopod) | 0 | 15 | 0 | 34 | 0 | 18 | 0 | 0 | 27 | 0 | 5 | 0 | 2 | 23 |
| Sub-community 2: Sun (Mega Charizard-Y) | 0 | 4 | 4 | 61 | 0 | 81 | 1 | 0 | 0 | 1 | 0 | 20 | 2 | 15 |
| Sub-community 3: Rain Perish Trap (Mega Gengar) | 0 | 2 | 0 | 0 | 0 | 0 | 100 | 0 | 0 | 0 | 0 | 0 | 0 | 3 |
| minor | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
| unassigned | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 5 |
- Cluster majority:
| Cluster | Archetype | Majority sub-community | % |
| :--- | :--- | :--- | :--- |
| cluster-1 | Mega Raichu Balance | Sub-community 0: Rain (Mega Golisopod) | 100.0% |
| cluster-2 | Mega Floette-Eternal Balance | Sub-community 1: Trick Room (Mega Golisopod) | 68.2% |
| cluster-3 | Mega Gardevoir + Indeedee-F/Sneasler Psyspam Offense | Sub-community 0: Rain (Mega Golisopod) | 50.0% |
| cluster-5 | Mega Golisopod + Archaludon/Pelipper Rain | Sub-community 0: Rain (Mega Golisopod) | 58.7% |
| cluster-6 | Mega Raichu + Rillaboom/Gholdengo Balance | Sub-community 0: Rain (Mega Golisopod) | 100.0% |
| cluster-7 | Mega Charizard + Garchomp/Farigiraf Sun-Room | Sub-community 2: Sun (Mega Charizard-Y) | 81.8% |
| cluster-8 | Mega Gengar + Rillaboom/Incineroar Rain | Sub-community 3: Rain Perish Trap (Mega Gengar) | 97.1% |
| cluster-9 | Mega Staraptor Psyspam Offense | Sub-community 0: Rain (Mega Golisopod) | 100.0% |
| cluster-10 | Mega Salamence + Rillaboom/Incineroar Trick Room | Sub-community 1: Trick Room (Mega Golisopod) | 62.8% |
| cluster-11 | Mega Salamence + Rillaboom/Kingambit Balance | Sub-community 2: Sun (Mega Charizard-Y) | 100.0% |
| cluster-12 | Mega Golisopod Rain | Sub-community 0: Rain (Mega Golisopod) | 90.7% |
| cluster-13 | Mega Charizard Tailwind Offense | Sub-community 2: Sun (Mega Charizard-Y) | 100.0% |
| cluster-15 | Mega Camerupt + Indeedee-F/Farigiraf Psyspam Trick Room | Sub-community 1: Trick Room (Mega Golisopod) | 50.0% |

## 10. Set Variants and Role Tags
### Rillaboom
- Teams: 1620, weighted support: 54.6%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Community: Community 1: Sneasler / Salamence
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
- Community: Community 1: Sneasler / Salamence
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
- Community: Community 2: Setup (Mega Floette)
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
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Community: Community 1: Sneasler / Salamence
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
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Community: Community 3: Psyspam
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
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Community: Community 4: Rain
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
- Community: Community 3: Psyspam
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
- Community: Community 4: Rain
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
- Community: Community 4: Rain
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
- Community: Community 4: Rain
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
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Community: Community 4: Rain
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
- Community: Community 2: Setup (Mega Floette)
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
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Community: Community 4: Rain
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
- Community: Community 1: Sneasler / Salamence
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
- Community: Community 1: Sneasler / Salamence
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
- Community: Community 3: Psyspam
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
- Community: Community 3: Psyspam
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
- Community: Community 4: Rain
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
- Community: Community 1: Sneasler / Salamence
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
- Community: Community 3: Psyspam
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
- Community: Community 1: Sneasler / Salamence
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
- Community: Community 4: Rain
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

### Metagross
- Teams: 160, weighted support: 5.4%, mega share: 97.4%
- Community: Community 3: Psyspam
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

### Sinistcha
- Teams: 134, weighted support: 4.6%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
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
- Community: Community 2: Setup (Mega Floette)
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
- Community: Community 4: Rain
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
- Community: Community 3: Psyspam
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
- Community: Community 3: Psyspam
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
- Community: Community 4: Rain
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
- Teams: 82, weighted support: 3.1%, mega share: 0.0%
- Community: Community 0: Rillaboom / Raichu / Gholdengo
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
- Teams: 88, weighted support: 3.1%, mega share: 87.5%
- Community: Community 2: Setup (Mega Floette)
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
- Community: Community 3: Psyspam
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
- Community: Community 1: Sneasler / Salamence
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

### Annihilape
- Teams: 54, weighted support: 2.0%, mega share: 0.0%
- Community: Community 3: Psyspam
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
- Community: Community 3: Psyspam
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
- Teams: 50, weighted support: 1.7%, mega share: 96.1%
- Community: Community 2: Setup (Mega Floette)
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
- Teams: 50, weighted support: 1.7%, mega share: 68.0%
- Community: Community 3: Psyspam
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
- Teams: 38, weighted support: 1.5%, mega share: 0.0%
- Community: Community 4: Rain
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
- Teams: 28, weighted support: 0.9%, mega share: 100.0%
- Community: Community 4: Rain
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

### Espathra
- Teams: 18, weighted support: 0.7%, mega share: 0.0%
- Community: Community 2: Setup (Mega Floette)
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

### Altaria
- Teams: 19, weighted support: 0.7%, mega share: 25.9%
- Community: Community 3: Psyspam
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
- Teams: 19, weighted support: 0.6%, mega share: 0.0%
- Community: Community 4: Rain
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

### Rotom-Wash
- Teams: 14, weighted support: 0.5%, mega share: 0.0%
- Community: Community 3: Psyspam
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
- Teams: 11, weighted support: 0.3%, mega share: 0.0%
- Community: Community 3: Psyspam
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
- Teams: 13, weighted support: 0.3%, mega share: 100.0%
- Community: Community 3: Psyspam
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
- Community: Community 4: Rain
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
- Teams: 7, weighted support: 0.2%, mega share: 18.5%
- Community: Community 4: Rain
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

### Gliscor
- Teams: 5, weighted support: 0.2%, mega share: 0.0%
- Community: none
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

### Bellibolt
- Teams: 6, weighted support: 0.1%, mega share: 0.0%
- Community: none
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
- Teams: 4, weighted support: 0.1%, mega share: 100.0%
- Community: Community 1: Sneasler / Salamence
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

## 12. Sheet vs. Ladder
Ladder: median rank over 15 daily snapshots from 2026-09-16 to 2026-09-30 (M6, M-C, Doubles), those archived within the sheet window (2026-09-09 to 2026-10-01); snapshots are kept 14 days, so a longer window is compared over at most its last 14 days. Teammates and items are from 2026-09-30. Battle data provided by Pokémon Champions Battle Data (https://championsbattledata.com); only figures derived from these snapshots are shown.
Sheet = shared teams (2627 of 2957 placed; of the window's teams, only those with a placement are results; the rest are social shares, videos, and ladder pastes), not only Bo3 open-sheet results.
Ladder teammates: the site's top 8 in its order (metric unstated); absence means not in the top 8, not rare.

### 12.1 Coverage
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

### 12.4 Ladder-calibrated view (λ = 1)
A model built from the ladder's rank order, not a ladder usage share: among the sheet's species nodes with a ladder entry, the i-th in the ladder order takes the i-th highest sheet support as its rank-matched support, and each team's weight is multiplied once by the geometric mean over its species of (rank-matched support / sheet support)^λ. The sheet's communities, cores, labels, and assignments are unchanged; a species thin or absent on the sheet (outside its species nodes) can't be reweighted. Unlike the coverage table (§12.1, the ladder's top 60), this view reweights every sheet species node the ladder lists, at any rank.
Calibrated support need not land on its rank-matched support: a species' factor is averaged with its teammates' in each team's multiplier and every share is renormalised, so it usually moves only part of the way, and can stop short, overshoot, or even move against its own factor; λ = 1 does not mean the ladder is matched. Mapping the i-th in the ladder order to the i-th sheet support assumes the ladder's usage curve has the sheet's shape; where sheet supports are compressed, one or two ranks swing a factor a lot. Where the two orders agree the factor is exactly one by construction, which does not mean equal usage; such a species still moves through its teammates and the renormalisation. Reweighting scales the sheet's own teams: it cannot add a ladder build the sheet lacks, so it corrects how much of each sheet archetype appears, not which archetypes exist, and sub-community labels keep the sheet's items.
Effective teams (Kish): 2735.34 under the sheet weights, 2669.89 calibrated; team weight multiplier 0.57 to 1.82.

#### Definitions
Each archetype definition of §13 by rank, its variants under it. Share = its teams over the window's teams, every roster copy counting one (§13's Share column); calibrated share = the same teams each weighted by its team multiplier above, over every window team so weighted. Each team's base weight is one (no placement weighting), so the difference is the calibration alone. A model built from the ladder's rank order, not a ladder usage share.
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

#### Communities
| Community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Rillaboom / Raichu / Gholdengo | 33.9% | 28.5% | 13.5% | 13.1% |
| 1 | Sneasler / Salamence | 19.2% | 19.4% | 15.2% | 15.4% |
| 2 | Setup (Mega Floette) | 7.9% | 7.7% | 7.5% | 7.4% |
| 3 | Psyspam | 16.1% | 19.0% | 5.4% | 6.0% |
| 4 | Rain | 22.6% | 25.2% | 4.5% | 4.9% |
| 5 | Pincurchin / Raichu-Alola | 0.0% | 0.0% | 0.0% | 0.0% |
| — | Unassigned | 0.3% | 0.3% | — | — |

#### Sub-communities of Community 0: Rillaboom / Raichu / Gholdengo (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Rillaboom / Raichu@Raichunite Y / Gholdengo | 67.2% | 63.9% | 12.9% | 13.6% |
| 1 | Kingambit + Sneasler/Salamence@Salamencite | 7.4% | 7.7% | 10.6% | 10.9% |
| 2 | Setup | 23.0% | 25.7% | 6.1% | 6.6% |
| 3 | Golisopod@Golisopite / Farigiraf@Grassy Seed | 0.3% | 0.4% | 1.5% | 1.8% |
| 4 | Volcarona / Glimmora@Glimmoranite | 0.9% | 1.0% | 1.1% | 1.1% |
| 5 | Perish Trap (Mega Gengar) | 0.7% | 0.8% | 0.7% | 0.7% |
| 6 | Garchomp / Whimsicott | 0.0% | 0.0% | 0.2% | 0.2% |
| — | Unassigned | 0.5% | 0.7% | — | — |

#### Sub-communities of Community 1: Sneasler / Salamence (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Sand (Mega Tyranitar, Mega Salamence) | 42.2% | 41.1% | 6.6% | 6.5% |
| 1 | Kingambit / Rillaboom | 40.3% | 40.7% | 1.8% | 1.9% |
| 2 | Psyspam (Mega Salamence) | 16.4% | 17.1% | 2.2% | 2.3% |
| — | Unassigned | 1.2% | 1.1% | — | — |

#### Sub-communities of Community 2: Setup (Mega Floette) (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Setup (Mega Floette) · Incineroar | 43.6% | 43.1% | 15.1% | 15.0% |
| 1 | Rillaboom / Garchomp@Garchompite Z / Volcarona@Grassy Seed | 17.2% | 19.1% | 16.3% | 16.3% |
| 2 | Setup (Mega Delphox, Mega Floette) | 35.3% | 34.2% | 8.9% | 8.8% |
| 3 | Gengar@Gengarite / Kommo-o | 1.3% | 1.2% | 2.4% | 2.3% |
| 4 | Absol@Absolite Z / Espathra / Goodra-Hisui | 2.1% | 2.0% | 0.0% | 0.0% |
| — | Unassigned | 0.4% | 0.4% | — | — |

#### Sub-communities of Community 3: Psyspam (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Psyspam (Mega Gardevoir) | 41.6% | 46.3% | 5.8% | 5.7% |
| 1 | Psyspam (Mega Staraptor) | 15.7% | 12.7% | 3.6% | 3.7% |
| 2 | Trick Room Psyspam (Mega Camerupt) | 16.7% | 16.7% | 1.6% | 1.7% |
| 3 | Psyspam · Whimsicott | 11.8% | 10.8% | 1.3% | 1.2% |
| 4 | Rillaboom / Glimmora@Glimmoranite | 12.0% | 11.3% | 2.1% | 2.0% |
| 5 | Blastoise@Blastoisinite / Sinistcha | 0.1% | 0.1% | 0.2% | 0.2% |
| — | Unassigned | 2.0% | 2.0% | — | — |

#### Sub-communities of Community 4: Rain (shares of the parent's primary team weight)
| Sub-community | Label | Sheet primary | Calibrated primary | Sheet hybrid | Calibrated hybrid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | Rain (Mega Golisopod) | 34.7% | 37.3% | 16.3% | 16.3% |
| 1 | Trick Room (Mega Golisopod) | 18.0% | 18.3% | 16.5% | 17.1% |
| 2 | Sun (Mega Charizard-Y) | 30.3% | 29.8% | 8.4% | 8.5% |
| 3 | Rain Perish Trap (Mega Gengar) | 16.1% | 13.6% | 2.4% | 2.5% |
| 4 | Aerodactyl / Tsareena | 0.1% | 0.1% | 0.2% | 0.2% |
| — | Unassigned | 0.9% | 0.9% | — | — |

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

## 13. Definitions
Over team_query's 2957 teams shared 2026-09-09 to 2026-10-01: 24 definitions and 19 variants from 218 candidates (346 seeds); floor 45 teams, ceiling 1479 teams; admission ran 4 rounds, 3 absorbed. Teams: exclusive 1353 (45.8%), hybrid 1136 (38.4%), uncovered 468 (15.8%) (a variant counts as its parent). Recent: 1484 teams dated 2026-09-25 to 2026-10-01; earlier: 1473 teams.

### 13.1 Ranked definitions
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

### 13.2 Definitions in detail
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

### 13.3 Refused candidates
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

### 13.4 Mode extension
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

### 13.5 Uncovered teams
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
