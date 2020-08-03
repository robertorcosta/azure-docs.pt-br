---
author: baanders
description: incluir arquivo para a visão geral das etapas na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407403"
---
>[!NOTE]
>Essas operações devem ser concluídas por um usuário com uma função de *proprietário* na assinatura do Azure. Embora algumas partes possam ser concluídas sem essa permissão elevada, a cooperação de um proprietário será necessária para configurar completamente uma instância utilizável. Veja mais informações sobre isso na seção [*pré-requisitos: permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para uma nova instância de gêmeos digital do Azure consiste em três partes:
1. **Criando a instância**
2. **Configurando permissões de acesso do usuário**: os usuários do Azure precisam ter a função de *proprietário do Azure digital gêmeos (versão prévia)* na instância do gêmeos digital do Azure para poder gerenciá-la e seus dados. Nesta etapa, você como um proprietário na assinatura do Azure atribuirá essa função à pessoa que gerenciará sua instância de gêmeos digital do Azure. Isso pode ser você mesmo ou outra pessoa na sua organização.
3. **Configurando permissões de acesso para aplicativos cliente**: é comum gravar um aplicativo cliente que será usado para interagir com sua instância. Para que o aplicativo cliente acesse o gêmeos digital do Azure, você precisa configurar um registro de *aplicativo* no [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) que o aplicativo cliente usará para autenticar a instância.

Para continuar, você precisará de uma assinatura do Azure. Você pode configurar um gratuitamente [aqui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
