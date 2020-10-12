---
title: Particionamento no BD Cosmos do Azure
description: Saiba mais sobre o particionamento em Azure Cosmos DB, práticas recomendadas ao escolher uma chave de partição e como gerenciar partições lógicas
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: aa7d67cd6bd1bd422bd257b75ac5bde3bd534d7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481826"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Particionamento no BD Cosmos do Azure

Azure Cosmos DB usa o particionamento para dimensionar contêineres individuais em um banco de dados para atender às necessidades de desempenho do seu aplicativo. No particionamento, os itens em um contêiner são divididos em subconjuntos distintos chamados *partições lógicas*. As partições lógicas são formadas com base no valor de uma *chave de partição* associada a cada item em um contêiner. Todos os itens em uma partição lógica têm o mesmo valor de chave de partição.

Por exemplo, um contêiner contém itens. Cada item tem um valor exclusivo para a `UserID` propriedade. Se `UserID` serve como a chave de partição para os itens no contêiner e há 1.000 valores exclusivos `UserID` , 1.000 partições lógicas são criadas para o contêiner.

Além de uma chave de partição que determina a partição lógica do item, cada item em um contêiner tem uma *ID de item* (exclusiva em uma partição lógica). A combinação da chave de partição e da *ID do item* cria o *índice*do item, que identifica exclusivamente o item.

[Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que afetará o desempenho do aplicativo.

## <a name="managing-logical-partitions"></a>Gerenciando partições lógicas

Azure Cosmos DB gerencia de forma transparente e automática o posicionamento de partições lógicas em partições físicas para atender com eficiência às necessidades de escalabilidade e desempenho do contêiner. À medida que os requisitos de taxa de transferência e armazenamento de um aplicativo aumentam, Azure Cosmos DB move partições lógicas para espalhar automaticamente a carga em um número maior de partições físicas. Você pode saber mais sobre [partições físicas](partition-data.md#physical-partitions).

O Azure Cosmos DB usa o particionamento baseado em hash para distribuir partições lógicas entre partições físicas. Azure Cosmos DB hash do valor de chave de partição de um item. O resultado com hash determina a partição física. Em seguida, Azure Cosmos DB aloca o espaço de chave dos hashes de chave de partição uniformemente entre as partições físicas.

As transações (em procedimentos armazenados ou gatilhos) são permitidas somente em itens em uma única partição lógica.

Você pode saber mais sobre [como Azure Cosmos DB gerencia partições](partition-data.md). (Não é necessário entender os detalhes internos para criar ou executar seus aplicativos, mas adicionados aqui para um leitor curioso.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Escolhendo uma chave de partição

Uma chave de partição tem dois componentes: **caminho de chave de partição** e valor de chave de **partição**. Por exemplo, considere um item {"userId": "Andrew", "worksFor": "Microsoft"} se você escolher "userId" como a chave de partição, estes são os dois componentes de chave de partição:

* O caminho da chave de partição (por exemplo: "/userid"). O caminho da chave de partição aceita caracteres alfanuméricos e sublinhados (_). Você também pode usar objetos aninhados usando a notação de caminho padrão (/).

* O valor da chave de partição (por exemplo: "Andrew"). O valor da chave de partição pode ser de tipos de cadeia de caracteres ou numéricos.

Para saber mais sobre os limites de taxa de transferência, armazenamento e comprimento da chave de partição, consulte o artigo [Azure Cosmos DB cotas de serviço](concepts-limits.md) .

A seleção de sua chave de partição é uma opção de design simples, mas importante no Azure Cosmos DB. Depois de selecionar a chave de partição, não é possível alterá-la in-loco. Se você precisar alterar sua chave de partição, mova seus dados para um novo contêiner com a nova chave de partição desejada.

Para **todos os** contêineres, sua chave de partição deve:

* Ser uma propriedade que tem um valor que não é alterado. Se uma propriedade for sua chave de partição, você não poderá atualizar o valor dessa propriedade.
* Tenha uma alta cardinalidade. Em outras palavras, a propriedade deve ter uma ampla variedade de valores possíveis.
* Armazenamento de dados e consumo de RU (unidade de solicitação de espalhamento) uniformemente em todas as partições lógicas. Isso garante o consumo até a RU e a distribuição de armazenamento em suas partições físicas.

Se você precisar de [Transações ACID com vários itens](database-transactions-optimistic-concurrency.md#multi-item-transactions) no Azure Cosmos DB, será necessário usar [procedimentos armazenados ou gatilhos](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Todos os procedimentos armazenados e gatilhos baseados em JavaScript têm como escopo uma única partição lógica.

## <a name="partition-keys-for-read-heavy-containers"></a>Chaves de partição para contêineres de leitura intensa

Para a maioria dos contêineres, os critérios acima são tudo o que você precisa considerar ao escolher uma chave de partição. No entanto, para grandes contêineres pesados de leitura, convém escolher uma chave de partição que aparece com frequência como um filtro em suas consultas. As consultas podem ser [roteadas de forma eficiente para apenas as partições físicas relevantes](how-to-query-container.md#in-partition-query) , incluindo a chave de partição no predicado de filtro.

Se a maioria das solicitações de carga de trabalho for consultas e a maioria das suas consultas tiver um filtro de igualdade na mesma propriedade, essa propriedade poderá ser uma boa opção de chave de partição. Por exemplo, se você executar com frequência uma consulta que filtra em `UserID` e, em seguida, selecionar `UserID` como a chave de partição reduziria o número de [consultas entre partições](how-to-query-container.md#avoiding-cross-partition-queries).

No entanto, se o contêiner for pequeno, provavelmente você não terá partições físicas suficientes para precisar se preocupar com o impacto no desempenho de consultas entre partições. A maioria dos contêineres pequenos no Azure Cosmos DB requer apenas uma ou duas partições físicas.

Se o contêiner puder aumentar para mais de algumas partições físicas, você deverá selecionar uma chave de partição que minimize as consultas entre partições. Seu contêiner exigirá mais do que algumas partições físicas quando qualquer uma das seguintes opções for verdadeira:

* Seu contêiner terá mais de 30.000 RU provisionados
* Seu contêiner armazenará mais de 100 GB de dados

## <a name="using-item-id-as-the-partition-key"></a>Usando a ID do item como a chave de partição

Se o contêiner tiver uma propriedade que tenha uma ampla gama de valores possíveis, provavelmente será uma ótima opção de chave de partição. Um exemplo possível de tal propriedade é a *ID do item*. Para contêineres de leitura intensa pequenos ou contêineres de gravação pesada de qualquer tamanho, a *ID do item* é naturalmente uma ótima opção para a chave de partição.

Há garantia de que a *ID do item* de Propriedade do sistema exista em cada item em seu contêiner Cosmos. Você pode ter outras propriedades que representam uma ID lógica do seu item. Em muitos casos, essas também são ótimas opções de chave de partição pelos mesmos motivos da *ID do item*.

A *ID do item* é uma ótima opção de chave de partição pelos seguintes motivos:

* Há uma grande variedade de valores possíveis (uma ID de *Item* exclusiva por item).
* Como há uma ID de *Item* exclusiva por item, a *ID do item* faz um ótimo trabalho em balanceamento uniforme de consumo e armazenamento de dados em ru.
* Você pode facilmente fazer leituras de ponto eficientes, pois você sempre saberá a chave de partição de um item se souber sua *ID de item*.

Algumas coisas a serem consideradas ao selecionar a *ID do item* como a chave de partição incluem:

* Se a *ID do item* for a chave de partição, ela se tornará um identificador exclusivo em todo o seu contêiner. Você não poderá ter itens com uma *ID de item*duplicada.
* Se você tiver um contêiner de leitura intensa que tenha muitas [partições físicas](partition-data.md#physical-partitions), as consultas serão mais eficientes se tiverem um filtro de igualdade com a ID do *Item*.
* Você não pode executar procedimentos armazenados ou gatilhos em várias partições lógicas.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [particionamento e dimensionamento horizontal em Azure Cosmos DB](partition-data.md).
* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre a [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
