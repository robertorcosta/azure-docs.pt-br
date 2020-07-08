---
title: Como o processo local com Kubernetes funciona
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Descreve os processos para usar o processo local com o kubernetes para conectar seu computador de desenvolvimento ao cluster kubernetes
keywords: Processo local com kubernetes, Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: dd126fc55a86b1de115239a31e5adb7b1d264846
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974391"
---
# <a name="how-local-process-with-kubernetes-works"></a>Como o processo local com Kubernetes funciona

O processo local com o kubernetes permite que você execute e depure o código em seu computador de desenvolvimento, enquanto ainda está conectado ao cluster kubernetes com o restante do seu aplicativo ou serviços. Por exemplo, se você tiver uma grande arquitetura de microserviços com muitos bancos de dados e serviços interdependentes, é difícil replicar essas dependências no seu computador de desenvolvimento. Além disso, a criação e a implantação de código em seu cluster kubernetes para cada alteração de código durante o desenvolvimento de loop interno podem ser lentas, demoradas e difíceis de usar com um depurador.

O processo local com kubernetes evita que você precise compilar e implantar seu código no cluster, em vez de criar uma conexão diretamente entre o computador de desenvolvimento e o cluster. Conectar seu computador de desenvolvimento ao cluster durante a depuração permite que você teste e desenvolva rapidamente seu serviço no contexto do aplicativo completo sem criar nenhuma configuração de Docker ou kubernetes.

O processo local com o kubernetes redireciona o tráfego entre o cluster kubernetes conectado e o computador de desenvolvimento. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução no cluster kubernetes se comuniquem como se estivessem no mesmo cluster kubernetes. O processo local com o kubernetes também fornece uma maneira de replicar as variáveis de ambiente e os volumes montados disponíveis para pods no seu cluster kubernetes em seu computador de desenvolvimento. Fornecer acesso a variáveis de ambiente e volumes montados em seu computador de desenvolvimento permite que você trabalhe rapidamente em seu código sem ter que replicar essas dependências manualmente.

## <a name="using-local-process-with-kubernetes"></a>Usando o processo local com kubernetes

Para usar o processo local com o kubernetes, você precisará [Visual Studio Code][vs-code] com as extensões de serviço do [Azure dev Spaces][azds-vs-code] e [do Azure kubernetes][az-aks-vs-code] instaladas e em execução no MacOS ou no Windows 10, bem como na [CLI do Azure dev Spaces instalada][azds-cli]. Você também pode usar o [Visual Studio 2019][visual-studio] em execução no Windows 10 com as cargas de trabalho *ASP.net e desenvolvimento da Web* e de *desenvolvimento do Azure* instaladas e o sinalizador de recurso de visualização *AzureDevSpacesTools. LocalKubernetesDebugging* habilitado, bem como a [CLI do Azure dev Spaces instalada][azds-cli]. Ao usar o processo local com o kubernetes para estabelecer uma conexão com o cluster do kubernetes, você tem a opção de redirecionar todo o tráfego para e de um pod existente no cluster para seu computador de desenvolvimento.

> [!NOTE]
> Ao usar o processo local com kubernetes, será solicitado que você forneça o nome do serviço para redirecionar para o computador de desenvolvimento. Essa opção é uma maneira conveniente de identificar um pod para redirecionamento. Todo o redirecionamento entre o cluster kubernetes e o computador de desenvolvimento é para um pod.

Quando o processo local com kubernetes estabelece uma conexão com o cluster, ele:

* Solicita que você configure o serviço a ser substituído no cluster, a porta no computador de desenvolvimento a ser usada para seu código e a tarefa de inicialização para seu código como uma ação única.
* Substitui o contêiner no pod no cluster por um contêiner de agente remoto que redireciona o tráfego para o computador de desenvolvimento.
* Executa o [kubectl Port-Forward][kubectl-port-forward] no computador de desenvolvimento para encaminhar o tráfego do seu computador de desenvolvimento para o agente remoto em execução no cluster.
* Coleta informações de ambiente do seu cluster usando o agente remoto. Essas informações de ambiente incluem variáveis de ambiente, serviços visíveis, montagens de volume e montagens secretas.
* Configura o ambiente no Visual Studio ou Visual Studio Code para que o serviço em seu computador de desenvolvimento possa acessar as mesmas variáveis como se estivesse em execução no cluster.  
* Atualiza o arquivo de hosts para mapear serviços no cluster para endereços IP locais em seu computador de desenvolvimento. Essas entradas de arquivo de hosts permitem que o código em execução no computador de desenvolvimento faça solicitações a outros serviços em execução no cluster. Para atualizar o arquivo de hosts, o processo local com kubernetes solicitará acesso de administrador em seu computador de desenvolvimento ao se conectar ao cluster.
* Inicia a execução e a depuração do código no computador de desenvolvimento. Se necessário, o processo local com kubernetes liberará as portas necessárias no computador de desenvolvimento, interrompendo os serviços ou processos que estão usando essas portas no momento.

Depois de estabelecer uma conexão com o cluster, você pode executar e depurar o código nativamente em seu computador, sem a Containerização, e o código pode interagir diretamente com o restante do cluster. Qualquer tráfego de rede que o agente remoto recebe é redirecionado para a porta local especificada durante a conexão para que seu código em execução nativamente possa aceitar e processar esse tráfego. As variáveis de ambiente, os volumes e os segredos do seu cluster são disponibilizados para o código em execução no seu computador de desenvolvimento. Além disso, devido às entradas de arquivo de hosts e encaminhamento de porta adicionados ao seu computador de desenvolvedor por processo local com o kubernetes, seu código pode enviar tráfego de rede para serviços em execução no cluster usando os nomes de serviço do cluster e esse tráfego é encaminhado para os serviços em execução no cluster. O tráfego é roteado entre o computador de desenvolvimento e o cluster todo o tempo que você está conectado.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

Ao usar o processo local com o kubernetes para se conectar ao cluster, os logs de diagnóstico do cluster são registrados no [diretório temporário][azds-tmp-dir]do computador de desenvolvimento. Usando Visual Studio Code, você também pode usar o comando *Mostrar informações de diagnóstico* para imprimir as variáveis de ambiente e as entradas DNS atuais do cluster.

## <a name="limitations"></a>Limitações

O processo local com kubernetes tem as seguintes limitações:

* O processo local com o kubernetes redireciona o tráfego de um único serviço para seu computador de desenvolvimento. Você não pode usar o processo local com kubernetes para redirecionar vários serviços ao mesmo tempo.
* Um serviço deve ser apoiado por um único pod para se conectar a esse serviço. Você não pode se conectar a um serviço com vários pods, como um serviço com réplicas.
* Um pod pode ter apenas um único contêiner em execução nesse pod para o processo local com kubernetes para se conectar com êxito. O processo local com kubernetes não pode se conectar a serviços com pods que têm contêineres adicionais, como contêineres sidecar injetados por malhas de serviços.
* O processo local com kubernetes precisa de permissões elevadas para executar em seu computador de desenvolvimento para editar o arquivo de hosts.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o processo local com o kubernetes para se conectar ao seu computador de desenvolvimento local ao cluster, consulte [usar o processo local com o kubernetes com o Visual Studio Code][local-process-kubernetes-vs-code] e [usar o processo local com o kubernetes com o Visual Studio][local-process-kubernetes-vs].

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download