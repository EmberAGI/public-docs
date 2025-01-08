---
icon: gear-complex-code
description: >-
  The Action Graph holds the keys to Ember's Agent-ready DeFi paradigm. It
  serves as a comprehensive map of DeFi actions and protocols, optimally routing
  incoming intents.
---

# How it works

<figure><img src="../.gitbook/assets/Capture d’écran, le 2024-09-26 à 07.35.42 (1).png" alt=""><figcaption><p>User query “Move my ETH Compound position to wBTC on Savvy”</p></figcaption></figure>

Here we aggregate liquidity routers and protocols, their tokens and smart contracts, _and the actions that can be taken between them_. Using custom A\* and Q\* algorithms, we optimize routing between a given asset or position and another, while leveraging existing asset-to-asset routers -such as Squid, Mayan, etc.-  Then, all the steps are composed into single transactions, greatly simplifying the user and development experience

What sets the Action Graph apart is its ability to handle complex, multi-step and crosschain DeFi operations seamlessly. This allows Agents built on Ember to execute sophisticated strategies that would typically require multiple manual transactions in a single, cohesive operation.
