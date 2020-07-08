---
title: Configurar uma conexão com uma conta de armazenamento usando uma identidade gerenciada (versão prévia)
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma conexão de indexador com uma conta de armazenamento do Azure usando uma identidade gerenciada (versão prévia)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: e6c766008faa6bbe53a4af69f7da9325cb9ff6a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559864"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>Configurar uma conexão com uma conta de armazenamento do Azure usando uma identidade gerenciada (versão prévia)

> [!IMPORTANT] 
> No momento, o suporte para configurar uma conexão com uma fonte de dados usando uma identidade gerenciada está em uma visualização pública restrita. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção.
> Você pode solicitar acesso à versão prévia preenchendo [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).

Esta página descreve como configurar uma conexão de indexador com uma conta de armazenamento do Azure usando uma identidade gerenciada, em vez de fornecer credenciais na cadeia de conexão do objeto da fonte de dados.

Antes de saber mais sobre esse recurso, é recomendável compreender o que é um indexador e como configurá-lo para a fonte de dados. Encontre mais informações nos links a seguir:
* [Visão geral do indexador](search-indexer-overview.md)
* [Indexador de blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexador do Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela do Azure](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Configurar a conexão

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Habilitar a identidade gerenciada atribuída ao sistema

Quando uma identidade gerenciada atribuída pelo sistema é habilitada, o Azure cria uma identidade para o serviço de pesquisa que pode ser usada para autenticar outros serviços do Azure no mesmo locatário e assinatura. Use essa identidade em atribuições de RBAC (controle de acesso baseado em função) que permitem o acesso aos dados durante a indexação.

![Habilitar a identidade gerenciada atribuída ao sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Habilitar a identidade gerenciada atribuída ao sistema")

Depois de selecionar **Salvar**, você verá uma ID do objeto que foi atribuída ao serviço de pesquisa.

![ID do objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")
 
### <a name="2---add-a-role-assignment"></a>2 - Adicionar uma atribuição de função

Nesta etapa, você dará permissão ao seu serviço do Azure Cognitive Search para ler dados da conta de armazenamento.

1. No portal do Azure, navegue até a conta de armazenamento que contém os dados que você deseja indexar.
2. Selecione **Controle de Acesso (IAM)**
3. Selecione **Adicionar** > **Adicionar atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-storage.png "Adicionar atribuição de função")

4. Selecione as funções apropriadas com base no tipo de conta de armazenamento que você deseja indexar:
    1. O armazenamento de blobs do Azure requer que você adicione o serviço de pesquisa às funções **Acesso a Dados e Leitor** e **Leitor de Dados do Blob de Armazenamento**.
    1. O Azure Data Lake Storage Gen2 requer que você adicione o serviço de pesquisa às funções **Acesso a Dados e Leitor** e **Leitor de Dados do Blob de Armazenamento**.
    1. O armazenamento de tabelas do Azure requer que você adicione o serviço de pesquisa apenas à função **Acesso a Dados e Leitor**.
5.  Deixe **Atribuir acesso a** como **Usuário, grupo ou entidade de serviço do Azure AD**
6.  Pesquise pelo serviço de pesquisa, selecione-o e, em seguida, selecione **Salvar**

    ![Adicionar a atribuição de função Acesso a Dados e Leitor](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Adicionar a atribuição de função Acesso a Dados e Leitor")

Observe que, ao se conectar ao armazenamento de blobs do Azure e ao Azure Data Lake Storage Gen2, você também deve adicionar a atribuição de função **Leitor de Dados do Blob de Armazenamento**.

![Adicionar atribuição de função Leitor de Dados do Blob de Armazenamento](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Adicionar atribuição de função Leitor de Dados do Blob de Armazenamento")

### <a name="3---create-the-data-source"></a>3 - Criar a fonte de dados

Ao indexar de uma conta de armazenamento, a fonte de dados deve ter as seguintes propriedades obrigatórias:

* **name** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.
* **tipo**
    * Armazenamento de blobs do Azure: `azureblob`
    * Armazenamento de tabelas do Azure: `azuretable`
    * Azure Data Lake Storage Gen2: **type** será fornecido quando você se inscrever na visualização usando [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **credentials**
    * Ao usar uma identidade gerenciada para autenticar, o formato **credentials** é diferente de quando não usa uma identidade gerenciada. Aqui, você fornecerá um ResourceId sem chave de conta ou senha. O ResourceId deve incluir a ID da assinatura da conta de armazenamento, o grupo de recursos da conta de armazenamento e o nome da conta de armazenamento.
    * Formato da identidade gerenciada: 
        * *ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.Storage/storageAccounts/**your storage account name**/;*
* **container** especifica um contêiner ou tabela na conta de armazenamento. Por padrão, todos os blobs no contêiner são recuperáveis. Se você quiser apenas blobs de índice em um diretório virtual específico, especifique o diretório usando o parâmetro opcional **query**.

Exemplo de como criar um objeto de fonte de dados de blob usando a [API REST](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

O portal do Azure e o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) também oferecem suporte à cadeia de conexão de identidades gerenciadas. O portal do Azure requer um sinalizador de recurso que será fornecido a você ao se inscrever na visualização usando o link na parte superior desta página. 

### <a name="4---create-the-index"></a>4 - Criar o índice

O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Veja como criar um índice com um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Criar o indexador

Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador.

Definição de indexador de exemplo para um indexador de blob:

    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir as agendas do indexador, consulte [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Confira também

Saiba mais sobre os indexadores de armazenamento do Azure:
* [Indexador de blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexador do Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela do Azure](search-howto-indexing-azure-tables.md)
