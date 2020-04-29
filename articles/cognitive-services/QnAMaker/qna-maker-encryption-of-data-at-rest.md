---
title: QnA Maker criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: QnA Maker criptografia de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372090"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker criptografia de dados em repouso

QnA Maker criptografa automaticamente seus dados quando eles são persistidos na nuvem, ajudando a atender às suas metas organizacionais de segurança e conformidade.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Também há uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK) oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

QnA Maker usa o suporte do CMK do Azure Search. Você precisa criar [CMK em Azure Search usando Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Esta instância do Azure deve ser associada ao serviço de QnA Maker para torná-lo CMK habilitado.

> [!IMPORTANT]
> O recurso de serviço de Azure Search deve ter sido criado após janeiro de 2019 e não pode estar na camada gratuita (compartilhada). Não há suporte para configurar chaves gerenciadas pelo cliente no portal do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

O serviço de QnA Maker usa o CMK do serviço de Azure Search. Siga estas etapas para habilitar o CMKs:

1. Crie uma nova instância de Azure Search e habilite os pré-requisitos mencionados nos [pré-requisitos de chave gerenciada pelo cliente para o Azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Exibir configurações de criptografia](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando você cria um recurso de QnA Maker, ele é automaticamente associado a uma instância de Azure Search. Isso não pode ser usado com CMK. Para usar o CMK, você precisará associar sua instância recém-criada do Azure Search que foi criado na etapa 1. Especificamente, você precisará atualizar o e `AzureSearchAdminKey` `AzureSearchName` o em seu QnA Maker recurso.

   ![Exibir configurações de criptografia](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Em seguida, crie uma nova configuração de aplicativo:
   * **Nome**: Defina como`CustomerManagedEncryptionKeyUrl`
   * **Valor**: esse é o valor que você obteve na etapa 1 ao criar sua instância de Azure Search.

   ![Exibir configurações de criptografia](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Quando terminar, reinicie o tempo de execução. Agora seu serviço de QnA Maker está habilitado para CMK.

## <a name="regional-availability"></a>Disponibilidade regional

As chaves gerenciadas pelo cliente estão disponíveis em todas as regiões de Azure Search.

## <a name="next-steps"></a>Próximas etapas

* [Criptografia em Azure Search usando CMKs no Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Criptografia de dados em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
