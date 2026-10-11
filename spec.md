# Сутності

## Customer

- id: int (PK)
- email: string
- phoneNumber: string
- fullName: string
## Brand

- id: int (PK)
- name: string
- country: string
## Guitar

- id: int (PK)
- model: string
- stockQuantity: int
- currentPrice: decimal
- brandId: int (FK)
## OrderItem

- id: int (PK)
- quantity: int
- unitPrice: decimal
- orderId: int (FK)
- guitarId: int (FK)
## Order

- id: int (PK)
- orderDate: date
- customerId: int (FK)
- status: string
статуси: pendingPayment, processing, shipped, delivered
pendingPayment - очікує оплати, processing - обробляється, shipped - відправлено, delivered - доставлено
# Зв'язки

## Customer-Order: 0:N
Клієнт має 0 або багато замовлень, замовлення належить рівно одному клієнту
## Brand-Guitar: 0:N

Бренд має 0 або багато гітар, гітара належить рівно одному бренду
## Order-OrderItem: 1:N

Замовлення має 1 або багато позицій, позиція належить рівно одному замовленню
## Guitar-OrderItem: 0:N

Гітара може бути в 0 або багатьох позиціях, позиція стосується рівно однієї гітари
# Критерії прийняття

Усі id мають тип int і є первинними ключами.

Усі зовнішні ключі (brandId, customerId, orderId, guitarId) мають тип int, збігаються з типом ключа, на який вказують.

Ціни (currentPrice, unitPrice) мають тип decimal.
