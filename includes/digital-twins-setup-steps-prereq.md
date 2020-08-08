---
author: baanders
description: incluir arquivo para as etapas visão geral e pré-requisito de permissão na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009624"
---
>[!NOTE]
>Essas operações devem ser concluídas por um usuário com o poder de gerenciar os recursos e o acesso do usuário na assinatura do Azure. Embora algumas etapas possam ser concluídas com permissões inferiores, a cooperação de alguém com essas permissões será necessária para configurar completamente uma instância utilizável. Veja mais informações sobre isso na seção [*pré-requisitos: permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para uma nova instância de gêmeos digital do Azure consiste em três partes:
1. **Criando a instância**
2. **Configurando permissões de acesso do usuário**: os usuários do Azure precisam ter a função de *proprietário do Azure digital gêmeos (versão prévia)* na instância do gêmeos digital do Azure para poder gerenciá-la e seus dados. Nesta etapa, você como um proprietário/administrador da assinatura do Azure atribuirá essa função à pessoa que gerenciará sua instância de gêmeos digital do Azure. Isso pode ser você mesmo ou outra pessoa na sua organização.
3. **Configurando permissões de acesso para aplicativos cliente**: é comum gravar um aplicativo cliente que será usado para interagir com sua instância. Para que o aplicativo cliente acesse o gêmeos digital do Azure, você precisa configurar um registro de *aplicativo* no [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) que o aplicativo cliente usará para autenticar a instância.

Para continuar, você precisará de uma assinatura do Azure. Você pode configurar um gratuitamente [aqui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: requisitos de permissão

Para poder concluir todas as etapas neste artigo, você precisa ter uma [função em sua assinatura](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) que tenha as seguintes permissões:
* Criar e gerenciar recursos do Azure
* Gerenciar o acesso do usuário aos recursos do Azure (incluindo concessão e delegação de permissões)

Funções comuns que atendem a esse requisito são *proprietário*, *administrador da conta*ou a combinação de administrador e *colaborador*de *acesso do usuário* . Para obter uma explicação completa sobre funções e permissões, incluindo quais permissões estão incluídas com outras funções, visite [*funções de administrador de assinatura clássicas, funções do Azure e funções do Azure ad*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) na documentação do RBAC do Azure.

Para exibir sua função em sua assinatura, visite a [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure (você pode usar este link ou procurar *assinaturas* com a barra de pesquisa do Portal). Procure o nome da assinatura que você está usando e exiba sua função na coluna *minha função* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Exibição da página assinaturas no portal do Azure, mostrando o usuário como proprietário" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Se você descobrir que o valor é *colaborador*ou outra função que não tem as permissões necessárias descritas acima, você pode entrar em contato com o usuário em sua assinatura *que tem essas* permissões (como um proprietário de assinatura ou administrador de conta) e proceder de uma das seguintes maneiras:
* Solicite que eles concluam as etapas neste artigo em seu nome
* Solicite que eles elevem sua função na assinatura para que você tenha as permissões para continuar. Se isso é apropriado depende de sua organização e sua função dentro dela.