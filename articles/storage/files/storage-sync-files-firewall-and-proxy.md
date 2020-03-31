---
title: Firewall local de Sincronização de Arquivos do Azure e configurações de proxy | Microsoft Docs
description: Configuração da rede local de Sincronização de Arquivos do Azure
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294519"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Configurações de proxy e firewall da Sincronização de arquivos do Azure
A Sincronização de arquivos do Azure se conecta seus servidores locais para arquivos do Azure, permitindo camadas de recursos de nuvem e sincronização de vários locais. Como tal, um servidor local deve estar conectado à internet. Um administrador de TI precisa decidir o melhor caminho para o servidor acessar os serviços de nuvem do Azure.

Este artigo fornecerá informações sobre requisitos específicos e as opções disponíveis para conectar com êxito e segurança o servidor de Sincronização de Arquivos do Azure.

## <a name="overview"></a>Visão geral
A Sincronização de Arquivos do Azure atua como um serviço de coordenação entre o Windows Server, seu compartilhamento de arquivos do Azure e vários outros serviços do Azure para sincronizar os dados conforme descrito em seu grupo de sincronização. Para que a Sincronização de Arquivos do Azure funcione corretamente, você precisará configurar seus servidores para se comunicar com os seguintes serviços do Azure:

- Armazenamento do Azure
- Sincronização de Arquivos do Azure
- Azure Resource Manager
- Serviços de Autenticação

> [!Note]  
> O agente de Sincronização de Arquivos do Azure no Windows Server inicia todas as solicitações para serviços que resultam em apenas ter que considerar o tráfego de saída de uma perspectiva de firewall de nuvem. <br /> Nenhum serviço do Azure inicia uma conexão com o agente de sincronização de arquivos do Azure.

## <a name="ports"></a>Portas
A Sincronização de Arquivos do Azure move metadados e dados de arquivos exclusivamente via HTTPS e exige que a porta 443 tenha a saída aberta.
Como resultado, todo o tráfego é criptografado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e conexões especiais para o Azure
O agente de Sincronização de Arquivos do Azure não tem requisitos sobre canais especiais como [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. para o Azure.

A Sincronização de Arquivos do Azure funcionará por meios disponíveis que permitem o alcance no Azure, automaticamente se adaptando a várias características de rede com largura de banda, latência, bem como oferecendo controle de administração para ajustar. Alguns recursos não estão disponíveis neste momento. Se você quiser configurar o comportamento específico, fale por meio do [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
A Sincronização de Arquivos do Azure oferece suporte a configurações de proxy específicas do aplicativo e de todo o computador.

As **configurações de proxy específicas do aplicativo** permitirão a configuração de um proxy especificamente para o tráfego da Sincronização de Arquivos do Azure. As configurações de proxy específicas do aplicativo são suportadas no agente versão 4.0.1.0 ou mais recente e podem ser configuradas durante a instalação do agente ou usando o cmdlet do PowerShell Set-StorageSyncProxyConfiguration.

Comandos do PowerShell para definir as configurações de proxy específicas do aplicativo:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
As **configurações de proxy amplas do computador** são transparentes para o agente de Sincronização de Arquivos do Azure, pois o tráfego inteiro do servidor é roteado através do proxy.

Para definir as configurações de proxy amplas do computador, siga as etapas abaixo: 

1. Definir configurações de proxy para aplicativos .NET 

   - Edite esses dois arquivos:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adicione a seção <system.net> nos arquivos machine.config (abaixo da seção <system.serviceModel>).  Altere 127.0.01: 8888 para o endereço IP e porta para o servidor proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Definir as configurações de proxy do WinHTTP 

   - Execute o comando a seguir em um prompt de comandos com privilégios elevados ou no PowerShell para ver a configuração de proxy existente:   

     netsh winhttp show proxy

   - Execute o comando a seguir em um prompt de comandos com privilégios elevados ou no PowerShell para definir a configuração de proxy (altere 127.0.01: 8888 para o endereço IP e porta para o servidor proxy):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Reinicie o serviço do Agente de Sincronização de Armazenamento executando o comando a seguir em um prompt de comandos com privilégios elevados ou no PowerShell: 

      net stop filesyncsvc

      Observação: o serviço do Agente de Sincronização de Armazenamento (filesyncsvc) será iniciado automaticamente quando interrompido.

## <a name="firewall"></a>Firewall
Conforme mencionado em uma seção anterior, a porta 443 precisa estar com a saída aberta. Com base em políticas em seu banco de dados, a ramificação ou a região, restringir ainda mais o tráfego por essa porta a domínios específicos pode ser desejado ou necessário.

A tabela a seguir descreve os domínios necessários para a comunicação:

| Serviço | Ponto final da nuvem pública | Ponto de extremidade do Azure Governamental | Uso |
|---------|----------------|---------------|------------------------------|
| **Gerente de Recursos do Azure** | `https://management.azure.com` | https://management.usgovcloudapi.net | Qualquer chamada de usuário (como o PowerShell) passa por essa URL, incluindo a chamada de registro inicial do servidor. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | As chamadas do Azure Resource Manager devem ser feitas por um usuário autenticado. Para ter êxito, essa URL é usada para autenticação do usuário. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Como parte da implantação de Sincronização de Arquivos do Azure, será criado um objeto de serviço do Azure Active Directory da assinatura. Essa URL é usada para fazer isso. Essa entidade de segurança é usada para a delegação de um conjunto mínimo de direitos para o Serviço de Sincronização de Arquivos do Azure. O usuário que estiver executando a configuração inicial de Sincronização de Arquivos do Azure deve ser um usuário autenticado com privilégios de proprietário da assinatura. |
| **Armazenamento Azure** | &ast;.core.windows.net | &ast;Core.usgovcloudapi.net | Quando o servidor baixa um arquivo, o servidor executa essa movimentação de dados com mais eficiência quando se comunicando diretamente com o compartilhamento de arquivos do Azure na conta de armazenamento. O servidor tem uma chave SAS que só permite o acesso de compartilhamento do arquivo de destino. |
| **Sincronização de Arquivos do Azure** | &ast;. one.microsoft.com<br>&ast;Afs.azure.net. | &ast;Afs.azure.us | Após o registro do servidor inicial, o servidor recebe uma URL regional para a instância do serviço de Sincronização de Arquivos do Azure nessa região. O servidor pode usar a URL para se comunicar de forma direta e eficiente com a instância de tratando sua sincronização. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Depois de instalar o agente da Sincronização de Arquivos do Azure, a URL do PKI é usada para baixar os certificados intermediários necessários para se comunicar com o serviço de Sincronização de Arquivos do Azure e do compartilhamento de arquivos do Azure. A URL do OCSP é usada para verificar o status de um certificado. |

> [!Important]
> Ao permitir o tráfego para o &ast;. one.microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Há muitos mais serviços da Microsoft nos subdomínios.

Se o &ast;. one.microsoft.com for muito amplo, você poderá limitar a comunicação do servidor permitindo a comunicação apenas com instâncias regionais explícitas do serviço Sincronização de Arquivos do Azure. Qual instância escolher depende da região do serviço de sincronização de armazenamento em que o servidor está implantado e registrado. Essa região é chamada de "URL do ponto de extremidade primário" na tabela abaixo.

Por motivos de BCDR (continuidade dos negócios e recuperação de desastres), você pode ter especificado os compartilhamentos de arquivos do Azure em uma conta de GRS (armazenamento com redundância global). Se esse for o caso, os compartilhamentos de arquivos do Azure farão failover na região emparelhada se ocorrer uma interrupção regional duradoura. A Sincronização de Arquivos do Azure usa os mesmos emparelhamentos regionais do armazenamento. Portanto, se você usar contas de armazenamento GRS, você precisa habilitar URLs adicionais para permitir que seu servidor fale com a região emparelhada para o Azure File Sync. A tabela abaixo chama isso de "região pareada". Adicionalmente, há uma URL do perfil do gerenciador de tráfego que também precisa ser habilitada. Isso garantirá que o tráfego possa ser roteado novamente diretamente para a região emparelhada no caso de um failover e é chamado de "URL de Descoberta" na tabela abaixo.

| Nuvem  | Região | URL do ponto de extremidade primário | Região emparelhada | URL de descoberta |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https:\//kailani-aue.one.microsoft.com | Sudeste da Austrália | https:\//tm-kailani-aue.one.microsoft.com |
| Público |Sudeste da Austrália | https:\//kailani-aus.one.microsoft.com | Leste da Austrália | https:\//tm-kailani-aus.one.microsoft.com |
| Público | Sul do Brasil | https:\//brazilsouth01.afs.azure.net | Centro-Sul dos Estados Unidos | https:\//tm-brazilsouth01.afs.azure.net |
| Público | Canadá Central | https:\//kailani-cac.one.microsoft.com | Leste do Canadá | https:\//tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https:\//kailani-cae.one.microsoft.com | Canadá Central | https:\//tm-kailani.cae.one.microsoft.com |
| Público | Índia Central | https:\//kailani-cin.one.microsoft.com | Sul da Índia | https:\//tm-kailani-cin.one.microsoft.com |
| Público | Centro dos EUA | https:\//kailani-cus.one.microsoft.com | Leste dos EUA 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Público | Leste da Ásia | https:\//kailani11.one.microsoft.com | Sudeste Asiático | https:\//tm-kailani11.one.microsoft.com |
| Público | Leste dos EUA | https:\//kailani1.one.microsoft.com | Oeste dos EUA | https:\//tm-kailani1.one.microsoft.com |
| Público | Leste dos EUA 2 | https:\//kailani-ess.one.microsoft.com | Centro dos EUA | https:\//tm-kailani-ess.one.microsoft.com |
| Público | Leste do Japão | https:\//japaneast01.afs.azure.net | Oeste do Japão | https:\//tm-japaneast01.afs.azure.net |
| Público | Oeste do Japão | https:\//japanwest01.afs.azure.net | Leste do Japão | https:\//tm-japanwest01.afs.azure.net |
| Público | Coreia Central | https:\//koreacentral01.afs.azure.net/ | Sul da Coreia | https:\//tm-koreacentral01.afs.azure.net/ |
| Público | Sul da Coreia | https:\//koreasouth01.afs.azure.net/ | Coreia Central | https:\//tm-koreasouth01.afs.azure.net/ |
| Público | Centro-Norte dos EUA | https:\//northcentralus01.afs.azure.net | Centro-Sul dos Estados Unidos | https:\//tm-northcentralus01.afs.azure.net |
| Público | Norte da Europa | https:\//kailani7.one.microsoft.com | Europa Ocidental | https:\//tm-kailani7.one.microsoft.com |
| Público | Centro-Sul dos Estados Unidos | https:\//southcentralus01.afs.azure.net | Centro-Norte dos EUA | https:\//tm-southcentralus01.afs.azure.net |
| Público | Sul da Índia | https:\//kailani-sin.one.microsoft.com | Índia Central | https:\//tm-kailani-sin.one.microsoft.com |
| Público | Sudeste Asiático | https:\//kailani10.one.microsoft.com | Leste da Ásia | https:\//tm-kailani10.one.microsoft.com |
| Público | Sul do Reino Unido | https:\//kailani-uks.one.microsoft.com | Oeste do Reino Unido | https:\//tm-kailani-uks.one.microsoft.com |
| Público | Oeste do Reino Unido | https:\//kailani-ukw.one.microsoft.com | Sul do Reino Unido | https:\//tm-kailani-ukw.one.microsoft.com |
| Público | Centro-Oeste dos EUA | https:\//westcentralus01.afs.azure.net | Oeste dos EUA 2 | https:\//tm-westcentralus01.afs.azure.net |
| Público | Europa Ocidental | https:\//kailani6.one.microsoft.com | Norte da Europa | https:\//tm-kailani6.one.microsoft.com |
| Público | Oeste dos EUA | https:\//kailani.one.microsoft.com | Leste dos EUA | https:\//tm-kailani.one.microsoft.com |
| Público | Oeste dos EUA 2 | https:\//westus201.afs.azure.net | Centro-Oeste dos EUA | https:\//tm-westus201.afs.azure.net |
| Governamental | Governo dos EUA do Arizona | https:\//usgovarizona01.afs.azure.us | Governo dos EUA do Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Governamental | Governo dos EUA do Texas | https:\//usgovtexas01.afs.azure.us | Governo dos EUA do Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Se estiver utilizando contas de LRS (armazenamento com redundância local) ou ZRS (com redundância de zona), será necessário somente habilitar a URL listada em "URL do ponto de extremidade primário".

- Se estiver utilizando contas de GRS (armazenamento com redundância global), habilite três URLs.

**Exemplo:** você implanta um serviço de sincronização de armazenamento em `"West US"` e registra o servidor com ele. As URLs para permitir que o servidor comunique-se para esse caso são:

> - https:\//kailani.one.microsoft.com (ponto final primário: West US)
> - https:\//kailani1.one.microsoft.com (região de fail-over emparelhada: Leste dos EUA)
> - https:\//tm-kailani.one.microsoft.com (URL de descoberta da região primária)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Permitir lista para endereços IP do Azure File Sync
O Azure File Sync suporta o uso de tags de [serviço,](../../virtual-network/service-tags-overview.md)que representam um grupo de prefixos de endereço IP para um determinado serviço Azure. Você pode usar tags de serviço para criar regras de firewall que permitem a comunicação com o serviço Azure File Sync. A tag de serviço para `StorageSyncService`Azure File Sync é .

Se você estiver usando o Azure File Sync no Azure, você pode usar a tag nome de serviço diretamente no seu grupo de segurança de rede para permitir o tráfego. Para saber mais sobre como fazer isso, consulte [grupos de segurança da Rede](../../virtual-network/security-overview.md).

Se você estiver usando o Azure File Sync no local, você pode usar a API de tag de serviço para obter faixas de endereço IP específicas para a lista de permitir do firewall. Existem dois métodos para obter essas informações:

- A lista atual de faixas de endereços IP para todos os serviços do Azure que suportam tags de serviço são publicadas semanalmente no Microsoft Download Center a forma de um documento JSON. Cada nuvem do Azure tem seu próprio documento JSON com os intervalos de endereço IP relevantes para essa nuvem:
    - [Público do Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Governo dos EUA para Azure](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
- A API de detecção de marca de serviço (visualização) permite a recuperação programática da lista atual de tags de serviço. Na visualização, a API de detecção de marca de serviço pode retornar informações menos atuais do que as informações devolvidas dos documentos JSON publicados no Microsoft Download Center. Você pode usar a superfície da API com base na sua preferência de automação:
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Como a API de detecção de tag de serviço não é atualizada com tanta freqüência quanto os documentos JSON publicados no Microsoft Download Center, recomendamos o uso do documento JSON para atualizar a lista de permitir do firewall no local. Isso pode ser feito da seguinte maneira:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Em seguida, você pode usar `$ipAddressRanges` os intervalos de endereçoIP para atualizar seu firewall. Verifique o site do seu aparelho de firewall/rede para obter informações sobre como atualizar seu firewall.

## <a name="test-network-connectivity-to-service-endpoints"></a>Teste a conectividade da rede aos pontos finais de serviço
Uma vez que um servidor esteja registrado no serviço Azure File Sync, o cmdlet e o ServerRegistration.exe podem ser usados para testar comunicações com todos os pontos finais (URLs) específicos para este servidor. Este cmdlet pode ajudar a solucionar problemas quando a comunicação incompleta impede que o servidor funcione totalmente com o Azure File Sync e pode ser usado para ajustar as configurações de proxy e firewall.

Para executar o teste de conectividade de rede, instale o agente Azure File Sync versão 9.1 ou posterior e execute os seguintes comandos do PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Resumo e limitação de risco
As listas no início deste documento contém as URLs de Sincronização de Arquivos do Azure com que está se comunicando. Os firewalls devem permitir o tráfego de saída para esses domínios. A Microsoft se esforça para manter essa lista atualizada.

A configuração das regras de firewall de restrição de domínio pode ser uma medida para melhorar a segurança. Se essas configurações de firewall são utilizadas, é necessário ter em mente que URLs serão adicionadas e poderão até mesmo ser alteradas ao longo do tempo. Consulte este artigo periodicamente.

## <a name="next-steps"></a>Próximas etapas
- [Planejamento para uma implantação do Azure File Sync](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
