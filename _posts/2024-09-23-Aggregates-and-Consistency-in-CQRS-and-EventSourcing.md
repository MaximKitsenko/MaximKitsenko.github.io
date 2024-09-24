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

## Adding new SkuRegistry aggregate
