---
title: Versões de cluster suportadas no Azure Service Fabric
description: Saiba mais sobre as versões de cluster no Azure Service Fabric, incluindo um link para os lançamentos mais recentes do blog da equipe service fabric.
ms.topic: troubleshooting
ms.date: 03/02/2020
ms.openlocfilehash: 1158266857f0864ed2f442edb100032c70063719
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385037"
---
# <a name="supported-service-fabric-versions"></a>Versões do Service Fabric com suporte

Certifique-se de que seu cluster está sempre executando uma versão de malha de serviço azure suportada. Um mínimo de 60 dias após anunciarmos o lançamento de uma nova versão do Service Fabric, o suporte para a versão anterior termina. Você encontrará anúncios de novos lançamentos no blog da [equipe service fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Consulte os seguintes documentos para obter detalhes sobre como manter seu cluster executando uma versão de Malha de Serviço suportada:

- [Atualizar um cluster do Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Atualize a versão do Service Fabric que é executada no cluster autônomo do Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versões com suporte

A tabela a seguir lista as versões do Service Fabric e suas datas finais de suporte.

| runtime do Service Fabric no cluster | Permite a atualização diretamente da versão do cluster |Versão compatível do pacote SDK ou NuGet | Fim do suporte |
| --- | --- |--- | --- |
| Todas as versões de cluster antes de 5.3.121 | 5.1.158.* |Menor ou igual à versão 2.3 |20 de janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menor ou igual à versão 2.3 |24 de fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menor ou igual à versão 2.4 |10 de maio de 2017       |
| 5.5.* | 5.4.164.* |Menor ou igual à versão 2.5 |10 de agosto de 2017    |
| 5.6.* | 5.4.164.* |Menor ou igual à versão 2.6 |13 de outubro de 2017   |
| 5.7.* | 5.4.164.* |Menor ou igual à versão 2.7 |15 de dezembro de 2017  |
| 6.0.* | 5.6.205.* |Menor ou igual à versão 2.8 |30 de março de 2018     |
| 6.1.* | 5.7.221.* |Menor ou igual à versão 3.0 |15 de julho de 2018      |
| 6.2.* | 6.0.232.* |Menor ou igual à versão 3.1 |26 de outubro de 2018   |
| 6.3.* | 6.1.480.* |Menor ou igual à versão 3.2 |31 de março de 2019  |
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |15 de setembro de 2019 |
| 6.5.* | 6.4.617.* |Menor ou igual à versão 3.4 |1 de agosto de 2020 |
| 7.0.466.* | 6.4.664.* |Menor ou igual à versão 4.0|Versão atual, então sem data de término |
| 7.0.466.* | 6.5.* |Menor ou igual à versão 4.0|Versão atual, então sem data de término |
| 7.0.470.* | 7.0.466.* |Menor ou igual à versão 4.0 |Versão atual, então sem data de término |

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

A tabela a seguir lista os sistemas operacionais suportados para as versões de Malha de Serviço suportadas.

| Sistema operacional | Versão de Malha de Serviço suportada mais antiga |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Windows Server 1709 | 6,0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6,0 |

## <a name="supported-version-names"></a>Nomes de versão suportados

A tabela a seguir lista os nomes da versão do Service Fabric e seus números de versão correspondentes.

| Nome da versão | Número da versão do Windows | Número da versão do Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | NA |
| 5.3 CU1 | 5.3.204.9494 | NA |
| 5.3 CU2 | 5.3.301.9590 | NA |
| 5.3 CU3 | 5.3.311.9590 | NA |
| 5.4 CU2 | 5.4.164.9494 | NA |
| 5.5 CU1 | 5.5.216.0    | NA |
| 5.5 CU2 | 5.5.219.0    | NA |
| 5.5 CU3 | 5.5.227.0    | NA |
| 5.5 CU4 | 5.5.232.0    | NA |
| 5.6 RTO | 5.6.204.9494 | NA |
| 5.6 CU2 | 5.6.210.9494 | NA |
| 5.6 CU3 | 5.6.220.9494 | NA |
| 5.7 RTO | 5.7.198.9494 | NA |
| 5.7 CU4 | 5.7.221.9494 | NA |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | NA |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | NA |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | NA |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | NA |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
