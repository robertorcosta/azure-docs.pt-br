---
title: Criar e recuperar atributos de uma chave no Azure Key Vault – CLI do Azure
description: Início Rápido que mostra como definir e recuperar uma chave do Azure Key Vault usando a CLI do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 19c4a966bb4099e70efea224127bc9b7292ffd0a
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940541"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Início Rápido: Definir e recuperar uma chave do Azure Key Vault usando a CLI do Azure

Neste guia de início rápido, você cria um cofre de chaves no Azure Key Vault com CLI do Azure. O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). A CLI do Azure é usada para criar e gerenciar recursos do Azure usando comandos ou scripts. Depois de concluir isso, você armazenará uma chave.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para saber mais sobre as opções de logon por meio da CLI, veja [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

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

## <a name="add-a-key-to-key-vault"></a>Adicionar uma chave ao Key Vault

Para adicionar uma chave ao cofre, basta executar algumas etapas adicionais. Essa chave pode ser usada por um aplicativo. 

Digite os comandos abaixo para criar um chamado **ExampleKey**:

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Agora, você pode referenciar essa chave que foi adicionada ao Azure Key Vault usando o respectivo URI. Use **'https://Contoso-Vault2.vault.azure.net/keys/ExampleKey '** para obter a versão atual. 

Para ver a chave armazenada anteriormente:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Agora, você criou um Key Vault, armazenou uma chave e a recuperou.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não forem mais necessários, você poderá usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados. Você pode excluir os recursos da seguinte maneira:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou uma chave nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira a referência dos [comandos az keyvault da CLI do Azure](/cli/azure/keyvault?view=azure-cli-latest)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)
