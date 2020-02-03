---
title: Armazenar segredos em um cofre de chaves no Azure DevTest Labs | Microsoft Docs
description: Saiba como armazenar segredos em um Azure Key Vault e usá-los ao criar uma VM, fórmula ou ambiente.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 29a4d20390575778ccdecde390c257ccf6a48eb1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720921"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Armazenar segredos em um cofre de chaves no Azure DevTest Labs
Talvez seja necessário inserir um segredo complexo ao usar o Azure DevTest Labs: a senha para sua VM do Windows, a chave SSH pública para sua VM do Linux ou o token de acesso pessoal para clonar o repositório do Git por meio de um artefato. Segredos geralmente são longos e têm caracteres aleatórios. Portanto, inseri-los pode ser complicado e inconveniente, especialmente se você usar o mesmo segredo várias vezes.

Para resolver esse problema e também manter seus segredos em um local seguro, o DevTest Labs dá suporte ao armazenamento de segredos em um [Azure Key Vault](../key-vault/key-vault-overview.md). Quando um usuário salva um segredo pela primeira vez, o serviço DevTest Labs cria automaticamente um cofre de chaves no mesmo grupo de recursos que contém o laboratório e armazena o segredo no cofre de chaves. O DevTest Labs cria um cofre de chaves separado para cada usuário. 

Observe que o usuário do laboratório precisará primeiro criar uma máquina virtual de laboratório antes de poder criar um segredo no cofre de chaves. Isso ocorre porque o DevTest Lab Service precisa associar o usuário do laboratório a um documento de usuário válido antes de poder criar e armazenar segredos em seu cofre de chaves. 


## <a name="save-a-secret-in-azure-key-vault"></a>Salvar um segredo no Azure Key Vault
Para salvar seu segredo no Azure Key Vault, siga as etapas a seguir:

1. Selecione **Meus segredos** no menu esquerdo.
2. Insira um **nome** para o segredo. Você vê esse nome na lista suspensa ao criar uma VM, fórmula ou um ambiente. 
3. Insira o segredo como o **valor**.

    ![Armazenar segredo](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Usar um segredo do Azure Key Vault
Quando você precisa inserir um segredo para criar uma VM, fórmula ou ambiente, você pode inserir um segredo manualmente ou selecione um segredo salvo do cofre de chaves. Para usar um segredo armazenado no cofre de chaves, execute as seguintes ações:

1. Selecione **Usar um segredo salvo**. 
2. Selecione o segredo na lista suspensa para **Escolher um segredo**. 

    ![Usar o segredo na VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Usar um segredo em um modelo do Azure Resource Manager
Você pode especificar o nome do segredo em um modelo do Azure Resource Manager que é usado para criar uma VM, conforme mostrado no exemplo a seguir:

![Usar o segredo no ambiente ou em fórmula](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Criar uma VM usando o segredo](devtest-lab-add-vm.md) 
- [Criar uma fórmula usando o segredo](devtest-lab-manage-formulas.md)
- [Criar um ambiente usando o segredo](devtest-lab-create-environment-from-arm.md)
