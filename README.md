# Desafio de Arquitetura: Engenharia Reversa e Views no E-commerce

## 1. Diagnóstico do Problema (O Debug Arquitetural)

O problema da View está na falta de filtros. O código original soma o valor de todos os pedidos, sem conferir o status e a data. Com isso, pedidos cancelados (`Cancelled`) ou que ainda estão retidos (`On Hold`) acabam entrando no faturamento, mesmo sem terem sido enviados. A consulta também não limita os resultados ao ano de 2004, como a diretoria solicitou.

Os `JOINs` entre `customers`, `orders` e `orderdetails` estão corretos. O primeiro relaciona cada cliente aos seus pedidos, e o segundo relaciona os pedidos aos detalhes de cada pedido. Portanto, o erro principal não está na ligação das tabelas, mas na ausência do `WHERE`. O agrupamento também foi ajustado para considerar o nome do cliente e o número do pedido.

## 2. A Refatoração (O Conserto da View)

Para corrigir a View, foi mantido o cálculo do valor total de cada pedido e foram adicionados filtros para considerar somente os pedidos com status `Shipped`, realizados no ano de 2004.

