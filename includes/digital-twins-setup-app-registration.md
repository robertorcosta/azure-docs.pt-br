---
author: baanders
description: arquivo de inclusão da etapa permissões de acesso na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 18aa039977beeff3f6b16249822fbd98370ca404
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408226"
---
Depois de configurar uma instância do gêmeos digital do Azure, é comum interagir com essa instância por meio de um aplicativo cliente. Para criar um aplicativo cliente em funcionamento, você precisará certificar-se de que o aplicativo cliente será capaz de se autenticar no gêmeos digital do Azure. Isso é feito configurando um **registro de aplicativo** [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) para o aplicativo cliente usar.

Esse registro de aplicativo é onde você configura permissões de acesso para as [APIs do Azure digital gêmeos](../articles/digital-twins/how-to-use-apis-sdks.md). Posteriormente, o aplicativo cliente será autenticado no registro do aplicativo e, como resultado, receberá as permissões de acesso configuradas para as APIs.

>[!TIP]
> Como proprietário de uma assinatura, você pode preferir configurar um novo registro de aplicativo para cada nova instância de gêmeos digital do Azure *ou* para fazer isso apenas uma vez, estabelecendo um único registro de aplicativo que será compartilhado entre todas as instâncias de gêmeos digitais do Azure na assinatura.

### <a name="create-the-registration"></a>Criar o registro