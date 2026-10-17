```gherkin
 REQ-1
 Scenario: SC-1 Платіжний сервіс підтверджує оплату
  Given існує замовлення зі статусом pendingPayment
  And в ньому позиція: гітара "Solar", quantity = 2
  And гітара "Solar" має stockQuantity = 5
  When платіжний сервіс підтверджує оплату
  Then статус замовлення стає processing
  And stockQuantity гітари "Stratocaster" стає 3
  
 REQ-2 
 Scenario: SC-3 Платіжний сервіс відхиляє оплату
  Given існує замовлення зі статусом pendingPayment
  When платіжний сервіс відхиляє оплату
  Then Клієнт бачить повідомлення про відхилену оплату
  And замовлення залишається в статусі pendingPayment

 REQ-3 
 Scenario: SC-3 Зміна замовлення в обробці
  Given замовлення має статус processing
  When Клієнт намагається додати до нього гітару "Solar"
  Then система відхиляє спробу
  And Клієнт бачить повідомлення, що замовлення вже в обробці

 REQ-4
 Scenario: SC-4 Менеджер відправляє замовлення
  Given замовлення має статус processing
  When Менеджер змінює статус замовлення на shipped
  Then система передає відправлення службі доставки

 REQ-5
 Scenario: SC-5 Замовлення доставлено
  Given замовлення має статус shipped
  When служба доставки повідомляє про доставку
  Then статус замовлення стає delivered
  
 REQ-6
  Scenario: SC-7 Клієнт додає більше гітар, ніж є на складі
  Given гітара "Solar" має stockQuantity = 1
  And Клієнт створює нове замовлення
  When Клієнт додає 2 штуки "Solar" до замовлення
  Then система відхиляє додавання
  And Клієнт бачить повідомлення про недостатню кількість

REQ-7 
Scenario: SC-7 Клієнт бачить новий статус
  Given замовлення має статус processing
  When Менеджер змінює статус замовлення на shipped
  Then Клієнт бачить статус shipped

REQ-8
Scenario: SC-8 Менеджер змінює залишок
  Given гітара "Solar" має stockQuantity = 5
  When Менеджер змінює stockQuantity на 3
  Then система зберігає значення 3
  And спроба Клієнта додати 4 штуки "Solar" відхиляється
  
  
