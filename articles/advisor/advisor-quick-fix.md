---
title: Correção rápida para recomendações do Advisor
description: Realize a remediação em massa usando o Quick Fix no Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502484"
---
# <a name="quick-fix-remediation-for-advisor"></a>Correção rápida para advisor
**O Quick Fix** permite uma maneira mais rápida e fácil de remediação para recomendação sobre vários recursos. Ele fornece capacidade para remediações em massa de recursos e ajuda você a otimizar suas assinaturas mais rapidamente com remediação em escala para seus recursos.
O recurso está disponível apenas para algumas recomendações, através do portal Azure.


## <a name="steps-to-use-quick-fix"></a>Etapas para usar 'Correção rápida'

1. Na lista de recomendações que possuem o rótulo **Quick Fix,** clique na recomendação.

   ![Correção rápida do conselheiro](./media/quick-fix-1.png)
   
   *Os preços na imagem são, por exemplo, apenas para fins*

2. Na página de detalhes da recomendação, você verá a lista de recursos para os quais você tem essa recomendação. Selecione todos os recursos que deseja remediar para a recomendação.

   ![Correção rápida do conselheiro](./media/quick-fix-2.png)
   
   *Os preços na imagem são, por exemplo, apenas para fins*

3. Depois de selecionar os recursos, clique no botão **Correção rápida** para remediar em massa.

   > [!NOTE]
   > Alguns dos recursos listados podem ser desativados, porque você não tem as permissões apropriadas para modificá-los.
   
   > [!NOTE]
   > Se houver outras implicações, além dos benefícios mencionados no Advisor, você será comunicado na experiência para ajudá-lo a tomar decisões de remediação informadas.
   
4. Você receberá uma notificação para a conclusão da remediação. Você verá um erro se houver recursos que não sejam remediados e recursos no modo selecionado na exibição da lista de recursos.  


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional do Orientador](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
