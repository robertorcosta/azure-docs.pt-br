---
title: Criar cofres de Serviços de recuperação usando a API REST
description: Neste artigo, saiba como gerenciar operações de backup e restauração do backup de VM do Azure usando a API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74173408"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Criar o Cofre de Serviços de Recuperação do Azure usando a API REST

As etapas para criar uma Área Segura dos Serviços de Recuperação do Azure usando a API REST são descritas na [documentação da API REST do Vault de criação](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Vamos usar este documento como referência para criar um cofre chamado "testVault" em "Oeste dos EUA".

Para criar ou atualizar um cofre dos serviços de recuperação do Azure, use a seguinte operação *PUT*.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Criar uma solicitação

Para criar a solicitação *PUT*, o parâmetro `{subscription-id}` é obrigatório. Se você tiver várias assinaturas, consulte [trabalhando com várias assinaturas](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Defina `{resourceGroupName}` e `{vaultName}` para seus recursos, junto com o parâmetro `api-version`. Este artigo usa `api-version=2016-06-01`.

Os cabeçalhos a seguir são necessários:

| Cabeçalho da solicitação   | Descrição |
|------------------|-----------------|
| *Tipo de Conteúdo:*  | Obrigatórios. Defina como `application/json`. |
| *Autorização:* | Obrigatórios. Defina como um  [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer` válido. |

Para saber mais sobre como criar a solicitação, confira [Componentes de uma solicitação/resposta de API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

As definições comuns a seguir são usadas para criar um corpo de solicitação:

|Nome  |Obrigatório  |Type  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  eTag Opcional       |
|local     |  true       |String         |   Localização do recurso      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|sku     |         |  [SKU](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifica o identificador exclusivo do sistema para cada recurso do Azure     |
|marcas     |         | Objeto        |     Marcações de recursos    |

Observe que o nome do vault e o nome do grupo de recursos são fornecidos no PUT URI. O corpo da solicitação define o local.

## <a name="example-request-body"></a>Exemplo do corpo de solicitação

O corpo do exemplo a seguir é usado para criar um cofre no "Oeste dos EUA". Especifique o local. A SKU é sempre "Standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Respostas

Há duas respostas bem-sucedidas para a operação para criar ou atualizar um cofre do Recovery Services:

|Nome  |Type  |Descrição  |
|---------|---------|---------|
|200 OK     |   [Armazenadas](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Criado     | [Armazenadas](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Criado      |

Para saber mais sobre as respostas da API REST, veja [Processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Exemplo de resposta

Uma resposta condensada *201 criada* do corpo da solicitação de exemplo anterior mostra um *id* foi atribuído e o *provisioningState* é *bem-sucedido*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

[Crie uma política de backup para fazer backup de uma VM do Azure neste Vault](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs REST do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
