---
title: Monitoramento de métricas e logs brutos para a CDN do Azure da Microsoft
description: Este artigo descreve a CDN do Azure das métricas de monitoramento da Microsoft e dos logs brutos.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: c41bf8bc6e5aa3749786bc1189343dfdebdc1508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321094"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Monitoramento de métricas e logs brutos para a CDN do Azure da Microsoft
Com a CDN do Azure da Microsoft, você pode monitorar recursos das seguintes maneiras para ajudá-lo a solucionar problemas, rastrear e depurar questões. 

* Os logs brutos fornecem informações avançadas sobre cada solicitação que a CDN recebe. Os logs brutos são diferentes dos logs de atividades. Os logs de atividade fornecem visibilidade das operações realizadas nos recursos do Azure.
* Métricas, que exibem quatro métricas-chave na CDN, incluindo a taxa de acertos de bytes, a contagem de solicitações, o tamanho da resposta e a latência total. Ele também fornece dimensões diferentes para dividir as métricas.
* Alerta, que permite que o cliente configure o alerta para métricas-chave
* Métricas adicionais, que permitem que os clientes usem o Azure Log Analytics para habilitar métricas adicionais de valor. Também fornecemos exemplos de consulta para algumas outras métricas no Azure Log Analytics.

> [!IMPORTANT]
> O recurso de logs brutos HTTP está disponível para a CDN do Azure da Microsoft.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Configuração-portal do Azure

Para configurar logs brutos para seu perfil da CDN do Azure da Microsoft: 

1. No menu portal do Azure, selecione **todos os recursos**  >>  **\<your-CDN-profile>** .

2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.

3. Selecione **+ Adicionar configuração de diagnóstico**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::
    
    > [!IMPORTANT]
    > Os logs brutos só estão disponíveis no nível do perfil enquanto os logs de código de status http agregados estão disponíveis no nível do ponto de extremidade.

4. Em **Configurações de diagnóstico**, insira um nome em **Nome das configurações de diagnóstico**.

5. Selecione o **AzureCdnAccessLog** e defina a retenção em dias.

6. Selecione os **Detalhes do destino**. As opções de destino são:
    * **Enviar para o Log Analytics**
        * Selecione a **Assinatura** e o **Workspace do Log Analytics**.
    * **Arquivar em uma conta de armazenamento**
        * Selecione a **Assinatura** e a **Conta de Armazenamento**.
    * **Transmitir por streaming para um hub de eventos**
        * Selecione a **Assinatura**, **Namespace do hub de eventos**, **Nome do hub de eventos (opcional)** e **Nome de política do hub de eventos**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::

7. Selecione **Salvar**.

## <a name="configuration---azure-powershell"></a>Configuração-Azure PowerShell

Use [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) para definir a configuração de diagnóstico para logs brutos.

Os dados de retenção são definidos pela opção **-RetentionInDays** no comando.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Habilitar os logs de diagnóstico em uma conta de armazenamento

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Para habilitar os logs de diagnóstico em uma conta de armazenamento, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Habilitar logs de diagnóstico para Log Analytics espaço de trabalho

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para habilitar os logs de diagnóstico para um espaço de trabalho Log Analytics, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Habilitar logs de diagnóstico para o namespace do hub de eventos

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para habilitar os logs de diagnóstico para um namespace de Hub de eventos, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Propriedades de logs brutos

Atualmente, a CDN do Azure do serviço da Microsoft fornece logs brutos. Os logs brutos fornecem solicitações de API individuais com cada entrada com o seguinte esquema: 

| Propriedade              | Descrição                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | A cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pela Front Door, também enviada como o cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |
| HttpMethod            | Método HTTP usado pela solicitação.                                                                                                                                                                     |
| HttpVersion           | O tipo da conexão ou solicitação.                                                                                                                                                                   |
| RequestUri            | URI da solicitação recebida.                                                                                                                                                                         |
| RequestBytes          | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação.                                                                                                   |
| ResponseBytes         | Bytes enviados pelo servidor de back-end como a resposta.                                                                                                                                                    |
| UserAgent             | O tipo de navegador que o cliente usou.                                                                                                                                                               |
| ClientIp              | Endereço IP do cliente que fez a solicitação.                                                                                                                                                  |
| TimeTaken             | O período de tempo que a ação tomou em milissegundos.                                                                                                                                            |
| SecurityProtocol      | A versão do protocolo TLS/SSL usada pela solicitação ou NULL se não houver criptografia.                                                                                                                           |
| Ponto de extremidade              | O host do ponto de extremidade da CDN foi configurado no perfil da CDN pai.                                                                                                                                   |
| Nome do host de back-end     | O nome do host de back-end ou da origem em que as solicitações estão sendo enviadas.                                                                                                                                |
| Enviado à blindagem de origem </br> (preterido) * **consulte a observação sobre a reprovação abaixo.** | Se for verdadeiro, significa que a solicitação foi respondida do cache da blindagem de origem em vez do pop de borda. A blindagem de origem é um cache pai usado para melhorar a taxa de acertos do cache.                                       |
| isReceivedFromClient | Se for true, significa que a solicitação veio do cliente. Se for false, a solicitação será um erro na borda (POP filho) e será respondida do escudo de origem (POP pai). 
| HttpStatusCode        | O código de status HTTP retornado do proxy.                                                                                                                                                        |
| HttpStatusDetails     | Status resultante na solicitação. O significado desse valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de status.                                                                                              |
| Pop                   | O pop de borda, que respondeu à solicitação do usuário. As abreviações dos POPs são códigos de aeroporto de seus respectivos metros.                                                                                   |
| Status do cache          | Significa que o objeto foi retornado do cache ou veio da origem.                                                                                                             |
> [!NOTE]
> Os logs podem ser exibidos em seu perfil de Log Analytics executando uma consulta. Uma consulta de exemplo teria A seguinte aparência:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Enviado à substituição da blindagem de origem
A propriedade de log bruto **isSentToOriginShield** foi preterida e substituída por um novo campo **isReceivedFromClient**. Use o novo campo se você já estiver usando o campo preterido. 

Os logs brutos incluem logs gerados a partir da borda da CDN (POP filho) e da blindagem de origem. A blindagem de origem se refere a nós pai que estão estrategicamente localizados em todo o mundo. Esses nós se comunicam com servidores de origem e reduzem a carga de tráfego na origem. 

Para cada solicitação que vai para a blindagem de origem, há duas entradas de log:

* Um para nós de borda
* Uma para a blindagem de origem. 

Para diferenciar a saída ou as respostas dos nós de borda versus a blindagem de origem, você pode usar o campo **isReceivedFromClient** para obter os dados corretos. 

Se o valor for false, isso significa que a solicitação é respondida da blindagem de origem para nós de borda. Essa abordagem é eficaz para comparar logs brutos com dados de cobrança. Os encargos não são incorridos para a saída da blindagem de origem para os nós de borda. Os encargos são incorridos para a saída dos nós de borda para os clientes. 

**Exemplo de consulta Kusto para excluir logs gerados no escudo de origem no Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> O recurso de logs brutos HTTP está disponível automaticamente para todos os perfis criados ou atualizados após **25 de fevereiro de 2020**. Para perfis da CDN criados anteriormente, é necessário atualizar o ponto de extremidade da CDN depois de configurar o registro em log. Por exemplo, é possível navegar para a filtragem geográfica em pontos de extremidade da CDN e bloquear qualquer país/região que não seja relevante para sua carga de trabalho e clicar em salvar.


## <a name="metrics"></a>Métricas
A CDN do Azure da Microsoft é integrada com Azure Monitor e publica quatro métricas de CDN para ajudar a controlar, solucionar problemas e depurar problemas. 

As métricas são exibidas em gráficos e acessíveis por meio do PowerShell, da CLI e da API. As métricas da CDN são gratuitas.

A CDN do Azure da Microsoft mede e envia suas métricas em intervalos de 60 segundos. As métricas podem levar até 3 minutos para aparecer no Portal. 

Para obter mais informações, consulte [Azure monitor métricas](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

**Métricas com suporte da CDN do Azure da Microsoft**

| Métricas         | Descrição                                                                                                      | Dimensão                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Taxa de acertos de bytes * | A porcentagem de egresso do cache da CDN, computada com relação à saída total.                                      | Ponto de extremidade                                                                                    |
| RequestCount    | O número de solicitações de cliente atendidas pela CDN.                                                                     | Ponto de extremidade </br> País do cliente. </br> Região do cliente. </br> Código de status HTTP. </br> Grupo de status HTTP. |
| ResponseSize    | O número de bytes enviados como respostas da borda CDN para os clientes.                                                  |Ponto de extremidade </br> País do cliente. </br> Região do cliente. </br> Código de status HTTP. </br> Grupo de status HTTP.                                                                                          |
| TotalLatency    | O tempo total da solicitação do cliente recebida pela CDN **até que o último byte de resposta seja enviado da CDN para o cliente**. |Ponto de extremidade </br> País do cliente. </br> Região do cliente. </br> Código de status HTTP. </br> Grupo de status HTTP.                                                                                             |

***Bytes atingido ração = (saída da borda-saída da origem)/egress do Edge**

Cenários excluídos no cálculo da taxa de acertos de bytes:

* Você não configura explicitamente nenhum cache por meio do mecanismo de regras ou do comportamento de cache de cadeia de caracteres de consulta.
* Você configura explicitamente a diretiva de controle de cache com cache não armazenado ou privado.

### <a name="metrics-configuration"></a>Configuração de métricas

1. No menu portal do Azure, selecione **todos os recursos**  >>  **\<your-CDN-profile>** .

2. Em **monitoramento**, selecione **métricas**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::

3. Selecione **Adicionar métrica**, selecione a métrica a ser adicionada:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::

4. Selecione **Adicionar filtro** para adicionar um filtro:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::

5. Selecione **aplicar** divisão para ver a tendência por dimensões diferentes:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::

6. Selecione **novo gráfico** para adicionar um novo gráfico:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::

### <a name="alerts"></a>Alertas

Você pode configurar alertas na CDN da Microsoft selecionando alertas de **monitoramento**  >>  **Alerts**.

Selecione **nova regra de alerta** para as métricas listadas na seção métricas:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::

O alerta será cobrado com base em Azure Monitor. Para obter mais informações sobre alertas, consulte [Azure monitor alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

### <a name="additional-metrics"></a>Métricas adicionais
Você pode habilitar métricas adicionais usando o Azure Log Analytics e logs brutos para obter um custo adicional.

1. Siga as etapas acima em habilitando o diagnóstico para enviar o log bruto para o log Analytics.

2. Selecione o espaço de trabalho Log Analytics que você criou:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::   

3. Selecione **logs** em **geral** no espaço de trabalho do log Analytics.  Em seguida, selecione **introdução**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::   
 
4. Selecione **perfis CDN**.  Selecione um exemplo de consulta para executar ou fechar a tela de exemplo para inserir uma consulta personalizada:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::   

4. Para exibir dados por gráfico, selecione **gráfico**.  Selecione **fixar no painel** para fixar o gráfico no painel do Azure:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true"::: 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você habilitou os logs brutos HTTP para o serviço de CDN da Microsoft.

Para obter mais informações sobre a CDN do Azure e os outros serviços do Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) os padrões de uso da CDN do Azure.

* Saiba mais sobre o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configurar o [Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
