---
title: Migrando para o Bridge para o kubernetes
services: azure-dev-spaces
ms.date: 09/21/2020
ms.topic: conceptual
description: Descreve os processos que o Power Azure Dev Spaces
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, ponte para kubernetes
ms.openlocfilehash: b585ee20efb7b377a041152996ef41d8c59c539e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994076"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrando para o Bridge para o kubernetes

A ponte para o kubernetes fornece uma alternativa de peso mais leve a muitos dos cenários de desenvolvimento que funcionam com Azure Dev Spaces. A ponte para o kubernetes é uma experiência somente no lado do cliente usando extensões no [Visual Studio][vs]   e [Visual Studio Code][vsc].  

O Bridge para kubernetes ajuda sua experiência de desenvolvimento, permitindo que um aplicativo kubernetes estabelecido inclua um serviço em execução na estação de trabalho de desenvolvimento local. Ao contrário dos espaços de desenvolvimento, o Bridge to kubernetes reduz as complexidades de loop interno por meio da necessidade de criar configurações de Docker e kubernetes, permitindo que os desenvolvedores se concentrem puramente na lógica de negócios de seu código de microatendimento.

O Bridge para kubernetes ajuda você a iterar no código em execução no seu computador de desenvolvimento enquanto consome dependências e configuração existente do seu ambiente kubernetes. Por outro lado, Azure Dev Spaces implanta seu microserviço no ambiente kubernetes antes de você poder depurar remotamente seu serviço e iterar em seu código.

Este artigo fornece uma comparação entre Azure Dev Spaces e a ponte para kubernetes, bem como instruções para migrar do Azure Dev Spaces para o Bridge para o kubernetes.

## <a name="development-approaches"></a>Abordagens de desenvolvimento

![Abordagens de desenvolvimento](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces ajudou os desenvolvedores kubernetes a trabalhar com código em execução diretamente em seu cluster AKS, evitando a necessidade de um ambiente local que não se assemelhasse ao ambiente implantado. Essa abordagem melhorou determinados aspectos do desenvolvimento, mas também introduziu um pré-requisito de aprendizado e manutenção de conceitos adicionais, como Docker, kubernetes e Helm, antes de começar a usar Azure Dev Spaces.

A ponte para o kubernetes permite que os desenvolvedores trabalhem diretamente em seus computadores de desenvolvimento ao mesmo tempo em que interagem com o restante do cluster. Essa abordagem aproveita a familiaridade e a velocidade de execução de código diretamente em seus computadores de desenvolvimento enquanto compartilha as dependências e o ambiente fornecido pelo cluster. Essa abordagem também aproveita a fidelidade e o dimensionamento que vem da execução no kubernetes.

## <a name="feature-comparison"></a>Comparação de recursos

Azure Dev Spaces e Bridge para kubernetes têm recursos semelhantes, eles também são diferentes em várias áreas:

| Requisito  | Azure Dev Spaces  | Ponte para kubernetes  |
|---------------|-------------------|--------------------------------|
| Serviço de Kubernetes do Azure | Em 15 regiões do Azure | Qualquer região de serviço do AKS    |
| **Segurança** |
| Acesso de segurança necessário no cluster  | Colaborador de cluster AKS  | Kubernetes RBAC – atualização de implantação   |
| Acesso de segurança necessário no seu computador de desenvolvimento  | N/D  | Administrador local/sudo   |
| **Usabilidade** |
| Independente dos artefatos do kubernetes e do Docker  | Não  | Sim   |
| Reversão automática de alterações, pós-depuração  | Não  | Sim   |
| **Ambientes** |
| Funciona com o Visual Studio 2019  | Sim  | Sim   |
| Funciona com Visual Studio Code  | Sim  | Sim   |
| Funciona com a CLI  | Sim  | Não   |
| **Compatibilidade do sistema operacional** |
| Funciona no Windows 10  | Sim  | Sim  |
| Funciona no Linux  | Sim  | Sim  |
| Funciona no macOS  | Sim  | Sim  |
| **Funcionalidades** |
| Isolamento de desenvolvedor ou desenvolvimento de equipe  | Sim  | Sim  |
| Substituir seletivamente variáveis de ambiente  | Não  | Sim  |
| Criação de gráfico Dockerfile e Helm  | Sim  | Não  |
| Implantação persistente de código para kubernetes  | Sim  | Não  |
| Depuração remota em um pod kubernetes  | Sim  | Não  |
| Depuração local, conectada ao kubernetes  | Não  | Sim  |
| Depurando vários serviços ao mesmo tempo, na mesma estação de trabalho  | Sim  | Sim  |

## <a name="kubernetes-inner-loop-development"></a>Desenvolvimento de loop interno do kubernetes

A maior diferença entre Azure Dev Spaces e ponte para kubernetes é onde seu código é executado. Azure Dev Spaces ajuda a desenvolver e depurar seu código de microserviço, mas requer que você execute esse código em seu cluster. O Bridge to kubernetes permite que você desenvolva e depure seu código de microserviço diretamente no seu computador de desenvolvimento enquanto ainda estiver no contexto do aplicativo maior em execução no kubernetes. A ponte para kubernetes estende o perímetro do cluster kubernetes e permite que os processos locais herdem a configuração do kubernetes.

![Desenvolvimento de loop interno](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Ao usar a ponte para kubernetes, é estabelecida uma conexão de rede entre o computador de desenvolvimento e o cluster.Durante o tempo de vida dessa conexão, um proxy é adicionado ao cluster no lugar da implantação do kubernetes que redireciona as solicitações para o serviço para o seu computador de desenvolvimento. Quando você se desconecta, a implantação do aplicativo será revertida para usar a versão original da implantação em execução no cluster. Essa abordagem é diferente de como Azure Dev Spaces funciona no qual o código é sincronizado com o cluster, criado e executado. Azure Dev Spaces também não reverte seu código.

A ponte para o kubernetes tem a flexibilidade de trabalhar com aplicativos em execução no kubernetes, independentemente de seu método de implantação. Se você usar CI/CD para compilar e executar seu aplicativo, independentemente de você usar ferramentas estabelecidas ou scripts personalizados, você ainda poderá usar o Bridge para kubernetes para desenvolver e depurar seu código.

> [!TIP]
> A [extensão Microsoft kubernetes][kubernetes-extension] permite que você desenvolva rapidamente manifestos do kubernetes com o IntelliSense e ajuda os gráficos do Scaffold Helm.  

### <a name="use-visual-studio-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Use o Visual Studio para fazer a transição para a ponte para o kubernetes da Azure Dev Spaces

1. Atualize o IDE do Visual Studio para a versão 16,7 ou superior e instale a ponte para a extensão kubernetes do [Visual Studio Marketplace][vs-marketplace].
1. Desabilite o controlador de Azure Dev Spaces usando o portal do Azure ou a [CLI do Azure dev Spaces][azds-delete].
1. Remova o `azds.yaml` arquivo do seu projeto.
1. Reimplante o aplicativo.
1. Configure a ponte para o kubernetes em seu aplicativo implantado. Para obter mais informações sobre como usar o Bridge para kubernetes no Visual Studio, consulte [usar o Bridge para kubernetes][use-btk-vs].
1. Inicie a depuração no Visual Studio usando a ponte recém-criada para kubernetes o perfil de depuração.

### <a name="use-visual-studio-code-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Use Visual Studio Code para fazer a transição para a ponte para o kubernetes da Azure Dev Spaces

1. Instale a [ponte para a extensão kubernetes][vsc-marketplace].
1. Desabilite o controlador de Azure Dev Spaces usando o portal do Azure ou a [CLI do Azure dev Spaces][azds-delete].
1. Remova o `azds.yaml` arquivo do seu projeto.
1. Reimplante o aplicativo.
1. Configure a ponte para o kubernetes em seu aplicativo implantado. Para obter mais informações sobre como usar o Bridge para kubernetes no Visual Studio Code, consulte [usar o Bridge para kubernetes][use-btk-vsc].
1. Inicie a depuração no Visual Studio Code usando a ponte recém-criada para kubernetes o perfil de inicialização.

## <a name="team-development-in-a-shared-cluster"></a>Desenvolvimento de equipe em um cluster compartilhado

Você também pode usar o roteamento específico do desenvolvedor com a ponte para kubernetes. O cenário de desenvolvimento de equipe Azure Dev Spaces usa vários namespaces kubernetes para isolar um serviço do restante do aplicativo usando o conceito de namespaces pai e filho. O Bridge to kubernetes oferece o mesmo recurso, mas com características de desempenho aprimoradas e dentro do mesmo namespace de aplicativo.

Ambas as pontes para kubernetes e Azure Dev Spaces exigem que os cabeçalhos HTTP estejam presentes e propagados em todo o aplicativo. Se você já tiver configurado seu aplicativo para manipular a propagação de cabeçalho para Azure Dev Spaces, o cabeçalho precisará ser atualizado. Para fazer a transição para a ponte para o kubernetes da Azure Dev Spaces, atualize o cabeçalho configurado de *azds-Route-as* para *kubernetes-Route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Avaliar ponte para kubernetes

Se você quiser experimentar a ponte para o kubernetes antes de desabilitar Azure Dev Spaces em seu cluster, a maneira mais fácil é usar um novo cluster. Se você tentar usar Azure Dev Spaces e ponte para kubernetes ao mesmo tempo no mesmo cluster, encontrará problemas ao usar os recursos de roteamento em ambos.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Usar o Visual Studio para avaliar a ponte para o kubernetes

1. Atualize o IDE do Visual Studio para a versão 16,7 ou superior e instale a ponte para a extensão kubernetes do [Visual Studio Marketplace][vs-marketplace].
1. Crie um novo cluster AKS e implante seu aplicativo. Você também pode usar um [aplicativo de exemplo][btk-sample-app].
1. Configure a ponte para o kubernetes em seu aplicativo implantado. Para obter mais informações sobre como usar o Bridge para kubernetes no Visual Studio, consulte [usar o Bridge para kubernetes][use-btk-vs].
1. Inicie a depuração no Visual Studio usando a ponte recém-criada para kubernetes o perfil de depuração.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Usar Visual Studio Code para avaliar a ponte para o kubernetes

1. Instale a [ponte para a extensão kubernetes][vsc-marketplace].
1. Crie um novo cluster AKS e implante seu aplicativo. Você também pode usar um [aplicativo de exemplo][btk-sample-app].
1. Configure a ponte para o kubernetes em seu aplicativo implantado. Para obter mais informações sobre como usar o Bridge para kubernetes no Visual Studio Code, consulte [usar o Bridge para kubernetes][use-btk-vsc].
1. Inicie a depuração no Visual Studio usando a ponte recém-criada para o perfil de inicialização do kubernetes.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como a ponte para kubernetes funciona.

> [!div class="nextstepaction"]
> [Como o Bridge to kubernetes funciona][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/