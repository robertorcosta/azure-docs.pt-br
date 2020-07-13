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
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955991"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: Excluir um aplicativo do seu locatário do Azure AD (Azure Active Directory)

Este guia de início rápido usa o portal do Azure para excluir um aplicativo que foi adicionado ao seu locatário do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Para excluir um aplicativo do seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- (Opcional: Conclusão de [Exibir seus aplicativos](view-applications-portal.md)).
- (Opcional: Conclusão de [Adicionar um aplicativo](add-application-portal.md)).
- (Opcional: Conclusão de [Configurar um aplicativo](add-application-portal-configure.md)).
- (Opcional: Conclusão de [Configurar o logon único](add-application-portal-setup-sso.md)).

>[!IMPORTANT]
>Recomendamos usar um ambiente que não seja de produção para testar as etapas neste início rápido.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Excluir um aplicativo do seu locatário do Azure AD

Para excluir um aplicativo do seu locatário do Azure AD:

1. No portal do Azure AD, selecione **Aplicativos empresariais** e localize e escolha o aplicativo que você deseja excluir. Nesse caso, excluímos o aplicativo GitHub_test que adicionamos no guia de início rápido anterior.
2. Na seção Gerenciar da navegação, selecione **Propriedades**.
3. Selecione Excluir e Sim para confirmar que deseja excluir o aplicativo do seu locatário do Azure AD.


## <a name="next-steps"></a>Próximas etapas

- [Melhores práticas de gerenciamento de aplicativos](application-management-fundamentals.md)
- [Cenários comuns de gerenciamento de aplicativos](common-scenarios.md)
- [Visibilidade e controle do gerenciamento de aplicativos](cloud-app-security.md)