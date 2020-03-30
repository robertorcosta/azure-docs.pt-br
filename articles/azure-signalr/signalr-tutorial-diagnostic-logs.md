---
title: Logs de diagnóstico para o serviço Azure SignalR
description: Saiba como configurar registros de diagnóstico para o Azure SignalR Service e como utilizá-lo para solucionar problemas.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536727"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Logs de diagnóstico para o serviço Azure SignalR

Este tutorial abrange o que são registros de diagnóstico para o Azure SignalR Service e como configurar registros de diagnóstico e como solucionar problemas com registros de diagnóstico.

## <a name="prerequisites"></a>Pré-requisitos
Para habilitar registros de diagnóstico, você precisará de um lugar para armazenar seus dados de registro. Este tutorial usa o Azure Storage e o Log Analytics.

* [Armazenamento Azure](../azure-monitor/platform/resource-logs-collect-storage.md) - Retém registros de diagnóstico para auditoria de políticas, análise estática ou backup.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) - Uma ferramenta flexível de pesquisa e análise de logs que permite a análise de logs brutos gerados por um recurso do Azure.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Configure registros de diagnóstico para um serviço Azure SignalR

Você pode visualizar registros de diagnóstico para o Azure SignalR Service. Esses logs fornecem uma visão mais rica da conectividade à instância do Azure SignalR Service. Os registros diagnósticos fornecem informações detalhadas de cada conexão. Por exemplo, informações básicas (ID do usuário, ID de conexão e tipo de transporte e assim por diante) e informações de eventos (conectar, desconectar e abortar evento e assim por diante) da conexão. Os registros diagnósticos podem ser usados para identificação de problemas, rastreamento de conexão e análise.

### <a name="enable-diagnostic-logs"></a>Habilitar logs de diagnóstico

Os logs de diagnóstico estão desabilitados por padrão. Para habilitar logs de diagnóstico, siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)em **Monitoramento,** clique **em Configurações de Diagnóstico**.

    ![Navegação de painel para configurações de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Em seguida, clique **em Adicionar configuração de diagnóstico**.

    ![Adicionar registros de diagnóstico](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Defina o alvo de arquivo que você deseja. Atualmente, fazemos suporte **ao Archive para uma conta de armazenamento** e **enviamos para o Log Analytics.**

1. Selecione os registros que deseja arquivar.

    ![Painel Configurações de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Salve as novas configurações de diagnóstico.

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, no painel **Logs de diagnóstico**.

Para saber mais sobre como configurar um diagnóstico, confira a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Categorias dos logs de diagnóstico

O Azure SignalR Service captura registros de diagnóstico em uma categoria:

* **Todos os logs**: Rastrear conexões que se conectam ao Serviço De Sinalização Azure. Os logs fornecem informações sobre a conexão/desconexão, autenticação e estrangulamento. Para obter mais informações, consulte a próxima seção.

### <a name="archive-to-a-storage-account"></a>Arquivar em uma conta de armazenamento

Os logs são armazenados na conta de armazenamento configurada no painel **logs do Diagnostics.** Um recipiente `insights-logs-alllogs` nomeado é criado automaticamente para armazenar registros de diagnóstico. Dentro do recipiente, os registros `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`são armazenados no arquivo . Basicamente, o caminho é `resource ID` `Date Time`combinado por e . Os arquivos de `hour`log são divididos por . Portanto, os minutos `m=00`sempre são .

Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito nas seções a seguir.

As seqüências JSON de registro de arquivamento incluem elementos listados nas tabelas a seguir:

**Formato**

Nome | Descrição
------- | -------
time | Tempo de evento de log
level | Nível de evento de log
resourceId | ID de recursos do seu Serviço De SignalR do Azure
local | Localização do seu Serviço De Sinalização Azure
category | Categoria do evento log
operationName | Nome da operação do evento
callerIpAddress | Endereço IP do seu servidor/cliente
properties | Propriedades detalhadas relacionadas a este evento de log. Para obter mais detalhes, consulte a tabela de propriedades abaixo

**Tabela de propriedades**

Nome | Descrição
------- | -------
type | Tipo do evento de log. Atualmente, fornecemos informações sobre conectividade ao Serviço Azure SignalR. O `ConnectivityLogs` único tipo está disponível
collection | Coleta do evento de log. Os valores `Connection` `Authorization` permitidos são: , e`Throttling`
ConnectionId | Identidade da conexão
Transporttype | Tipo de transporte da conexão. Os valores `Websockets` \| `ServerSentEvents` \| permitidos são:`LongPolling`
connectionType | Tipo da conexão. Os valores permitidos são: `Server` \| `Client`. `Server`: conexão do lado do servidor; `Client`: conexão do lado do cliente
userId | Identidade do usuário
message | Mensagem detalhada do evento de log

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log de arquivo morto:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Esquema de registros de arquivos para análise de log

Para visualizar registros de diagnóstico, siga estas etapas:

1. Clique `Logs` no log analytics de destino.

    ![Item do menu do Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Digite `SignalRServiceDiagnosticLogs` e selecione o intervalo de tempo para consultar registros de diagnóstico. Para consulta avançada, consulte [Iniciar com o Log Analytics no Azure Monitor](../azure-monitor/log-query/get-started-portal.md)

    ![Login de consulta no Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

As colunas de registro de arquivamento incluem elementos listados na tabela a seguir:

Nome | Descrição
------- | ------- 
TimeGenerated | Tempo de evento de log
Coleção | Coleta do evento de log. Os valores `Connection` `Authorization` permitidos são: , e`Throttling`
OperationName | Nome da operação do evento
Location | Localização do seu Serviço De Sinalização Azure
Nível | Nível de evento de log
CallerIpAddress | Endereço IP do seu servidor/cliente
Mensagem | Mensagem detalhada do evento de log
UserId | Identidade do usuário
ConnectionId | Identidade da conexão
ConnectionType | Tipo da conexão. Os valores permitidos são: `Server` \| `Client`. `Server`: conexão do lado do servidor; `Client`: conexão do lado do cliente
Transporttype | Tipo de transporte da conexão. Os valores `Websockets` \| `ServerSentEvents` \| permitidos são:`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Solução de problemas com registros de diagnóstico

Para solucionar problemas para o Azure SignalR Service, você pode habilitar logs laterais do servidor/cliente para capturar falhas. No momento, o Azure SignalR Service expõe registros de diagnóstico, você também pode habilitar logs para o lado do serviço.

Ao encontrar uma situação de crescimento ou queda inesperada de conexão, você pode aproveitar os registros de diagnóstico para solucionar problemas.

Problemas típicos são frequentemente sobre as mudanças inesperadas de quantidade das conexões, as conexões atingem os limites de conexão e a falha de autorização. Veja as próximas seções sobre como solucionar problemas.

#### <a name="unexpected-connection-number-changes"></a>Mudanças inesperadas no número de conexão

##### <a name="unexpected-connection-dropping"></a>Conexão inesperada caindo

Se você encontrar conexões inesperadas, primeiro habilitar logs nos lados do serviço, servidor e cliente.

Se uma conexão se desconectar, os registros de `ConnectionAborted` diagnóstico `ConnectionEnded` `operationName`registrarão esse evento de desconexão, você verá ou entrará .

A diferença `ConnectionAborted` `ConnectionEnded` entre `ConnectionEnded` e é que é uma desconexão esperada que é acionada pelo lado cliente ou servidor. Embora `ConnectionAborted` o é geralmente um evento de queda de `message`conexão inesperada, e a razão de abortar será fornecida em .

As razões de abortamento estão listadas na tabela a seguir:

Motivo | Descrição
------- | ------- 
Contagem de conexões atinge limite | A contagem de conexões atinge o limite do seu nível de preço atual. Considere aumentar a unidade de serviço
Servidor de aplicativos fechou a conexão | Servidor de aplicativos aciona o aborto. Pode ser considerado como um aborto esperado
Tempo de intervalo de ping de conexão | Geralmente é causado por problema de rede. Considere verificar a disponibilidade do seu servidor de aplicativo a partir da internet
Recarga de serviço, reconexão | O Azure SignalR Service está recarregando. O Azure SignalR suporta a reconexão automática, você pode esperar até que seja reconectado ou reconecte-se manualmente ao Azure SignalR Service
Erro transitório do servidor interno | Erro transitório ocorre no Azure SignalR Service, deve ser recuperado automaticamente
A conexão do servidor caiu | A conexão do servidor é retirada com erro desconhecido, considere a solução de problemas com o log lateral do serviço/servidor/cliente primeiro. Tente excluir problemas básicos (por exemplo, problema de rede, problema do lado do servidor do aplicativo e assim por diante). Se o problema não for resolvido, entre em contato conosco para obter mais ajuda. Para obter mais informações, consulte Obter seção [de ajuda.](#get-help) 

##### <a name="unexpected-connection-growing"></a>Conexão inesperada crescendo

Para solucionar problemas sobre o crescimento inesperado da conexão, a primeira coisa que você precisa fazer é filtrar as conexões extras. Você pode adicionar iD de usuário de teste exclusivo à sua conexão cliente de teste. Em seguida, verifique-o com registros de diagnóstico, se você ver que mais de uma conexão cliente tem o mesmo ID ou IP do usuário de teste, então é provável que o lado do cliente crie e estabeleça mais conexões do que a expectativa. Verifique o lado do seu cliente.

#### <a name="authorization-failure"></a>Falha de autorização

Se você receber 401 retornou não autorizado para solicitações de clientes, verifique seus registros de diagnóstico. Se você `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`encontrar, significa que todos os públicos em seu token de acesso são inválidos. Tente usar as audiências válidas sugeridas no registro.


#### <a name="throttling"></a>Limitação

Se você descobrir que não pode estabelecer conexões do cliente SignalR ao Azure SignalR Service, verifique seus registros de diagnóstico. Se você `Connection count reaches limit` encontrar no registro de diagnóstico, você estabelecerá muitas conexões ao SignalR Service, que atingem o limite de contagem de conexões. Considere aumentar seu Serviço SignalR. Se você `Message count reaches limit` encontrar no registro de diagnóstico, isso significa que você usa o nível livre e usa a cota de mensagens. Se você quiser enviar mais mensagens, considere alterar seu Serviço SignalR para o nível padrão para enviar mensagens adicionais. Para obter mais informações, consulte [os preços do serviço Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Obter ajuda

Recomendamos que você soque problemas sozinho primeiro. A maioria dos problemas são causados por problemas de servidor de aplicativos ou rede. Siga [o guia de solução de problemas com registro de diagnóstico](#troubleshooting-with-diagnostic-logs) e guia básico de solução de [problemas](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) para encontrar a causa raiz.
Se o problema ainda não puder ser resolvido, considere abrir um problema no GitHub ou criar um ticket no Portal Azure.
Forneça:
1. Intervalo de tempo de cerca de 30 minutos quando o problema ocorre
2. ID de recurso do Azure SignalR Service
3. Detalhes de problemas, o mais específico possível: Por exemplo, o appserver não envia mensagens, quedas de conexão do cliente e assim por diante
4. Logs coletados do lado do servidor/cliente e outros materiais que podem ser úteis
5. [Opcional] Código repro

> [!NOTE]
> Se você abrir problema no GitHub, mantenha suas informações confidenciais (por exemplo, ID de recurso, logs de servidor/cliente) privadas, enviadas apenas para membros da organização Microsoft em particular.