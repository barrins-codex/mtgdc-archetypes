
# MTGDC-Archetypes
Format data for use with MTGDC-Parser ((https://github.com/barrins-codex/mtgdc-parser).

## Definition Maintainers

The definitions of the archetypes are currently maintained by [Spigushe](https://github.com/Spigushe).

## Data Structure

All definitions are required to be placed in the `arcehtypes` folder.

## Archetypes

This folder should contain one file for each archetype defined. Each archetype has four properties:

* Name
* IncludeColorInName
* Conditions
* Variants

The Conditions define the rules for how to match an archetype. They are an array of objects containing two properties:

```
    {
      "Type": "InMainboard",
      "Cards": ["Ad Nauseam"]
    }
```

Type must be one of the following values:

* contains
* one_or_more
* two_or_more
* never

This work in conjunction with the Cards property to define the conditions for those rules to match.

* `contains`: The card listed is required.
* `one_or_more`: At least one of the cards listed is required.
* `one_or_more`: At least two of the cards listed are required.
* `never`: The card listed must not be present.

If an archetype has multiple conditions, all must be satisfied for the archetype to match successfully.

To help understanding, here's two examples. This is the current "Ad Nauseam" archetype definition:

```
  "conditions": [
        {
            "type": "contains",
            "cards": ["Underworld Breach", "Lotus Petal", "Brain Freeze"]
        },
        {
            "type": "never",
            "cards": ["Persist"]
        }
    ]
```

It specifies three different cards with the "contains" rule, so the deck must contain all four cards to match successfully.

It also specifies that Persist is not a card that is played in this specific archetype. If all those cards are played together, it means that `Breach` is not the correct archetype, rather `Reanimator`.

This is the current "Underworld Cookbook" archetype definition.

```
  "conditions": [
        {
            "type": "contains",
            "cards": ["The Underworld Cookbook", "Asmoranomardicadaistinaculdacar"]
        },
        {
            "type": "one_or_more",
            "cards": ["Goblin Wielder", "Goblin Engineer", "Experimental Synthetizer"]
        }
    ]
```

This deck has several possible configurations of creature that can enable recursion for the Cookbook, which are listed in the "one_or_more" condition. Any combination of "Cookbook+Asmo+1xCreature that brings the cookbook back" will be a successful match.

An archetype may also include multiple Variants. Each variant has the same structure as the archetype, and for a variant to match the deck needs to first match the "main" archetype rules, then match the variant rules. This is particularly useful when you have several similar archetypes that use similar "core" but still have enough differences that you want to tag them separetely.
~~Check the ```archetypes/to_be_defined_later.js``` file for an example of variant usage.~~

## Fallbacks

One of the challenges of automated tagging is how to work with "goodstuff" decks, that don't have specific archetype-defining cards, like generic "[Deck Color] Control" or "[Deck Color] Midrange" decks. There's way too many variations between these, and creating archetypes for all of them is rather cumbersome and difficult to maintain.

MTGOArchetypeParser supports an alternative to deal with these decks by using "Fallbacks", or "Piles". They are defined as a set of "Common Cards" that are frequently used in decks of this archetype. As an example, here's the current list of common cards used for "Control" piles:

```
  "common_cards": [
        "Archmage's Charm",
        "Counterspell",
        "Creeping Tar Pit",
        "Cremate",
        "Cryptic Command",
        "Damn",
        "Disfigure",
        "Dissipate",
        "Dissolve",
        "Drawn in the Loch",
        "Emrakul, the Promised End",
        "Fact or Fiction",
        "Faerie Trickery",
        "Hall of Storm Giants",
        "Hard Evidence",
        "Hinder",
        "Jace, the Mind Sculptor",
        "Leadership Vacuum",
        "Mystic Confluence",
        "Rune Snag",
        "Shorikai, Genesis Engine",
        "Spell Crumble",
        "Supreme Verdict",
        "Teferi, Hero of Dominaria",
        "Think Twice",
        "Tithe",
        "Toxic Deluge",
        "Void Shatter",
        "Wall of Omen",
        "Wrath of God"
    ]
```

Whenever there' s a deck that it fails to identify using the Archetype rules, it'll compare this deck to *all* the fallbacks defined and see which fallback it shares the most cards with, and tag the deck using that fallback.

Notice there's a rule requiring at least 10% matching cards with a fallback for it to be accepted, so even with multiple fallbacks it's possible for a deck to fail all fallbacks.
