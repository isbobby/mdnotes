# Overview
There are four most important concepts in today's card payment ecosystem (both debit and credit cards). They are
1. A card issued by a bank, the bank is the issuer who gives the card holder an account
2. A charging merchant
3. Payment network, the linkage between the card and the card reader
4. Secure internet
## Card, Issuer & Issuer Processor
Payment systems are established around a **card** issued by a bank. This card can be a physical card, or a virtual card (with just card number), or a tokenised card (in Apple Pay).

The bank's function is to provide the card holder an underlying bank account, a debit card, and potentially access to credit facilities and a credit card.

The issuer often works with technology partners to connect with the payment network. The issuer processor usually have a connection directly to the payment network to approve or decline a transaction. Sometimes, the issuer may have built this technology in house.
## Merchant, Merchant Acquirer and Acquirer Processor
A merchant - physical or online - receives payment from a card. This can be done physically with a card reader, or handled by softwares to receive online payments.

A merchant acquirer goes and onboard merchants to provide them with the tools and facilities to accept and process card based payments. Then, the acquirer works with an acquirer processor to connect with the payment network, to allow approval / decline of transaction.
## Payment Network
This is also referred to as "card scheme". This network sits in between acquirers and issuers and pass messages back and forth to make the transaction happen. This network also set the communication rules. Both Visa and Mastercard are payment network.
## Example
