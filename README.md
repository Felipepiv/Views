# Diagnóstico do problema:

O erro cometido pelo Dev Jr. 
foi agrupar os resultados somente pelo número do pedido (orderNumber), 
mas também selecionar o nome do cliente (customerName) sem incluí-lo no agrupamento.

Com isso, fica evidente uma falha na lógica de agrupamento. 
O campo customerName está presente no SELECT, mas não foi incluído no GROUP BY. 
Essa situação pode gerar resultados inconsistentes ou até mesmo um erro, dependendo da configuração do MySQL.

# A Refatoração (O Conserto da View):

![](img/WhatsApp%20Image%202026-11-03%20at%2020.15.34.jpeg)

### Explicando as alterações:

1. CREATE OR REPLACE VIEW: cria uma view ou substitui a view anterior
2. "Where o.status"
3. GROUP BY c.customerName, o.orderNumber: agrupa os resultados pelo nome do cliente e pelo número do pedido, corrigindo a falha do código original.
4. SUM(od.quantityOrdered * od.priceEach): calcula o valor total de cada pedido, multiplicando a quantidade de produtos pelo preço unitário.


![](img/WhatsApp%20Image%202026-11-03%20at%2020.20.12.jpeg)

Código usado para verificar se a View view_relatorios_financeiro está mostrando algum pedido com status Cancelled(cancelado). Com isso, a view foi criada com o filtro WHERE o.status <> 'Cancelled', o resultado esperado esperado é que nenhuma linha seja retornada, confirmando que os pedidos cancelados foram excluídos do relatório financeiro.

# Síntese Teórica: Segurança e Abstração

## Segurança de dados

Uma das principais vantagens das Views é possibilitar o controle das informações que serão disponibilizadas para cada tipo de usuário. Por exemplo, a tabela `customers` pode armazenar diversos dados dos clientes, mas, para um relatório financeiro, são necessários apenas o nome do cliente, o número do pedido e o valor total. Dessa forma, a View permite apresentar somente essas informações, evitando a exposição de dados que não são relevantes para aquela atividade.

Além disso, também é possível restringir os registros exibidos utilizando condições como `WHERE`. Neste estudo de caso, por exemplo, a View pode apresentar apenas os pedidos que atendam aos critérios estabelecidos, como um determinado período de envio.

Entretanto, para garantir essa proteção, é necessário configurar corretamente as permissões dos usuários. A equipe responsável pelo banco de dados pode conceder acesso de consulta (`SELECT`) à View, sem liberar o acesso direto às tabelas originais. No MySQL, a opção `SQL SECURITY` determina se as permissões serão verificadas com base no criador da View (`DEFINER`) ou no usuário que está realizando a consulta (`INVOKER`) (MYSQL, 2026).

Assim, as Views contribuem para a aplicação do princípio do menor privilégio, garantindo que cada usuário tenha acesso somente às informações necessárias para desempenhar suas funções. Para isso, as permissões devem ser configuradas e testadas com contas de usuários adequadas.

## Complexidade de abstração

Outra vantagem importante das Views é a capacidade de simplificar consultas SQL mais complexas. Quando uma consulta envolve várias tabelas, relacionamentos, filtros e cálculos, sua escrita e manutenção podem se tornar mais difíceis.

Ao armazenar essa lógica em uma View, a equipe de banco de dados disponibiliza uma estrutura mais simples para os usuários. No banco ClassicModels, por exemplo, o funcionário não precisa conhecer todos os relacionamentos entre as tabelas `customers`, `orders` e `orderdetails`, nem escrever novamente a fórmula utilizada para calcular o valor total de cada pedido. Basta consultar a View pelo seu nome para obter os dados já organizados.

Outro benefício é a padronização das consultas. Quando diferentes setores utilizam a mesma View, todos trabalham com os mesmos critérios e regras, diminuindo as chances de ocorrerem divergências nos resultados. Caso seja necessário modificar alguma regra, a equipe responsável pode atualizar a definição da View, sem precisar alterar individualmente todas as consultas utilizadas em relatórios e aplicações.

Portanto, a View funciona como uma camada intermediária entre as tabelas do banco de dados e os usuários, ocultando os detalhes mais complexos da estrutura interna e disponibilizando as informações de maneira mais simples, organizada e adequada às necessidades de cada atividade.

## Referências

DEV MEDIA. **MySQL – Trabalhando com Views**. DevMedia, [s. l.], [s. d.]. Disponível em: <https://www.devmedia.com.br/mysql-trabalhando-com-views/8724>. Acesso em: 3 nov. 2026.

COMO CRIAR VISUALIZAÇÕES NO SQL – CRIAÇÃO DE VIEWS. [S. l.: s. n.], [s. d.]. 1 vídeo (YouTube). Disponível em: <https://youtu.be/HAWBo3ZROBk>. Acesso em: 3 nov. 2026.