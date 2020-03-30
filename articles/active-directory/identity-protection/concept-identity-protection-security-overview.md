---
title: Visão geral da proteção de segurança do diretório ativo do Azure
description: Saiba como a visão geral da Segurança fornece uma visão sobre a postura de segurança da sua organização.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382191"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – visão geral de segurança

A [visão geral de segurança](https://aka.ms/IdentityProtectionRefresh) no portal Azure fornece uma visão da postura de segurança da sua organização. Ela ajuda a identificar possíveis ataques e compreender a eficácia de suas políticas.

A "Visão geral de segurança" é amplamente dividida em duas seções:

- Tendências, à esquerda, fornecem uma linha do tempo de risco em sua organização.
- Blocos, à direita, realçam os principais problemas atuais em sua organização e sugerem como agir rapidamente.

![Visão geral da segurança](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Tendências

### <a name="new-risky-users-detected"></a>Novos usuários arriscados detectados

Este gráfico mostra o número de novos usuários arriscados detectados durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico por nível de risco (baixo, médio, alto). Passe o mouse sobre os incrementos de data UTC para ver o número de usuários arriscados detectados nesse dia. Um clique nesse gráfico apresentará a você o relatório "Usuários arriscados". Para corrigir os usuários que estão em risco, considere alterar as respectivas senhas.

### <a name="new-risky-sign-ins-detected"></a>Novas entradas arriscadas detectadas

Este gráfico mostra o número de novas entradas arriscadas detectadas durante o período de tempo selecionado. Você pode filtrar a exibição deste gráfico pelo tipo de risco de entrada (em tempo real ou agregação) e o nível de risco de entrada (baixo, médio, alto). Entradas desprotegidas são entradas bem-sucedidas de risco em tempo real que não passaram por desafio de MFA. (Nota: Os logins que são arriscados devido às detecções offline não podem ser protegidos em tempo real por políticas de risco de login). Passe o mouse sobre os incrementos de data UTC para ver o número de entradas detectadas como em risco nesse dia. Um clique nesse gráfico apresentará a você o relatório 'Entradas arriscadas'.

## <a name="tiles"></a>Blocos
 
### <a name="high-risk-users"></a>Usuários de risco alto

O bloco "Usuários de risco alto" mostra a última contagem de usuários com alta probabilidade de comprometimento de identidade. Eles devem ser uma prioridade principal para investigação. Um clique no bloco "Usuários de risco alto" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco alto. Usando esse relatório, você pode aprender mais e corrigir esses usuários com uma redefinição de senha.

![Visão geral da segurança](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Usuários de risco médio
O bloco "Usuários de risco médio" mostra a última contagem de usuários com probabilidade média de comprometimento de identidade. Um clique no bloco "Usuários de risco médio" redirecionará para uma exibição filtrada do relatório "Usuários arriscados", mostrando apenas os usuários com um nível de risco médio. Usando esse relatório, você pode investigar e corrigir ainda mais esses usuários.

### <a name="unprotected-risky-sign-ins"></a>Entradas arriscadas desprotegidas

O azulejo 'Desprotegido de risco' mostra a contagem da última semana de logins arriscados bem-sucedidos e em tempo real que não foram bloqueados ou o MFA desafiado por uma política de acesso condicional, política de risco de proteção de identidade ou MFA por usuário. Esses são logons potencialmente comprometidos que tiveram êxito e não passaram por desafio de MFA. Para proteger essas entradas no futuro, aplique uma política de risco de entrada. Um clique no bloco "Entradas arriscadas desprotegidas" redirecionará para a folha de configuração de política de risco de entrada, na qual você poderá configurar a política de risco de entrada para exigir o MFA em uma entrada com um nível de risco especificado.

### <a name="legacy-authentication"></a>Autenticação herdada

O bloco "Autenticação herdada" mostra a contagem de autenticações herdadas realizadas na semana passada em sua organização. Protocolos de autenticação herdados não são compatíveis com métodos de segurança modernos, tais como um MFA. Para impedir a autenticação herdada, você pode aplicar uma política de acesso condicional. Um clique no bloco ‘Autenticação herdada’ redirecionará você para a ‘Classificação de Segurança de Identidade’.

### <a name="identity-secure-score"></a>Classificação de segurança de identidade

A Classificação de Segurança de Identidade mede e compara a sua postura de segurança para os padrões de mercado. Se você clicar em 'Pontuação de segurança de identidade (Visualização)' ela será redirecionada para a lâmina 'Identity Secure Score', onde você pode aprender mais sobre como melhorar sua postura de segurança.

## <a name="next-steps"></a>Próximas etapas

- [O que é risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
