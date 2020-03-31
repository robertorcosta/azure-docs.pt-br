---
title: Análise de log de diagnósticos do Windows Virtual Desktop - Azure
description: Como usar o log analytics com o recurso de diagnóstico supérdia do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127937"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Use o Log Analytics para o recurso de diagnóstico

O Windows Virtual Desktop oferece um recurso de diagnóstico que permite ao administrador identificar problemas através de uma única interface. Esse recurso registra informações de diagnóstico sempre que alguém atribuído à função Windows Virtual Desktop usa o serviço. Cada registro contém informações sobre qual função do Windows Virtual Desktop estava envolvida na atividade, quaisquer mensagens de erro que apareçam durante a sessão, informações do inquilino e informações do usuário. O recurso de diagnóstico cria registros de atividades para ações administrativas e de usuário. Cada registro de atividades se enquadra em três categorias principais: 

- Atividades de assinatura de feed: quando um usuário tenta se conectar ao seu feed através de aplicativos de desktop remoto da Microsoft.
- Atividades de conexão: quando um usuário tenta se conectar a uma área de trabalho ou RemoteApp através de aplicativos de desktop remoto da Microsoft.
- Atividades de gerenciamento: quando um administrador executa operações de gerenciamento no sistema, como criar pools de host, atribuir usuários a grupos de aplicativos e criar atribuições de funções.

As conexões que não alcançam o Windows Virtual Desktop não aparecerão nos resultados dos diagnósticos porque o serviço de função de diagnóstico em si faz parte do Windows Virtual Desktop. Problemas de conexão do Windows Virtual Desktop podem acontecer quando o usuário está enfrentando problemas de conectividade de rede.

## <a name="why-you-should-use-log-analytics"></a>Por que você deve usar o Log Analytics

Recomendamos que você use o Log Analytics para analisar dados de diagnóstico no cliente Azure que vão além da solução de problemas de um único usuário. Como você pode puxar os contadores de desempenho da VM para o Log Analytics, você tem uma ferramenta para coletar informações para sua implantação.

## <a name="before-you-get-started"></a>Antes de começar

Antes de usar o Log Analytics com o recurso de diagnóstico, você precisará [criar um espaço de trabalho](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace).

Depois de criar seu espaço de trabalho, siga as instruções em [Conectar computadores Windows ao Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) para obter as seguintes informações: 

- O ID do espaço de trabalho
- A chave principal do seu espaço de trabalho

Você precisará dessas informações mais tarde no processo de configuração.

## <a name="push-diagnostics-data-to-your-workspace"></a>Empurre os dados de diagnóstico para o seu espaço de trabalho 

Você pode empurrar os dados de diagnóstico do seu inquilino do Windows Virtual Desktop para o Log Analytics para o seu espaço de trabalho. Você pode configurar esse recurso imediatamente quando criar seu inquilino pela primeira vez ligando seu espaço de trabalho ao seu inquilino, ou você pode configurá-lo mais tarde com um inquilino existente.

Para vincular seu inquilino ao seu espaço de trabalho do Log Analytics enquanto você está configurando seu novo inquilino, execute o cmdlet a seguir para fazer login no Windows Virtual Desktop com sua conta de usuário TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Se você vai vincular um inquilino existente em vez de um novo inquilino, execute este cmdlet em vez disso: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Você precisará executar esses cmdlets para cada inquilino que deseja vincular ao Log Analytics. 

>[!NOTE]
>Se você não quiser vincular o espaço de trabalho do Log `New-RdsTenant` Analytics ao criar um inquilino, execute o cmdlet em vez disso. 

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para envio de eventos diagnósticos

Os eventos de diagnóstico são enviados ao Log Analytics quando concluídos.  

## <a name="example-queries"></a>Consultas de exemplo

As seguintes consultas de exemplo mostram como o recurso de diagnóstico gera um relatório para as atividades mais frequentes em seu sistema:

Este primeiro exemplo mostra as atividades de conexão iniciadas pelos usuários com clientes de desktop remoto suportados:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

Esta próxima consulta de exemplo mostra atividades de gerenciamento por administradores em inquilinos:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Pare de enviar dados para o Log Analytics 

Para parar de enviar dados de um inquilino existente para o Log Analytics, execute o cmdlet a seguir e defina strings vazias:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Você precisará executar este cmdlet para cada inquilino que você deseja parar de enviar dados. 

## <a name="next-steps"></a>Próximas etapas 

Para rever cenários de erro comuns que o recurso de diagnóstico pode identificar para você, consulte [Identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).
