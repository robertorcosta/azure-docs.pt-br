---
title: Delegar governança de acesso para acessar gerentes de pacotes no gerenciamento de direitos Azure AD - Azure Active Directory
description: Aprenda a delegar o controle de acesso dos administradores de TI para acessar os gerentes de pacotes e os gerentes de projeto para que eles possam gerenciar o próprio acesso.
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
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174364"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegar governança de acesso para acessar gerentes de pacotes no gerenciamento de direitos Ad do Azure

Para delegar a criação e o gerenciamento de pacotes de acesso em um catálogo, você adiciona os usuários à função gerenciador de pacotes de acesso. Os gerentes de pacotes de acesso devem estar familiarizados com a necessidade de os usuários solicitarem acesso aos recursos em um catálogo. Por exemplo, se um catálogo é usado para um projeto, então um lead de projeto pode ser um gerenciador de pacotes de acesso para esse catálogo.  Os gerenciadores de pacotes de acesso não podem adicionar recursos a um catálogo, mas eles podem gerenciar os pacotes de acesso e políticas em um catálogo.  Ao delegar a um gerenciador de pacotes de acesso, essa pessoa pode então ser responsável por:

- Quais funções um usuário terá para os recursos em um catálogo
- Quem precisará de acesso
- Quem precisa aprovar os pedidos de acesso
- Quanto tempo o projeto vai durar

Este vídeo fornece uma visão geral de como delegar o controle de acesso do proprietário do catálogo para acessar o gerenciador de pacotes.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Como proprietário de catálogo, delegue a um gerenciador de pacotes de acesso

Siga estas etapas para atribuir um usuário à função gerenciador de pacotes de acesso:

**Papel pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos** e abra o catálogo ao que deseja adicionar administradores.

1. No menu à esquerda, clique **em Funções e administradores**.

    ![Catálogos de funções e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique **em Adicionar gerentes de pacotes de acesso** para selecionar os membros para essas funções.

1. Clique **em Selecionar** para adicionar esses membros.

## <a name="remove-an-access-package-manager"></a>Remova um gerenciador de pacotes de acesso

Siga estas etapas para remover um usuário da função gerenciador de pacotes de acesso:

**Papel pré-requisito:** Administrador global, administrador de usuário ou proprietário do catálogo

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catálogos** e abra o catálogo ao que deseja adicionar administradores.

1. No menu à esquerda, clique **em Funções e administradores**.

1. Adicione uma marca de seleção ao lado de um gerenciador de pacotes de acesso que você deseja remover.

1. Clique em **Remover**.

## <a name="next-steps"></a>Próximas etapas

- [Criar um pacote de acesso](entitlement-management-access-package-create.md)
