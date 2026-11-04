## Учасники

| Учасник | Що робить | Зв'язок з ER (лаба 1) |
|---|---|---|
| Клієнт | натискає «оплатити», бачить повідомлення | Customer |
| OrderService | керує замовленням і його статусом | Order, OrderItem |
| StockService | перевіряє й змінює залишок гітар | Guitar.stockQuantity |
| PaymentService | підтверджує або відхиляє оплату (зовнішня система) | актор з лаби 2 |

## Кроки сценаріїв UC-3

### SC-1 Оплату підтверджено (REQ-1). Фрагмент: loop
1. PaymentService → OrderService: confirmPayment(orderId)
2. OrderService → OrderService: updateStatus(orderId, processing)
3. loop для кожної позиції замовлення:
   OrderService → StockService: decreaseStock(guitarId, quantity), відповідь ok

### SC-2 Оплату відхилено (REQ-2). Фрагмент: alt
1. Клієнт → OrderService: payOrder(orderId)
2. OrderService → PaymentService: requestPayment(orderId)
3. alt оплату підтверджено:
   - PaymentService → OrderService: confirmPayment(orderId)
   - OrderService → OrderService: updateStatus(orderId, processing)
   - loop для кожної позиції замовлення:
     OrderService → StockService: decreaseStock(guitarId, quantity), відповідь ok
   або оплату відхилено:
   - PaymentService → OrderService: declinePayment(orderId)
   - статус лишається pendingPayment
   - OrderService → Клієнт: notifyClient(orderId, paymentDeclined)

### SC-9 Повторна оплата (REQ-12). Фрагмент: alt
1. Клієнт → OrderService: payOrder(orderId) (замовлення в pendingPayment, попередню оплату відхилено)
2. OrderService → PaymentService: requestPayment(orderId)
3. alt оплату підтверджено:
   - PaymentService → OrderService: confirmPayment(orderId)
   - OrderService → OrderService: updateStatus(orderId, processing)
   - loop для кожної позиції замовлення:
     OrderService → StockService: decreaseStock(guitarId, quantity), відповідь ok
   або оплату відхилено:
   - PaymentService → OrderService: declinePayment(orderId)
   - статус лишається pendingPayment
   - OrderService → Клієнт: notifyClient(orderId, paymentDeclined)

### SC-10 Нестача товару на момент оплати (REQ-13). Фрагмент: alt
1. PaymentService → OrderService: confirmPayment(orderId)
2. OrderService → StockService: checkStock(guitarId, quantity)
3. alt товару достатньо:
   - StockService → OrderService: available
   - OrderService → OrderService: updateStatus(orderId, processing)
   - loop для кожної позиції замовлення:
     OrderService → StockService: decreaseStock(guitarId, quantity), відповідь ok
   або товару недостатньо:
   - StockService → OrderService: unavailable
   - статус лишається pendingPayment
   - OrderService → Клієнт: notifyClient(orderId, outOfStock)
