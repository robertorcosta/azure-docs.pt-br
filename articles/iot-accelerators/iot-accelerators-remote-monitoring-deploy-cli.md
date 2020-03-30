---
title: Implantar a solução de monitoramento remoto usando o CLI - Azure | Microsoft Docs
description: Este guia prático mostra como provisionar o acelerador de solução de monitoramento remoto usando a CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258287"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Implantar o acelerador de solução de Monitoramento Remoto usando a CLI

Este guia prático mostra como implantar o acelerador de solução de monitoramento remoto. Implante a solução usando a CLI. Você também pode implantar a solução usando a ui baseada na Web em azureiotsolutions.com, para saber sobre essa opção ver o [acelerador de solução De monitoramento remoto](quickstart-remote-monitoring-deploy.md) iniciar rapidamente.

## <a name="prerequisites"></a>Pré-requisitos

Para implantar o acelerador de solução de Monitoramento Remoto, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/).

Para executar a CLI, você precisará do [Node.js](https://nodejs.org/) instalado no seu computador local.

## <a name="install-the-cli"></a>Instalar a CLI

Para instalar a CLI, execute o seguinte comando no seu ambiente de linha de comando:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Entrar na CLI

Antes de implantar o acelerador de solução, você deve entrar em sua assinatura do Azure usando a CLI:

```cmd/sh
pcs login
```

Siga as instruções na tela para concluir o processo de entrada.

## <a name="deployment-options"></a>Opções de implantação

Há várias opções para configurar o processo de implantação ao implantar o acelerador de solução:

| Opção | Valores | Descrição |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Uma implantação _básica_ destina-se a testes e demonstrações, e implanta todos os microsserviços em uma única máquina virtual. Uma implementação _padrão_ é destinada à produção, ela implanta os micros serviços em várias máquinas virtuais. Uma implantação _local_ configura um contêiner Docker para executar os microsserviços na sua máquina local e usa os serviços de nuvem do Azure, como armazenamento e o Cosmos DB. |
| Runtime | `dotnet`, `java` | Seleciona a implementação de linguagem dos microsserviços. |

Para saber como usar a opção de implantação local, consulte [Executando a solução de monitoramento remoto localmente](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Implantações básicas e padrão

Esta seção resume as principais diferenças entre uma implantação básica e padrão.

### <a name="basic"></a>Basic

Você pode fazer uma implantação básica a partir de [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) ou usando o CLI.

Implantação Basic é voltada para a apresentação da solução. Para reduzir custos, todos os micros services são implantados em uma única máquina virtual. Esta implantação não usa uma arquitetura pronta para produção.

Uma implantação básica cria os seguintes serviços em sua assinatura do Azure:

| Contagem | Recurso                       | Type         | Usadas para |
|-------|--------------------------------|--------------|----------|
| 1     | [Máquina Virtual Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hospedar microsserviços |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Camada Padrão | Comunicação e gerenciamento de dispositivo |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Armazenamento de dados de configuração, regras, alertas e outros armazenamentos frios |  
| 1     | [Conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Armazenamento de VM e pontos de verificação de streaming |
| 1     | [Aplicação web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hospedar o aplicativo Web front-end |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gerenciando identidades de usuários e segurança |
| 1     | [Mapas do Azure](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualizando locais de ativos |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Habilitar a análise em tempo real |
| 1     | [Serviço de provisionamento de dispositivo do Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Provisionamento de dispositivos em escala |
| 1     | [Insights da série do tempo do Azure](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento para dados de mensagens e permite análise de telemetria de mergulho profundo |

### <a name="standard"></a>Standard

Você pode fazer uma implantação padrão apenas usando o CLI.

Uma implantação padrão é uma implantação pronta para produção que um desenvolvedor pode personalizar e estender. Use a opção de implantação padrão quando estiver pronto para personalizar uma arquitetura pronta para produção, criada para dimensionamento e extensibilidade. Os micros serviços de aplicativos são criados como contêineres do Docker e implantados usando o Serviço do Azure Kubernetes. O orquestrador do Kubernetes implementa, dimensiona e gerencia os micros serviços.

Uma implantação padrão cria os seguintes serviços em sua assinatura do Azure:

| Contagem | Recurso                                     | SKU / Tamanho      | Usadas para |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Serviço Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service)| Use um serviço de orquestração de contêineres do Kubernetes totalmente gerenciado, o padrão é de três agentes|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Camada Padrão | Controle, comando e gerenciamento de dispositivos |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Armazenar dados de configuração e telemetria do dispositivo como regras, alertas e mensagens |
| 5     | [Contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 para armazenamento de máquina virtual e 1 para os pontos de verificação de streaming |
| 1     | [Serviço de Aplicativo](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Gateway de aplicativo sobre TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gerenciando identidades de usuários e segurança |
| 1     | [Mapas do Azure](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualizando locais de ativos |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unidades              | Habilitar a análise em tempo real |
| 1     | [Serviço de provisionamento de dispositivo do Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Provisionamento de dispositivos em escala |
| 1     | [Insights da série do tempo do Azure](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 unidade              | Armazenamento para dados de mensagens e permite análise de telemetria de mergulho profundo |

> [!NOTE]
> Você pode encontrar informações de [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)preços para esses serviços em . Você pode encontrar detalhes de uso e faturamento para sua assinatura no [Portal do Azure](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Implantar o acelerador de solução

Exemplos de implantação:

### <a name="example-deploy-net-version"></a>Exemplo: implantar a versão .NET

O exemplo a seguir mostra como implantar a versão .NET básica do acelerador de solução de Monitoramento Remoto:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemplo: implantar a versão Java

O exemplo a seguir mostra como implantar a versão Standard em Java do acelerador de solução de Monitoramento Remoto:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opções de comando pcs

Quando você executa o comando `pcs` para implantar uma solução, é solicitado:

- Um nome para a solução. Esse nome deve ser exclusivo.
- A assinatura do Azure a utilizar.
- Um local.
- Credenciais para as máquinas virtuais que hospedam o microsserviços. Você pode usar essas credenciais para acessar as máquinas virtuais para solução de problemas.

Quando o comando `pcs` termina, ele exibe a URL do seu novo acelerador de solução. O comando `pcs` também cria um arquivo `{deployment-name}-output.json` que contém informações como o nome do Hub IoT que ele criou.

Para saber mais sobre os parâmetros de linha de comando, execute:

```cmd/sh
pcs -h
```

Para obter mais informações sobre a CLI, consulte [Como usar a CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu como:

> [!div class="checklist"]
> * Configurar o acelerador de solução
> * Implantar o acelerador de solução
> * Entrar no acelerador de solução

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel de solução](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
