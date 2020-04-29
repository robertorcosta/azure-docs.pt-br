---
title: Como conectar seu computador de desenvolvimento ao cluster AKS funciona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos que usam Azure Dev Spaces para conectar seu computador de desenvolvimento ao cluster do serviço kubernetes do Azure
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241706"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Como conectar seu computador de desenvolvimento ao cluster AKS funciona

Com o Azure Dev Spaces, você pode conectar seu computador de desenvolvimento ao cluster AKS, permitindo que você execute e depure o código no seu computador de desenvolvimento como se ele estivesse em execução no cluster. Azure Dev Spaces redireciona o tráfego entre o cluster AKS conectado executando um pod no cluster que atua como um agente remoto para redirecionar o tráfego entre o computador de desenvolvimento e o cluster. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução no cluster AKS se comuniquem como se estivessem no mesmo cluster AKS. Essa conexão também permite que você execute e depure código com ou sem um contêiner em seu computador de desenvolvimento. Conectar seu computador de desenvolvimento ao seu cluster ajuda a desenvolver rapidamente seu aplicativo e realizar testes de ponta a ponta.

## <a name="connecting-to-your-cluster"></a>Conectando ao cluster

Você se conecta ao cluster AKS existente usando [Visual Studio Code][vs-code] com a extensão de [Azure dev Spaces][azds-vs-code] instalada em execução no MacOS ou no Windows 10. Ao estabelecer uma conexão, você tem a opção de redirecionar todo o tráfego para e de um pod novo ou existente no cluster para seu computador de desenvolvimento.

> [!NOTE]
> Ao usar Visual Studio Code para se conectar ao cluster, a extensão de Azure Dev Spaces oferece a opção de redirecionar um serviço para seu computador de desenvolvimento. Essa opção é uma maneira conveniente de identificar um pod para redirecionamento. Todo o redirecionamento entre o cluster AKS e o computador de desenvolvimento é para um pod.

Conectar-se ao cluster não exige que você tenha Azure Dev Spaces habilitados no cluster. Em vez disso, quando a extensão de Azure Dev Spaces estabelece uma conexão com o cluster, ela:

* Substitui o contêiner no pod no cluster AKS por um contêiner de agente remoto que redireciona o tráfego para o computador de desenvolvimento. Ao redirecionar um novo pod, Azure Dev Spaces cria um novo pod em seu cluster AKS com o agente remoto.
* Executa o [kubectl Port-Forward][kubectl-port-forward] no computador de desenvolvimento para encaminhar o tráfego do seu computador de desenvolvimento para o agente remoto em execução no cluster.
* Coleta informações de ambiente do seu cluster usando o agente remoto. Essas informações de ambiente incluem variáveis de ambiente, serviços visíveis, montagens de volume e montagens secretas.
* Configura o ambiente no Visual Studio Code terminal para que o serviço em seu computador de desenvolvimento possa acessar as mesmas variáveis como se estivesse em execução no cluster.  
* Atualiza o arquivo de hosts para mapear serviços no cluster AKS para endereços IP locais em seu computador de desenvolvimento. Essas entradas de arquivo de hosts permitem que o código em execução no computador de desenvolvimento faça solicitações a outros serviços em execução no cluster. Para atualizar o arquivo de hosts, Azure Dev Spaces solicitará acesso de administrador em seu computador de desenvolvimento ao se conectar ao cluster.

Se você tiver Azure Dev Spaces habilitado no cluster, também terá a opção de usar o [redirecionamento de tráfego oferecido pelo Azure dev Spaces][how-it-works-routing]. O redirecionamento de tráfego oferecido pelo Azure Dev Spaces permite que você se conecte a uma cópia do seu serviço em execução em um espaço de desenvolvimento filho. Usar um espaço de desenvolvimento filho ajuda a evitar a interrupção de outras pessoas que trabalham no espaço de desenvolvimento pai, já que você está apenas redirecionando o tráfego direcionado à instância do espaço filho do seu serviço, deixando a instância de espaço pai do serviço sem modificações.

Quando você se conectar ao cluster, o tráfego será roteado para o computador de desenvolvimento, independentemente de você ter seu serviço em execução no computador de desenvolvimento.

## <a name="running-code-on-your-development-computer"></a>Executando código em seu computador de desenvolvimento

Depois de estabelecer uma conexão com o cluster do AKS, você pode executar qualquer código nativamente em seu computador, sem a Containerização. Qualquer tráfego de rede que o agente remoto recebe é redirecionado para a porta local especificada durante a conexão para que seu código em execução nativamente possa aceitar e processar esse tráfego. As variáveis de ambiente, os volumes e os segredos do seu cluster são disponibilizados para o código em execução no seu computador de desenvolvimento. Além disso, devido às entradas de arquivo de hosts e encaminhamento de porta adicionados ao seu computador desenvolvedor por Azure Dev Spaces, seu código pode enviar tráfego de rede para serviços em execução no seu cluster usando os nomes de serviço do cluster e esse tráfego é encaminhado para os serviços em execução no cluster.

Como seu código está em execução no seu computador de desenvolvimento, você tem a flexibilidade de usar qualquer ferramenta que normalmente use para desenvolvimento para executar seu código e depurá-lo. O tráfego é roteado entre o computador de desenvolvimento e o cluster todo o tempo que você está conectado. Essa conexão persistente permite que você inicie, pare e reinicie seu código o quanto necessário sem precisar restabelecer uma conexão.

Além disso, Azure Dev Spaces fornece uma maneira de replicar variáveis de ambiente e arquivos montados disponíveis para pods em seu cluster AKS em seu computador de desenvolvimento por meio do arquivo *azds-local. env* . Você também pode usar esse arquivo para criar novas variáveis de ambiente e montagens de volume.

## <a name="additional-configuration-with-azds-localenv"></a>Configuração adicional com azds-local. env

O arquivo *azds-local. env* permite replicar variáveis de ambiente e arquivos montados disponíveis para o pods em seu cluster AKs. Você pode especificar as seguintes ações em um arquivo *azds-local. env* :

* Baixe um volume e defina o caminho para esse volume como uma variável de ambiente.
* Baixe um arquivo individual ou conjunto de arquivos de um volume e monte-o em seu computador de desenvolvimento.
* Disponibilizar um serviço independentemente do cluster ao qual você está conectado.

Aqui está um exemplo de arquivo *azds-local. env* :

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

Um arquivo *azds-local. env* padrão não é criado automaticamente, portanto, você deve criar manualmente o arquivo na raiz do seu projeto.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

Quando conectado ao cluster do AKS, os logs de diagnóstico do cluster são registrados no [diretório temporário][azds-tmp-dir]do computador de desenvolvimento. Usando Visual Studio Code, você também pode usar o comando *Mostrar informações de diagnóstico* para imprimir as variáveis de ambiente e entradas DNS atuais do cluster AKs.

## <a name="next-steps"></a>Próximas etapas

Para começar a conectar seu computador de desenvolvimento local ao cluster AKS, consulte [conectar seu computador de desenvolvimento a um cluster AKs][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
