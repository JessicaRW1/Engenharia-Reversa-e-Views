DESAFIO DE ARQUITETURA: ENGENHARIA REVERSA E VIEWS NO E-COMMERCE
Disciplina: Banco de Dados 
Atividade: O Furo de Caixa no ClassicModels
1. Diagnóstico do problema
O erro da Visualização original é falta de filtros. Ela junta as tabelas clientes , pedidos e detalhes do pedido e soma o valor dos produtos de todos os pedidos, sem verificar se eles foram realmente enviados. Assim, pedidos com status Cancelado e Em Espera também entram no relatório financeiro, embora não atendam aos critérios definidos pela diretoria.

Outro problema é que a consulta não limita os pedidos ao ano de 2004. Dessa forma, ela pode trazer valores de outros anos. Os relacionamentos feitos com JOIN estão corretos, pois ligam o cliente ao pedido e o pedido aos seus itens. A falha principal não está na ligação das tabelas, mas na ausência da cláusula WHERE com as condições permitidas.

Também foi configurado o GROUP BY , colocando o nome do cliente junto ao número do pedido. Isso deixa mais claro como os valores são agrupados e evita problemas com configurações de agrupamento mais restritivas do MySQL.
