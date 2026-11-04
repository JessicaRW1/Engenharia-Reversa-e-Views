# Desafio de Arquitetura: Engenharia Reversa e Views no E-commerce

## 1. Diagnóstico do Problema (O Debug Arquitetural)

O problema da View está na falta de filtros. O código original soma o valor de todos os pedidos, sem conferir o status e a data. Com isso, pedidos cancelados (`Cancelled`) ou que ainda estão retidos (`On Hold`) acabam entrando no faturamento, mesmo sem terem sido enviados. A consulta também não limita os resultados ao ano de 2004, como a diretoria solicitou.

Os `JOINs` entre `customers`, `orders` e `orderdetails` estão corretos. O primeiro relaciona cada cliente aos seus pedidos, e o segundo relaciona os pedidos aos detalhes de cada pedido. Portanto, o erro principal não está na ligação das tabelas, mas na ausência do `WHERE`. O agrupamento também foi ajustado para considerar o nome do cliente e o número do pedido.

## 2. A Refatoração (O Conserto da View)

Para corrigir a View, foi mantido o cálculo do valor total de cada pedido e foram adicionados filtros para considerar somente os pedidos com status `Shipped`, realizados no ano de 2004.

```sql
USE classicmodels;

CREATE OR REPLACE VIEW view_relatorio_financeiro AS
SELECT
    c.customerName,
    o.orderNumber,
    SUM(od.quantityOrdered * od.priceEach) AS valor_total
FROM customers c
JOIN orders o ON c.customerNumber = o.customerNumber
JOIN orderdetails od ON o.orderNumber = od.orderNumber
WHERE o.status = 'Shipped'
  AND o.orderDate >= '2004-01-01'
  AND o.orderDate < '2005-01-01'
GROUP BY c.customerName, o.orderNumber;
```
Para visualizar os pedidos presentes na View, utilizamos a seguinte consulta:

```sql
SELECT *
FROM view_relatorio_financeiro
ORDER BY orderNumber;
```

Para verificar se algum pedido cancelado, retido ou fora do ano de 2004 entrou no relatório, utilizamos esta consulta:

```sql
SELECT
    v.orderNumber,
    o.status,
    o.orderDate
FROM view_relatorio_financeiro v
JOIN orders o ON v.orderNumber = o.orderNumber
WHERE o.status <> 'Shipped'
   OR o.orderDate < '2004-01-01'
   OR o.orderDate >= '2005-01-01';
```

**Resultado esperado:** a última consulta deve retornar zero registros, indicando que nenhum pedido fora dos critérios definidos está aparecendo na View. A confirmação depende da execução no banco `classicmodels`, utilizando o DBeaver.

## 3. Síntese Teórica: Segurança e Abstração

Uma View é uma consulta SQL salva no banco de dados que pode ser consultada como uma tabela virtual. Em vez de repetir um comando longo toda vez que precisa de um relatório, o usuário acessa a View pronta. Esse recurso é explicado nos materiais da Universidade Federal do Rio Grande do Norte (UFRN) e da Universidade Virtual do Estado de São Paulo (UNIVESP).

### Segurança de Dados

Uma View permite mostrar apenas os campos necessários para quem vai consultar as informações. No caso da ClassicModels, o gerente pode visualizar o nome do cliente, o número do pedido e o valor total, sem precisar acessar outras colunas das tabelas originais.

Para que essa proteção funcione, também é necessário configurar corretamente as permissões de acesso no banco de dados.
### Abstração da Complexidade

A View esconde os detalhes de uma consulta mais trabalhosa. O gerente não precisa saber como funcionam os `JOINs`, os filtros ou a soma dos valores.

Ele pode consultar o relatório com um comando simples:

```sql
SELECT * FROM view_relatorio_financeiro;
```

Isso facilita o uso e evita que cada pessoa faça o cálculo de um jeito diferente.

Por esses motivos, utilizar uma View nesse caso torna o relatório financeiro mais simples de consultar e ajuda a manter os critérios de cálculo iguais para todos.

## Referências

UNIVERSIDADE FEDERAL DO RIO GRANDE DO NORTE (UFRN). **Aula 07 – Linguagem SQL: Subconsultas e Visões**. Instituto Metrópole Digital, [s. d.]. Disponível em: https://materialpublic.imd.ufrn.br/curso/disciplina/4/56/7/10. Acesso em: 3 nov. 2026.

UNIVERSIDADE VIRTUAL DO ESTADO DE SÃO PAULO (UNIVESP). **Banco de Dados – Introdução a Views e conceitos básicos sobre Data Warehouse e Data Lake**. YouTube, 16 fev. 2024. Disponível em: https://www.youtube.com/watch?v=lUSAFdDk1c0. Acesso em: 3 nov. 2026.

ORACLE. **MySQL 8.4 Reference Manual: CREATE VIEW Statement**. [S. l.], [s. d.]. Disponível em: https://dev.mysql.com/doc/refman/8.4/en/create-view.html. Acesso em: 3 nov. 2026.
