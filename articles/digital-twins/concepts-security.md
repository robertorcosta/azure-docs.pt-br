---
title: Segurança para soluções dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Entenda as práticas recomendadas de segurança com o Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0f1f9e17646c4432d9c1103b3c579fc6bb0d2f13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85833157"
---
# <a name="secure-azure-digital-twins-with-role-based-access-control"></a>Proteger o gêmeos digital do Azure com o controle de acesso baseado em função

Por segurança, o Azure digital gêmeos permite o controle de acesso preciso sobre dados, recursos e ações específicos em sua implantação. Ele faz isso por meio de uma função granular e uma estratégia de gerenciamento de permissões chamada **de RBAC (controle de acesso baseado em função)**. Você pode ler sobre os princípios gerais do RBAC para o Azure [aqui](../role-based-access-control/overview.md).

## <a name="rbac-through-azure-ad"></a>RBAC por meio do Azure AD

O RBAC é fornecido ao Azure digital gêmeos por meio da integração com o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Você pode usar o RBAC para conceder permissões a uma *entidade de segurança*, que pode ser um usuário, um grupo ou uma entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo Azure AD e recebe um token 2,0 do OAuth em retorno. Esse token pode ser usado para autorizar uma solicitação de acesso a uma instância do gêmeos digital do Azure.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Com o Azure AD, o Access é um processo de duas etapas. Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar o Azure digital gêmeos, a solicitação deve ser *autenticada* e *autorizada*. 

1. Primeiro, a identidade da entidade de segurança é *autenticada*e um token OAuth 2,0 é retornado.
2. Em seguida, o token é passado como parte de uma solicitação para o serviço de gêmeos digital do Azure, para *autorizar* o acesso ao recurso especificado.

A etapa de autenticação requer que qualquer solicitação de aplicativo contenha um token de acesso OAuth 2,0 em tempo de execução. Se um aplicativo estiver sendo executado em uma entidade do Azure, como um aplicativo [Azure Functions](../azure-functions/functions-overview.md) , ele poderá usar uma **identidade gerenciada** para acessar os recursos. Leia mais sobre identidades gerenciadas na próxima seção.

A etapa de autorização requer que uma função RBAC seja atribuída à entidade de segurança. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. O Azure digital gêmeos fornece funções RBAC que abrangem conjuntos de permissões para recursos do gêmeos digital do Azure. Essas funções são descritas posteriormente neste artigo.

Para saber mais sobre funções e atribuições de função com suporte no Azure, confira [entender as diferentes funções](../role-based-access-control/rbac-and-directory-admin-roles.md) na documentação do RBAC do Azure.

### <a name="authentication-with-managed-identities"></a>Autenticação com identidades gerenciadas

[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é um recurso entre o Azure que permite que você crie uma identidade segura associada à implantação na qual o código do aplicativo é executado. Em seguida, você pode associar essa identidade a funções de controle de acesso, para conceder permissões personalizadas para acessar recursos específicos do Azure que seu aplicativo precisa.

Com identidades gerenciadas, a plataforma do Azure gerencia essa identidade de runtime. Você não precisa armazenar e proteger as chaves de acesso no código ou na configuração do aplicativo, seja para a identidade ou para os recursos que você precisa acessar. Um aplicativo cliente do Azure digital gêmeos em execução dentro de um aplicativo de serviço Azure App não precisa manipular regras e chaves de SAS nem quaisquer outros tokens de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace do gêmeos digital do Azure. Quando o aplicativo se conecta, o Azure digital gêmeos associa o contexto da entidade gerenciada ao cliente. Quando ele estiver associado a uma identidade gerenciada, o cliente de gêmeos digital do Azure poderá realizar todas as operações autorizadas. A autorização será concedida por meio da Associação de uma entidade gerenciada a uma função de RBAC do Azure digital gêmeos (descrita abaixo).

### <a name="authorization-rbac-roles-for-azure-digital-twins"></a>Autorização: funções RBAC para o gêmeos digital do Azure

O Azure fornece as funções RBAC internas abaixo para autorizar o acesso a um recurso de gêmeos digital do Azure:
* Proprietário do gêmeos digital do Azure (versão prévia) – Use essa função para fornecer acesso completo nos recursos do gêmeos digital do Azure.
* Leitor de gêmeos digital do Azure (versão prévia) – Use essa função para conceder acesso somente leitura aos recursos do gêmeos digital do Azure.

> [!TIP]
> A função leitor de gêmeos digital do Azure (versão prévia) agora também dá suporte a relações de navegação.

Para obter mais informações sobre como as funções internas são definidas, consulte [entender as definições de função](../role-based-access-control/role-definitions.md) na documentação do RBAC do Azure. Para obter informações sobre como criar funções RBAC personalizadas, consulte [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md).

Você pode atribuir funções de duas maneiras:
* por meio do painel controle de acesso (IAM) para o gêmeos digital do Azure no portal do Azure (consulte [Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure](../role-based-access-control/role-assignments-portal.md))
* por meio de comandos da CLI para adicionar ou remover uma função

Para obter etapas mais detalhadas sobre como fazer isso, experimente no [tutorial do gêmeos digital do Azure: conectar uma solução de ponta a ponta](tutorial-end-to-end.md).

## <a name="permission-scopes"></a>Escopos de permissão

Antes de atribuir uma função de RBAC a uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que é melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso aos recursos de gêmeos digitais do Azure.
* Modelos: as ações para este recurso determinam o controle sobre os [modelos](concepts-models.md) carregados no Azure digital gêmeos.
* Consultar grafo digital gêmeos: as ações para esse recurso determinam a capacidade de executar [operações de consulta](concepts-query-language.md) no gêmeos digital no grafo do Azure digital gêmeos.
* Digital: as ações desse recurso fornecem controle sobre as operações CRUD em [gêmeos digital](concepts-twins-graph.md) no grafo de entrelaçamento.
* Relação de gêmeos digital: as ações para esse recurso definem o controle sobre as operações CRUD em [relações](concepts-twins-graph.md) entre digital no grafo de entrelaçamento.
* Rota de eventos: as ações para este recurso determinam as permissões para [rotear eventos](concepts-route-events.md) do Azure digital gêmeos para um serviço de ponto de extremidade como [Hub de eventos](../event-hubs/event-hubs-about.md), [grade de eventos](../event-grid/overview.md)ou barramento de [serviço](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="next-steps"></a>Próximas etapas

* Veja como percorrer essas etapas com um aplicativo cliente de exemplo em [como autenticar um aplicativo cliente](how-to-authenticate-client.md).

* Leia mais sobre o [RBAC para Azure](../role-based-access-control/overview.md).
