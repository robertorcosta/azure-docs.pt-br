---
title: Execução de runbook na Automação do Azure
description: Descreve os detalhes de como um runbook na Automação do Azure é processado.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: ace701a6c19f4fe3af1c9ae6f5e63097dd59d405
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871683"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure

A automação de processos na automação do Azure permite que você crie e gerencie runbooks de PowerShell, de fluxo de trabalho do PowerShell e gráficos. Para obter detalhes, consulte [runbooks de automação do Azure](automation-runbook-types.md). 

A automação executa seus runbooks com base na lógica definida dentro deles. Se um runbook for interrompido, ele será reiniciado no início. Esse comportamento exige que você grave runbooks que dão suporte à reinicialização se ocorrerem problemas transitórios.

Iniciar um runbook na automação do Azure cria um trabalho, que é uma instância de execução única do runbook. Cada trabalho acessa recursos do Azure fazendo uma conexão com sua assinatura do Azure. O trabalho só poderá acessar recursos em seu datacenter se esses recursos estiverem acessíveis da nuvem pública.

A automação do Azure atribui um trabalho para executar cada trabalho durante a execução do runbook. Enquanto os trabalhadores são compartilhados por muitas contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não pode controlar quais serviços de trabalho suas solicitações de trabalho.

Quando você exibe a lista de runbooks no portal do Azure, ele mostra o status de cada trabalho que foi iniciado para cada runbook. A automação do Azure armazena logs de trabalho por um máximo de 30 dias.

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks), Runbooks de fluxo de trabalhos do [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)e [runbooks gráficos](automation-runbook-types.md#graphical-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](automation-update-azure-modules.md).

## <a name="runbook-execution-environment"></a>Ambiente de execução de runbook

Os Runbooks na automação do Azure podem ser executados em uma área restrita do Azure ou em um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md). 

Quando os runbooks são projetados para autenticar e executar em recursos no Azure, eles são executados em uma área restrita do Azure, que é um ambiente compartilhado que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma área restrita são restringidos pelas limitações de recurso da área restrita. O ambiente de área restrita do Azure não oferece suporte a operações interativas. Ele impede o acesso a todos os servidores COM fora do processo. Ele também requer o uso de arquivos MOF locais para runbooks que fazem chamadas Win32.

Você também pode usar um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md) para executar runbooks diretamente no computador que hospeda a função e em relação aos recursos locais no ambiente. A automação do Azure armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores atribuídos.

>[!NOTE]
>Para ser executado em um Hybrid Runbook Worker Linux, seus scripts devem ser assinados e o trabalho configurado de acordo. Como alternativa, a [validação da assinatura deve ser desativada](automation-linux-hrw-install.md#turn-off-signature-validation). 

A tabela a seguir lista algumas tarefas de execução de runbook com o ambiente de execução recomendado listado para cada uma.

|Tarefa|Recomendação|Anotações|
|---|---|---|
|Integração com serviços do Azure|Área restrita do Azure|Hospedado no Azure, a autenticação é mais simples. Se você estiver usando uma Hybrid Runbook Worker em uma VM do Azure, poderá [usar a autenticação de runbook com identidades gerenciadas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Obter o desempenho ideal para gerenciar recursos do Azure|Área restrita do Azure|O script é executado no mesmo ambiente, que tem menos latência.|
|Redução de custos operacionais|Área restrita do Azure|Não há nenhuma sobrecarga de computação e nenhuma necessidade de uma VM.|
|Executar script de execução longa|Hybrid Runbook Worker|As áreas restritas do Azure têm [limites de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagir com serviços locais|Hybrid Runbook Worker|Acesse diretamente o computador host ou os recursos em outros ambientes de nuvem ou no ambiente local. |
|Exigir software de terceiros e executáveis|Hybrid Runbook Worker|Você gerencia o sistema operacional e pode instalar o software.|
|Monitoramento de um arquivo ou uma pasta com um runbook|Hybrid Runbook Worker|Use uma [tarefa do Inspetor](automation-watchers-tutorial.md) em um Hybrid runbook Worker.|
|Executar um script com uso intensivo de recursos|Hybrid Runbook Worker| As áreas restritas do Azure têm [limites de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Usar módulos com requisitos específicos| Hybrid Runbook Worker|Alguns exemplos são:</br> WinSCP: dependência de winscp.exe </br> Administração do IIS-dependência na habilitação ou gerenciamento do IIS|
|Instalar um módulo com um instalador|Hybrid Runbook Worker|Os módulos para área restrita devem dar suporte à cópia.|
|Usar runbooks ou módulos que exigem .NET Framework versão diferente do 4.7.2|Hybrid Runbook Worker|As áreas restritas do Azure dão suporte .NET Framework 4.7.2 e não há suporte para a atualização para uma versão diferente.|
|Executar scripts que exigem elevação|Hybrid Runbook Worker|As áreas restritas não permitem elevação. Com um Hybrid Runbook Worker, você pode desativar o UAC e usar o [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) ao executar o comando que requer elevação.|
|Executar scripts que exigem acesso ao Instrumentação de Gerenciamento do Windows (WMI)|Hybrid Runbook Worker|Trabalhos em execução em áreas restritas na nuvem não podem acessar o provedor WMI. |

## <a name="resources"></a>Recursos

Seus runbooks devem incluir lógica para lidar com [recursos](https://docs.microsoft.com/rest/api/resources/resources), por exemplo, VMS, rede e recursos na rede. Os recursos são vinculados a uma assinatura do Azure, e os runbooks exigem credenciais apropriadas para acessar qualquer recurso. Para obter um exemplo de como manipular recursos em um runbook, consulte [manipular recursos](manage-runbooks.md#handle-resources). 

## <a name="security"></a>Segurança

A automação do Azure usa a [ASC (central de segurança do Azure)](https://docs.microsoft.com/azure/security-center/security-center-introAzure) para fornecer segurança para seus recursos e detectar o comprometimento em sistemas Linux. A segurança é fornecida em suas cargas de trabalho, independentemente de os recursos estarem no Azure ou não. Consulte [introdução à autenticação na automação do Azure](automation-security-overview.md).

O ASC coloca restrições em usuários que podem executar qualquer script, assinado ou não assinado, em uma VM. Se você for um usuário com acesso de raiz a uma VM, deverá configurar explicitamente a máquina com uma assinatura digital ou desativá-la. Caso contrário, você só poderá executar um script para aplicar as atualizações do sistema operacional depois de criar uma conta de automação e habilitar o recurso apropriado.

## <a name="subscriptions"></a>Assinaturas

Uma [assinatura](https://docs.microsoft.com/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) do Azure é um contrato com a Microsoft para usar um ou mais serviços baseados em nuvem, para os quais você está cobrado. Para a automação do Azure, cada assinatura é vinculada a uma conta de automação do Azure e você pode [criar várias assinaturas](manage-runbooks.md#work-with-multiple-subscriptions) na conta.

## <a name="credentials"></a>Credenciais

Um runbook requer [credenciais](shared-resources/credentials.md) apropriadas para acessar qualquer recurso, seja para sistemas do Azure ou de terceiros. Essas credenciais são armazenadas na automação do Azure, Key Vault, etc.  

## <a name="azure-monitor"></a>Azure Monitor

A automação do Azure usa o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para monitorar suas operações de máquina. As operações exigem um espaço de trabalho Log Analytics e [agentes de log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

### <a name="log-analytics-agent-for-windows"></a>Agente do Log Analytics para Windows

O [agente do log Analytics para Windows](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windowsmonitor) funciona com Azure monitor para gerenciar VMs do Windows e computadores físicos. Os computadores podem ser executados no Azure ou em um ambiente não Azure, como um datacenter local. Você deve configurar o agente para relatar para um ou mais espaços de trabalho do Log Analytics. 

>[!NOTE]
>O agente Log Analytics para Windows era conhecido anteriormente como Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agente do Log Analytics para Linux

O [agente de log Analytics para Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux) funciona de forma semelhante ao agente para Windows, mas conecta computadores Linux ao Azure monitor. O agente é instalado com uma conta de usuário do **nxautomation** que permite a execução de comandos que exigem permissões raiz, por exemplo, em um Hybrid runbook Worker. A conta **nxautomation** é uma conta de sistema que não requer uma senha. 

A conta **nxautomation** com as permissões sudo correspondentes deve estar presente durante [a instalação de um Hybrid runbook Worker do Linux](automation-linux-hrw-install.md). Se você tentar instalar o trabalho e a conta não estiver presente ou não tiver as permissões apropriadas, a instalação falhará.

Os logs disponíveis para o agente de Log Analytics e a conta **nxautomation** são:

* log do agente/var/opt/Microsoft/omsagent/log/omsagent.log-Log Analytics 
* /var/opt/Microsoft/omsagent/Run/automationworker/Worker.log-log de trabalho de automação

>[!NOTE]
>O usuário **nxautomation** integrado como parte do gerenciamento de atualizações executa apenas runbooks assinados.

## <a name="runbook-permissions"></a>Permissões de runbook

Um runbook precisa de permissões para autenticação no Azure, por meio de credenciais. Consulte [gerenciar contas Executar como da automação do Azure](manage-runas-account.md). 

## <a name="modules"></a>Módulos

A automação do Azure dá suporte a vários módulos padrão, incluindo alguns módulos AzureRM (AzureRM. Automation) e um módulo que contém vários cmdlets internos. Também há suporte para módulos instaláveis, incluindo os módulos AZ (AZ. Automation), que atualmente estão sendo usados em preferência aos módulos AzureRM. Para obter detalhes dos módulos que estão disponíveis para seus runbooks e configurações DSC, consulte [gerenciar módulos na automação do Azure](shared-resources/modules.md).

## <a name="certificates"></a>Certificados

A automação do Azure usa [certificados](shared-resources/certificates.md) para autenticação no Azure ou os adiciona ao Azure ou a recursos de terceiros. Os certificados são armazenados com segurança para acesso por runbooks e configurações DSC. 

Seus runbooks podem usar certificados autoassinados, que não são assinados por uma autoridade de certificação (CA). Consulte [criar um novo certificado](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Trabalhos

A automação do Azure dá suporte a um ambiente para executar trabalhos da mesma conta de automação. Um único runbook pode ter muitos trabalhos em execução ao mesmo tempo. Quanto mais trabalhos você executar ao mesmo tempo, mais frequentemente eles poderão ser enviados à mesma área restrita. 

Os trabalhos em execução no mesmo processo de área restrita podem afetar os outros. Um exemplo é a execução do cmdlet [Disconnect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) . A execução desse cmdlet desconecta cada trabalho de runbook no processo de área restrita compartilhada. Para obter um exemplo de como trabalhar com esse cenário, consulte [impedir trabalhos simultâneos](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>Os trabalhos do PowerShell iniciados a partir de um runbook executado em uma área restrita do Azure podem não ser executados no [modo de linguagem](/powershell/module/microsoft.powershell.core/about/about_language_modes)completo do PowerShell. 

### <a name="job-statuses"></a>Status de trabalho

A tabela a seguir descreve os status que são possíveis para um trabalho. Você pode exibir um resumo de status para todos os trabalhos de runbook ou analisar detalhes de um trabalho de runbook específico no portal do Azure. Você também pode configurar a integração com o seu espaço de trabalho do Log Analytics a fim de encaminhar fluxos de trabalho e status do trabalho do runbook. Para obter mais informações sobre como integrar com logs de Azure Monitor, consulte [encaminhar status do trabalho e fluxos de trabalho de automação para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md). Consulte também [obter status do trabalho](manage-runbooks.md#obtain-job-statuses) para obter um exemplo de como trabalhar com status em um runbook.

| Status | Descrição |
|:--- |:--- |
| Concluído |Operação concluída com sucesso. |
| Failed (Falha) |Falha na compilação de um runbook de fluxo de trabalho gráfico ou do PowerShell. Um runbook do PowerShell não pôde ser iniciado ou o trabalho tinha uma exceção. Consulte [tipos de runbook de automação do Azure](automation-runbook-types.md).|
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fair-share) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está aguardando que os recursos em um trabalho de automação fiquem disponíveis para que possam ser iniciados. |
| Continuando |O sistema está retomando o trabalho depois que ele ter sido suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado, pois atingiu o limite de fração justa . Ele continuará em breve a partir de seu último ponto de verificação. |
| Iniciando |O trabalho foi atribuído a um trabalhador e o sistema está iniciando-o. |
| Parado |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está parando o trabalho. |
| Suspenso |Aplica-se somente a [runbooks de fluxo de trabalho gráfico e do PowerShell](automation-runbook-types.md) . O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Se um runbook não tiver um ponto de verificação, ele começará do início. Se ele tiver um ponto de verificação, poderá iniciar novamente e retomar no último ponto de verificação. O sistema suspende apenas o runbook quando ocorre uma exceção. Por padrão, a `ErrorActionPreference` variável é definida como continuar, indicando que o trabalho continua em execução em um erro. Se a variável de preferência for definida como parar, o trabalho será suspenso em um erro.  |
| Suspensão |Aplica-se somente a [runbooks de fluxo de trabalho gráfico e do PowerShell](automation-runbook-types.md) . O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já tiver passado seu último ponto de verificação, ele será concluído antes que possa ser suspenso. |

## <a name="activity-logging"></a>Log de atividades

A execução de runbooks na automação do Azure grava detalhes em um log de atividades para a conta de automação. Para obter detalhes sobre como usar o log, consulte [recuperar detalhes do log de atividades](manage-runbooks.md#retrieve-details-from-activity-log). 

## <a name="exceptions"></a>Exceções

Esta seção descreve algumas maneiras de lidar com exceções ou problemas intermitentes em seus runbooks. Um exemplo é uma exceção WebSocket. A manipulação correta de exceções impede falhas de rede transitórias de causar falha nos runbooks. 

### <a name="erroractionpreference"></a>ErrorActionPreference

A variável [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como o PowerShell responde a um erro de não finalização. Os erros de encerramento sempre são encerrados e `ErrorActionPreference`não são afetados pelo.

Quando o runbook usa `ErrorActionPreference`, um erro normalmente não conclusivo, como o `PathNotFound` do cmdlet [Get-ChildItem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) , interrompe a conclusão do runbook. O exemplo a seguir demonstra o uso de `ErrorActionPreference`. O comando de [Write-Output](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) final nunca é executado, pois o script é interrompido.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Tentar capturar finalmente

[Tentar catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é usado em scripts do PowerShell para lidar com erros de encerramento. O script pode usar esse mecanismo para capturar exceções específicas ou exceções gerais. A `catch` instrução deve ser usada para rastrear ou tentar lidar com erros. O exemplo a seguir tenta baixar um arquivo que não existe. Ele captura a `System.Net.WebException` exceção e retorna o último valor para qualquer outra exceção.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de encerramento. Esse mecanismo pode ser útil ao definir sua própria lógica em um runbook. Se o script atender a um critério que deve parar, ele poderá usar a `throw` instrução para parar. O exemplo a seguir usa essa instrução para mostrar um parâmetro de função necessário.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Errors

Seus runbooks devem lidar com erros. A automação do Azure dá suporte a dois tipos de erros do PowerShell, terminando e não encerrando. 

Os erros de encerramento param a execução do runbook quando eles ocorrem. O runbook pára com um status de trabalho de falha.

Os erros de não finalização permitem que um script continue mesmo depois que eles ocorrerem. Um exemplo de um erro de não finalização é aquele que ocorre quando um runbook usa `Get-ChildItem` o cmdlet com um caminho que não existe. O PowerShell vê que o caminho não existe, gera um erro e continua até a próxima pasta. O erro nesse caso não define o status do trabalho de runbook como Failed e o trabalho pode até ser concluído. Para forçar um runbook a parar se houver um erro sem finalização, você pode usar `ErrorAction Stop` no cmdlet.

## <a name="calling-processes"></a>Chamando processos

Os Runbooks executados em áreas restritas do Azure não oferecem suporte a processos de chamada, como executáveis (arquivos **. exe** ) ou subprocessos. O motivo disso é que uma área restrita do Azure é um processo compartilhado executado em um contêiner que pode não ser capaz de acessar todas as APIs subjacentes. Para cenários que exigem software de terceiros ou chamadas para subprocessos, você deve executar um runbook em um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Características do dispositivo e do aplicativo

Os trabalhos de runbook em áreas restritas do Azure não podem acessar nenhuma característica de dispositivo ou aplicativo. A API mais comum usada para consultar as métricas de desempenho no Windows é o WMI, com algumas das métricas comuns de uso de memória e CPU. No entanto, não importa qual API é usada, pois os trabalhos em execução na nuvem não podem acessar a Microsoft implementation of Web-Based Enterprise Management (WBEM). Essa plataforma se baseia no modelo CIM (CIM), fornecendo os padrões do setor para definir características de dispositivo e aplicativo.

## <a name="webhooks"></a>Webhooks

Os serviços externos, por exemplo, Azure DevOps Services e GitHub, podem iniciar um runbook na automação do Azure. Para fazer esse tipo de inicialização, o serviço usa um [webhook](automation-webhooks.md) por meio de uma única solicitação HTTP. O uso de um webhook permite que os runbooks sejam iniciados sem a implementação de uma solução completa de automação do Azure. 

## <a name="shared-resources"></a><a name="fair-share"></a>Recursos compartilhados

Para compartilhar recursos entre todos os runbooks na nuvem, o Azure usa um conceito chamado Fair share. Usando Fair share, o Azure descarrega temporariamente ou interrompe qualquer trabalho que tenha sido executado por mais de três horas. Os trabalhos para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e Runbooks do [Python](automation-runbook-types.md#python-runbooks) são interrompidos e não reiniciados e o status do trabalho se torna parado.

Para tarefas de automação do Azure de longa execução, é recomendável usar um Hybrid Runbook Worker. Os Hybrid Runbook Workers não são limitados por fração justa e não limitam o tempo de execução de um runbook. Os outros [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) do trabalho se aplicam a áreas restritas do Azure e ao Hybrid Runbook Workers. Embora Hybrid runbook Workers não sejam limitados pelo limite de compartilhamento justo de 3 horas, você deve desenvolver runbooks para executar em trabalhos que dão suporte a reinicializações de problemas de infraestrutura local inesperados.

Outra opção é otimizar um runbook usando runbooks filho. Por exemplo, seu runbook pode executar um loop por meio da mesma função em vários recursos, por exemplo, com uma operação de banco de dados em vários bancos. Você pode mover essa função para um [runbook filho](automation-child-runbooks.md) e fazer com que seu runbook a chame usando [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). Os runbooks filho são executados em paralelo em processos separados.

O uso de runbooks filho diminui a quantidade total de tempo que o runbook pai deve concluir. Seu runbook pode usar o cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) para verificar o status do trabalho para um runbook filho se ele ainda tiver mais operações após a conclusão do filho.

## <a name="next-steps"></a>Próximas etapas

* Para começar a trabalhar com um runbook, consulte [gerenciar runbooks na automação do Azure](manage-runbooks.md).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, confira a [Documentação do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
