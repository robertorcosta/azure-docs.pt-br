---
title: Solução de problemas comuns
description: Aprenda a solucionar problemas com os vários SDKs enquanto consulta os recursos do Azure com o Azure Resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303910"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Solucionando problemas usando o Gráfico de Recursos do Azure

Você pode encontrar erros ao consultar os recursos do Azure com o Gráfico de Recursos do Azure. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes do erro

A maioria dos erros é o resultado de um problema durante a execução de uma consulta com o Gráfico de Recursos do Azure. Quando uma consulta falha, o SDK fornece detalhes sobre a consulta com falha. Essas informações indicam o problema para que possam ser corrigidos e uma consulta posterior seja bem sucedida.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Cenário: Muitas assinaturas

#### <a name="issue"></a>Problema

Os clientes com acesso a mais de 1000 assinaturas, incluindo assinaturas entre inquilinos com [o Azure Lighthouse,](../../../lighthouse/overview.md)não podem obter dados em todas as assinaturas em uma única chamada para o Azure Resource Graph.

#### <a name="cause"></a>Causa

A Cli e powerShell do Azure encaminham apenas as primeiras 1000 assinaturas ao Azure Resource Graph. A API REST for Azure Resource Graph aceita um número máximo de assinaturas para executar a consulta.

#### <a name="resolution"></a>Resolução

Solicitações em lote para a consulta com um subconjunto de assinaturas para ficar abaixo do limite de assinatura de 1000. A solução está usando o parâmetro **De assinatura** no PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Cenário: Cabeçalho RESTO do tipo de conteúdo não suportado

#### <a name="issue"></a>Problema

Os clientes que consultarem a API Rest do Gráfico de Recursos do Azure recebem uma resposta _de 500_ (Erro interno do servidor) retornada.

#### <a name="cause"></a>Causa

A API rest do Gráfico de `Content-Type` recursos do Azure só suporta um **de aplicativo/json**. Algumas ferramentas REST ou agentes padrão para **texto/planície,** que não é suportado pela API REST.

#### <a name="resolution"></a>Resolução

Valide se a ferramenta ou agente que você está usando para consultar `Content-Type` o Gráfico de Recursos do Azure tem o cabeçalho rest API configurado para **aplicativo/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Cenário: Sem permissão de leitura para todas as assinaturas na lista

#### <a name="issue"></a>Problema

Os clientes que passam explicitamente uma lista de assinaturas com uma consulta do Azure Resource Graph recebem uma resposta _403_ (Proibida).

#### <a name="cause"></a>Causa

Se o cliente não tiver permissão de leitura para todas as assinaturas fornecidas, a solicitação será negada devido à falta de direitos de segurança adequados.

#### <a name="resolution"></a>Resolução

Inclua pelo menos uma assinatura na lista de assinaturas à qual o cliente que executa a consulta tenha pelo menos acesso à leitura. Para obter mais informações, consulte [Permissões no Gráfico de Recursos do Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.