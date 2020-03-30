---
title: Usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014462"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Relatório de usuários sinalizados para risco no portal do Azure

O Azure Active Directory (Azure AD) detecta ações suspeitas relacionadas às suas contas de usuário. Para cada ação detectada, um registro chamado [de detecção de risco](concept-risk-events.md) é criado.

Você pode acessar os relatórios de segurança do [portal do Azure](https://portal.azure.com) selecionando a folha **Azure Active Directory** e, em seguida, navegando até a seção **Segurança**. 

As detecções de risco detectadas são usadas para calcular:

- **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. 

- **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. 

Para saber como configurar as políticas que desencadeiam essas detecções de risco, consulte [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md). 

![Entradas de risco](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Qual é a licença do Azure AD necessária para acessar o relatório de usuários em risco?  

Todas as edições do Azure Active Directory fornecem relatórios de usuários sinalizados como risco. No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições do Azure Active Directory Gratuito e Básico**, você obtém uma lista de usuários sinalizados como risco. 

- Além disso, a edição **Azure Active Directory Premium 1** permite examinar algumas das detecções de risco subjacentes que foram detectadas para cada relatório. 

- A edição **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre todas as detecções de risco subjacentes e também permite configurar políticas de segurança que respondam automaticamente aos níveis de risco configurados.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>O relatório de usuários em risco para as edições gratuita e básica do Azure AD

Os usuários sinalizados para relatório de risco nas edições gratuita e básica do Azure AD fornece uma lista de contas de usuário que podem ter sido comprometidas. 

![Entradas de risco](./media/concept-user-at-risk/03.png)

A seleção de um usuário fornece informações de entrada. Você pode examinar o histórico de entradas dos usuários em risco e redefinir a senha, se necessário.

Essa caixa de diálogo fornece uma opção para:

- Baixar o relatório
- Pesquisar usuários

    ![Entradas de risco](./media/concept-user-at-risk/16.png)

Para obter informações mais detalhadas, você precisa de uma licença premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Relatório de usuários em risco para as edições premium do Azure AD

O relatório de usuários sinalizados para risco nas edições premium do Azure AD fornece a você:

- Uma lista de contas de usuário que pode ter sido comprometida 

- Informações agregadas sobre os [tipos de detecção de risco](concept-risk-events.md) detectados

- Uma opção para baixar o relatório

- Uma opção para configurar uma [política de correção de risco de usuário](../identity-protection/howto-user-risk-policy.md)  

![Entradas de risco](./media/concept-user-at-risk/71.png)

Ao selecionar um usuário, você obtém uma exibição detalhada do relatório deste usuário, que lhe habilita a:

- Abrir a exibição Todas as entradas

- Redefinir a senha do usuário

- Descartar todos os eventos

- Investigue as detecções de risco relatadas para o usuário. 

![Entradas de risco](./media/concept-user-at-risk/324.png)

Para investigar uma detecção de risco, selecione uma da lista para abrir a lâmina **Detalhes** para essa detecção de risco. Na lâmina **Detalhes,** você tem a opção de fechar manualmente uma detecção de risco ou reativar uma detecção de risco fechada manualmente. 

![Entradas de risco](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Próximas etapas

- [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de correção de risco](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

