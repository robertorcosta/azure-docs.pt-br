---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74851614"
---
Você será cobrado pelo armazenamento do Azure com base no uso da sua conta de armazenamento. Todos os objetos em uma conta de armazenamento são cobrados juntos como um grupo. 

Os custos de armazenamento são calculados de acordo com os seguintes fatores: 

* **Região** refere-se à região geográfica na qual sua conta está baseada.
* **Tipo de conta** refere-se ao tipo de conta de armazenamento que você está usando. 
* **Camada de acesso** refere-se ao padrão de uso de dados que você especificou para sua conta de armazenamento de BLOBs v2 ou de uso geral.
* A **capacidade** de armazenamento refere-se à quantidade de alocação de sua conta de armazenamento que você está usando para armazenar dados.
* A **replicação** determina quantas cópias dos seus dados serão mantidas de uma só vez e em quais locais.
* As **Transações** referem-se a todas as operações de leitura e gravação no armazenamento do Azure.
* A **saída de dados** refere-se a dados transferidos para fora de uma região do Azure. Quando os dados em sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, você é cobrado pela saída de dados. Para obter informações sobre como usar grupos de recursos para agrupar seus dados e serviços na mesma região para limitar os encargos de saída, consulte [O que é um grupo de recursos do Azure?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas de preços com base no tipo de conta, capacidade de armazenamento, replicação e transações. A página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas de preços para saída de dados. Você pode usar a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ajudar a estimar os custos.

