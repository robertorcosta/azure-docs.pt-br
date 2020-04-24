---
title: Gerenciar sua conta
description: Saiba o que inclui uma conta do lote do Azure
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce0c612e3434249c0c7d078949b10e0b9f6b1f10
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116002"
---
# <a name="manage-your-batch-account"></a>Gerenciar sua conta do lote

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito por meio de uma conta do Lote.

Você pode criar uma conta do Lote do Azure usando o [portal do Azure](batch-account-create-portal.md) ou por meio de programação, como com a [biblioteca .NET do Gerenciamento de Lote](batch-management-dotnet.md). Ao criar a conta, você pode associar uma conta de armazenamento do Azure para armazenar dados de entrada e saída ou aplicativos relacionados ao trabalho.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

## <a name="components-of-the-batch-account"></a>Componentes da conta do lote

A conta do lote permite que você execute trabalhos em lote HPC (computação de alto desempenho) e paralelos em grande escala com eficiência no Azure. Na conta que você gerencia:

- Os aplicativos que você está executando

- A alocação de pools e nós dentro de pools

- O número e os tipos de tarefas 

- A entrada e a saída de dados. Você não precisa instalar software adicional para gerenciar tarefas.

- Ao criar a conta do lote, você será solicitado a atribuir um nome a ela. Esse nome é sua ID e, uma vez atribuída, não pode ser alterado.

- Para alterar o nome de uma conta, você precisa excluí-la e criar uma nova conta do lote.

- A conta é criada na assinatura que você deseja usar.

- Use a conta para identificar e recuperar chaves de conta primárias e secundárias de qualquer conta do lote em sua assinatura.

- A conta mantém informações sobre alocação de pool e cotas de núcleo.  

- A conta contém informações de localização.

- A conta identifica sua conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- Crie uma conta do lote usando o [portal do Azure](batch-account-create-portal.md).
- Crie uma conta do lote programaticamente, como com a [biblioteca .net de gerenciamento do lote](batch-management-dotnet.md).
- [Configure ou desabilite o acesso remoto para nós de computação em um pool do lote do Azure](pool-endpoint-configuration.md).
- [Executar tarefas de preparação e liberação do trabalho em nós de computação do Lote](batch-job-prep-release.md)

