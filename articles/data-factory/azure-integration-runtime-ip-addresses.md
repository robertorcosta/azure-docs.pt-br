---
title: Endereços de IP do Azure Integration Runtime
description: Saiba de quais endereços IP você deve permitir tráfego de entrada, a fim de configurar corretamente firewalls para garantir o acesso da rede aos armazenamentos de dados.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 598876e12fe04129692d0c9a842f4edb2ec00768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086797"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Endereços de IP do Azure Integration Runtime

Os endereços IP que o Azure Integration Runtime usa dependem da região onde o tempo de execução da integração do Azure está localizado. *Todos os* Os tempos de execução de integração do Azure que estão na mesma região usam as mesmas faixas de endereço IP.

> [!IMPORTANT]  
> Os fluxos de dados não utilizam esses IPs atualmente. 
>
> Você pode usar essas faixas de IP para execuções de movimentação de dados, pipeline e atividades externas. Essas faixas de IP podem ser usadas para whitelisting em data stores/ Network Security Group (NSG)/ Firewalls para acesso de entrada a partir do tempo de execução do Azure Integration. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Endereços IP do Azure Integration Runtime: regiões específicas

Permitir o tráfego a partir dos endereços IP listados para o tempo de execução da Integração do Azure na região específica do Azure onde seus recursos estão localizados:

|                | Região              | Endereços IP                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Ásia           | Leste da Ásia           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Sudeste Asiático      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Austrália      | Leste da Austrália      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Sudeste da Austrália | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brasil         | Sul do Brasil        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | Canadá Central      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| China          | Leste da China 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| Europa         | Norte da Europa        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa Ocidental         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| França         | França Central      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| Índia          | Índia Central       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japão          | Leste do Japão          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Coreia do Sul          | Coreia Central       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| África do Sul   | Norte da África do Sul  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| United Kingdom | Sul do Reino Unido            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Estados Unidos  | Centro dos EUA          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | Leste dos EUA             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | Leste dos EUA 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | Leste dos EUA 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | Centro-Norte dos EUA    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | Centro-Sul dos Estados Unidos    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | Centro-Oeste dos EUA     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | Oeste dos EUA             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | Oeste dos EUA 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | Gov. dos EUA – Virgínia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Problema conhecido com o Armazenamento Azure

* Ao se conectar à conta do Azure Storage, as regras da rede IP não têm efeito sobre as solicitações originárias do tempo de execução de integração do Azure na mesma região que a conta de armazenamento. Para obter mais detalhes, [consulte este artigo](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Em vez disso, sugerimos o uso de [serviços confiáveis enquanto nos conectamos ao Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Próximas etapas

* [Considerações sobre segurança para movimentação de dados no Azure Data Factory](data-movement-security-considerations.md)
