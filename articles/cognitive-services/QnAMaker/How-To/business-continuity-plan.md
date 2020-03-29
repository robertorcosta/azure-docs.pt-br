---
title: Plano de continuidade dos negócios – QnA Maker
titleSuffix: Azure Cognitive Services
description: O principal objetivo do plano de continuidade de negócios é criar um ponto de extremidade resiliente da base de conhecimento que garantiria a total ausência de tempo de inatividade para o bot ou aplicativo que a estivesse consumindo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: f920a789f1ec631c5d70863b10d8364b3eb81b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650462"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Criar um plano de continuidade de negócios para o seu serviço QnA Maker

O principal objetivo do plano de continuidade de negócios é criar um ponto de extremidade resiliente da base de conhecimento que garantiria a total ausência de tempo de inatividade para o bot ou aplicativo que a estivesse consumindo.

![Plano de backup do QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível como representada acima é a seguinte:

1. Configurar dois [serviços QnA Maker](../How-To/set-up-qnamaker-service-azure.md) em paralelo nas [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Mantenha os índices de pesquisa azure primários e secundários em sincronia. Use a amostra do GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como restaurar os índices do Azure de backup.

3. Fazer backup do Application Insights usando [exportação contínua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Depois que as pilhas primárias e secundárias forem configuradas, use o [gerenciador de tráfego](https://docs.microsoft.com/azure/traffic-manager/) para configurar os dois pontos de extremidade e configurar um método de roteamento.

5. Você precisaria criar um certificado SSL (Secure Sockets Layer, camada de soquetes seguros) para o ponto final do gerenciador de tráfego. [Associar o certificado SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) nos serviços de Aplicativo.

6. Por fim, use o ponto de extremidade do gerenciador de tráfego em seu bot ou aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha a capactia](./improve-knowledge-base.md)
