---
title: Recursos para migrar aplicativos para o Azure Active Directory | Microsoft Docs
description: Recursos para ajudá-lo a migrar o acesso e a autenticação de aplicativos para o Azure AD (Azure Active Directory).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30469858a5dd83f7f5f707f74466302b3000510
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968723"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Recursos para migrar aplicativos para o Azure Active Directory

Recursos para ajudá-lo a migrar o acesso e a autenticação de aplicativos para o Azure AD (Azure Active Directory). Responda a esta pequena pesquisa (https://aka.ms/AppsMigrationFeedback) para fornecer feedback sobre sua experiência de migração de aplicativos para o Azure AD (incluindo bloqueadores para migração, necessidade de ferramentas / orientação ou motivos para manter seu IDP local). 

| Recurso  | Descrição  |
|:-----------|:-------------|
|[Migrando os aplicativos para o Azure AD](https://aka.ms/migrateapps/whitepaper) | Este white paper apresenta os benefícios da migração e descreve como planejar a migração em quatro fases claramente definidas: descoberta, classificação, migração e gerenciamento contínuo. Você será orientado em como pensar sobre o processo e dividir o projeto em partes fáceis de consumir. Ao longo do documento, haverá links para recursos importantes que irão ajudá-lo durante o processo. |
|[Guia de soluções: migrar aplicativos dos Serviços de Federação do Active Directory (AD FS) para o Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Este guia de solução explica as mesmas quatro fases de planejamento e execução de um projeto de migração de aplicativo descrito em um nível superior no white paper de migração. Neste guia, você aprenderá como aplicar essas fases ao objetivo específico de mover um aplicativo do Serviços de Federação do Active Directory (AD FS) para o Azure AD.|
| [Ferramenta: script de preparação para migração dos Serviços de Federação do Active Directory (AD FS)](https://aka.ms/migrateapps/adfstools) | Este é um script que você pode executar em seu servidor local dos Serviços de Federação do Active Directory (AD FS) para determinar a prontidão dos aplicativos para migração para o Azure AD.|
| [Plano de implantação: migrar do AD FS para a sincronização de hash de senha](https://aka.ms/ADFSTOPHSDPDownload) | Com a sincronização de hash de senha, hashes de senhas de usuário são sincronizados do Active Directory local para o Azure AD. Isso permite que o Azure AD autentique usuários sem interagir com o Active Directory local.| 
| [Plano de implantação: migrar do AD FS para a autenticação de passagem](https://aka.ms/ADFSTOPTADPDownload)|A autenticação de passagem do Azure AD ajuda os usuários a entrar nos aplicativos locais e baseados em nuvem usando a mesma senha. Esse recurso melhora a experiência dos usuários, já que terão uma senha a menos a ser lembrada. Além disso, reduz os custos de assistência técnica de TI porque é menos provável que os usuários esqueçam como entrar quando é necessário lembrar de apenas uma senha. Quando as pessoas entram usando o Azure AD, esse recurso valida as senhas dos usuários diretamente no Active Directory local.|
| [Plano de implantação: habilitar logon único em um aplicativo SaaS com Azure AD](https://aka.ms/SSODPDownload) | O SSO (logon único) ajuda a acessar todos os aplicativos e recursos necessários para fazer negócios, ao conectar apenas uma vez, usando uma única conta de usuário. Por exemplo, depois que um usuário conecta-se, o usuário poderá passar do Microsoft Office para o SalesForce, para o Box sem autenticar (por exemplo, digitar uma senha) uma segunda vez. 
| [Plano de implantação: estender aplicativos para Azure AD com Proxy de Aplicativo](https://aka.ms/AppProxyDPDownload)| Fornecer acesso de laptops e outros dispositivos de funcionários a aplicativos locais envolve tradicionalmente VPNs (redes privadas virtuais) ou DMZs (zonas desmilitarizadas). Essas soluções são complexas e difíceis de proteger, além de serem caras para configurar e gerenciar. O Proxy de Aplicativo do Azure Active Directory facilita o acesso a aplicativos locais. |
| [Planos de implantação](../fundamentals/active-directory-deployment-plans.md) | Encontre mais planos de implantação para implantar recursos, como autenticação multifator, acesso condicional, provisionamento de usuário, SSO contínuo, redefinição de senha de autoatendimento e muito mais! |


