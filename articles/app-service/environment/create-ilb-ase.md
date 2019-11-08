---
title: Criar balanceador de carga interno com um Ambiente do Serviço de Aplicativo – Azure
description: Detalhes sobre como criar e usar um ambiente do Serviço de Aplicativo do Azure isolado da Internet
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 07b47374484cf954b1fc4279c93dddcc6cec7e61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470558"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Criar e usar um Ambiente do Serviço de Aplicativo de Balanceador de Carga Interno 

O Ambiente do Serviço de Aplicativo do Azure é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede de uma VNet (rede virtual) do Azure. Há duas maneiras de implantar um ASE (ambiente do serviço de aplicativo): 

- Com um VIP em um endereço IP externo, geralmente chamado de ASE externo.
- Com um VIP em um endereço IP interno, geralmente chamado de ASE ILB devido ao ponto de extremidade interno ser um balanceador de carga interno (ILB). 

Este artigo mostra como criar um ASE ILB. Para obter uma visão geral do ASE, confira [Introdução aos ambientes do Serviço de Aplicativo][Intro]. Para saber como criar um ASE externo, confira [Como criar um ASE externo][MakeExternalASE].

## <a name="overview"></a>Visão geral 

Um ASE pode ser implantado com um ponto de extremidade acessível pela Internet ou com um endereço IP em sua VNet. Para definir o endereço IP como um endereço de VNet, o ASE deve ser implantado com um ILB. Quando você implanta o ASE com um ILB, você deve fornecer o nome do seu ASE. O nome do ASE é usado no sufixo de domínio para os aplicativos em seu ASE.  O sufixo de domínio do seu ILB ASE é &lt;ASE name&gt;.appserviceenvironment.net. Aplicativos feitos em um ILB ASE não são colocados no DNS público. 

Versões anteriores do ILB ASE exigiam que você fornecesse um sufixo de domínio e um certificado padrão para conexões HTTPS. O sufixo de domínio não é mais coletado na criação do ILB ASE, e um certificado padrão também não é mais é coletado. Agora, quando você cria um ILB ASE, o certificado padrão é fornecido pela Microsoft e é de confiança do navegador. Ainda é possível definir nomes de domínio personalizados em aplicativos em seu ASE e definir certificados nesses nomes de domínio personalizados. 

Com um ILB ASE, você pode fazer coisas como:

-   hospedar aplicativos de intranet com segurança na nuvem, que são acessados site a site ou por ExpressRoute.
-   Proteger aplicativos com um dispositivo WAF
-   Hospedar aplicativos na nuvem que não estão listados em servidores DNS públicos.
-   Criar aplicativos back-end isolados da Internet, que podem ser integrados com seus aplicativos front-end com segurança.

### <a name="disabled-functionality"></a>Funcionalidade desabilitada ###

Há algumas coisas que você não pode fazer ao usar um ASE ILB:

-   Usar SSL baseado em IP.
-   Atribuir endereços IP a aplicativos específicos.
-   Comprar e usar um certificado com um aplicativo por meio do portal do Azure. Você pode obter certificados diretamente de uma autoridade de certificação e usá-los com seus aplicativos. Não é possível obtê-los por meio do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar um ASE ILB ##

Para criar um ASE ILB:

1. No portal do Azure, selecione **Criar um recurso** > **Web** > **Ambiente do Serviço de Aplicativo** .

2. Selecione sua assinatura.

3. Selecione ou crie um grupo de recursos.

4. Insira o nome do seu Ambiente do Serviço de Aplicativo.

5. Selecione o tipo de IP virtual Interno.

    ![Criação de ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Selecione Rede

7. Selecione ou crie uma Rede Virtual. Se você criar uma nova VNet, ela será definida com um intervalo de endereços de 192.168.250.0/23. Para criar uma VNet com um intervalo de endereços diferentes ou em um grupo de recursos diferente do ASE, use o portal de criação da Rede Virtual do Azure. 

8. Selecione ou crie uma sub-rede vazia. Se você quiser selecionar uma sub-rede, ela deverá estar vazia e não delegada. O tamanho da sub-rede não pode ser alterado após a criação do ASE. O tamanho recomendado é `/24`, que tem 256 endereços e é compatível com um ASE com tamanho máximo e quaisquer necessidades de dimensionamento. 

    ![Rede do ASE][1]

7. Selecione **Revisar e Criar** e selecione **Criar**.

## <a name="create-an-app-in-an-ilb-ase"></a>Como criar um aplicativo em uma ASE ILB ##

Você pode criar um aplicativo em uma ASE ILB da mesma maneira que você cria um aplicativo em um ASE.

1. No portal do Azure, selecione **Criar um recurso** > **Web** > **Aplicativo Web**.

1. Digite o nome do aplicativo.

1. Selecione a assinatura.

1. Selecione ou crie um grupo de recursos.

1. Selecione a Publicação, Pilha de Runtime e Sistema Operacional.

1. Selecione um local que seja um ILB ASE existente.  Você também pode criar um novo ASE durante a criação do aplicativo, selecionando um plano do Serviço de Aplicativo Isolado. Se você quiser criar um novo ASE, selecione a região na qual você deseja criar o ASE.

1. Selecione ou crie um plano do Serviço de Aplicativo. 

1. Selecione **Revisar e Criar** e selecione **Criar** quando tudo estiver pronto.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Trabalhos da Web, Funções e o ILB ASE 

As Funções e os trabalhos da Web são suportados em um ILB ASE, mas para que o portal funcione com eles, você deve ter acesso de rede ao site SCM.  Isso significa que seu navegador deve estar em um host que esteja na rede virtual ou conectado a ela. Se o ILB ASE tiver um nome de domínio que não terminam em *appserviceenvironment.net*, você precisará fazer seu navegador confiar no certificado HTTPS que está sendo usado por seu site do scm.

## <a name="dns-configuration"></a>Configuração de DNS 

Ao usar um VIP Externo, o DNS é gerenciado pelo Azure. Qualquer aplicativo criado no ASE é automaticamente adicionado ao DNS do Azure, que é um DNS público. Em um ASE ILB, é necessário gerenciar seu próprio DNS. O sufixo de domínio usado com um ILB ASE depende do nome do ASE. O sufixo de domínio é *&lt;nome do ASE&gt;.appserviceenvironment.net*. O endereço IP do ILB está no portal em **Endereços IP**. 

Para configurar seu DNS:

- crie uma zona para *&lt;nome do ASE&gt;.appserviceenvironment.net*
- crie um registro A nessa zona que aponte * para o endereço IP do ILB
- crie um registro A nessa zona que aponte @ para o endereço IP do ILB
- crie uma zona em *&lt;nome do ASE&gt;.appserviceenvironment.net* chamada scm
- crie um registro A na zona scm que aponte * para o endereço IP do ILB

## <a name="publish-with-an-ilb-ase"></a>Publicação com um ASE ILB

Para cada aplicativo criado, há dois pontos de extremidade. Em um ILB ASE, você tem *&lt;nome do aplicativo&gt;.&lt;Domínio ILB ASE&gt;* e *&lt;nome do aplicativo&gt;.scm.&lt;Domínio ILB ASE&gt;* . 

O nome do site SCM leva você para o console do Kudu, que é chamado de **Portal avançado** no portal do Azure. O console do Kudu lhe permite visualizar as variáveis de ambiente, explorar o disco, usar um console e muito mais. Para obter mais informações, confira [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. 

Sistemas CI baseados na internet, como o GitHub e o Azure DevOps ainda funcionam com uma ASE ILB se o agente de build for acessível pela internet e na mesma rede como ASE ILB. Dessa forma, no caso do Azure DevOps, se o agente de build for criado na mesma VNET como ILB ASE (sub-rede diferente é adequada), poderá extrair o código do git do Azure DevOps e implantar ao ASE ILB. Se você não quiser criar seu próprio agente de build, você precisa usar um sistema de CI que usa um modelo de pull, como o Dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Este domínio é exibido no perfil de publicação do aplicativo e na folha do portal do aplicativo (**Visão geral** > **Essentials** e também **Propriedades**). Se você tiver um ILB ASE com o sufixo de domínio *&lt;nome do ASE&gt;.appserviceenvironment.net* e um aplicativo chamado *mytest*, use *mytest.&lt;nome do ASE&gt;.appserviceenvironment.net* para FTP e *mytest.scm.contoso.net* para implantação da Web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Configurar um ILB ASE com um dispositivo WAF ##

Você pode combinar um dispositivo WAF (firewall do aplicativo Web) com seu ILB ASE para expor para a internet somente os aplicativos que você quiser e manter o restante acessível apenas na VNet. Isso permite que você crie aplicativos seguros de várias camadas, entre outras coisas.

Para saber mais sobre como configurar o ILB ASE com um dispositivo WAF, confira [Configuração de um firewall do aplicativo Web com o Ambiente do Serviço de Aplicativo][ASEWAF]. Este artigo mostra como usar uma solução de virtualização Barracuda com o seu ASE. Outra opção é usar o gateway de aplicativo do Azure. O gateway de aplicativo usa as regras básicas do OWASP para proteger os aplicativos colocados atrás dele. Para obter mais informações sobre o Gateway de Aplicativo do Azure, confira [Introdução ao firewall de aplicativo Web do Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASEs criados antes de maio de 2019

ILB ASEs criados antes de maio de 2019 exigiam a definição do sufixo de domínio durante a criação do ASE. Também exigiam o upload de um certificado padrão baseado nesse sufixo de domínio. Além disso, com um ILB ASE mais antigos, você não pode executar logon único no console do Kudu com aplicativos no ILB ASE. Ao configurar o DNS para um ILB ASE mais antigo, você precisa definir o registro A de caractere curinga em uma zona que corresponda ao seu sufixo de domínio. 

## <a name="get-started"></a>Introdução ##

* Para começar a usar ASEs, confira [Introdução aos ambientes do Serviço de Aplicativo][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
