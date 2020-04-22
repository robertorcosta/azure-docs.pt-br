---
title: Habilitar o registro em log de diagnóstico
description: Saiba como habilitar o log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769994"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Habilitar log de diagnósticos para aplicativos no Serviço de Aplicativo do Azure
## <a name="overview"></a>Visão geral
O Azure fornece diagnósticos internos para auxiliar na depuração de um [aplicativo de Serviço de Aplicativo](overview.md). Neste artigo, você saberá como habilitar o registro em log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.

Este artigo usa o [portal do Azure](https://portal.azure.com) e a CLI do Azure para trabalhar com logs de diagnóstico. Para saber mais sobre como trabalhar com logs de diagnóstico usando o Visual Studio, confira [Solucionando problemas do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Além das instruções de registro neste artigo, há uma nova capacidade integrada de registro com o Azure Monitoring. Você encontrará mais sobre esse recurso na seção [Enviar logs para o Azure Monitor (preview).](#send-logs-to-azure-monitor-preview) 
>
>

|Type|Plataforma|Location|Descrição|
|-|-|-|-|
| Registro em log do aplicativo | Windows, Linux | Sistema de arquivos app service e/ou blobs de armazenamento Azure | Registra mensagens geradas pelo código do aplicativo. As mensagens podem ser geradas pela estrutura web escolhida ou pelo código do aplicativo usando diretamente o padrão de registro padrão do seu idioma. Cada mensagem é atribuída a uma das seguintes categorias: **Crítica,** **Erro,** **Aviso,** **Informações,** **Depuração**e **Rastreamento**. Você pode selecionar o quão verboso deseja que o registro seja, definindo o nível de gravidade ao ativar o registro de aplicativos.|
| Registro de servidor web| Windows | Sistema de arquivos app service ou blobs de armazenamento Azure| Dados de solicitação HTTP brutos no [formato de arquivo de log estendido W3C](/windows/desktop/Http/w3c-logging). Cada mensagem de log inclui dados como o método HTTP, uri de recursos, IP cliente, porta do cliente, agente do usuário, código de resposta e assim por diante. |
| Mensagens de erro detalhadas| Windows | Sistema de arquivos app service | Cópias das páginas de erro *.htm* que teriam sido enviadas para o navegador cliente. Por razões de segurança, páginas de erro detalhadas não devem ser enviadas aos clientes em produção, mas o App Service pode salvar a página de erro cada vez que ocorrer um erro de aplicativo que tenha código HTTP 400 ou superior. A página pode conter informações que podem ajudar a determinar por que o servidor retorna o código de erro. |
| De uma solicitação de rastreio com falha | Windows | Sistema de arquivos app service | Informações detalhadas de rastreamento sobre solicitações com falha, incluindo um rastreamento dos componentes iIS usados para processar a solicitação e o tempo decorrido em cada componente. Isso é útil se você quiser melhorar o desempenho do site ou isolar um erro HTTP específico. Uma pasta é gerada para cada solicitação com falha, que contém o arquivo de log XML e a folha de estilos XSL para exibir o arquivo log. |
| Registro de implantação | Windows, Linux | Sistema de arquivos app service | Logs para quando você publica conteúdo em um aplicativo. O registro de implantação acontece automaticamente e não há configurações configuráveis para registro de implantação. Isso ajuda a determinar por que uma implantação falhou. Por exemplo, se você usar um [script de implantação personalizado,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)poderá usar o registro de implantação para determinar por que o script está falhando. |

> [!NOTE]
> O App Service fornece uma ferramenta de diagnóstico interativa e dedicada para ajudá-lo a solucionar problemas em seu aplicativo. Para obter mais informações, consulte [a visão geral dos diagnósticos do Azure App Service](overview-diagnostics.md).
>
> Além disso, você pode usar outros serviços do Azure para melhorar os recursos de registro e monitoramento do seu aplicativo, como [o Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Habilitar o registro de aplicativos (Windows)

> [!NOTE]
> O registro de aplicativos para armazenamento blob só pode usar contas de armazenamento na mesma região que o App Service

Para habilitar o registro de aplicativos para aplicativos do Windows no [portal Azure,](https://portal.azure.com)navegue até o seu aplicativo e selecione **os logs do App Service**.

Selecione **Em :"Em:** Registro de **aplicativos (Sistema de Arquivos)** ou **Registro de Aplicativos (Blob)**, ou ambos. 

A opção **Filesystem** é para fins temporários de depuração, e desliga-se em 12 horas. A opção **Blob** é para registro de longo prazo e precisa de um recipiente de armazenamento blob para gravar logs.  A opção **Blob** também inclui informações adicionais nas mensagens de log, como o ID da instância VM de origem da mensagem de log (),`InstanceId`id de thread ()`Tid`e um carimbo de tempo mais granular ().[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)

> [!NOTE]
> No momento, somente logs de aplicativo do .NET podem ser gravados no Armazenamento de Blobs. Java, PHP, Node.js, logs de aplicativos Python só podem ser armazenados no sistema de arquivos app service (sem modificações de código para gravar logs para armazenamento externo).
>
> Além disso, se você [regenerar as chaves de acesso da sua conta de armazenamento,](../storage/common/storage-create-storage-account.md)você deve redefinir a respectiva configuração de registro para usar as chaves de acesso atualizadas. Para fazer isso:
>
> 1. Na guia **Configurar**, defina o respectivo recurso de log como **Desativado**. Salve sua configuração.
> 2. Habilite o registro no blob da conta de armazenamento novamente. Salve sua configuração.
>
>

Selecione o **Nível**ou o nível de detalhes a serem logados. A tabela a seguir mostra as categorias de log incluídas em cada nível:

| Nível | Categorias incluídas |
|-|-|
|**Desabilitado** | Nenhum |
|**Erro** | Erro, Crítico |
|**Aviso** | Aviso, Erro, Crítico|
|**Informações** | Informações, Aviso, Erro, Crítico|
|**Verbose** | Rastreamento, Depuração, Informações, Aviso, Erro, Crítico (todas as categorias) |

Quando terminar, **selecione Salvar**.

## <a name="enable-application-logging-linuxcontainer"></a>Habilitar o registro de aplicativos (Linux/Container)

Para habilitar o registro de aplicativos para aplicativos Linux ou aplicativos de contêineres personalizados no [portal Azure,](https://portal.azure.com)navegue até o seu aplicativo e selecione **os logs do App Service**.

No **registro de aplicativos,** selecione **Sistema de arquivos**.

Em **Cota (MB),** especifique a cota de disco para os logs de aplicativos. No **Período de Retenção (Dias),** defina o número de dias que os registros devem ser retidos.

Quando terminar, **selecione Salvar**.

## <a name="enable-web-server-logging"></a>Habilitar o log de servidor web

Para habilitar o registro de servidores web para aplicativos Windows no [portal Azure,](https://portal.azure.com)navegue até o seu aplicativo e selecione **os logs do App Service**.

Para **registro de servidor Web,** selecione **Armazenamento** para armazenar logs no armazenamento blob ou sistema **de arquivos** para armazenar logs no sistema de arquivos App Service. 

No **Período de Retenção (Dias),** defina o número de dias que os registros devem ser retidos.

> [!NOTE]
> Se você [regenerar as chaves de acesso de sua conta de armazenamento](../storage/common/storage-create-storage-account.md), será necessário redefinir a respectiva configuração de log para usar as chaves atualizadas. Para fazer isso:
>
> 1. Na guia **Configurar**, defina o respectivo recurso de log como **Desativado**. Salve sua configuração.
> 2. Habilite o registro no blob da conta de armazenamento novamente. Salve sua configuração.
>
>

Quando terminar, **selecione Salvar**.

## <a name="log-detailed-errors"></a>Registrar erros detalhados

Para salvar a página de erro ou o rastreamento de solicitação com falha para aplicativos do Windows no [portal Azure,](https://portal.azure.com)navegue até o seu aplicativo e selecione **os logs do App Service**.

Em **Registro de erro detalhado** ou rastreamento de solicitação com **falha,** selecione **Em,** em seguida, **selecione Salvar**.

Ambos os tipos de logs são armazenados no sistema de arquivos app service. Até 50 erros (arquivos/pastas) são retidos. Quando o número de arquivos HTML excede 50, os 26 erros mais antigos são automaticamente excluídos.

## <a name="add-log-messages-in-code"></a>Adicionar mensagens de log em código

No código do aplicativo, você usa as facilidades de registro habituais para enviar mensagens de registro para os logs do aplicativo. Por exemplo:

- Os aplicativos ASP.NET podem usar a classe [Rastreamento.de.Diagnóstico.de.Sistema](/dotnet/api/system.diagnostics.trace) para registrar informações no log de diagnóstico do aplicativo. Por exemplo:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Por padrão, ASP.NET Core usa o provedor de registro [Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Para obter mais informações, consulte [Registro do ASP.NET Core no Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Transmitir logs

Antes de fazer o streaming de logs em tempo real, habilite o tipo de log que deseja. Todas as informações gravadas em arquivos que terminam em .txt, .log ou .htm armazenados no diretório */LogFiles* (d:/home/logfiles) são transmitidas pelo App Service.

> [!NOTE]
> Alguns tipos de buffer de log gravam no arquivo de log, o que pode resultar em eventos com problemas na transmissão. Por exemplo, uma entrada para log de aplicativo, que ocorre quando um usuário visita uma página, pode ser exibida durante a transmissão antes da entrada de log HTTP correspondente para a solicitação da página.
>

### <a name="in-azure-portal"></a>No Portal do Azure

Para transmitir logs no [portal Azure,](https://portal.azure.com)navegue até o aplicativo e selecione **Log stream**. 

### <a name="in-cloud-shell"></a>Em Cloud Shell

Para transmitir logs ao vivo no [Cloud Shell,](../cloud-shell/overview.md)use o seguinte comando:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Para filtrar eventos específicos como erros, use o parâmetro **-Filtro** . Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Para filtrar tipos específicos de log como HTTP, use o parâmetro **-Caminho** . Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>No terminal local

Para transmitir logs no console local, [instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e [faça login na sua conta](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Uma vez conectado, seguiu as [instruções para Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Acessar arquivos de log

Se você configurar a opção Blobs de armazenamento do Azure para um tipo de log, você precisará de uma ferramenta cliente que funcione com o Azure Storage. Para obter mais informações, consulte [Azure Storage Client Tools](../storage/common/storage-explorers.md).

Para logs armazenados no sistema de arquivos app service, a maneira mais fácil é baixar o arquivo ZIP no navegador em:

- Aplicativos Linux/container:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplicativos do Windows:`https://<app-name>.scm.azurewebsites.net/api/dump`

Para aplicativos Linux/container, o arquivo ZIP contém logs de saída do console tanto para o host docker quanto para o contêiner docker. Para um aplicativo de saída dimensionada, o arquivo ZIP contém um conjunto de logs para cada instância. No sistema de arquivos App Service, esses arquivos de log são o conteúdo do diretório */home/LogFiles.*

Para aplicativos do Windows, o arquivo ZIP contém o conteúdo do diretório *D:\Home\LogFiles* no sistema de arquivos App Service. Ele contém a seguinte estrutura:

| Tipo de log | Diretório | Descrição |
|-|-|-|
| **Registros de aplicativos** |*/LogFiles/Application/* | Contém um ou mais arquivos de texto. O formato das mensagens de log depende do provedor de registro que você usa. |
| **Rastreamentos de solicitação com falha** | */LogFiles/W3SVC###########* | Contém arquivos XML e um arquivo XSL. Você pode visualizar os arquivos XML formatados no navegador. |
| **Registros de erro detalhados** | */LogFiles/DetailedErrors/* | Contém arquivos de erro HTM. Você pode visualizar os arquivos HTM no navegador.<br/>Outra maneira de visualizar os rastreamentos de solicitação sem falhas é navegar até a página do aplicativo no portal. No menu esquerdo, **selecione Diagnosticar e resolver problemas,** em seguida, procure registros **de rastreamento de solicitação falha,** em seguida, clique no ícone para navegar e visualizar o rastreamento desejado. |
| **Logs do servidor web** | */LogFiles/http/RawLogs/* | Contém arquivos de texto formatados usando o [formato de arquivo de log estendido W3C](/windows/desktop/Http/w3c-logging). Essas informações podem ser lidas usando um editor de texto ou um utilitário como [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>O App Service não `s-computername` `s-ip`suporta `cs-version` os campos ou campos. |
| **Logs de implantação** | */LogFiles/Git/* e */implantações/* | Contêm registros gerados pelos processos internos de implantação, bem como registros de implantações do Git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Enviar logs para o Azure Monitor (visualização)

Com a nova [integração do Azure Monitor,](https://aka.ms/appsvcblog-azmon)você pode [criar configurações de diagnóstico (visualização)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) para enviar logs para contas de armazenamento, hubs de eventos e análises de log. 

> [!div class="mx-imgBorder"]
> ![Configurações de diagnóstico (visualização)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipos de log suportados

A tabela a seguir mostra os tipos de log e descrições suportadas: 

| Tipo de log | Suporte ao Windows | Suporte ao Linux (Docker) | Descrição |
|-|-|-|
| AppServiceConsoleLogs | TBA | Sim | Saída padrão e erro padrão |
| AppServiceHTTPLogs | Sim | Sim | Logs do Web Server |
| AppServiceEnvironmentLogs | Sim | Sim | Ambiente de serviço do aplicativo: dimensionamento, alterações de configuração e registros de status|
| AppServiceAuditLogs | Sim | Sim | Atividade de login via FTP e Kudu |
| AppServiceFileAuditLogs | Sim | TBD | Alterações de arquivo via FTP e Kudu |
| AppServiceAppLogs | TBA | Java SE & Tomcat | Logs de aplicativo |

## <a name="next-steps"></a><a name="nextsteps"></a> Próximas etapas
* [Registros de consulta com o Monitor Do Azure](../azure-monitor/log-query/log-query-overview.md)
* [Como monitorar aplicativos no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
