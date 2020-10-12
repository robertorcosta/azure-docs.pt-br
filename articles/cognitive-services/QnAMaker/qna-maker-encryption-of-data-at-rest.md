---
title: QnA Maker criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e também permite que você gerencie suas assinaturas de serviços cognitivas com suas próprias chaves, chamadas CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados em repouso para QnA Maker e como habilitar e gerenciar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: e744423e00377ef763824f6e39865e6b3e8ee475
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073532"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker criptografia de dados em repouso

QnA Maker criptografa automaticamente seus dados quando eles são persistidos na nuvem, ajudando a atender às suas metas organizacionais de segurança e conformidade.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Também há a opção de gerenciar sua assinatura com suas próprias chaves chamadas CMK (chaves gerenciadas pelo cliente). O CMK oferece maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados. Se o CMK estiver configurado para sua assinatura, a criptografia dupla será fornecida, que oferece uma segunda camada de proteção, permitindo que você controle a chave de criptografia por meio de seu Azure Key Vault.

QnA Maker usa o suporte do CMK do Azure Search. Você precisa criar [CMK em Azure Search usando Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Esta instância do Azure deve ser associada ao serviço de QnA Maker para torná-lo CMK habilitado.

> [!IMPORTANT]
> O recurso de serviço de Azure Search deve ter sido criado após janeiro de 2019 e não pode estar na camada gratuita (compartilhada). Não há suporte para configurar chaves gerenciadas pelo cliente no portal do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

O serviço de QnA Maker usa o CMK do serviço de Azure Search. Siga estas etapas para habilitar o CMKs:

1. Crie uma nova instância de Azure Search e habilite os pré-requisitos mencionados nos [pré-requisitos de chave gerenciada pelo cliente para o Azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Exibir configurações de criptografia 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando você cria um recurso de QnA Maker, ele é automaticamente associado a uma instância de Azure Search. Isso não pode ser usado com CMK. Para usar o CMK, você precisará associar sua instância recém-criada do Azure Search que foi criado na etapa 1. Especificamente, você precisará atualizar o `AzureSearchAdminKey` e o `AzureSearchName` em seu QnA Maker recurso.

   ![Exibir configurações de criptografia 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Em seguida, crie uma nova configuração de aplicativo:
   * **Nome**: Defina como `CustomerManagedEncryptionKeyUrl`
   * **Valor**: esse é o valor que você obteve na etapa 1 ao criar sua instância de Azure Search.

   ![Exibir configurações de criptografia 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Quando terminar, reinicie o tempo de execução. Agora seu serviço de QnA Maker está habilitado para CMK.

## <a name="regional-availability"></a>Disponibilidade regional

As chaves gerenciadas pelo cliente estão disponíveis em todas as regiões de Azure Search.

## <a name="encryption-of-data-in-transit"></a>Criptografia de dados em trânsito

QnA Maker portal é executado no navegador do usuário. Cada ação dispara uma chamada direta para a respectiva API de serviço cognitiva. Portanto, QnA Maker é compatível com os dados em trânsito.
No entanto, como o serviço do portal QnA Maker está hospedado no oeste dos EUA, ele ainda não é ideal para clientes que não sejam dos EUA. 

## <a name="next-steps"></a>Próximas etapas

* [Criptografia em Azure Search usando CMKs no Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Criptografia de dados em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)