---
title: Regras de nomenclatura de entidades do Azure Data Factory | Microsoft Docs
description: Descreve as regras de nomenclatura para entidades de Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: c31cffd3c1734e8f71f8971d597eb9e3703ae331
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166438"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - regras de nomenclatura
A tabela a seguir fornece regras de nomenclatura para artefatos Data Factory.

| NOME | Exclusividade do nome | Verificações de validação |
|:--- |:--- |:--- |
| Data Factory |Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas, isto é, `MyDF` e `mydf` referem-se ao mesmo data factory. |<ul><li>Cada data factory está vinculado a exatamente uma assinatura do Azure.</li><li>Os nomes do objeto devem começar com uma letra ou número e podem conter apenas letras, números e o caractere traço (-).</li><li>Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número. Não há permissão para traços consecutivos em nomes de contêiner.</li><li>O nome pode ter de 3 a 63 caracteres.</li></ul> |
| Serviços/conjuntos de dados/Pipelines vinculados |Exclusivo em um mesmo data factory. Os nomes não diferenciam maiúsculas de minúsculas. |<ul><li>Os nomes de objetos devem começar com uma letra, um número ou um sublinhado (_).</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”, ”>”,” * ”,”%”,”&”,”:”,”\\”</li><li>Os traços ("-") não são permitidos apenas em nomes de serviços vinculados e de conjuntos de dados.</li></ul>  |
| Grupo de recursos |Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas. | Para saber mais, veja [Restrições e regras de nomenclatura do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/considerations/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Próximas etapas
Saiba como criar data factories, seguindo as instruções passo a passo no artigo [Início rápido: criar um data factory](quickstart-create-data-factory-powershell.md). 
