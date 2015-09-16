# Scala Pet Store API (Work in Progress)

## Notes
This repo is forked from Kato-san's spetstore.

[![Build Status](https://travis-ci.org/j5ik2o/spetstore.png?branch=master)](https://travis-ci.org/j5ik2o/spetstore)

## The goal
Based on DDD, we give an example on implementation (because we use the concepts of DDD design, there can be vairous ways to be implement. In this example, I give the best implementation in my opinion)

## Characteristics
- Design principles based on DDD
- Targeted domain is a Pet store
- Support Scala 2.11
- Support Play 2.3.x + Google Guice
- The application is REST like API

## Targeted Domain
- Pet Store
- We base mainly on [Java Pet Store](http://www.oracle.com/technetwork/java/petstore1-3-1-02-139690.html) but there are some parts are re-arranged.
- We refers to the following implemtation [mybatis/jpetstore-6](https://github.com/mybatis/jpetstore-6)

## Layer Architecture
We follow the layered architecture in DDD and divided them into

- Application Layer
- Domain Layer
- Infrastructure Layer

The more important thing is, the domain layer that reflexed mental model.
We devide domain layer from non-domain layers.

## Domain Layer

The purpose of a domain is to manage a pet store. We import pet, pet food, accessories in to our inventory and sell it to our customer. This is our domain (We have not yet implemented the inventory concept)

### Domain Model

- [Customer Module](https://github.com/j5ik2o/spetstore/tree/master/app/com/github/j5ik2o/spetstore/domain/model/customer) = Customer Modul
    - [Customer](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/customer/Customer.scala) (GE,A) = Pet Store's customers
        - [StatusType](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/basic/StatusType.scala) (VO)
        - [CustomerProfile](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/customer/CustomerProfile.scala) (VO)
        - [CustomerConfig](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/customer/CustomerConfig.scala) (VO)
- [Item Module](https://github.com/j5ik2o/spetstore/tree/master/app/com/github/j5ik2o/spetstore/domain/model/item) = Product Module
    - [Category](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/item/Category.scala) (GE,A) = Product Category (ex. Dog)
    - [ItemType](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/item/ItemType.scala) (GE,A) = Product Type (example: Chó Nhật)
    - [Item](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/item/Item.scala) (GE,A) = Product (Ex. Bochi)
- [Purchase Module](https://github.com/j5ik2o/spetstore/tree/master/app/com/github/j5ik2o/spetstore/domain/model/purchase) = Buying Module
    - [Cart](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/purchase/Cart.scala) (GE,A) = Shopping Cart
        - [CartItem](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/purchase/CartItem.scala) (VO) = Product and Quanlity inside a cart
    - [Order](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/purchase/Order.scala) (GE,A) = Order
        - [OrderItem](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/model/purchase/OrderItem.scala) (VO) = Ordered product  and quality

GE = Global Entity
VO = Value Object
A  = Aggregate

### Specs
Firstly, let's write the Spec of important parts

- [CartSpec](https://github.com/j5ik2o/spetstore/blob/master/test/com/github/j5ik2o/spetstore/domain/model/purchase/CartSpec.scala)
- [OrderSpec](https://github.com/j5ik2o/spetstore/blob/master/test/com/github/j5ik2o/spetstore/domain/model/purchase/OrderSpec.scala)

The important thing here is, in the presentation of the model (model name, attribute name, behaviours' name(parameter, return value) we don't we words that does not appear in Ubiquitous Language.
By principle, in these element, there is not terms that used in technical implementation(実装技術の言葉を含めてしまうとメンタルモデルが離れていきドメインについて理解することが難しくなるため。ただし、StringやIntなどのデータ型や、ListやMap, Try, Option, Futureなどのコンテナ型は例外とする). Knowledged related to technical implementation si supported under application layer or infrastructure layer.
- TODO
    - Implement Inventory
    - Inventory
    - Implement product model (sub-model)

## Infrastructure Layer
### DDD base code
For the sake of simplicity, we don't prepare any special library but only simple base code.
- [Entity](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/infrastructure/support/Entity.scala) = Entity's responsibilities under DDD
- [Repository](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/infrastructure/support/Repository.scala) = Repository's Responsibilities under DDD
    - [RepositoryOnJDBC](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/infrastructure/support/RepositoryOnJDBC.scala)  
    Repository's skeleton's implementation with JDBC. Implement with [ScalikeJDBC](http://scalikejdbc.org/). By the way, 1エンティティが複数テーブルにマッピングされるような実装は今のところ作っていません。そのうち作ります。Specは[こちら](https://github.com/j5ik2o/spetstore/blob/master/test/com/github/j5ik2o/spetstore/domain/infrastructure/support/RepositoryOnJDBCSpec.scala)
    - [RepositoryOnMemory](https://github.com/j5ik2o/spetstore/blob/master/app/com/github/j5ik2o/spetstore/domain/infrastructure/support/RepositoryOnMemory.scala)
    メモリに対応したリポジトリの骨格実装。内部実装はMapですがRepositoryとして操作できる。Specは[こちら](https://github.com/j5ik2o/spetstore/blob/master/test/com/github/j5ik2o/spetstore/domain/infrastructure/support/RepositoryOnMemorySpec.scala)
    - RepositoryOnMemcached (TODO)  
    Repository's skeleton implemtation using Memcached
    - CacheManagementRepository(TODO)  
    Repository's implemtation for 

## Application Layer
- Controller (TODO)
- Application Service (TODO)


## Main Frameworks/Libraries We Use

- Play Framework 2.3.x
- Google Guice
- ScalikeJDBC 2.x
- json4s
- nscala-time
- specs2
- mockito

## Reference code
- https://github.com/mybatis/jpetstore-6
- https://github.com/scalikejdbc/hello-scalikejdbc
- https://github.com/tototoshi/play-json4s
- https://github.com/tototoshi/play-flyway
- http://qiita.com/opengl-8080/items/6fb69cd2493e149cac3a

