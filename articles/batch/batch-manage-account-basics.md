---
title: Gerenciar sua conta-Azure batch | Microsoft Docs
description: Saiba o que inclui uma conta do lote do Azure
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
ms.openlocfilehash: 72630a2003b63e60ba79882e1861283173840425
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375181"
---
# <a name="manage-your-batch-account"></a>Gerenciar sua conta do lote

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito por meio de uma conta do Lote.

Você pode criar uma conta do Lote do Azure usando o [portal do Azure](batch-account-create-portal.md) ou por meio de programação, como com a [biblioteca .NET do Gerenciamento de Lote](batch-management-dotnet.md). Ao criar a conta, você pode associar uma conta de armazenamento do Azure para armazenar dados de entrada e saída ou aplicativos relacionados ao trabalho.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

## <a name="components-of-the-batch-account"></a>Componentes da conta do lote

A conta do lote permite que você execute trabalhos em lote HPC (computação de alto desempenho) e paralelos em grande escala com eficiência no Azure. Na conta que você gerencia:

- os aplicativos que você está executando

- a alocação de pools e nós dentro de pools

- o número e os tipos de tarefas 

- a entrada e a saída de dados. Você não precisa instalar software adicional para gerenciar tarefas.

- Ao criar a conta do lote, você será solicitado a atribuir um nome a ela. Esse nome é sua ID e, uma vez atribuída, não pode ser alterado.

- Para alterar o nome de uma conta, você precisa excluí-la e criar uma nova conta do lote.

- A conta é criada na assinatura que você deseja usar.

- Use a conta para identificar e recuperar chaves de conta primárias e secundárias de qualquer conta do lote em sua assinatura.

- A conta mantém informações sobre alocação de pool e cotas de núcleo.  

- A conta contém informações de localização.

- A conta identifica sua conta de armazenamento.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Crie uma conta do lote usando o [portal do Azure](batch-account-create-portal.md).
- Crie uma conta do lote programaticamente, como com a [biblioteca .net de gerenciamento do lote](batch-management-dotnet.md).
- [Configure ou desabilite o acesso remoto para nós de computação em um pool do lote do Azure](pool-endpoint-configuration.md).
- [Executar tarefas de preparação e liberação de trabalho em nós de computação do lote](batch-job-prep-release.md)
