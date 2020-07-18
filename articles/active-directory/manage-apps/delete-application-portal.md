---
title: 'Início Rápido: Excluir um aplicativo do seu locatário do Azure AD (Azure Active Directory)'
description: Este início rápido usa o portal do Azure para excluir um aplicativo de seu locatário do Azure AD (Azure Active Directory).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0b3ef559abe1c65872d8ecf87f63e6ff3ed4b0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223900"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: Excluir um aplicativo do seu locatário do Azure AD (Azure Active Directory)

Este início rápido usa o portal do Azure para excluir um aplicativo que foi adicionado ao seu locatário do Azure AD (Azure Active Directory).

## <a name="prerequisites"></a>Pré-requisitos

Para excluir um aplicativo do seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- Opcional: conclusão de [Exibir seus aplicativos](view-applications-portal.md).
- Opcional: conclusão de [Adicionar um aplicativo](add-application-portal.md).
- Opcional: conclusão de [Configurar um aplicativo](add-application-portal-configure.md).
- Opcional: conclusão de [Configurar o logon único](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Use um ambiente de não produção para testar as etapas deste início rápido.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Excluir um aplicativo do seu locatário do Azure AD

Para excluir um aplicativo do seu locatário do Azure AD:

1. No portal do Azure AD, selecione **Aplicativos empresariais**. Em seguida, encontre e selecione o aplicativo que você deseja excluir. Nesse caso, excluímos o aplicativo **GitHub_test** que adicionamos no guia de início rápido anterior.
1. Na seção **Gerenciar** do painel esquerdo, selecione **Propriedades**.
1. Selecione **Excluir** e **Sim** para confirmar que deseja excluir o aplicativo do seu locatário do Azure AD.


## <a name="next-steps"></a>Próximas etapas

- [Melhores práticas de gerenciamento de aplicativos](application-management-fundamentals.md)
- [Cenários comuns de gerenciamento de aplicativos](common-scenarios.md)
- [Visibilidade e controle do gerenciamento de aplicativos](cloud-app-security.md)