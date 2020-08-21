---
title: Parametrizar serviços vinculados no Azure Data Factory
description: Saiba como parametrizar serviços vinculados no Azure Data Factory e passar valores dinâmicos em tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: b3aadab1b4af80f98c57a279b69606a02846e996
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716820"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar serviços vinculados no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Agora é possível parametrizar um serviço vinculado e passar valores dinâmicos em tempo de execução. Por exemplo, se você quiser se conectar a bancos de dados diferentes no mesmo SQL Server lógico, agora poderá parametrizar o nome do banco de dados na definição de serviço vinculado. Isso impede que você precise criar um serviço vinculado para cada banco de dados no SQL Server lógico. Também é possível parametrizar outras propriedades na definição de serviço vinculado - por exemplo, *Nome de usuário.*

Você pode usar a interface do usuário do Data Factory no portal do Azure ou uma interface de programação para parametrizar os serviços vinculados.

> [!TIP]
> É recomendável não parametrizar senhas ou segredos. Armazene todas as cadeias de conexão no Azure Key Vault e defina o *Nome secreto*.

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Armazenamento de dados com suporte

Neste momento, há suporte para a parametrização do serviço vinculado na interface do usuário do Data Factory para os repositórios de dados a seguir. Para todos os outros armazenamentos de dados, é possível parametrizar o serviço vinculado selecionando o ícone de **Código** na guia **Conexões** e usando o editor JSON.

- Amazon Redshift
- Azure Cosmos DB (API do SQL)
- Banco de Dados do Azure para MySQL
- Banco de Dados SQL do Azure
- Azure Synapse Analytics (antigo SQL DW)
- MySQL
- Oracle
- SQL Server
-  HTTP Genérico
- REST Genérico

## <a name="data-factory-ui"></a>IU do Data Factory

![Adicionar conteúdo dinâmico à definição de Serviço Vinculado](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Criar um novo parâmetro](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
