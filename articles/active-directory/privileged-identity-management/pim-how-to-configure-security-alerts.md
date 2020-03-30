---
title: Alertas de segurança para funções do Azure AD no PIM - Azure AD | Microsoft Docs
description: Configure alertas de segurança para funções azure AD Privilegiação de identidade privilegiada no Diretório Ativo do Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253306"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configure alertas de segurança para funções azure AD no Gerenciamento de Identidade Privilegiada

O PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) gera alertas quando há atividadesuspeita ou insegura em sua organização Azure Active Directory (Azure AD). Quando um alerta é acionado, ele aparece no painel de gerenciamento de identidade privilegiado. Selecione o alerta para ver um relatório que lista os usuários ou as funções que dispararam o alerta.

## <a name="determine-your-version-of-pim"></a>Determine sua versão do PIM

A partir de novembro de 2019, a parte de funções Azure AD do Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências para funções de recursos do Azure. Isso cria recursos adicionais, bem como [alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo lançada, quais procedimentos você segue neste artigo dependem da versão do Gerenciamento de Identidade Privilegiada que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Gerenciamento de Identidade Privilegiada você tem. Depois de conhecer sua versão do Gerenciamento de Identidade Privilegiada, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que esteja na função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Abra **o Azure AD Privileged Identity Management**. Se você tiver um banner na parte superior da página de visão geral, siga as instruções na guia **Nova versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga as etapas deste artigo para investigar alertas de segurança para as funções do Azure AD.

# <a name="new-version"></a>[Nova versão](#tab/new)

![Funções Azure AD - Alertas de listagem de painéis de alerta e a gravidade](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta seção lista todos os alertas de segurança para funções ad do Azure, juntamente com como corrigir e como prevenir. Severidade tem o seguinte significado:

- **Alta**: exige ação imediata devido a uma violação da política.
- **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
- **Baixa**: não requer ação imediata, mas sugere uma alteração de política preferencial.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão usando suas funções privilegiadas

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Os usuários que receberam papéis privilegiados que não precisam aumentam a chance de um ataque. Também é mais fácil para os invasores permanecerem despercebidos nas contas que não estão sendo ativamente usadas. |
| **Como consertar?** | Revise os usuários da lista e remova-os de funções privilegiadas que eles não precisam. |
| **Prevenção** | Atribua funções privilegiadas apenas aos usuários que têm uma justificativa de negócios. </br>Agende revisões de [acesso regulares](pim-how-to-start-security-review.md) para verificar se os usuários ainda precisam de acesso. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se um usuário passar por um número especificado de dias sem ativar uma função. |
| **Número de dias** | Esta configuração especifica o número máximo de dias, de 0 a 100, que um usuário pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não exigem autenticação multifator para ativação

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Sem autenticação multifatorial, usuários comprometidos podem ativar funções privilegiadas. |
| **Como consertar?** | Revise a lista de funções e [exija autenticação multifatorial](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de mitigação no portal** | Torna necessária a autenticação multifatorial para ativação da função privilegiada. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | A atual organização Azure AD não tem Azure AD Premium P2. |
| **Como consertar?** | Revise informações sobre [edições do Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md). Atualizar para o Microsoft Azure Active Directory Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas possíveis em uma função com privilégios

| | |
| --- | --- |
| **Severidade** | Médio |
| **Por que recebo este alerta?** | As contas em uma função privilegiada não mudaram sua senha nos últimos 90 dias. Essas contas podem ser de serviço ou compartilhadas, que não estejam passando por manutenção e estejam vulneráveis aos invasores. |
| **Como consertar?** | Examine as contas na lista. Se eles não precisarem mais de acesso, remova-os de suas funções privilegiadas. |
| **Prevenção** | Certifique-se de que as contas compartilhadas estejam girando senhas fortes quando houver uma alteração nos usuários que conhecem a senha. </br>Revise regularmente contas com funções privilegiadas usando [revisões de acesso](pim-how-to-start-security-review.md) e remova atribuições de funções que não são mais necessárias. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Práticas recomendadas** | Contas de acesso compartilhado, de serviço e de emergência que autenticam usando uma senha e são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter suas senhas giradas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou comprometimento de direitos de acesso administrativo</li><li>Depois que os privilégios de qualquer usuário são alterados para que eles não sejam mais administradores (por exemplo, depois que um funcionário que era administrador deixa a TI ou deixa a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não haja nenhuma violação ou alteração conhecida na equipe de TI</li></ul>Como várias pessoas têm acesso às credenciais dessas contas, as credenciais devem ser rotacionadas para garantir que as pessoas que deixaram suas funções não possam mais acessar as contas. [Saiba mais sobre como garantir contas](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Funções estão sendo atribuídas fora do Gerenciamento de Identidade Privilegiada

| | |
| --- | --- |
| **Severidade** | Alta |
| **Por que recebo este alerta?** | As atribuições de função privilegiadas feitas fora do Gerenciamento de Identidade Privilegiada não são adequadamente monitoradas e podem indicar um ataque ativo. |
| **Como consertar?** | Revise os usuários da lista e remova-os de funções privilegiadas atribuídas fora do Gerenciamento de Identidade Privilegiada. |
| **Prevenção** | Investigue onde os usuários estão sendo atribuídos funções privilegiadas fora do Gerenciamento de Identidade Privilegiada e proíba futuras atribuições a partir daí. |
| **Ação de mitigação no portal** | Remove o usuário de sua função privilegiada. |

### <a name="there-are-too-many-global-administrators"></a>Há muitos administradores globais

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Administrador global é o papel mais privilegiado. Se um administrador global for comprometido, o invasor obtém acesso a todas as suas permissões, o que coloca todo o seu sistema em risco. |
| **Como consertar?** | Revise os usuários da lista e remova qualquer um que não precise absolutamente da função de administrador Global. </br>Em vez disso, atribua funções privilegiadas mais baixas a esses usuários. |
| **Prevenção** | Designe aos usuários a função menos privilegiada de que precisam. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se dois critérios diferentes forem atendidos e você puder configurar ambos. Primeiro, você precisa atingir um certo limite de administradores globais. Em segundo lugar, uma certa porcentagem de suas atribuições totais de função deve ser de administradores globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido. |
| **Número mínimo de administradores globais** | Esta configuração especifica o número de administradores globais, de 2 a 100, que você considera muito poucos para sua organização Azure AD. |
| **Percentual de administradores globais** | Essa configuração especifica a porcentagem mínima de administradores que são administradores globais, de 0% a 100%, abaixo do qual você não quer que sua organização Azure AD caia. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão sendo ativadas com muita frequência

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo usuário é um sinal de um ataque. |
| **Como consertar?** | Revise os usuários na lista e assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para sua função privilegiada esteja definida por tempo suficiente para que eles executem suas tarefas. |
| **Prevenção** | Assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para funções privilegiadas esteja definida por tempo suficiente para que os usuários executem suas tarefas.</br>[Requerem autenticação multifatorial](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas compartilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Gatilho** | Disparado se um usuário ativar a mesma função privilegiada várias vezes dentro de um período especificado. Você pode configurar o período e o número de ativações. |
| **Período de tempo de renovação de ativação** | Essa configuração especifica em dias, horas, minutos e segundos o período de tempo que você deseja usar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Esta configuração especifica o número de ativações, de 2 a 100, nas quais você gostaria de ser notificado, dentro do prazo escolhido. Você pode mudar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Na página Alertas, vá para **Configurações**.

![Página de alertas com Configurações destacadas](media/pim-how-to-configure-security-alerts/alert-settings.png)

Personalize configurações nos diferentes alertas para trabalhar com seu ambiente e as metas de segurança.

![Definir página para um alerta para ativar e configurar configurações](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

![Funções Azure AD - Alertas de listagem de painéis de alerta e a gravidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta seção lista todos os alertas de segurança para funções ad do Azure, juntamente com como corrigir e como prevenir. Severidade tem o seguinte significado:

- **Alta**: exige ação imediata devido a uma violação da política.
- **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
- **Baixa**: não requer ação imediata, mas sugere uma alteração de política preferencial.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão usando suas funções privilegiadas

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Os usuários que receberam papéis privilegiados que não precisam aumentam a chance de um ataque. Também é mais fácil para os invasores permanecerem despercebidos nas contas que não estão sendo ativamente usadas. |
| **Como consertar?** | Revise os usuários da lista e remova-os de funções privilegiadas que eles não precisam. |
| **Prevenção** | Atribua funções privilegiadas apenas aos usuários que têm uma justificativa de negócios. </br>Agende revisões de [acesso regulares](pim-how-to-start-security-review.md) para verificar se os usuários ainda precisam de acesso. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se um usuário passar por um número especificado de dias sem ativar uma função. |
| **Número de dias** | Esta configuração especifica o número máximo de dias, de 0 a 100, que um usuário pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não exigem autenticação multifator para ativação

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Sem autenticação multifatorial, usuários comprometidos podem ativar funções privilegiadas. |
| **Como consertar?** | Revise a lista de funções e [exija autenticação multifatorial](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de mitigação no portal** | Torna necessária a autenticação multifatorial para ativação da função privilegiada. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | A atual organização Azure AD não tem Azure AD Premium P2. |
| **Como consertar?** | Revise informações sobre [edições do Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md). Atualizar para o Microsoft Azure Active Directory Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas possíveis em uma função com privilégios

| | |
| --- | --- |
| **Severidade** | Médio |
| **Por que recebo este alerta?** | As contas em uma função privilegiada não mudaram sua senha nos últimos 90 dias. Essas contas podem ser de serviço ou compartilhadas, que não estejam passando por manutenção e estejam vulneráveis aos invasores. |
| **Como consertar?** | Examine as contas na lista. Se eles não precisarem mais de acesso, remova-os de suas funções privilegiadas. |
| **Prevenção** | Certifique-se de que as contas compartilhadas estejam girando senhas fortes quando houver uma alteração nos usuários que conhecem a senha. </br>Revise regularmente contas com funções privilegiadas usando [revisões de acesso](pim-how-to-start-security-review.md) e remova atribuições de funções que não são mais necessárias. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Práticas recomendadas** | Contas de acesso compartilhado, de serviço e de emergência que autenticam usando uma senha e são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter suas senhas giradas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou comprometimento de direitos de acesso administrativo</li><li>Depois que os privilégios de qualquer usuário são alterados para que eles não sejam mais administradores (por exemplo, depois que um funcionário que era administrador deixa a TI ou deixa a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não haja nenhuma violação ou alteração conhecida na equipe de TI</li></ul>Como várias pessoas têm acesso às credenciais dessas contas, as credenciais devem ser rotacionadas para garantir que as pessoas que deixaram suas funções não possam mais acessar as contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Funções estão sendo atribuídas fora do Gerenciamento de Identidade Privilegiada

| | |
| --- | --- |
| **Severidade** | Alta |
| **Por que recebo este alerta?** | As atribuições de função privilegiadas feitas fora do Gerenciamento de Identidade Privilegiada não são adequadamente monitoradas e podem indicar um ataque ativo. |
| **Como consertar?** | Revise os usuários da lista e remova-os de funções privilegiadas atribuídas fora do Gerenciamento de Identidade Privilegiada. |
| **Prevenção** | Investigue onde os usuários estão sendo atribuídos funções privilegiadas fora do Gerenciamento de Identidade Privilegiada e proíba futuras atribuições a partir daí. |
| **Ação de mitigação no portal** | Remove o usuário de sua função privilegiada. |

### <a name="there-are-too-many-global-administrators"></a>Há muitos administradores globais

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Administrador global é o papel mais privilegiado. Se um administrador global for comprometido, o invasor obtém acesso a todas as suas permissões, o que coloca todo o seu sistema em risco. |
| **Como consertar?** | Revise os usuários da lista e remova qualquer um que não precise absolutamente da função de administrador Global. </br>Em vez disso, atribua funções privilegiadas mais baixas a esses usuários. |
| **Prevenção** | Designe aos usuários a função menos privilegiada de que precisam. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se dois critérios diferentes forem atendidos e você puder configurar ambos. Primeiro, você precisa atingir um certo limite de administradores globais. Em segundo lugar, uma certa porcentagem de suas atribuições totais de função deve ser de administradores globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido. |
| **Número mínimo de administradores globais** | Esta configuração especifica o número de administradores globais, de 2 a 100, que você considera muito poucos para sua organização Azure AD. |
| **Percentual de administradores globais** | Essa configuração especifica a porcentagem mínima de administradores que são administradores globais, de 0% a 100%, abaixo do qual você não quer que sua organização Azure AD caia. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão sendo ativadas com muita frequência

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo usuário é um sinal de um ataque. |
| **Como consertar?** | Revise os usuários na lista e assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para sua função privilegiada esteja definida por tempo suficiente para que eles executem suas tarefas. |
| **Prevenção** | Assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para funções privilegiadas esteja definida por tempo suficiente para que os usuários executem suas tarefas.</br>[Requerem autenticação multifatorial](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas compartilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Gatilho** | Disparado se um usuário ativar a mesma função privilegiada várias vezes dentro de um período especificado. Você pode configurar o período e o número de ativações. |
| **Período de tempo de renovação de ativação** | Essa configuração especifica em dias, horas, minutos e segundos o período de tempo que você deseja usar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Esta configuração especifica o número de ativações, de 2 a 100, nas quais você gostaria de ser notificado, dentro do prazo escolhido. Você pode mudar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Você pode personalizar alguns dos alertas de segurança no Gerenciamento de Identidade Privilegiada para trabalhar com as necessidades e metas de segurança da sua organização. Siga estas etapas para abrir as configurações de alerta de segurança:

1. Gestão **de identidade privilegiada aberta** no Azure AD.

1. Selecione **funções Azure AD**.

1. Selecione **Configurações** e, em seguida, **Alertas**.

    ![Funções azure AD - Configurações com alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecione um nome de alerta para configurar a configuração desse alerta.

    ![Para o alerta selecionado, painel de configurações de alerta de segurança](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Próximas etapas

- [Configure as configurações de função AD do Azure no Gerenciamento de Identidade Privilegiada](pim-how-to-change-default-settings.md)
