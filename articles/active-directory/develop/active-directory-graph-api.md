---
title: API do Graph do Azure Active Directory | Microsoft Docs
description: Uma visão geral e guia de início rápido para a API do Graph do Azure AD, que permite acesso programático ao Azure AD por meio de pontos de extremidade da API REST.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9515238449b3ece79815c9b3c616bd715b3b3400
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476534"
---
# <a name="azure-active-directory-graph-api"></a>API do Graph do Active Directory do Azure

> [!IMPORTANT]
> Recomendamos fortemente que você use [o Microsoft Graph](https://developer.microsoft.com/graph) em vez da Azure AD Graph API para acessar os recursos do Azure Active Directory (Azure AD). Nossos esforços de desenvolvimento agora estão concentrados no Microsoft Graph e não há planejamento de novas melhorias para a API do Azure AD Graph. Há um número muito limitado de cenários para os quais a API ad graph do Azure ainda pode ser apropriada; para obter mais informações, consulte o [Microsoft Graph ou o blog Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) e os aplicativos Migrate [Azure AD Graph para o Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Este artigo se aplica à API do Graph do Azure Active Directory. Para obter informações semelhantes relacionadas a API do Microsoft Graph, consulte [usar a API do Graph](https://docs.microsoft.com/graph/use-the-api).

A API do Graph do Active Directory do Azure fornece acesso programático ao AD do Azure por meio de pontos de extremidade da API REST. Aplicativos podem usar a API do Microsoft Azure AD Graph para realizar operações CRUD (criar, ler, atualizar e excluir) nos objetos e dados do diretório. Por exemplo, a API do Microsoft Azure AD Graph fornece suporte às seguintes operações comuns para um objeto de usuário:

* Criar um novo usuário em um diretório
* Obtenha propriedades detalhadas de um usuário, como seus grupos
* Atualize as propriedades de um usuário, como sua localização e número de telefone, ou altere sua senha
* Verifique a adesão de um grupo de usuários para obter acesso baseado em função
* Desative a conta de um usuário ou exclua-a inteiramente

Além disso, você pode executar operações semelhantes em outros objetos, como grupos e aplicativos. Para chamar a API do Microsoft Azure AD Graph em um diretório, o aplicativo deve estar registrado no Microsoft Azure AD. O aplicativo também deve ter acesso concedido para API do Microsoft Azure AD Graph. Esse acesso é normalmente obtido por meio de um fluxo de consentimento do usuário ou administrador.

Para começar a usar a API do Azure Active Directory Graph, consulte o guia de inicialação rápida do [Azure AD Graph API](active-directory-graph-api-quickstart.md)ou visualize a documentação interativa de [referência da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Recursos

API do Microsoft Azure AD Graph fornece os seguintes recursos:

* **Pontos de extremidade da API REST**: a API do Microsoft Azure AD Graph é um serviço RESTful composto por pontos de extremidade acessados por meio de solicitações HTTP padrão. A API do Microsoft Azure AD Graph fornece suporte a tipos de conteúdo XML ou JavaScript Object Notation (JSON) para solicitações e respostas. Para saber mais, consulte [Referência da API REST do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autenticação com Microsoft Azure AD**: cada solicitação à API do Microsoft Azure AD Graph deve ser autenticada com o anexo de um JSON Web Token (JWT) no cabeçalho de Autorização da solicitação. Este token é adquirido fazendo uma solicitação ao ponto final do token do Azure AD e fornecendo credenciais válidas. Você pode usar o fluxo de credenciais de cliente OAuth 2.0 ou o fluxo de concessão do código de autorização para adquirir um token para chamar o Graph. Para obter mais informações, veja [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **RBAC (Autorização baseada em função)**: grupos de segurança são usados para executar a RBAC na API do Microsoft Azure AD Graph. Por exemplo, se você quiser determinar se um usuário tem acesso a um recurso específico, o aplicativo poderá chamar a operação [Verificar associação de grupo (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups), que retornará verdadeiro ou falso.
* **Consulta diferencial**: consulta diferencial permite controlar as alterações em um diretório entre dois períodos de tempo sem precisar fazer consultas frequentes à API do Microsoft Azure AD Graph. Este tipo de solicitação retornará apenas as alterações feitas entre a solicitação de consulta diferencial anterior e a solicitação atual. Para saber mais, leia [Consulta diferencial da API do Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Extensões de diretório**: é possível adicionar propriedades personalizadas a objetos de diretório sem exigir um armazenamento de dados externo. Por exemplo, se o aplicativo exigir uma propriedade de ID do Skype para cada usuário, você poderá registrar a nova propriedade no diretório e estará disponível para uso em cada objeto de usuário. Para obter mais informações, consulte [Extensões de esquema do diretório da API do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protegido pelos escopos de permissão**: a API do Microsoft Azure AD Graph expõe escopos de permissão que permitem acesso seguro aos dados do Microsoft Azure AD usando o OAuth 2.0. Isso fornece suporta a uma variedade de tipos de aplicativo cliente, incluindo:
  
  * interfaces de usuário que recebem acesso delegado aos dados por meio de autorização do usuário conectado (delegado)
  * aplicativos de serviço/daemon que funcionam em segundo plano sem que um usuário conectado esteja presente e use o controle de acesso baseado em função definido pelo aplicativo
    
    Ambas as permissões delegadas e de aplicativo representam um privilégio exposto pela API do Microsoft Azure AD Graph e podem ser solicitadas pelos aplicativos clientes por meio dos recursos de permissões de registro do aplicativo no [Portal do Azure](https://portal.azure.com). [Os escopos de permissão da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) fornecem informações sobre o que está disponível para uso pelo aplicativo cliente.

## <a name="scenarios"></a>Cenários

A API do Microsoft Azure AD Graph habilita muitos cenários de aplicativos. A seguir, os cenários mais comuns:

* **Aplicativo de linha de negócios (único locatário)**: neste cenário, um desenvolvedor corporativo trabalha para uma organização que tem uma assinatura do Office 365. O desenvolvedor está criando um aplicativo da Web que interage com o Azure AD para executar tarefas, como atribuir uma licença a um usuário. Essa tarefa exige acesso à API do Graph do Azure AD, portanto, o desenvolvedor registra o aplicativo de locatário único no Azure AD e configura as permissões de leitura e gravação da API do Graph do Azure AD. Em seguida, o aplicativo é configurado para usar suas próprias credenciais ou as do usuário de login atual para adquirir um token para chamar a API do Graph do Azure AD.
* **Software como um aplicativo de serviço (multilocatário)**: nesse cenário, um fornecedor de software independente (ISV) está desenvolvendo um aplicativo da Web multilocatário hospedado que fornece recursos de gerenciamento de usuário para outras organizações que usam o Azure AD. Esses recursos exigem acesso a objetos de diretório, portanto, o aplicativo precisa chamar a API do Graph do Azure AD. O desenvolvedor registra o aplicativo no Microsoft Azure AD, configura para exigir as permissões de leitura e gravação para a API do Microsoft Azure AD Graph e habilita o acesso externo para que outras organizações possam concordar com o uso do aplicativo em seu diretório. Quando um usuário de outra organização autentica o aplicativo pela primeira vez, é mostrada uma caixa de diálogo de consentimento com as permissões que o aplicativo está solicitando. A concessão de consentimento dará ao aplicativo as permissões solicitadas à API ad Graph azure no diretório do usuário. Para saber mais sobre a estrutura de consentimento, consulte [Visão geral da estrutura de consentimento](consent-framework.md).

## <a name="next-steps"></a>Próximas etapas

Para começar a usar a API do Graph do Azure Active Directory, consulte os tópicos a seguir:

* [Guia de início rápido para a API do Graph do Azure AD](active-directory-graph-api-quickstart.md)
* [Documentação do REST para a Microsoft Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
