---
title: Sobre o Aplicativos Móveis no Serviço de Aplicativo do Azure
description: Saiba mais sobre as vantagens que o Serviço de Aplicativo traz para seus aplicativos móveis corporativos.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 89e3fb57f29a6c24b64082db06678b3965ad8517
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388618"
---
# <a name="getting-started"> </a>Sobre Aplicativos Móveis no Serviço de Aplicativo do Azure

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

O Serviço de Aplicativo do Azure é uma oferta de [PaaS](https://azure.microsoft.com/overview/what-is-paas/) (plataforma como um serviço) para desenvolvedores profissionais. O serviço oferece um conjunto avançado de recursos para cenários Web, móveis e de integração. 

O recurso Aplicativos Móveis do Serviço de Aplicativo do Azure dá aos desenvolvedores empresariais e integradores de sistema uma plataforma de desenvolvimento de aplicativos móveis que é altamente dimensionável e globalmente disponível.

![Visão geral dos recursos dos Aplicativos Móveis](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Por que os aplicativos móveis?
Com o recurso Aplicativos Móveis, você pode:

* **Criar aplicativos nativos e multiplataforma**: quer você esteja criando aplicativos nativos iOS, Android e Windows ou aplicativos Xamarin ou Cordova (PhoneGap) multiplataforma, você pode aproveitar o Serviço de Aplicativo usando SDKs nativos.
* **Conectar-se a seus sistemas corporativos**: com o recurso Aplicativos Móveis, você pode adicionar logon corporativo em minutos e conectar-se aos recursos locais ou de nuvem de sua empresa.
* **Criar aplicativos prontos para uso offline com sincronização de dados**: torne sua força de trabalho móvel mais produtiva por meio da criação de aplicativos que trabalham offline e use Aplicativos Móveis para sincronizar dados em segundo plano quando houver conectividade com qualquer uma das suas origens de dados corporativos ou APIs SaaS (software como um serviço).
* **Notificações por push para milhões em segundos**: atraia seus clientes com notificações instantâneas de envio por push em qualquer dispositivo, personalizadas segundo suas necessidades e enviadas na hora certa.

## <a name="mobile-apps-features"></a>Recursos dos Aplicativos Móveis
Os recursos a seguir são importantes para o desenvolvimento móvel habilitado para nuvem:

* **Autenticação e autorização**: suporte para provedores de identidade, incluindo o Azure Active Directory para autenticação corporativa, além de provedores sociais, como contas do Facebook, do Google, do Twitter e da Microsoft. Os Aplicativos Móveis oferecem um serviço OAuth 2.0 para cada provedor. Você também pode integrar o SDK do provedor de identidade à funcionalidade específica do provedor.

    Descubra mais sobre os [recursos de autenticação].

* **Acesso a Dados**: os Aplicativos Móveis fornecem uma fonte de dados OData v3 compatível com dispositivos móveis vinculada ao Banco de Dados SQL do Azure ou a um SQL Server local. Como esse serviço pode ser baseado no Entity Framework, você pode integrar facilmente com outros provedores de dados NoSQL e SQL, incluindo o [Armazenamento de Tabelas do Azure], o MongoDB, o [Azure Cosmos DB] e os provedores de API SaaS, como o Office 365 e o Salesforce.com.

* **Sincronização offline**: os SDKs do nosso cliente facilitam a criação de aplicativos móveis robustos e dinâmicos que funcionam com um conjunto de dados offline. Você pode sincronizar esse conjunto de dados automaticamente com os dados de back-end, incluindo o suporte a resolução de conflitos.

  Descubra mais sobre os [recursos de dados].

* **Notificações por push**: os SDKs de cliente se integram perfeitamente aos recursos de registro dos Hubs de Notificação do Azure, permitindo que você envie notificações por push para milhões de usuários simultaneamente.

  Descubra mais sobre os [recursos de notificação por push].

* **SDKs de Cliente**: há um conjunto completo de SDKs de cliente que abrangem o desenvolvimento nativo ([iOS], [Android] e [Windows]), desenvolvimento de plataforma cruzada ([Xamarin.iOS ou Xamarin.Android], [Xamarin.Forms]) e o desenvolvimento de aplicativos híbridos ([Apache Cordova]). Cada SDK de cliente está disponível com uma licença MIT e é software livre.

## <a name="azure-app-service-features"></a>Recursos do Serviço de Aplicativo do Azure
Os recursos de plataforma abaixo são úteis para sites de produção móvel:

* **Dimensionamento automático**: com o Serviço de Aplicativo, você pode escalar verticalmente ou horizontalmente de maneira fácil para lidar com todas as cargas clientes de entrada. Selecione o número e o tamanho das VMs manualmente ou configure o dimensionamento automático para dimensionar seu back-end de aplicativo móvel com base na carga ou a agenda.

  Descubra mais sobre o [dimensionamento automático].

* **Ambientes de Preparo**: o Serviço de Aplicativo pode executar várias versões do seu site, permitindo que você execute um teste A/B, um teste em produção como parte de um plano de DevOps maior e o preparo de um novo back-end.

  Descubra mais sobre [ambientes de preparo].

* **Implantação contínua**: o Serviço de Aplicativo pode se integrar a sistemas comuns de _gerenciamento de controle de origem_ (SCM), permitindo que você implante facilmente uma nova versão do seu back-end.

  Descubra mais sobre [opções de implantação](../app-service/deploy-local-git.md).

* **Rede virtual**: o Serviço de Aplicativo pode se conectar a recursos locais usando a rede virtual, o Azure ExpressRoute ou conexões híbridas.

  Descubra mais sobre as [conexões híbridas], as [redes virtuais] e o [ExpressRoute].

* **Ambientes Isolados e dedicados**: para executar com segurança aplicativos do Serviço de Aplicativo do Azure, é possível executar o Serviço de Aplicativo em um ambiente totalmente isolado e dedicado. Esse ambiente é ideal para cargas de trabalho de aplicativos que exigem escala alta, isolamento ou acesso seguro à rede.

  Descubra mais sobre os [Ambientes do Serviço de Aplicativo].

## <a name="next-steps"></a>Próximos passos

Para começar a usar os Aplicativos Móveis no Serviço de Aplicativo do Azure, conclua o tutorial de [introdução]. O tutorial abrange os fundamentos da produção de um back-end móvel e um cliente de sua escolha. Ele também aborda a integração de autenticação, sincronização offline e notificações por push. Você pode concluir o tutorial várias vezes, uma vez para cada aplicativo cliente.

Para saber mais sobre Aplicativos Móveis, reveja nosso [mapa de aprendizado].
Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Introdução]: app-service-mobile-ios-get-started.md
[Armazenamento de Tabelas do Azure]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[recursos de autenticação]: ./app-service-mobile-auth.md
[recursos de dados]: ./app-service-mobile-offline-data-sync.md
[recursos de notificação por push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS ou Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[Dimensionamento automático]: ../app-service/manage-scale-up.md
[ambientes de preparo]: ../app-service/deploy-staging-slots.md
[conexões híbridas]: ../biztalk-services/integration-hybrid-connection-overview.md
[redes virtuais]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[Ambientes do Serviço de Aplicativo]: ../app-service/environment/intro.md
[mapa de aprendizado]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Serviço de aplicativo do Azure]: ../app-service/overview.md
