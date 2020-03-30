---
title: Design azure Cosmos TABELAS DB para dimensionamento e desempenho
description: 'Guia de design de armazenamento de mesa azure: tabelas escaláveis e performáticas no armazenamento Azure Cosmos DB e Azure Table'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246468"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Guia de design da tabela de armazenamento da tabela azure Table: tabelas escaláveis e performáticas

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Para projetar tabelas escalonáveis e de alto desempenho, é necessário considerar uma variedade de fatores, inclusive os custos. Se já projetou esquemas para bancos de dados relacionais, você já estará familiarizado com essas considerações. Porém, embora haja algumas semelhanças entre o armazenamento de Tabelas do Azure e os modelos relacionais, existem muitas diferenças importantes. Normalmente, essas diferenças resultam em designs diferentes que possam parecer contraintuitivos ou errados para alguém que esteja familiarizado com bancos de dados relacionais, mas que fazem sentido se você estiver criando para um armazenamento de chave/valor de NoSQL, como armazenamento de Tabelas.

O armazenamento de tabelas foi projetado para suportar aplicativos em escala de nuvem que podem conter bilhões de entidades ("linhas" na terminologia do banco de dados relacionais) de dados ou para conjuntos de dados que devem suportar grandes volumes de transações. Portanto, você precisa pensar diferente sobre como armazenar seus dados e entender como funciona o armazenamento de tabelas. Um armazenamento de dados NoSQL bem projetado pode permitir que sua solução dimensione muito mais (e a um custo menor) do que uma solução que usa um banco de dados relacional. Este guia ajuda você com esses tópicos.  

## <a name="about-azure-table-storage"></a>Sobre o armazenamento de tabelas do Azure
Esta seção destaca algumas das principais características do armazenamento de tabela que são especialmente relevantes para projetar para desempenho e escalabilidade. Se você é novo no armazenamento de armazenamento e tabela do Azure, consulte [Introdução ao Armazenamento Microsoft Azure](../storage/common/storage-introduction.md) e [Comece com o armazenamento da Tabela Azure usando .NET](table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia esteja no armazenamento de tabelas, ele inclui algumas discussões sobre o armazenamento azure Queue e o armazenamento Azure Blob, e como você pode usá-los junto com o armazenamento de tabela em uma solução.  

O armazenamento de mesa usa um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades da entidade. Cada entidade tem um par de chaves para identificá-lo exclusivamente, e uma coluna de carimbo de tempo que o armazenamento de tabela usa para rastrear quando a entidade foi atualizada pela última vez. O campo de carimbo de tempo é adicionado automaticamente, e você não pode substituir manualmente o carimbo de tempo com um valor arbitrário. O armazenamento de tabelas usa este último carimbo de tempo modificado (LMT) para gerenciar a concorrência otimista.  

> [!NOTE]
> As operações de API `ETag` REST de armazenamento de tabela também retornam um valor que deriva do LMT. Neste documento, os termos ETag e LMT são usados de forma intercambiável, pois se referem aos mesmos dados subjacentes.  
> 
> 

O exemplo a seguir mostra uma estrutura de tabela simples para armazenar entidades de funcionário e departamento. Muitos dos exemplos mostrados posteriormente neste guia baseiam-se neste design simples.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>department</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Até agora, este design se parece com uma tabela em um banco de dados relacional. As principais diferenças são as colunas obrigatórias e a capacidade de armazenar vários tipos de entidades na mesma tabela. Além disso, cada uma das propriedades definidas pelo usuário, como **FirstName** ou **Age,** tem um tipo de dados, como inteiro ou string, assim como uma coluna em um banco de dados relacional. Ao contrário de um banco de dados relacional, no entanto, a natureza sem esquema de armazenamento de tabela significa que uma propriedade não precisa ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos em uma única propriedade, você deve usar um formato serializado como JSON ou XML. Para obter mais informações, consulte [O modelo de dados de armazenamento da tabela de compreensão](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Sua escolha `PartitionKey` `RowKey` e é fundamental para um bom design de mesa. Cada entidade armazenada em uma tabela `PartitionKey` deve `RowKey`ter uma combinação única de e . Como acontece com as teclas `PartitionKey` em `RowKey` uma tabela de banco de dados relacional, os valores e valores são indexados para criar um índice clustered que permite olhares rápidos. O armazenamento de tabelas, no entanto, não cria nenhum índice secundário, então essas são as duas únicas propriedades indexadas (alguns dos padrões descritos posteriormente mostram como você pode contornar essa limitação aparente).  

Uma tabela é composta de uma ou mais partições, e muitas das decisões de design que você tomar serão em torno de escolher uma solução adequada `PartitionKey` e `RowKey` otimizar sua solução. Uma solução pode consistir em apenas uma única tabela que contém todas as suas entidades organizadas em partições, mas normalmente uma solução tem várias tabelas. As tabelas ajudam você a organizar logicamente suas entidades e ajudam você a gerenciar o acesso aos dados usando listas de controle de acesso. Você pode soltar uma tabela inteira usando uma única operação de armazenamento.  

### <a name="table-partitions"></a>Partições de tabela
O nome da conta, `PartitionKey` o nome da tabela e, em conjunto, identificam a partição dentro do serviço de armazenamento onde o armazenamento de tabela armazena a entidade. Além de fazer parte do esquema de endereçamento para entidades, as partições definem um escopo para transações (veja a seção mais tarde neste artigo, [Transações de grupos de entidades),](#entity-group-transactions)e formam a base de como o armazenamento de tabelas escala. Para obter mais informações sobre partições de tabela, consulte [Lista de verificação de desempenho e escalabilidade para armazenamento em tabela](../storage/tables/storage-performance-checklist.md).  

No armazenamento de tabela, um nó individual presta serviços a uma ou mais partições completas e as escalas de serviço, dinamicamente, equilibram partições em nós. Se um nó estiver em carga, o armazenamento de tabela pode dividir o intervalo de partições reparadas por esse nó em nós diferentes. Quando o tráfego diminui, o armazenamento de tabela pode mesclar os intervalos de partição de nós silenciosos de volta para um único nó.  

Para obter mais informações sobre os detalhes internos do armazenamento de tabela e, em particular, como ele gerencia partições, consulte [o Microsoft Azure Storage: Um serviço de armazenamento em nuvem altamente disponível com forte consistência.](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

### <a name="entity-group-transactions"></a>Transações de grupos de entidades
No armazenamento de tabelas, as transações de grupos de entidades (EGTs) são o único mecanismo incorporado para realizar atualizações atômicas em várias entidades. Os EGTs também são chamados de *transações em lote.* Os EGTs só podem operar em entidades armazenadas na mesma partição (compartilhando a mesma chave de partição em uma determinada tabela), então sempre que você precisar de comportamento transacional atômico em várias entidades, certifique-se de que essas entidades estejam na mesma partição. Esta é muitas vezes uma razão para manter vários tipos de entidades na mesma tabela (e partição), e não usar várias tabelas para diferentes tipos de entidades. Uma única EGT pode operar no máximo 100 entidades.  Se você enviar vários EGTs simultâneos para processamento, é importante garantir que esses EGTs não operem em entidades comuns entre EGTs. Caso contrário, você corre o risco de atrasar o processamento.

EGTs também apresentam uma desvantagem potencial para avaliar em seu design. O uso de mais partições aumenta a escalabilidade do seu aplicativo, porque o Azure tem mais oportunidades para solicitações de balanceamento de carga entre nós. Mas isso pode limitar a capacidade do seu aplicativo de realizar transações atômicas e manter uma forte consistência para seus dados. Além disso, existem destinos de escalabilidade específica no nível de uma partição que pode limitar a taxa de transferência de transações que você pode esperar para um único nó.

Para obter mais informações sobre metas de escalabilidade para contas de armazenamento do Azure, consulte [metas de escalabilidade para contas de armazenamento padrão](../storage/common/scalability-targets-standard-account.md). Para obter mais informações sobre metas de escalabilidade para armazenamento de tabela, consulte [metas de escalabilidade e desempenho para armazenamento de tabela](../storage/tables/scalability-targets.md). As seções posteriores deste guia discutem diversas estratégias de design que o ajudarão a gerenciar compensações como esta e descrevem a melhor escolha para sua chave de partição com base nos requisitos específicos do aplicativo cliente.  

### <a name="capacity-considerations"></a>Considerações sobre a capacidade
A tabela a seguir inclui alguns dos principais valores a serem informados quando você está projetando uma solução de armazenamento de tabela:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas em uma conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de partições em uma tabela |Limitado apenas pela capacidade da conta de armazenamento. |
| Número de entidades em uma partição |Limitado apenas pela capacidade da conta de armazenamento. |
| Tamanho de uma entidade individual |Até 1 MB, com no máximo 255 `PartitionKey` `RowKey`propriedades `Timestamp`(incluindo o , e ). |
| Tamanho do`PartitionKey` |Uma corda de até 1 KB de tamanho. |
| Tamanho do`RowKey` |Uma corda de até 1 KB de tamanho. |
| Tamanho de uma transação de grupo de entidades |Uma transação pode incluir no máximo 100 entidades, e a carga deve ter menos de 4 MB de tamanho. Uma EGT só pode atualizar uma entidade uma vez. |

Para obter mais informações, consulte [Entendendo o modelo de dados de serviço da tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerações de custo
O armazenamento de tabelas é relativamente barato, mas você deve incluir estimativas de custos tanto para o uso da capacidade quanto para a quantidade de transações como parte de sua avaliação de qualquer solução que use armazenamento de tabela. Em muitos cenários, no entanto, armazenar dados desnormalizados ou duplicados para melhorar o desempenho ou escalabilidade da sua solução é uma abordagem válida a ser tomada. Para obter mais informações sobre preços, confira [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Diretrizes de design da tabela
Essas listas resumem algumas das principais diretrizes que você deve ter em mente ao projetar suas tabelas. Este guia aborda todos eles com mais detalhes mais tarde. Essas diretrizes são diferentes das diretrizes que você normalmente seguiria para o design de banco de dados relacional.  

Projetando seu armazenamento de tabela para ser *lido* eficientemente:

* **Design para consulta em aplicativos com alta taxa de leitura.** Ao projetar suas tabelas, pense nas consultas (especialmente as sensíveis à latência) que você executará antes de pensar em como atualizar suas entidades. Isso normalmente resulta em uma solução eficiente e de alto desempenho.  
* **Especifique tanto `PartitionKey` e `RowKey` em suas consultas.** *Consultas pontuais* como essas são as consultas de armazenamento de tabela mais eficientes.  
* **Considere armazenar cópias duplicadas de entidades.** O armazenamento de mesa é barato, então considere armazenar a mesma entidade várias vezes (com chaves diferentes), para permitir consultas mais eficientes.  
* **Considere a desnormalização de seus dados.** O armazenamento de mesa é barato, então considere desnormalizar seus dados. Por exemplo, armazene entidades resumidas para que consultas a dados agregados só tenham de acessar uma única entidade.  
* **Use valores de chave composta.** As únicas chaves `PartitionKey` `RowKey`que você tem são e. Por exemplo, use valores de chave composta para habilitar caminhos alternativo com chave de acesso para entidades.  
* **Use a projeção de consulta.**  Você pode reduzir a quantidade de dados transferidos pela rede por meio de consultas que selecionam apenas os campos necessários.  

Projetando seu armazenamento de tabela para ser eficiente *em gravação:*  

* **Não crie partições quentes.**  Escolha chaves que permitam que você distribua suas solicitações por várias partições em qualquer momento.  
* **Evite picos no tráfego.** Distribua o tráfego por um período razoável de tempo e evite picos de tráfego.
* **Não crie, necessariamente, uma tabela separada para cada tipo de entidade.**  Quando você precisar de transações atômicas nos tipos de entidade, pode armazenar esses vários tipos de entidade na mesma partição, na mesma tabela.
* **Considere a produtividade máxima que deve ser atingida.** Você deve estar ciente das metas de escalabilidade para armazenamento de tabela e garantir que seu design não os exceda.  

Mais tarde, neste guia, você verá exemplos que colocam todos esses princípios em prática.  

## <a name="design-for-querying"></a>Design para consulta
O armazenamento de mesa pode ser lido intensivamente, escrever intensivamente ou uma mistura dos dois. Esta seção considera projetar para suportar operações de leitura de forma eficiente. Normalmente, um design que dá suporte a operações de leitura com eficiência também é eficiente para operações de gravação. No entanto, existem considerações adicionais ao projetar para apoiar operações de gravação. Estes são discutidos na próxima seção, [Design for data modification](#design-for-data-modification).

Um bom ponto de partida para permitir que você leia dados de forma eficiente é perguntar "Quais consultas meu aplicativo precisará executar para recuperar os dados necessários?"  

> [!NOTE]
> Com o armazenamento de mesa, é importante obter o design correto na frente, porque é difícil e caro alterá-lo mais tarde. Por exemplo, em um banco de dados relacional, muitas vezes é possível resolver problemas de desempenho simplesmente adicionando índices a um banco de dados existente. Esta não é uma opção com armazenamento de mesa.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Como sua `PartitionKey` escolha `RowKey` e afeta o desempenho da consulta
Os exemplos a seguir supõem que o armazenamento de tabelas é armazenar entidades de funcionários com a seguinte estrutura (a maioria dos exemplos omite a `Timestamp` propriedade para clareza):  

| Nome da coluna | Tipo de dados |
| --- | --- |
| `PartitionKey`(Nome do departamento) |String |
| `RowKey`(ID do funcionário) |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |Integer |
| `EmailAddress` |String |

Aqui estão algumas diretrizes gerais para projetar consultas de armazenamento de tabela. A sintaxe do filtro usada nos exemplos a seguir é da API REST de armazenamento de tabela. Para obter mais informações, consulte [Entidades de Consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Uma *consulta de ponto* é a busca mais eficiente a ser usada e é recomendada para consultas ou consultas de alto volume que requerem a menor latência. Tal consulta pode usar os índices para localizar uma entidade `PartitionKey` individual `RowKey` de forma eficiente, especificando os valores e os valores. Por exemplo: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* O segundo melhor é uma *consulta de intervalo*. Ele usa `PartitionKey`o , e filtra `RowKey` em uma gama de valores para retornar mais de uma entidade. O `PartitionKey` valor identifica uma partição `RowKey` específica, e os valores identificam um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* O terceiro melhor é uma *varredura de partição.* Ele usa `PartitionKey`o , e filtra em outra propriedade não-chave e pode retornar mais de uma entidade. O `PartitionKey` valor identifica uma partição específica, e os valores de propriedade selecionam para um subconjunto das entidades nessa partição. Por exemplo: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Uma *varredura de tabela* não `PartitionKey`inclui o , e é ineficiente porque pesquisa todas as partições que compõem sua tabela para quaisquer entidades correspondentes. Ele realiza uma varredura de tabela, independentemente `RowKey`de seu filtro usar ou não o . Por exemplo: `$filter=LastName eq 'Jones'`.  
* Consultas de armazenamento da Tabela Azure que `PartitionKey` `RowKey` retornam várias entidades classificam-nas e ordem. Para evitar recorrer às entidades no `RowKey` cliente, escolha um que defina a ordem de classificação mais comum. Os resultados de consulta retornados pela API da Tabela Azure no Azure Cosmos DB não são classificados por tecla de partição ou chave de linha. Para obter uma lista detalhada das diferenças entre os recursos, confira [Diferenças entre a API de Tabela no Azure Cosmos DB e no Armazenamento de Tabela do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Usar um "**ou**" para `RowKey` especificar um filtro com base em valores resulta em uma varredura de partição, e não é tratado como uma consulta de intervalo. Portanto, evite consultas que utilizem filtros como: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Para exemplos de código do lado do cliente que usam a Biblioteca cliente de armazenamento para executar consultas eficientes, consulte:  

* [Execute uma consulta de pontos usando a Biblioteca cliente de armazenamento](#run-a-point-query-by-using-the-storage-client-library)
* [Recupere várias entidades usando LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projeção do lado do servidor](#server-side-projection)  

Para obter exemplos de código do lado do cliente que pode lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhar com tipos de entidade heterogênea](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Escolha um apropriado`PartitionKey`
Sua escolha `PartitionKey` deve equilibrar a necessidade de permitir o uso de EGTs (para garantir consistência) contra a necessidade de distribuir suas entidades em várias partições (para garantir uma solução escalável).  

Em um extremo, você pode armazenar todas as suas entidades em uma única partição. Mas isso pode limitar a escalabilidade da sua solução e impedir que o armazenamento de tabela sofra solicitações de equilíbrio de carga. No outro extremo, você pode armazenar uma entidade por partição. Isso é altamente escalável e permite que o armazenamento de tabelas faça solicitações de saldo de carga, mas impede que você use transações de grupos de entidades.  

Um `PartitionKey` ideal permite que você use consultas eficientes e tenha partições suficientes para garantir que sua solução seja escalável. Normalmente, você verá que suas entidades terão uma propriedade adequada que distribui suas entidades em partições suficientes.

> [!NOTE]
> Por exemplo, em um sistema que armazena `UserID` informações sobre `PartitionKey`usuários ou funcionários, pode ser uma boa. Você pode ter várias entidades que usam um particular `UserID` como chave de partição. Cada entidade que armazena dados sobre um usuário é agrupada em uma única partição. Essas entidades são acessíveis via EGTs, embora ainda sejam altamente escaláveis.
> 
> 

Existem considerações adicionais `PartitionKey` em sua escolha de que se relacionam com a forma como você insere, atualiza e exclui entidades. Para obter mais informações, consulte [Design para modificação de dados](#design-for-data-modification) mais tarde neste artigo.  

### <a name="optimize-queries-for-table-storage"></a>Otimizar consultas para armazenamento de tabelas
O armazenamento de tabelas indexa `PartitionKey` automaticamente `RowKey` suas entidades usando os valores em um único índice agrupado. Esta é a razão pela qual as consultas pontuais são as mais eficientes de usar. No entanto, não há índices diferentes dos `PartitionKey` `RowKey`do índice agrupado no e .

Muitos designs devem atender aos requisitos para habilitar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de funcionários com base em e-mail, ID do funcionário ou sobrenome. Os padrões a seguir na seção [Padrões de design](#table-design-patterns) de tabela abordam esses tipos de requisitos. Os padrões também descrevem maneiras de contornar o fato de que o armazenamento de tabelas não fornece índices secundários.  

* [Padrão de índice secundário intraparticionário](#intra-partition-secondary-index-pattern): Armazene várias cópias de cada entidade usando valores diferentes `RowKey` (na mesma partição). Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes.  
* [Padrão de índice secundário interpartição :](#inter-partition-secondary-index-pattern)Armazene `RowKey` várias cópias de cada entidade usando valores diferentes em partições separadas ou em tabelas separadas. Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes.  
* [Padrão de entidades de](#index-entities-pattern)índice : Manter entidades de índice para permitir pesquisas eficientes que retornam listas de entidades.  

### <a name="sort-data-in-table-storage"></a>Classificar dados no armazenamento de tabelas

O armazenamento de tabela retorna os resultados da `PartitionKey` consulta `RowKey`classificados em ordem crescente, com base e, em seguida, por .

> [!NOTE]
> Os resultados de consulta retornados pela API da Tabela Azure no Azure Cosmos DB não são classificados por tecla de partição ou chave de linha. Para obter uma lista detalhada das diferenças entre os recursos, confira [Diferenças entre a API de Tabela no Azure Cosmos DB e no Armazenamento de Tabela do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

As chaves no armazenamento de tabela sao valores de string. Para garantir que os valores numéricos classifique corretamente, você deve convertê-los em um comprimento fixo e apad-los com zeros. Por exemplo, se o valor de `RowKey` ID do funcionário que você usa como valor inteiro é um valor inteiro, você deve converter o ID do funcionário **123** para **00000123**. 

Muitos aplicativos têm requisitos para usar dados classificados em ordens diferentes: por exemplo, classificação de funcionários por nome ou por data de ingresso. Os seguintes padrões na seção [Padrões de design de tabela](#table-design-patterns) abordam como alternar ordens de classificação para suas entidades:  

* [Padrão de índice secundário intraparticionário](#intra-partition-secondary-index-pattern): Armazene várias cópias de cada entidade usando valores diferentes `RowKey` (na mesma partição). Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes.  
* [Padrão de índice secundário interpartição :](#inter-partition-secondary-index-pattern)Armazene `RowKey` várias cópias de cada entidade usando valores diferentes em partições separadas em tabelas separadas. Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes.
* [Padrão de cauda de log](#log-tail-pattern): Recupere as entidades *n* adicionadas mais recentemente a uma partição, usando um `RowKey` valor que classifica em ordem de data e hora inversas.  

## <a name="design-for-data-modification"></a>Design para modificação de dados
Esta seção enfoca as considerações de design para otimizar inserções, atualizações e exclusões. Em alguns casos, você precisará avaliar a troca entre projetos que otimizam para consultas contra projetos que otimizam a modificação de dados. Esta avaliação é semelhante ao que você faz em projetos para bases de dados relacionais (embora as técnicas para gerenciar as compensações de design sejam diferentes em um banco de dados relacional). Os [padrões de design da tabela](#table-design-patterns) de seção descrevem alguns padrões de design detalhados para armazenamento de tabela, e destaca algumas dessas trocas. Na prática, você verá que muitos projetos otimizados para consultas de entidades também funcionam bem para modificar entidades.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Otimizar o desempenho das operações de inserção, atualização e exclusão
Para atualizar ou excluir uma entidade, você deve `PartitionKey` ser `RowKey` capaz de identificá-la usando os valores. A este respeito, `PartitionKey` sua `RowKey` escolha e para modificar entidades deve seguir critérios semelhantes à sua escolha para apoiar consultas pontuais. Você quer identificar entidades da forma mais eficiente possível. Você não quer usar uma partição ou varredura de tabela ineficiente `PartitionKey` para `RowKey` localizar uma entidade para descobrir os valores que você precisa para atualizá-la ou excluí-la.  

Os seguintes padrões na seção [Padrões de design](#table-design-patterns) de tabela saem otimizando o desempenho das operações de inserção, atualização e exclusão:  

* [Padrão de exclusão](#high-volume-delete-pattern)de alto volume : Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em sua própria tabela separada. Você exclui as entidades excluindo a tabela.  
* [Padrão da série de dados](#data-series-pattern): Armazene a série completa de dados em uma única entidade para minimizar o número de solicitações que você faz.  
* [Padrão de entidades amplas](#wide-entities-pattern): Use múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de grandes entidades](#large-entities-pattern): Use o armazenamento blob para armazenar grandes valores de propriedade.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Garantir a consistência nas entidades armazenadas
Outro fator-chave que influencia sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência usando transações atômicas. Você só pode usar uma EGT para operar em entidades armazenadas na mesma partição.  

Os seguintes padrões na seção [Padrões de design de tabela](#table-design-patterns) abordam a consistência do gerenciamento:  

* [Padrão de índice secundário intraparticionário](#intra-partition-secondary-index-pattern): Armazene várias cópias de cada entidade usando valores diferentes `RowKey` (na mesma partição). Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes.  
* [Padrão de índice secundário interpartição :](#inter-partition-secondary-index-pattern)Armazene `RowKey` várias cópias de cada entidade usando valores diferentes em partições separadas ou em tabelas separadas. Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes.  
* [Eventualmente, padrão de transações consistentes](#eventually-consistent-transactions-pattern): Habilite um comportamento eventualmente consistente através dos limites de partição ou limites do sistema de armazenamento usando filas do Azure.
* [Padrão de entidades de](#index-entities-pattern)índice : Manter entidades de índice para permitir pesquisas eficientes que retornam listas de entidades.  
* [Padrão de desnormalização](#denormalization-pattern): Combine dados relacionados em uma única entidade, para permitir que você recupere todos os dados necessários com uma consulta de ponto único.  
* [Padrão da série de dados](#data-series-pattern): Armazene a série completa de dados em uma única entidade, para minimizar o número de solicitações que você faz.  

Para obter mais informações, consulte [transações do grupo Entity](#entity-group-transactions) mais tarde neste artigo.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir o design para modificações eficientes facilita consultas eficientes
Em muitos casos, um design para consultas eficientes resulta em modificações eficientes, mas você sempre deve avaliar se esse é o caso para seu cenário específico. Alguns dos padrões na seção [Padrões](#table-design-patterns) de design de tabela avaliam explicitamente as trocas entre consultas e entidades modificadoras, e você deve sempre levar em conta o número de cada tipo de operação.  

Os seguintes padrões na seção [Padrões de design](#table-design-patterns) de tabela abordam as trocas entre projetar consultas eficientes e projetar para uma modificação eficiente de dados:  

* [Padrão de chave](#compound-key-pattern) `RowKey` composto : Use valores compostos para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  
* [Padrão de cauda de log](#log-tail-pattern): Recupere as entidades *n* adicionadas mais recentemente a uma partição, usando um `RowKey` valor que classifica em ordem de data e hora inversas.  

## <a name="encrypt-table-data"></a>Criptografar dados de tabela
A biblioteca cliente .NET Azure Storage suporta criptografia de propriedades de entity de string para inserir e substituir operações. As strings criptografadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta em strings após a descriptografia.    

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades que devem ser criptografadas. Especifique um `EncryptProperty` atributo (para `TableEntity`entidades POCO que derivam), ou especifique um resolver criptografia nas opções de solicitação. Um resolver criptografia é um delegado que pega uma chave de partição, chave de linha e nome da propriedade e retorna um Boolean o que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca do cliente usa essas informações para decidir se uma propriedade deve ser criptografada enquanto escreve para o fio. O representante também oferece a possibilidade de lógica em torno de como as propriedades são criptografadas. (Por exemplo, se X, então criptografar a propriedade A; de outra forma, criptografar as propriedades A e B.) Não é necessário fornecer essas informações durante a leitura ou consulta de entidades.

A merge não é suportada no momento. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente a fusão das novas propriedades e a atualização dos metadados resultarão em perda de dados. A fusão ou requer fazer chamadas de serviço extras para ler a entidade pré-existente do serviço ou usar uma nova chave por propriedade. Nenhum deles é adequado por razões de desempenho.     

Para obter informações sobre como criptografar dados da tabela, consulte [criptografia do lado do cliente e do Azure Key Vault para o Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Modelo de relacionamento
A criação de modelos de domínio é uma etapa importante na criação de sistemas complexos. Normalmente, você usa o processo de modelagem para identificar entidades e as relações entre elas, como uma forma de entender o domínio do negócio e informar o design do seu sistema. Esta seção se concentra em como você pode traduzir alguns dos tipos de relacionamento comuns encontrados em modelos de domínio para projetos de armazenamento de tabela. O processo de mapeamento de um modelo de dados lógicos para um modelo de dados físico baseado em NoSQL é diferente do usado ao projetar um banco de dados relacional. O design de bancos de dados relacionais normalmente assume um processo de normalização de dados otimizado para minimizar a redundância. Tal projeto também assume uma capacidade de consulta declarativa que abstrai a implementação de como o banco de dados funciona.  

### <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrerem com frequência: por exemplo, um departamento tem muitos funcionários. Existem várias maneiras de implementar relacionamentos de um a muitos no armazenamento de tabelas, cada um com prós e contras que podem ser relevantes para o cenário específico.  

Considere o exemplo de uma grande multinacional com dezenas de milhares de departamentos e entidades de funcionários. Cada departamento tem muitos funcionários e cada funcionário está associado a um departamento específico. Uma abordagem é armazenar departamentos separados e entidades de funcionários, como o seguinte:  

![Gráfico mostrando uma entidade de departamento e uma entidade de funcionários][1]

Este exemplo mostra uma relação implícita entre os tipos, `PartitionKey` com base no valor. Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e suas entidades de funcionário relacionadas na mesma partição. Você pode optar por usar diferentes partições, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidades.  

Uma abordagem alternativa é desnormalizar seus dados e armazenar apenas entidades de funcionários com dados dedepartamentos desnormalizados, como mostrado no exemplo a seguir. Neste cenário em particular, essa abordagem desnormalizada pode não ser a melhor se você tiver um requisito para ser capaz de alterar os detalhes de um gerente de departamento. Para fazer isso, você precisaria atualizar todos os funcionários do departamento.  

![Gráfico da entidade dos funcionários][2]

Para saber mais, confira [Padrão de desnormalização](#denormalization-pattern) mais adiante neste guia.  

A tabela a seguir resume os prós e contras de cada uma das abordagens para armazenar funcionários e entidades de departamento que tenham uma relação de um a muitos. Você também deve considerar a frequência com que espera realizar várias operações. Pode ser aceitável ter um design que inclua uma operação cara se essa operação só acontece com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Vantagens</th>
<th>Desvantagens</th>
</tr>
<tr>
<td>Separar tipos de entidade, mesma partição, mesma tabela</td>
<td>
<ul>
<li>Você pode atualizar uma entidade de departamento com uma única operação.</li>
<li>Você pode usar uma EGT para manter a consistência se precisar modificar uma entidade de departamento sempre que atualizar/inserir/excluir uma entidade de funcionário. Por exemplo, se você mantiver uma contagem de funcionários do departamento para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Você pode precisar recuperar um funcionário e uma entidade de departamento para algumas atividades do cliente.</li>
<li>As operações de armazenamento ocorrem na mesma partição. Em grandes volumes de transações, isso pode resultar em um hotspot.</li>
<li>Você não pode mover um funcionário para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separada, diferentes partições ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Você pode atualizar uma entidade de departamento ou funcionário com uma única operação.</li>
<li>Em grandes volumes de transações, isso pode ajudar a espalhar a carga por mais partições.</li>
</ul>
</td>
<td>
<ul>
<li>Você pode precisar recuperar um funcionário e uma entidade de departamento para algumas atividades do cliente.</li>
<li>Você não pode usar EGTs para manter a consistência quando atualiza/insere/exclui um funcionário e atualiza um departamento. Por exemplo, a atualização de uma contagem de funcionários em uma entidade de departamento.</li>
<li>Você não pode mover um funcionário para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Desnormalizar em um único tipo de entidade</td>
<td>
<ul>
<li>Você pode recuperar todas as informações necessárias com uma única solicitação.</li>
</ul>
</td>
<td>
<ul>
<li>Pode ser caro manter a consistência se você precisar atualizar as informações do departamento (isso exigiria que você atualizasse todos os funcionários em um departamento).</li>
</ul>
</td>
</tr>
</table>

A forma como você escolhe entre essas opções, e qual dos prós e contras são mais significativos, depende de seus cenários específicos de aplicação. Por exemplo, com que frequência você modifica entidades de departamento? Todas as suas consultas de funcionários precisam de informações adicionais do departamento? Quão perto você está dos limites de escalabilidade em suas partições ou em sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um-para-um
Os modelos de domínio podem incluir relações um-para-um entre entidades. Se você precisar implementar um relacionamento um-para-um no armazenamento de tabela, você também deve escolher como vincular as duas entidades relacionadas quando você precisar recuperá-las. Este link pode ser implícito, baseado em uma convenção nos valores-chave, ou `PartitionKey` `RowKey` explícito, armazenando um link na forma e valores em cada entidade para sua entidade relacionada. Para ver uma discussão sobre a conveniência ou não de armazenar as entidades relacionadas na mesma partição, confira a seção [Relações de um-para-muitos](#one-to-many-relationships).  

Existem também considerações de implementação que podem levá-lo a implementar relacionamentos um-para-um no armazenamento de tabelas:  

* Manuseio de grandes entidades (para obter mais informações, consulte [o padrão de grandes entidades](#large-entities-pattern)).  
* Implementando controles de acesso (para obter mais informações, consulte [Controle de acesso com assinaturas de acesso compartilhadas](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Unindo o cliente
Embora existam maneiras de modelar relacionamentos no armazenamento de tabela, não se esqueça que as duas principais razões para usar o armazenamento de tabela são escalabilidade e desempenho. Se você descobrir que está modelando muitos relacionamentos que comprometem o desempenho e a escalabilidade da sua solução, você deve se perguntar se é necessário construir todas as relações de dados em seu design de mesa. Você pode ser capaz de simplificar o design e melhorar a escalabilidade e o desempenho da sua solução, se você permitir que seu aplicativo cliente realize quaisquer solicitações necessárias.  

Por exemplo, se você tiver pequenas tabelas que contenham dados que não mudam com frequência, você pode recuperar esses dados uma vez e cacheá-los no cliente. Isso pode evitar idas e voltas repetidas para recuperar os mesmos dados. Nos exemplos que vimos neste guia, o conjunto de departamentos em uma pequena organização provavelmente será pequeno e mudará com pouca frequência. Isso torna-se um bom candidato para dados que um aplicativo cliente pode baixar uma vez e cache como dados de pesquisa.  

### <a name="inheritance-relationships"></a>Relações de herança
Se o seu aplicativo cliente usar um conjunto de classes que fazem parte de uma relação de herança para representar entidades empresariais, você pode facilmente persistir essas entidades no armazenamento de tabelas. Por exemplo, você pode ter o seguinte conjunto de `Person` classes definidas em seu aplicativo cliente, onde é uma classe abstrata.

![Diagrama das relações de herança][3]

Você pode persistir instâncias das duas classes concretas no armazenamento de tabela usando uma única `Person` tabela. Use entidades que se pareçam com as seguintes:  

![Gráfico mostrando a entidade do cliente e a entidade dos funcionários][4]

Para obter mais informações sobre como trabalhar com vários tipos de entidades na mesma tabela no código do cliente, consulte [Trabalhar com tipos de entidades heterogêneas](#work-with-heterogeneous-entity-types) mais tarde neste guia. Isso fornece exemplos de como reconhecer o tipo de entidade no código do cliente.  

## <a name="table-design-patterns"></a>Padrões de design de tabela
Em seções anteriores, você aprendeu sobre como otimizar seu design de tabela tanto para recuperar dados de entidades usando consultas quanto para inserir, atualizar e excluir dados da entidade. Esta seção descreve alguns padrões apropriados para uso com armazenamento de tabela. Além disso, você verá como você pode praticamente abordar algumas das questões e trade-offs levantadas anteriormente neste guia. O diagrama a seguir resume as relações entre os diferentes padrões:  

![Diagrama de padrões de design de tabela][5]

O mapa de padrões destaca algumas relações entre padrões (azul) e anti-padrões (laranja) que estão documentados neste guia. Certamente há muitos outros padrões que vale a pena considerar. Por exemplo, um dos principais cenários para o armazenamento de tabelaé usar o padrão de [exibição materializado](https://msdn.microsoft.com/library/azure/dn589782.aspx) a partir do padrão de segregação de responsabilidade da consulta de [comando.](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão de índice secundário intrapartição
Armazene várias cópias `RowKey` de cada entidade usando valores diferentes (na mesma partição). Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes. As atualizações entre cópias podem ser mantidas consistentes usando EGTs.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de tabelas indexa `PartitionKey` `RowKey` automaticamente as entidades usando os valores. Isso permite que um aplicativo cliente recupere uma entidade de forma eficiente usando esses valores. Por exemplo, usando a seguinte estrutura de tabela, um aplicativo cliente pode usar uma consulta de ponto `PartitionKey` para `RowKey` recuperar uma entidade de funcionário individual usando o nome do departamento e o ID do funcionário (e valores). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.

![Gráfico da entidade dos funcionários][6]

Se você também quiser encontrar uma entidade de funcionário com base no valor de outra propriedade, como endereço de e-mail, você deve usar uma varredura de partição menos eficiente para encontrar uma correspondência. Isso porque o armazenamento de tabelas não fornece índices secundários. Além disso, não há opção de solicitar uma lista de funcionários classificados em uma ordem diferente da `RowKey` ordem.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias `RowKey` cópias de cada entidade, com cada cópia usando um valor diferente. Se você armazenar uma entidade com as seguintes estruturas, você pode recuperar eficientemente as entidades dos funcionários com base no endereço de e-mail ou no ID do funcionário. Os valores `RowKey` `empid_`de `email_` prefixo para , e permitem que você consulte um único funcionário, ou uma série de funcionários, usando uma série de endereços de e-mail ou IDs de funcionários.  

![Gráfico mostrando a entidade dos funcionários com valores variados da RowKey][7]

Os dois critérios de filtro a seguir (um olhando para cima pelo ID do funcionário e outro procurando por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Se você consultar uma série de entidades de funcionários, você pode especificar um intervalo classificado na ordem de ID do funcionário ou um intervalo classificado no pedido de endereço de e-mail. Consulta para entidades com o `RowKey`prefixo apropriado no .  

* Para encontrar todos os funcionários do departamento de Vendas com um ID de funcionário na faixa 000100 a 000199, use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000100') e (RowKey le 'empid_000199')  
* Para encontrar todos os funcionários do departamento de Vendas com um endereço de e-mail começando com a letra "a", use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'email_a') e (RowKey lt 'email_b')  
  
A sintaxe do filtro usada nos exemplos anteriores é da API REST de armazenamento de tabela. Para obter mais informações, consulte [Entidades de Consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* O armazenamento de mesa é relativamente barato de usar, portanto, a sobrecarga de custos de armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, você deve sempre avaliar o custo do seu design com base nos requisitos de armazenamento antecipados e apenas adicionar entidades duplicadas para suportar as consultas que seu aplicativo cliente executará.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, certifique-se de que você não exceda as metas de escalabilidade para uma partição individual.  
* Você pode manter suas entidades duplicadas consistentes entre si usando EGTs para atualizar as duas cópias da entidade atomicamente. Isso significa que você deve armazenar todas as cópias de uma entidade na mesma partição. Para obter mais informações, consulte [Use transações de grupo de entidades](#entity-group-transactions).  
* O valor utilizado `RowKey` para o deve ser único para cada entidade. Considere o uso de valores de chave composta.  
* O preenchimento de valores `RowKey` numéricos no (por exemplo, o ID do funcionário 000223) permite a classificação e filtragem corretas com base nos limites superior e inferior.  
* Você não precisa necessariamente duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procurarem as entidades `RowKey` usando o endereço de e-mail na nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:

  ![Gráfico da entidade dos funcionários][8]

* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para procurar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando:

- Seu aplicativo cliente precisa recuperar entidades usando uma variedade de chaves diferentes.
- Seu cliente precisa recuperar entidades em diferentes ordens de classificação.
- Você pode identificar cada entidade usando uma variedade de valores únicos.

No entanto, certifique-se de que você não exceda os limites de escalabilidade `RowKey` de partição quando estiver realizando revisões de entidade usando os diferentes valores.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* [Transações de grupos de entidades](#entity-group-transactions)
* [Trabalhar com tipos de entidade heterogênea](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice secundário entre partições
Armazene várias cópias `RowKey` de cada entidade usando valores diferentes em partições separadas ou em tabelas separadas. Isso permite maquiagens rápidas e eficientes e `RowKey` ordens de classificação alternativas usando valores diferentes.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de tabelas indexa `PartitionKey` `RowKey` automaticamente as entidades usando os valores. Isso permite que um aplicativo cliente recupere uma entidade de forma eficiente usando esses valores. Por exemplo, usando a seguinte estrutura de tabela, um aplicativo cliente pode usar uma consulta de ponto `PartitionKey` para `RowKey` recuperar uma entidade de funcionário individual usando o nome do departamento e o ID do funcionário (e valores). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.  

![Gráfico da entidade dos funcionários][9]

Se você quiser ser capaz de encontrar uma entidade funcionário com base no valor de outra propriedade, como o endereço de email, deve usar uma verificação de partição menos eficiente para localizar uma correspondência. Isso porque o armazenamento de tabelas não fornece índices secundários. Além disso, não há opção de solicitar uma lista de funcionários classificados em uma ordem diferente da `RowKey` ordem.  

Você está antecipando um alto volume de transações contra essas entidades e quer minimizar o risco da taxa de armazenamento de tabela limitar seu cliente.  

#### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias `PartitionKey` `RowKey` cópias de cada entidade, com cada cópia usando valores e valores diferentes. Se você armazenar uma entidade com as seguintes estruturas, você pode recuperar eficientemente as entidades dos funcionários com base no endereço de e-mail ou no ID do funcionário. O prefixo `PartitionKey` `empid_`valoriza `email_` para , e permite identificar qual índice você deseja usar para uma consulta.  

![Gráfico mostrando entidade de empregados com índice primário e entidade de empregados com índice secundário][10]

Os dois critérios de filtro a seguir (um olhando para cima pelo ID do funcionário e outro procurando por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se você consultar uma série de entidades de funcionários, você pode especificar um intervalo classificado na ordem de ID do funcionário ou um intervalo classificado no pedido de endereço de e-mail. Consulta para entidades com o `RowKey`prefixo apropriado no .  

* Para encontrar todos os funcionários do departamento de Vendas com um ID de funcionário na faixa **000100** a **000199**, classificado em ordem de id de funcionários, use: $filter=(PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Para encontrar todos os funcionários do departamento de Vendas com um endereço de e-mail que começa com "a", classificado na ordem de endereço de e-mail, use: $filter=(PartitionKey eq 'email_Sales') e (RowKey ge 'a') e (RowKey lt 'b')  

Observe que a sintaxe do filtro usada nos exemplos anteriores é da API REST de armazenamento de tabela. Para obter mais informações, consulte [Entidades de Consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você pode manter suas entidades duplicadas eventualmente consistentes entre si usando o [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de mesa é relativamente barato de usar, portanto, a sobrecarga de custos de armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, sempre avalie o custo do seu design com base nos requisitos de armazenamento antecipados e adicione apenas entidades duplicadas para suportar as consultas que seu aplicativo cliente executará.  
* O valor utilizado `RowKey` para o deve ser único para cada entidade. Considere o uso de valores de chave composta.  
* O preenchimento de valores `RowKey` numéricos no (por exemplo, o ID do funcionário 000223) permite a classificação e filtragem corretas com base nos limites superior e inferior.  
* Você não precisa necessariamente duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procurarem as entidades `RowKey` usando o endereço de e-mail na nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:
  
  ![Gráfico mostrando entidade de funcionários com índice secundário][11]
* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para procurar os dados necessários no índice principal.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando:

- Seu aplicativo cliente precisa recuperar entidades usando uma variedade de chaves diferentes.
- Seu cliente precisa recuperar entidades em diferentes ordens de classificação.
- Você pode identificar cada entidade usando uma variedade de valores únicos.

Use este padrão quando quiser evitar exceder os limites de escalabilidade da partição `RowKey` quando estiver realizando buscas de entidade usando os diferentes valores.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intrapartição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistentes
Habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e problema
EGTs habilitam transações atômicas entre várias entidades que compartilham a mesma chave de partição. Por razões de desempenho e escalabilidade, você pode decidir armazenar entidades que têm requisitos de consistência em partições separadas ou em um sistema de armazenamento separado. Em tal cenário, você não pode usar EGTs para manter a consistência. Por exemplo, você pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, em tabelas diferentes ou em diferentes contas de armazenamento.  
* Uma entidade armazenada no armazenamento de mesa e uma bolha armazenada no armazenamento Blob.  
* Uma entidade armazenada no armazenamento de tabela e um arquivo em um sistema de arquivos.  
* Uma entidade armazenada no armazenamento de tabelas, mas indexada usando o Azure Cognitive Search.  

#### <a name="solution"></a>Solução
Ao usar as filas do Azure, você pode implementar uma solução que fornece consistência eventual em duas ou mais partições ou sistemas de armazenamento.

Para ilustrar essa abordagem, suponha que você tenha um requisito para ser capaz de arquivar entidades de ex-funcionários. As entidades de ex-funcionários raramente são questionadas e devem ser excluídas de qualquer atividade que tinfete com os atuais funcionários. Para implementar esse requisito, você armazena funcionários ativos na tabela **Atual** e ex-funcionários na tabela **Arquivo.** O arquivamento de um funcionário exige que você exclua a entidade da tabela **Corrente** e adicione a entidade à tabela **Arquivo.**

Mas você não pode usar um EGT para realizar essas duas operações. Para evitar o risco de que uma falha faça com que uma entidade seja exibida nas tabelas ou em nenhuma, a operação de arquivamento deve ser eventualmente consistente. O diagrama de sequência a seguir descreve as etapas nesta operação.  

![Diagrama de solução para consistência eventual][12]

Um cliente inicia a operação de arquivamento colocando uma mensagem em uma fila do Azure (neste exemplo, para arquivar #456 de funcionários). Uma função de trabalho controla a fila para novas mensagens; quando encontra uma, lê a mensagem e deixa uma cópia oculta na fila. A função de trabalho, em seguida, busca uma cópia da entidade na tabela **Atual**, insere uma cópia na tabela **Arquivo morto** e exclui o original da tabela **Atual**. Finalmente, se não houve erros das etapas anteriores, a função de trabalho exclui a mensagem oculta da fila.  

Neste exemplo, a etapa 4 do diagrama insere o funcionário na tabela **Arquivo.** Ele pode adicionar o funcionário a uma bolha no armazenamento Blob ou a um arquivo em um sistema de arquivos.  

#### <a name="recover-from-failures"></a>Recuperar-se de falhas
É importante que as operações nas etapas 4-5 no diagrama sejam *idempotentes* caso a função do trabalhador precise reiniciar a operação de arquivamento. Se você estiver usando o armazenamento de tabela, para a etapa 4 você deve usar uma operação "inserir ou substituir"; para a etapa 5, você deve usar uma operação "excluir se existir" na biblioteca do cliente que você está usando. Se você estiver usando outro sistema de armazenamento, você deve usar uma operação idempotente apropriada.  

Se a função do trabalhador nunca completar a etapa 6 no diagrama, então, após um intervalo, a mensagem reaparece na fila pronta para a função do trabalhador tentar reprocessá-la. A função do trabalhador pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizar como uma mensagem de "veneno" para investigação, enviando-a para uma fila separada. Para obter mais informações sobre como ler mensagens de fila e verificar a contagem de [filas,](https://msdn.microsoft.com/library/azure/dd179474.aspx)consulte Obter mensagens .  

Alguns erros do armazenamento de tabela e do armazenamento na fila são erros transitórios, e seu aplicativo cliente deve incluir lógica de repetição adequada para manuseá-los.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Esta solução não prevê o isolamento da transação. Por exemplo, um cliente pode ler as tabelas **Corrente** e **Arquivamento** quando a função do trabalhador estava entre as etapas 4-5 no diagrama e ver uma exibição inconsistente dos dados. Os dados serão consistentes eventualmente.  
* Você deve ter certeza de que as etapas 4-5 são impotentes para garantir uma eventual consistência.  
* Você pode dimensionar a solução usando várias filas e instâncias de função de trabalho.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando quiser garantir a consistência eventual entre entidades que existem nas tabelas ou partições diferentes. Você pode estender esse padrão para garantir uma eventual consistência para as operações em armazenamento table e armazenamento Blob, e outras fontes de dados não-Azure Storage, como um banco de dados ou o sistema de arquivos.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)  
* [Mesclar ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento de transações for importante para sua solução, considere redesenhar suas tabelas para permitir que você use EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades de índice
Mantenha entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  

#### <a name="context-and-problem"></a>Contexto e problema
O armazenamento de tabelas indexa `PartitionKey` `RowKey` automaticamente as entidades usando os valores. Isso permite que um aplicativo cliente recupere uma entidade de forma eficiente usando uma consulta de ponto. Por exemplo, usando a seguinte estrutura de tabela, um aplicativo cliente pode recuperar eficientemente uma `PartitionKey` `RowKey`entidade de funcionário individual usando o nome do departamento e o ID do funcionário (o e ).  

![Gráfico da entidade dos funcionários][13]

Se você também quiser ser capaz de recuperar uma lista de entidades de funcionários com base no valor de outra propriedade não única, como sobrenome, você deve usar uma varredura de partição menos eficiente. Esta varredura encontra correspondências, em vez de usar um índice para procurá-los diretamente. Isso porque o armazenamento de tabelas não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para habilitar a procuração pelo sobrenome com a estrutura da entidade anterior, você deve manter listas de IDs de funcionários. Se você quiser recuperar as entidades de funcionários com um sobrenome específico, como Jones, você deve primeiro localizar a lista de ids de funcionários para funcionários com Jones como seu sobrenome, e, em seguida, recuperar essas entidades de funcionários. Existem três opções principais para armazenar as listas de IDs de funcionários:  

* Use o armazenamento Blob.  
* Crie entidades de índice na mesma partição que as entidades do funcionário.  
* Crie entidades de índice em uma partição ou tabela separada.  

Opção 1: Use o armazenamento Blob  

Crie uma bolha para cada sobrenome único, e em cada `PartitionKey` blob `RowKey` armazenar uma lista dos valores (departamento) e (iD do funcionário) para funcionários que têm esse sobrenome. Quando você adicionar ou excluir um funcionário, certifique-se de que o conteúdo da bolha relevante seja eventualmente consistente com as entidades dos funcionários.  

Opção 2: Criar entidades de índice na mesma partição  

Use entidades de índice que armazenam os seguintes dados:  

![Gráfico mostrando a entidade dos funcionários, com string contendo uma lista de Identidades de funcionários com o mesmo sobrenome][14]

A `EmployeeIDs` propriedade contém uma lista de ids de funcionários `RowKey`para funcionários com o sobrenome armazenado no .  

As etapas a seguir descrevem o processo que você deve seguir quando estiver adicionando um novo funcionário. Neste exemplo, estamos adicionando um funcionário com ID 000152 e sobrenome Jones no departamento de Vendas:  

1. Recupere a entidade `PartitionKey` do índice com `RowKey` um valor "Vendas", e o valor "Jones". Salve o ETag dessa entidade para usar na etapa 2.  
2. Criar uma transação de grupo de entidades (ou seja,`PartitionKey` uma operação `RowKey` em lote) que insira a nova entidade`PartitionKey` de funcionários `RowKey` (valor "Vendas" e valor "000152"), e atualize a entidade do índice (valor "Vendas" e valor "Jones"). O EGT faz isso adicionando o novo ID do funcionário à lista no campo EmployeeIDs. Para obter mais informações sobre EGTs, consulte [Transações do grupo Entity](#entity-group-transactions).  
3. Se o EGT falhar por causa de um erro de concorrência otimista (ou seja, outra pessoa modificou a entidade do índice), então você precisa começar de novo na etapa 1.  

Você pode usar uma abordagem semelhante para excluir um funcionário se estiver usando a segunda opção. Mudar o sobrenome de um funcionário é um pouco mais complexo, pois você precisa executar um EGT que atualize três entidades: a entidade do empregado, a entidade de índice para o sobrenome antigo e a entidade de índice para o novo sobrenome. Você deve recuperar cada entidade antes de fazer quaisquer alterações, a fim de recuperar os valores do ETag que você pode usar para executar as atualizações usando uma concorrência otimista.  

As etapas a seguir descrevem o processo que você deve seguir quando você precisa procurar todos os funcionários com um sobrenome específico em um departamento. Neste exemplo, estamos procurando todos os funcionários com sobrenome Jones no departamento de Vendas:  

1. Recupere a entidade `PartitionKey` do índice com `RowKey` um valor "Vendas", e o valor "Jones".  
2. Analise a lista de ids de funcionários no `EmployeeIDs` campo.  
3. Se você precisar de informações adicionais sobre cada um desses funcionários (como `PartitionKey` seus endereços `RowKey` de e-mail), recupere cada uma das entidades de funcionários usando o valor "Vendas", e valores da lista de funcionários que você obteve na etapa 2.  

Opção 3: Criar entidades de índice em uma partição ou tabela separada  

Para esta opção, use entidades de índice que armazenem os seguintes dados:  

![Gráfico mostrando a entidade dos funcionários, com string contendo uma lista de Identidades de funcionários com o mesmo sobrenome][15]

A `EmployeeIDs` propriedade contém uma lista de ids de funcionários `RowKey`para funcionários com o sobrenome armazenado no .  

Você não pode usar EGTs para manter a consistência, porque as entidades de índice estão em uma partição separada das entidades de funcionários. Certifique-se de que as entidades de índice são eventualmente consistentes com as entidades de funcionários.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Essa solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma `RowKey` para consultar as entidades de índice para obter a lista de valores e, em seguida, consultas para recuperar cada entidade na lista.  
* Como uma entidade individual tem um tamanho máximo de 1 MB, a opção 2 e a opção 3 na solução assumem que a lista de IDs de funcionários para qualquer sobrenome em particular nunca é superior a 1 MB. Se a lista de IDs de funcionários provavelmente tiver mais de 1 MB de tamanho, use a opção 1 e armazene os dados do índice no armazenamento Blob.  
* Se você usar a opção 2 (usando EGTs para lidar com a adição e exclusão de funcionários e alterar o sobrenome de um funcionário), você deve avaliar se o volume de transações abordará os limites de escalabilidade em uma determinada partição. Se este for o caso, você deve considerar uma solução eventualmente consistente (opção 1 ou opção 3). Essas filas de uso para lidar com as solicitações de atualização e permitem que você armazene suas entidades de índice em uma partição separada das entidades de funcionários.  
* A opção 2 nesta solução pressupõe que você deseja procurar pelo sobrenome dentro de um departamento. Por exemplo, você quer recuperar uma lista de funcionários com um sobrenome Jones no departamento de Vendas. Se você quiser ser capaz de procurar todos os funcionários com um sobrenome Jones em toda a organização, use a opção 1 ou a opção 3.
* Você pode implementar uma solução baseada em fila que ofereça consistência eventual. Para obter mais detalhes, consulte o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use este padrão quando quiser procurar um conjunto de entidades que compartilhem um valor de propriedade comum, como todos os funcionários com o sobrenome Jones.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados juntos em uma única entidade para que você possa recuperar todos os dados que precisa com uma ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, você normalmente normaliza dados para remover a duplicação que ocorre quando as consultas recuperam dados de várias tabelas. Se você normalizar dados em tabelas do Azure, terá de ir e voltar várias vezes do cliente ao servidor para recuperar os dados relacionados. Por exemplo, com a seguinte estrutura de tabela, você precisa de duas ida e volta para recuperar os detalhes de um departamento. Uma viagem busca a entidade do departamento que inclui o ID do gerente, e a segunda viagem busca os detalhes do gerente em uma entidade de funcionários.  

![Gráfico de entidade de departamento e entidade de funcionários][16]

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalize os dados e mantenha uma cópia dos detalhes do gerente na entidade de departamento. Por exemplo:   

![Gráfico de entidade de departamento desnormalizada e combinada][17]

Com as entidades de departamento armazenadas com essas propriedades, agora você pode recuperar todos os detalhes necessários sobre um departamento usando uma consulta de ponto.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Existe alguma sobrecarga de custo associada ao armazenar alguns dados duas vezes. O benefício de desempenho resultante de menos solicitações ao armazenamento de tabela normalmente supera o aumento marginal dos custos de armazenamento. Além disso, esse custo é parcialmente compensado por uma redução no número de transações que você precisa para buscar os detalhes de um departamento.  
* Você deve manter a consistência das duas entidades que armazenam informações sobre os gerentes. Você pode lidar com o problema de consistência usando EGTs para atualizar várias entidades em uma única transação atômica. Neste caso, a entidade do departamento e a entidade dos funcionários do gerente do departamento são armazenadas na mesma partição.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar pesquisar informações relacionadas com frequência. Esse padrão reduz o número de consultas que o cliente deve fazer para recuperar os dados necessários.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composta
Use `RowKey` valores compostos para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, é natural usar as junções em consultas para devolver dados relacionados ao cliente em uma única consulta. Por exemplo, você pode usar a ID do funcionário para pesquisar uma lista de entidades relacionadas que contém o desempenho e analisar os dados desse funcionário.  

Suponha que você está armazenando entidades de funcionários no armazenamento de tabelausando a seguinte estrutura:  

![Gráfico da entidade dos funcionários][18]

Você também precisa armazenar dados históricos relacionados a avaliações e desempenho para cada ano que o funcionário trabalhou para sua organização, e você precisa ser capaz de acessar essas informações por ano. Uma opção é criar outra tabela que armazena entidades com a seguinte estrutura:  

![Gráfico da entidade de revisão de funcionários][19]

Com essa abordagem, você pode decidir duplicar algumas informações (como nome e sobrenome) na nova entidade, para permitir que você recupere seus dados com uma única solicitação. No entanto, você não pode manter uma consistência forte porque você não pode usar um EGT para atualizar as duas entidades atomicamente.  

#### <a name="solution"></a>Solução
Armazene um novo tipo de entidade em sua tabela original usando entidades com a seguinte estrutura:  

![Gráfico da entidade dos funcionários com chave composta][20]

Observe como `RowKey` o é agora uma chave composta, composta pelo ID do funcionário e o ano dos dados de revisão. Isso permite que você recupere o desempenho do funcionário e revise os dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como você pode recuperar todos os dados de revisão de um funcionário específico (por exemplo, um funcionário 000123 no departamento de Vendas):  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você deve usar um caractere separador adequado que `RowKey` facilite a análise do valor: por exemplo, **000123_2012**.  
* Você também está armazenando esta entidade na mesma partição que outras entidades que contêm dados relacionados para o mesmo funcionário. Isso significa que você pode usar EGTs para manter uma consistência forte.
* Você deve considerar com que freqüência você consultará os dados para determinar se esse padrão é apropriado. Por exemplo, se você acessar os dados de revisão com pouca frequência e os principais dados dos funcionários com frequência, você deve mantê-los como entidades separadas.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar armazenar uma ou mais entidades relacionadas que você consulta com frequência.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)  
* [Trabalhar com tipos de entidade heterogênea](#work-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de rastro do log
Recupere as entidades *n* adicionadas mais `RowKey` recentemente a uma partição usando um valor que classifica em ordem de data e hora inversas.  

> [!NOTE]
> Os resultados de consulta retornados pela API da Tabela Azure no Azure Cosmos DB não são classificados por tecla de partição ou chave de linha. Assim, embora este padrão seja adequado para armazenamento de mesa, ele não é adequado para o Azure Cosmos DB. Para obter uma lista detalhada de diferenças de recursos, consulte [diferenças entre a API da tabela no Azure Cosmos DB e o Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é conseguir recuperar as entidades criadas mais recentemente, por exemplo, as dez solicitações de despesas mais recentes enviadas por um funcionário. As consultas de `$top` tabela suportam uma operação de consulta para retornar as primeiras *entidades n* de um conjunto. Não há nenhuma operação de consulta equivalente para retornar as últimas *entidades n* em um conjunto.  

#### <a name="solution"></a>Solução
Armazene as entidades usando um `RowKey` que naturalmente classifica em ordem de data/hora inversa, de modo que a entrada mais recente é sempre a primeira na tabela.  

Por exemplo, para recuperar os dez reembolsos de despesa mais recentes enviadas por um funcionário, você pode usar um valor de escala inversa derivado de data/hora atual. A seguinte amostra de código C# mostra uma maneira de criar `RowKey` um valor adequado de "carrapatos invertidos" para uma espécie de mais recente ao mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Você pode voltar ao valor de data/hora usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta a tabela tem esta aparência:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você deve pad o valor do tique-taque reverso com zeros de liderança, para garantir que o valor da seqüência classifica como esperado.  
* Você deve estar ciente das metas de escalabilidade no nível de uma partição. Tenha cuidado para não criar partições de ponto quente.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar acessar entidades em ordem de data/hora inversa ou quando precisar acessar as entidades adicionadas mais recentemente.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Antipadrão prefixar/acrescentar](#prepend-append-anti-pattern)  
* [Recuperar entidades](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de exclusão de alto volume
Permitir a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em sua própria tabela separada. Você exclui as entidades excluindo a tabela.  

#### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos excluem dados antigos que não precisam mais estar disponíveis para um aplicativo cliente, ou que o aplicativo tenha arquivado em outro meio de armazenamento. Você normalmente identifica esses dados por uma data. Por exemplo, você tem o requisito para excluir registros de todas as solicitações de login com mais de 60 dias de idade.  

Um projeto possível é usar a data e a `RowKey`hora da solicitação de login no :  

![Gráfico da entidade de tentativa de login][21]

Essa abordagem evita hotspots de partição, pois o aplicativo pode inserir e excluir entidades de login para cada usuário em uma partição separada. No entanto, essa abordagem pode ser cara e demorada se você tiver um grande número de entidades. Primeiro, você precisa realizar uma varredura de tabela para identificar todas as entidades a serem excluídas, e então você deve excluir cada entidade antiga. Você pode reduzir o número de viagens de ida e volta ao servidor, necessário para excluir as entidades antigas por envio em lote de várias solicitações de exclusão para as EGTs.  

#### <a name="solution"></a>Solução
Use uma tabela separada para cada dia de tentativas de entrada. Você pode usar o projeto de entidade anterior para evitar hotspots quando estiver inserindo entidades. Excluir entidades antigas agora é simplesmente uma questão de excluir uma tabela todos os dias (uma única operação de armazenamento), em vez de encontrar e excluir centenas e milhares de entidades individuais de login todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Seu design suporta outras maneiras de seu aplicativo usar os dados, como procurar entidades específicas, vincular-se a outros dados ou gerar informações agregadas?  
* O design evita pontos de acesso quando você está inserindo novas entidades?  
* Espere um atraso, se você quiser reutilizar o mesmo nome de tabela após a exclusão. É melhor sempre usar nomes de tabela exclusivos.  
* Espere alguma limitação de taxa quando você usar uma nova tabela, enquanto o armazenamento de tabela aprende os padrões de acesso e distribui as partições entre nós. Você deve considerar com que frequência precisa criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando tiver um alto volume de entidades que devem ser excluídas ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)
* [Modificar entidades](#modify-entities)  

### <a name="data-series-pattern"></a>Padrão de série de dados
Armazene série de dados completa em uma única entidade para minimizar o número de solicitações que você faz.  

#### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é um aplicativo armazenar uma série de dados que ele normalmente precisa recuperar uma só vez. Por exemplo, seu aplicativo pode registrar a quantidade de mensagens instantâneas que cada funcionário envia por hora e usar essas informações para avaliar quantas mensagens cada usuário enviou nas 24 horas anteriores. Um design pode ser armazenar 24 entidades para cada funcionário:  

![Gráfico da entidade de estatísticas de mensagens][22]

Com esse design, você pode facilmente localizar e atualizar a entidade a ser atualizada para cada funcionário sempre que o aplicativo precisar atualizar o valor de contagem das mensagem. No entanto, para recuperar as informações e traçar um gráfico de atividades das 24 horas anteriores, você deve recuperar 24 entidades.  

#### <a name="solution"></a>Solução
Use o seguinte design, com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Gráfico mostrando entidade de estatísticas de mensagens com propriedades separadas][23]

Com esse design, você pode usar uma operação de mesclagem para atualizar a contagem de mensagens de um funcionário, em uma hora específica. Agora, você pode recuperar todas as informações necessárias para traçar o gráfico usando uma solicitação de uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Se sua série completa de dados não se encaixar em uma única entidade (uma entidade pode ter até 252 propriedades), use um armazenamento de dados alternativo, como uma bolha.  
* Se você tiver vários clientes atualizando uma entidade simultaneamente, use o **ETag** para implementar uma concorrência otimista. Se você tem muitos clientes, você pode experimentar alta contenção.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar atualizar e recuperar uma série de dados associados a uma entidade individual.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de grandes entidades](#large-entities-pattern)  
* [Mesclar ou substituir](#merge-or-replace)  
* [Eventualmente, padrão de transações consistentes](#eventually-consistent-transactions-pattern) (se você estiver armazenando a série de dados em uma bolha)  

### <a name="wide-entities-pattern"></a>Padrão de entidades longas
Use várias entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode ter mais de 252 propriedades (excluindo as propriedades obrigatórias do sistema), e não pode armazenar mais de 1 MB de dados no total. Em um banco de dados relacional, você normalmente trabalharia em torno de quaisquer limites sobre o tamanho de uma linha adicionando uma nova tabela e forçando uma relação de 1 para 1 entre eles.  

#### <a name="solution"></a>Solução
Ao usar o armazenamento de tabela, você pode armazenar várias entidades para representar um único objeto de grande negócio com mais de 252 propriedades. Por exemplo, se você quiser armazenar uma contagem do número de mensagens de Mensagens IM enviadas por cada funcionário nos últimos 365 dias, você pode usar o seguinte design que usa duas entidades com esquemas diferentes:  

![Gráfico mostrando entidade de estatísticas de mensagens com Rowkey 01 e entidade de estatísticas de mensagens com Rowkey 02][24]

Se você precisar fazer uma alteração que requer a atualização de ambas as entidades para mantê-las sincronizadas entre si, pode usar uma EGT. Caso contrário, você pode usar uma operação de mesclagem única para atualizar a contagem de mensagens de um dia específico. Para recuperar todos os dados de um funcionário individual, você deve recuperar ambas as entidades. Você pode fazer isso com duas `PartitionKey` solicitações `RowKey` eficientes que usam a e um valor.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere o seguinte ponto ao decidir como implementar esse padrão:  

* A recuperação de uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use este padrão quando precisar armazenar entidades cujo tamanho ou número de propriedades exceda os limites para uma entidade individual no armazenamento de tabelas.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações de grupos de entidades](#entity-group-transactions)
* [Mesclar ou substituir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de entidades grandes
Use o armazenamento Blob para armazenar grandes valores de propriedade.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se uma ou várias de suas propriedades armazenarem valores que fazem com que o tamanho total da sua entidade exceda esse valor, você não poderá armazenar toda a entidade no armazenamento de tabela.  

#### <a name="solution"></a>Solução
Se sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, você pode armazenar dados no armazenamento Blob e, em seguida, armazenar o endereço da bolha em uma propriedade na entidade. Por exemplo, você pode armazenar a foto de um funcionário no armazenamento `Photo` Blob e armazenar um link para a foto na propriedade de sua entidade de funcionário:  

![Gráfico mostrando a entidade dos funcionários com string para Foto apontando para o armazenamento Blob][25]

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Para manter uma eventual consistência entre a entidade no armazenamento de tabelas e os dados no armazenamento Blob, use o [padrão de transações eventualmente consistente para](#eventually-consistent-transactions-pattern) manter suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados do blob.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use este padrão quando precisar armazenar entidades cujo tamanho exceda os limites para uma entidade individual no armazenamento de tabelas.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades longas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antipadrão prefixar/acrescentar
Quando você tiver um alto volume de inserções, aumente a escalabilidade espalhando as pastilhas através de várias partições.  

#### <a name="context-and-problem"></a>Contexto e problema
A prefixação ou o acréscimo de entidades às suas entidades armazenadas normalmente fazem com que o aplicativo adicione novas entidades à primeira ou última partição de uma sequência de partições. Neste caso, todas as inserções em qualquer momento particular estão ocorrendo na mesma partição, criando um hotspot. Isso impede que o armazenamento de tabelas insira o balanceamento de carga em vários nós e possivelmente faz com que seu aplicativo atinja os alvos de escalabilidade para partição. Por exemplo, considere o caso de um aplicativo que registra rede e acesso de recursos por funcionários. Uma estrutura de entidade como a seguinte pode resultar na partição da hora atual se tornando um hotspot, se o volume de transações atingir o alvo de escalabilidade para uma partição individual:  

![Gráfico da entidade dos funcionários][26]

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um hotspot em qualquer partição específica, pois o aplicativo registra eventos:  

![Gráfico mostrando a entidade dos funcionários com a RowKey compondo o Ano, Mês, Dia, Hora e ID do Evento][27]

Observe com este exemplo `PartitionKey` `RowKey` como as chaves compostas e as do ambos são. O `PartitionKey` uso do departamento e do ID do funcionário para distribuir o registro em várias partições.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* A estrutura de chave alternativa que evita a criação de partições ativas em inserções dá suporte eficiente a consultas que o aplicativo cliente faz?  
* O volume antecipado de transações significa que você provavelmente atingirá as metas de escalabilidade para uma partição individual e será estrangulado pelo armazenamento de tabela?  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Evite o anti-padrão de preparação/apêndice quando seu volume de transações provavelmente resultará em limitação de taxa pelo armazenamento de tabela quando você acessar uma partição quente.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de final do log](#log-tail-pattern)  
* [Modificar entidades](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Antipadrão de dados de log
Normalmente, você deve usar o armazenamento Blob em vez de armazenamento de tabela para armazenar dados de log.  

#### <a name="context-and-problem"></a>Contexto e problema
Um caso de uso comum para dados de registro é recuperar uma seleção de entradas de registro para um intervalo de data/hora específico. Por exemplo, você deseja encontrar todos os erros e mensagens críticas que seu aplicativo registrou entre 15:04 e 15:06 em uma data específica. Você não deseja usar a data e a hora da mensagem de log para determinar a partição para a qual você salva entidades de log. Isso resulta em uma partição quente porque, a qualquer `PartitionKey` momento específico, todas as entidades de log compartilharão o mesmo valor (consulte o [padrão anti-padrão Prepend/apêndice](#prepend-append-anti-pattern)). Por exemplo, o seguinte esquema de entidade para uma mensagem de log resulta em uma partição quente, porque o aplicativo grava todas as mensagens de log para a partição para a data e hora atuais:  

![Gráfico da entidade de mensagens de log][28]

Neste exemplo, `RowKey` o inclui a data e a hora da mensagem de registro para garantir que as mensagens de registro sejam classificadas em ordem de data/hora. O `RowKey` também inclui um ID de mensagem, caso várias mensagens de registro compartilhem a mesma data e hora.  

Outra abordagem é `PartitionKey` usar um que garanta que o aplicativo escreva mensagens em uma variedade de partições. Por exemplo, se a fonte da mensagem de log fornecer uma maneira de distribuir mensagens em muitas partições, você poderá usar o seguinte esquema de entidade:  

![Gráfico da entidade de mensagens de log][29]

No entanto, o problema com este esquema é que para recuperar todas as mensagens de log por um período de tempo específico, você deve pesquisar cada partição na tabela.

#### <a name="solution"></a>Solução
A seção anterior destacava o problema de tentar usar o armazenamento de tabela para armazenar entradas de log, e sugeria dois projetos insatisfatórios. Uma solução levou a uma partição quente com o risco de má performance escrevendo mensagens de registro. A outra solução resultou em um desempenho de consulta ruim, devido à necessidade de escanear cada partição na tabela para recuperar mensagens de log por um período de tempo específico. O armazenamento Blob oferece uma solução melhor para esse tipo de cenário, e é assim que a análise do Azure Storage armazena os dados de log que coleta.  

Esta seção descreve como a análise de armazenamento armazena dados de registro no armazenamento Blob, como uma ilustração dessa abordagem para armazenar dados que você normalmente consulta por intervalo.  

A análise de armazenamento armazena mensagens de registro em um formato delimitado em várias blobs. O formato delimitado facilita para um aplicativo cliente analisar os dados na mensagem de log.  

A análise de armazenamento usa uma convenção de nomeação para blobs que permite localizar o blob (ou blobs) que contêm as mensagens de log para as quais você está pesquisando. Por exemplo, uma bolha chamada "fila/2014/07/31/1800/000001.log" contém mensagens de log que se relacionam com o serviço de fila para a hora a partir das 18:00 em 31 de julho de 2014. "000001" indica que este é o primeiro arquivo de log para esse período. A análise de armazenamento também registra os carimbos de tempo das primeiras e últimas mensagens de registro armazenadas no arquivo, como parte dos metadados do blob. A API para armazenamento Blob permite localizar bolhas em um contêiner com base em um prefixo de nome. Para localizar todos os blobs que contêm dados de registro de fila para a hora a partir das 18:00, você pode usar o prefixo "fila/2014/07/31/1800".  

Os buffers de análise de armazenamento registram mensagens de log internamente e, em seguida, atualiza periodicamente a bolha apropriada ou cria uma nova com o lote mais recente de entradas de log. Isso reduz o número de gravações que deve executar para o armazenamento Blob.  

Se você está implementando uma solução semelhante em seu próprio aplicativo, considere como gerenciar a troca entre confiabilidade e custo e escalabilidade. Em outras palavras, avalie o efeito de escrever cada entrada de log no armazenamento Blob como ela acontece, em comparação com as atualizações de buffer em seu aplicativo e escrevê-las no armazenamento Blob em lotes.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de log:  

* Se você criar um design de tabela que evite partições quentes potenciais, você pode descobrir que não pode acessar seus dados de log de forma eficiente.  
* Para processar dados de log, um cliente normalmente precisa carregar vários registros.  
* Embora os dados de log sejam frequentemente estruturados, o armazenamento Blob pode ser uma solução melhor.  

### <a name="implementation-considerations"></a>Considerações sobre a implementação
Esta seção discute algumas das considerações a serem lembradas ao implementar os padrões descritos nas seções anteriores. Grande parte dessa seção usa exemplos escritos em c# que usam a Biblioteca de Cliente de Armazenamento (versão 4.3.0 no momento da redação).  

### <a name="retrieve-entities"></a>Recuperar entidades
Conforme discutido na seção [Design para consulta](#design-for-querying), a consulta mais eficiente é uma consulta de ponto. No entanto, em alguns cenários você pode precisar recuperar várias entidades. Esta seção descreve algumas abordagens comuns para recuperar entidades usando a Biblioteca do Cliente de Armazenamento.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Execute uma consulta de pontos usando a Biblioteca cliente de armazenamento
A maneira mais fácil de executar uma consulta de ponto é usar a operação da tabela **Recuperar.** Como mostrado no seguinte trecho de código C#, esta `PartitionKey` operação recupera uma entidade `RowKey` com um valor "Vendas", e um de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como este exemplo espera que a `EmployeeEntity`entidade que ele recupera seja do tipo .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Recupere várias entidades usando LINQ
Você pode recuperar várias entidades usando LINQ com biblioteca cliente de armazenamento e especificando uma consulta com uma cláusula **onde.** Para evitar uma varredura de tabela, `PartitionKey` você deve sempre incluir o `RowKey` valor na cláusula onde, e se possível o valor para evitar varreduras de tabela e partição. O armazenamento de tabelas suporta um conjunto limitado de operadores de comparação (maiores ou iguais, menores ou iguais, iguais e não iguais) para usar na cláusula onde. O seguinte trecho de código C# encontra todos os funcionários cujo sobrenome `RowKey` começa com "B" (assumindo `PartitionKey` que as lojas o sobrenome) no departamento de Vendas (assumindo as lojas o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observe como a consulta especifica `RowKey` a `PartitionKey` e a para garantir um melhor desempenho.  

A amostra de código a seguir mostra funcionalidade equivalente usando a API fluente (para obter mais informações sobre APIs fluentes em geral, consulte [As melhores práticas para projetar uma API fluente](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A amostra faz `CombineFilters` vários métodos para incluir as três condições do filtro.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Recuperar um grande número de entidades de uma consulta
Uma consulta ideal retorna uma entidade `PartitionKey` individual `RowKey` com base em um valor e um valor. No entanto, em alguns cenários você pode ter um requisito para retornar muitas entidades da mesma partição, ou mesmo de muitas partições. Você deve sempre testar totalmente o desempenho do seu aplicativo nesses cenários.  

Uma consulta contra o armazenamento tabela pode retornar um máximo de 1.000 entidades ao mesmo tempo e ser executada por um máximo de cinco segundos. O armazenamento de tabela retorna um token de continuação para permitir que o aplicativo cliente solicite o próximo conjunto de entidades, se alguma das seguintes coisas for verdadeira:

- O conjunto de resultados contém mais de 1.000 entidades.
- A consulta não foi concluída em cinco segundos.
- A consulta cruza o limite de partição. 

Para obter mais informações sobre como os tokens de continuação funcionam, consulte [o tempo de consulta e a paginação](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se você estiver usando a Biblioteca do Cliente de Armazenamento, ele pode lidar automaticamente com tokens de continuação para você à medida que retorna entidades do armazenamento de tabela. Por exemplo, a seguinte amostra de código C# lida automaticamente com tokens de continuação se o armazenamento da Tabela os retornar em uma resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

O código c# a seguir trata os tokens de continuação explicitamente:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Usando tokens de continuação explicitamente, você pode controlar quando o aplicativo recupera o próximo segmento de dados. Por exemplo, se o aplicativo cliente permitir que os usuários page através das entidades armazenadas em uma tabela, um usuário pode decidir não fazer a página através de todas as entidades recuperadas pela consulta. Seu aplicativo só usaria um token de continuação para recuperar o próximo segmento quando o usuário tivesse terminado de paginar todas as entidades do segmento atual. Essa abordagem tem vários benefícios:  

* Você pode limitar a quantidade de dados a serem recuperados do armazenamento de tabelas e que você se mova sobre a rede.  
* Você pode executar I/O assíncrono em .NET.  
* Você pode serializar o token de continuação para armazenamento persistente, para que você possa continuar no caso de uma falha de aplicativo.  

> [!NOTE]
> Um token de continuação normalmente retorna um segmento contendo 1.000 entidades, embora possa conter menos. Este também é o caso se você limitar o número de entradas que uma consulta retorna usando **Take** para retornar as primeiras n entidades que correspondem aos seus critérios de consulta. O armazenamento de tabelas pode retornar um segmento contendo menos de n entidades, juntamente com um token de continuação para permitir que você recupere as entidades restantes.  
> 
> 

O código c# a seguir mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 propriedades e até 1 MB de tamanho. Quando você consulta a tabela e recupera entidades, você pode não precisar de todas as propriedades, e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Você pode usar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo a seguir `Email` recupera apenas `PartitionKey` `RowKey`a `Timestamp`propriedade `ETag`(juntamente com , , e ) das entidades selecionadas pela consulta.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observe como `RowKey` o valor está disponível mesmo que não esteja incluído na lista de propriedades a serem recuperadas.  

### <a name="modify-entities"></a>Modificar entidades
A Biblioteca cliente de armazenamento permite que você modifique suas entidades armazenadas no armazenamento de tabela, inserindo, excluindo e atualizando entidades. Você pode usar EGTs para emlote várias inserções, atualizar e excluir operações em conjunto, para reduzir o número de ida e volta necessária e melhorar o desempenho de sua solução.  

Exceções lançadas quando a Biblioteca cliente de armazenamento executa um EGT normalmente incluem o índice da entidade que causou a falha do lote. Isso é útil quando você está depurando código que usa EGTs.  

Você também deve considerar como seu design afeta a forma de tratamento, por parte do cliente, das operações de simultaneidade e atualização.  

#### <a name="managing-concurrency"></a>Gerenciando simultaneidade
Por padrão, o armazenamento de tabelas implementa verificações de concorrência otimistas no nível de entidades individuais para inserir, mesclar e excluir operações, embora seja possível que um cliente force o armazenamento de tabelas a contornar essas verificações. Para obter mais informações, consulte [Gerenciamento de moeda simultas no Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Mesclar ou substituir
O `Replace` método `TableOperation` da classe sempre substitui a entidade completa no armazenamento de tabelas. Se você não incluir um imóvel na solicitação quando esse imóvel existir na entidade armazenada, a solicitação removerá essa propriedade da entidade armazenada. A menos que você queira remover uma propriedade explicitamente de uma entidade armazenada, você deve incluir todas as propriedades na solicitação.  

Você pode `Merge` usar o `TableOperation` método da classe para reduzir a quantidade de dados que você envia para o armazenamento da tabela quando quiser atualizar uma entidade. O `Merge` método substitui quaisquer propriedades da entidade armazenada por valores de propriedade da entidade incluída na solicitação. Este método deixa intactas quaisquer propriedades na entidade armazenada que não estejam incluídas na solicitação. Isso é útil se você tiver grandes entidades e só precisar atualizar um pequeno número de propriedades em uma solicitação.  

> [!NOTE]
> Os `*Replace` `Merge` métodos falham se a entidade não existir. Como alternativa, você pode `InsertOrReplace` `InsertOrMerge` usar os métodos que criam uma nova entidade se ela não existir.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidade heterogênea
O armazenamento de mesa é uma loja de mesas *sem esquema.* Isso significa que uma única mesa pode armazenar entidades de vários tipos, proporcionando grande flexibilidade em seu design. O exemplo a seguir ilustra uma tabela que armazena as entidades funcionário e departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Cada entidade ainda `PartitionKey` `RowKey`deve `Timestamp` ter , e valores, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada que indique o tipo de entidade a menos que você escolha armazenar essa informação em algum lugar. Há duas opções para identificar o tipo de entidade:  

* Preparar o tipo de `RowKey` entidade para `PartitionKey`o (ou possivelmente o ). Por `EMPLOYEE_000123` exemplo, `DEPARTMENT_SALES` `RowKey` ou como valores.  
* Use uma propriedade separada para registrar o tipo de entidade, conforme mostrado na tabela a seguir.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

A primeira opção, antes do `RowKey`tipo de entidade para o , é útil se houver a possibilidade de que duas entidades de tipos diferentes possam ter o mesmo valor-chave. Ela também agrupa entidades do mesmo tipo juntas na partição.  

As técnicas discutidas nesta seção são especialmente relevantes para a discussão sobre[as relações de herança.](#inheritance-relationships)  

> [!NOTE]
> Considere incluir um número de versão no valor do tipo entidade, para permitir que os aplicativos clientes evoluam objetos POCO e trabalhem com diferentes versões.  
> 
> 

O restante desta seção descreve alguns dos recursos na Biblioteca de Cliente de Armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Recuperar tipos de entidades heterogêneas
Se você estiver usando a Biblioteca cliente de armazenamento, você tem três opções para trabalhar com vários tipos de entidades.  

Se você sabe o tipo da `RowKey` `PartitionKey` entidade armazenada com valores específicos, então você pode especificar o tipo de entidade quando recuperar a entidade. Você viu isso nos dois exemplos anteriores `EmployeeEntity`que recuperam entidades do tipo : Execute uma consulta de [ponto usando a Biblioteca cliente de armazenamento](#run-a-point-query-by-using-the-storage-client-library) e [recupere várias entidades usando LINQ](#retrieve-multiple-entities-by-using-linq).  

A segunda opção é `DynamicTableEntity` usar o tipo (um saco de propriedade), em vez de um tipo de entidade POCO de concreto. Essa opção também pode melhorar o desempenho, porque não há necessidade de serializar e desserializar a entidade para tipos .NET. O código C# a seguir potencialmente recupera várias entidades de `DynamicTableEntity` diferentes tipos da tabela, mas retorna todas as entidades como instâncias. Em seguida, `EntityType` ele usa a propriedade para determinar o tipo de cada entidade:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Para recuperar outras propriedades, `TryGetValue` você `Properties` deve usar `DynamicTableEntity` o método na propriedade da classe.  

Uma terceira opção é `DynamicTableEntity` combinar usando `EntityResolver` o tipo e uma instância. Isso permite que você resolver para vários tipos POCO na mesma consulta. Neste exemplo, `EntityResolver` o delegado `EntityType` está usando a propriedade para distinguir entre os dois tipos de entidade que a consulta retorna. O `Resolve` método `resolver` usa o `DynamicTableEntity` delegado `TableEntity` para resolver instâncias em instâncias.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>Modificar tipos de entidades heterogêneas
Você não precisa saber o tipo de entidade para excluí-lo, e você sempre sabe o tipo de entidade quando você insere.ou não. No entanto, `DynamicTableEntity` você pode usar o tipo para atualizar uma entidade sem saber o seu tipo, e sem usar uma classe de entidade POCO. A amostra de código a seguir recupera `EmployeeCount` uma única entidade e verifica se a propriedade existe antes de atualizá-la.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>Controle o acesso com assinaturas de acesso compartilhadas
Você pode usar tokens de assinatura de acesso compartilhado (SAS) para permitir que os aplicativos clientes modifiquem (e consultam) entidades de tabela diretamente, sem a necessidade de autenticar diretamente com o armazenamento de tabela. Normalmente, há três benefícios principais para usar SAS em seu aplicativo:  

* Você não precisa distribuir a chave da sua conta de armazenamento para uma plataforma insegura (como um dispositivo móvel) para permitir que esse dispositivo acesse e modifique entidades no armazenamento de tabelas.  
* Você pode descarregar parte do trabalho que as funções web e trabalhadores desempenham na gestão de suas entidades. Você pode descarregar para dispositivos clientes, como computadores de usuário final e dispositivos móveis.  
* Você pode atribuir um conjunto limitado e limitado de permissões a um cliente (como permitir acesso somente à leitura a recursos específicos).  

Para obter mais informações sobre o uso de tokens SAS com armazenamento de tabela, consulte [Usando assinaturas de acesso compartilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, você ainda deve gerar os tokens SAS que concedem um aplicativo cliente às entidades no armazenamento de tabelas. Faça isso em um ambiente que tenha acesso seguro às chaves da sua conta de armazenamento. Geralmente, você usa uma função de trabalho ou Web para gerar tokens SAS e enviá-los aos aplicativos clientes que precisam acessar suas entidades. Como ainda há uma sobrecarga envolvida na geração e fornecimento de tokens SAS aos clientes, você deve considerar a melhor maneira de reduzir essa sobrecarga, especialmente em cenários de alto volume.  

É possível gerar um token SAS que concede acesso a um subconjunto das entidades em uma tabela. Por padrão, você cria um token SAS para uma tabela inteira. Mas também é possível especificar que o token SAS `PartitionKey` concede acesso a `PartitionKey` uma `RowKey` série de valores, ou uma série de valores. Você pode optar por gerar tokens SAS para usuários individuais do seu sistema, de tal forma que o token SAS de cada usuário só lhes permite acesso às suas próprias entidades no armazenamento de tabela.  

### <a name="asynchronous-and-parallel-operations"></a>Operações paralelas e assíncronas
Desde que você esteja distribuindo suas solicitações por várias partições, pode melhorar a capacidade de resposta do cliente e a taxa de transferência usando consultas assíncronas ou paralelas.
Por exemplo, você pode ter duas ou mais instâncias de função de trabalho acessando suas tabelas em paralelo. Você pode ter funções individuais de trabalhador responsáveis por determinados conjuntos de partições, ou simplesmente ter várias instâncias de função do trabalhador, cada uma capaz de acessar todas as partições em uma tabela.  

Dentro de uma instância do cliente, você pode melhorar o throughput executando operações de armazenamento de forma assíncrona. A Biblioteca de Cliente de Armazenamento facilita a gravação de consultas e modificações assíncronas. Por exemplo, você pode começar com o método síncrono que recupera todas as entidades em uma partição, conforme mostrado no seguinte código C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Você pode facilmente modificar este código para que a consulta seja executada de forma assíncrona, da seguinte forma:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método `async` agora inclui o `Task` modificador e retorna uma instância.  
* Em vez `ExecuteSegmented` de chamar o método para `ExecuteSegmentedAsync` recuperar resultados, o método agora chama o método. O método `await` usa o modificador para recuperar resultados assíncronamente.  

O aplicativo cliente pode chamar esse método várias vezes, com valores diferentes para o `department` parâmetro. Cada consulta é executada em um segmento separado.  

Não há uma versão assíncrona do `Execute` método na `TableQuery` classe, porque a `IEnumerable` interface não suporta enumeração assíncrona.  

Você também pode inserir, atualizar e excluir entidades de forma assíncrona. O exemplo c# a seguir mostra um método síncrono simples para inserir ou substituir uma entidade funcionário:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Você pode facilmente modificar este código para que a atualização seja executada de forma assíncrona, da seguinte forma:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método `async` agora inclui o `Task` modificador e retorna uma instância.  
* Em vez `Execute` de chamar o método para atualizar `ExecuteAsync` a entidade, o método agora chama o método. O método `await` usa o modificador para recuperar resultados assíncronamente.  

O aplicativo cliente pode chamar vários métodos assíncronos como este, e cada invocação do método é executada em um segmento separado.  


[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

