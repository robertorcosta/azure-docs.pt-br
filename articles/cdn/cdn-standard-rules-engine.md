---
title: Use um mecanismo de regras para aplicar HTTPS no Standard Azure CDN | Microsoft Docs
description: Use o mecanismo de regras para o Microsoft Standard Azure Content Delivery Network (Azure CDN) para personalizar como o Azure CDN lida com solicitações HTTP, incluindo o bloqueio da entrega de certos tipos de conteúdo, a definição de uma política de cache e a modificação de cabeçalhos HTTP. Neste artigo, aprenda a criar uma regra para redirecionar os usuários para HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 91a442573139bf4fdd09978290bf2380c8bcb97e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259911"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configure o mecanismo de regras padrão para CDN do Azure

Este artigo descreve como configurar e usar o mecanismo de regras padrão para a Azure Content Delivery Network (Azure CDN).

## <a name="standard-rules-engine"></a>Motor de regras padrão

Você pode usar o mecanismo de regras padrão para o Azure CDN para personalizar a forma como as solicitações HTTP são tratadas. Por exemplo, você pode usar o mecanismo de regras para impor a entrega de conteúdo para usar protocolos específicos, para definir uma política de cache ou para modificar um cabeçalho HTTP. Este artigo demonstra como criar uma regra que redireciona automaticamente os usuários para HTTPS. 

> [!NOTE]
> O mecanismo de regras descrito neste artigo está disponível apenas para CDN Standard Azure da Microsoft. 

## <a name="redirect-users-to-https"></a>Redirecione os usuários para HTTPS

1. Em seus perfis da Microsoft, vá para a Azure Content Delivery Network.

1. Na página de perfil do **CDN,** selecione o ponto final para o que deseja criar.
  
1. Selecione a guia **Mecanismo de regras.**
   
    O painel **Rules Engine** abre e exibe a lista de regras globais disponíveis. 
   
    [![Azure CDN página de novas regras](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A ordem na qual várias regras são listadas afeta a forma como as regras são tratadas. As ações especificadas em uma regra podem ser substituídas por uma regra subseqüente.
   >

1. Selecione **Adicionar regra** e digite um nome de regra. Os nomes das regras devem começar com uma letra e podem conter apenas números e letras.

1. Para identificar o tipo de solicitações a que a regra se aplica, crie uma condição de correspondência:
    1. Selecione **Adicionar condição**e selecione a condição de correspondência de **protocolo de** solicitação.
    1. Para **Operador**, selecione **É igual a**.
    1. Para **valor,** selecione **HTTP**.
   
   [![Condição de correspondência de regras do Azure CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Você pode selecionar entre várias condições de correspondência na lista de itens de isto de **condição de adicionar.** Para obter uma lista detalhada das condições de jogo, consulte [Condições de correspondência no mecanismo de regras padrão](cdn-standard-rules-engine-match-conditions.md).
   
1. Selecione a ação a ser aplicada às solicitações que satisfaçam a condição de correspondência:
   1. Selecione **Adicionar ação**e, em seguida, selecione **redirecionar URL**.
   1. Para **tipo,** selecione **Found (302)**.
   1. Para **Protocolo**, selecione **HTTPS**.
   1. Deixe todos os outros campos em branco para usar os valores de entrada.
   
   [![Ação de regra do Azure CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Você pode selecionar entre várias ações na lista de parada de **ação Adicionar.** Para obter uma lista detalhada de ações, consulte [Ações no mecanismo de regras Padrão](cdn-standard-rules-engine-actions.md).

6. Selecione **Salvar** para salvar a nova regra. A regra está agora disponível para uso.
   
   > [!IMPORTANT]
   > As alterações de regras podem levar até 15 minutos para se propagar através do CDN do Azure.
   >
   

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras Standard](cdn-standard-rules-engine-reference.md)
- [Condições de correspondência no motor de regras Padrão](cdn-standard-rules-engine-match-conditions.md)
- [Ações no mecanismo de regras Padrão](cdn-standard-rules-engine-actions.md)
