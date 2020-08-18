---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515236"
---
**1. Como os clientes serão notificados sobre a desativação do SDK?**

A Microsoft fornecerá uma notificação antecipada de 12 meses para o fim do suporte do SDK de desativação, a fim de facilitar uma transição tranqüila para um SDK com suporte. Além disso, os clientes serão notificados por meio de vários canais de comunicação – portal do Azure, atualizações do Azure e comunicação direta com administradores de serviço atribuídos.

**2. os clientes podem criar aplicativos usando um SDK de Azure Cosmos DB "para serem" desativados durante o período de 12 meses?** 

Sim, os clientes terão acesso completo para criar, implantar e modificar aplicativos usando o SDK de Azure Cosmos DB "a ser" desativado durante o período de aviso de 12 meses. Durante o período de aviso de 12 meses, os clientes são aconselhados a migrar para uma versão mais recente com suporte do SDK do Azure Cosmos DB, conforme apropriado. 

**3. após a data de desativação, o que acontece com os aplicativos que estão usando o SDK de Azure Cosmos DB sem suporte?** 

Após a data de desativação, o Azure Cosmos DB não fará mais correções de bugs, adicionará novos recursos e dará suporte às versões descontinuadas do SDK. Se você preferir não atualizar, as solicitações enviadas das versões desativadas do SDK continuarão sendo servidas pelo serviço de Azure Cosmos DB. 

**4. quais versões do SDK terão os recursos e as atualizações mais recentes?**

Novos recursos e atualizações serão adicionados somente à versão secundária mais recente da versão mais recente do SDK com suporte. É recomendável sempre usar a versão mais recente para aproveitar os novos recursos, aprimoramentos de desempenho e correções de bugs. Se você estiver usando uma versão antiga e não desativada do SDK, suas solicitações para Azure Cosmos DB continuarão a funcionar, mas você não terá acesso a quaisquer novos recursos.  

**5. o que devo fazer se não for possível atualizar meu aplicativo antes de uma data de fechamento?**

Recomendamos atualizar o mais rápido possível para o SDK mais recente. Depois que um SDK tiver sido marcado para desativação, você terá 12 meses para atualizar seu aplicativo. Se não for possível atualizar pela data de aposentadoria, as solicitações enviadas das versões desativadas do SDK continuarão a ser servidas pelo Azure Cosmos DB, para que os aplicativos em execução continuem a funcionar. No entanto, o Azure Cosmos DB não fará mais correções de bugs, adicionará novos recursos e dará suporte às versões desativadas do SDK. 

Se você tiver um plano de suporte e precisar de suporte técnico, [entre em contato conosco](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ao arquivar um tíquete de suporte.
    


