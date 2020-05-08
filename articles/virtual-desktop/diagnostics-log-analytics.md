---
title: Log Analytics de diagnóstico de área de trabalho virtual do Windows – Azure
description: Como usar o log Analytics com o recurso de diagnóstico de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a9e68a2e0c39364cc5105f230b00ffb90d867d
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888806"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Usar Log Analytics para o recurso de diagnóstico

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A área de trabalho virtual do Windows usa [Azure monitor](../azure-monitor/overview.md) para monitoramento e alertas como muitos outros serviços do Azure. Isso permite que os administradores identifiquem problemas por meio de uma única interface. O serviço cria logs de atividade para ações administrativas e de usuário. Cada log de atividades se enquadra nas seguintes categorias:  

- Atividades de gerenciamento:
    - Acompanhe se as tentativas de alterar objetos da área de trabalho virtual do Windows usando APIs ou PowerShell foram bem-sucedidas. Por exemplo, alguém pode criar um pool de hosts com êxito usando o PowerShell?
- Feed 
    - Os usuários podem assinar com êxito os espaços de trabalho? 
    - Os usuários veem todos os recursos publicados no cliente Área de Trabalho Remota?
- Conexões: 
    - Quando os usuários iniciam e concluem conexões com o serviço. 
- Registro do host: 
    - O host da sessão foi registrado com êxito com o serviço na conexão?
- Errões: 
    - Os usuários estão encontrando problemas com atividades específicas? Esse recurso pode gerar uma tabela que acompanha os dados da atividade, desde que as informações sejam unidas com as atividades.
- Pontos  
    - Etapas específicas no tempo de vida de uma atividade que foram atingidas. Por exemplo, durante uma sessão, um usuário tinha um balanceamento de carga para um host específico, o usuário estava conectado durante uma conexão e assim por diante.

As conexões que não chegam à área de trabalho virtual do Windows não aparecerão nos resultados do diagnóstico porque o próprio serviço de função de diagnóstico faz parte da área de trabalho virtual do Windows. Problemas de conexão de área de trabalho virtual do Windows podem ocorrer quando o usuário está enfrentando problemas de conectividade de rede.

Azure Monitor permite analisar os dados da área de trabalho virtual do Windows e examinar os contadores de desempenho de máquina virtual (VM), tudo na mesma ferramenta. Este artigo informará mais sobre como habilitar o diagnóstico para seu ambiente de área de trabalho virtual do Windows. 

>[!NOTE] 
>Para saber como monitorar suas VMs no Azure, consulte [monitorando máquinas virtuais do Azure com Azure monitor](../azure-monitor/insights/monitor-vm-azure.md). Além disso, lembre-se de [examinar os limites do contador de desempenho](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) para uma melhor compreensão da experiência do usuário no host da sessão.

## <a name="before-you-get-started"></a>Antes de começar

Antes de poder usar Log Analytics, você precisará criar um espaço de trabalho. Para fazer isso, siga as instruções em um dos dois artigos a seguir:

- Se preferir usar portal do Azure, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md).
- Se preferir o PowerShell, consulte [criar um log Analytics espaço de trabalho com o PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

Depois de criar seu espaço de trabalho, siga as instruções em [conectar computadores Windows a Azure monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) para obter as seguintes informações:

- A ID do espaço de trabalho
- A chave primária do seu espaço de trabalho

Você precisará dessas informações posteriormente no processo de instalação.

Certifique-se de examinar o gerenciamento de permissões para Azure Monitor habilitar o acesso a dados para aqueles que monitoram e mantêm seu ambiente de área de trabalho virtual do Windows. Para obter mais informações, consulte Introdução [às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md). 

## <a name="push-diagnostics-data-to-your-workspace"></a>Enviar dados de diagnóstico por push para seu espaço de trabalho

Você pode enviar por push dados de diagnóstico de seus objetos da área de trabalho virtual do Windows para o Log Analytics para seu espaço de trabalho. Você pode configurar esse recurso imediatamente ao criar seus objetos pela primeira vez.

Para configurar Log Analytics para um novo objeto:

1. Entre no portal do Azure e vá para a **área de trabalho virtual do Windows**. 

2. Navegue até o objeto (como um pool de hosts, grupo de aplicativos ou espaço de trabalho) para o qual você deseja capturar logs e eventos. 

3. Selecione **configurações de diagnóstico** no menu no lado esquerdo da tela. 

4. Selecione **Adicionar configuração de diagnóstico** no menu que aparece no lado direito da tela. 
   
    As opções mostradas na página Configurações de diagnóstico variam de acordo com o tipo de objeto que você está editando.

    Por exemplo, quando você estiver habilitando o diagnóstico para um grupo de aplicativos, verá opções para configurar pontos de verificação, erros e gerenciamento. Para espaços de trabalho, essas categorias configuram um feed para controlar quando os usuários assinam a lista de aplicativos. Para saber mais sobre as configurações de diagnóstico [, consulte Criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](../azure-monitor/platform/diagnostic-settings.md). 

     >[!IMPORTANT] 
     >Lembre-se de habilitar o diagnóstico para cada objeto de Azure Resource Manager que você deseja monitorar. Os dados estarão disponíveis para atividades após a habilitação do diagnóstico. Pode levar algumas horas após a primeira configuração.  

5. Insira um nome para a configuração de configurações e, em seguida, selecione **Enviar para log Analytics**. O nome que você usa não deve ter espaços e deve estar em conformidade com as [convenções de nomenclatura do Azure](../azure-resource-manager/management/resource-name-rules.md). Como parte dos logs, você pode selecionar todas as opções que deseja adicionar à sua Log Analytics, como ponto de verificação, erro, gerenciamento e assim por diante.

6. Clique em **Salvar**.

>[!NOTE]
>Log Analytics oferece a opção de transmitir dados para os [hubs de eventos](../event-hubs/event-hubs-about.md) ou arquivá-los em uma conta de armazenamento. Para saber mais sobre esse recurso, confira [transmitir dados de monitoramento do Azure para um hub de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) e [arquivar logs de recursos do Azure para a conta de armazenamento](../azure-monitor/platform/resource-logs-collect-storage.md). 

## <a name="how-to-access-log-analytics"></a>Como acessar Log Analytics

Você pode acessar espaços de trabalho do Log Analytics no portal do Azure ou Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics de acesso em um espaço de trabalho Log Analytics

1. Entre no portal do Azure.

2. Pesquise **log Analytics espaço de trabalho**. 

3. Em serviços, selecione **log Analytics espaços de trabalho**. 
   
4. Na lista, selecione o espaço de trabalho que você configurou para o seu objeto de área de trabalho virtual do Windows.

5. Uma vez no espaço de trabalho, selecione **logs**. Você pode filtrar a lista de menus com a função de **pesquisa** . 

### <a name="access-log-analytics-on-azure-monitor"></a>Log Analytics de acesso no Azure Monitor

1. Entrar no portal do Azure

2. Procure e selecione **Monitor**. 

3. Selecione **logs**.

4. Siga as instruções na página de registro para definir o escopo da consulta.  

5. Você está pronto para consultar o diagnóstico. Todas as tabelas de diagnóstico têm um prefixo "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para enviar eventos de diagnóstico

Os eventos de diagnóstico são enviados para Log Analytics quando concluídos.

Log Analytics apenas os relatórios nesses Estados intermediários para atividades de conexão:

- Started (iniciado)
- Conectado
- Concluído

## <a name="example-queries"></a>Consultas de exemplo

As consultas de exemplo a seguir mostram como o recurso de diagnóstico gera um relatório para as atividades mais frequentes em seu sistema.

Para obter uma lista de conexões feitas por seus usuários, execute este cmdlet:

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

Para exibir a atividade de feed de seus usuários:

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

Para localizar todas as conexões de um único usuário: 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

Para localizar o número de vezes que um usuário se conectou por dia:

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

Para localizar a duração da sessão por usuário:

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

Para localizar erros para um usuário específico:

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

Para descobrir se ocorreu um erro específico:

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Para localizar a ocorrência de um erro em todos os usuários:

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>A tabela mais importante para a solução de problemas é WVDErrors. Use essa consulta para entender quais problemas ocorrem para atividades do usuário, como conexões ou feeds, quando um usuário assina a lista de aplicativos ou áreas de trabalho. A tabela mostrará erros de gerenciamento, bem como problemas de registro de host.
>
>Durante a visualização pública, se precisar de ajuda para resolver um problema, certifique-se de fornecer a CorrelationId para o erro na sua solicitação de ajuda. Além disso, verifique se o valor de erro do serviço sempre diz Service-Error = "false". Um valor "false" significa que o problema pode ser resolvido por uma tarefa de administrador no seu fim. Se o erro = "verdadeiro", você precisará escalar o problema para a Microsoft.

## <a name="next-steps"></a>Próximas etapas 

Para examinar os cenários de erro comuns que o recurso de diagnóstico pode identificar para você, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).
