---
title: Práticas recomendadas de desenvolvimento seguro no Microsoft Azure
description: Práticas recomendadas para ajudá-lo a desenvolver um código mais seguro e implantar um aplicativo mais seguro na nuvem.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934861"
---
# <a name="secure-development-best-practices-on-azure"></a>Práticas recomendadas de desenvolvimento seguro no Azure
Esta série de artigos apresenta atividades de segurança e controles a considerar quando você desenvolve aplicações para a nuvem. As fases do Ciclo de Vida do Desenvolvimento de Segurança da Microsoft (SDL) e as questões e conceitos de segurança a serem considerados durante cada fase do ciclo de vida são abordadas. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar em cada fase do ciclo de vida para projetar, desenvolver e implantar um aplicativo mais seguro.

As recomendações nos artigos vêm de nossa experiência com a segurança do Azure e das experiências de nossos clientes. Você pode usar esses artigos como referência para o que você deve considerar durante uma fase específica do seu projeto de desenvolvimento, mas sugerimos que você também leia todos os artigos do início ao fim pelo menos uma vez. Ler todos os artigos apresenta conceitos que você pode ter perdido em fases anteriores do seu projeto. A implementação desses conceitos antes de lançar seu produto pode ajudá-lo a criar software seguro, atender aos requisitos de conformidade de segurança e reduzir os custos de desenvolvimento.

Esses artigos destinam-se a ser um recurso para designers de software, desenvolvedores e testadores em todos os níveis que constroem e implantam aplicativos Azure seguros.

## <a name="overview"></a>Visão geral

A segurança é um dos aspectos mais importantes de qualquer aplicação, e não é uma coisa simples de acertar. Felizmente, o Azure fornece muitos serviços que podem ajudá-lo a proteger seu aplicativo na nuvem. Esses artigos abordam atividades e serviços do Azure que você pode implementar em cada etapa do ciclo de vida do desenvolvimento de software para ajudá-lo a desenvolver um código mais seguro e implantar um aplicativo mais seguro na nuvem.

## <a name="security-development-lifecycle"></a>Ciclo de vida de desenvolvimento de segurança

Seguir as melhores práticas para o desenvolvimento seguro de software requer a integração da segurança em cada fase do ciclo de vida do desenvolvimento de software, desde a análise de requisitos até a manutenção, independentemente da metodologia do projeto[(cachoeira,](https://en.wikipedia.org/wiki/Waterfall_model) [ágil](https://en.wikipedia.org/wiki/Agile_software_development)ou [DevOps).](https://en.wikipedia.org/wiki/DevOps) Na esteira de violações de dados de alto perfil e da exploração de falhas de segurança operacional, mais desenvolvedores estão entendendo que a segurança precisa ser tratada durante todo o processo de desenvolvimento.

Quanto mais tarde você corrigir um problema no seu ciclo de vida de desenvolvimento, mais essa correção vai lhe custar. Problemas de segurança não são exceção. Se você ignorar os problemas de segurança nas fases iniciais do desenvolvimento de seu software, cada fase que se segue pode herdar as vulnerabilidades da fase anterior. Seu produto final terá acumulado vários problemas de segurança e a possibilidade de uma violação. A segurança em cada fase do ciclo de vida do desenvolvimento ajuda você a pegar problemas cedo, e ajuda a reduzir seus custos de desenvolvimento.

Seguimos as fases do [Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) para introduzir atividades e serviços do Azure que você pode usar para cumprir práticas seguras de desenvolvimento de software em cada fase do ciclo de vida.

As fases do SDL são:

  - Treinamento
  - Requisitos
  - Design
  - Implementação
  - Verificação
  - Versão
  - Resposta

![Ciclo de vida de desenvolvimento de segurança](./media/secure-dev-overview/01-sdl-phase.png)

Nestes artigos, agrupamos as fases do SDL em design, desenvolvimento e implantação.

## <a name="engage-your-organizations-security-team"></a>Envolva a equipe de segurança da sua organização

Sua organização pode ter um programa formal de segurança de aplicativos que o auxilia com atividades de segurança do início ao fim durante o ciclo de vida do desenvolvimento. Se sua organização tiver equipes de segurança e conformidade, certifique-se de engajá-las antes de começar a desenvolver seu aplicativo. Pergunte a eles em cada fase do SDL se há alguma tarefa que você perdeu.

Entendemos que muitos leitores podem não ter uma equipe de segurança ou conformidade para se engajar. Esses artigos podem ajudar a guiá-lo nas questões de segurança e decisões que você precisa considerar em cada fase do SDL.

## <a name="resources"></a>Recursos

Use os seguintes recursos para saber mais sobre o desenvolvimento de aplicativos seguros e para ajudar a proteger seus aplicativos no Azure:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – O SDL é um processo de desenvolvimento de software da Microsoft que ajuda os desenvolvedores a criar softwares mais seguros. Ele ajuda você a atender aos requisitos de conformidade de segurança, ao mesmo tempo em que reduz os custos de desenvolvimento.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – A OWASP é uma comunidade online que produz artigos, metodologias, documentação, ferramentas e tecnologias disponíveis gratuitamente no campo da segurança de aplicativos web.

[Empurrando para a esquerda, como um chefe](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – uma série de artigos online que descreve diferentes tipos de atividades de segurança de aplicativos que os desenvolvedores devem completar para criar um código mais seguro.

[Plataforma de identidade Microsoft](../../active-directory/develop/index.yml) – A plataforma de identidade da Microsoft é uma evolução do serviço de identidade Azure AD e da plataforma de desenvolvedores. É uma plataforma completa que consiste em um serviço de autenticação, bibliotecas de código aberto, registro e configuração de aplicativos, documentação completa do desenvolvedor, amostras de código e outros conteúdos do desenvolvedor. A plataforma de identidade da Microsoft suporta protocolos padrão do setor, como o OAuth 2.0 e o OpenID Connect.

[Práticas recomendadas de segurança para soluções Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – Uma coleção de práticas recomendadas de segurança para usar quando você projeta, implanta e gerencia soluções em nuvem usando o Azure. Este artigo destina-se a ser um recurso para profissionais de TI. Isso pode incluir designers, arquitetos, desenvolvedores e testadores que compilam e implantam soluções seguras do Azure.

[Projetos de segurança e conformidade no Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure Security and Compliance Blueprints são recursos que podem ajudá-lo a criar e lançar aplicativos alimentados por nuvem que cumprem regulamentos e padrões rigorosos.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos controles de segurança e atividades que possam ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Projetar aplicativos seguros](secure-design.md)
- [Desenvolver aplicativos e segurança](secure-develop.md)
- [Implantar aplicativos seguros](secure-deploy.md)
