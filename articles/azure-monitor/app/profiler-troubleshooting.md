---
title: Problemas de solução de problemas com o Azure Application Insights Profiler
description: Este artigo contém etapas de solução de problemas e informações para ajudar os desenvolvedores que estão tendo problemas para habilitar ou usar o Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 55bc4ff05b650884ef17e0de10d7156cbf458a9c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640954"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Solucionar problemas ao habilitar ou exibir o Profiler do Application Insights

## <a name="active-issues"></a>Problemas ativos

* O perfil dos aplicativos ASP.NET Core 3.x é suportado agora no Azure App Services.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Solução de problemas gerais

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Os perfis são carregados apenas se há solicitações para seu aplicativo enquanto o Profiler está em execução

O Azure Application Insights Profiler coleta dados de criação de perfil por dois minutos a cada hora. Ele também coleta dados quando você seleciona o botão **Criar Perfil Agora** no painel **Configurar o Application Insights Profiler**. Mas os dados de criação de perfil só são carregados quando podem ser anexados a uma solicitação que aconteceu enquanto o Profiler estava em execução. 

O Profiler grava mensagens de rastreamento e eventos personalizados no recurso do Application Insights. Você pode usar esses eventos para ver como o Profiler está sendo executado. Se você acha que o Profiler deve estar em execução e capturando rastreios, mas eles não estão sendo exibidos no painel **Desempenho**, é possível verificar como o Profiler está em execução:

1. Procure por mensagens de rastreamento e eventos personalizados enviados pelo Profiler para o recurso Application Insights. Você pode usar essa cadeia de caracteres de pesquisa para encontrar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    A imagem a seguir exibe dois exemplos de pesquisas de dois recursos de IA: 
    
   * No exemplo à esquerda, o aplicativo não está recebendo solicitações enquanto o Profiler está em execução. A mensagem explica que o upload foi cancelado devido à ausência de atividade. 

   * No exemplo à direita, você pode ver que o Profiler iniciou e enviou eventos personalizados quando detectou solicitações que ocorreram enquanto o Profiler estava em execução. Se o evento personalizado ServiceProfilerSample é exibido, isso significa que o Profiler anexou um rastreamento a uma solicitação e você pode exibir o rastreamento no painel **Desempenho do Application Insights**.

     Se nenhuma telemetria é exibida, o Profiler não está em execução. Para solucionar problemas, confira as seções de solução de problemas para o seu tipo de aplicativo específico posteriormente neste artigo.  

     ![Pesquisar por telemetria do Profiler][profiler-search-telemetry]

1. Se houver solicitações durante o período de tempo que o Profiler executou, verifique se as solicitações são tratadas pela parte do seu aplicativo que tem o Profiler habilitado. Embora às vezes os aplicativos consistam em vários componentes, o Profiler só é habilitado para alguns dos componentes. O painel **Configurar Application Insights Profiler** mostra os componentes que carregaram rastreios.

### <a name="other-things-to-check"></a>Outros itens a serem verificados
* Verifique se o aplicativo está sendo executado no .NET Framework 4.6.
* Se seu aplicativo web é um aplicativo ASP.NET Core, deve estar executando pelo menos ASP.NET Core 2.0.
* Se os dados que você está tentando exibir forem mais antigos do que duas semanas, tente limitar seu filtro de tempo e tente novamente. Rastreamentos são excluídos após sete dias.
* Verifique se nenhum firewall ou proxy bloqueou o acesso a https://gateway.azureserviceprofiler.net.
* O Profiler não é suportado em planos de serviços de aplicativos gratuitos ou compartilhados. Se você estiver usando um desses planos, tente escalar até um dos planos básicos e o Profiler deve começar a funcionar.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dois segmentos paralelos de contagem

Em alguns casos, a métrica de tempo toral no visualizador de pilha é maior que a duração da solicitação.

Essa situação poderá ocorrer quando dois ou mais threads estiverem associados a uma solicitação e eles estiverem operando em paralelo. Nesse caso, o tempo total de thread é maior que o tempo decorrido. Um thread pode estar aguardando até que o outro seja concluído. O visualizador tenta detectar essa situação e omite a espera desinteressante. Ao fazê-lo, ele erra ao exibir muitas informações em vez de omitir o que poderiam ser informações críticas.

Quando você vir threads paralelos em seus rastreamentos, determine quais threads estão aguardando para que você possa certificar-se do caminho crítico para a solicitação. Geralmente, o thread rapidamente entra em estado de espera é simplesmente aguardando outros threads. Concentre-se nos outros threads e ignore o tempo nos threads em espera.

### <a name="error-report-in-the-profile-viewer"></a>Relatório de erros no visualizador de perfil
Envie um tíquete de suporte no portal. Certifique-se de incluir uma ID de correlação da mensagem de erro.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Solucionar problemas do Profiler no Serviço de Aplicativo do Azure
Para o Profiler funcionar corretamente:
* Seu plano de serviço de aplicativo da web deve ser de nível Básico ou superior.
* Seu aplicativo da web deve ter o Application Insights ativado.
* Seu aplicativo web deve ter as seguintes configurações do aplicativo:

    |Configurações de Aplicativo    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o recurso Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* O trabalho Web **ApplicationInsightsProfiler3** precisa estar em execução. Para verificar o trabalho Web:
   1. Vá para o [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. No menu **Ferramentas**, selecione **Painel do WebJobs**.  
      O painel **WebJobs** é aberto. 
   
      ![trabalho Web do criador de perfil]   
   
   1. Para exibir os detalhes do webjob, incluindo o log, selecione o link **ApplicationInsightsProfiler3.**  
     O painel **Detalhes do WebJob Contínuo**.

      ![Criador de perfil de trabalho Web de log]

Se você não conseguir descobrir por que o Profiler não está funcionando para você, você serviceprofilerhelp@microsoft.compode baixar o registro e enviá-lo para nossa equipe para assistência, . 
    
### <a name="manual-installation"></a>Instalação manual

Quando você configura o Profiler, são feitas atualizações nas configurações do aplicativo Web. Será possível aplicar as atualizações manualmente se o ambiente exigir. Um exemplo pode ser que seu aplicativo está sendo executado em um ambiente de Aplicativos Web para o PowerApps. Para aplicar atualizações manualmente:

1. No painel **Controle de Aplicativos da Web,** abra **configurações**.

1. Definir **a versão .NET Framework** para **v4.6**.

1. Defina **Sempre ativo** como **Ativado**.
1. Crie essas configurações do aplicativo:

    |Configurações de Aplicativo    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o recurso Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Número excessivo de sessões de criação de perfil ativas

No momento, é possível habilitar o Profiler em no máximo quatro aplicativos Web do Azure e slots de implantação em execução no mesmo plano de serviço. Se você tiver mais do que quatro aplicativos Web em execução em um Plano do Serviço de Aplicativo, o Profiler poderá lançar uma *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. O Profiler é executado separadamente para cada aplicativo Web e tenta iniciar uma sessão do ETW (Rastreamento de Eventos para Windows) para cada aplicativo. Mas há um número limitado de sessões do ETW que podem estar ativas simultaneamente. Se o trabalho Web do Profiler relatar um número excessivo de sessões de criação de perfil ativas, mova alguns aplicativos Web para um plano de serviço diferente.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Erro de implantação: diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se estiver reimplementando seu aplicativo Web em um recurso de Aplicativos Web com o Profiler habilitado, você poderá ver a seguinte mensagem:

*Diretório\\Não Vazio 'D:\\site\\\\residencial\\wwwroot App_Data empregos'*

Este erro ocorrerá se você executar a Implantação da Web de scripts ou do pipeline de implantação do Azure DevOps. A solução é adicionar os seguintes parâmetros de implantação adicionados à tarefa de Implantação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Esses parâmetros excluem a pasta usada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Eles não afetam a instância do Profiler em execução no momento.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como fazer para determinar se o Application Insights Profiler está em execução?

O Profiler é executado como um trabalho Web contínuo no aplicativo Web. Você pode abrir o recurso do aplicativo web no [portal Azure](https://portal.azure.com). Se o painel **WebJobs**, verifique o status de **ApplicationInsightsProfiler**. Se ele não estiver em execução, abra **Logs** para obter mais informações.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Solucionar problemas com o Profiler e o Diagnóstico do Azure

>**O bug no profiler que navega no WAD for Cloud Services foi corrigido.** A versão mais recente do WAD (1.12.2.0) para Cloud Services funciona com todas as versões recentes do App Insights SDK. Os hosts do Cloud Service atualizarão o WAD automaticamente, mas não é imediato. Para forçar um upgrade, você pode reimplantar seu serviço ou reiniciar o nó.

Para ver se o Profiler está configurado corretamente pelo Diagnóstico do Azure, siga estas três etapas: 
1. Primeiro, você verifique se o conteúdo da configuração do Diagnóstico do Azure implantado é o esperado. 

1. Segundo, verifique se o Diagnóstico do Azure passa a iKey adequada na linha de comando do Profiler. 

1. Terceiro, verifique o arquivo de log do Profiler para ver se o Profiler foi executado, mas retornou um erro. 

Para verificar as configurações que foram usadas para configurar o Diagnóstico do Azure:

1. Faça login na máquina virtual (VM) e abra o arquivo de registro neste local. (A unidade pode ser c: ou d: e a versão do plugin poderia ser diferente.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    ou
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. No arquivo, você pode pesquisar pela cadeia de caracteres **WadCfg** para encontrar as configurações que foram passadas para a VM para configurar o Diagnóstico do Azure. Você pode verificar se a iKey utilizado pelo coletor do Profiler está correta.

1. Verifique a linha de comando que é usada para iniciar o Profiler. Os argumentos usados para iniciar o Profiler estão no arquivo a seguir. (A unidade pode ser c: ou d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Verifique se o iKey na linha de comando do Profiler está correto. 

1. Usando o caminho encontrado no arquivo *config.json* acima, verifique o arquivo de log do Profiler. Ele exibe as informações de depuração que indicam as configurações que o Profiler está usando. Ele também exibe mensagens de erro e de status do Profiler.  

    Se o Profiler estiver sendo executado enquanto seu aplicativo estiver recebendo solicitações, a seguinte mensagem será exibida: *Atividade detectada a partir do iKey*. 

    Quando o rastreamento está sendo carregado, a seguinte mensagem é exibida: *Comece a carregar o rastreamento*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de firewall ou proxy de rede

Se o aplicativo se conectar à Internet por meio de um proxy ou de um firewall, você pode precisar editar as regras para permitir que seu aplicativo se comunique com o serviço Application Insights Profiler. Os IPs usados pelo Application Insights Profiler estão incluídos na tag de serviço do Azure Monitor.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[trabalho Web do criador de perfil]:./media/profiler-troubleshooting/Profiler-webjob.png
[Criador de perfil de trabalho Web de log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








