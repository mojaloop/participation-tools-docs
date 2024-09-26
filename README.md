# Mojaloop Participation Tools

The participation tools is an open sourced collection of artifacts and software solution to support the organizational participation in a Mojaloop switch.
This includes:
 - financial service providers who provide account related services to customers (DFSPs). 
 - financial technology providers who initiate payments and payment requests (PISPs).
 - foreign exchange providers who sell currency to support payments.

These Participation tools are open sourced to support collaboration between Mojaloop scheme implementations, with the aim of building best practice integrations. If any of these artifacts need improving or customization, in the spirit of open source, make these adjustments in a general way and contribute changes back. 

## Slack Channel
The following Mojaloop slack channel exists for supporting Mojaloop participation tools: **#ws-participation-tools**

## Mojaloop Integration Toolkit (ITK)
The Mojaloop integration toolkit or ITK is the collective noun for all mojaloop participantion tools.

Here is the list of participation tool artifacts:

1. [**Mojaloop Connector**](./mojaloop-connector/MojaloopConnector.md) the main participation tool component. This 
   - abstracts the Mojaloop FSPIOP api supporting easy upgrades to newer API versions
   - provides a synchronous API experience simplifying integrations
   - manages the Mojaloop security
   - provides optional support for large scale bulk transfers to support the G2P use case.
1. [**Payment Manager**](./payment-manager/PaymentManager.md) the component that support the running/deployment and configuring of the Mojaloop Connector
   - automatically on-boards the Mojaloop Security using best practice
   - provides docker and helm deployments of Mojaloop connector and other supportive microservices
   - provides configurable best practice RBAC security
   - provides the ability to trace and inspect a transfer through a UI
   - provides technical operational metrics to support management of the connection
1. [**DFSP liquidity Design Guide**](./liquidity-design/LiquidityDesign.md) this is a guide to support understanding and business operational design for a DFSP connecting to Mojaloop. This guide includes a:
   - description of the Mojaloop process that control the flow of funds
   - description of how this requirement translates into process for managing DFSP flow of funds as both a net debtor, or a net creditor.
1. [**IIPS Design Patterns**](./integration-design-patterns/IIPSDesignPatterns.md) this resource help guide the integration designs. These design have detailed sequence diagrams illustrating API calls, and responsibilities carried out by the integration. Various generic scenarios are shown with the consequence of adapting an approach / pattern is discussed. At a high level the patterns include: 
   - patterns for building integrations as a Payer DFSP
   - patterns for building integrations as a Payee DFSP
1. [**Core-Connector Testing Harness**](./core-connector/CoreConnectorTestingHarness.md) this is a repository that can be downloaded to deploy and run the core-connector testing harness. It includes
   - A docker-compose script for deploying the testing harness
   - pre-configured TTK. This solution makes use of the Mojaloop testing tool kit
   - Golden-Path core-connector test collection used to test core connectors.
1. [**Core-Connector Template**](./core-connector/CoreConnectorTemplate.md) this is a template repository for building core-connectors. It includes
   - a typescript code repository with pre-configured scripts and CI controls to support quick development that align with Mojaloop engineering best practices.
1. [**Core-Connector Development Guide**](./core-connector/CoreConnectorBuildingGuide.md) this provides a core-connector testing harness that can be used to locally test integrations as they are being built, and then to verify that all unhappy path requirements have been catered for.
   - a docker compose repository and Golden Path Core-Connector test collection.




