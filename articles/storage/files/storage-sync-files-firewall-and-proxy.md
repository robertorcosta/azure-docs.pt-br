---
title: Firewall local de Sincronização de Arquivos do Azure e configurações de proxy | Microsoft Docs
description: Configuração da rede local de Sincronização de Arquivos do Azure
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93681813c12f0df99909c849e57153e7a64c78fb
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299304"
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

| Serviço | Ponto de extremidade de nuvem pública | Ponto de extremidade do Azure Governamental | Uso |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Qualquer chamada de usuário (como o PowerShell) passa por essa URL, incluindo a chamada de registro inicial do servidor. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | As chamadas do Azure Resource Manager devem ser feitas por um usuário autenticado. Para ter êxito, essa URL é usada para autenticação do usuário. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Como parte da implantação de Sincronização de Arquivos do Azure, será criado um objeto de serviço do Azure Active Directory da assinatura. Essa URL é usada para fazer isso. Essa entidade de segurança é usada para a delegação de um conjunto mínimo de direitos para o Serviço de Sincronização de Arquivos do Azure. O usuário que estiver executando a configuração inicial de Sincronização de Arquivos do Azure deve ser um usuário autenticado com privilégios de proprietário da assinatura. |
| **Armazenamento do Azure** | &ast;.core.windows.net | &ast;. core.usgovcloudapi.net | Quando o servidor baixa um arquivo, o servidor executa essa movimentação de dados com mais eficiência quando se comunicando diretamente com o compartilhamento de arquivos do Azure na conta de armazenamento. O servidor tem uma chave SAS que só permite o acesso de compartilhamento do arquivo de destino. |
| **Sincronização de Arquivos do Azure** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Após o registro do servidor inicial, o servidor recebe uma URL regional para a instância do serviço de Sincronização de Arquivos do Azure nessa região. O servidor pode usar a URL para se comunicar de forma direta e eficiente com a instância de tratando sua sincronização. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Depois de instalar o agente da Sincronização de Arquivos do Azure, a URL do PKI é usada para baixar os certificados intermediários necessários para se comunicar com o serviço de Sincronização de Arquivos do Azure e do compartilhamento de arquivos do Azure. A URL do OCSP é usada para verificar o status de um certificado. |

> [!Important]
> Ao permitir o tráfego para o &ast;. one.microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Há muitos mais serviços da Microsoft nos subdomínios.

Se o &ast;. one.microsoft.com for muito amplo, você poderá limitar a comunicação do servidor permitindo a comunicação apenas com instâncias regionais explícitas do serviço Sincronização de Arquivos do Azure. Qual instância escolher depende da região do serviço de sincronização de armazenamento em que o servidor está implantado e registrado. Essa região é chamada de "URL do ponto de extremidade primário" na tabela abaixo.

Por motivos de BCDR (continuidade dos negócios e recuperação de desastres), você pode ter especificado os compartilhamentos de arquivos do Azure em uma conta de GRS (armazenamento com redundância global). Se esse for o caso, os compartilhamentos de arquivos do Azure farão failover na região emparelhada se ocorrer uma interrupção regional duradoura. A Sincronização de Arquivos do Azure usa os mesmos emparelhamentos regionais do armazenamento. Portanto, se você usar contas de armazenamento GRS, será necessário habilitar URLs adicionais para permitir que o servidor se comunique com a região emparelhada para Sincronização de Arquivos do Azure. A tabela a seguir chama essa "região emparelhada". Adicionalmente, há uma URL do perfil do gerenciador de tráfego que também precisa ser habilitada. Isso garantirá que o tráfego possa ser roteado novamente diretamente para a região emparelhada no caso de um failover e é chamado de "URL de Descoberta" na tabela abaixo.

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

> - https:\//kailani.one.microsoft.com (ponto de extremidade primário: oeste dos EUA)
> - https:\//kailani1.one.microsoft.com (região de failover emparelhada: leste dos EUA)
> - https:\//tm-kailani.one.microsoft.com (URL de descoberta da região primária)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lista de permissões para endereços IP de Sincronização de Arquivos do Azure
Se o firewall local exigir a adição de endereços IP específicos a uma lista de permissões para se conectar ao Sincronização de Arquivos do Azure, você poderá adicionar os seguintes intervalos de endereços IP com base nas regiões às quais você está se conectando.

| Região | Intervalos de endereços IP |
|--------|-------------------|
| Centro dos EUA | 52.176.149.179/32, 20.37.157.80/29 |
| Leste dos EUA 2 | 40.123.47.110/32, 20.41.5.144/29 |
| Leste dos EUA | 104.41.148.238/32, 20.42.4.248/29 |
| Centro-Norte dos EUA | 65.52.62.167/32, 40.80.188.24/29 |
| Centro-Sul dos Estados Unidos | 104.210.219.252/32, 13.73.248.112/29 |
| Oeste dos EUA 2 | 52.183.27.204/32, 20.42.131.224/29 |
| Centro-Oeste dos EUA | 52.161.25.233/32, 52.150.139.104/29 |
| Oeste dos EUA | 40.112.150.67/32, 40.82.253.192/29 |
| Canadá Central | 52.228.42.41/32, 52.228.81.248/29 |
| Leste do Canadá | 52.235.36.119/32, 40.89.17.232/29 |
| Sul do Brasil | 191.237.253.115/32, 191.235.225.216/29 |
| Norte da Europa | 40.113.94.67/32, 20.38.85.152/29 |
| Europa Ocidental | 104.40.191.8/32, 20.50.1.0/29 |
| França Central | 52.143.166.54/32, 20.43.42.8/29 |
| Sul da França | 52.136.131.99/32, 51.105.88.248/29 |
| Sul do Reino Unido | 51.140.67.72/32, 51.104.25.224/29 |
| Oeste do Reino Unido | 51.140.202.34/32, 51.137.161.240/29 |
| Norte da Suíça | 51.107.48.224/29 |
| Oeste da Suíça | 51.107.144.216/29 |
| Oeste da Noruega | 51.120.224.216/29 |
| Leste da Noruega | 51.120.40.224/29 |
| Leste da Ásia | 23.102.225.54/32, 20.189.108.56/29 |
| Sudeste Asiático | 13.76.81.46/32, 20.43.131.40/29 |
| Austrália Central | 20.37.224.216/29 |
| Austrália Central 2 | 20.36.120.216/29 |
| Leste da Austrália | 13.75.153.240/32, 20.37.195.96/29 |
| Sudeste da Austrália | 13.70.176.196/32, 20.42.227.128/29 |
| Sul da Índia | 104.211.231.18/32, 20.41.193.160/29 |
| Oeste da Índia | 52.136.48.216/29 |
| Leste do Japão | 104.41.161.113/32, 20.43.66.0/29 |
| Oeste do Japão | 23.100.106.151/32, 40.80.57.192/29 |
| Coreia Central | 52.231.67.75/32, 20.41.65.184/29 |
| Sul da Coreia | 52.231.159.38/32, 40.80.169.176/29 |
| DoD do Leste dos EUA | 20.140.72.152/29 |
| Governo dos EUA do Arizona | 20.140.64.152/29 |
| Governo dos EUA do Arizona | 52.244.75.224/32, 52.244.79.140/32 |
| US Gov Iowa | 52.244.79.140/32, 52.244.75.224/32 |
| Governo dos EUA do Texas | 52.238.166.107/32, 52.238.79.29/32 |
| Gov. dos EUA – Virgínia | 13.72.17.152/32, 52.227.153.92/32 |
| Norte da África do Sul | 102.133.175.72/32 |
| Oeste da África do Sul | 102.133.75.173/32, 102.133.56.128/29, 20.140.48.216/29 |
| EAU Central | 20.45.71.151/32, 20.37.64.216/29, 20.140.48.216/29 |
| Norte dos EAU | 40.123.216.130/32, 20.38.136.224/29, 20.140.56.136/29 |

## <a name="test-network-connectivity-to-service-endpoints"></a>Testar a conectividade de rede para pontos de extremidade de serviço
Depois que um servidor é registrado com o serviço de Sincronização de Arquivos do Azure, o cmdlet Test-StorageSyncNetworkConnectivity e o ServerRegistration. exe podem ser usados para testar as comunicações com todos os pontos de extremidade (URLs) específicos desse servidor. Esse cmdlet pode ajudar a solucionar problemas quando a comunicação incompleta impede que o servidor trabalhe totalmente com Sincronização de Arquivos do Azure e pode ser usado para ajustar as configurações de proxy e firewall.

Para executar o teste de conectividade de rede, instale Sincronização de Arquivos do Azure Agent versão 9,1 ou posterior e execute os seguintes comandos do PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Resumo e limitação de risco
As listas no início deste documento contém as URLs de Sincronização de Arquivos do Azure com que está se comunicando. Os firewalls devem permitir o tráfego de saída para esses domínios. A Microsoft se esforça para manter essa lista atualizada.

A configuração das regras de firewall de restrição de domínio pode ser uma medida para melhorar a segurança. Se essas configurações de firewall são utilizadas, é necessário ter em mente que URLs serão adicionadas e poderão até mesmo ser alteradas ao longo do tempo. Consulte este artigo periodicamente.

## <a name="next-steps"></a>Próximas etapas
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
