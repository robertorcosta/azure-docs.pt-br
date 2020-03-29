---
title: QnA Maker criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: QnA Maker criptografia de dados em repouso .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372090"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker criptografia de dados em repouso

O QnA Maker criptografa automaticamente seus dados quando eles são perscisos na nuvem, ajudando a cumprir suas metas de segurança organizacional e conformidade.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Há também uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK) oferecem maior flexibilidade para criar, girar, desativar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

O QnA Maker usa o suporte a CMK da pesquisa do Azure. Você precisa criar [CMK no Azure Search usando o Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Esta instância do Azure deve ser associada ao serviço QnA Maker para torná-lo CMK ativado.

> [!IMPORTANT]
> O recurso do serviço de pesquisa do Azure deve ter sido criado após janeiro de 2019 e não pode estar no nível gratuito (compartilhado). Não há suporte para configurar chaves gerenciadas pelo cliente no portal Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

O serviço QnA Maker usa CMK do serviço de pesquisa Azure. Siga estas etapas para habilitar CMKs:

1. Crie uma nova instância de Pesquisa do Azure e habilite os pré-requisitos mencionados nos [pré-requisitos principais gerenciados pelo cliente para a Pesquisa Cognitiva do Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Exibir configurações de criptografia](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando você cria um recurso Do Criador de QnA, ele é automaticamente associado a uma instância do Azure Search. Isto não pode ser usado com CMK. Para usar o CMK, você precisará associar sua instância recém-criada do Azure Search que foi criada na etapa 1. Especificamente, você precisará atualizar `AzureSearchAdminKey` o `AzureSearchName` recurso e em seu QnA Maker.

   ![Exibir configurações de criptografia](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Em seguida, crie uma nova configuração de aplicativo:
   * **Nome**: Defina isso para`CustomerManagedEncryptionKeyUrl`
   * **Valor**: Este é o valor que você tem na Etapa 1 ao criar sua instância de pesquisa do Azure.

   ![Exibir configurações de criptografia](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Quando terminar, reinicie o tempo de execução. Agora seu serviço QnA Maker está habilitado para CMK.

## <a name="regional-availability"></a>Disponibilidade regional

As chaves gerenciadas pelo cliente estão disponíveis em todas as regiões de Pesquisa do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Criptografia no Azure Search usando CMKs no Cofre chave do Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Criptografia de dados em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
