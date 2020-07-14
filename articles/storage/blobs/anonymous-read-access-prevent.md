---
title: Impedir acesso de leitura público anônimo a contêineres e blobs
titleSuffix: Azure Storage
description: ''
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 90d7cd65bbc07524391f34fe0efce2b044664cef
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209231"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Impedir acesso de leitura público anônimo a contêineres e blobs

O acesso de leitura público anônimo a contêineres e blobs no armazenamento do Azure é uma maneira conveniente de compartilhar dados, mas também pode representar um risco de segurança. É importante habilitar o acesso anônimo criteriosamente e entender como avaliar o acesso anônimo aos seus dados. A complexidade operacional, erro humano ou ataque mal-intencionado contra dados acessíveis publicamente pode resultar em violações de dados dispendiosas. A Microsoft recomenda que você habilite o acesso anônimo somente quando necessário para o cenário do aplicativo.

Por padrão, uma conta de armazenamento permite que um usuário com permissões apropriadas configure o acesso público a contêineres e blobs. Você pode desabilitar essa funcionalidade no nível da conta de armazenamento, de modo que os contêineres e blobs na conta não possam ser configurados para acesso público.

Este artigo descreve como analisar solicitações anônimas em uma conta de armazenamento e como impedir o acesso anônimo para toda a conta de armazenamento ou para um contêiner individual.

## <a name="detect-anonymous-requests-from-client-applications"></a>Detectar solicitações anônimas de aplicativos cliente

Quando você desabilita o acesso de leitura público para uma conta de armazenamento, você corre o risco de rejeitar solicitações para contêineres e blobs que estão atualmente configurados para acesso público. Desabilitar o acesso público para uma conta de armazenamento substitui as configurações de acesso público para todos os contêineres nessa conta de armazenamento. Quando o acesso público estiver desabilitado para a conta de armazenamento, qualquer solicitação anônima futura para essa conta falhará.

Para entender como a desabilitação do acesso público pode afetar os aplicativos cliente, a Microsoft recomenda que você habilite o registro em log e as métricas para essa conta e analise padrões de solicitações anônimas em um intervalo de tempo. Use as métricas para determinar o número de solicitações anônimas para a conta de armazenamento e use logs para determinar quais contêineres estão sendo acessados anonimamente.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Monitorar solicitações anônimas com Metrics Explorer

Para rastrear solicitações anônimas para uma conta de armazenamento, use o Azure Metrics Explorer no portal do Azure. Para obter mais informações sobre Metrics Explorer, consulte [introdução ao Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Siga estas etapas para criar uma métrica que rastreia solicitações anônimas:

1. Navegue até sua conta de armazenamento no portal do Azure. Na seção **monitoramento** , selecione **métricas**.
1. Selecione **Adicionar métrica**. Na caixa de diálogo **métrica** , especifique os seguintes valores:
    1. Deixe o campo de escopo definido como o nome da conta de armazenamento.
    1. Defina o **namespace de métrica** como *blob*. Essa métrica relatará solicitações apenas ao armazenamento de BLOBs.
    1. Defina o campo de **métrica** para *Transações*.
    1. Defina o campo de **agregação** como *sum*.

    A nova métrica exibirá a soma do número de transações no armazenamento de BLOBs em um determinado intervalo de tempo. A métrica resultante aparece conforme mostrado na imagem a seguir:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Captura de tela mostrando como configurar a métrica para somar transações de BLOB":::

1. Em seguida, selecione o botão **Adicionar filtro** para criar um filtro na métrica para solicitações anônimas.
1. Na caixa de diálogo **filtro** , especifique os seguintes valores:
    1. Defina o valor da **Propriedade** como *autenticação*.
    1. Defina o campo **operador** para o sinal de igual (=).
    1. Defina o campo **valores** como *anônimo*.
1. No canto superior direito, selecione o intervalo de tempo durante o qual você deseja exibir a métrica. Você também pode indicar como a agregação das solicitações deve ser granular, especificando intervalos em qualquer lugar, de 1 minuto a 1 mês.

Depois de configurar a métrica, as solicitações anônimas começarão a aparecer no grafo. A imagem a seguir mostra solicitações anônimas agregadas nos últimos trinta minutos.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Captura de tela mostrando solicitações anônimas agregadas no armazenamento de BLOBs":::

Você também pode configurar uma regra de alerta para notificá-lo quando um determinado número de solicitações anônimas for feito em sua conta de armazenamento. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analisar logs para identificar contêineres que recebem solicitações anônimas

Os logs de armazenamento do Azure capturam detalhes sobre as solicitações feitas na conta de armazenamento, incluindo como uma solicitação foi autorizada. Você pode analisar os logs para determinar quais contêineres estão recebendo solicitações anônimas.

Para registrar solicitações em log para sua conta de armazenamento do Azure para avaliar solicitações anônimas, você pode usar o log de armazenamento do Azure no Azure Monitor (versão prévia). Para obter mais informações, consulte [monitorar o armazenamento do Azure](../common/monitor-storage.md).

O log de armazenamento do Azure no Azure Monitor dá suporte ao uso de consultas de log para analisar dados de log. Para consultar logs, você pode usar um espaço de trabalho de Log Analytics do Azure. Para saber mais sobre consultas de log, consulte [tutorial: introdução às consultas de log Analytics](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Criar uma configuração de diagnóstico no portal do Azure

Para registrar dados do armazenamento do Azure com Azure Monitor e analisá-los com o Azure Log Analytics, você deve primeiro criar uma configuração de diagnóstico que indica quais tipos de solicitações e para quais serviços de armazenamento você deseja registrar dados. Para criar uma configuração de diagnóstico no portal do Azure, siga estas etapas:

1. Registre-se no [log de armazenamento do Azure na versão prévia do Azure monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Crie um novo espaço de trabalho Log Analytics na assinatura que contém sua conta de armazenamento do Azure. Depois de configurar o log para sua conta de armazenamento, os logs estarão disponíveis no espaço de trabalho Log Analytics. Para obter mais informações, confira [Criar um workspace do Log Analytics no portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Navegue até sua conta de armazenamento no portal do Azure.
1. Na seção monitoramento, selecione **configurações de diagnóstico (versão prévia)**.
1. Selecione **blob** para registrar solicitações feitas no armazenamento de BLOBs.
1. Selecione **Adicionar configuração de diagnóstico**.
1. Forneça um nome para a configuração de diagnóstico.
1. Em **detalhes da categoria**, na seção **log** , escolha quais tipos de solicitações registrar em log. Todas as solicitações anônimas serão solicitações de leitura, portanto, selecione **StorageRead** para capturar solicitações anônimas.
1. Em **detalhes de destino**, selecione **Enviar para log Analytics**. Selecione sua assinatura e o espaço de trabalho Log Analytics criado anteriormente, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Captura de tela mostrando como criar uma configuração de diagnóstico para solicitações de log":::

Depois de criar a configuração de diagnóstico, as solicitações para a conta de armazenamento são registradas subsequentemente de acordo com essa configuração. Para obter mais informações, consulte [criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](../../azure-monitor/platform/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos logs de armazenamento do Azure no Azure Monitor, consulte [logs de recursos (versão prévia)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Logs de consulta para solicitações anônimas

Os logs de armazenamento do Azure no Azure Monitor incluem o tipo de autorização que foi usado para fazer uma solicitação para uma conta de armazenamento. Em sua consulta de log, filtre a propriedade **AuthenticationType** para exibir solicitações anônimas.

Para recuperar os logs dos últimos 7 dias para solicitações anônimas no armazenamento de BLOBs, abra seu espaço de trabalho Log Analytics. Em seguida, Cole a consulta a seguir em uma nova consulta de log e execute-a. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Você também pode configurar uma regra de alerta com base nessa consulta para notificá-lo sobre solicitações anônimas. Para obter mais informações, consulte [criar, exibir e gerenciar alertas de log usando Azure monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Corrigir acesso público anônimo

Depois de avaliar as solicitações anônimas para contêineres e blobs em sua conta de armazenamento, você pode tomar medidas para limitar ou impedir o acesso público. Se alguns contêineres em sua conta de armazenamento puderem estar disponíveis para acesso público, você poderá configurar a configuração de acesso público para cada contêiner em sua conta de armazenamento. Essa opção fornece o controle mais granular sobre o acesso público. Para obter mais informações, consulte [definir o nível de acesso público para um contêiner](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

Para aumentar a segurança, você pode desabilitar o acesso público para uma conta de armazenamento inteira. A configuração de acesso público para uma conta de armazenamento substitui as configurações individuais dos contêineres nessa conta. Quando você desabilita o acesso público para uma conta de armazenamento, os contêineres configurados para permitir acesso público não são mais acessíveis anonimamente. Para obter mais informações, consulte [habilitar ou desabilitar o acesso de leitura público para uma conta de armazenamento](anonymous-read-access-configure.md#enable-or-disable-public-read-access-for-a-storage-account).

Se seu cenário exigir que determinados contêineres estejam disponíveis para acesso público, pode ser aconselhável mover esses contêineres e seus BLOBs para contas de armazenamento que são reservadas para acesso público. Em seguida, você pode desabilitar o acesso público para qualquer outra conta de armazenamento.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Verifique se o acesso público a um BLOB não é permitido

Para verificar se o acesso público a um blob específico foi negado, você pode tentar baixar o blob por meio de sua URL. Se o download for executado com sucesso, o BLOB ainda estará disponível publicamente. Se o BLOB não estiver publicamente acessível porque o acesso público foi desabilitado para a conta de armazenamento, você verá uma mensagem de erro indicando que o acesso público não é permitido nessa conta de armazenamento.

O exemplo a seguir mostra como usar o PowerShell para tentar baixar um blob por meio de sua URL. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Verifique se a modificação da configuração de acesso público do contêiner não é permitida

Para verificar se a configuração de acesso público de um contêiner não pode ser modificada após o acesso público ser desabilitado para a conta de armazenamento, você pode tentar modificar a configuração. A alteração da configuração de acesso público do contêiner falhará se o acesso público estiver desabilitado para a conta de armazenamento.

O exemplo a seguir mostra como usar o PowerShell para tentar alterar a configuração de acesso público de um contêiner. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Verifique se a criação de um contêiner com acesso público habilitado não é permitida

Se o acesso público for desabilitado para a conta de armazenamento, você não poderá criar um novo contêiner com acesso público habilitado. Para verificar, você pode tentar criar um contêiner com acesso público habilitado.

O exemplo a seguir mostra como usar o PowerShell para tentar criar um contêiner com acesso público habilitado. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:
 
```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

## <a name="next-steps"></a>Próximas etapas

- [Configurar acesso de leitura público anônimo para contêineres e blobs](anonymous-read-access-configure.md)
- [Acesse contêineres públicos e blobs anonimamente com o .NET](anonymous-read-access-client.md)