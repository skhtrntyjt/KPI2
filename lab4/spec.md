# Процес: оплата замовлення (деталізує UC-3)

Форма: activity-діаграма. Обрано, бо процес це послідовність дій
з розвилками й паралельним кроком, а не життєвий цикл одного об'єкта.

## Кроки
1. Початок: замовлення у статусі pendingPayment.
2. Клієнт → система: payOrder(orderId).
3. Система → PaymentService: requestPayment(orderId) (повідомлення).
4. Очікування відповіді до 10 секунд. Розгалуження:
   - confirmPayment(orderId): крок 5;
   - declinePayment(orderId): notifyClient(orderId, paymentDeclined),
     статус лишається pendingPayment, кінець;
   - відповіді немає за 10 с: notifyClient(orderId, paymentTimeout),
     статус лишається pendingPayment, кінець.
5. Система → StockService: checkStock(guitarId, quantity).
   Розгалуження: товару недостатньо: notifyClient(orderId, outOfStock),
   статус лишається pendingPayment, кінець.
6. updateStatus(orderId, processing).
7. Паралельно (fork):
   - decreaseStock(guitarId, quantity) для кожної позиції;
   - notifyClient(orderId, processing).
8. Join, кінець.

## Повідомлення
| Повідомлення | Хто надсилає | Хто приймає | Без відповіді |
|---|---|---|---|
| requestPayment | система | PaymentService | таймаут 10 с, pendingPayment, сповіщення Клієнту |
| notifyClient | система | Клієнт | процес не чекає відповіді, іде далі |
