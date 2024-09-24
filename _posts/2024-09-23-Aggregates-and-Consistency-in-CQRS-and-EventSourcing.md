---
layout: post
title: Aggregates and Consistency in CQRS and Event Sourcing
---

Often when we work with data we should think about consistency. A lot of articles, especcially in relational databases world say - 'data should be consistent', from the other hand a lot of modern approaches say - `eventual consistency is good trade off'. Sometimes it's not clear how to find good balance, especially when you come from relational database world, where you think in terms of Transaction. In this article i will describe some the questions (and answers) about consistency we faced in the design process of application within CQRS + Event Sourcing approach.

Lets imagine we develop e-commerce application with `Product` Aggregate. Each product has unique SKU. Every time we want to create product we send command `CreateProduct(sku)`, every time we want to rename product we send command `ChangeProductSku(id,newSku)`.
When aggregate receive each of these commands, it makes some validations and produces the corresponding events: `ProductCreated(Sku)` or `ProductSkuUpdated(Id,Sku)`.

Which validations should be made during this process? Skipping validations which are not important for our discussion, we can say that we need to check `sku` for uniqiness. So, aggregate should make sure that there is no other product with a such `sku` before reising `ProductSkuUpdated(Id,Sku)` event.

Example:
there are `ProductA` and `ProductB`
renaming `ProductA` to `ProductB` should fail, since product with a such `sku` already exists.

In Rdb world we would just make something like `select count from products where sku = ProductB` and if query returns > 0 we can stop preocessing and reject command.

The problem is in fact that we have no such list. From here we start our jurney of finding ways to acheive that.

## Using Projection

To get the list of existing Skus we can add a `projection`, which will accumulate all such events and prepare `View`. This view will be just list of `Sku`. Aggregate will use this `View` to answer the question does sku already exists or not. 

But in `EventSourcing` world `View` can fall behind of aggregate. 

*there are product0 and product1 // view is [product0, product1]*

*user sends command rename product0 to productA // view is still [product0, product1]*

*aggregate makes validation and rise event product0RenamedToProductA // view is still [product0, product1]*

*user sends command rename product1 to productA // view is still [product0, product1]*

*aggregate makes validation and rise event productSkuChanged // view is still [product0, product1]*

*Projection receives event product0RenamedToProductA // view is now [productA, product1]*

*Projection receives event product1RenamedToProductA // view is now [productA, productA] or [productA]*

So looks like this approach doesn't work. And using View from Aggregate was bad idea.

## Adding new `SkuRegistry` aggregate

Another approach is to add new aggregate - `SkuRegistry` which will keep list of all skus which are in usage. Each time User wants to rename product, it sends command `CreateProductIfSkuIsFree`, when  `SkuRegistryAggregate` receives this command it checks (we know that everything within aggregate is consistent), does this `productA` sku exists in aggregates list of sku, if it exists, processing stopped, if not - new event rised `SkuBooked(ProductA)` as a reaction on this event `ChangeProductSku(ProductA)Command` can be sent.

 But this approach looks like overcomplication, it adds new aggregate, new commands and events. More over, what if Application will be shut down after `SkuBookedand` before `ChangeProductSku` ? We also need some aditionl mechanism to solve such cases.

## Using Projection with eventual consistency.

Previous approches issued more questions then answers, these quesitons are:

*-Can we use Read models from Write models, in other words, can we use Views made by projections from Aggregates?*
*-Should we keep consistency between aggregates?*
*-Can we brake these rules?*

1) **Can we use Read models from Write models, in other words, can we use Views made by projections from Aggregates?** ( Yes )

Explanation:

> Consider the scenario where an Invoice Aggregate needs some
Customer information (for example, name, billing address, and tax ID) in
order to calculate and prepare a proper Invoice. We can capture this information in an easy-to-consume form via CustomerBillingProjection,
which will create and maintain an exclusive instance of CustomerBillingView. This Read Model is available to the Invoice Aggregate through the
Domain Service named IProvideCustomerBillingInformation. (Implementing Domain-Driven Design, Vaughn Vernon, page 573)

2) **Should we keep consistency between aggregates?** (No)

Explanation:

> Trying to keep multiple
Aggregate instances consistent may be telling you that your team has missed
an invariant. You may end up folding the multiple Aggregates into one new
concept with a new name in order to address the newly recognized business
rule. *(Implementing Domain-Driven Design, Vaughn Vernon, page 359)*

> Accepting that all Aggregate instances in a large-scale, high-traffic enterprise are never completely consistent helps us accept that eventual consistency also makes sense in the smaller scale where just a few instances are
involved.*(Implementing Domain-Driven Design, Vaughn Vernon, page 364)*

> By definition, we don’t require two aggregates to be immediately
consistent, so if we fail to process an event and update only a
single aggregate, our system can still be made eventually
consistent. *(Architecture Patterns with Python Enabling Test Driven Development)*

3) **Can we brake these rules** (Yes)

> An experienced DDD practitioner may at times decide to persist changes to
multiple Aggregate instances in a single transaction, but only with good reason *(Implementing Domain-Driven Design, Vaughn Vernon, page 367)*


According to these rules the following solutions can be suggested:
 - if we need to control uniqueness of SKU, it looks like we should add another aggregate, something like 'SkusRegistry'. Which will keep a list of SKU.
 - Use eventual consistency in the following way - allow multiple product aggregates to have the same SKU, detect this situation somehow ( e.g. with help of projections), fix duplicates 