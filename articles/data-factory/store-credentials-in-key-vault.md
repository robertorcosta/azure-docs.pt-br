---
title: Armazenar credenciais no Azure Key Vault
description: Saiba como armazenar as credenciais para os armazenamentos de dados usados em um Azure Key Vault, de modo que o Azure Data Factory possa recuperá-las automaticamente em tempo de execução.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 1e2458fa125ee4d223ef46001534fb7ae72b805e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684013"
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados e computar em um [Azure Key Vault](../key-vault/key-vault-overview.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que usa o armazenamento de dados/computação.

No momento, todos os tipos de atividade, exceto a atividade personalizada suportam esse recurso. Para configuração de conector especificamente, verifique a seção "propriedades do serviço vinculadas" em [cada tópico conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende da identidade gerenciada data factory. Saiba como ele funciona da [identidade gerenciada para o data Factory](data-factory-service-identity.md) e certifique-se de que seu data Factory tenha um associado.

## <a name="steps"></a>Etapas

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. **Recupere data Factory identidade gerenciada** copiando o valor de "ID do aplicativo de identidade gerenciada" gerado junto com sua fábrica. Se você usar a interface do usuário de criação do ADF, a ID do aplicativo de identidade gerenciada será mostrada na janela de criação do serviço vinculado Azure Key Vault; Você também pode recuperá-lo de portal do Azure, consulte [recuperar data Factory identidade gerenciada](data-factory-service-identity.md#retrieve-managed-identity).
2. **Conceda o acesso de identidade gerenciada à sua Azure Key Vault.** Em seu cofre de chaves-políticas de acesso de >-> Adicionar New-> Pesquise essa ID de aplicativo de identidade gerenciada para conceder permissão **Get** no menu suspenso permissões secretas. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. **Crie um serviço vinculado que aponte para seu Azure Key Vault.** Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. **Crie um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves.** Consulte [fazer referência a segredo armazenado no cofre de chaves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifique a URL (nome DNS) do Azure Key Vault. | Sim |

**Usando a criação da interface do usuário:**

Clique em **Conexões** -> **Serviços vinculados** ->  **+ Novo** -> procure "Azure Key Vault":

![Pesquisar AKV](media/store-credentials-in-key-vault/search-akv.png)

Selecione o Azure Key Vault provisionado onde as credenciais são armazenadas. Você pode fazer uma **Conexão de teste** para garantir que sua conexão AKV seja válida. 

![Configurar AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Exemplo de JSON:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Fazer referência a segredo armazenado no cofre de chaves

As propriedades a seguir têm suporte quando você configura um campo no serviço vinculado, referenciando um segredo do cofre de chaves:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do campo deve ser definida como: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome do segredo no Azure Key Vault. | Sim |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificada, a versão mais recente do segredo será sempre usada.<br/>Se especificada, a versão especificada será sempre usada.| Não |
| store | Refere-se a um serviço vinculado do Azure Key Vault que você usa para armazenar a credencial. | Sim |

**Usando a criação da interface do usuário:**

Selecione **Azure Key Vault** para os campos secretos ao criar a conexão para seu armazenamento de dados/computação. Selecione o serviço vinculado do Azure Key Vault provisionados e forneça o **Nome secreto**. Opcionalmente, você pode fornecer uma versão do segredo também. 

>[!TIP]
>Para os conectores que usam a cadeia de conexão no serviço vinculado, como SQL Server, armazenamento de BLOBs, etc., você pode optar por armazenar apenas o campo secreto, por exemplo, senha em AKV, ou armazenar a cadeia de conexão inteira em AKV. Você pode encontrar ambas as opções na interface do usuário.

![Configurar segredo do AKV](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Exemplo de JSON: (consulte a seção "senha")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
