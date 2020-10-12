---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380630"
---
O cofre de chaves dá suporte a até 1024 entradas de política de acesso, com cada entrada concedendo um conjunto distinto de permissões a uma determinada entidade de segurança. Devido a essa limitação, é recomendável atribuir políticas de acesso a grupos de usuários, quando possível, em vez de usuários individuais. O uso de grupos facilita muito o gerenciamento de permissões para várias pessoas em sua organização. Para obter mais informações, consulte [gerenciar o acesso a aplicativos e recursos usando grupos de Azure Active Directory](/azure/active-directory/fundamentals/active-directory-manage-groups)

Para obter detalhes completos sobre o controle de acesso do Key Vault, confira [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](/azure/key-vault/general/overview-security#identity-and-access-management). 
