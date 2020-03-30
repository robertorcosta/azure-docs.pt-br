---
title: Como funciona a criação de um cluster para espaços de dev do Azure
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como funciona a criação de um cluster azure Kubernetes Service para espaços azure dev
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241719"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Como funciona a criação de um cluster para espaços de dev do Azure

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente os aplicativos kubernetes e colaborar com sua equipe em um cluster Azure Kubernetes Service (AKS). Uma maneira é habilitar espaços de desenvolvimento do Azure no cluster AKS para que você possa [executar serviços diretamente em seu cluster][how-it-works-up] e usar recursos [adicionais de rede e roteamento][how-it-works-routing]. Este artigo descreve o que acontece quando você prepara seu cluster e habilita o Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Prepare seu cluster AKS

Para preparar seu cluster AKS para Espaços de Dev, verifique se o cluster AKS está em uma região [suportada pelo Azure Dev Spaces][supported-regions] e você está executando o Kubernetes 1.10.3 ou posterior. Para ativar o Azure Dev Spaces no seu cluster a partir do portal Azure, navegue até o seu cluster, clique em *Espaços de dev,* altere *Os espaços de uso de dev* para *Sim*e clique em *Salvar*. Você também pode habilitar o Azure Dev `az aks use-dev-spaces`Spaces a partir da CLI do Azure executando .

Para um exemplo de criação de um cluster AKS para Espaços de Desenvolvimento, consulte o desenvolvimento da [equipe quickstart][quickstart-team].

Quando o Azure Dev Spaces está ativado no cluster AKS, ele instala o controlador para o cluster. O controlador reside fora do seu cluster AKS. Ele impulsiona o comportamento e a comunicação entre o lado do cliente e o cluster AKS. Uma vez ativado, você pode interagir com o controlador usando a ferramenta do lado do cliente.

O controlador executa as seguintes ações:

* Gerencia a criação e a seleção de espaço de v.
* Instala o gráfico Helm do seu aplicativo e cria objetos Kubernetes.
* Constrói a imagem do contêiner do aplicativo.
* Implanta seu aplicativo no AKS.
* Faz compilações e reinicializações incrementais quando seu código fonte muda.
* Gerencia logs e traços HTTP.
* Encaminha stdout e stderr para a ferramenta do lado do cliente.
* Configura o roteamento para aplicativos dentro de um espaço, bem como em espaços de pais e filhos.

O controlador é um recurso Azure separado fora do seu cluster e faz o seguinte aos recursos em seu cluster:

* Cria ou designa um namespace kubernetes para usar como espaço de v.
* Remove qualquer namespace kubernetes chamado *azds*, se ele existe, e cria um novo.
* Implanta uma configuração de webhook do Kubernetes.
* Implanta um servidor de admissão de webhook.

Ele usa o mesmo princípio de serviço que o cluster AKS usa para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Azure Dev Spaces preparam cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Para usar o Azure Dev Spaces, deve haver pelo menos um espaço de dev. O Azure Dev Spaces usa espaços de nomes Kubernetes dentro do seu cluster AKS para espaços de dev. Quando um controlador está sendo instalado, ele solicita que você crie um novo espaço de nome Kubernetes ou escolha um namespace existente para usar como seu primeiro espaço de dev. Por padrão, o controlador oferece para atualizar o espaço de nome kubernetes *padrão* existente para o seu primeiro espaço de desenvolvimento.

Quando um namespace é designado como um espaço de v, o controlador adiciona o *rótulo azds.io/space=true* a esse namespace para identificá-lo como um espaço de v. O espaço inicial de dev que você cria ou designa é selecionado por padrão depois de preparar seu cluster. Quando um espaço é selecionado, ele é usado pelo Azure Dev Spaces para criar novas cargas de trabalho.

Você pode usar as ferramentas do lado do cliente para criar novos espaços de dev e remover espaços de dev existentes. Devido a uma limitação no Kubernetes, o espaço de dev *padrão* não pode ser removido. O controlador também remove quaisquer namespaces kubernetes existentes chamados *azds* para evitar conflitos com o `azds` comando usado pela ferramenta do lado do cliente.

O servidor de admissão do webhook Kubernetes é usado para injetar pods com três contêineres durante a implantação para instrumentação: um contêiner proxy devspaces, um contêiner devspaces-proxy-init e um contêiner de construção de devspaces. **Todos os três contêineres são executados com acesso raiz no cluster AKS.** Eles também usam o mesmo princípio de serviço que o cluster AKS usa para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Azure Dev Spaces Kubernetes webhook servidor de admissão](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

O contêiner devspaces-proxy é um contêiner sidecar que lida com todo o tráfego TCP dentro e fora do contêiner de aplicativo e ajuda a executar o roteamento. O contêiner devspaces-proxy redireciona mensagens HTTP se determinados espaços estiverem sendo usados. Por exemplo, ele pode ajudar a encaminhar mensagens HTTP entre aplicativos em espaços de pais e filhos. Todo tráfego não-HTTP passa por devspaces-proxy não modificado. O contêiner devspaces-proxy também registra todas as mensagens HTTP de entrada e saída e as envia para as ferramentas do lado do cliente como rastreamentos. Esses traços podem então ser visualizados pelo desenvolvedor para inspecionar o comportamento do aplicativo.

O contêiner devspaces-proxy-init é um [contêiner init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) que adiciona regras adicionais de roteamento com base na hierarquia de espaço ao contêiner do seu aplicativo. Ele adiciona regras de roteamento atualizando a configuração do arquivo */etc/resolv.conf* do contêiner do aplicativo antes de começar. As atualizações para */etc/resolv.conf* permitem a resolução de DNS de serviços em espaços parentais. As atualizações de configuração iptables garantem que todo o tráfego TCP dentro e fora do contêiner do aplicativo seja roteado através de devspaces-proxy. Todas as atualizações de devspaces-proxy-init acontecem além das regras que o Kubernetes adiciona.

O contêiner de construção de devspaces é um contêiner init e tem o código fonte do projeto e o soquete Docker montado. O código-fonte do projeto e o acesso ao Docker permitem que o contêiner do aplicativo seja construído diretamente pelo pod.

> [!NOTE]
> O Azure Dev Spaces usa o mesmo nó para construir o contêiner do seu aplicativo e executá-lo. Como resultado, o Azure Dev Spaces não precisa de um registro externo de contêiner para construir e executar sua aplicação.

O servidor de admissão do Webhook Kubernetes ouve qualquer novo pod criado no cluster AKS. Se essa cápsula for implantada em qualquer namespace com a etiqueta *azds.io/space=true,* ela injeta essa cápsula com os recipientes adicionais. O contêiner de construção de devspaces só é injetado se o contêiner do aplicativo for executado usando a ferramenta do lado do cliente.

Depois de preparar seu cluster AKS, você pode usar as ferramentas do lado do cliente para preparar e executar seu código em seu espaço de dev.

## <a name="client-side-tooling"></a>Ferramentas laterais do cliente

A ferramenta do lado do cliente permite ao usuário:
* Gerar um arquivo Docker, gráfico Helm e um arquivo de configuração do Azure Dev Spaces para o aplicativo.
* Crie espaços de desenvolvimento de pais e filhos.
* Diga ao controlador para construir e iniciar sua aplicação.

Enquanto seu aplicativo estiver em execução, a ferramenta do lado do cliente também:
* Recebe e exibe stdout e stderr de seu aplicativo em execução em AKS.
* Usa [o port forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) para permitir o\/acesso da Web ao seu aplicativo usando http: /localhost.
* Anexa um depurador ao seu aplicativo em execução no AKS.
* Sincroniza o código-fonte no espaço do desenvolvimento quando uma alteração é detectada para compilações incrementais, permitindo uma iteração rápida.
* Permite conectar sua máquina de desenvolvedor diretamente ao seu cluster AKS.

Você pode usar as ferramentas do lado do cliente `azds` da linha de comando como parte do comando. Você também pode usar as ferramentas do lado do cliente com:

* Visual Studio Code usando a [extensão Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio com [Ferramentas visuais studio para Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar as ferramentas do lado do cliente para preparar e executar seu código em seu espaço de dev, consulte [Como funciona a preparação de um projeto para o Azure Dev Spaces][how-it-works-prep].

Para começar a usar o Azure Dev Spaces para o desenvolvimento da equipe, consulte o desenvolvimento da [equipe no Azure Dev Spaces][quickstart-team] quickstart.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md