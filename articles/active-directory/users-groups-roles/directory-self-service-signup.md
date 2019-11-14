---
title: Inscrição de autoatendimento para usuários verificados por email – Azure AD | Microsoft Docs
description: Usar a inscrição de autoatendimento em um locatário do Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027627"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>O que é a inscrição de autoatendimento para o Azure Active Directory?

Este artigo explica como usar a inscrição de autoatendimento para preencher uma organização no Azure Active Directory (AD do Azure). Se você quiser assumir um nome de domínio de uma organização não gerenciada do Azure AD, consulte [assumir um diretório não gerenciado como administrador](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Por que usar a inscrição de autoatendimento?
* Leve aos clientes os serviços que eles desejam mais rápido
* Crie ofertas baseadas em email para um serviço
* Crie fluxos de inscrição baseados em email que permitam que os usuários criem identidades usando seus aliases de email de trabalho fáceis de lembrar
* Um autoatendimento criado no diretório do Azure AD pode ser convertido em um diretório gerenciado que pode ser usado para outros serviços

## <a name="terms-and-definitions"></a>Termos e definições
* **Inscrição de autoatendimento**: esse é o método pelo qual um usuário se inscreve em um serviço de nuvem e tem uma identidade criada automaticamente para eles no Azure AD com base em seu domínio de email.
* **Diretório do Azure AD não gerenciado**: este é o diretório em que a identidade é criada. Um diretório não gerenciado é um diretório sem nenhum administrador global.
* **Usuário verificado por email**: Este é um tipo de conta de usuário no AD do Azure. Um usuário que tem uma identidade criada automaticamente após a inscrição para uma oferta de autoatendimento é conhecido como um usuário verificado por email. Um usuário verificadas por email é um membro comum de um diretório marcado com creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Como controlar as configurações de autoatendimento?
Os administradores têm dois controles de autoatendimento atualmente. Eles podem controlar se:

* Os usuários podem ingressar no diretório por email
* Os usuários podem licenciar eles próprios para aplicativos e serviços

### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar esses recursos?
Um administrador pode configurar esses recursos usando os parâmetros Set-MsolCompanySettings do cmdlet do Azure AD a seguir:

* **AllowEmailVerifiedUsers** controla se um usuário pode criar ou ingressar em um diretório não gerenciado. Se você definir esse parâmetro como $false, nenhum usuário verificado por email poderá ingressar no diretório.
* **AllowAdHocSubscriptions** controla a capacidade dos usuários de executar inscrição de autoatendimento. Se você definir esse parâmetro como $false, nenhum usuário poderá executar a inscrição de autoatendimento.
  
AllowEmailVerifiedUsers e AllowAdHocSubscriptions são configurações de todo o diretório que podem ser aplicadas a um diretório gerenciado ou não gerenciado. Aqui está um exemplo onde:

* Você administra um diretório com um domínio verificado como contoso.com
* Você usa a colaboração B2B de um diretório diferente para convidar um usuário que ainda não existe (userdoesnotexist@contoso.com) no diretório inicial de contoso.com
* O diretório base tem o AllowEmailVerifiedUsers ativado

Se as condições anteriores forem verdadeiras, em seguida, um usuário de membro é criado no diretório inicial e um usuário de convidado B2B é criado no diretório que convida.

As entradas de avaliação do Flow e do PowerApps não são controladas pela configuração **AllowAdHocSubscriptions** . Para obter mais informações, consulte os seguintes artigos:

* [Como posso impedir meus usuários existentes de começarem a usar o Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Perguntas e respostas sobre o Flow na sua organização](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Como os controles funcionam juntos?
Esses dois parâmetros podem ser usados em conjunto para definir um controle mais preciso sobre a inscrição de autoatendimento. Por exemplo, o comando a seguir permitirá que os usuários executem inscrição de autoatendimento, mas somente se esses usuários já tiverem uma conta no Azure AD (em outras palavras, os usuários que precisarem de uma conta verificada por email para serem criados primeiro não poderão executar a inscrição por autoatendimento):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

O fluxograma a seguir explica as diferentes combinações para esses parâmetros e as condições resultantes para o diretório e a inscrição de autoatendimento.

![fluxograma de controles de inscrição de autoatendimento](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Para obter mais informações e exemplos de como usar esses parâmetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Próximas etapas

* [Adicionar um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlets do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Fechar sua conta corporativa ou de estudante em um diretório não gerenciado](users-close-account.md)
