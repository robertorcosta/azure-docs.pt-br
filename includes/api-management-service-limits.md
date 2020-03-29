---
title: incluir arquivo
description: incluir arquivo
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 2d4c8c2c831bd6ef16f60c34a6353f4a742798f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76159222"
---
| Recurso | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho do cache | 5 GiB por unidade<sup>2</sup> |
| Conexões back-end simultâneas<sup>3</sup> por autoridade HTTP | 2.048 por unidade<sup>4</sup> |
| Tamanho máximo de resposta em cache | 2 MiB |
| Tamanho máximo do documento da política | 256 KiB<sup>5</sup> |
| Domínios de gateway personalizado máximo por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados ca por instância de serviço | 10 |
| Número máximo de instâncias de serviço por assinatura <sup>7</sup> | 20 |
| Número máximo de assinaturas por instância de serviço <sup>7</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço <sup>7</sup> | 50 |
| Número máximo de APIs por instância de serviço <sup>7</sup> | 50 |
| Número máximo de operações de API por instância de serviço <sup>7</sup> | 1,000 |
| Duração máxima total da solicitação<sup>7</sup> | 30 segundos |
| Tamanho máximo de carga útil do buffer <sup>7</sup> | 2 MiB |
| Tamanho máximo da URL de solicitação<sup>8</sup> | 4096 bytes |

<sup>1</sup> Os limites de dimensionamento dependem do nível de preços. Para ver os níveis de preços e seus limites de escala, consulte [os preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> O tamanho do cache por unidade depende do nível de preços. Para ver os níveis de preços e seus limites de escala, consulte [os preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> As conexões são agrupadas e reutilizadas, a menos que explicitamente fechadas pela parte de trás.<br/>
<sup>4</sup> Esse limite é por unidade dos níveis Básico, Padrão e Premium. O nível de Desenvolvedor é limitado a 1.024. Esse limite não se aplica à camada Consumo.<br/>
<sup>5</sup> Esse limite se aplica aos níveis Básico, Padrão e Premium. Na camada Consumo, o tamanho do documento de diretiva é limitado a 4 KiB.<br/>
<sup>6</sup> Este recurso está disponível apenas no nível Premium.<br/>
<sup>7</sup> Este recurso se aplica apenas à camada Consumo.<br/>
<sup>8</sup> Aplica-se apenas ao nível de Consumo. Inclui uma seqüência de consulta de até 2048.<br/>
