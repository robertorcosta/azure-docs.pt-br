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
ms.openlocfilehash: 9ed918c841989cb50efa362251d09c0b655b7fc4
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89570102"
---
| Recurso | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho do cache | 5 GiB por unidade<sup>2</sup> |
| Conexões de back-end simultâneas<sup>3</sup> por autoridade HTTP | 2\.048 por unidade<sup>4</sup> |
| Tamanho máximo de resposta em cache | 2 MiB |
| Tamanho máximo do documento da política | 256 KiB<sup>5</sup> |
| Domínios de gateway personalizado máximo por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados de autoridade de certificação por instância de serviço<sup>7</sup> | 10 |
| Número máximo de instâncias de serviço por assinatura<sup>8</sup> | 20 |
| Número máximo de assinaturas por instância de serviço<sup>8</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>8</sup> | 50 |
| Número máximo de APIs por instância de serviço<sup>8</sup> | 50 |
| Número máximo de operações de API por instância de serviço<sup>8</sup> | 1,000 |
| Duração total máxima da solicitação<sup>8</sup> | 30 segundos |
| Tamanho máximo de conteúdo do buffer<sup>8</sup> | 2 MiB |
| Tamanho máximo da URL de solicitação<sup>9</sup> | 4096 bytes |
| Número máximo de gateways auto-hospedados<sup>10</sup> | 25 |

<sup>1</sup>Os limites de escala dependem do tipo de preço. Para obter detalhes dos tipos de preço e dos limites de escala deles, confira [Preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>O tamanho do cache por unidade depende do tipo de preço. Para ver os tipos de preço e os limites de escala deles, confira [Preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>As conexões são colocadas em pool e reutilizadas, a menos que sejam explicitamente encerradas pelo back-end.<br/>
<sup>4</sup>Esse é o limite por unidade dos níveis Básico, Standard e Premium. O nível Desenvolvedor está limitado a 1.024. Esse limite não se aplica ao nível de Consumo.<br/>
<sup>5</sup>Esse limite se aplica aos níveis Básico, Standard e Premium. No nível de Consumo, o tamanho do documento de política é limitado a 4 KiB.<br/>
<sup>6</sup>Vários domínios personalizados têm suporte apenas nos níveis Desenvolvedor e Premium.<br/>
<sup>7</sup>Certificados de autoridade de certificação não têm suporte no nível de Consumo.<br/>
<sup>8</sup>Esse limite aplica-se apenas ao nível de Consumo. Não há nenhum limite nessas categorias para outros níveis.<br/>
<sup>9</sup>Aplica-se apenas ao nível de Consumo. Inclui uma cadeia de caracteres de consulta de até 2048 bytes de comprimento.<br/>
<sup>10</sup>Os gateways auto-hospedados têm suporte apenas nos níveis Desenvolvedor e Premium. O limite se aplica ao número de [recursos de gateway auto-hospedados](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway). Para aumentar esse limite, entre em contato com o [suporte](https://azure.microsoft.com/support/options/). Observe que o número de nós (ou réplicas) associado a um recurso de gateway auto-hospedado é ilimitado no nível Premium e é limitado em um nó no nível Desenvolvedor.
