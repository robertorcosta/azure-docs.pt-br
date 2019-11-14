---
title: Implantar seu primeiro aplicativo no Cloud Foundry no Microsoft Azure
description: Implantar um aplicativo no Cloud Foundry no Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b1f9ab5289a41aacb5514e954f1ca01f6ad66152
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036839"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Implantar seu primeiro aplicativo no Cloud Foundry no Microsoft Azure

O [Cloud Foundry](https://cloudfoundry.org) é uma plataforma de aplicativos populares de software livre disponível no Microsoft Azure. Neste artigo, mostramos como implantar e gerenciar um aplicativo no Cloud Foundry em um ambiente do Azure.

## <a name="create-a-cloud-foundry-environment"></a>Criar um ambiente do Cloud Foundry

Há várias opções para a criação de um ambiente do Cloud Foundry no Azure:

- Use a [oferta de Cloud Foundry dinâmica][pcf-azuremarketplace] no Azure Marketplace para criar um ambiente padrão que inclui o PCF Ops Manager e o Service Broker do Azure. Você pode encontrar [instruções completas][pcf-azuremarketplace-pivotaldocs] para implantar a oferta do Marketplace na documentação do Pivotal.
- Crie um ambiente personalizado [implantando a Cloud Foundry dinâmica manualmente][pcf-custom].
- [Implante os pacotes de Cloud Foundry de código-fonte aberto diretamente][oss-cf-bosh] Configurando um diretor de [Bosh](https://bosh.io) , uma VM que coordena a implantação do ambiente de Cloud Foundry.

> [!IMPORTANT] 
> Se estiver implantando o PCF por meio do Azure Marketplace, anote a SYSTEMDOMAINURL e as credenciais de administrador necessárias para acessar o Pivotal Apps Manager, que são descritas no guia de implantação do marketplace. Elas são necessárias para concluir este tutorial. Para implantações do marketplace, o SYSTEMDOMAINURL está no formulário https://system.*ip-address*.cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Conectar-se ao Cloud Controller

O Cloud Controller é o ponto de entrada primário em um ambiente do Cloud Foundry para implantação e gerenciamento de aplicativos. A CCAPI (API básica do Cloud Controller) é uma API REST, mas é acessível por meio de várias ferramentas. Nesse caso, interagimos com ele por meio da [CLI do Cloud Foundry][cf-cli]. Você pode instalar a CLI no Linux, no MacOS ou no Windows, mas se preferir não instalá-lo, ele estará disponível pré-instalado no [Azure cloud Shell][cloudshell-docs].

Para fazer logon, prefixe `api` ao SYSTEMDOMAINURL que você obteve na implantação do marketplace. Como a implantação padrão usa um certificado autoassinado, você também deve incluir a opção `skip-ssl-validation`.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Você deverá fazer logon no Cloud Controller. Use as credenciais da conta do administrador que você obteve nas etapas de implantação do marketplace.

O Cloud Foundry fornece *organizações* e *espaços* como namespaces para isolar as equipes e os ambientes em uma implantação compartilhada. A implantação do marketplace PCF inclui a organização padrão *sistema* e um conjunto de espaços criados para conter os componentes base, como o serviço de dimensionamento automático e o Azure Service Broker. Por enquanto, escolha o espaço *sistema*.


## <a name="create-an-org-and-space"></a>Criar uma organização e um espaço

Se você digitar `cf apps`, verá um conjunto de aplicativos do sistema que foram implantados no espaço do sistema na organização do sistema. 

Você deve manter a organização *sistema* reservada para aplicativos do sistema; portanto, crie uma organização e um espaço para hospedar nosso aplicativo de exemplo.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Use o comando de destino para alternar para a nova organização e o novo espaço:

```bash
cf target -o testorg -s dev
```

Agora, quando você implanta um aplicativo, ele é criado automaticamente na nova organização e no novo espaço. Para confirmar se atualmente não existem aplicativos na nova organização/espaço, digite `cf apps` novamente.

> [!NOTE] 
> Para obter mais informações sobre organizações e espaços e como eles podem ser usados para RBAC (controle de acesso baseado em função), consulte a [documentação do Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Implantar um aplicativo

Vamos usar um aplicativo de exemplo do Cloud Foundry chamado Hello Spring Cloud, que é escrito em Java e baseado no [Spring Framework](https://spring.io) e no [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Clonar o repositório Hello Spring Cloud

O aplicativo de exemplo Hello Spring Cloud está disponível no GitHub. Clone-o no ambiente e altere para o novo diretório:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Compilar o aplicativo

Compile o aplicativo usando o [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Implantar o aplicativo com cf push

Você pode implantar a maioria dos aplicativos no Cloud Foundry usando o comando `push`:

```bash
cf push
```

Quando você *envia por push* um aplicativo, o Cloud Foundry detecta o tipo de aplicativo (nesse caso, um aplicativo Java) e identifica suas dependências (nesse caso, o Spring Framework). Em seguida, ele empacota tudo o que é necessário para executar o código em uma imagem de contêiner autônoma, conhecida como *droplet*. Por fim, o Cloud Foundry agenda o aplicativo em um dos computadores disponíveis no ambiente e cria uma URL na qual você pode acessá-lo, que está disponível na saída do comando.

![Saída do comando cf push][cf-push-output]

Para ver o aplicativo hello-spring-cloud, abra a URL fornecida no navegador:

![Interface do usuário padrão do Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Para saber mais sobre o que acontece durante a `cf push`, consulte [como os aplicativos são preparados][cf-push-docs] na documentação do Cloud Foundry.

## <a name="view-application-logs"></a>Exibir logs do aplicativo

Use a CLI do Cloud Foundry para exibir os logs de um aplicativo pelo nome:

```bash
cf logs hello-spring-cloud
```

Por padrão, o comando dos logs usa a *parte final*, que mostra os novos logs conforme eles são gravados. Para ver os novos logs serem exibidos, atualize o aplicativo hello-spring-cloud no navegador.

Para exibir os logs que já foram gravados, adicione a opção `recent`:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Dimensionar o aplicativo

Por padrão, `cf push` cria apenas uma única instância do aplicativo. Para garantir a alta disponibilidade e habilitar a expansão para uma maior vazão de dados, geralmente é recomendável executar mais de uma instância dos aplicativos. Escale horizontalmente os aplicativos já implantados com facilidade usando o comando `scale`:

```bash
cf scale -i 2 hello-spring-cloud
```

A execução do comando `cf app` no aplicativo mostra que o Cloud Foundry está criando outra instância do aplicativo. Depois que o aplicativo for iniciado, o Cloud Foundry iniciará automaticamente o balanceamento de carga de tráfego para ele.


## <a name="next-steps"></a>Próximas etapas

- [Leia a documentação do Cloud Foundry][cloudfoundry-docs]
- [Configurar o plug-in Azure DevOps Services para Cloud Foundry][vsts-plugin]
- [Configurar o bocal do Microsoft Log Analytics para Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
