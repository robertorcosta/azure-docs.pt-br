---
title: Armazenar credenciais no Azure Key Vault
description: Saiba como armazenar as credenciais para os armazenamentos de dados usados em um Azure Key Vault, de modo que o Azure Data Factory possa recuperá-las automaticamente em runtime.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: b5a181625488a57de4b878d13c01a8c90bf8785a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414671"
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Você pode armazenar credenciais para armazenamentos de dados e computar em um [Azure Key Vault](../key-vault/key-vault-overview.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que usa o armazenamento de dados/computação.

No momento, todos os tipos de atividade, exceto a atividade personalizada suportam esse recurso. Para configuração de conector especificamente, verifique a seção "propriedades do serviço vinculadas" em [cada tópico conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso se baseia na identidade gerenciada pela fábrica de dados. Saiba como ele funciona a partir da [identidade gerenciada para fábrica de dados](data-factory-service-identity.md) e certifique-se de que sua fábrica de dados tenha uma fábrica de dados associada.

## <a name="steps"></a>Etapas

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. **Recupere a identidade gerenciada da fábrica** de dados copiando o valor de "ID de objeto de identidade gerenciada" gerado junto com sua fábrica. Se você usar a UI de autoria do ADF, o ID do objeto de identidade gerenciado será mostrado na janela de criação de serviço selada do Azure Key Vault; você também pode recuperá-lo do portal Azure, consulte recuperar a [identidade gerenciada pela fábrica de dados](data-factory-service-identity.md#retrieve-managed-identity).
2. **Conceda o acesso de identidade gerenciado ao seu Cofre chave Azure.** Em seu cofre-chave - > políticas de acesso -> Adicionar política de acesso, pesquise essa identidade gerenciada para conceder permissão **Obter** permissões secretas de saque. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. **Crie um serviço vinculado apontando para o cofre de chaves do Azure.** Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. **Crie serviço vinculado ao armazenamento de dados, dentro do qual fazem referência ao segredo correspondente armazenado no cofre de chaves.** Consulte [fazer referência a segredo armazenado no cofre de chaves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifique a URL (nome DNS) do Azure Key Vault. | Sim |

**Usando a criação da interface do usuário:**

Selecione **conexões vinculadas** -> **serviços** -> **novos**. No novo serviço vinculado, procure e selecione "Azure Key Vault":

![Pesquisar o cofre de chaves do Azure](media/store-credentials-in-key-vault/search-akv.png)

Selecione o Azure Key Vault provisionado onde as credenciais são armazenadas. Você pode fazer uma **Conexão de teste** para garantir que sua conexão AKV seja válida. 

![Configurar o Azure Key Vault](media/store-credentials-in-key-vault/configure-akv.png)

**Exemplo json:**

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

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do campo deve ser definida como: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome secreto em Azure Key Vault. | Sim |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificada, a versão mais recente do segredo será sempre usada.<br/>Se especificada, a versão especificada será sempre usada.| Não |
| store | Refere-se a um serviço vinculado do Azure Key Vault que você usa para armazenar a credencial. | Sim |

**Usando a criação da interface do usuário:**

Selecione **Azure Key Vault** para os campos secretos ao criar a conexão para seu armazenamento de dados/computação. Selecione o serviço vinculado do Azure Key Vault provisionados e forneça o **Nome secreto**. Opcionalmente, você pode fornecer uma versão do segredo também. 

>[!TIP]
>Para conectores que usam a seqüência de conexões no serviço vinculado como SQL Server, Blob storage, etc., você pode escolher armazenar apenas o campo secreto, por exemplo, senha no AKV, ou para armazenar toda a cadeia de conexão no AKV. Você pode encontrar ambas as opções na ui.

![Configure o segredo do Cofre de Chaves do Azure](media/store-credentials-in-key-vault/configure-akv-secret.png)

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
