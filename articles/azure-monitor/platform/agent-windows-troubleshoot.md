---
title: Problemas de solução de problemas com o agente Log Analytics para Windows
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com o agente Log Analytics para Windows no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333514"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Como solucionar problemas com o agente do Log Analytics para Windows 

Este artigo fornece ajuda para solucionar erros que você pode experimentar com o agente Log Analytics para Windows no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhuma dessas etapas funcionar para você, os seguintes canais de suporte também estarão disponíveis:

* Os clientes com benefícios de suporte Premier podem abrir uma solicitação de suporte com [Premier](https://premier.microsoft.com/).
* Os clientes com contratos de suporte do Azure podem abrir uma solicitação de suporte [no portal do Azure](https://manage.windowsazure.com/?getsupport=true).
* Visite a página De feedback do Log [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) Analytics para revisar ideias e bugs enviados ou arquivar uma nova. 

## <a name="important-troubleshooting-sources"></a>Fontes importantes para solução de problemas

 Para ajudar na solução de problemas relacionados ao agente Log Analytics para Windows, o agente registra eventos no Registro de Eventos do Windows, especificamente no *Gerenciador de Operações de Aplicativos e Serviços\.*  

## <a name="connectivity-issues"></a>Problemas de conectividade

Se o agente estiver se comunicando através de um servidor proxy ou firewall, pode haver restrições no local impedindo a comunicação do computador de origem e do serviço Azure Monitor. Caso a comunicação seja bloqueada, devido à configuração errada, o registro com um espaço de trabalho pode falhar ao tentar instalar o agente ou configurar o agente pós-configuração para relatar a um espaço de trabalho adicional. A comunicação do agente pode falhar após o registro bem sucedido. Esta seção descreve os métodos para solucionar esse tipo de problema com o agente do Windows.

Verifique duas vezes se o firewall ou proxy está configurado para permitir as seguintes portas e URLs descritas na tabela a seguir. Confirme também que a inspeção HTTP não está habilitada para tráfego web, pois pode impedir um canal TLS seguro entre o agente e o Monitor Azure.  

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |  

Para obter informações de firewall necessárias para o Governo Do Azure, consulte [a gestão do governo do Azure](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). Se você planeja usar o Azure Automation Hybrid Runbook Worker para se conectar e se registrar no serviço de Automação para usar runbooks ou soluções de gerenciamento em seu ambiente, ele deve ter acesso ao número da porta e às URLs descritas no [Configure sua rede para o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Existem várias maneiras de verificar se o agente está se comunicando com sucesso com o Azure Monitor.

- Habilite a [avaliação do Azure Log Analytics Agent Health](../insights/solution-agenthealth.md) no espaço de trabalho. No painel do Agent Health, consulte a coluna **Contagem de agentes sem resposta** para ver rapidamente se o agente está listado.  

- Execute a seguinte consulta para confirmar se o agente está enviando um batimento cardíaco para o espaço de trabalho ao qual está configurado para relatar. Substitua pelo `<ComputerName>` nome real da máquina.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se o computador estiver se comunicando com sucesso com o serviço, a consulta deve retornar um resultado. Se a consulta não retornou um resultado, primeiro verifique se o agente está configurado para relatar ao espaço de trabalho correto. Se estiver configurado corretamente, siga para a etapa 3 e pesquise no Registro de Eventos do Windows para identificar se o agente está registrando qual problema pode estar impedindo-o de se comunicar com o Azure Monitor.

- Outro método para identificar um problema de conectividade é executando a ferramenta **TestCloudConnectivity.** A ferramenta é instalada por padrão com o agente na pasta *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. A partir de um prompt de comando elevado, navegue até a pasta e execute a ferramenta. A ferramenta retorna os resultados e destaca onde o teste falhou (por exemplo, se estava relacionado a uma determinada porta/URL que estava bloqueada). 

    ![Resultados da execução da ferramenta TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrar o registro de eventos *do Gerente de Operações* por **fontes** - de eventos*Módulos de Serviço de Saúde,* *HealthService*e *Conector de Serviço* e filtrar por **aviso** *e* erro de nível de *evento* para confirmar se ele escreveu eventos na tabela a seguir. Se forem, revise as etapas de resolução incluídas para cada evento possível.

    |ID do evento |Fonte |Descrição |Resolução |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Serviço de Integridade |A conexão com o serviço do agente falhou |Esse erro pode ocorrer quando o agente não pode se comunicar diretamente ou através de um servidor de firewall/proxy para o serviço Azure Monitor. Verifique as configurações do proxy do agente ou se o firewall/proxy da rede permite o tráfego tcp do computador para o serviço.|
    |2138 |Módulos do Serviço de Integridade |Proxy exige autenticação |Configure as configurações do proxy do agente e especifique o nome de usuário/senha necessário para autenticar com o servidor proxy. |
    |2129 |Módulos do Serviço de Integridade |Negociação TLS com falha de conexão/falha |Verifique as configurações tcp/IP do adaptador de rede e as configurações do proxy do agente.|
    |2127 |Módulos do Serviço de Integridade |Falha no envio de dados recebidos código de erro |Se isso só acontece periodicamente durante o dia, pode ser apenas uma anomalia aleatória que pode ser ignorada. Monitore para entender com que frequência isso acontece. Se isso acontecer com frequência ao longo do dia, primeiro verifique as configurações de rede e as configurações do proxy. Se a descrição incluir código de erro HTTP 404 e é a primeira vez que o agente tenta enviar dados para o serviço, ele incluirá um erro de 500 com um código de erro interno 404. 404 significa não encontrado, o que indica que a área de armazenamento do novo espaço de trabalho ainda está sendo provisionada. Na próxima tentativa, os dados serão reescritos com sucesso no espaço de trabalho como esperado. Um erro HTTP 403 pode indicar um problema de permissão ou credenciais. Há mais informações incluídas no erro 403 para ajudar a solucionar o problema.|
    |4000 |Conector de serviço |Falha na resolução de nomes DNS |A máquina não conseguiu resolver o endereço da Internet usado ao enviar dados para o serviço. Isso pode ser configurações de resolução de DNS na sua máquina, configurações incorretas de proxy ou talvez um problema temporário de DNS com seu provedor. Se isso acontecer periodicamente, pode ser causado por um problema transitório relacionado à rede.|
    |4001 |Conector de serviço |A conexão com o serviço falhou. |Esse erro pode ocorrer quando o agente não pode se comunicar diretamente ou através de um servidor de firewall/proxy para o serviço Azure Monitor. Verifique as configurações do proxy do agente ou se o firewall/proxy da rede permite o tráfego tcp do computador para o serviço.|
    |4002 |Conector de serviço |O serviço retornou o código de status HTTP 403 em resposta a uma consulta. Consulte o administrador do serviço para a saúde do serviço. A consulta será repetida mais tarde. |Este erro é escrito durante a fase inicial de registro do agente e você verá uma URL semelhante à seguinte: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Um código de erro 403 significa proibido e pode ser causado por um ID ou chave do espaço de trabalho digitado incorretamente, ou os dados e o tempo estão incorretos no computador. Se a hora for +/-15 minutos da hora atual, a integração falhará. Para corrigir isso, atualize a data e/ou o fuso horário do seu computador Windows.|

## <a name="data-collection-issues"></a>Problemas de coleta de dados

Depois que o agente é instalado e reporta para seu espaço de trabalho ou espaços de trabalho configurados, ele pode parar de receber configuração, coleta ou encaminhamento de desempenho, logs ou outros dados para o serviço, dependendo do que estiver ativado e direcionando o computador. É necessário determinar se:

- É um tipo de dados específico ou todos os dados que não estão disponíveis no espaço de trabalho?
- O tipo de dados é especificado por uma solução ou especificado como parte da configuração de coleta de dados do espaço de trabalho?
- Quantos computadores são afetados? É um único ou vários computadores reportando-se ao espaço de trabalho?
- Estava funcionando e parou em uma determinada hora do dia, ou nunca foi coletado? 
- A consulta de pesquisa de log que você está usando está usando está sintáticamente correta? 
- O agente já recebeu sua configuração do Azure Monitor?

O primeiro passo para solucionar problemas é determinar se o computador está enviando um evento de batimentocardíaco.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se a consulta retornar os resultados, você precisa determinar se um determinado tipo de dados não é coletado e encaminhado para o serviço. Isso pode ser causado pelo agente não receber configuração atualizada do serviço, ou algum outro sintoma que impeça o agente de operar normalmente. Execute as seguintes etapas para solucionar mais problemas.

1. Abra um prompt de comando elevado no computador `net stop healthservice && net start healthservice`e reinicie o serviço do agente digitando .
2. Abra o registro de eventos do *Gerente de Operações* e busque **os IDs de eventos** *7023, 7024, 7025, 7028* e *1210* da *HealthService,* fonte do **evento.**  Esses eventos indicam que o agente está recebendo com sucesso a configuração do Azure Monitor e eles estão monitorando ativamente o computador. A descrição do evento para o ID do evento 1210 também especificará na última linha todas as soluções e Insights que estão incluídos no escopo de monitoramento do agente.  

    ![Descrição do ID do evento 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se após vários minutos você não ver os dados esperados nos resultados ou visualização da consulta, dependendo se você estiver visualizando os dados de uma solução ou Insight, do registro de eventos do Gerente de *Operações,* procure por **fontes de eventos** *HealthService* e *Health Service Modules* e filtre por Aviso *e* *Erro* de Nível de **Evento** para confirmar se ele escreveu eventos a partir da tabela a seguir.

    |ID do evento |Fonte |Descrição |Resolução |
    |---------|-------|------------|
    |8000 |HealthService |Este evento especificará se um fluxo de trabalho relacionado ao desempenho, evento ou outro tipo de dados coletados não é capaz de encaminhar ao serviço para ingestão no espaço de trabalho. | O ID de evento 2136 da fonte HealthService é escrito juntamente com este evento e pode indicar que o agente não pode se comunicar com o serviço, possivelmente devido à configuração errada das configurações de proxy e autenticação, a interrupção da rede ou o firewall/proxy da rede não permite o tráfego TCP do computador para o serviço.| 
    |10102 e 10103 |Módulos do Serviço de Integridade |O fluxo de trabalho não conseguiu resolver a fonte de dados. |Isso pode ocorrer se o contador ou instância de desempenho especificado não existir no computador ou for definido incorretamente nas configurações de dados do espaço de trabalho. Se este for um [contador](data-sources-performance-counters.md#configuring-performance-counters)de desempenho especificado pelo usuário, verifique se as informações especificadas estão seguindo o formato correto e existem nos computadores de destino. |
    |26002 |Módulos do Serviço de Integridade |O fluxo de trabalho não conseguiu resolver a fonte de dados. |Isso pode ocorrer se o registro de eventos do Windows especificado não existir no computador. Este erro pode ser ignorado com segurança se o computador não tiver esse registro de evento registrado, caso contrário, se este for um registro de [evento](data-sources-windows-events.md#configuring-windows-event-logs)especificado pelo usuário, verifique se as informações especificadas estão corretas. |

