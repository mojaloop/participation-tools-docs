# DFSP Liquidity Design
## Introduction
All transactions that move through a Mojaloop payment gateway are prefunded. This removes systemic risk and allows the settlement of cleared funds between financial institutions to be fast and reliable. As only prefunded transactions are permitted, prefunding is also a mechanism for DFSP to control their exposure. Designing for liquidity for a DFSP must therefore cover both the mechanisms and procedures for managing the liquidity but also the controlling of exposure.

There are two types of liquidity required to connect a DFSP to a real-time payment gateway. Mojaloop requires an amount of pre-funded liquidity in order to send payments across the payment gateway, and payments that are received through the payment gateway require local liquidity in order to make funds immediately available to the customer. This document aims to describe the typical liquidity flows in a Mojaloop scheme, and attempts to provoke early planning for this liquidity by presenting various design approaches. 

The best solution depends largely on the mechanisms available in the Financial Institution’s core banking system, the requirements that a DFSP has for managing exposure, the use case supported, and the net flows anticipated. The integration design must implement the mechanism chosen so it is important to complete this design early on in an integration.

## What is in this toolkit?
This toolkit begins with an introduction to the steps involved in a mojaloop clearing system and the obligations of a connected Financial Institution. It then discusses a choice between using an internal ledger for managing the payment gateway funding or by using the core banking systems deposit and withdraw system. This second part may or may not be relevant to your organisation, but has been included to provoke a thorough design and evaluation of the procedures that are required to be performed for each step before selecting a mechanism to implement.
### Definition of steps Involved
#### Prefunding
All obligations inside the Mojaloop Scheme must be pre-funded. That means that before you can participate in a transaction as a payer DFSP, you will need to have pre-funded liquidity available to fund the transfer. Mojaloop will not allow the transfer to proceed unless there is sufficient pre-funded capital. This prefunding is shared across all obligations and institutions that are connected through Mojaloop, so is significantly more efficient than bilateral arrangements. Additionally, receiving funds as a  Payee DFSP does not require pre-funding and reduces the DFSP's obligation in the scheme. A DFSP is expected to be both a payer and payee. Mojaloop gives DFSPs liquidity credit for completed transfers where they are the credit party, and this means that liquidity cover, instead of being required for all transfers where the DFSP is the debtor, only covers the net of debits versus credits. Thus the net position of funds received less funds sent is available to make payments.
|Note: |
|:- | 
Liquidity needs to be in the form which is explicitly recognised by the scheme to which the DFSP belongs. Each scheme will have rules for this and they are likely to be different. This design document does not describe the specific mechanism as these rules are better described in the rules of the scheme.|

#### Real-time Clearing
The clearing of a transfer occurs during the transfer. The timing of this coincides with the hub’s receipt of the ILP fulfilment. The Mojaloop position ledgers are updated with the movement of funds, which means that the Payer DFSP has less pre-funded liquidity to spend and that the Payee DFSP has more pre-funded liquidity. What is recorded in these ledgers is an obligation from the debtor DFSP to the creditor DFSP, and it will be the case that a condition of a DFSP joining a Mojaloop scheme is that the DFSP accepts the scheme’s attribution of obligations.
#### Deferred-Net Settlement
Net Settlement is the process by which Mojaloop initiates an external process that updates the ownership of the prefunded liquidity in line with the mojaloop position ledgers. The position ledger is then reset, and the settlement ledgers are updated to reflect the new pre-funded amounts.
#### Capital Flows
If there is a net movement of funds from a DFSP either as a net payee (creditor) or net payer (debtor), then there will need to be a movement of funds to compensate for this in order to maintain liquidity.

## Mojaloop FX Clearing Rails Explained (Deferred net)
Mojaloop supports both deferred net settlement, and continuous gross settlement; this chapter only describes the deferred net settlement for a cross-currency transaction. A mojaloop FX transaction is composed of two transfers that are linked. The first is an FX transfer that provides liquidity in the target currency, and the second transfer is between the end participant organisations I.e. Payer and Payee DFSPs. The transfers are tied together in a way that means they either both succeed or both fail. The FX transfer is initiated by the Payer DFSP organisation, and is an agreement between the Payer DFSP and the chosen Foreign Exchange Provider (FXP). The movement of funds is in source currency from the Payer DFSP to the FXP, and in target currency from the FXP to the Payee DFSP.

Next this chapter goes through each of the phases required to operate separately, I.e. the prefunding, the clearing, the settlement, and then the capital flows, before showing them all together.

<div style="page-break-after: always"></div>

### Prefunding
Here the Payer DFSP must provide prefunded liquidity into the Mojaloop Scheme to be held as collateral. This is done through a funds-in process inside mojaloop, and the Payer DFSP settlement account is updated to reflect the amount. The Payee DFSP must provide liquidity in their float account, or have that liquidity available to be deposited into a payee’s account. Similarly the Foreign exchange provider needs to have prefunded liquidity in the target currency. This is done through a funds-in process in mojaloop, and their target currency settlement account is updated to reflect the total. There is now sufficient liquidity to support a cross-currency transaction.

![T-Accounts for Transfer prefunding](./images/SI_Toolkit-T-Accounts%20for%20FX%20Transfer%20-%20prefunding.png)

<div style="page-break-after: always"></div>

### Clearing
The clearing process occurs when the transfer is made. The timing of the transfers are critical, but is not covered in this description, as this description is concentrating on the flow of funds. 
The Payer DFSP moves funds out of the Payers account as either a withdrawal, or a transfer into the float account. Funds are moved in source currency from the Payer DFSP’s position account to the FXP’s position account. Funds are moved in target currency from the FXP’s target position account to the Payee DFSP position account. The Payee DFSP moves cleared funds into the Payee’s account either by transferring from the float account, or by depositing the funds using their working capital.

![T-Accounts for Transfer clearing](./images/SI_Toolkit-T-Accounts%20for%20FX%20Transfer%20-%20Clearing.png)

|Float Account: |
|:- |
|The float account in this diagram at each of the DFSPs, is a representative of the accounting mechanism that is used to manage the liquidity leaving and returning the DFSPs core banking system.|

<div style="page-break-after: always"></div>

### Settlement
The settlement process is when mojaloop interacts with the external liquidity mechanisms of the scheme to change ownership of the pre-funded liquidity to be in line with the transaction that have been processed. While it is doing this, it updates the settlement accounts to reflect the new pre-funded liquidity ownership by moving funds between each participant's settlement account and their position accounts. The diagram represents Payer DFSP has funds moving from their settlement account into their position account. This is a simplified view, as the real fund flows occur in the externally linked accounts, and the settlement and position ledgers are updated to reflect the new position and prefunded liquidity available. In a similar simplified view, the FXP has funds moved from their position account into their settlement account in source currency, and from the settlement account into their position account in target currency. Similarly the Payee DFSP has funds moved from their position account into their settlement account. 

![T-Accounts for Transfer settlement](./images/SI_Toolkit-T-Accounts%20for%20FX%20Transfer%20-%20Settlement.png)

<div style="page-break-after: always"></div>

### Capital Flows
The capital flow is important to compensate for a net movement of funds from any particular organisation connected to the scheme. A net Payer DFSP would need to replenish their prefunded liquidity by either moving funds from their float account, or by moving working capital into mojaloop using the funds-in process. A net Payee DFSP would use mojaloop’s funds-out process to move pre-funded liquidity into their float account, or working capital for the organisation. If there is a net movement between currencies, then the FXP would need to withdraw pre-funded capital from the net receiver of currency using funds-out, and deposit funds into the net payer pre-funded currency account using funds-in. The timing of these flows are left to the discretion of each organisation.

![T-Accounts for Transfer Capital Flows](./images/SI_Toolkit-T-Accounts%20for%20FX%20Transfer%20-%20capitalFlows.png)

<div style="page-break-after: always"></div>

### Summary
This diagram illustrates all the flows together.

![T-Accounts for Transfer All](./images/SI_Toolkit-T-Accounts%20for%20FX%20Transfer%20-%20All.png)

<div style="page-break-after: always"></div>

## Managing Liquidity Outflows
### Flows as a Payer DFSP and net debtor
As a Payer DFSP in a transfer, the DFSP will be required to provide pre-funded liquidity. This is illustrated using the green arrows. During Clearing of a transfer, the funds are moved from the payer’s account into the float account dedicated for connecting to this payment gateway. The Payer DFSP position account inside Mojaloop is debited. During settlement, the funds are moved from the Payer DFSP’s pre-funded settlement account to balance the debit on the Payer DFSPs position account. If there is a new outflow of funds from a DFSP, that will need to maintain liquidity by moving funds from their float account into Payer DFSP’s pre-funded liquidity account in the scheme.

![T-Accounts for Transfer All](./images/SI_Toolkit-Payer%20DFSP.png)

|Float / Mirror Account: |
|:- |
| The float account in this diagram at each of the DFSPs, is a representative of the accounting mechanism that is used to manage the liquidity leaving and returning the DFSPs core banking system. The core banking system of the DFSP may have a built-in mechanism for this e.g. a Deposit or Withdraw function. Alternatively the functionality can be layered on by making use of a dedicated ledger/s/account to keep track of incoming and outgoing funds. |

<div style="page-break-after: always"></div>

## Managing Liquidity Inflows
### Flows as a Payee DFSP and net creditor
As a Payee DFSP in a transfer, the DFSP has to have working capital prefunded into their dedicated internal float account. During Clearing of a transfer, the funds are moved from the float account into the payee’s account. Funds must be available to be spent immediately by the Payee. The Payee DFSP’s position account is credited. During settlement, the funds are moved from the Payee DFSP position account into their pre-funded settlement account to balance the credit on the Payee DFSPs position account. To maintain working capital, the float account must be deposited with withdrawn funds from the Payee DFSP’s pre-funded liquidity account.

![T-Accounts for Transfer All](./images/SI_Toolkit-Payee%20DFSP.png)


|Float / Mirror Account: |
|:- |
| The float account in this diagram at each of the DFSPs, is a representative of the accounting mechanism that is used to manage the liquidity leaving and returning the DFSPs core banking system. The core banking system of the DFSP may have a built-in mechanism for this e.g. a Deposit or Withdraw function. Alternatively the functionality can be layered on by making use of a dedicated ledger/s/account to keep track of incoming and outgoing funds. |
