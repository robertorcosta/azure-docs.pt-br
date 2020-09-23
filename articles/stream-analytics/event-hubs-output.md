---
title: Saída dos hubs de eventos de Azure Stream Analytics
description: Este artigo descreve como gerar dados de saída de Azure Stream Analytics para os hubs de eventos do Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 50d2d974815e0921d99154bce67f604b7314970d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892020"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Saída dos hubs de eventos de Azure Stream Analytics

Os serviço de [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo. Um uso de um hub de eventos como saída é quando a saída de um trabalho do Stream Analytics torna-se a entrada de outro trabalho de streaming. Para obter informações sobre o tamanho máximo da mensagem e a otimização do tamanho do lote, consulte a seção [Tamanho do lote de saída](#output-batch-size).

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir tem os parâmetros necessários para configurar os fluxos de dados dos hubs de eventos como uma saída.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse hub de eventos. |
| Namespace do Hub de Eventos | Um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo hub de eventos, também criou um namespace do hub de eventos. |
| Nome do Hub de Eventos | O nome da sua saída de hub de eventos. |
| Nome da política do hub de eventos | A política de acesso compartilhado, que você pode criar na guia **Configurar** do hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do hub de eventos | A chave de acesso compartilhado que é usada para autenticar o acesso ao namespace do hub de eventos. |
| Coluna de chave da partição | Opcional. Uma coluna contém a chave de partição para a saída do hub de eventos. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável somente à serialização de JSON. Uma **Linha separada** especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada**, o JSON será lido um objeto por vez. O conteúdo inteiro por si só não seria um JSON válido. A **matriz** especifica que a saída é formatada como uma matriz de objetos JSON.  |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez do conteúdo. Há mais informações sobre esse recurso na seção [Propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |

## <a name="partitioning"></a>Particionamento

O particionamento varia dependendo do alinhamento da partição. Quando a chave de partição de saída do hub de eventos estiver alinhada de forma igual com a etapa de consulta (anterior) de upstream, o número de gravadores será o mesmo que o de partições na saída do hub de eventos. Cada gravador usa [a classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet&preserve-view=true) para enviar eventos para a partição específica. Quando a chave de partição de saída do hub de eventos não é alinhada com a etapa de consulta (anterior) de upstream, o número de gravadores será o mesmo que o de partições nessa etapa anterior. Cada gravador usa a [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet&preserve-view=true) no **EventHubClient** para enviar eventos para todas as partições de saída. 

## <a name="output-batch-size"></a>Tamanho do lote de saída

O tamanho máximo da mensagem é 256 KB ou 1 MB por mensagem. Para obter mais informações, consulte [limites de hubs de eventos](../event-hubs/event-hubs-quotas.md). Quando o particionamento de entrada/saída não estiver alinhado, cada evento é compactado individualmente em um `EventData` e enviado em um lote com até o tamanho máximo da mensagem. Isso também ocorrerá se forem usadas [propriedades de metadados personalizadas](#custom-metadata-properties-for-output). Quando o particionamento de entrada/saída estiver alinhado, vários eventos serão compactados em uma única instância `EventData` com até o tamanho máximo da mensagem e serão enviados.

## <a name="custom-metadata-properties-for-output"></a>Personalizar propriedades de metadados para a saída

Você pode anexar colunas de consulta às suas mensagens de saída como propriedades de usuário. Essas colunas não entram no conteúdo. As propriedades estão presentes no formato de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados do Stream Analytics têm suporte, exceto Registro e Matriz.  

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure](quick-create-azure-cli.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando um modelo do Resource Manager](quick-create-azure-resource-manager.md)
* [Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)
