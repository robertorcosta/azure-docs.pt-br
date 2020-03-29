---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484092"
---
O contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Rastreia informações de cobrança.|
|Não|[ApplicationInsights](#applicationinsights-setting)|Permite a adição de suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner.|
|Sim|[Cobrança](#billing-configuration-setting)|Especifica o URI do ponto de extremidade do recurso de serviços no Azure.|
|Sim|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não|[Fluentd](#fluentd-settings)|Grava log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|Não|HTTP Proxy|Configura um proxy HTTP para fazer solicitações de saída.|
|Não|[Registrando em log](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|Não|[Mounts](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|
