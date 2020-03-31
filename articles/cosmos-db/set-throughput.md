---
title: Taxa de transferência de provisionamento nos contêineres e bancos de dados do Azure Cosmos
description: Saiba como definir a taxa de transferência provisionada para os contêineres e bancos de dados do Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251967"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprovisionar a taxa de transferência para contêineres e bancos de dados

Um banco de dados do Azure Cosmos é uma unidade de gerenciamento para um conjunto de contêineres. Um banco de dados é composto por um conjunto de contêineres independentes de esquema. Um contêiner do Azure Cosmos é a unidade de escalabilidade para taxa de transferência e armazenamento. Um contêiner é particionado horizontalmente em um conjunto de máquinas em uma região do Azure e é distribuído em todas as regiões do Azure associadas à sua conta do Azure Cosmos.

Com o Azure Cosmos DB, você pode provisão throughput em duas granularidades:
 
- Contêineres do Azure Cosmos
- Bancos de dados do Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Definir taxa de transferência em um contêiner  

O transporte provisionado em um contêiner Azure Cosmos é exclusivamente reservado para esse contêiner. O contêiner recebe a taxa de transferência provisionada o tempo todo. A taxa de transferência provisionada em um contêiner é respaldada financeiramente por SLAs. Para saber como configurar o throughput em um contêiner, consulte [Provision throughput em um contêiner Azure Cosmos](how-to-provision-container-throughput.md).

A configuração do throughput provisionado em um contêiner é a opção mais utilizada. Você pode dimensionar elasticamente o throughput para um recipiente provisionando qualquer quantidade de throughput usando [Unidades de Solicitação (RUs)](request-units.md). 

O throughput provisionado para um contêiner é distribuído uniformemente entre suas partições físicas, e assumindo uma boa chave de partição que distribui as partições lógicas uniformemente entre as partições físicas, o throughput também é distribuído uniformemente em todos os as partições lógicas do recipiente. Não é possível especificar seletivamente o throughput para partições lógicas. Como uma ou mais partições lógicas de um contêiner são hospedadas por uma partição física, as partições físicas pertencem exclusivamente ao contêiner e dão suporte à taxa de transferência provisionada no contêiner. 

Se a carga de trabalho em execução em uma partição lógica consumir mais do que o throughput que foi alocado para essa partição lógica, suas operações ficam com taxa limitada. Quando ocorrer limitação de taxa, você pode aumentar o throughput provisionado para todo o contêiner ou tentar novamente as operações. Para saber mais sobre particionamento, confira [Partições lógicas](partition-data.md).

É recomendável configurar a taxa de transferência na granularidade do contêiner quando você quiser desempenho garantido para o contêiner.

A imagem a seguir mostra como uma partição física hospeda uma ou mais partições lógicas de um contêiner:

![Partição física](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Definir taxa de transferência em um banco de dados

Quando você provisão throughput em um banco de dados do Azure Cosmos, o throughput é compartilhado em todos os contêineres (chamados contêineres de banco de dados compartilhados) no banco de dados. Uma exceção é se você especificou um throughput provisionado em contêineres específicos no banco de dados. O compartilhamento do throughput provisionado em nível de banco de dados entre seus contêineres é análogo à hospedagem de um banco de dados em um cluster de máquinas. Como todos os contêineres de um banco de dados compartilham os recursos disponíveis em um computador, evidentemente você não obtém o desempenho previsível em qualquer contêiner específico. Para saber como configurar o throughput provisionado em um banco de dados, consulte [Configurar throughput provisionado em um banco de dados Do Azure Cosmos](how-to-provision-database-throughput.md).

A configuração de throughput em um banco de dados do Azure Cosmos garante que você receba o throughput provisionado para esse banco de dados o tempo todo. Como todos os contêineres do banco de dados compartilham a taxa de transferência provisionada, o Azure Cosmos DB não fornece nenhuma garantia de taxa de transferência previsível para um contêiner específico nesse banco de dados. A parte da taxa de transferência que um contêiner específico pode receber depende do seguinte:

* Número de contêineres.
* Escolha de chaves de partição para vários contêineres.
* Da distribuição da carga de trabalho entre várias partições lógicas dos contêineres. 

É recomendável que você configure a taxa de transferência em um banco de dados quando quiser compartilhar a taxa de transferência em vários contêineres, mas não quer dedicar a taxa de transferência a nenhum contêiner específico. 

Os exemplos a seguir demonstram onde é preferível provisionar a taxa de transferência no nível do banco de dados:

* Compartilhar a taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil para um aplicativo multilocatário. Cada usuário pode ser representado por um contêiner do Azure Cosmos distinto.

* Compartilhar a taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil quando você migra um banco de dados NoSQL, como MongoDB ou Cassandra, hospedado em um cluster de VMs ou de servidores físicos locais para o Azure Cosmos DB. Pense na taxa de transferência provisionada configurada no banco de dados do Azure Cosmos como um equivalente lógico, porém mais econômico e elástico, ao da capacidade de computação do cluster do MongoDB ou Cassandra.  

Todos os contêineres criados dentro de um banco de dados com throughput provisionado devem ser criados com uma [chave de partição](partition-data.md). A qualquer momento, o throughput alocado em um contêiner dentro de um banco de dados é distribuído em todas as partições lógicas desse contêiner. Quando você tem contêineres que compartilham throughput provisionado configurado em um banco de dados, você não pode aplicar seletivamente o throughput a um contêiner específico ou a uma partição lógica. 

Se a carga de trabalho em uma partição lógica consumir mais do que a taxa de transferência alocada para uma partição lógica específica, suas operações serão limitadas por taxa. Quando ocorre limitação de taxa, você pode aumentar o throughput de todo o banco de dados ou tentar novamente as operações. Para saber mais sobre particionamento, confira [Partições lógicas](partition-data.md).

Os contêineres em um banco de dados de throughput compartilhado compartilham o throughput (RU/s) alocado nesse banco de dados. Você pode ter até quatro contêineres com um mínimo de 400 RU/s no banco de dados. Cada novo contêiner após os quatro primeiros exigirá um mínimo adicional de 100 RU/s. Por exemplo, se você tiver um banco de dados de throughput compartilhado com oito contêineres, o mínimo ru/s no banco de dados será de 800 RU/s.

> [!NOTE]
> Em fevereiro de 2020, introduzimos uma mudança que permite que você tenha um máximo de 25 contêineres em um banco de dados de throughput compartilhado, o que permite melhor o compartilhamento de throughput entre os contêineres. Após os primeiros 25 contêineres, você pode adicionar mais contêineres ao banco de dados somente se eles forem [provisionados com throughput dedicado,](#set-throughput-on-a-database-and-a-container)que é separado do throughput compartilhado do banco de dados.<br>
Se sua conta do Azure Cosmos DB já contiver um banco de dados de throughput compartilhado com >=25 contêineres, a conta e todas as outras contas na mesma assinatura do Azure estão isentas dessa alteração. Entre [em contato com o suporte do produto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você tiver comentários ou dúvidas. 

Se suas cargas de trabalho envolverem a exclusão e a recriação de todas as coleções em um banco de dados, é recomendável que você solte o banco de dados vazio e recrie um novo banco de dados antes da criação da coleção. A imagem a seguir mostra como uma partição física pode hospedar uma ou mais partições lógicas que pertencem a contêineres diferentes dentro de um banco de dados:

![Partição física](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Definir taxa de transferência em um banco de dados e um contêiner

É possível combinar os dois modelos. É permitido provisionar a taxa de transferência tanto no banco de dados como no contêiner. O exemplo a seguir mostra como provisionar a taxa de transferência em um banco de dados do Azure Cosmos e em um contêiner:

* Você pode criar um banco de dados do Azure Cosmos chamado *Z* com throughput provisionado de *RUs "K".* 
* Em seguida, crie cinco recipientes chamados *A,* *B,* *C,* *D*e *E* dentro do banco de dados. Ao criar o contêiner B, certifique-se de ativar **o throughput dedicado ao Provision para esta** opção de contêiner e configurar explicitamente *RUs "P"* de throughput provisionado neste recipiente. Observe que você pode configurar o throughput compartilhado e dedicado somente ao criar o banco de dados e o contêiner. 

   ![Definindo o throughput no nível do contêiner](./media/set-throughput/coll-level-throughput.png)

* O throughput *"K"* RUs é compartilhado entre os quatro recipientes *A,* *C,* *D*e *E*. A quantidade exata de throughput disponível para *A,* *C,* *D,* ou *E* varia. Não há SLAs para taxa de transferência de cada contêiner individual.
* O contêiner chamado *B* é garantido para obter o throughput *"P"* RUs o tempo todo. É respaldado por SLAs.

> [!NOTE]
> Um contêiner com throughput provisionado não pode ser convertido em contêiner de banco de dados compartilhado. Por outro lado, um contêiner de banco de dados compartilhado não pode ser convertido para ter um throughput dedicado.

## <a name="update-throughput-on-a-database-or-a-container"></a>Atualizar throughput em um banco de dados ou em um contêiner

Depois de criar um contêiner Azure Cosmos ou um banco de dados, você pode atualizar o throughput provisionado. Não há limite do throughput máximo provisionado que você pode configurar no banco de dados ou no contêiner. O [rendimento mínimo provisionado](concepts-limits.md#storage-and-throughput) depende dos seguintes fatores: 

* O tamanho máximo de dados que você já armazenou no contêiner
* O throughput máximo que você já provisionou no recipiente
* O número atual de contêineres Azure Cosmos que você tem em um banco de dados com throughput compartilhado. 

Você pode recuperar o rendimento mínimo de um contêiner ou de um banco de dados programáticamente usando os SDKs ou visualizar o valor no portal Azure. Ao usar o SDK .NET, o método [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) permite dimensionar o valor de throughput provisionado. Ao usar o Java SDK, o método [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) permite dimensionar o valor de throughput provisionado. 

Ao usar o SDK .NET, o método [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) permite recuperar o throughput mínimo de um contêiner ou de um banco de dados. 

Você pode dimensionar o throughput provisionado de um contêiner ou um banco de dados a qualquer momento. Quando uma operação de escala é realizada para aumentar o throughput, pode levar mais tempo devido às tarefas do sistema para prover os recursos necessários. Você pode verificar o status da operação de escala no portal Azure ou programaticamente usando os SDKs. Ao usar o .Net SDK, você pode obter o `DocumentClient.ReadOfferAsync` status da operação de escala usando o método.

## <a name="comparison-of-models"></a>Comparação de modelos

|**Parâmetro**  |**Taxa de transferência provisionada em um banco de dados**  |**Taxa de transferência provisionada em um contêiner**|
|---------|---------|---------|
|Mínimo de RUs |400 (após os primeiros quatro contêineres, cada contêiner adicional irá exigir um mínimo de 100 RUs por segundo.) |400|
|Mínimo de RUs por contêiner|100|400|
|Máximo de RUs|Ilimitado, no banco de dados.|Ilimitado, no contêiner.|
|RUs atribuídas ou disponíveis para um contêiner específico|Sem garantias. RUs atribuídas a um determinado contêiner dependem das propriedades. As propriedades podem ser a escolha de chaves de partição de contêineres que compartilham a taxa de transferência, a distribuição da carga de trabalho e o número de contêineres. |Todas as RUs configuradas no contêiner são reservadas exclusivamente ao contêiner.|
|Armazenamento máximo para um contêiner|Ilimitado.|Ilimitado.|
|Taxa de transferência máxima por partição lógica de um contêiner|10 mil RUs|10 mil RUs|
|Taxa de transferência máxima (dados + índice) por partição lógica de um contêiner|20 GB|20 GB|

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).

