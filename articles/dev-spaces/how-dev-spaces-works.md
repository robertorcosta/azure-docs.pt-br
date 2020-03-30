---
title: Como o Azure Dev Spaces funciona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos que alimentam os Espaços de Dev do Azure
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234983"
---
# <a name="how-azure-dev-spaces-works"></a>Como o Azure Dev Spaces funciona

Desenvolver um aplicativo Kubernetes pode ser um desafio. Você precisa de arquivos de configuração Docker e Kubernetes. Você precisa descobrir como testar seu aplicativo localmente e interagir com outros serviços dependentes. Você pode precisar lidar com o desenvolvimento e teste de vários serviços ao mesmo tempo e com uma equipe de desenvolvedores.

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente os aplicativos kubernetes e colaborar com sua equipe. Este artigo descreve o que o Azure Dev Spaces pode fazer e como ele funciona.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Iterar rapidamente e depurar sua aplicação Kubernetes

O Azure Dev Spaces reduz o esforço para desenvolver, testar e iterar sua aplicação kubernetes no contexto do seu cluster AKS. Essa redução no esforço permite que os desenvolvedores se concentrem na lógica de negócios de seus aplicativos e não configurem seus serviços para executar em Kubernetes.

### <a name="connect-your-development-machine-to-aks"></a>Conecte sua máquina de desenvolvimento à AKS

Com o Azure Dev Spaces, você pode conectar seu computador de desenvolvimento ao seu cluster AKS, permitindo que você execute e depura código no seu computador de desenvolvimento como se estivesse sendo executado no cluster. O Azure Dev Spaces redireciona o tráfego entre o cluster AKS conectado executando um pod em seu cluster que atua como um agente remoto para redirecionar o tráfego entre sua máquina de desenvolvimento e o cluster. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução em seu cluster AKS se comuniquecomo se estivessem no mesmo cluster AKS. Para obter mais informações sobre como conectar sua máquina de desenvolvimento ao AKS, consulte [Como funciona a conexão do seu computador de desenvolvimento ao seu cluster AKS][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>Execute seu código em AKS

Além de redirecionar o tráfego entre seu computador de desenvolvimento e seu cluster AKS, com o Azure Dev Spaces você pode configurar e executar rapidamente seu código diretamente no AKS. Com o Visual Studio Code ou o Azure Dev Spaces CLI, os espaços do Azure Dev carregarão seu código para cluster e, em seguida, o construirão e executarão. Os espaços do Azure Dev também podem sincronizar inteligentemente as alterações de código e reiniciar seu serviço para refletir as alterações conforme necessário. Durante a execução do seu código, os registros de compilação e os rastreamentos HTTP são transmitidos de volta para o seu cliente para que você possa monitorar o progresso e diagnosticar quaisquer problemas. Você também pode usar o Azure Dev Spaces, para anexar o depurador nos serviços Java, Node.js e .NET Core. Para obter mais informações, consulte [Como funciona a preparação de um projeto para o Azure Dev Spaces][how-it-works-prep], como funciona a [execução do seu código com o Azure Dev Spaces][how-it-works-up]e como funciona a [depuração remota do seu código com o Azure Dev Spaces][how-it-works-remote-debugging].

## <a name="team-development"></a>Desenvolvimento da equipe

O Azure Dev Spaces ajuda as equipes a trabalhar produtivamente em sua aplicação no mesmo cluster AKS sem ser disruptiva.

### <a name="intelligent-routing-between-dev-spaces"></a>Roteamento inteligente entre espaços de v

Com o Azure Dev Spaces, uma equipe pode compartilhar um único cluster AKS executando um aplicativo nativo da nuvem e criar espaços de desenvolvimento isolados onde a equipe pode desenvolver, testar e depurar sem interferir nos outros espaços de desenvolvimento. Uma versão de linha de base do aplicativo é executada em um espaço de desenvolvimento raiz. Os membros da equipe criam espaços independentes de desenvolvimento infantil com base no espaço raiz para desenvolvimento, teste e depuração de alterações no aplicativo. Através dos recursos de roteamento em Espaços de Desenvolvimento, os espaços de desenvolvimento infantil podem encaminhar solicitações entre os serviços que são executados no espaço de desenvolvimento infantil e o espaço de desenvolvimento dos pais. Esse roteamento permite que os desenvolvedores executem sua própria versão de um serviço enquanto reutilizam serviços dependentes do espaço pai. Cada espaço infantil tem sua própria URL única, que pode ser compartilhada e acessada por outros para colaboração. Para obter mais informações sobre como funciona o roteamento nos espaços do Azure Dev, consulte [como o roteamento funciona com o Azure Dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Teste ao vivo um pedido de retirada aberta

Você também pode usar o GitHub Actions com o Azure Dev Spaces para testar alterações em seu aplicativo em uma solicitação de tração diretamente em seu cluster antes de se fundir. O Azure Dev Spaces pode implantar automaticamente uma versão de revisão do aplicativo em seu cluster, permitindo que o autor e outros membros da equipe revisem as alterações no contexto de todo o aplicativo. Usando os recursos de roteamento do Azure Dev Spaces, esta versão de revisão do aplicativo também é implantada no seu cluster sem afetar outros espaços de desenvolvimento. Todos esses recursos permitem que você aprove e mescle solicitações de tração com confiança. Para ver um exemplo de GitHub Actions e Azure Dev Spaces, consulte [GitHub Actions & Azure Kubernetes Service][pr-flow].

## <a name="next-steps"></a>Próximas etapas

Para começar a conectar sua máquina de desenvolvimento local ao seu cluster AKS, consulte [Conecte sua máquina de desenvolvimento a um cluster AKS][connect].

Para começar a usar o Azure Dev Spaces para o desenvolvimento da equipe, consulte o desenvolvimento da [equipe no Azure Dev Spaces][quickstart-team] quickstart.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development