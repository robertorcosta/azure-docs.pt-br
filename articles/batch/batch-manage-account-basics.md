---
title: Gerenciar sua conta
description: Saiba o que está incluído em uma conta do Lote do Azure
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722959"
---
# <a name="manage-your-batch-account"></a>Gerenciar sua conta do Lote

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito por meio de uma conta do Lote.

Você pode criar uma conta do Lote do Azure usando o [portal do Azure](batch-account-create-portal.md) ou por meio de programação, como com a [biblioteca .NET do Gerenciamento de Lote](batch-management-dotnet.md). Ao criar a conta, você pode associar uma conta de armazenamento do Azure para armazenar dados de entrada e saída ou aplicativos relacionados ao trabalho.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

## <a name="components-of-the-batch-account"></a>Componentes da conta do Lote

A conta do Lote permite que você execute trabalhos em lotes de HPC (computação de alto desempenho) e paralelos em grande escala com eficiência no Azure. Dentro da conta que você gerencia:

- Os aplicativos que você está executando

- A alocação de pools e nós dentro de pools

- O número e os tipos de tarefas 

- A entrada e a saída de dados. Você não precisa instalar software adicional para gerenciar tarefas.

- Ao criar a conta do Lote, você será solicitado a atribuir um nome a ela. Esse nome é sua ID e, uma vez atribuída, não pode ser alterada.

- Para alterar o nome de uma conta, você precisa excluí-la e criar outra conta do Lote.

- A conta é criada na assinatura que você deseja usar.

- Use a conta para identificar e recuperar as chaves da conta principal e secundária de qualquer conta do Lote em sua assinatura.

- A conta mantém informações sobre alocação de pool e cotas de núcleo.  

- A conta contém informações de localização.

- A conta identifica sua conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- Criar uma conta do Lote usando o [portal do Azure](batch-account-create-portal.md).
- Criar uma conta do Lote por meio de programação, usando a [Biblioteca .NET de Gerenciamento de Lotes](batch-management-dotnet.md).
- [Configurar ou desabilitar o acesso remoto a nós de computação em um pool do Lote do Azure](pool-endpoint-configuration.md).
- [Executar tarefas de preparação e liberação do trabalho em nós de computação do Lote](batch-job-prep-release.md)

