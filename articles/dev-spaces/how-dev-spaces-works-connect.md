---
title: Como funciona conectar seu computador de desenvolvimento ao seu cluster AKS
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos fo usando o Azure Dev Spaces para conectar seu computador de desenvolvimento ao seu cluster azure Kubernetes Service
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241706"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Como funciona conectar seu computador de desenvolvimento ao seu cluster AKS

Com o Azure Dev Spaces, você pode conectar seu computador de desenvolvimento ao seu cluster AKS, permitindo que você execute e depura código no seu computador de desenvolvimento como se estivesse sendo executado no cluster. O Azure Dev Spaces redireciona o tráfego entre o cluster AKS conectado executando um pod em seu cluster que atua como um agente remoto para redirecionar o tráfego entre sua máquina de desenvolvimento e o cluster. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução em seu cluster AKS se comuniquecomo se estivessem no mesmo cluster AKS. Esta conexão também permite que você execute e depura código com ou sem um contêiner em seu computador de desenvolvimento. Conectar seu computador de desenvolvimento ao cluster ajuda você a desenvolver rapidamente seu aplicativo e realizar testes de ponta a ponta.

## <a name="connecting-to-your-cluster"></a>Conectando-se ao seu cluster

Você se conecta ao cluster AKS existente usando [o Visual Studio Code][vs-code] com a extensão [Azure Dev Spaces][azds-vs-code] instalada em execução no MacOS ou Windows 10. Quando você estabelece uma conexão, você tem a opção de redirecionar todo o tráfego de e para um pod novo ou existente no cluster para o seu computador de desenvolvimento.

> [!NOTE]
> Ao usar o Visual Studio Code para se conectar ao seu cluster, a extensão Azure Dev Spaces oferece a opção de redirecionar um serviço para o seu computador de desenvolvimento. Esta opção é uma maneira conveniente de identificar uma cápsula para redirecionamento. Todo o redirecionamento entre o cluster AKS e o computador de desenvolvimento é para um pod.

Conectar-se ao cluster não requer que você tenha espaços de dev do Azure ativados em seu cluster. Em vez disso, quando a extensão Azure Dev Spaces estabelece uma conexão com o seu cluster, ela:

* Substitui o contêiner no pod no cluster AKS por um contêiner de agente remoto que redireciona o tráfego para o seu computador de desenvolvimento. Ao redirecionar um novo pod, o Azure Dev Spaces cria um novo pod em seu cluster AKS com o agente remoto.
* Executa [o kubectl port-forward][kubectl-port-forward] em seu computador de desenvolvimento para encaminhar o tráfego do seu computador de desenvolvimento para o agente remoto em execução em seu cluster.
* Coleta informações do ambiente do seu cluster usando o agente remoto. Essas informações do ambiente incluem variáveis de ambiente, serviços visíveis, montagens de volume e montagens secretas.
* Configura o ambiente no terminal Visual Studio Code para que o serviço no seu computador de desenvolvimento possa acessar as mesmas variáveis como se estivesse sendo executado no cluster.  
* Atualiza seu arquivo hosts para mapear serviços em seu cluster AKS para endereços IP locais em seu computador de desenvolvimento. Essas entradas de arquivo hosts permitem que o código seja executado em seu computador de desenvolvimento para fazer solicitações a outros serviços em execução em seu cluster. Para atualizar seu arquivo hosts, o Azure Dev Spaces solicitará acesso do administrador em seu computador de desenvolvimento ao se conectar ao seu cluster.

Se você tiver o Azure Dev Spaces ativado em seu cluster, você também tem a opção de usar o [redirecionamento de tráfego oferecido pelo Azure Dev Spaces][how-it-works-routing]. O redirecionamento de tráfego oferecido pelo Azure Dev Spaces permite que você se conecte a uma cópia do seu serviço em execução em um espaço de desenvolvimento infantil. O uso de um espaço de desenvolvimento infantil ajuda você a evitar interromper outros que trabalham no espaço de desenvolvimento dos pais, uma vez que você está apenas redirecionando o tráfego direcionado à instância do espaço infantil do seu serviço, deixando a instância de espaço dos pais do serviço sem modificações.

Uma vez que você se conecta ao seu cluster, o tráfego é roteado para o seu computador de desenvolvimento, independentemente de você ter seu serviço em execução em seu computador de desenvolvimento.

## <a name="running-code-on-your-development-computer"></a>Executando código em seu computador de desenvolvimento

Depois de estabelecer uma conexão com o cluster AKS, você pode executar qualquer código nativamente em seu computador, sem contêiner. Qualquer tráfego de rede que o agente remoto recebe é redirecionado para a porta local especificada durante a conexão para que seu código em execução nativa possa aceitar e processar esse tráfego. As variáveis de ambiente, volumes e segredos do seu cluster são disponibilizados para código em execução no seu computador de desenvolvimento. Além disso, devido às entradas de arquivos hosts e ao encaminhamento da porta adicionados ao seu computador de desenvolvedor pelo Azure Dev Spaces, seu código pode enviar tráfego de rede para serviços em execução em seu cluster usando os nomes de serviço do seu cluster, e que o tráfego é encaminhado para o serviços que estão sendo executados em seu cluster.

Uma vez que seu código está sendo executado em seu computador de desenvolvimento, você tem a flexibilidade de usar qualquer ferramenta que você normalmente usa para o desenvolvimento para executar o seu código e depura-lo. O tráfego é roteado entre seu computador de desenvolvimento e seu cluster durante todo o tempo que você está conectado. Essa conexão persistente permite que você inicie, pare e reinicie seu código tanto quanto você precisa sem ter que restabelecer uma conexão.

Além disso, o Azure Dev Spaces fornece uma maneira de replicar variáveis de ambiente e arquivos montados disponíveis para pods em seu cluster AKS em seu computador de desenvolvimento através do arquivo *azds-local.env.* Você também pode usar este arquivo para criar novas variáveis de ambiente e montagens de volume.

## <a name="additional-configuration-with-azds-localenv"></a>Configuração adicional com azds-local.env

O arquivo *azds-local.env* permite replicar variáveis de ambiente e arquivos montados disponíveis para seus pods em seu cluster AKS. Você pode especificar as seguintes ações em um arquivo *azds-local.env:*

* Baixe um volume e defina o caminho para esse volume como uma variável de ambiente.
* Baixe um arquivo individual ou um conjunto de arquivos de um volume e monte-o no seu computador de desenvolvimento.
* Disponibilize um serviço independentemente do cluster ao que você esteja conectado.

Aqui está um exemplo *de arquivo azds-local.env:*

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Um arquivo *azds-local.env* padrão não é criado automaticamente, então você deve criar manualmente o arquivo na raiz do seu projeto.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

Quando conectados ao seu cluster AKS, os registros de diagnóstico do seu cluster são conectados ao [diretório temporário][azds-tmp-dir]do seu computador de desenvolvimento . Usando o Visual Studio Code, você também pode usar o comando *Mostrar informações de diagnóstico* para imprimir as variáveis de ambiente atuais e as entradas de DNS do seu cluster AKS.

## <a name="next-steps"></a>Próximas etapas

Para começar a conectar seu computador de desenvolvimento local ao seu cluster AKS, consulte [Conecte seu computador de desenvolvimento a um cluster AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
