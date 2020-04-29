---
title: Correção de correção rápida para recomendações do Advisor
description: Executar a correção em massa usando a correção rápida no Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502484"
---
# <a name="quick-fix-remediation-for-advisor"></a>Correção de correção rápida para o Advisor
A **correção rápida** permite uma maneira mais rápida e fácil de remediação para recomendação em vários recursos. Ele fornece capacidade para remediações em massa de recursos e ajuda você a otimizar suas assinaturas mais rapidamente com a correção em escala para seus recursos.
O recurso está disponível apenas para determinadas recomendações, por meio de portal do Azure.


## <a name="steps-to-use-quick-fix"></a>Etapas para usar a ' correção rápida '

1. Na lista de recomendações que têm o rótulo **correção rápida** , clique na recomendação.

   ![Correção rápida do Advisor](./media/quick-fix-1.png)
   
   *Os preços na imagem são apenas para fins de exemplo*

2. Na página detalhes da recomendação, você verá uma lista de recursos para os quais você tem essa recomendação. Selecione todos os recursos que você deseja corrigir para a recomendação.

   ![Correção rápida do Advisor](./media/quick-fix-2.png)
   
   *Os preços na imagem são apenas para fins de exemplo*

3. Depois de selecionar os recursos, clique no botão **correção rápida** para corrigir em massa.

   > [!NOTE]
   > Alguns dos recursos listados podem estar desabilitados, pois você não tem as permissões apropriadas para modificá-los.
   
   > [!NOTE]
   > Se houver outras implicações, além dos benefícios mencionados no Advisor, você será comunicado na experiência para ajudá-lo a tomar decisões de correção informadas.
   
4. Você receberá uma notificação para a conclusão da correção. Você verá um erro se houver recursos que não são corrigidos e recursos no modo selecionado na exibição de lista de recursos.  


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
* [API REST do Advisor](https://docs.microsoft.com/rest/api/advisor/)
