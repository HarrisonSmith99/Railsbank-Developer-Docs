- The Railsbank API is built on the belief that **Compliance** should come first. It should be simple and easy to adhere to regulation, and due to the nature of Railsbank, it is.

- Because Railsbank is essentially middleware between our **Partners** and **Customers**, information in the form of requests - be they POSTs, GETs, or PUTs - passes through the API from both sides all the time.

- We capitalize upon this unique opportunity with **The Compliance Firewall**. There are two parts to it: the **Partner Firewall** and the **Customer Firewall**. Every time a **customer** makes a request, *create ledger*, for instance, the information passes - or attempts to pass - through both.

- To pass through each firewall, the information must adhere to the **Firewall Rules** - the risk policy of the **partner** or **customer** - that are hard-coded into them. If it does not i.e. it breaks at least one rule, then the entity within the request - the enduser, beneficiary, ledger, or transaction - falls into **Quarantine**. There is a **Partner Quarantine** and a **Customer Quarantine**.

- For example, a **customer** is indifferent to endusers living in Iran, and thus does not write a **firewall rule**. However, a **partner** is not particularly keen on having any Iranian residents using its services and hence writes a rule of *quarantine endusers from Iran*. If an Iranian resident then signs up to the **customer's app or business**, their information will pass successfully through the **Customer Firewall** but fall into the **Partner Quarantine**.

- When an entity or transaction falls into **Quarantine** you will be notified via webhook. Then, a compliance officer will manually check the entity or transaction and decide whether to **decline** or allow it to **pass** through - unless it has failed an *autodecline* rule, in which case it will be declined immediately

- This means that an irregular entity will only be created **after** it has been manually reviewed by a compliance officer - protecting both our **Partners** and **Customers**. The creation of all entities - including transactions - can therefore be monitored using **The Compliance Firewall**
