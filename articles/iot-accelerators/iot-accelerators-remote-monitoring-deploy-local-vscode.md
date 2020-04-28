---
title: Implantar a solução de monitoramento remoto localmente-Visual Studio Code-Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar o acelerador de solução de monitoramento remoto no computador local usando o Visual Studio Code para teste e desenvolvimento.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73890952"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Implantar o acelerador de solução de Monitoramento Remoto localmente – Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra como implantar o acelerador de solução de Monitoramento Remoto no computador local para teste e desenvolvimento. Você aprenderá a executar os microsserviços no Visual Studio Code. Uma implantação de microserviços locais usa os seguintes serviços de nuvem: Hub IoT, Cosmos DB, análise de streaming do Azure e Azure Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de Monitoramento Remoto, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração do computador

Para concluir a implantação local, você precisa ter as seguintes ferramentas instaladas no computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensão C# do VS Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) – este software é um pré-requisito para a CLI do PCS que os scripts usam para criar recursos do Azure. Não use o Node.js v10

> [!NOTE]
> O Visual Studio Code está disponível para Windows, Mac e Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Executar os microsserviços

Nesta seção, você executa os microsserviços do Monitoramento Remoto. Execute a interface do usuário da Web nativamente, o serviço de Simulação de Dispositivo no Docker e os microsserviços no Visual Studio Code.

### <a name="build-the-code"></a>Compilar o código

Navegue para azure-iot-pcs-remote-monitoring-dotnet\services no prompt de comando e execute os comandos a seguir para compilar o código.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implantar todos os outros microsserviços na máquina local

As etapas a seguir mostram como executar os microserviços de monitoramento remoto no Visual Studio Code:

1. Inicie o Visual Studio Code.
1. Em VS Code, abra a pasta **Azure-IOT-PCs-Remote-Monitoring-dotnet** .
1. Crie uma nova pasta chamada **. vscode** na pasta **Azure-IOT-PCs-Remote-Monitoring-dotnet** .
1. Copie os arquivos **Launch. JSON** e **Tasks. JSON** de services\scripts\local\launch\idesettings\vscode para a pasta **. vscode** que você acabou de criar.
1. Abra o **painel de depuração** no vs Code e execute a configuração **executar todos os microserviços** . Essa configuração executa o microsserviço de simulação de dispositivo no Docker e executa os outros microsserviços no depurador.

A saída da execução **executar todos os microsserviços** no console de depuração é semelhante ao seguinte:

[![Implantar-locais-microserviços](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Executar a interface do usuário da Web

Nesta etapa, você inicia a interface do usuário da Web. Navegue para a pasta **azure-iot-pcs-remote-monitoring-dotnet\webui** em sua cópia local e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o início for concluído, o navegador exibirá a página **http\/:/localhost: 3000/Dashboard**. São esperados erros nessa página. Para exibir o aplicativo sem erros, conclua a etapa a seguir.

### <a name="configure-and-run-nginx"></a>Configurar e executar o NGINX

Configure um servidor proxy reverso para vincular o aplicativo Web e os microsserviços em execução no computador local:

* Copie o arquivo **nginx.conf** da pasta **webui\scripts\localhost** para o diretório de instalação **nginx\conf**.
* Execute **nginx**.

Para saber mais sobre como executar **nginx**, confira [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Conectar-se ao painel

Para acessar o painel da solução de monitoramento remoto, navegue até\/http:/localhost: 9000 em seu navegador.

## <a name="clean-up"></a>Limpar

Para evitar encargos desnecessários, quando terminar o teste, remova os serviços de nuvem de sua assinatura do Azure. Para remover os serviços, navegue até o [portal do Azure](https://ms.portal.azure.com) e exclua o grupo de recursos que o script **start.cmd** criou.

Você também pode excluir a cópia local do repositório de Monitoramento Remoto criada quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel de solução](quickstart-remote-monitoring-deploy.md).
