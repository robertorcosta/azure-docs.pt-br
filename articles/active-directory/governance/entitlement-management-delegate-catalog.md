---
title: Delegar governança de acesso para criadores de catálogo no gerenciamento de direitos do Azure AD (versão prévia) – Azure Active Directory
description: Saiba como delegar a governança de acesso dos administradores de ti aos criadores de catálogo e aos gerentes de projeto para que eles possam gerenciar o acesso.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170757"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Delegar governança de acesso para criadores de catálogo no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para delegar a usuários que não são administradores, para que eles possam criar seus próprios catálogos, você pode adicionar esses usuários à função de criador de catálogo definida pelo gerenciamento de direitos do Azure AD. Você pode adicionar usuários individuais ou pode adicionar um grupo, cujos membros são então capazes de criar catálogos.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Como um administrador de ti, delegado a um criador de catálogo

Siga estas etapas para atribuir um usuário à função de criador de catálogo.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, na seção **Gerenciamento de direitos** , clique em **configurações**.

1. Clique em **Editar**.

    ![Configurações para adicionar criadores de catálogo](./media/entitlement-management-delegate/settings-delegate.png)

1. Na seção **Gerenciamento de direitos de representante** , clique em **Adicionar criadores de catálogo** para selecionar os usuários ou grupos aos quais você deseja delegar essa função de gerenciamento de direitos.

1. Clique em **Selecionar**.

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

- [Criar e gerenciar um catálogo de recursos](entitlement-management-catalog-create.md)
- [Delegar governança de acesso para acessar gerenciadores de pacotes](entitlement-management-delegate-managers.md)

