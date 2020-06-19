---
title: Configurar uma conexão a uma conta do Cosmos DB usando uma identidade gerenciada (versão prévia)
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma conexão de indexador a uma conta do Cosmos DB usando uma identidade gerenciada (versão prévia)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 3524f55f70ff42bd5ff800fb2bd7ab7b0e732596
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663284"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>Configurar uma conexão do indexador a um banco de dados do Cosmos DB usando uma identidade gerenciada (versão prévia)

> [!IMPORTANT] 
> No momento, o suporte para configurar uma conexão a uma fonte de dados usando uma identidade gerenciada está em versão prévia pública restrita. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção.
> Você pode solicitar acesso à versão prévia preenchendo [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).

Esta página descreve como configurar uma conexão de indexador a um banco de dados do Cosmos DB do Azure usando uma identidade gerenciada, em vez de fornecer credenciais na cadeia de conexão do objeto da fonte de dados.

Antes de saber mais sobre esse recurso, é recomendável compreender o que é um indexador e como configurá-lo para a fonte de dados. Encontre mais informações nos links a seguir:
* [Visão geral do indexador](search-indexer-overview.md)
* [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Configurar uma conexão usando uma identidade gerenciada

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – Habilitar a identidade gerenciada atribuída ao sistema

Quando uma identidade gerenciada atribuída pelo sistema é habilitada, o Azure cria uma identidade para o serviço de pesquisa que pode ser usada para autenticar outros serviços do Azure no mesmo locatário e assinatura. Use essa identidade em atribuições de RBAC (controle de acesso baseado em função) que permitem o acesso aos dados durante a indexação.

![Habilitar a identidade gerenciada atribuída ao sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ativar a identidade gerenciada atribuída ao sistema")

Depois de selecionar **Salvar**, você verá uma ID do objeto que foi atribuída ao serviço de pesquisa.

![ID do objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")
 
### <a name="2---add-a-role-assignment"></a>2 – Adicionar uma atribuição de função

Nesta etapa, você dará permissão ao seu serviço do Azure Cognitive Search para ler dados do banco de dados do Cosmos DB.

1. No portal do Azure, navegue até a conta de armazenamento do Cosmos DB que contém os dados que você deseja indexar.
2. Selecione **Controle de Acesso (IAM)**
3. Selecione **Adicionar** > **Adicionar atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Adicionar atribuição de função")

4. Selecione **Função de Leitor de Conta do Cosmos DB**
5. Deixe **Atribuir acesso a** como **Usuário, grupo ou entidade de serviço do Azure AD**
6. Pesquise pelo serviço de pesquisa, selecione-o e, em seguida, selecione **Salvar**

    ![Adicionar a atribuição de função Acesso a Dados e Leitor](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Adicionar a atribuição de função Acesso a Dados e Leitor")

### <a name="3---create-the-data-source"></a>3 – Criar a fonte de dados

A **fonte de dados** especifica os dados no índice, nas credenciais e nas políticas para identificação de alterações nos dados (como documentos modificados ou excluídos em sua coleção). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Ao usar identidades gerenciadas para autenticar a fonte de dados, as **credenciais** não incluirão uma chave de conta.

Exemplo de como criar um objeto de fonte de dados do Cosmos DB usando a [API REST](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **name** | Obrigatórios. Escolha qualquer nome para representar o objeto da fonte de dados. |
|**tipo**| Obrigatórios. Deve ser `cosmosdb`. |
|**credentials** | Obrigatórios. <br/><br/>Ao se conectar usando uma identidade gerenciada, o formato de **credenciais** deve ser: *Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>O formato de ResourceId: *ResourceId=/subscriptions/**sua ID de assinatura**/resourceGroups/**nome do seu grupo de recursos**/providers/Microsoft.DocumentDB/databaseAccounts/**nome da sua conta do cosmos db**/;*<br/><br/>Para coleções SQL, a cadeia de conexão não requer um ApiKind.<br/><br/>Para coleções do MongoDB, adicione **ApiKind=MongoDb** à cadeia de conexão. <br/><br/>Para gráficos Gremlin e tabelas Cassandra, inscreva-se na [versão prévia restrita do indexador](https://aka.ms/azure-cognitive-search/indexer-preview) para obter acesso à versão prévia e informações sobre como formatar as credenciais.<br/>|
| **contêiner** | Contém os seguintes elementos: <br/>**nome**: Obrigatórios. Especifique a ID da coleção do banco de dados a ser indexada.<br/>**query**: Opcional. Você pode especificar uma consulta para nivelar um documento JSON arbitrário, criando um esquema nivelado que o Azure Cognitive Search possa indexar.<br/>Para a API do MongoDB, API do Gremlin e API do Cassandra, não há suporte para consultas. |
| **dataChangeDetectionPolicy** | Recomendadas |
|**dataDeletionDetectionPolicy** | Opcional |

O portal do Azure e o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) também oferecem suporte à cadeia de conexão de identidades gerenciadas. O portal do Azure requer um sinalizador de recurso que será fornecido a você ao se inscrever na visualização usando o link na parte superior desta página. 

### <a name="4---create-the-index"></a>4 – Criar o índice

O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Confira como criar um índice com um campo `booktitle` pesquisável:

```
POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 – Criar o indexador

Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador.

Exemplo de definição de indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir as agendas do indexador, confira [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Confira também

Para saber mais sobre os indexadores do Cosmos DB:
* [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)
