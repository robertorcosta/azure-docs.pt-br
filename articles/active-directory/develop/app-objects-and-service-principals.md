---
title: Objetos de entidade de serviço e aplicativo no Azure Active Directory
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a relação entre objetos de aplicativo e de entidade de serviço no Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03054f328513c7356b02d296076c211cc1c3865e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844576"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objetos de entidade de serviço e aplicativo no Azure Active Directory

Às vezes, o significado do termo "aplicativo" pode ser interpretado de forma errada quando usado no contexto do Azure AD (Azure Active Directory). Este artigo esclarece os aspectos conceituais e concretos da integração de aplicativos do Azure AD, com uma ilustração de registro e consentimento para um [aplicativo multilocatário](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Visão Geral

Um aplicativo que foi integrado ao Azure AD tem implicações que ultrapassam o aspecto de software. "Aplicativo" é frequentemente usado como um termo conceitual, referindo-se não apenas ao software de aplicativo, mas também ao seu registro e função no Azure AD nas "conversas" de autenticação/autorização em runtime.

Por definição, um aplicativo pode funcionar nestas funções:

- Função de [cliente](developer-glossary.md#client-application) (consumindo um recurso)
- Função de [servidor de recurso](developer-glossary.md#resource-server) (expondo APIs aos clientes)
- Nas duas funções, de cliente e de servidor de recurso

Um [fluxo de Concessão de Autorização OAuth 2.0](developer-glossary.md#authorization-grant) define o protocolo de conversa, permitindo que o cliente/recurso acesse/proteja dados de um recurso, respectivamente.

Nas seções a seguir, você verá como o modelo de aplicativo do Azure AD representa um aplicativo no tempo de design e no tempo de execução.

## <a name="application-registration"></a>Registro de aplicativo

Quando você registra um aplicativo do Azure AD na [portal do Azure][AZURE-Portal], dois objetos são criados em seu locatário do Azure AD:

- Um objeto de aplicativo, e
- Um objeto de entidade de serviço

### <a name="application-object"></a>Objeto de aplicativo

Um aplicativo do Azure AD é definido por seu único objeto de aplicativo, que reside no locatário do Azure AD em que o aplicativo foi registrado, sendo conhecido como o locatário "inicial" do aplicativo. A [entidade de aplicativo][MS-Graph-App-Entity] Microsoft Graph define o esquema para as propriedades de um objeto de aplicativo.

### <a name="service-principal-object"></a>Objeto de entidade de serviço

Para acessar os recursos que são protegidos por um locatário do Azure AD, a entidade que requer acesso deve ser representada por uma entidade de segurança. Isso é verdadeiro para usuários (entidade de usuário) e aplicativos (entidade de serviço).

A entidade de segurança define a política de acesso e as permissões para o usuário/aplicativo no locatário do Azure AD. Isso habilita recursos principais como a autenticação do usuário/aplicativo durante a entrada, bem como a autorização durante o acesso aos recursos.

Quando um aplicativo recebe permissão para acessar os recursos em um locatário (após o registro ou o [consentimento](developer-glossary.md#consent)), um objeto de entidade de serviço é criado. A [entidade Microsoft Graph servicePrincipalName][MS-Graph-Sp-Entity] define o esquema para as propriedades de um objeto de entidade de serviço.

### <a name="application-and-service-principal-relationship"></a>Relação do aplicativo e a entidade de serviço

Você pode considerar o aplicativo como a representação *global* de seu aplicativo para uso em todos os locatários, e a entidade de serviço como a representação *local* para uso em um locatário específico.

O objeto de aplicativo serve como o modelo do qual as propriedades comuns e padrão são *derivadas* para uso na criação de objetos de entidade de serviço correspondentes. Portanto, um objeto de aplicativo tem uma relação de um para um com o aplicativo de software e uma relação de um para muitos com seus objetos de entidade de serviço correspondentes.

Uma entidade de serviço deve ser criada em cada locatário no qual o aplicativo é usado, permitindo o estabelecimento de uma identidade para entrada e/ou acesso aos recursos que estão sendo protegidos pelo locatário. Um aplicativo de locatário único tem apenas uma entidade de serviço (em seu locatário inicial), criado e com consentimento para uso durante o registro do aplicativo. Uma API/aplicativo Web multilocatário também tem uma entidade de serviço criada em cada locatário no qual um usuário consentiu com o seu uso.

> [!NOTE]
> As alterações feitas no objeto de aplicativo também são refletidas apenas no objeto de entidade de serviço do locatário inicial do aplicativo (o locatário em que ele foi registrado). Para aplicativos multilocatário, as alterações no objeto do aplicativo não serão refletidas em objetos de entidade de serviço dos locatários de qualquer consumidor até que o acesso seja removido por meio do [Painel de Acesso do Aplicativo](https://myapps.microsoft.com) e concedido novamente.
>
> Observe também que os aplicativos nativos são registrados como multilocatários, por padrão.

## <a name="example"></a>Exemplo

O diagrama a seguir ilustra o relacionamento entre o objeto de aplicativo de um aplicativo e os objetos de entidade de serviço correspondentes, no contexto de um aplicativo multilocatário de exemplo chamado **aplicativo de RH**. Há três locatários do Azure AD nesse exemplo de cenário:

- **Adatum**: o locatário usado pela empresa que desenvolveu o **aplicativo de RH**
- **Contoso**: o locatário utilizado pela empresa Contoso, que é consumidora do **aplicativo de HR**
- **Fabrikam**: o locatário usado pela organização Fabrikam, que também consome o **aplicativo de HR**

![Relação entre objeto de aplicativo e objeto de entidade de serviço](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Nesse cenário de exemplo:

| Etapa | Descrição |
|------|-------------|
| 1    | É o processo de criação do aplicativo e dos objetos de entidade de serviço no locatário inicial do aplicativo. |
| 2    | Quando os administradores da Contoso e da Fabrikam concluem o consentimento, um objeto de entidade de serviço é criado no locatário do Azure AD da empresa e recebe as permissões concedidas pelo administrador. Observe também que o aplicativo de RH pode ser configurado/projetado para permitir o consentimento pelos usuários para uso individual. |
| 3    | Os locatários do consumidor do aplicativo de RH (Contoso e Fabrikam) tem seu próprio objeto de entidade de serviço. Cada um deles representa o uso de uma instância do aplicativo em runtime, controlado pelas permissões concedidas pelo respectivo administrador. |

## <a name="next-steps"></a>Próximos passos

- Você pode usar o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) para consultar os objetos de aplicativo e entidade de serviço.
- Você pode acessar o objeto de aplicativo de um aplicativo usando a API Microsoft Graph, o editor de manifesto [do aplicativo portal do Azure][AZURE-Portal] ou os [cmdlets do PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado por sua [entidade de aplicativo][MS-Graph-App-Entity]OData.
- Você pode acessar o objeto de entidade de serviço de um aplicativo por meio da API do Microsoft Graph ou dos [cmdlets do PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado por sua [entidade de UserEntity][MS-Graph-Sp-Entity]do OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
