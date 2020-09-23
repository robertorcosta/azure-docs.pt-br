---
title: Planos de serviço e cotas para o Azure Spring Cloud
description: Saiba mais sobre cotas de serviço e planos de serviço para o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4c832819f61d208d0722823d0a74354960f182
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904271"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Cotas e planos de serviço para o Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Todos os serviços do Azure definem limites e cotas padrão para recursos e recursos.   O Azure Spring Cloud oferece dois tipos de preço: básico e Standard. Vamos detalhar os limites de ambas as camadas neste artigo.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Limites e camadas de serviço do Azure Spring Cloud

| Recurso | Basic | Standard
------- | ------- | -------
vCPU | 1 por instância de serviço | 4 por instância de serviço
Memória | 2 GB por instância de serviço | 8 GB por instância de serviço
Instâncias de serviço do Azure Spring Cloud por região por assinatura | 10 | 10
Instâncias totais de aplicativo por instância de serviço do Azure Spring Cloud | 25 | 500
Volumes persistentes | aplicativos de 1 GB/aplicativo x 10 | 50 GB/aplicativo x 10 aplicativos

## <a name="next-steps"></a>Próximas etapas

Alguns limites padrão podem ser aumentados. Se a sua instalação exigir um aumento, [crie uma solicitação de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
