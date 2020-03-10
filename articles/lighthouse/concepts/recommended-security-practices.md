---
title: Práticas de segurança recomendadas
description: Ao usar o gerenciamento de recursos delegados do Azure, é importante considerar a segurança e o controle de acesso.
ms.date: 07/11/2019
ms.topic: conceptual
ms.openlocfilehash: 8972d6548eccb1006d90bfcbb4dba8c01b05a981
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394049"
---
# <a name="recommended-security-practices"></a>Práticas de segurança recomendadas

Ao usar o gerenciamento de recursos delegados do Azure, é importante considerar a segurança e o controle de acesso. Os usuários em seu locatário terão acesso direto a grupos de recursos e assinaturas de cliente, portanto, convirá tomar medidas para manter a segurança do seu locatário. Também será conveniente só permitir o acesso necessário para gerenciar os recursos dos seus clientes com eficiência. Esse tópico fornece recomendações para ajudá-lo a fazer isso.

## <a name="require-azure-multi-factor-authentication"></a>Exigir autenticação multifator do Azure

A [autenticação multifator do Azure](../../active-directory/authentication/concept-mfa-howitworks.md) (também conhecida como verificação em duas etapas) ajuda a impedir que invasores obtenham acesso a uma conta exigindo várias etapas de autenticação. Você deve exigir a Autenticação Multifator para todos os usuários em seu locatário do provedor de serviços, incluindo usuários que terão acesso aos recursos do cliente.

Sugerimos que você solicite que seus clientes implementem a Autenticação Multifator do Azure em seus locatários também.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Atribuir permissões a grupos usando o princípio de menor privilégio

Para facilitar o gerenciamento, é recomendável usar grupos de usuários do Azure AD para cada função necessária para gerenciar os recursos dos seus clientes. Isso permite que você adicione ou remova usuários individuais do grupo, conforme necessário, em vez de atribuir permissões diretamente a esse usuário.

Ao criar a estrutura de permissões, siga o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para concluir seu trabalho, ajudando a reduzir a chance de erros acidentais.

Por exemplo, convém usar uma estrutura como esta:

|Nome do grupo  |Tipo  |principalId  |Definição de função  |ID de definição de função  |
|---------|---------|---------|---------|---------|
|Arquitetos     |Grupo de usuários         |\<principalId\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Avaliação     |Grupo de usuários         |\<principalId\>         |Leitor         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Especialistas de VM     |Grupo de usuários         |\<principalId\>         |Colaborador de VM         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automação     |SPN (Nome da entidade de serviço)         |\<principalId\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Depois de criar esses grupos, é possível atribuir usuários conforme necessário. Somente adicione os usuários que realmente precisam ter acesso. Examine a associação de grupo regularmente e remova os usuários que não são mais adequados ou necessários para incluir.

Tenha em mente que, quando você [integrar clientes por meio de uma oferta pública de serviço gerenciado](../how-to/publish-managed-services-offers.md), qualquer grupo (ou entidade de serviço ou usuário) que você incluir terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes para trabalhar com cada cliente, será necessário publicar um plano privado separado que seja exclusivo para cada cliente ou integrar clientes individualmente usando modelos do Azure Resource Manager. Por exemplo, você pode publicar um plano público com acesso muito limitado e, em seguida, trabalhar com o cliente diretamente para integrar seus recursos para acesso adicional usando um Modelo de Recurso do Azure personalizado que concede acesso adicional, conforme necessário.


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Implantar Autenticação Multifator do Azure](../../active-directory/authentication/howto-mfa-getstarted.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
