---
title: "No substitutions for Liskov Substitution Principle (LSP)"
date: 2019-03-15T10:00:00-04:00
draft: false
tags: ["Clean-Architecture", "Clean-Code", "Liskov-Substitution-Principle", "LSP", "Kotlin"]
---

In this post, we’ll explore the relationship between two concepts, ‘high level policy’ and ‘low level details’ and how the Liskov Substitution Principle bridges the gap. High level policy is a function or a set of functions that describes what needs to be done in a certain program path, whereas low level details are implementations of the dependencies that enable the high level policy. Let’s take a look at an example.

Here’s a high level policy for ordering a product from an online store:

- Check inventory if product is in stock
- If in stock
  - Charge customer
  - Send order to shipping system
  - Update inventory as item is reserved for customer
- If not in stock
  - Deny order placement for time being

This high level description of the steps that need to be done to place an order can be expressed in the following code

{{<highlight kotlin>}}
class OrderProductUseCase(private val inventoryService: InventoryService,
                          private val shippingService: ShippingService,
                          private val paymentService: PaymentService) {
    fun placeOrder(customer: Customer, order: ProductOrder) {
        val isInStock = inventoryService.isInStock(order.product.id)
        if(isInStock) {
            inventoryService.reserveProduct(order.product)
            paymentService.charge(customer, order)
            shippingService.ship(order)
        } else {
            throw OutOfStockException()
        }
    }
}
{{</highlight>}}

As you scan through the code, you might notice that there are no specific details about what is entailed in placing an order, rather what is described is how an order is placed. The how is the ‘low level details’ part. The details are hidden behind contracts, `InventoryService`, `ShippingService`, and `PaymentService` in our case.

As an example, the contract for the inventory service as dictated by the high level policy above is the following:

{{<highlight kotlin>}}
interface InventoryService {
    fun isInStock(productId: Long): Boolean

    fun reserveProduct(product: Product)
}
{{</highlight>}}

What would be an implementation of a contract? Here’s a really simple one for the inventory service:

{{<highlight kotlin>}}
class CompanyDataWarehouseInventoryService: InventoryService {
    override fun isInStock(productId: Long): Boolean {
        return true
    }

    override fun reserveProduct(product: Product) {
        println("Reserved product!")
    }
}
{{</highlight>}}

The high level policy above is only interested in the contract. It does not care about details.

If the high level policy does not care about the details (implementations), then what does? To connect the policy to the details, that is where the application is built up from a set of contract implementations. An example of driver code might be:

{{<highlight kotlin>}}
fun main() {
    val paymentService: PaymentService = CompanyPaymentService()
    val shippingService: ShippingService = CompanyShippingService()
    val inventoryService: InventoryService = CompanyDataWarehouseInventoryService()
    val orderProductUseCase: OrderProductUseCase = OrderProductUseCase(inventoryService, shippingService, paymentService)

    val customer = Customer()
    val order = ProductOrder(1L)
    orderProductUseCase.placeOrder(customer, order)
}
{{</highlight>}}

This is a very simplified implementation that illustrates the build up of the system from scratch. Most commonly you would use a dependency injection container such as Spring or Dagger to maintain object instances in the system.

What was described above is a perfect mold for talking about the Liskov Substitution Principle (LSP).

In effect, LSP states that when you implement an interface (e.g. `CompanyDataWarehouseInventoryService` implements `InventoryService` contract), the instance of the subclass object must be usable through the interface without the user knowing the difference. What we can see is that in our driver code above (i.e. the main function), we can inject any implementation of `InventoryService` and the high level policy (i.e. the ‘user’ aka `OrderProductUseCase`) will not care since it is only concerned with the contract, not the details.

Liskov Substitution Principle is a foundational principle in Object Oriented design and is the L in SOLID, the now famous acronym of a set of engineering principles. When framed against a backdrop of ‘high level policy’ and ‘low level details’, LSP stands out as a fundamental tenet to clean system design using contracts.
