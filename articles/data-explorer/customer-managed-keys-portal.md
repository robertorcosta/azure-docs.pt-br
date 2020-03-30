---
title: Configure chaves gerenciadas pelo cliente usando o portal Azure
description: Este artigo descreve como configurar a criptografia de chaves gerenciadas pelo cliente em seus dados no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: c014ed2c25711677617d3bf8ff5d2f0f968a3b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301038"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Configure chaves gerenciadas pelo cliente usando o portal Azure

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modelo de Gerenciador de recursos do Azure](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Habilite a criptografia com chaves gerenciadas pelo cliente no portal Azure

Este artigo mostra como ativar a criptografia de chaves gerenciadas pelo cliente usando o portal Azure. Por padrão, a criptografia do Azure Data Explorer usa chaves gerenciadas pela Microsoft. Configure o cluster Azure Data Explorer para usar as chaves gerenciadas pelo cliente e especifique a chave para associar ao cluster.

1. No [portal Azure,](https://portal.azure.com/)acesse o recurso [de cluster do Azure Data Explorer.](create-cluster-database-portal.md#create-a-cluster) 
1. Selecione **Configurações** > **Criptografia** no painel esquerdo do portal.
1. No painel **Criptografia,** selecione **'Ligado'** para a configuração **da chave gerenciada pelo cliente.**
1. Clique **em Selecionar tecla**.

    ![Configurar chaves gerenciadas pelo cliente](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Na **tecla Select da janela Azure Key Vault,** selecione um **cofre de chaves** existente na lista de isaque. Se você selecionar **Criar novo** para criar um novo [Key Vault,](/azure/key-vault/quick-create-portal#create-a-vault)você será encaminhado para a tela **Criar key vault.**

1. Selecione **A tecla**.
1. Selecione **versão**.
1. Clique em **Selecionar**.

    ![Selecione a chave do Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. No painel **Criptografia** que agora contém sua chave, **selecione Salvar**. Quando a criação do CMK for bem-sucedida, você verá uma mensagem de sucesso em **Notificações**.

    ![Salvar a chave gerenciada pelo cliente](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Ao habilitar as chaves gerenciadas pelo cliente para o cluster Azure Data Explorer, você criará uma identidade atribuída ao sistema para o cluster se não existir. Além disso, você fornecerá as permissões de exibição necessárias para o cluster Azure Data Explorer no Cofre de Chaves selecionado e obterá as propriedades do Key Vault. 

> [!NOTE]
> Selecione **Desfazer** para remover a chave gerenciada pelo cliente depois de criada.

## <a name="next-steps"></a>Próximas etapas

* [Grupos secure Azure Data Explorer no Azure](security.md)
* [Proteja seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) ativando a criptografia em repouso.
* [Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Configure as chaves gerenciadas pelo cliente usando C #](customer-managed-keys-csharp.md)



