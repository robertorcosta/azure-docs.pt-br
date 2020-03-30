---
title: Dimensione o Azure Cosmos DB em um cronograma usando o temporizador de funções do Azure
description: Saiba como dimensionar as alterações no throughput no Azure Cosmos DB usando as funções PowerShell e Azure.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935162"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Scale Azure Cosmos DB throughput usando o gatilho do temporizador de funções do Azure

O desempenho de uma conta do Azure Cosmos é baseado na quantidade de throughput provisionado expressa em Unidades de Solicitação por segundo (RU/s). O provisionamento é em uma segunda granularidade e é cobrado com base no maior RU/s por hora. Esse modelo de capacidade provisionada permite que o serviço forneça uma taxa de transferência previsível e consistente, além de garantia de baixa latência e alta disponibilidade. A maioria das cargas de trabalho de produção esses recursos. No entanto, em ambientes de desenvolvimento e teste onde o Azure Cosmos DB é usado apenas durante o horário de trabalho, você pode aumentar a produção pela manhã e reduzir a escala à noite após o horário de trabalho.

Você pode definir o throughput através de Modelos de [Gerenciador de Recursos do Azure,](resource-manager-samples.md) [Azure CLI](cli-samples.md)e [PowerShell](powershell-samples-sql.md), para contas API Core (SQL) ou usando os SDKs Azure Cosmos DB específicos para o idioma. A vantagem de usar modelos de gerenciador de recursos, Azure CLI ou PowerShell é que eles suportam todas as APIs do modelo Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Projeto de amostra de agendador de throughput

Para simplificar o processo de escala do Azure Cosmos DB em um cronograma, criamos um projeto de exemplo chamado [Azure Cosmos throughput scheduler](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Este projeto é um aplicativo De funções do Azure com dois gatilhos temporizador: "ScaleUpTrigger" e "ScaleDownTrigger". Os gatilhos executam um script PowerShell que define o `resources.json` throughput em cada recurso conforme definido no arquivo em cada gatilho. O ScaleUpTrigger está configurado para ser executado às 8 AM UTC e o ScaleDownTrigger está configurado `function.json` para ser executado a 6 PM UTC e esses tempos podem ser facilmente atualizados dentro do arquivo para cada gatilho.

Você pode clonar este projeto localmente, modificá-lo para especificar os recursos do Azure Cosmos DB para escalar e diminuir e o cronograma a ser executado. Mais tarde, você pode implantá-lo em uma assinatura do Azure e protegê-la usando a identidade de serviço gerenciada com permissões RBAC [(Role-based Access Control, controle de acesso baseado em](role-based-access-control.md) função) com a função "Operador Azure Cosmos DB" para definir throughput em suas contas do Azure Cosmos.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais e baixe a amostra do programador de [throughput do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
