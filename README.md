Desafio de Arquitetura: Engenharia Reversa e Visualizações no E-commerce
1. Diagnóstico do Problema (O Debug Arquitetural)
O problema do View está na falta de filtros. O código original soma o valor de todos os pedidos, sem conferir o status e os dados. Com isso, pedidos cancelados ( Canceled ) ou que ainda estão retidos ( On Hold ) acabam chegando no faturamento, mesmo sem terem sido enviados. A consulta também não limita os resultados ao ano de 2004, como a diretoria solícita.

Os JOINs entre clientes , pedidos e detalhes do pedido estão corretos: o primeiro relacionado a cada cliente aos seus pedidos, e o segundo relacionado aos pedidos aos produtos. Portanto, o erro principal não está na ligação das tabelas, mas na ausência do WHERE . O agrupamento também foi configurado para considerar o nome do cliente e o número do pedido.
