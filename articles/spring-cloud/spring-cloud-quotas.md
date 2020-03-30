---
title: Planos de serviços e cotas para Azure Spring Cloud
description: Conheça as cotas de serviços e planos de serviços para o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278883"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Cotas e Planos de Serviços para Nuvem de Primavera do Azure

Todos os serviços do Azure estabelecem limites de inadimplência e cotas para recursos e recursos.  Durante o período de pré-visualização, o Azure Spring Cloud oferece apenas um plano de serviço.

Este artigo detalha as cotas de serviço oferecidas durante o período de pré-visualização atual.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Níveis e cotas de serviços da Azure Spring Cloud

Durante o período de pré-visualização, o Azure Spring Cloud oferece apenas um nível de serviço.

Recurso | Amount
------- | -------
vCPU | 4 por instância de serviço
Memória | 8 GBytes por instância de serviço
Instâncias de serviço do Azure Spring Cloud por região por assinatura | 10
Instâncias totais de aplicativo por instância de serviço do Azure Spring Cloud | 500
Total de instâncias de aplicativos por aplicativo de Primavera | 20
Volumes persistentes | 10 x 50 GBytes

Quando você atinge uma cota, você receberá um erro de 400 que diz: "A cota excede o limite para assinatura *de sua assinatura* na região onde o seu serviço *Azure Spring Cloud é criado*.

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Se o seu recurso exigir um aumento, [crie uma solicitação de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
