---
title: Como funciona o roteamento com o Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos que alimentam os espaços de dev do Azure e como funciona o roteamento
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241381"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Como funciona o roteamento com o Azure Dev Spaces

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente os aplicativos kubernetes e colaborar com sua equipe em um cluster Azure Kubernetes Service (AKS). Uma vez que seu projeto esteja sendo executado em um espaço de desenvolvimento, o Azure Dev Spaces fornece recursos adicionais de rede e roteamento para o seu projeto.

Este artigo descreve como o roteamento funciona com o Dev Spaces.

## <a name="how-routing-works"></a>Como funciona o roteamento

Um espaço de v é construído em cima do AKS e usa os [mesmos conceitos de rede](../aks/concepts-network.md). O Azure Dev Spaces também possui um serviço centralizado de gerente de *ingresse* e implanta seu próprio Controlador de Ingress para o cluster AKS. O serviço *de ingressmanager* monitora clusters AKS com espaços de desenvolvimento e aumenta o Azure Dev Spaces Ingress Controller no cluster com objetos Ingress para roteamento para pods de aplicativos. O contêiner proxy devspaces em `azds-route-as` cada pod adiciona um cabeçalho HTTP para tráfego HTTP a um espaço de dev com base na URL. Por exemplo, uma solicitação *http://azureuser.s.default.serviceA.fedcba09...azds.io* à URL obteria um cabeçalho HTTP com `azds-route-as: azureuser`. O contêiner devspaces-proxy não `azds-route-as` adicionará um cabeçalho se um já estiver presente.

Quando uma solicitação HTTP é feita a um serviço de fora do cluster, a solicitação vai para o controlador Ingress. O controlador ingress encaminha a solicitação diretamente para o pod apropriado com base em seus objetos e regras de Ingress. O contêiner proxy devspaces no pod recebe `azds-route-as` a solicitação, adiciona o cabeçalho com base na URL e, em seguida, encaminha a solicitação para o contêiner de aplicativo.

Quando uma solicitação HTTP é feita a um serviço de outro serviço dentro do cluster, a solicitação passa primeiro pelo contêiner devspaces-proxy do serviço de chamada. O contêiner devspaces-proxy analisa a solicitação `azds-route-as` HTTP e verifica o cabeçalho. Com base no cabeçalho, o contêiner devspaces-proxy procurará o endereço IP do serviço associado ao valor do cabeçalho. Se um endereço IP for encontrado, o contêiner devspaces-proxy redireciona a solicitação para esse endereço IP. Se um endereço IP não for encontrado, o contêiner devspaces-proxy encaminha a solicitação para o contêiner de aplicativo pai.

Por exemplo, o serviço de *aplicativosA* e *serviceB* são implantados em um espaço de desenvolvimento pai chamado *default*. *serviceA* conta com *serviceB* e faz chamadas HTTP para ele. O Azure User cria um espaço de desenvolvimento infantil com base no espaço *padrão* chamado *azureuser*. O Azure User também implanta sua própria versão de *serviçoA* em seu espaço infantil. Quando uma solicitação *http://azureuser.s.default.serviceA.fedcba09...azds.io*é feita para:

![Roteamento de espaços azure dev](media/how-dev-spaces-works/routing.svg)

1. O controlador Ingress procura o IP para o pod associado à URL, que é *serviceA.azureuser*.
1. O controlador Ingress encontra o IP para o pod no espaço de dev do Usuário Do zure e encaminha a solicitação para o pod *serviceA.azureuser.*
1. O contêiner devspaces-proxy no *pod serviceA.azureuser* `azds-route-as: azureuser` recebe a solicitação e adiciona como um cabeçalho HTTP.
1. O contêiner devspaces-proxy no *pod serviceA.azureuser* encaminha a solicitação para o contêiner de aplicativo *serviceA* no pod *serviceA.azureuser.*
1. O aplicativo *serviceA* no *pod serviceA.azureuser* faz uma chamada para *serviceB*. O aplicativo *serviceA* também contém código `azds-route-as` para preservar o `azds-route-as: azureuser`cabeçalho existente, que neste caso é .
1. O contêiner devspaces-proxy no *pod serviceA.azureuser* recebe a solicitação e procura o `azds-route-as` IP do *serviceB* com base no valor do cabeçalho.
1. O contêiner devspaces-proxy no *pod serviceA.azureuser* não encontra um IP para *serviceB.azureuser*.
1. O contêiner proxy devspaces no *pod serviceA.azureuser* procura o IP para *serviceB* no espaço pai, que é *serviceB.default*.
1. O contêiner proxy devspaces no *pod serviceA.azureuser* encontra o IP para *serviceB.default* e encaminha a solicitação para o pod *serviceB.default.*
1. O contêiner proxy devspaces no pod *serviceB.default* recebe a solicitação e encaminha a solicitação para o contêiner de aplicativo *serviceB* no pod *serviceB.default.*
1. O aplicativo *serviceB* no *pod serviceB.default* retorna uma resposta ao *pod serviceA.azureuser.*
1. O contêiner devspaces-proxy no *pod serviceA.azureuser* recebe a resposta e encaminha a resposta ao contêiner de aplicativo *serviceA* no pod *serviceA.azureuser.*
1. O aplicativo *serviceA* recebe a resposta e, em seguida, retorna sua própria resposta.
1. O contêiner devspaces-proxy no *pod serviceA.azureuser* recebe a resposta do contêiner de aplicativo *serviceA* e encaminha a resposta ao chamador original fora do cluster.

Todos os outros tráfegos TCP que não são HTTP passam pelo controlador Ingress e contêineres proxy devspaces não modificados.

## <a name="sharing-a-dev-space"></a>Compartilhando um espaço de vév

Ao trabalhar com uma equipe, você pode [compartilhar um espaço de v em toda uma equipe](how-to/share-dev-spaces.md) e criar espaços derivados de v. Um espaço de desenvolvimento pode ser usado por qualquer pessoa com acesso contribuinte ao grupo de recursos do espaço de desenvolvimento.

Você também pode criar um novo espaço de vév que é derivado de outro espaço de v. Quando você cria um espaço de v derivado, o *rótulo azds.io/parent-space=PARENT-SPACE-NAME* é adicionado ao espaço de nome do espaço derivado. Além disso, todas as aplicações do espaço de desenvolvimento pai são compartilhadas com o espaço derivado de v. Se você implantar uma versão atualizada de um aplicativo para o espaço de v derivado, ele só existirá no espaço de desenvolvimento derivado e o espaço de desenvolvimento pai permanecerá inalterado. Você pode ter um máximo de três níveis de espaços de v derivados ou espaços *de avós.*

O espaço de v v derivado também direcionará de forma inteligente as solicitações entre seus próprios aplicativos e os aplicativos compartilhados de seu pai. O roteamento funciona tentando encaminhar a solicitação para um aplicativo no espaço de desenvolvimento derivado e voltando para o aplicativo compartilhado do espaço de desenvolvimento pai. O roteamento retornará ao aplicativo compartilhado no espaço dos avós se o aplicativo não estiver no espaço dos pais.

Por exemplo: 
* O *padrão* de espaço de desenvolvimento tem *aplicativos serviceA* e *serviceB*.
* O espaço dev *azureuser* é derivado do *padrão*.
* Uma versão atualizada do *serviçoA* é implantada *no azureuser*.

Ao usar *o azureuser*, todas as solicitações para *serviceA* serão encaminhadas para a versão atualizada no *azureuser*. Uma solicitação de *serviceB* tentará primeiro ser encaminhada para a versão *azureuser* do *serviceB*. Como ele não existe, ele será encaminhado para a versão *padrão* do *serviceB*. Se a versão *azureuser* do *serviçoA* for removida, todas as solicitações ao *serviceA* voltarão a usar a versão *padrão* do *serviçoA*.

## <a name="next-steps"></a>Próximas etapas

Para ver alguns exemplos de como o Azure Dev Spaces usa roteamento para fornecer iteração e desenvolvimento rápidos, consulte [Como conectar sua máquina de desenvolvimento ao seu espaço de desenvolvimento funciona,][how-it-works-connect]como funciona a [depuração remota do seu código com o Azure Dev Spaces][how-it-works-remote-debugging]e o [GitHub Actions & O Azure Kubernetes Service][pr-flow].

Para começar a usar o roteamento com o Azure Dev Spaces para o desenvolvimento da equipe, consulte o desenvolvimento da [equipe no Azure Dev Spaces][quickstart-team] quickstart.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md