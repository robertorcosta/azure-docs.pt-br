---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424172"
---
> [!NOTE]
> Um aplicativo Web pode atingir o tempo limite após 20 minutos de inatividade. Somente as solicitações para o aplicativo web real reiniciam o temporizador. Ver a configuração do aplicativo no portal Azure ou`https://<app_name>.scm.azurewebsites.net`fazer solicitações para o site de ferramentas avançadas ( ) não redefinir o temporizador. Se o aplicativo executar webJobs contínuos ou agendados (timer trigger), habilite **o Always On** para garantir que o WebJobs seja executado de forma confiável. Este recurso está disponível apenas nos [tipos de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Básico, Standard e Premium.
