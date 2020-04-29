---
title: Como configurar um cluster para Azure Dev Spaces funciona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como configurar um cluster do serviço kubernetes do Azure para Azure Dev Spaces funciona
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241719"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Como configurar um cluster para Azure Dev Spaces funciona

Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente aplicativos kubernetes e colaborar com sua equipe em um cluster do AKS (serviço kubernetes do Azure). Uma maneira é habilitar Azure Dev Spaces em seu cluster AKS para que você possa [executar serviços diretamente no cluster][how-it-works-up] e usar [recursos de rede e roteamento adicionais][how-it-works-routing]. Este artigo descreve o que acontece quando você prepara o cluster e habilita o Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Preparar o cluster AKS

Para preparar o cluster AKS para espaços de desenvolvimento, verifique se o cluster AKS está em uma região [com suporte pelo Azure dev Spaces][supported-regions] e se você está executando o kubernetes 1.10.3 ou posterior. Para habilitar Azure Dev Spaces em seu cluster do portal do Azure, navegue até o cluster, clique em *espaços de desenvolvimento*, altere *usar espaços de desenvolvimento* para *Sim*e clique em *salvar*. Você também pode habilitar Azure Dev Spaces do CLI do Azure executando `az aks use-dev-spaces`.

Para obter um exemplo de como configurar um cluster AKS para espaços de desenvolvimento, consulte o guia de [início rápido de desenvolvimento de equipe][quickstart-team].

Quando Azure Dev Spaces está habilitado no cluster AKS, ele instala o controlador para o cluster. O controlador reside fora do cluster AKS. Ele orienta o comportamento e a comunicação entre as ferramentas do lado do cliente e o cluster AKS. Quando ele estiver habilitado, você poderá interagir com o controlador usando as ferramentas do lado do cliente.

O controlador executa as seguintes ações:

* Gerencia a criação e a seleção de espaço de desenvolvimento.
* Instala o gráfico Helm do seu aplicativo e cria objetos kubernetes.
* Compila a imagem de contêiner do aplicativo.
* Implanta seu aplicativo no AKS.
* O incrementais cria e reinicia quando o código-fonte é alterado.
* Gerencia logs e rastreamentos HTTP.
* Encaminha stdout e stderr para as ferramentas do lado do cliente.
* Configura o roteamento de aplicativos dentro de um espaço, bem como entre os espaços pai e filho.

O controlador é um recurso do Azure separado fora do seu cluster e faz o seguinte para os recursos em seu cluster:

* Cria ou designa um namespace kubernetes para usar como um espaço de desenvolvimento.
* Remove qualquer namespace kubernetes chamado *azds*, se ele existir, e cria um novo.
* Implanta uma configuração de webhook kubernetes.
* Implanta um servidor de admissão de webhook.

Ele usa a mesma entidade de serviço que seu cluster AKS usa para fazer chamadas de serviço para outros componentes de Azure Dev Spaces.

![Azure Dev Spaces preparar o cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Para usar Azure Dev Spaces, deve haver pelo menos um espaço de desenvolvimento. Azure Dev Spaces usa namespaces kubernetes no cluster AKS para espaços de desenvolvimento. Quando um controlador está sendo instalado, ele solicita que você crie um novo namespace kubernetes ou escolha um namespace existente para usar como seu primeiro espaço de desenvolvimento. Por padrão, o controlador oferece para atualizar o namespace kubernetes *padrão* existente para seu primeiro espaço de desenvolvimento.

Quando um namespace é designado como um espaço de desenvolvimento, o controlador adiciona o rótulo *azds.Io/Space=true* a esse namespace para identificá-lo como um espaço de desenvolvimento. O espaço de desenvolvimento inicial que você criar ou designar será selecionado por padrão depois que você preparar o cluster. Quando um espaço é selecionado, ele é usado pelo Azure Dev Spaces para criar novas cargas de trabalho.

Você pode usar as ferramentas do lado do cliente para criar novos espaços de desenvolvimento e remover os espaços de desenvolvimento existentes. Devido a uma limitação no kubernetes, o espaço de desenvolvimento *padrão* não pode ser removido. O controlador também remove os namespaces kubernetes existentes chamados *azds* para evitar conflitos com o `azds` comando usado pelas ferramentas do lado do cliente.

O servidor de admissão do webhook kubernetes é usado para injetar pods com três contêineres durante a implantação para instrumentação: um contêiner devspaces-proxy, um contêiner devspaces-proxy-init e um contêiner devspaces-Build. **Todos esses três contêineres são executados com acesso raiz no cluster AKS.** Eles também usam a mesma entidade de serviço que seu cluster AKS usa para fazer chamadas de serviço para outros componentes de Azure Dev Spaces.

![Azure Dev Spaces servidor de admissão do webhook do kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

O contêiner devspaces-proxy é um contêiner sidecar que manipula todo o tráfego TCP para dentro e fora do contêiner do aplicativo e ajuda a executar o roteamento. O contêiner devspaces-proxy redireciona mensagens HTTP se determinados espaços estiverem sendo usados. Por exemplo, ele pode ajudar a rotear mensagens HTTP entre aplicativos em espaços pai e filho. Todo o tráfego não HTTP passa pelo devspaces-proxy sem modificações. O contêiner devspaces-proxy também registra todas as mensagens HTTP de entrada e saída e as envia para as ferramentas do lado do cliente como rastreamentos. Esses rastreamentos podem ser exibidos pelo desenvolvedor para inspecionar o comportamento do aplicativo.

O contêiner devspaces-proxy-init é um [contêiner de inicialização](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) que adiciona mais regras de roteamento com base na hierarquia de espaço ao contêiner do seu aplicativo. Ele adiciona regras de roteamento atualizando o arquivo */etc/resolv.conf* do contêiner do aplicativo e a configuração do iptables antes de iniciar. As atualizações para */etc/resolv.conf* permitem a resolução de DNS de serviços em espaços pai. As atualizações de configuração do iptables garantem que todo o tráfego TCP dentro e fora do contêiner do aplicativo seja roteado, por meio do devspaces-proxy. Todas as atualizações de devspaces-proxy-init acontecem além das regras que o kubernetes adiciona.

O contêiner devspaces-Build é um contêiner init e tem o código-fonte do projeto e o soquete do Docker montado. O código-fonte do projeto e o acesso ao Docker permitem que o contêiner de aplicativo seja criado diretamente pelo Pod.

> [!NOTE]
> Azure Dev Spaces usa o mesmo nó para compilar o contêiner do aplicativo e executá-lo. Como resultado, Azure Dev Spaces não precisa de um registro de contêiner externo para compilar e executar seu aplicativo.

O servidor de admissão do webhook kubernetes escuta qualquer novo pod criado no cluster AKS. Se esse Pod for implantado em qualquer namespace com o rótulo *azds.Io/Space=true* , ele injetará esse Pod com os contêineres adicionais. O contêiner devspaces-Build será injetado apenas se o contêiner do aplicativo for executado usando as ferramentas do lado do cliente.

Depois de preparar o cluster do AKS, você poderá usar as ferramentas do lado do cliente para preparar e executar seu código no espaço de desenvolvimento.

## <a name="client-side-tooling"></a>Ferramentas do lado do cliente

As ferramentas do lado do cliente permitem que o usuário:
* Gere um Dockerfile, um gráfico do Helm e um arquivo de configuração Azure Dev Spaces para o aplicativo.
* Crie espaços de desenvolvimento pai e filho.
* Peça ao controlador para criar e iniciar seu aplicativo.

Enquanto seu aplicativo está em execução, as ferramentas do lado do cliente também:
* Recebe e exibe stdout e stderr de seu aplicativo em execução no AKS.
* Usa [porta-encaminhar](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) para permitir acesso à Web ao seu aplicativo usando\/http:/localhost.
* Anexa um depurador ao seu aplicativo em execução no AKS.
* Sincroniza o código-fonte para o espaço de desenvolvimento quando uma alteração é detectada para compilações incrementais, permitindo iteração rápida.
* Permite que você conecte seu computador de desenvolvedor diretamente ao cluster AKS.

Você pode usar as ferramentas do lado do cliente na linha de comando como parte do `azds` comando. Você também pode usar as ferramentas do lado do cliente com:

* Visual Studio Code usando a [extensão Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio com [Ferramentas do Visual Studio para kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar as ferramentas do lado do cliente para preparar e executar seu código em seu espaço de desenvolvimento, consulte [como a preparação de um projeto para Azure dev Spaces funciona][how-it-works-prep].

Para começar a usar o Azure Dev Spaces para o desenvolvimento em equipe, consulte o [desenvolvimento de equipe no guia de][quickstart-team] início rápido Azure dev Spaces.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md