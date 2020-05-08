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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "68424172"
---
> [!NOTE]
> Um aplicativo Web pode atingir o tempo limite após 20 minutos de inatividade. Somente as solicitações para o aplicativo web real reiniciam o temporizador. Exibir a configuração do aplicativo no portal do Azure ou fazer solicitações para o site de ferramentas avançadas (`https://<app_name>.scm.azurewebsites.net`) não redefine o timer. Se seu aplicativo executar trabalhos Web contínuos ou agendados (gatilho de temporizador), habilite **Always on** para garantir que os trabalhos Web sejam executados de forma confiável. Este recurso está disponível apenas nos [tipos de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Básico, Standard e Premium.
