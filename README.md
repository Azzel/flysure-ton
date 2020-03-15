# Flysure-ton
### TON Blockchain FlySure smart contract
This smart contract operates as insurance policies processor. It covers flight delay cover.
Someone could buy policy by transfering the grams and flight's details.
The owner of contract should operate as oracle by providing the actual statuses of flights.
If status is normal - the contract simple delete the policy from storage. If status is delayed - the policy holder receives insurance compensation.

Contract's data:
- int seq_num;
- int insurer_public_key - owner public key, to verify sender;
- int price - the nominal premium amount for policy;
- int settlement - the sum assured for policy in case of claim occurs;
- int reserve - the total amount of current contract's balance;
- cell policies - dictionary of policies:
-- policyholder address - as dict key. so contract could manage only one policy for curtain address;
-- policyholder wc - policyholder workchain;
-- policy flight date - the date of the flight, as int;
-- policy flight code - the code of the flight, as int.

## How to operate with Contract
### Initialise the contract
To initialise the contract use init.fif. File helps to create a new flysure contract with <price> amount of policy premium and <settlement> amount for claim payment, with private keys saved to or loaded from <filename-base>.pk (new-flysure.pk by default).
You could test it locally with test-init.fif
For example (if you have correctly set up PATH and FIFTPATH):

```sh
$ fift -s init.fif 0 0.002 0.02
```
To test inisialisation locally with TVM you could use test-init.fif file like this:
```sh
$ fift -s test-init.fif my-flysure
```
### Make new policy
To conclude the policy send the internal message from you wallet to flysure contract adress with gram amount strict equal to <price> and with fligth's details as message.
This message will create new policy on contract's data and uodate
TODO: make a rule for converting flight code into int.
You could test it locally with test-internal.fif:
```sh
$ fift -s test-internal.fif my-flysure-data-0 0.002 kQD4m3hY4LFvPov_6hCIen3ZtvvdqrJkUCziiV5qTFEKXbko 1 2 my-flysure-data-1
```
### Update flight status
To update the flight's statuses and when to settle the policies with delayed flights and remove the normal ones from contracts data the owner should send external message. The message should contain flight data and code for one curtain flight.
If reserve of contract not enough for settlement the policy will stay in data and the owner should resend the message again when new policies will be concluded and the reserve will rise.
To create message use flight-status-message.fif (seqno = 1, date = 1, code = 2 and status = 0)
```sh
$ fift -s flight-status-message.fif my-flysure 1 1 2 0
```
You could test it locally with test-external.fif:
```sh
$ fift -s test-external.fif my-flysure-data-1 my-flysure-query my-flysure-data-2
```
### Eplore the result
Test fif files save the result data in boc file witch could be explored with show-state.fif file providing the correct boc's file name (my-flysure-data-0<.boc>):
```sh
$ fift -s show-state.fif my-flysure-data-0
```
