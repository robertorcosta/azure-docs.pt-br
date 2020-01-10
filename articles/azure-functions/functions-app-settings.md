---
title: Referência de configurações de aplicativo para Azure Functions
description: Documentação de referência para as configurações de aplicativo ou variáveis de ambiente do Azure Functions.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 4de6f093e43bbb8b3e258c3dd2a71f728beb7287
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769533"
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

Na versão 2. x e versões posteriores do tempo de execução do functions, o configura o comportamento do aplicativo com base no ambiente de tempo de execução. Esse valor é [lido durante a inicialização](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Você pode definir `AZURE_FUNCTIONS_ENVIRONMENT` para qualquer valor, mas há suporte para [três valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [desenvolvimento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [preparo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [produção](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Quando `AZURE_FUNCTIONS_ENVIRONMENT` não é definido, o padrão é `Development` em um ambiente local e `Production` no Azure. Essa configuração deve ser usada em vez de `ASPNETCORE_ENVIRONMENT` para definir o ambiente de tempo de execução. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de conexão da conta de armazenamento opcional para armazenar logs e exibi-los na guia **Monitor** do portal. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

> [!TIP]
> Para desempenho e experiência, é recomendável usar APPINSIGHTS_INSTRUMENTATIONKEY e App Insights para monitoramento, em vez de AzureWebJobsDashboard

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

O Azure Functions runtime usa essa cadeia de conexão da conta armazenamento para todas as funções, exceto para as funções disparadas por HTTP. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador usado para TypeScript. Permite substituir o padrão se necessário.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Determina se a edição no portal do Azure está habilitada. Os valores válidos são "readwrite" e "readonly".

|Chave|Valor de exemplo|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

A versão do runtime do Functions para usar nesse aplicativo de funções. Um til com a versão principal significa usar a versão mais recente da versão principal (por exemplo, "~2"). Quando novas versões da mesma versão principal estão disponíveis, elas são instaladas automaticamente no aplicativo de funções. Para fixar o aplicativo a uma versão específica, use o número de versão completo (por exemplo, "2.0.12345"). O padrão é "~ 2". Um valor de pinos `~1` seu aplicativo para a versão 1.x do runtime.

|Chave|Valor de exemplo|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>\_modo de\_de compatibilidade do Functions\_v2

Essa configuração permite que seu aplicativo de funções seja executado em um modo compatível com versão 2. x no tempo de execução da versão 3. x. Use essa configuração somente se encontrar problemas ao [atualizar seu aplicativo de funções da versão 2. x para 3. x do tempo de execução](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Essa configuração destina-se somente como uma solução alternativa de curto prazo enquanto você atualiza seu aplicativo para ser executado corretamente na versão 3. x. Há suporte para essa configuração, contanto que o [tempo de execução 2. x tenha suporte](functions-versions.md). Se você encontrar problemas que impeçam que seu aplicativo seja executado na versão 3. x sem usar essa configuração, informe [seu problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Requer que o [functions\_versão de\_de extensão](functions-app-settings.md#functions_extension_version) seja definido como `~3`.

|Chave|Valor de exemplo|
|---|------------|
|\_modo de\_de compatibilidade do Functions\_v2|true|

## <a name="functions_worker_process_count"></a>O Functions\_o processo de\_do trabalhador\_contagem

Especifica o número máximo de processos de trabalho de idioma, com um valor padrão de `1`. O valor máximo permitido é `10`. As invocações de função são distribuídas uniformemente entre os processos de trabalho de linguagem. Os processos de trabalho de idioma são gerados a cada 10 segundos até que a contagem definida por funções\_o processo de\_de trabalho\_contagem seja atingida. O uso de vários processos de trabalho de idioma não é o mesmo que o [dimensionamento](functions-scale.md). Considere usar essa configuração quando sua carga de trabalho tiver uma combinação de invocações associadas à CPU e de e/s. Essa configuração se aplica a todos os idiomas do non-.NET.

|Chave|Valor de exemplo|
|---|------------|
|O Functions\_o processo de\_do trabalhador\_contagem|2|


## <a name="functions_worker_runtime"></a>FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO

O runtime do trabalho de linguagem deve ser carregado no aplicativo de funções.  Isso irá corresponder ao idioma que está sendo usado em seu aplicativo (por exemplo, "dotnet"). Para funções em vários idiomas, você precisará publicá-las em vários aplicativos, cada um com um valor de runtime de trabalho correspondente.  Os valores válidos são `dotnet`C#(F#/), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chave|Valor de exemplo|
|---|------------|
|FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Para consumo & apenas planos Premium. Cadeia de conexão para a conta de armazenamento na qual o código do aplicativo de funções e a configuração são armazenados. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Para consumo & apenas planos Premium. O caminho do arquivo para o código do aplicativo de funções e a configuração. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. O padrão é uma cadeia única que começa com o nome do aplicativo de funções. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

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

_Somente Windows._  
Define a versão do node. js a ser usada ao executar seu aplicativo de funções no Windows. Você deve usar um til (~) para que o tempo de execução use a versão mais recente disponível da versão principal de destino. Por exemplo, quando definido como `~10`, a versão mais recente do node. js 10 é usada. Quando uma versão principal é destinada a um til, você não precisa atualizar manualmente a versão secundária. 

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>SITE\_EXECUTAR\_DO\_PACOTE

Permite que seu aplicativo de funções execute de um arquivo de pacote montado.

|Chave|Valor de exemplo|
|---|------------|
|SITE\_EXECUTAR\_DO\_PACOTE|1|

Os valores válidos são `1` ou uma URL que resolve para o local de um arquivo de pacote de implantação. Quando definido como `1`, o pacote deve estar na pasta `d:\home\data\SitePackages`. Ao usar a implantação em zip com essa configuração, o pacote é automaticamente carregado para esse local. Na versão prévia, essa configuração foi nomeada `WEBSITE_RUN_FROM_ZIP`. Para obter mais informações, veja [Executar suas funções de um arquivo de pacote](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por padrão, os proxies de funções utilizarão um atalho para enviar chamadas à API de proxies diretamente para funções no mesmo aplicativo de funções, em vez de criar uma nova solicitação HTTP. Essa configuração permite que você desabilite esse comportamento.

|Chave|Valor|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Chamadas com uma URL de back-end apontando para uma função no Aplicativo de funções local não serão mais enviadas diretamente para a função e, em vez disso, serão direcionadas de volta para o front-end HTTP para o Aplicativo de funções|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Esse é o valor padrão. Chamadas com uma URL de back-end apontando para uma função no Aplicativo de funções local serão encaminhadas diretamente para essa função|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Essa configuração controla se %2F é decodificado como barras nos parâmetros de rota quando inseridos na URL de back-end. 

|Chave|Valor|Description|
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


## <a name="next-steps"></a>Próximos passos

[Saiba como atualizar as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings)

[Consulte as configurações globais no arquivo host.json](functions-host-json.md)

[Consulte outras configurações de aplicativo para aplicativos do Serviço de Aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
