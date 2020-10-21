---
title: Traga sua própria chave (chaves gerenciadas pelo cliente) com os serviços de mídia
description: Você pode usar uma chave gerenciada pelo cliente (ou seja, traga sua própria chave) com os serviços de mídia.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a89ff56eb9e0f0a29b5b1fed7543c5f718425e51
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325963"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Traga sua própria chave (chaves gerenciadas pelo cliente) com os serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Bring Your Own Key (BYOK) é uma ampla iniciativa do Azure para ajudar os clientes a mover suas cargas de trabalho para a nuvem. As chaves gerenciadas pelo cliente permitem que os clientes sigam as regulamentações de conformidade do setor e melhoram o isolamento de locatários de um serviço. Dar aos clientes o controle das chaves de criptografia é uma maneira de minimizar o acesso e o controle desnecessários e criar confiança nos serviços da Microsoft.

## <a name="keys-and-key-management"></a>Chaves e gerenciamento de chaves

Você pode usar sua própria chave com os serviços de mídia ao usar a API dos serviços de mídia 2020-05-01. Uma chave de conta padrão é criada para todas as contas que são criptografadas por uma chave do sistema de propriedade dos serviços de mídia. Quando você usa sua própria chave, a chave de conta é criptografada com sua chave. As chaves de conteúdo são criptografadas pela chave de conta. As URLs de JobInputHttp e as chaves de validação de token simétrico também são criptografadas.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Uma chave gerenciada pelo cliente substitui uma chave gerenciada pelo sistema":::

Os serviços de mídia usam a identidade gerenciada da conta dos serviços de mídia para ler sua chave de um Key Vault pertencente a você. Os serviços de mídia exigem que a Key Vault esteja na mesma região que a conta e que ela tenha a proteção de exclusão e limpeza reversível habilitada.

Sua chave pode ser uma chave RSA 2048, 3072 ou 4096, e as chaves de software e HSM são suportadas.

> [!NOTE]
> Não há suporte para chaves do EC.

Você pode especificar um nome de chave e uma versão de chave, ou apenas um nome de chave. Quando você usar apenas um nome de chave, os serviços de mídia usarão a versão de chave mais recente. Novas versões de chaves do cliente são detectadas automaticamente e a chave de conta é criptografada novamente.

> [!WARNING]
> Os serviços de mídia monitoram o acesso à chave do cliente. Se a chave do cliente se tornar inacessível (por exemplo, a chave tiver sido excluída ou o Key Vault tiver sido excluído ou a concessão de acesso tiver sido removida), os serviços de mídia passarão a conta para o estado inacessível da chave do cliente (desabilitando efetivamente a conta). No entanto, a conta pode ser excluída nesse estado. As únicas operações com suporte são conta GET, lista e exclusão; todas as outras solicitações (codificação, streaming e assim por diante) falharão até que o acesso à chave de conta seja restaurado.

## <a name="tutorial"></a>Tutorial
Experimente o tutorial [usar chaves gerenciadas pelo cliente ou BYOK (traga sua própria chave) com os serviços de mídia](tutorial-byok.md) para saber mais sobre como configurar e usar chaves gerenciadas pelo cliente com o postmaster e a API REST do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: usar chaves gerenciadas pelo cliente ou BYOK (traga sua própria chave) com os serviços de mídia](tutorial-byok.md)
