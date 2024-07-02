# ProjetoSQL

# Exploração e Análise de Dados de Crédito com SQL

## Os Dados

Vamos trabalhar com uma tabela de dados que representa informações de clientes de um banco e contém as seguintes colunas:

- **idade**: idade do cliente
- **sexo**: sexo do cliente (F ou M)
- **dependentes**: número de dependentes do cliente
- **escolaridade**: nível de escolaridade dos clientes
- **salario_anual**: faixa salarial do cliente
- **tipo_cartao**: tipo de cartão do cliente
- **qtd_produtos**: quantidade de produtos comprados nos últimos 12 meses
- **iteracoes_12m**: quantidade de iterações/transações nos últimos 12 meses
- **meses_inativo_12m**: quantidade de meses que o cliente ficou inativo
- **limite_credito**: limite de crédito do cliente
- **valor_transacoes_12m**: valor das transações dos últimos 12 meses
- **qtd_transacoes_12m**: quantidade de transações dos últimos 12 meses

A tabela foi criada no AWS Athena junto com o S3 Bucket com uma versão dos dados disponibilizados em: [Dataset no GitHub](https://github.com/andre-marcos-perez/ebac-course-utils/tree/main/dataset)

## Exploração de Dados

### Quantidade de Informações na Base de Dados
- **Query**: `SELECT count(*) AS Quantidade_Linhas FROM credito`
- **Resposta**: 2564 linhas

### Como São os Dados (10 Primeiras Linhas)
- **Query**: `SELECT * FROM credito LIMIT 10`
- Valores nulos (na) serão tratados posteriormente.

### Tipos de Dados
- **Query**: `DESCRIBE credito`

### Tipos de Escolaridade Disponíveis no Dataset
- **Query**: `SELECT DISTINCT escolaridade FROM credito`
- Valores nulos (na) serão tratados posteriormente.

### Tipos de Estado Civil Disponíveis no Dataset
- **Query**: `SELECT DISTINCT estado_civil FROM credito`
- Valores nulos (na) serão tratados posteriormente.

### Tipos de Salario Anual Disponíveis no Dataset
- Os dados tratam-se da faixa salarial anual dos clientes.
- **Query**: `SELECT DISTINCT salario_anual FROM credito`
- Valores nulos (na) serão tratados posteriormente.

### Tipos de Cartão Disponíveis no Dataset
- **Query**: `SELECT DISTINCT tipo_cartao FROM credito`

## Análise de Dados

### Número de Clientes por Faixa Salarial
- **Query**: `SELECT count(*), salario_anual FROM credito GROUP BY salario_anual`
- Entre as faixas de renda informadas, há uma concentração maior (701) em clientes que ganham menos de $40k ao ano, sugerindo uma abordagem de negócios focada neste público.

### Distribuição de Clientes por Sexo
- **Query**: `SELECT count(*), sexo FROM credito GROUP BY sexo`
- Utilizando Python, criamos um gráfico de pizza para visualizar melhor essas informações.

### Faixa etária dos clientes
- **Query**: `select avg(idade) as media_idade, min(idade) as min_idade, max(idade) as max_idade, sexo from credito group by sexo`
- Não foram retiradas informações relevantes desses dados, devido não ter sido encontrada nenhuma discrepância de idades entre homens e mulheres.

### Qual a maior e menor transação dos clientes
- **Query**: `select min(valor_transacoes_12m) as transacao_minima, max(valor_transacoes_12m) as transacao_minima from credito`
- Nesse banco de dados vemos a soma de transações em 12 meses, que variam de 510.16 a 5776.58

### Quais as características dos clientes que possuem os maiores creditos
- **Query**: `select max(limite_credito) as limite_credito, escolaridade, tipo_cartao, sexo from credito where escolaridade != 'na' and tipo_cartao != 'na' group by escolaridade, tipo_cartao, sexo order by limite_credito desc limit 10`
- Não parece haver um impacto da escolaridade no limite. O limite mais alto é oferecido para um homem sem educação formal. O cartão também parece não estar relacionado com a escolaridade nem com o limite. Dentre os maiores limites, encontramos clientes com cartão: gold, silver, platinum e blue

### Quais as características dos clientes que possuem os menores creditos?
- **Query**: `select max(limite_credito) as limite_credito, escolaridade, tipo_cartao, sexo from credito where escolaridade != 'na' and tipo_cartao != 'na' group by escolaridade, tipo_cartao, sexo order by limite_credito asc`
- Dessa vez conseguimos perceber que não há clientes com cartão platinum dentre os menores limites. Também foi possível perceber que a maioria dos menores limites são mulheres enquanto nos maiores limites predomina homens.

### Homens ou mulheres gastam mais?
- **Query**: `select max(valor_transacoes_12m) as maior_valor_gasto, avg(valor_transacoes_12m) as media_valor_gasto, min(valor_transacoes_12m) as min_valor_gasto, sexo from credito group by sexo`
- Apesar da diferença nos limites, os gastos de homens e mulheres são similares.

### O salário impacta no limite?
- **Query**: `select avg(qtd_produtos) as qts_produtos, avg(valor_transacoes_12m) as media_valor_transacoes, avg(limite_credito) as media_limite, sexo, salario_anual from credito where salario_anual != 'na' group by sexo, salario_anual order by avg(valor_transacoes_12m) desc`
- Nota-se que as pessoas que tem menor faixa salarial também apresentam menor limite de crédito.

### Conclusão

Essas foram algumas análises extraídas do dataset de crédito.

Alguns insights interessantes:

* a maior parte dos clientes possui renda até 40K
* a maior parte dos clientes é homem
* a escolaridade não parece influenciar no limite nem no tipo do cartão
* os clientes com maiores limites são em sua maioria homens
* os clientes com menores limites são em sua maioria mulheres
* dentre os menores limites não há presença de cartão platinum
* a faixa salarial impacta diretamente no limite de crédito
* não existem clientes com salário anual acima de 60K do sexo feminino

