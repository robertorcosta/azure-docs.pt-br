---
title: incluir arquivo
description: incluir arquivo
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 072f13f5a0884cf95fe760e17ff0d770111f4da0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204669"
---
| Recurso | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho do cache | 5 GiB por unidade<sup>2</sup> |
| Conexões de back-end simultâneas<sup>3</sup> por autoridade http | 2.048 por unidade<sup>4</sup> |
| Tamanho máximo de resposta em cache | 2 MiB |
| Tamanho máximo do documento da política | 256 KiB<sup>5</sup> |
| Domínios de gateway personalizado máximo por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados de autoridade de certificação por instância de serviço<sup>7</sup> | 10 |
| Número máximo de instâncias de serviço por assinatura<sup>8</sup> | 20 |
| Número máximo de assinaturas por instância de serviço<sup>8</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>8</sup> | 50 |
| Número máximo de APIs por instância de serviço<sup>8</sup> | 50 |
| Número máximo de operações de API por instância de serviço<sup>8</sup> | 1,000 |
| Duração total de solicitação máxima<sup>8</sup> | 30 segundos |
| Tamanho máximo de carga em buffer<sup>8</sup> | 2 MiB |
| Tamanho máximo da URL de solicitação<sup>9</sup> | 4096 bytes |
| Número máximo de gateways auto-hospedados<sup>10</sup> | 25 |

<sup>1</sup> Os limites de dimensionamento dependem do tipo de preço. Para obter detalhes sobre os tipos de preço e seus limites de dimensionamento, consulte [preços do gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> O tamanho do cache por unidade depende do tipo de preço. Para ver os tipos de preço e seus limites de dimensionamento, consulte [preços de gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> As conexões são agrupadas e reutilizadas, a menos que sejam fechadas explicitamente pelo back-end.<br/>
<sup>4</sup> Esse limite é por unidade das camadas básica, Standard e Premium. A camada de desenvolvedor é limitada a 1.024. Esse limite não se aplica à camada de consumo.<br/>
<sup>5</sup> Esse limite se aplica às camadas Basic, Standard e Premium. No tipo de consumo, o tamanho do documento de política é limitado a 4 KiB.<br/>
<sup>6</sup> Vários domínios personalizados têm suporte apenas nas camadas Developer e Premium.<br/>
<sup>7</sup> Não há suporte para certificados de autoridade de certificação na camada de consumo.<br/>
<sup>8</sup> Este recurso se aplica somente à camada de consumo.<br/>
<sup>9</sup> Aplica-se somente à camada de consumo. Inclui uma cadeia de caracteres de consulta de até 2048 bytes de comprimento.<br/>
<sup>10</sup> Os gateways auto-hospedados têm suporte apenas nas camadas Developer e Premium. O limite se aplica ao número de [recursos de gateway hospedados internamente](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway). Para aumentar esse limite, entre em contato com o [suporte](https://azure.microsoft.com/support/options/). Observe que o número de nós (ou réplicas) associados a um recurso de gateway auto-hospedado é ilimitado na camada Premium e é limitado em um único nó na camada de desenvolvedor.
