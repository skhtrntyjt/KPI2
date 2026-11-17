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

## Уточнення і свідомі обмеження

1. paymentTimeout. У sequence-діаграмі лаби 3 гілки таймауту немає
   (там лише paymentDeclined і outOfStock). У лабі 4 додано
   notifyClient(orderId, paymentTimeout), бо процес має показати реакцію
   на відсутність відповіді від PaymentService. Це уточнення до лаби 3.

2. Запізніла відповідь. Якщо PaymentService відповість після 10 секунд,
   коли процес уже завершився гілкою таймауту, така відповідь у цій
   версії не обробляється. Це свідомий ризик: можливе списання коштів
   при повідомленні Клієнту про невдачу.

3. notifyClient без очікування відповіді. Повідомлення notifyClient
   надсилається Клієнту, процес не чекає відповіді й іде далі
   (або завершується). У діаграмі вузли notifyClient підписано
   «без очікування відповіді».
