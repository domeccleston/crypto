# Notes on Crypto

I've been vaguely interested in crypto for a while, but not seriously so. In 2021 I've belatedly resolved to
acquire a decent understanding of how it works.

# Cryptocurrency

## Why do we need cryptocurrency?

- Cryptocurrency = ledger - trust + cryptography.

- Say that some friends often lend money to each other. It's inconvenient to keep passing cash around. Instead,
  you can keep a ledger: note down all the transactions, and at the end of the month we can settle up.

- This works fine, with friends. What if we're in a situation of lower trust? A conniving debtor might lie about
  their amount, or add a false payment.

- A simple way of preventing this would be ensuring that everyone sign their name. We could check if a transaction
  has been signed with the correct signature.

- But it's easy enough to forge a paper signature.

- How could we create a digital signature? It seems as though we could just copy whatever data makes up that signature.

- The answer is using a public-key/private-key pair. The latter is also known as a secret key.

  1. Signature = sign(message, sk). This ensures that a new signature is generated every time, and can't be copied
  over from previous signings.

  2. Verifification = verify(message, signature, pk). This returns true or false depending on whether the
  signature was generated from a private key linked to the current public key.

  - Signatures are 256-bit numbers, i.e. there are 2<sup>256</sup> possible signatures.

- With secure digital signatures, it's now possible for the group to keep a secure ledger.

- The final element required is a unique ID for each transaction, so that we can identify between transactions of
  the same amount and participants.

- At this stage, we're still using cash at the end of the month to tally up. But actually, this is no longer needed.
  We could just say that everyone starts with 100∆, and we can use that money to pay each other, but no one can pay
  more than they have on the ledger.

- We could then establish some sort of a rate of exchange between ∆ and ¢.

- This removes another element of trust: we don't need to trust that people will settle up in dollars.

- But at this stage, we still need to have trust in some centralized host of the ledger.

- We can overcome this through having everyone keep their own copy of the ledger. When a transaction occurs,
  we broadcast it out to the rest of the network.

- But this has obvious problems: how can we ensure that everyone else has a ledger that exactly matches yours?

- **This is the problem that motivated the original bitcoin whitepaper**.

## Bitcoin

- The solution that Bitcoin provided to that problem is to trust whatever ledger has the most computational work
  put into it.

- The reason for this is that using proof of work can make it unfeasible to generate fraudulent transactions, due
  to the amount of work required. Why?

- ### Hash functions

  - Hash functions take a message and generate a random-looking but deterministic output (or 'digest'). Hash functions are one-way: it's computationally unfeasible to generate input from output.
  - This means that if I have some output and am trying to figure out what message I need to pass into SHA256 to generate that output, the best I can do is randomly guess.
  - But making 2<sup>256</sup> guesses would take a long time.<sup>i</sup>
  - How can such a function prove that a particular list of transactions is associated with a large amount of computational effort?

### Block hashing

- Say we see a number that has been hashed with SHA256 that begins with 30 zeroes. This would (on average) take a long time to generate, because it would take many millions of inputs to generate such a rare output.

- This is called proof of work.

- Importantly, it's tied to the transaction history. If the transaction history were changed, it would take a very long time to recompute a hash value that the hash of the altered list, along with the new data, leads with 30 zeroes.

- The core idea of the bitcoin whitepaper is that we trust whatever ledger has the most work put into it

- #### Blocks

  - Blocks contain:
    1. A list of transations
    2. A proof of work
    3. A link to the hash of the previous block
        - This means that changing any block changes any subsequent blocks.

### Mining blocks

- This chain of blocks is what constitutes a blockchain.

- Anyone in the world can be a block creator. They can listen for transactions being broadcast, collect them into a block, and then do some work to find some number that makes the hash of that block start with 60 zeroes.

- Once they find it, they broadcast out the block they find.

- To reward the block creator for this work, when they put together a block, they will be allowed to add a special transaction to the top of it that conjures ∆10 out of thin air. This is the block reward. The act of creating blocks is referred to as 'mining'.

### Defer to the longest chain

- So, miners need to listen to transactions in order to create blocks.

- For people interested in *making* transactions, they need to do the inverse: listen for blocks being broadcast by miners, and updating their own personal copy of the blockchain.

- **Here is where Bitcoin solves the problem of ensuring consistency between ledgers**.

- The simple solution is that if someone hears two blockchains, they defer to the longest, the one with the most work put into it. If there is a tie, just wait until one is longer.

- If everyone agrees to defer to the longest chain, we've arrived at a decentralized consensus.

### Is that it?

- It's not immediately obvious that trusting the longest chain is sufficient to ensure safe transactions.

- What would it take to 'fool' this system?

- #### Cheating proof of work
  - Alice could try fooling Bob with a fraudulent block: she could broadcast him a block that includes her paying him 100∆, without telling the other miners, so as to trick him into thinking she'd payed him.

  - To do this, she'd need to find a valid proof of work before all the other miners, so that she could send it to him. This is possible, if she could find a block faster than all the other miners.

  - But to keep Bob believing that her ledger is correct, she'd need to continue to keep mining blocks faster than all the other miners, in order, to maintain the longest chain.

  - She might be able to keep this up for a while, but unless she has close to 50% of the total amount of mining computation capacity, she will lose out.

  - This means that you shouldn't trust any new block that appears immediately: one single block could be false. You should wait until a few blocks have been added onto it, after which it's probably safe.



---
**Footnotes**

i. There's no mathematical proof that it's hard to compute in the reverse direction, but in practice it is.

**References**

1. '*But how does bitcoin actually work?*' 3Blue1Brown, 2017
2. '*Bitcoin: a Peer-to-Peer Electronic Cash System*' Satoshi Nakamoto, 2009