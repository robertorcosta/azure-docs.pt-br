---
title: Delegar governança de acesso aos criadores de catálogos no gerenciamento de direitos Azure AD - Azure Active Directory
description: Aprenda a delegar o controle de acesso dos administradores de TI aos criadores de catálogos e gerentes de projetos para que eles possam gerenciar o próprio acesso.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120184"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegar governança de acesso a criadores de catálogos na gestão de direitos Ad do Azure

Um catálogo é um contêiner de recursos e pacotes de acesso. Você cria um catálogo quando deseja agrupar recursos relacionados e pacotes de acesso. Por padrão, um administrador Global ou um administrador de usuário podem [criar um catálogo](entitlement-management-catalog-create.md)e adicionar usuários adicionais como proprietários de catálogos.

Para delegar aos usuários que não são administradores, para que eles possam criar seus próprios catálogos, você pode adicionar esses usuários à função de criador de catálogo sustal definido pelo Azure AD. Você pode adicionar usuários individuais ou adicionar um grupo, cujos membros são capazes de criar catálogos.  Depois de criar um catálogo, eles podem posteriormente adicionar recursos que possuem ao seu catálogo.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Como administrador de TI, delegue a um criador de catálogo

Siga essas etapas para atribuir um usuário à função de criador do catálogo.

**Papel pré-requisito:** Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, na seção **De administração de direitos,** clique em **Configurações**.

1. Clique em **Editar**.

    ![Configurações para adicionar criadores de catálogos](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Na seção **Dedém de gerenciamento de direitos,** clique **em Adicionar criadores de catálogo** para selecionar os usuários ou grupos para os os qual você deseja delegar essa função de gerenciamento de direitos.

1. Clique em **Selecionar**.

1. Clique em **Salvar**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Permitir que funções delegadas acessem o portal Azure

Para permitir que funções delegadas, como criadores de catálogos e gerenciadores de pacotes de acesso, acessem o portal Azure para gerenciar pacotes de acesso, você deve verificar a configuração do portal de administração.

**Papel pré-requisito:** Administrador global ou administrador de usuário

1. No portal Azure, clique em **Azure Active Directory** e clique **em Usuários**.

1. No menu à esquerda, clique **em Configurações do usuário**.

1. Certifique-se de que **o acesso restrito ao portal de administração do Azure AD** está definido como **No**.

    ![Configurações do usuário Azure AD - Portal de administração](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Próximas etapas

- [Criar e gerenciar um catálogo de recursos](entitlement-management-catalog-create.md)
- [Delegar governança de acesso para acessar gerentes de pacotes](entitlement-management-delegate-managers.md)

