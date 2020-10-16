---
title: Conexões do indexador por meio de um ponto de extremidade privado
titleSuffix: Azure Cognitive Search
description: Configure as conexões do indexador para acessar o conteúdo de outros recursos do Azure que são protegidos por meio de um ponto de extremidade privado.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: ef8b3865b0914c0d06ff69d20396f1ff368642bc
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102720"
---
# <a name="indexer-connections-through-a-private-endpoint-azure-cognitive-search"></a>Conexões do indexador por meio de um ponto de extremidade privado (Azure Pesquisa Cognitiva)

Muitos recursos do Azure (como contas de armazenamento do Azure) podem ser configurados para aceitar conexões de uma lista específica de redes virtuais e recusar conexões externas que se originam de uma rede pública. Se você estiver usando um indexador para indexar dados no Azure Pesquisa Cognitiva e sua fonte de dados estiver em uma rede privada, você poderá criar uma conexão de [ponto de extremidade privada](../private-link/private-endpoint-overview.md) (de saída) para alcançar os dados.

Para usar esse método de conexão do indexador, há dois requisitos:

+ O recurso do Azure que fornece conteúdo ou código deve ser registrado anteriormente com o [serviço de link privado do Azure](https://azure.microsoft.com/services/private-link/).

+ O serviço de Pesquisa Cognitiva do Azure deve ser básico ou superior (não disponível na camada gratuita). Além disso, para indexadores que têm um qualificable, o serviço de pesquisa deve ser S2 ou superior. Para obter mais informações, consulte [limites de serviço](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>APIs de gerenciamento de recursos de link privado compartilhado

Pontos de extremidade privados de recursos protegidos que são criados por meio de APIs de Pesquisa Cognitiva do Azure são chamados de *recursos de link privado compartilhado* porque você está "compartilhando" o acesso a um recurso (como uma conta de armazenamento) que foi integrado ao [serviço de link privado do Azure](https://azure.microsoft.com/services/private-link/).

Por meio de sua API REST de gerenciamento, o Azure Pesquisa Cognitiva fornece uma operação [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) que você pode usar para configurar o acesso de um indexador de pesquisa cognitiva do Azure.

As conexões de ponto de extremidade privado para alguns recursos só podem ser criadas com a versão de visualização da API de gerenciamento de pesquisa ( `2020-08-01-Preview` ou posterior), indicada com a marca "Preview" na tabela a seguir. Recursos sem a marca "visualização" podem ser criados usando a versão de API de visualização ou de disponibilidade geral ( `2020-08-01` ou posterior).

Veja a seguir a lista de recursos do Azure para os quais os pontos de extremidade privados de saída podem ser criados a partir do Azure Pesquisa Cognitiva. `groupId` listado na tabela abaixo precisa ser usado exatamente (diferenciando maiúsculas de minúsculas) na API para criar um recurso de link privado compartilhado.

| Recursos do Azure | ID do Grupo |
| --- | --- |
| Armazenamento do Azure-Blob (ou) ADLS Gen 2 | `blob`|
| Armazenamento do Azure-tabelas | `table`|
| API do Azure Cosmos DB-SQL | `Sql`|
| Banco de Dados SQL do Azure | `sqlServer`|
| Banco de dados do Azure para MySQL (visualização) | `mysqlServer`|
| Cofre de Chave do Azure | `vault` |
| Azure Functions (versão prévia) | `sites` |

A lista de recursos do Azure para os quais há suporte para conexões de ponto de extremidade privadas de saída também pode ser consultada usando a [API de lista com suporte](/rest/api/searchmanagement/privatelinkresources/listsupported).

Neste artigo, uma combinação de [ARMClient](https://github.com/projectkudu/ARMClient) e [postmaster](https://www.postman.com/) é usada para demonstrar as chamadas à API REST.

> [!NOTE]
> Ao longo deste artigo, suponha que o nome do serviço de pesquisa é __contoso-Search,__ que existe no grupo de recursos __contoso__ de uma assinatura com a ID de assinatura __00000000-0000-0000-0000-000000000000__. A ID de recurso deste serviço de pesquisa será `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

O restante dos exemplos mostrará como o serviço de __pesquisa da Contoso__ pode ser configurado para que seus indexadores possam acessar dados da conta de armazenamento seguro `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Protegendo sua conta de armazenamento

Configure a conta de armazenamento para [permitir o acesso somente de sub-redes específicas](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Na portal do Azure, se você marcar essa opção e deixar o conjunto vazio, significa que nenhum tráfego de qualquer rede virtual é permitido.

   ![Acesso à Rede Virtual](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Acesso à Rede Virtual")

> [!NOTE]
> A [abordagem de serviço confiável da Microsoft](../storage/common/storage-network-security.md#trusted-microsoft-services) pode ser usada para ignorar as restrições de rede virtual ou IP nessa conta de armazenamento e pode permitir que o serviço de pesquisa acesse dados na conta de armazenamento, conforme descrito em [acesso do indexador ao armazenamento do Azure usando a exceção de serviço confiável ](search-indexer-howto-access-trusted-service-exception.md). No entanto, ao usar essa abordagem de comunicação entre o Azure Pesquisa Cognitiva e a conta de armazenamento ocorre por meio do endereço IP público da conta de armazenamento, na rede de backbone seguro da Microsoft.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Etapa 1: criar um recurso de link privado compartilhado para a conta de armazenamento

Faça a seguinte chamada à API para solicitar que o Azure Pesquisa Cognitiva crie uma conexão de ponto de extremidade privada de saída para a conta de armazenamento

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

O conteúdo do `create-pe.json` arquivo (que representa o corpo da solicitação para a API) são os seguintes:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Uma `202 Accepted` resposta é retornada em caso de êxito – o processo de criação de um ponto de extremidade privado de saída é uma operação de execução longa (assíncrona). Ele envolve a implantação dos seguintes recursos-

1. Um ponto de extremidade privado alocado com um endereço IP privado em um `"Pending"` estado. O endereço IP privado é obtido do espaço de endereço alocado para a rede virtual do ambiente de execução do indexador particular específico do serviço de pesquisa. Após a aprovação do ponto de extremidade privado, qualquer comunicação do Azure Pesquisa Cognitiva à conta de armazenamento é originada do endereço IP privado e de um canal de link privado seguro.
2. Uma zona DNS privada para o tipo de recurso, com base no `groupId` . Isso garantirá que qualquer pesquisa de DNS para o recurso privado utilize o endereço IP associado ao ponto de extremidade privado.

Certifique-se de especificar o correto `groupId` para o tipo de recurso para o qual você está criando o ponto de extremidade privado. Qualquer incompatibilidade resultará em uma mensagem de resposta não bem-sucedida.

Como todas as operações assíncronas do Azure, a `PUT` chamada retorna um `Azure-AsyncOperation` valor de cabeçalho que terá a seguinte aparência:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Esse URI pode ser sondado periodicamente para obter o status da operação. Recomendamos aguardar até que o status da operação de recurso de link privado compartilhado tenha atingido um estado de terminal (ou seja, `succeeded` ) antes de continuar.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Etapa 2a: aprovar a conexão de ponto de extremidade particular para a conta de armazenamento

> [!NOTE]
> Esta seção usa portal do Azure para percorrer o fluxo de aprovação de um ponto de extremidade privado para o armazenamento. A [API REST](/rest/api/storagerp/privateendpointconnections) disponível por meio do RP (provedor de recursos de armazenamento) também pode ser usada.
>
> Outros provedores, como o CosmosDB, o SQL Server do Azure, etc., também oferecem APIs RP semelhantes para gerenciar conexões de ponto de extremidade privadas.

Navegue até a guia "**conexões de ponto de extremidade privado**" da conta de armazenamento em portal do Azure. Deve haver uma solicitação para uma conexão de ponto de extremidade privada, com a mensagem de solicitação da chamada à API anterior (quando a operação assíncrona tiver sido __bem-sucedida__).

   ![Aprovação de ponto de extremidade particular](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Aprovação de ponto de extremidade particular")

Selecione o ponto de extremidade privado que foi criado pelo Azure Pesquisa Cognitiva (use a coluna "ponto de extremidade privado" para identificar a conexão de ponto de extremidade particular pelo nome especificado na API anterior) e escolha "aprovar", com uma mensagem apropriada (a mensagem não é significativa). Verifique se a conexão de ponto de extremidade privada aparece da seguinte maneira (pode em qualquer lugar de 1-2 minutos para que o status seja atualizado no Portal)

![Ponto de extremidade privado aprovado](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Ponto de extremidade privado aprovado")

Depois que a solicitação de conexão de ponto de extremidade particular é aprovada, isso significa que o tráfego é *capaz* de fluir pelo ponto de extremidade privado. Depois que o ponto de extremidade privado for aprovado, o Azure Pesquisa Cognitiva criará os mapeamentos de zona DNS necessários na zona DNS criada para ele.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Etapa 2B: consultar o status do recurso de link privado compartilhado

 Para confirmar se o recurso de link privado compartilhado foi atualizado após a aprovação, obtenha seu status usando a [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Se o `properties.provisioningState` do recurso for `Succeeded` e `properties.status` for `Approved` , significa que o recurso de link privado compartilhado é funcional e indexadores podem ser configurados para se comunicarem por meio do ponto de extremidade privado.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Etapa 3: configurar o indexador para ser executado no ambiente privado

> [!NOTE]
> Esta etapa pode ser executada mesmo antes que a conexão de ponto de extremidade particular seja aprovada. Até que a conexão de ponto de extremidade particular seja aprovada, qualquer indexador que tentar se comunicar com um recurso seguro (como a conta de armazenamento) acabará em um estado de falha transitória. Novos indexadores não serão criados. Assim que a conexão de ponto de extremidade particular for aprovada, os indexadores poderão acessar a conta de armazenamento particular.

1. [Crie uma fonte de dados](/rest/api/searchservice/create-data-source) que aponte para a conta de armazenamento seguro e um contêiner apropriado dentro da conta de armazenamento. O seguinte mostra essa solicitação no postmaster.
![Criar fonte de dados](media\search-indexer-howto-secure-access\create-ds.png "Criação da fonte de dados")

1. Da mesma forma, [crie um índice](/rest/api/searchservice/create-index) e, opcionalmente, [crie um conconhecimentor](/rest/api/searchservice/create-skillset) usando a API REST.

1. [Crie um indexador](/rest/api/searchservice/create-indexer) que aponte para a fonte de dados, o índice e o skillset criados acima. Além disso, Force o indexador a ser executado no ambiente de execução particular, definindo a propriedade de configuração do indexador `executionEnvironment` como `"Private"` .
![Criar indexador](media\search-indexer-howto-secure-access\create-idr.png "Criação do indexador")

O indexador deve ser criado com êxito e deve estar fazendo o andamento da indexação do conteúdo da conta de armazenamento na conexão de ponto de extremidade privada. O status do indexador pode ser monitorado usando a [API de status do indexador](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Se você já tiver indexadores existentes, poderá simplesmente atualizá-los por meio da [API Put](/rest/api/searchservice/create-indexer) para definir `executionEnvironment` como `"Private"` .

## <a name="troubleshooting-issues"></a>Solução de problemas

- Ao criar um indexador, se a criação falhar com uma mensagem de erro semelhante a "as credenciais de fonte de dados são inválidas", isso significa que a conexão de ponto de extremidade particular não foi *aprovada* ou não está funcionando.
Obtenha o status do recurso de link privado compartilhado usando a [API Get](/rest/api/searchmanagement/sharedprivatelinkresources/get). Se tiver sido *aprovado* , verifique o `properties.provisioningState` do recurso. Se for `Incomplete` , isso significa que algumas das dependências subjacentes do recurso falharam ao provisionar – emita novamente a `PUT` solicitação para "recriar" o recurso de link privado compartilhado que deve corrigir o problema. Uma nova aprovação pode ser necessária-Verifique o status do recurso mais uma vez para verificar.
- Se o indexador for criado sem definir seu `executionEnvironment` , a criação do indexador poderá ser bem-sucedida, mas seu histórico de execução mostrará que as execuções do indexador não são bem-sucedidas. Você deve [atualizar o indexador](/rest/api/searchservice/update-indexer) para especificar o ambiente de execução.
- Se o indexador for criado sem definir o `executionEnvironment` e ele for executado com êxito, significa que o Azure pesquisa cognitiva decidiu que seu ambiente de execução é o ambiente "privado" específico do serviço de pesquisa. No entanto, isso pode ser alterado com base em uma variedade de fatores (recursos consumidos pelo indexador, a carga no serviço de pesquisa e assim por diante) e pode falhar em um ponto posterior – é altamente recomendável que você defina o `executionEnvironment` como `"Private"` para garantir que ele não falhará no futuro.
- [Cotas e limites](search-limits-quotas-capacity.md) determinam quantos recursos de vínculo privado compartilhado podem ser criados e dependem do SKU do serviço de pesquisa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre pontos de extremidade privados:

- [O que são pontos de extremidade privados?](../private-link/private-endpoint-overview.md)
- [Configurações de DNS necessárias para pontos de extremidade privados](../private-link/private-endpoint-dns.md)