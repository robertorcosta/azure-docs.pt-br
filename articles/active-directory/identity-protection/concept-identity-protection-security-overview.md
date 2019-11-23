---
title: Azure Active Directory Identity Protection security overview
description: Learn how the Security overview gives you an insight into your organization’s security posture.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382191"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – visão geral de segurança

The [Security overview](https://aka.ms/IdentityProtectionRefresh) in the Azure portal gives you an insight into your organization’s security posture. Ela ajuda a identificar possíveis ataques e compreender a eficácia de suas políticas.

A "Visão geral de segurança" é amplamente dividida em duas seções:

- Tendências, à esquerda, fornecem uma linha do tempo de risco em sua organização.
- Blocos, à direita, realçam os principais problemas atuais em sua organização e sugerem como agir rapidamente.

![Visão geral da segurança](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Novos usuários arriscados detectados

Este gráfico mostra o número de novos usuários arriscados detectados durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico por nível de risco (baixo, médio, alto). Passe o mouse sobre os incrementos de data UTC para ver o número de usuários arriscados detectados nesse dia. Um clique nesse gráfico apresentará a você o relatório "Usuários arriscados". Para corrigir os usuários que estão em risco, considere alterar as respectivas senhas.

### <a name="new-risky-sign-ins-detected"></a>Novas entradas arriscadas detectadas

Este gráfico mostra o número de novas entradas arriscadas detectadas durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico pelo tipo de risco de entrada (em tempo real ou agregação) e o nível de risco de entrada (baixo, médio, alto). Entradas desprotegidas são entradas bem-sucedidas de risco em tempo real que não passaram por desafio de MFA. (Note: Sign-ins that are risky because of offline detections cannot be protected in real-time by sign-in risk policies). Passe o mouse sobre os incrementos de data UTC para ver o número de entradas detectadas como em risco nesse dia. Um clique nesse gráfico apresentará a você o relatório 'Entradas arriscadas'.

## <a name="tiles"></a>Blocos
 
### <a name="high-risk-users"></a>Usuários de risco alto

O bloco "Usuários de risco alto" mostra a última contagem de usuários com alta probabilidade de comprometimento de identidade. Eles devem ser uma prioridade principal para investigação. Um clique no bloco "Usuários de risco alto" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco alto. Usando esse relatório, você pode aprender mais e corrigir esses usuários com uma redefinição de senha.

![Visão geral da segurança](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Usuários de risco médio
O bloco "Usuários de risco médio" mostra a última contagem de usuários com probabilidade média de comprometimento de identidade. Um clique no bloco "Usuários de risco médio" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco médio. Usando esse relatório, você pode investigar e corrigir ainda mais esses usuários.

### <a name="unprotected-risky-sign-ins"></a>Entradas arriscadas desprotegidas

The ‘Unprotected risky sign-ins' tile shows the last week’s count of successful, real-time risky sign-ins that were not blocked or MFA challenged by a Conditional Access policy, Identity Protection risk policy, or per-user MFA. Esses são logons potencialmente comprometidos que tiveram êxito e não passaram por desafio de MFA. Para proteger essas entradas no futuro, aplique uma política de risco de entrada. Um clique no bloco "Entradas arriscadas desprotegidas" redirecionará para a folha de configuração de política de risco de entrada, na qual você poderá configurar a política de risco de entrada para exigir o MFA em uma entrada com um nível de risco especificado.

### <a name="legacy-authentication"></a>Autenticação herdada

O bloco "Autenticação herdada" mostra a contagem de autenticações herdadas realizadas na semana passada em sua organização. Protocolos de autenticação herdados não são compatíveis com métodos de segurança modernos, tais como um MFA. Para impedir a autenticação herdada, você pode aplicar uma política de acesso condicional. Um clique no bloco ‘Autenticação herdada’ redirecionará você para a ‘Classificação de Segurança de Identidade’.

### <a name="identity-secure-score"></a>Classificação de segurança de identidade

A Classificação de Segurança de Identidade mede e compara a sua postura de segurança para os padrões de mercado. If you click on ‘Identity Secure Score (Preview)’ tile, it will redirect to the ‘Identity Secure Score’ blade where you can learn more about improving your security posture.

## <a name="next-steps"></a>Próximos passos

- [What is risk](concept-identity-protection-risks.md)

- [Policies available to mitigate risks](concept-identity-protection-policies.md)
