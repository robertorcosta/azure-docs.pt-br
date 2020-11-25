---
title: Crie resiliência na identidade do cliente e no gerenciamento de acesso usando o Azure AD B2C | Microsoft Docs
description: Métodos para criar resiliência na identidade do cliente e gerenciamento de acesso usando o Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda9378a41e0d98276957a6fad75db2c20e6e4e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919301"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Crie resiliência em sua identidade do cliente e gerenciamento de acesso com Azure Active Directory B2C

O [Azure Active Directory (AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview) é uma plataforma de CIAM (gerenciamento de identidades e acesso do cliente) projetada para ajudá-lo a iniciar seus aplicativos críticos voltados para o cliente com êxito. Temos muitos recursos internos para [resiliência](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) que foram projetados para ajudar nossos serviços a dimensionar suas necessidades e melhorar a resiliência diante de possíveis situações de interrupção. Além disso, ao iniciar um aplicativo de missão crítica, é importante considerar vários elementos de design e configuração em seu aplicativo, além de como o aplicativo é configurado dentro de Azure AD B2C para garantir que você obtenha um comportamento resiliente em resposta a cenários de falha ou interrupção. Neste artigo, discutiremos algumas das práticas recomendadas para ajudá-lo a aumentar a resiliência.

Um serviço resiliente é aquele que continua funcionando apesar das interrupções. Você pode ajudar a melhorar a resiliência em seu serviço:

- compreendendo todos os componentes

- eliminando pontos únicos de falhas

- isolando componentes com falha para limitar seu impacto

- fornecendo redundância com mecanismos de failover e caminhos de recuperação rápidos

Ao desenvolver seu aplicativo, recomendamos considerar como aumentar a [resiliência de autenticação e autorização em seus aplicativos](resilience-app-development-overview.md) com os componentes de identidade de sua solução. Este artigo tenta abordar aprimoramentos de resiliência específica para Azure AD B2C aplicativos. Nossas recomendações são agrupadas por funções CIAM.

![Imagem mostra os componentes do CIAM ](media/resilience-b2c/high-level-components.png) nas seções subsequentes, guiaremos você para criar resiliência nas seguintes áreas:

- [Experiência do usuário final](resilient-end-user-experience.md): habilite um plano de fallback para seu fluxo de autenticação e reduza o impacto potencial de uma interrupção do serviço de autenticação Azure ad B2C.

- [Interfaces com processos externos](resilient-external-processes.md): Crie resiliência em seus aplicativos e interfaces recuperando-se de erros.  

- [Práticas recomendadas para desenvolvedores](resilience-b2c-developer-best-practices.md): Evite fragilidade devido a problemas comuns de política personalizada e melhore o tratamento de erros nas áreas como interações com verificadores de declarações, aplicativos de terceiros e APIs REST.

- [Monitoramento e análise](resilience-with-monitoring-alerting.md): avalie a integridade do serviço monitorando indicadores principais e detecte falhas e interrupções de desempenho por meio de alertas.

- Recursos de resiliência para desenvolvedores de Azure AD B2C
  - [Experiência do usuário final resiliente](resilient-end-user-experience.md)
  - [Interfaces resilientes com processos externos](resilient-external-processes.md)
  - [Resiliência por meio de práticas recomendadas para desenvolvedores](resilience-b2c-developer-best-practices.md)
  - [Resiliência por meio de monitoramento e análise](resilience-with-monitoring-alerting.md)
- [Crie resiliência em sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência de autenticação e autorização em seus aplicativos](resilience-app-development-overview.md)
