---
title: Configurar a versão mínima necessária da TLS (segurança da camada de transporte) para uma conta de armazenamento
titleSuffix: Azure Storage
description: Configure uma conta de armazenamento para exigir uma versão mínima do protocolo TLS para clientes que fazem solicitações no armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209217"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Configurar a versão mínima necessária da TLS (segurança da camada de transporte) para uma conta de armazenamento

A comunicação entre um aplicativo cliente e uma conta de armazenamento do Azure é criptografada usando o protocolo TLS. O TLS é um protocolo criptográfico padrão que garante a privacidade e a integridade dos dados entre clientes e serviços pela Internet. Para obter mais informações sobre o TLS, consulte [segurança da camada de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Atualmente, o armazenamento do Azure dá suporte a três versões do protocolo TLS: 1,0, 1,1 e 1,2. O TLS 1,2 é a versão mais segura do TLS. O armazenamento do Azure usa o TLS 1,2 em pontos de extremidade HTTPs públicos, mas o TLS 1,0 e o TLS 1,1 ainda têm suporte para compatibilidade com versões anteriores.

Por padrão, as contas de armazenamento do Azure permitem que os clientes enviem e recebam dados com a versão mais antiga do TLS, TLS 1,0 e superior. Para impor medidas de segurança mais estritas, você pode configurar sua conta de armazenamento para exigir que os clientes enviem e recebam dados com uma versão mais recente do TLS. Se uma conta de armazenamento exigir uma versão mínima do TLS, todas as solicitações feitas com uma versão anterior falharão.

Este artigo descreve como configurar uma conta de armazenamento para exigir que os clientes enviem solicitações com uma versão mínima do TLS. Para obter informações sobre como especificar uma versão específica do TLS ao enviar uma solicitação de um aplicativo cliente, consulte [configurar TLS (segurança de camada de transporte) para um aplicativo cliente](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Detectar a versão do TLS usada por aplicativos cliente

Ao impor uma versão mínima do TLS para sua conta de armazenamento, você corre o risco de rejeitar solicitações de clientes que estão enviando dados com uma versão anterior do TLS. Para entender como a configuração da versão mínima do TLS pode afetar os aplicativos cliente, a Microsoft recomenda que você habilite o registro em log para sua conta de armazenamento do Azure e analise os logs após um intervalo de tempo para determinar quais versões dos aplicativos cliente TLS estão usando.

Para registrar solicitações em sua conta de armazenamento do Azure e determinar a versão do TLS usada pelo cliente, você pode usar o log de armazenamento do Azure no Azure Monitor (visualização). Para obter mais informações, consulte [monitorar o armazenamento do Azure](monitor-storage.md).

O log de armazenamento do Azure no Azure Monitor dá suporte ao uso de consultas de log para analisar dados de log. Para consultar logs, você pode usar um espaço de trabalho de Log Analytics do Azure. Para saber mais sobre consultas de log, consulte [tutorial: introdução às consultas de log Analytics](../../azure-monitor/log-query/get-started-portal.md).

Para registrar dados do armazenamento do Azure com Azure Monitor e analisá-los com o Azure Log Analytics, você deve primeiro criar uma configuração de diagnóstico que indica quais tipos de solicitações e para quais serviços de armazenamento você deseja registrar dados. Para criar uma configuração de diagnóstico no portal do Azure, siga estas etapas:

1. Registre-se no [log de armazenamento do Azure na versão prévia do Azure monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Crie um novo espaço de trabalho Log Analytics na assinatura que contém sua conta de armazenamento do Azure. Depois de configurar o log para sua conta de armazenamento, os logs estarão disponíveis no espaço de trabalho Log Analytics. Para obter mais informações, confira [Criar um workspace do Log Analytics no portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Navegue até sua conta de armazenamento no portal do Azure.
1. Na seção monitoramento, selecione **configurações de diagnóstico (versão prévia)**.
1. Selecione o serviço de armazenamento do Azure para o qual você deseja registrar solicitações. Por exemplo, escolha **blob** para registrar solicitações ao armazenamento de BLOBs.
1. Selecione **Adicionar configuração de diagnóstico**.
1. Forneça um nome para a configuração de diagnóstico.
1. Em **detalhes da categoria**, na seção **log** , escolha quais tipos de solicitações registrar em log. Você pode registrar em log as solicitações de leitura, gravação e exclusão. Por exemplo, escolher **StorageRead** e **StorageWrite** registrará em log as solicitações de leitura e gravação para o serviço selecionado.
1. Em **detalhes de destino**, selecione **Enviar para log Analytics**. Selecione sua assinatura e o espaço de trabalho Log Analytics criado anteriormente, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Captura de tela mostrando como criar uma configuração de diagnóstico para solicitações de log":::

Depois de criar a configuração de diagnóstico, as solicitações para a conta de armazenamento são registradas subsequentemente de acordo com essa configuração. Para obter mais informações, consulte [criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](../../azure-monitor/platform/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos logs de armazenamento do Azure no Azure Monitor, consulte [logs de recursos (versão prévia)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Consultar solicitações registradas por versão de TLS

Os logs de armazenamento do Azure no Azure Monitor incluem a versão de TLS usada para enviar uma solicitação para uma conta de armazenamento. Use a propriedade **TlsVersion** para verificar a versão de TLS de uma solicitação registrada.

Para recuperar os logs dos últimos 7 dias e determinar quantas solicitações foram feitas no armazenamento de BLOBs com cada versão do TLS, abra seu espaço de trabalho do Log Analytics. Em seguida, Cole a consulta a seguir em uma nova consulta de log e execute-a. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Os resultados mostram a contagem do número de solicitações feitas com cada versão do TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Captura de tela mostrando os resultados da consulta do log Analytics para retornar a versão do TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Consultar solicitações registradas por endereço IP do chamador e cabeçalho do agente do usuário

Os logs de armazenamento do Azure no Azure Monitor também incluem o endereço IP do chamador e o cabeçalho do agente do usuário para ajudá-lo a avaliar quais aplicativos cliente acessaram a conta de armazenamento. Você pode analisar esses valores para decidir se os aplicativos cliente devem ser atualizados para usar uma versão mais recente do TLS ou se é aceitável falha na solicitação do cliente se ele não for enviado com a versão mínima do TLS.

Para recuperar os logs dos últimos 7 dias e determinar quais clientes fizeram solicitações com uma versão do TLS antes do TLS 1,2, Cole a consulta a seguir em uma nova consulta de log e execute-a. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>Configurar a versão mínima do TLS para uma conta

Para configurar a versão mínima do TLS para uma conta de armazenamento, use o portal do Azure ou CLI do Azure para definir a versão **minimumTlsVersion** para a conta. Essa propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implantação Azure Resource Manager. Para obter mais informações, consulte [visão geral da conta de armazenamento](storage-account-overview.md).

# <a name="portal"></a>[Portal](#tab/portal)

Para configurar a versão mínima do TLS para uma conta de armazenamento com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Selecione a definição de **configuração** .
1. Em **versão mínima do TLS**, use a lista suspensa para selecionar a versão mínima do TLS necessária para acessar os dados nessa conta de armazenamento, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Captura de tela mostrando como configurar a versão mínima do TLS no portal do Azure":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a versão mínima do TLS para uma conta de armazenamento com CLI do Azure, primeiro obtenha a ID do recurso para sua conta de armazenamento chamando o comando [AZ Resource show](/cli/azure/resource#az-resource-show) . Em seguida, chame o comando [AZ Resource Update](/cli/azure/resource#az-resource-update) para definir a propriedade **minimumTlsVersion** para a conta de armazenamento. Os valores válidos para **minimumTlsVersion** são `TLS1_0` `TLS1_1` e `TLS1_2` .

O exemplo a seguir define a versão mínima do TLS como 1,2. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> Depois de atualizar a versão mínima do TLS para a conta de armazenamento, pode levar até 30 segundos antes que a alteração seja totalmente propagada.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>Verificar a versão mínima necessária do TLS para uma conta

Para determinar a versão mínima necessária do TLS configurada para uma conta de armazenamento, verifique a propriedade Azure Resource Manager **minimumTlsVersion** . Para verificar essa propriedade para um número grande de contas de armazenamento de uma só vez, use o Gerenciador de grafo de recursos do Azure.

A propriedade **minimumTlsVersion** não é definida por padrão e não retorna um valor até que você a defina explicitamente. A conta de armazenamento assume como padrão a permissão de solicitações enviadas com o TLS versão 1,0 ou superior se o valor da propriedade for nulo.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Verificar a versão mínima necessária do TLS para uma única conta de armazenamento

Para verificar a versão mínima necessária do TLS para uma única conta de armazenamento usando CLI do Azure, chame o comando [AZ Resource show](/cli/azure/resource#az-resource-show) e consulte a propriedade **minimumTlsVersion** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Verificar a versão mínima necessária do TLS para um conjunto de contas de armazenamento

Para verificar a versão mínima necessária do TLS em um conjunto de contas de armazenamento com o desempenho ideal, você pode usar o Gerenciador de grafo de recursos do Azure no portal do Azure. Para saber mais sobre como usar o Gerenciador de grafo de recursos, consulte [início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](/azure/governance/resource-graph/first-query-portal).

A execução da consulta a seguir no Gerenciador de grafo de recursos retorna uma lista de contas de armazenamento e exibe a versão mínima do TLS para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>Testar a versão mínima do TLS de um cliente

Para testar se a versão mínima necessária do TLS para uma conta de armazenamento proíbe chamadas feitas com uma versão anterior, você pode configurar um cliente para usar uma versão anterior do TLS. Para obter mais informações sobre como configurar um cliente para usar uma versão específica do TLS, consulte [configurar TLS (segurança da camada de transporte) para um aplicativo cliente](transport-layer-security-configure-client-version.md).

Quando um cliente acessa uma conta de armazenamento usando uma versão de TLS que não atende à versão mínima do TLS configurada para a conta, o armazenamento do Azure retorna o código de erro 400 erro (solicitação incorreta) e uma mensagem indicando que a versão do TLS usada não é permitida para fazer solicitações nessa conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Configurar o protocolo TLS para um aplicativo cliente](transport-layer-security-configure-client-version.md)
- [Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
