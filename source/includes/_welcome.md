# Welcome
## What We Do

Railsbank is an open Banking and Compliance platform that enables **Customers** to create their own products that need banking capabilities or integrate banking capabilities into their systems, apps and workflows.

**Customers** connect to the platform via the **Customer API** and access financial products through what we call **Product Rails** - a full list of the cabilities available via the Railsbank **Product Rails** can be seen below or on our [main website](https://www.railsbank.com/).

The platform is purely an API, built to be easily and rapidly integrated into your business. Railsbank is all about making your life super simple to integrate a complete set of financial services capabilities into your business, app, or product.

These guides are designed to help anyone and everyone - from CEOs to developers - understand how easy it is to operate the API. However, if you want a more detailed, development-focused documents after going through them, head to the [API Docs](https://docs.railsbank.com/).

## Environments

Your **environments** allow you to go from risk-free testing to complete global banking with only a few tiny changes in your code.

There are three environments within Railsbank: **Play,** **PlayLive,** and **Live**.

### **Play**
-
- The Base URL for Play is: `https://play.railsbank.com`
- Our preliminary Sandbox, through which you can test the API with complete freedom.
- **Is not** connected to the ouside world; it is a simulation. Therefore, it **costs nothing**, and there is no limit to the number of calls, transactions, or volumes.  
-  Can be accessed within minutes.
- Partners, money, IBANs, account numbers, and sort codes are simulated, however they work in the same way as **PlayLive** and **Live**, with the same endpoints and fields.
- Allows your developers to integrate into the API with ease.


### **PlayLive**
-
- The Base URL for PlayLive is: `https://playlive.railsbank.com`
- Our second Sandbox, through which you can test the API with real money.
- **Is** connected to the outside world: real money, real bank accounts, real IBANs, real partners.
- Access to capabilities that cannot be simulated, namely ID&V and screening.
- Can be access within minutes.
- **Costs nothing**, but usage is limited.
- Limits:
  - 3 endusers
  - 3 ledgers
  - Maximum value of a transaction: £5 or €5
  - Maximum value of aggregated transactions: £20 or €20.

### **Live**
-
- The Base URL for Live is: `https://live.railsbank.com`
- Complete access to global banking.
- Connected to all of the Railsbank capabilities; access is dependent upon your needs and commercial agreement.
- Requires a 2 week onboarding process with Railsbank.
- Limits are based upon **Partner** risk policies.
