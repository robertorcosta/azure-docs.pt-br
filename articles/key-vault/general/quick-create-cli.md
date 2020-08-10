---
title: Guia de Início Rápido – Criar um Azure Key Vault com a CLI do Azure
description: Guia de início rápido que mostra como criar um Azure Key Vault usando a CLI do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: f3b9758d52c4be8e4d85c398f5ef1d0b3fae7e86
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541797"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Início Rápido: Criar um cofre de chaves usando a CLI do Azure

O Azure Key Vault é um serviço de nuvem que fornece um repositório seguro para [chaves](../keys/index.yml), [segredos](../secrets/index.yml) e [certificados](../certificates/index.yml). Para obter mais informações sobre o Key Vault, confira [Sobre o Azure Key Vault](overview.md); para obter mais informações sobre o que pode ser armazenado em um cofre de chaves, confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Neste guia de início rápido, você criará um cofre de chaves com a [CLI do Azure](/cli/azure/). A CLI do Azure é usada para criar e gerenciar recursos do Azure usando comandos ou scripts.  Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para saber mais sobre as opções de logon por meio da CLI, veja [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Crie um Key Vault no grupo de recursos da etapa anterior. Você precisará fornecer algumas informações:

- Nome do cofre de chaves: uma cadeia de 3 a 24 caracteres contendo somente números (0-9), letras (a-z, A-Z) e hifens (-)

  > [!Important]
  > Cada cofre de chaves deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

A saída desse cmdlet mostra as propriedades do cofre de chaves criado recentemente. Anote as duas propriedades listadas abaixo:

- **Nome do cofre**: o nome que você forneceu ao parâmetro --name acima.
- **URI do cofre**: No exemplo, ele é https://&lt;nome-exclusivo-do-cofre-de-chaves&gt;.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.

Quando não forem mais necessários, você poderá usar o comando [az group delete](/cli/azure/group) da CLI do Azure para remover o grupo de recursos e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um Key Vault e o excluiu. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](overview.md)
- Confira a referência dos [comandos az keyvault da CLI do Azure](/cli/azure/keyvault?view=azure-cli-latest)
- Examine as [Melhores práticas do Azure Key Vault](best-practices.md)
