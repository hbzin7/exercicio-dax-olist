#  Exercício Prático III: DAX Aplicado ao Negócio (Olist)

**Repositório:** Projeto de Business Intelligence II — CEUB
**Ferramentas:** Power BI & DAX
**Modelo de Dados:** Star Schema (f_pedidos, dim_clientes, dim_produtos, dim_vendedor, dim_reviews)

---

##  Bloco 1: Volume e Operações

### Q01. Qual é o número total de pedidos registrados na plataforma?
* **Etapa A - Raciocínio de Negócio:**
  Para descobrir a quantidade total de pedidos transacionados no e-commerce, precisamos contar as linhas da nossa tabela fato f_pedidos, pois cada registro representa uma ordem/pedido efetuado na plataforma.
* **Etapa B - Código DAX:**
  Total Pedidos = COUNTROWS(f_pedidos)

---

### Q02. Quantos clientes únicos já realizaram ao menos uma compra na Olist?
* **Etapa A - Raciocínio de Negócio:**
  Como um mesmo cliente pode realizar mais de um pedido ao longo do tempo, para identificar a base real de compradores ativos precisamos fazer uma contagem distinta (única) do identificador do cliente (id_cliente) na tabela f_pedidos.
* **Etapa B - Código DAX:**
  Total Clientes Unicos = DISTINCTCOUNT(f_pedidos[id_cliente])

---

### Q03. Qual é o valor total faturado pela plataforma?
* **Etapa A - Raciocínio de Negócio:**
  O faturamento bruto acumulado é a soma de todos os valores negociados nas vendas. Calculamos isso somando os valores da coluna valor_pedido pertencente à tabela fato f_pedidos.
* **Etapa B - Código DAX:**
  Faturamento Total = SUM(f_pedidos[valor_pedido])

---

### Q04. Qual é o valor total de frete cobrado em todos os pedidos?
* **Etapa A - Raciocínio de Negócio:**
  Para calcular o montante total arrecadado com taxas de entrega, fazemos a somatória simples de todos os valores presentes no campo valor_frete da tabela f_pedidos.
* **Etapa B - Código DAX:**
  Total Frete = SUM(f_pedidos[valor_frete])

---

### Q05. Quantos produtos distintos foram vendidos ao longo do histórico da plataforma?
* **Etapa A - Raciocínio de Negócio:**
  Para mapear a variedade do portfólio movimentado, realizamos a contagem dos códigos únicos de produtos (id_produto) que figuram no histórico de vendas da tabela f_pedidos.
* **Etapa B - Código DAX:**
  Total Produtos Distintos = DISTINCTCOUNT(f_pedidos[id_produto])

---

### Q06. Quantos vendedores distintos realizaram ao menos uma venda?
* **Etapa A - Raciocínio de Negócio:**
  Buscamos identificar a quantidade de parceiros comerciais ativos. Para isso, fazemos a contagem distinta da chave id_vendedor diretamente na tabela fato f_pedidos.
* **Etapa B - Código DAX:**
  Total Vendedores Ativos = DISTINCTCOUNT(f_pedidos[id_vendedor])

---

### Q07. Qual é o ticket médio por pedido, ou seja, quanto um pedido vale em média?
* **Etapa A - Raciocínio de Negócio:**
  O ticket médio indica o valor médio transacionado em cada compra. Calculamos dividindo o faturamento total (Faturamento Total, da Q03) pelo total de pedidos (Total Pedidos, da Q01), utilizando a função DIVIDE para prevenir erros de divisão por zero.
* **Etapa B - Código DAX:**
  Ticket Medio = DIVIDE([Faturamento Total], [Total Pedidos], 0)

---

### Q08. Qual é o frete médio por pedido?
* **Etapa A - Raciocínio de Negócio:**
  O frete médio nos mostra o custo médio de entrega suportado por pedido. Para obter este indicador, dividimos o valor total de frete cobrado (Total Frete, da Q04) pela quantidade total de pedidos (Total Pedidos, da Q01).
* **Etapa B - Código DAX:**
  Frete Medio = DIVIDE([Total Frete], [Total Pedidos], 0)

---

##  Bloco 2: Satisfação e Qualidade

### Q09. Qual é a nota média de avaliação dos pedidos na plataforma?
* **Etapa A - Raciocínio de Negócio:**
  Para aferir a média global de satisfação, tiramos a média simples da coluna nota_avaliacao localizada na tabela de dimensões de avaliações (dim_reviews).
* **Etapa B - Código DAX:**
  Nota Media Avaliacao = AVERAGE(dim_reviews[nota_avaliacao])

---

### Q10. Quantos pedidos receberam a nota máxima (5) ou seja, clientes plenamente satisfeitos?
* **Etapa A - Raciocínio de Negócio:**
  Queremos saber o volume de clientes completamente satisfeitos. Para isso, contamos a quantidade de linhas da tabela dim_reviews filtrando apenas os registros onde nota_avaliacao é igual a 5.
* **Etapa B - Código DAX:**
  Total Pedidos Nota 5 = CALCULATE(COUNTROWS(dim_reviews), dim_reviews[nota_avaliacao] = 5)

---

### Q11. Quantos pedidos receberam nota crítica (1 ou 2), sinalizando insatisfação grave?
* **Etapa A - Raciocínio de Negócio:**
  Para monitorar a insatisfação grave, contamos o total de avaliações na tabela dim_reviews aplicando um filtro contextual onde a nota seja 1 ou 2.
* **Etapa B - Código DAX:**
  Total Pedidos Nota Critica = CALCULATE(COUNTROWS(dim_reviews), dim_reviews[nota_avaliacao] IN {1, 2})

---

### Q12. Qual é o percentual de pedidos com nota 5 sobre o total de pedidos avaliados?
* **Etapa A - Raciocínio de Negócio:**
  Calculamos a proporção de excelência dividindo o número de pedidos com nota máxima (Total Pedidos Nota 5, da Q10) pelo total geral de avaliações registradas na tabela dim_reviews.
* **Etapa B - Código DAX:**
  % Pedidos Nota 5 = DIVIDE([Total Pedidos Nota 5], COUNTROWS(dim_reviews), 0)

---

### Q13. Qual é o percentual de pedidos com nota crítica (1 ou 2) sobre o total de pedidos avaliados?
* **Etapa A - Raciocínio de Negócio:**
  A proporção de insatisfação é obtida dividindo o volume de notas críticas (Total Pedidos Nota Critica, da Q11) pelo total absoluto de avaliações na tabela dim_reviews.
* **Etapa B - Código DAX:**
  % Pedidos Nota Critica = DIVIDE([Total Pedidos Nota Critica], COUNTROWS(dim_reviews), 0)

---

### Q14. Crie uma coluna calculada na tabela dim_reviews que classifique cada avaliação em três categorias: "Positiva" (notas 4 e 5), "Neutra" (nota 3) e "Negativa" (notas 1 e 2).
* **Etapa A - Raciocínio de Negócio:**
  Criamos uma nova coluna linha a linha na tabela dim_reviews agrupando as notas: se for 4 ou 5, rotulamos como "Positiva"; se for 3, "Neutra"; se for 1 ou 2, "Negativa".
* **Etapa B - Código DAX:**
  classificacao_avaliacao = SWITCH(TRUE(), dim_reviews[nota_avaliacao] >= 4, "Positiva", dim_reviews[nota_avaliacao] = 3, "Neutra", dim_reviews[nota_avaliacao] <= 2, "Negativa", "Não Avaliado")

---

### Q15. Quantos pedidos receberam avaliação do tipo "Negativa" conforme a coluna criada na Q14?
* **Etapa A - Raciocínio de Negócio:**
  Utilizando a coluna de classificação recém-criada na Q14, contamos o número de registros na tabela dim_reviews onde o valor categorizado seja igual a "Negativa".
* **Etapa B - Código DAX:**
  Total Avaliacoes Negativas = CALCULATE(COUNTROWS(dim_reviews), dim_reviews[classificacao_avaliacao] = "Negativa")

---

##  Bloco 3: Logística e Prazo de Entrega

### Q16. Crie uma coluna calculada na tabela f_pedidos que calcule o tempo de entrega em dias, definido como a diferença entre data_entrega e data_compra.
* **Etapa A - Raciocínio de Negócio:**
  Para calcular quantos dias a encomenda levou até chegar ao consumidor, criamos uma coluna calculada na tabela f_pedidos aplicando a diferença em dias entre a data_entrega e a data_compra.
* **Etapa B - Código DAX:**
  dias_tempo_entrega = DATEDIFF(f_pedidos[data_compra], f_pedidos[data_entrega], DAY)

---

### Q17. Crie uma coluna calculada em f_pedidos que classifique cada pedido como "No prazo" se data_entrega <= data_limite_envio, Ou "Atrasado" caso contrário.
* **Etapa A - Raciocínio de Negócio:**
  Analisamos linha a linha na tabela f_pedidos: se a data_entrega for menor ou igual à data_limite_envio, a entrega é classificada como "No prazo"; do contrário, será pontuada como "Atrasado".
* **Etapa B - Código DAX:**
  status_prazo = IF(f_pedidos[data_entrega] <= f_pedidos[data_limite_envio], "No prazo", "Atrasado")

---

### Q18. Qual é o tempo médio de entrega em dias considerando todos os pedidos entregues?
* **Etapa A - Raciocínio de Negócio:**
  Calculamos a média aritmética dos valores contidos na coluna calculada dias_tempo_entrega (criada na Q16) para todos os pedidos que possuem a entrega concluída/registrada.
* **Etapa B - Código DAX:**
  Tempo Medio Entrega = AVERAGE(f_pedidos[dias_tempo_entrega])

---

### Q19. Quantos pedidos foram entregues com atraso?
* **Etapa A - Raciocínio de Negócio:**
  Contamos o total de linhas na tabela f_pedidos filtrando as ocorrências em que a coluna status_prazo (criada na Q17) possui o valor "Atrasado".
* **Etapa B - Código DAX:**
  Total Pedidos Atrasados = CALCULATE(COUNTROWS(f_pedidos), f_pedidos[status_prazo] = "Atrasado")

---

### Q20. Qual é o percentual de pedidos entregues no prazo sobre o total de pedidos com entrega registrada?
* **Etapa A - Raciocínio de Negócio:**
  Medimos a eficiência logística dividindo a contagem de pedidos que possuem o status "No prazo" pelo total de pedidos da tabela f_pedidos que têm a data de entrega devidamente preenchida.
* **Etapa B - Código DAX:**
  % Entregas No Prazo Ops = DIVIDE(CALCULATE(COUNTROWS(f_pedidos), f_pedidos[status_prazo] = "No prazo"), CALCULATE(COUNTROWS(f_pedidos), NOT(ISBLANK(f_pedidos[data_entrega]))), 0)

---

### Q21. Qual é o tempo médio entre a aprovação do pedido e o envio ao transportador (diferença entre data_envio e data_aprovacao)?
* **Etapa A - Raciocínio de Negócio:**
  Mede a agilidade do vendedor (fulfillment/despacho). Usamos a função iterativa AVERAGEX para calcular, linha a linha em f_pedidos, a diferença em dias entre data_aprovacao e data_envio, extraindo a média final do processo.
* **Etapa B - Código DAX:**
  Tempo Medio Despacho Lojista = AVERAGEX(FILTER(f_pedidos, NOT(ISBLANK(f_pedidos[data_envio])) && NOT(ISBLANK(f_pedidos[data_aprovacao]))), DATEDIFF(f_pedidos[data_aprovacao], f_pedidos[data_envio], DAY))

---

### Q22. Crie uma coluna calculada que calcule o prazo prometido ao cliente em dias - definido como a diferença entre data_prevista_entrega e data_compra.
* **Etapa A - Raciocínio de Negócio:**
  Para registrar a expectativa de espera exibida na tela de checkout, calculamos na tabela f_pedidos a diferença em dias entre a data_compra e a data_prevista_entrega.
* **Etapa B - Código DAX:**
  prazo_prometido_dias = DATEDIFF(f_pedidos[data_compra], f_pedidos[data_prevista_entrega], DAY)

---

### Q23. Qual é o peso médio (em gramas) dos produtos vendidos?
* **Etapa A - Raciocínio de Negócio:**
  Como a informação física do produto está na tabela de dimensão dim_produtos, utilizamos a função RELATED dentro do iterador AVERAGEX para buscar o peso correspondente de cada item vendido na f_pedidos e calcular a sua média em gramas.
* **Etapa B - Código DAX:**
  Peso Medio Produtos Gramas = AVERAGEX(f_pedidos, RELATED(dim_produtos[peso_g]))

---

##  Bloco 4: Segmentação Geográfica e por Categoria

### Q24. Crie uma coluna calculada em f_pedidos que traga o estado do cliente (estado_cliente) a partir da tabela dim_clientes.
* **Etapa A - Raciocínio de Negócio:**
  Para facilitar filtros no nível do pedido, buscamos a coluna estado_cliente localizada na tabela relacional dim_clientes e trazemos para a tabela fato f_pedidos usando a função RELATED.
* **Etapa B - Código DAX:**
  estado_cliente_pedidos = RELATED(dim_clientes[estado_cliente])

---

### Q25. Quantos pedidos foram realizados por clientes do estado de São Paulo (SP)?
* **Etapa A - Raciocínio de Negócio:**
  Contamos o total de pedidos registrados na tabela f_pedidos aplicando o filtro de contexto para considerar apenas os clientes vinculados ao estado "SP".
* **Etapa B - Código DAX:**
  Total Pedidos Clientes SP = CALCULATE(COUNTROWS(f_pedidos), dim_clientes[estado_cliente] = "SP")

---

### Q26. Qual é o valor total faturado em pedidos de clientes da região Sudeste?
* **Etapa A - Raciocínio de Negócio:**
  Somamos a receita (valor_pedido) na tabela f_pedidos restringindo a amostra para compras realizadas por consumidores residentes nos estados componentes da região Sudeste (SP, RJ, MG, ES).
* **Etapa B - Código DAX:**
  Faturamento Regiao Sudeste = CALCULATE(SUM(f_pedidos[valor_pedido]), dim_clientes[estado_cliente] IN {"SP", "RJ", "MG", "ES"})

---

### Q27. Crie uma coluna calculada em f_pedidos que traga a categoria do produto (categoria_produto) a partir da tabela dim_produtos.
* **Etapa A - Raciocínio de Negócio:**
  Trazemos a descrição da categoria do item vendível da tabela dim_produtos para a tabela fato f_pedidos por meio do relacionamento entre tabelas com a função RELATED.
* **Etapa B - Código DAX:**
  categoria_produto_pedidos = RELATED(dim_produtos[categoria_produto])

---

### Q28. Qual é o valor total faturado na categoria "cama_mesa_banho"?
* **Etapa A - Raciocínio de Negócio:**
  Calculamos o faturamento somando a coluna valor_pedido na fato f_pedidos, modificando o contexto de avaliação para filtrar estritamente a categoria "cama_mesa_banho" presente na dimensão de produtos.
* **Etapa B - Código DAX:**
  Faturamento Cama Mesa Banho = CALCULATE(SUM(f_pedidos[valor_pedido]), dim_produtos[categoria_produto] = "cama_mesa_banho")

---

### Q29. Quantos pedidos foram realizados por vendedores do estado de São Paulo (SP)?
* **Etapa A - Raciocínio de Negócio:**
  Contamos o volume de pedidos em f_pedidos filtrando a origem das ofertas: considerando apenas os vendedores cujo estado de origem registrado na tabela dim_vendedor seja igual a "SP".
* **Etapa B - Código DAX:**
  Total Pedidos Vendedores SP = CALCULATE(COUNTROWS(f_pedidos), dim_vendedor[estado_vendedor] = "SP")

---

##  Bloco 5: Painel OKR — Montando os KRs Estratégicos

### Q30. Construa a medida % Entregas no Prazo que retorna o percentual de pedidos entregues no prazo (KR1).
* **Etapa A - Raciocínio de Negócio:**
  Mede o percentual das entregas pontuais sobre o total finalizado. Dividimos a quantidade de pedidos cujo status_prazo é igual a "No prazo" pelo total de pedidos com entrega registrada.
* **Etapa B - Código DAX:**
  KR1 % Entregas no Prazo = DIVIDE(CALCULATE(COUNTROWS(f_pedidos), f_pedidos[status_prazo] = "No prazo"), CALCULATE(COUNTROWS(f_pedidos), NOT(ISBLANK(f_pedidos[data_entrega]))), 0)

---

### Q31. Construa a medida % Avaliações Negativas (KR2).
* **Etapa A - Raciocínio de Negócio:**
  Monitora a meta de redução de atrito. Dividimos o total de pedidos classificados com notas 1 e 2 (classificacao_avaliacao = "Negativa") pelo total de notas ativas em dim_reviews.
* **Etapa B - Código DAX:**
  KR2 % Avaliacoes Negativas = DIVIDE(CALCULATE(COUNTROWS(dim_reviews), dim_reviews[classificacao_avaliacao] = "Negativa"), COUNTROWS(dim_reviews), 0)

---

### Q32. Construa a medida Tempo Médio de Entrega em dias (KR3).
* **Etapa A - Raciocínio de Negócio:**
  Mede a agilidade do ciclo total. Calculamos a média do indicador de dias corridos entre data_compra e data_entrega utilizando a coluna de apoio dias_tempo_entrega.
* **Etapa B - Código DAX:**
  KR3 Tempo Medio Entrega = AVERAGE(f_pedidos[dias_tempo_entrega])

---

### Q33. Construa a medida Nota Média de Avaliação robusta (KR4).
* **Etapa A - Raciocínio de Negócio:**
  Garante o monitoramento seguro da nota média. Avaliamos via IF e COUNTROWS se há ao menos uma nota no contexto atual; se houver, executamos a média simples da nota_avaliacao, evitando erros sem contexto.
* **Etapa B - Código DAX:**
  KR4 Nota Media Avaliacao = IF(COUNTROWS(dim_reviews) > 0, AVERAGE(dim_reviews[nota_avaliacao]), BLANK())

---

### Q34. Desafio integrador: Nota Média Pedidos Atrasados vs. Média Geral.
* **Etapa A - Raciocínio de Negócio:**
  Para analisar o impacto direto da logística na satisfação do cliente, calculamos a média das notas de avaliação isolando no contexto de cálculo exclusivamente os pedidos cujo status da entrega foi "Atrasado".
* **Etapa B - Código DAX:**
  Nota Media Pedidos Atrasados = CALCULATE(AVERAGE(dim_reviews[nota_avaliacao]), f_pedidos[status_prazo] = "Atrasado")

* **Interpretação de Negócio:**
  Ao comparar a Nota Média Pedidos Atrasados com a Nota Média Geral (KR4), observa-se tipicamente uma queda substancial no indicador dos pedidos atrasados. Isso comprova que a experiência logística é o principal direcionador da satisfação no e-commerce. Atrasos convertem clientes satisfeitos em detratores (notas 1 e 2). Focar na eficiência do despachante e na malha de transporte é o investimento com maior retorno para alavancar a retenção e o NPS do marketplace.
