---
title: Limites de dados para o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Limitações de dados para a API de Análise de Texto dos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 068c2dc698e9f0b6d6f2f6486dff863c1343b178
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88258277"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limites de dados e taxa para a API de Análise de Texto
<a name="data-limits"></a>

Use este artigo para localizar os limites para o tamanho e as tarifas para as quais você pode enviar dados para API de Análise de Texto. 

## <a name="data-limits"></a>Limites de dados

> [!NOTE]
> * Se você precisar analisar documentos maiores do que o limite permite, poderá dividir o texto em partes menores antes de enviá-los para a API. 
> * Um documento é uma cadeia de caracteres de texto.  

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um único documento | 5\.120 caracteres conforme medidos por [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). Também se aplica à Análise de Texto do contêiner de integridade. |
| Tamanho máximo de toda a solicitação | 1 MB. Também se aplica à Análise de Texto do contêiner de integridade. |

O número máximo de documentos que você pode enviar em uma solicitação dependerá da versão da API e do recurso que você está usando.

#### <a name="version-3"></a>[Versão 3](#tab/version-3)

Os limites a seguir mudaram na v3 da API. Exceder os limites abaixo vai gerar um código de erro HTTP 400.


| Recurso | Máximo de documentos por solicitação | 
|----------|-----------|
| Detecção de Idioma | 1000 |
| Análise de Sentimento | 10 |
| Extração de Frases-Chave | 10 |
| Reconhecimento de Entidade Nomeada | 5 |
| Vinculação de Identidade | 5 |
| Análise de Texto do contêiner de integridade | 1000 |
#### <a name="version-2"></a>[Versão 2](#tab/version-2)

| Recurso | Máximo de documentos por solicitação | 
|----------|-----------|
| Detecção de Idioma | 1000 |
| Análise de Sentimento | 1000 |
| Extração de Frases-Chave | 1000 |
| Reconhecimento de Entidade Nomeada | 1000 |
| Vinculação de Identidade | 1000 |

---

## <a name="rate-limits"></a>Limites de taxa

O limite de taxa varia de acordo com o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Esses limites são os mesmos para as duas versões da API. Esses limites de taxa não se aplicam à Análise de Texto do contêiner de integridade, que não tem um limite de taxa definido.

| Camada          | Solicitações por segundo | Solicitações por minuto |
|---------------|---------------------|---------------------|
| S/Vários serviços | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

As solicitações são calculadas separadamente para cada recurso de Análise de Texto. Por exemplo, você pode enviar o número máximo de solicitações para o tipo de preço para cada recurso, ao mesmo tempo.  


## <a name="see-also"></a>Confira também

* [O que é a API de Análise de Texto](../overview.md)
* [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
