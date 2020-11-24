---
title: 'Início Rápido: Definir e recuperar um segredo do Azure Key Vault'
description: Início Rápido que mostra como definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 36688586cc0b9c94a07873bacfa6210f31695d36
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517233"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure

Neste guia de início rápido, você cria um cofre de chaves no Azure Key Vault com CLI do Azure. O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). A CLI do Azure é usada para criar e gerenciar recursos do Azure usando comandos ou scripts. Depois de concluído, você armazenará um segredo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este guia de início rápido exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, você criará um Key Vault no grupo de recursos criado na etapa anterior. Você precisará fornecer algumas informações:

- Neste início rápido, usamos **Contoso-vault2**. Você deve fornecer um nome exclusivo para o teste.
- Nome do grupo de recursos **ContosoResourceGroup**.
- O local **Leste dos EUA**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

A saída desse cmdlet mostra as propriedades do cofre de chaves criado recentemente. Anote as duas propriedades listadas abaixo:

- **Nome do cofre**: no exemplo, é **Contoso-Vault2**. Você usará esse nome para outros comandos do Key Vault.
- **URI do cofre**: no exemplo, é https://contoso-vault2.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Essa senha pode ser usada por um aplicativo. A senha será chamada **ExamplePassword** e armazenará o valor **hVFkk965BuUv** nela.

Digite os comandos abaixo para criar um segredo no Key Vault chamado **ExamplePassword** que armazenará o valor **hVFkk965BuUv**:

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

Agora, você pode fazer referência a essa senha que foi adicionada ao Azure Key Vault usando seu URI. Use **'https://Contoso-Vault2.vault.azure.net/secrets/ExamplePassword '** para obter a versão atual. 

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Agora, você criou um Key Vault, armazenou um segredo e o recuperou.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não forem mais necessários, você poderá usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados. Você pode excluir os recursos da seguinte maneira:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira a referência dos [comandos az keyvault da CLI do Azure](/cli/azure/keyvault?view=azure-cli-latest)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)
