---
title: Gerencie sua conta- Azure Batch | Microsoft Docs
description: Saiba o que compreende uma conta do Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4df2ec0439e659fd8dc1448c6209c9718114791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479783"
---
# <a name="manage-your-batch-account"></a>Gerencie sua conta em lote

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito por meio de uma conta do Lote.

Você pode criar uma conta do Lote do Azure usando o [portal do Azure](batch-account-create-portal.md) ou por meio de programação, como com a [biblioteca .NET do Gerenciamento de Lote](batch-management-dotnet.md). Ao criar a conta, você pode associar uma conta de armazenamento do Azure para armazenar dados de entrada e saída ou aplicativos relacionados ao trabalho.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

## <a name="components-of-the-batch-account"></a>Componentes da conta Batch

A conta Batch permite que você execute trabalhos em lote de computação paralela e de alto desempenho (HPC) em larga escala de forma eficiente no Azure. Dentro da conta que você gerencia:

- os aplicativos que você está executando

- a alocação de piscinas e nós dentro de piscinas

- o número e tipos de tarefas 

- a entrada e saída de dados. Você não precisa instalar software adicional para gerenciar tarefas.

- Quando você cria a conta Batch, você é solicitado a atribuir um nome a ela. Este nome é seu ID e uma vez atribuído não pode ser alterado.

- Para alterar o nome de uma conta, você precisa excluí-la e criar uma nova conta batch.

- A conta é criada dentro da assinatura que você deseja usar.

- Use a conta para identificar e recuperar chaves de conta primárias e secundárias de qualquer conta batch dentro de sua assinatura.

- A conta mantém informações sobre alocação de pool e cotas principais.  

- A conta contém informações de localização.

- A conta identifica sua conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- Crie uma conta em lote usando o [portal Azure](batch-account-create-portal.md).
- Crie uma conta em lote de forma programática, como na [biblioteca Batch Management .NET](batch-management-dotnet.md).
- [Configure ou desative o acesso remoto aos nós de computação em um pool de lotes Do zure](pool-endpoint-configuration.md).
- [Executar tarefas de preparação e liberação do trabalho em nós de computação do Lote](batch-job-prep-release.md)

