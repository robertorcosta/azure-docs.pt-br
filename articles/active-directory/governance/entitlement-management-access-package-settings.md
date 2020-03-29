---
title: Link de compartilhamento para solicitar um pacote de acesso no gerenciamento de direitos Azure AD - Azure Active Directory
description: Saiba como compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968755"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Link de compartilhamento para solicitar um pacote de acesso no gerenciamento de direitos ad do Azure

A maioria dos usuários em seu diretório pode entrar no portal Meu Acesso e ver automaticamente uma lista de pacotes de acesso que eles podem solicitar. No entanto, para usuários parceiros de negócios externos que ainda não estão em seu diretório, você precisará enviar-lhes um link que eles podem usar para solicitar um pacote de acesso. 

Desde que o catálogo do pacote de acesso esteja [habilitado para usuários externos](entitlement-management-catalog-create.md) e você tenha uma [política para o diretório do usuário externo,](entitlement-management-access-package-request-policy.md)o usuário externo pode usar o link do portal Meu Acesso para solicitar o pacote de acesso.

## <a name="share-link-to-request-an-access-package"></a>Link de compartilhamento para solicitar um pacote de acesso

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Na página Visão Geral, copie o link do **portal Meu Acesso**.

    ![Visão geral do pacote de acesso – Link do portal Meus Acessos](./media/entitlement-management-shared/my-access-portal-link.png)

    É importante que você copie todo o link do portal Meu Acesso ao enviá-lo para um parceiro de negócios interno. Isso garante que o parceiro tenha acesso ao portal do seu diretório para fazer sua solicitação. O link `myaccess`começa com , inclui uma dica de diretório, e termina com um ID do pacote de acesso.  (Para o governo dos EUA, o domínio `myaccess.microsoft.us`no link do portal Meu Acesso será .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Envie um e-mail ou envie o link para seu parceiro de negócios externo. Eles podem compartilhar o link com seus usuários para solicitar o pacote de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Solicite acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)