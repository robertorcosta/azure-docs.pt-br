---
title: Instruções detalhadas do Azure Application Insights Agent | Microsoft Docs
description: Instruções detalhadas para começar com o Application Insights Agent. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8f6134e8f8fdb9af3f578afaf0670c32a3896e01
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766871"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Agente de insights de aplicativos (anteriormente chamado De Status Monitor v2): Instruções detalhadas

Este artigo descreve como embarcar na Galeria PowerShell e baixar o módulo ApplicationMonitor.
Incluídos estão os parâmetros mais comuns que você precisará para começar.
Também fornecemos instruções de download manual caso você não tenha acesso à internet.

## <a name="get-an-instrumentation-key"></a>Obter uma chave de instrumentação

Para começar, você precisa de uma chave de instrumentação. Para obter mais informações, consulte [Criar um recurso de insights de aplicativos](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Execute powershell como admin com uma política de execução elevada

### <a name="run-as-admin"></a>Corra como Admin

O PowerShell precisa de permissões de nível de administrador para fazer alterações no computador.
### <a name="execution-policy"></a>Política de execução
- Descrição: Por padrão, a execução de scripts PowerShell está desativada. Recomendamos permitir scripts RemoteSigned apenas para o escopo Atual.
- Referência: [Sobre políticas de execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e política de [execução definida](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parâmetro opcional:
    - `-Force`. Ignora o aviso de confirmação.

**Erros de exemplo**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

Audite sua instância do `$PSVersionTable` PowerShell executando o comando.
Esse comando gera a seguinte saída:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Estas instruções foram escritas e testadas em um computador executando o Windows 10 e as versões listadas acima.

## <a name="prerequisites-for-powershell-gallery"></a>Pré-requisitos para A Galeria PowerShell

Essas etapas prepararão seu servidor para baixar módulos da Galeria PowerShell.

> [!NOTE] 
> O PowerShell Gallery é suportado no Windows 10, Windows Server 2016 e PowerShell 6.
> Para obter informações sobre versões anteriores, consulte [Installing PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Execute o PowerShell como Admin com uma política de execução elevada.
2. Instale o provedor de pacotes NuGet.
    - Descrição: Você precisa que este provedor interaja com repositórios baseados em NuGet, como o PowerShell Gallery.
    - Referência: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.
        - `-Force`. Ignora o aviso de confirmação.
    
    Você receberá este prompt se o NuGet não estiver configurado:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configure a PowerShell Gallery como um repositório confiável.
    - Descrição: Por padrão, a PowerShell Gallery é um repositório não confiável.
    - Referência: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parâmetro opcional:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.

    Você receberá este prompt se a PowerShell Gallery não for confiável:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Você pode confirmar essa alteração e auditar todos `Get-PSRepository` os PSRepositories executando o comando.

4. Instale a versão mais recente do PowerShellGet.
    - Descrição: Este módulo contém as ferramentas usadas para obter outros módulos da Galeria PowerShell. A versão 1.0.0.1 é embarcada com Windows 10 e Windows Server. A versão 1.6.0 ou superior é necessária. Para determinar qual versão está `Get-Command -Module PowerShellGet` instalada, execute o comando.
    - Referência: [Instalação do PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.
        - `-Force`. Ignora o aviso "já instalado" e instala a versão mais recente.

    Você receberá este erro se não estiver usando a versão mais recente do PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Reinicie o PowerShell. Você não pode carregar a nova versão na sessão atual. As novas sessões do PowerShell carregarão a versão mais recente do PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Baixe e instale o módulo via PowerShell Gallery

Essas etapas baixarão o módulo Az.ApplicationMonitor da PowerShell Gallery.

1. Certifique-se de que todos os pré-requisitos para a Galeria PowerShell sejam atendidos.
2. Execute o PowerShell como Admin com uma política de execução elevada.
3. Instale o módulo Az.ApplicationMonitor.
    - Referência: [Módulo de instalação](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parâmetros opcionais:
        - `-Proxy`. Especifica um servidor proxy para a solicitação.
        - `-AllowPrerelease`. Permite a instalação de versões alfa e beta.
        - `-AcceptLicense`. Ignora o prompt "Aceitar licença"
        - `-Force`. Ignora o aviso "Repositório Não Confiável".

## <a name="download-and-install-the-module-manually-offline-option"></a>Baixe e instale o módulo manualmente (opção offline)

Se por qualquer razão você não puder se conectar ao módulo PowerShell, você pode baixar e instalar manualmente o módulo Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Baixe manualmente o arquivo nupkg mais recente

1. Ir para https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selecione a versão mais recente do arquivo na tabela **Histórico de versão.**
3. Em **Opções de instalação,** selecione **Download manual**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opção 1: Instale em um diretório de módulos PowerShell
Instale o módulo PowerShell baixado manualmente em um diretório PowerShell para que ele seja descoberto pelas sessões do PowerShell.
Para obter mais informações, consulte [Instalando um módulo PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Descompactar nupkg como um arquivo zip usando expand-archive (v1.0.1.0)

- Descrição: A versão base do Microsoft.PowerShell.Archive (v1.0.1.0) não pode abrir zofo de arquivos nupkg. Renomeie o arquivo com a extensão .zip.
- Referência: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Descompactar nupkg usando expand-archive (v1.1.0.0)

- Descrição: Use uma versão atual do Expand-Archive para descompactar arquivos nupkg sem alterar a extensão.
- Referência: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opção 2: Descompactar e importar nupkg manualmente
Instale o módulo PowerShell baixado manualmente em um diretório PowerShell para que ele seja descoberto pelas sessões do PowerShell.
Para obter mais informações, consulte [Instalando um módulo PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).

Se estiver instalando o módulo em qualquer outro diretório, importe manualmente o módulo usando [o Módulo de Importação](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> DLLs serão instalados por caminhos relativos.
> Armazene o conteúdo do pacote no diretório de tempo de execução pretendido e confirme se as permissões de acesso permitem ler, mas não gravar.

1. Altere a extensão para ".zip" e extraia o conteúdo do pacote no diretório de instalação pretendido.
2. Encontre o caminho do arquivo de Az.ApplicationMonitor.psd1.
3. Execute o PowerShell como Admin com uma política de execução elevada.
4. Carregue o módulo `Import-Module Az.ApplicationMonitor.psd1` usando o comando.
    

## <a name="route-traffic-through-a-proxy"></a>Rota de tráfego através de um proxy

Quando você monitora um computador em sua intranet privada, você precisará encaminhar o tráfego HTTP através de um proxy.

Os comandos PowerShell para baixar e instalar o Az.ApplicationMonitor da Galeria PowerShell suportam um `-Proxy` parâmetro.
Revise as instruções anteriores ao escrever seus scripts de instalação.

O Application Insights SDK precisará enviar a telemetria do seu aplicativo para a Microsoft. Recomendamos que você configure configurações de proxy para o seu aplicativo em seu arquivo web.config. Para obter mais informações, consulte [FaQ do Application Insights: Passagem proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Habilitar o monitoramento

Use `Enable-ApplicationInsightsMonitoring` o comando para ativar o monitoramento.

Consulte a [referência da API](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring) para obter uma descrição detalhada de como usar este cmdlet.



## <a name="next-steps"></a>Próximas etapas

 Exiba sua telemetria:

- [Explorar métricas](../../azure-monitor/platform/metrics-charts.md) para monitorar o desempenho e o uso.
- [Pesquise eventos e registros](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar painéis](../../azure-monitor/app/overview-dashboard.md).

 Adicione mais telemetria:

- [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
- [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver exceções do código da página da Web e para ativar chamadas de rastreamento.
- [Adicione o Application Insights SDK ao seu código para](../../azure-monitor/app/asp-net.md) que você possa inserir chamadas de rastreamento e registro.

Faça mais com o Agente de Insights de Aplicativos:

- Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Agente de Insights de Aplicativos.
