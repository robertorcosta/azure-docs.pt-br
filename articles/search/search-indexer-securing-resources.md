---
title: Acessar recursos do indexador com segurança
titleSuffix: Azure Cognitive Search
description: Visão geral conceitual das opções de segurança de nível de rede para o acesso a dados do Azure por indexadores no Azure Pesquisa Cognitiva.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 5075c4858f9584cb19442e19d9009d46d0e00ff8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463399"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Acesso do indexador a fontes de dados usando recursos de segurança de rede do Azure

Os indexadores do Azure Pesquisa Cognitiva podem fazer chamadas de saída para vários recursos do Azure durante a execução. Este artigo explica os conceitos por trás do acesso do indexador aos recursos quando esses recursos são protegidos por firewalls IP, pontos de extremidade privados e outros mecanismos de segurança em nível de rede. Os tipos de recursos possíveis que um indexador pode acessar em uma execução típica são listados na tabela a seguir.

| Recurso | Finalidade dentro da execução do indexador |
| --- | --- |
| Armazenamento do Azure (BLOBs, tabelas, ADLS Gen 2) | Fonte de dados |
| Armazenamento do Azure (BLOBs, tabelas) | Habilidades (caching de documentos aprimorados e armazenamento de projeções de armazenamento de conhecimento) |
| Azure Cosmos DB (várias APIs) | Fonte de dados |
| Banco de Dados SQL do Azure | Fonte de dados |
| SQL Server em VMs de IaaS do Azure | Fonte de dados |
| Instâncias gerenciadas do SQL | Fonte de dados |
| Funções do Azure | Host para habilidades de API Web personalizadas |
| Serviços Cognitivos | Anexado ao skillset que será usado para faturar o enriquecimento além do limite de 20 documentos gratuitos |

> [!NOTE]
> O recurso de serviço cognitiva anexado a um qualificable é usado para cobrança, com base nos aprimoramentos executados e gravados no índice de pesquisa. Ele não é usado para acessar o API de Serviços Cognitivos. O acesso do pipeline de enriquecimento de um indexador para API de Serviços Cognitivos ocorre por meio de um canal de comunicação seguro, em que os dados são criptografados fortemente em trânsito e nunca são armazenados em repouso.

Os clientes podem proteger esses recursos por meio de vários mecanismos de isolamento de rede oferecidos pelo Azure. Com exceção do recurso de serviço cognitiva, os indexadores têm a capacidade limitada de acessar todos os outros recursos, mesmo se eles forem isolados de rede contornados na tabela a seguir.

| Recurso | Restrição de IP | Ponto de extremidade privado |
| --- | --- | ---- |
| Armazenamento do Azure (BLOBs, tabelas, ADLS Gen 2) | Com suporte apenas se a conta de armazenamento e o serviço de pesquisa estiverem em regiões diferentes | Com suporte |
| API do Azure Cosmos DB-SQL | Com suporte | Com suporte |
| API Azure Cosmos DB-Cassandra, Mongo e Gremlin | Com suporte | Sem suporte |
| Banco de Dados SQL do Azure | Com suporte | Com suporte |
| SQL Server em VMs de IaaS do Azure | Com suporte | N/D |
| Instâncias gerenciadas do SQL | Com suporte | N/D |
| Funções do Azure | Com suporte | Com suporte, somente para determinados SKUs do Azure Functions |

> [!NOTE]
> Além das opções listadas acima, para contas de armazenamento protegidas de rede do Azure, os clientes podem aproveitar o fato de que o Azure Pesquisa Cognitiva é um [serviço da Microsoft confiável](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services). Isso significa que um serviço de pesquisa específico pode ignorar restrições de rede virtual ou IP na conta de armazenamento e pode acessar dados na conta de armazenamento, se o controle de acesso baseado em função apropriado estiver habilitado na conta de armazenamento. Os detalhes estão disponíveis no [Guia de instruções](search-indexer-howto-access-trusted-service-exception.md). Essa opção pode ser utilizada em vez de uma rota de restrição de IP, caso a conta de armazenamento ou o serviço de pesquisa não possa ser movido para uma região diferente.

Ao escolher qual mecanismo de acesso seguro deve ser usado por um indexador, considere as seguintes restrições:

- Os [pontos de extremidade de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) não terão suporte para nenhum recurso do Azure.
- Um serviço de pesquisa não pode ser provisionado em uma rede virtual específica-essa funcionalidade não será oferecida pelo Azure Pesquisa Cognitiva.
- Quando os indexadores utilizam pontos de extremidade privados (de saída) para acessar recursos, podem ser aplicados [encargos de link privado](https://azure.microsoft.com/pricing/details/search/) adicionais.

## <a name="indexer-execution-environment"></a>Ambiente de execução do indexador

Os indexadores do Azure Pesquisa Cognitiva são capazes de extrair conteúdo com eficiência de fontes de dados, adicionando aprimoramentos ao conteúdo extraído, opcionalmente gerando projeções antes de gravar os resultados no índice de pesquisa. Dependendo do número de responsabilidades atribuídas a um indexador, ele pode ser executado em um dos dois ambientes:

- Um ambiente privado para um serviço de pesquisa específico. Os indexadores em execução nesses ambientes compartilham recursos com outras cargas de trabalho (como outras cargas de trabalho de indexação ou consulta iniciadas pelo cliente). Normalmente, somente os indexadores que não exigem muitos recursos (por exemplo, não usam um contratador de habilidades) são executados nesse ambiente.
- Um ambiente multilocatário que hospeda indexadores que são um recurso que se requerendo, como aqueles com um conjunto de qualificações. Recursos que exigem a execução desse ambiente para oferecer um desempenho ideal durante a verificação de que os recursos do serviço de pesquisa estão disponíveis para outras cargas de trabalho. Esse ambiente multilocatário é gerenciado e protegido pelo Azure Pesquisa Cognitiva, sem custo adicional para o cliente.

Para qualquer execução de indexador específico, o Azure Pesquisa Cognitiva determina o melhor ambiente no qual executar o indexador.

## <a name="granting-access-to-indexer-ip-ranges"></a>Concedendo acesso aos intervalos de IP do indexador

Se o recurso que o indexador está tentando acessar estiver restrito a apenas um determinado conjunto de intervalos de IP, você precisará expandir o conjunto para incluir os possíveis intervalos de IP dos quais uma solicitação de indexador pode se originar. Conforme mencionado acima, há dois ambientes possíveis em que os indexadores são executados e do qual as solicitações de acesso podem se originar. Você precisará adicionar os endereços IP de __ambos os__ ambientes para que o acesso do indexador funcione.

- Para obter o endereço IP do ambiente particular específico do serviço de pesquisa, `nslookup` (ou `ping` ) o FQDN (nome de domínio totalmente qualificado) do seu serviço de pesquisa. O FQDN de um serviço de pesquisa na nuvem pública, por exemplo, seria `<service-name>.search.windows.net` . Essas informações estão disponíveis no portal do Azure.
- Os endereços IP dos ambientes multilocatário estão disponíveis por meio da `AzureCognitiveSearch` marca de serviço. As [marcas de serviço do Azure](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) têm um intervalo publicado de endereços IP para cada serviço-isso está disponível por meio de uma [API de descoberta (versão prévia)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) ou um [arquivo JSON baixável](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). Em ambos os casos, os intervalos de IP são divididos por região. você pode escolher apenas os intervalos de IP atribuídos para a região em que o serviço de pesquisa é provisionado.

Para determinadas fontes de dados, a própria marca de serviço pode ser usada diretamente em vez de enumerar a lista de intervalos de IP (o endereço IP do serviço de pesquisa ainda precisa ser usado explicitamente). Essas fontes de dados restringem o acesso por meio da configuração de uma [regra de grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview), que oferece suporte nativo à adição de uma marca de serviço, ao contrário das regras de IP, como as oferecidas pelo armazenamento do Azure, COSMOSDB, SQL do Azure, etc `AzureCognitiveSearch`

- [SQL Server em VMs IaaS](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers#restrict-access-to-the-azure-cognitive-search)

- [Instâncias gerenciadas do SQL](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers#verify-nsg-rules)

Os detalhes são descritos no [Guia de instruções](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Concedendo acesso por meio de pontos de extremidade privados

Os indexadores podem utilizar [pontos de extremidade privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para acessar recursos, o acesso ao qual estão bloqueados para selecionar redes virtuais ou não têm nenhum acesso público habilitado.
Essa funcionalidade só está disponível para serviços pagos, com limites no número de pontos de extremidade privados que são criados. Os detalhes sobre os limites estão documentados na [página limites de Azure Search](search-limits-quotas-capacity.md).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Etapa 1: criar um ponto de extremidade privado para o recurso seguro

Os clientes devem chamar a operação de gerenciamento de pesquisa, [criar ou atualizar a API de *recurso de link privado compartilhado* ](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) para criar uma conexão de ponto de extremidade privada para seu recurso seguro (por exemplo, uma conta de armazenamento). O tráfego que passa por essa conexão de ponto de extremidade privada (de saída) se originará somente da rede virtual que está no ambiente de execução do indexador "particular" específico do serviço de pesquisa.

O Pesquisa Cognitiva do Azure validará que os chamadores dessa API têm permissões para aprovar solicitações de conexão de ponto de extremidade privado para o recurso seguro. Por exemplo, se você solicitar uma conexão de ponto de extremidade privada para uma conta de armazenamento à qual você não tem acesso, essa chamada será rejeitada.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Etapa 2: aprovar a conexão de ponto de extremidade particular

Quando a operação (assíncrona) que cria um recurso de link privado compartilhado for concluída, uma conexão de ponto de extremidade privado será criada em um estado "pendente". Nenhum tráfego flui pela conexão ainda.
O cliente espera, então, localizar essa solicitação em seu recurso seguro e "aprová-la". Normalmente, isso pode ser feito por meio do portal ou por meio da [API REST](https://docs.microsoft.com/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Etapa 3: forçar os indexadores a serem executados no ambiente "privado"

Um ponto de extremidade privado aprovado permite chamadas de saída do serviço de pesquisa para um recurso que tem alguma forma de restrições de acesso no nível da rede (por exemplo, uma fonte de dados da conta de armazenamento configurada para ser acessada somente de determinadas redes virtuais) para ter sucesso.
Isso significa que qualquer indexador capaz de acessar uma fonte de dados desse tipo no ponto de extremidade privado terá sucesso.
Se o ponto de extremidade privado não for aprovado, ou se o indexador não utilizar a conexão de ponto de extremidade particular, a execução do indexador terminará em `transientFailure` .

Para permitir que os indexadores acessem recursos por meio de conexões de ponto de extremidade privadas, é obrigatório definir o `executionEnvironment` do indexador como `"Private"` para garantir que todas as execuções do indexador possam utilizar o ponto de extremidade privado. Isso ocorre porque os pontos de extremidade privados são provisionados no ambiente específico do serviço de pesquisa particular.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Essas etapas são descritas mais detalhadamente no [Guia de instruções](search-indexer-howto-access-private.md).
Depois de ter um ponto de extremidade privado aprovado para um recurso, os indexadores que estão definidos para serem uma tentativa *privada* de obter acesso por meio da conexão de ponto de extremidade privado.

### <a name="limits"></a>Limites

Para garantir o desempenho ideal e a estabilidade do serviço de pesquisa, as restrições são impostas (por SKU do serviço de pesquisa) nas seguintes dimensões:

- Os tipos de indexadores que podem ser definidos como *particulares*.
- O número de recursos de vínculo privado compartilhado que podem ser criados.
- O número de tipos de recursos distintos para os quais os recursos de link privado compartilhado podem ser criados.

Esses limites são documentados em [limites de serviço](search-limits-quotas-capacity.md).
