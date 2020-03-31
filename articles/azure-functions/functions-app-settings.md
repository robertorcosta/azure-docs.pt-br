---
title: Referência de configurações de aplicativo para Azure Functions
description: Documentação de referência para as configurações de aplicativo ou variáveis de ambiente do Azure Functions.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3853ccbfd492bfaf4a82d62e6d31ab938285ee2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277824"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicativo para Azure Functions

As configurações de aplicativo em um aplicativo de funções contém opções de configuração global que afetam todas as funções desse aplicativo de funções. Quando você executa localmente, essas configurações são acessadas como [variáveis de ambiente](functions-run-local.md#local-settings-file). Este artigo lista as configurações de aplicativo disponíveis nos aplicativos de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Há outras opções de configuração global no arquivo [host.json](functions-host-json.md) e no arquivo [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Chave de instrumentação do Application Insights se você estiver usando o Application Insights. Consulte [Monitorar Azure Functions](functions-monitoring.md).

|Chave|Valor de exemplo|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Na versão 2.x e nas versões posteriores do tempo de execução Functions, configura o comportamento do aplicativo com base no ambiente de tempo de execução. Este valor é [lido durante a inicialização](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Você pode `AZURE_FUNCTIONS_ENVIRONMENT` definir para qualquer valor, mas [três valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) são suportados: [Desenvolvimento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Estadiamento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [Produção](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Quando `AZURE_FUNCTIONS_ENVIRONMENT` não é definido, ele `Development` é padrão `Production` em um ambiente local e no Azure. Esta configuração deve `ASPNETCORE_ENVIRONMENT` ser usada em vez de definir o ambiente de tempo de execução. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de conexão da conta de armazenamento opcional para armazenar logs e exibi-los na guia **Monitor** do portal. Esta configuração só é válida para aplicativos que visam a versão 1.x do tempo de execução funções do Azure. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Para saber mais, consulte [os requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Para melhor desempenho e experiência, as versões 2.x e posteriores `AzureWebJobsDashboard`usam APPINSIGHTS_INSTRUMENTATIONKEY e App Insights para monitoramento em vez de .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa desabilitar a página de aterrissagem padrão mostrada para a URL raiz de um aplicativo de funções. O padrão é `false`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando essa configuração de aplicativo é omitida ou definida como `false`, uma página semelhante ao exemplo a seguir é exibida na resposta à URL `<functionappname>.azurewebsites.net`.

![Página de aterrissagem do aplicativo de funções](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa usar o modo de versão durante a compilação do código .NET; `false` significa usar o modo de depuração. O padrão é `true`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas de recursos beta a habilitar. Os recursos beta habilitados por esses sinalizadores não estão prontos para produção, mas podem ser habilitados para uso experimental antes de serem lançados.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o provedor a ser usado para armazenar chaves. Atualmente, os repositórios com suporte são o armazenamento de blobs ("Blob") e o sistema de arquivos local ("Arquivos"). O padrão é blob na versão 2 e sistema de arquivos na versão 1.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsSecretStorageType|Arquivos|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O Azure Functions runtime usa essa cadeia de conexão da conta armazenamento para todas as funções, exceto para as funções disparadas por HTTP. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador usado para TypeScript. Permite substituir o padrão se necessário.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Dita se a edição no portal Azure está ativada. Os valores válidos são "readwrite" e "readonly".

|Chave|Valor de exemplo|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

A versão do runtime do Functions para usar nesse aplicativo de funções. Um til com a versão principal significa usar a versão mais recente da versão principal (por exemplo, "~2"). Quando novas versões da mesma versão principal estão disponíveis, elas são instaladas automaticamente no aplicativo de funções. Para fixar o aplicativo a uma versão específica, use o número de versão completo (por exemplo, "2.0.12345"). O padrão é "~ 2". Um valor de pinos `~1` seu aplicativo para a versão 1.x do runtime.

|Chave|Valor de exemplo|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNÇÕES\_V2\_\_MODO DE COMPATIBILIDADE

Esta configuração permite que seu aplicativo de função seja executado em um modo compatível com a versão 2.x na versão 3.x runtime. Use esta configuração somente se encontrar problemas ao [atualizar seu aplicativo de função da versão 2.x para 3.x do tempo de execução](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Esta configuração destina-se apenas como uma solução de curto prazo enquanto você atualiza seu aplicativo para ser executado corretamente na versão 3.x. Esta configuração é suportada desde que o [tempo de execução 2.x seja suportado](functions-versions.md). Se você encontrar problemas que impeçam o aplicativo de ser executado na versão 3.x sem usar esta configuração, por favor, [denuncie seu problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Requer que a VERSÃO DE `~3` [EXTENSÃO\_\_FUNCTIONS](functions-app-settings.md#functions_extension_version) seja definida como .

|Chave|Valor de exemplo|
|---|------------|
|FUNÇÕES\_V2\_\_MODO DE COMPATIBILIDADE|true|

## <a name="functions_worker_process_count"></a>FUNÇÕES\_\_CONTAGEM DE PROCESSOS DO\_TRABALHADOR

Especifica o número máximo de processos de trabalhador `1`de idiomas, com um valor padrão de . O valor máximo `10`permitido é . As invocações de funções são distribuídas uniformemente entre os processos do trabalhador linguístico. Os processos do trabalhador de idiomas são gerados\_a\_\_cada 10 segundos até que a contagem definida pela CONTAGEM DE PROCESSOS DO TRABALHADOR FUNCTIONS seja alcançada. Usar vários processos de trabalhador de idiomas não é o mesmo que [dimensionar](functions-scale.md). Considere usar essa configuração quando sua carga de trabalho tiver uma mistura de invocações vinculadas à CPU e à I/O. Esta configuração se aplica a todos os idiomas non-.NET.

|Chave|Valor de exemplo|
|---|------------|
|FUNÇÕES\_\_CONTAGEM DE PROCESSOS DO\_TRABALHADOR|2|


## <a name="functions_worker_runtime"></a>FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO

O runtime do trabalho de linguagem deve ser carregado no aplicativo de funções.  Isso irá corresponder ao idioma que está sendo usado em seu aplicativo (por exemplo, "dotnet"). Para funções em vários idiomas, você precisará publicá-las em vários aplicativos, cada um com um valor de runtime de trabalho correspondente.  Os valores válidos são `dotnet` `node` (C#/F#), `java` (JavaScript/TypeScript), (Java), `powershell` (PowerShell) e `python` (Python).

|Chave|Valor de exemplo|
|---|------------|
|FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Somente para consumo & planos Premium. Cadeia de conexão para a conta de armazenamento na qual o código do aplicativo de funções e a configuração são armazenados. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Somente para consumo & planos Premium. O caminho do arquivo para o código do aplicativo de funções e a configuração. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. O padrão é uma cadeia única que começa com o nome do aplicativo de funções. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

O número máximo de instâncias que o aplicativo de funções pode alcançar. O padrão é sem limites.

> [!NOTE]
> Essa configuração é uma versão prévia do recurso - e só confiável se definido como um valor < = 5

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Só para janelas._  
Define a versão do Node.js para usar ao executar seu aplicativo de função no Windows. Você deve usar um tilde (~) para que o tempo de execução use a versão mais recente disponível da versão principal direcionada. Por exemplo, quando `~10`definido para , a versão mais recente do Node.js 10 é usada. Quando uma versão principal é direcionada com um tilde, você não precisa atualizar manualmente a versão menor. 

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>SITE\_EXECUTAR\_DO\_PACOTE

Permite que seu aplicativo de funções execute de um arquivo de pacote montado.

|Chave|Valor de exemplo|
|---|------------|
|SITE\_EXECUTAR\_DO\_PACOTE|1|

Os valores válidos são `1` ou uma URL que resolve para o local de um arquivo de pacote de implantação. Quando definido como `1`, o pacote deve estar na pasta `d:\home\data\SitePackages`. Ao usar a implantação em zip com essa configuração, o pacote é automaticamente carregado para esse local. Na versão prévia, essa configuração foi nomeada `WEBSITE_RUN_FROM_ZIP`. Para obter mais informações, veja [Executar suas funções de um arquivo de pacote](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por padrão, os proxies de funções utilizarão um atalho para enviar chamadas à API de proxies diretamente para funções no mesmo aplicativo de funções, em vez de criar uma nova solicitação HTTP. Essa configuração permite que você desabilite esse comportamento.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Chamadas com uma URL backend apontando para uma função no aplicativo de função local não serão mais enviadas diretamente para a função e, em vez disso, serão direcionadas de volta para o front end HTTP para o Aplicativo de função|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Esse é o valor padrão. Chamadas com uma URL backend apontando para uma função no aplicativo de função local serão encaminhadas diretamente para essa função|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Essa configuração controla se %2F é decodificado como barras nos parâmetros de rota quando inseridos na URL de back-end. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Os parâmetros de rota com barras codificadas os terão decodificados. `example.com/api%2ftest` se tornará `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Esse é o comportamento padrão. Todos os parâmetros de rota serão passados inalterados|

### <a name="example"></a>Exemplo

Aqui, está um exemplo de proxy.json em um aplicativo de funções no myfunction.com da URL

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Decodificação URL|Entrada|Saída|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Próximas etapas

[Saiba como atualizar as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings)

[Consulte as configurações globais no arquivo host.json](functions-host-json.md)

[Consulte outras configurações de aplicativo para aplicativos do Serviço de Aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
