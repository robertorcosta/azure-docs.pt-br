---
title: Limites de recursos e objetos do Azure Analysis Services | Microsoft Docs
description: Este artigo descreve os limites de recursos e objetos para um servidor do Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573206"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limites de recurso e objeto do Analysis Services

Este artigo descreve os limites de objeto de recurso e modelo.

## <a name="tier-limits"></a>Limites de camada

Para limites de QPU e Memória para níveis de desenvolvedor, básico e padrão, consulte a [página de preços dos Serviços de Análise do Azure](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limites do objeto

Esses limites são teóricos. Desempenho será ser diminuído em números mais baixos.

|Objeto|Tamanho máximo/números|  
|------------|----------------------------|  
|Bancos de dados em uma instância|16.000|  
|Número combinado de tabelas e colunas em um banco de dados|16.000|  
|Linhas em uma tabela|Ilimitado<br /><br /> **Aviso:** com a restrição de que nenhuma coluna única na tabela pode ter mais de 1.999.999.997 valores distintos.|  
|Hierarquias em uma tabela|15,999|  
|Níveis em uma hierarquia|15,999|  
|Relações|8,000|  
|Colunas de chave na tabela de todos os|15,999|  
|Medidas em tabelas|2 ^ 31-1 = 2.147.483.647|  
|Células retornadas por uma consulta|2 ^ 31-1 = 2.147.483.647|  
|Tamanho do registro da consulta de origem|64 k|  
|Comprimento de nomes de objeto|512 caracteres|  


