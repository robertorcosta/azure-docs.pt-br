---
title: Ver latências relativas para regiões do Azure a partir de locais específicos
description: Saiba como exibir as latências relativas entre provedores de Internet para regiões do Azure de localidades específicas.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 39f81731f20566d1a39f3f0931ff52c4e8b43ec0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521389"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Exibir a latência relativa a regiões do Azure de localidades específicas

> [!WARNING]
> Este recurso está atualmente em pré-visualização e ainda está sendo testado para estabilidade.

Neste tutorial, saiba como usar o [Observador de Rede](network-watcher-monitoring-overview.md) do Azure para ajudar você a decidir em qual região do Azure implantar seu aplicativo ou serviço, com base nos dados demográficos de seus usuários. Além disso, você pode usá-lo para ajudar a avaliar as conexões dos provedores de serviços ao Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Criar um observador de rede

Se você já tiver um observador de rede em pelo menos uma [região](https://azure.microsoft.com/regions) do Azure, ignore as tarefas desta seção. Crie um grupo de recursos para o observador de rede. Neste exemplo, o grupo de recursos é criado na região Leste dos EUA, mas você pode criar o grupo de recursos em qualquer região do Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Crie um observador de rede. Você deve ter um observador de rede criado em pelo menos uma região do Azure. Neste exemplo, um observador de rede é criado na região do Azure Leste dos EUA.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Comparar as latências de rede relativas a uma única região do Azure de um local específico

Avalie os provedores de serviços ou soluciona um usuário que reporte um problema como "o site foi lento", de um local específico para a região azul onde um serviço é implantado. Por exemplo, o comando a seguir retorna as latências médias relativas do provedor de serviços de Internet entre o estado de Washington, nos Estados Unidos, e a região Oeste dos EUA 2 do Azure entre 13 a 15 de dezembro de 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> A região que você especifica no comando anterior não precisa ser a mesma que a região especificada durante a recuperação do observador de rede. O comando anterior requer apenas que você especifique um observador de rede existente. O observador de rede pode estar em qualquer região. Se você especificar valores para `-Country` e `-State`, eles deverão ser válidos. Os valores diferenciam maiúsculas de minúsculas. Os dados estão disponíveis para um número limitado de países/regiões, estados e cidades. Execute os comandos em [Exibir países/regiões disponíveis, estados, cidades e provedores](#view-available) para exibir uma lista de países/regiões, cidades e estados disponíveis para usar com o comando anterior. 

> [!WARNING]
> É necessário especificar uma data nos últimos 30 dias para `-StartTime` e `-EndTime`. A especificação de uma data anterior resultará na não devolução de dados.

A saída do comando anterior é a seguinte:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

Na saída retornada, o valor de **Pontuação** é a latência relativa entre regiões e provedores. Uma pontuação de 1 é a pior latência (mais alta), enquanto 100 é a mais baixa. As latências relativas são a média do dia. No exemplo anterior, embora esteja claro que as latências eram as mesmas nos dois dias e que há uma pequena diferença entre a latência dos dois provedores, também está claro que as latências dos dois provedores estão baixas na escala de 1 a 100. Embora isso seja esperado, como o estado de Washington, nos Estados Unidos, é fisicamente próximo à região Oeste dos EUA 2 do Azure, às vezes, os resultados não são conforme o esperado. Quanto maior for o intervalo de datas especificado, será mais fácil calcular a média de latência ao longo do tempo.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Comparar as latências de rede relativas entre regiões do Azure de um local específico

Se, em vez de especificar as latências relativas entre um local específico e uma região específica do Azure usando `-Location`, você quiser determinar as latências relativas a todas as regiões do Azure de um local físico específico, poderá fazê-lo. Por exemplo, o comando a seguir ajuda a avaliar em qual região do azure implantar um serviço se os usuários primários forem usuários do Comcast localizados no estado de Washington:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Diferentemente de quando você especifica um único local, se você não especificar um local, ou especificar vários locais, como "Oeste dos EUA 2", "Oeste dos EUA", deverá especificar um provedor de serviços de Internet ao executar o comando. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Veja os países/regiões disponíveis, estados, cidades e provedores

Os dados estão disponíveis para provedores de serviços de Internet específicos, países/regiões, estados e cidades. Para exibir uma lista de todos os provedores de serviços de Internet disponíveis, países/regiões, estados e cidades, para os dias que você pode visualizar dados, digite o seguinte comando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Os dados estão disponíveis apenas para os países/regiões, estados e cidades devolvidos pelo comando anterior. O comando anterior exige que você especifique um observador de rede existente. O exemplo especificou o observador de rede *NetworkWatcher_eastus* em um grupo de recursos denominado *NetworkWatcherRG*, mas você pode especificar qualquer observador de rede existente. Se você não tiver um observador de rede existente, crie um executando as tarefas em [Criar um observador de rede](#create-a-network-watcher). 

Depois de executar o comando anterior, você pode filtrar a saída retornada especificando os valores válidos para **País**, **Estado** e **Cidade**, se quiser.  Por exemplo, para exibir a lista de provedores de serviços de Internet disponíveis em Seattle, Washington, nos Estados Unidos, insira o seguinte comando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> O valor especificado para **País** deve ter letras maiúsculas e minúsculas. Os valores especificados para **Estado** e **Cidade** devem estar em minúsculas. Os valores devem estar na saída retornada após a execução do comando sem valores para **País**, **Estado** e **Cidade**. Se você especificar a capitalização incorreta ou especificar um valor para **País**, **Estado**, ou **Cidade** que não esteja na saída retornada após executar o comando sem valores para essas propriedades, a saída retornada estará vazia.
