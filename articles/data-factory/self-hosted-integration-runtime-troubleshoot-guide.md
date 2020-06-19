---
title: Solução de problemas do runtime de integração auto-hospedada no Azure Data Factory
description: Saiba como solucionar problemas do runtime de integração auto-hospedada no Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 94e214c55a0109beb85cd08ce87303e5bd0f8016
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835420"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Solução de problemas do runtime de integração auto-hospedada

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para o runtime de integração auto-hospedada no Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Erros e resoluções comuns

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Mensagem de erro: O runtime de integração auto-hospedada não se conecta ao serviço de nuvem

![Problema de conexão do runtime de integração auto-hospedada](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O runtime de integração auto-hospedada não se conecta ao serviço do Data Factory (back-end). Esse problema normalmente é causado pelas configurações de rede no firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de runtime de integração está em execução.
    
   ![Status de execução do serviço de runtime de integração auto-hospedada](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se o serviço estiver em execução, passe para a etapa 3.

1. Se não houver nenhum proxy configurado no runtime de integração auto-hospedada (que é a configuração padrão), execute o seguinte comando do PowerShell no computador onde o runtime de integração auto-hospedada está instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A URL do serviço pode variar, dependendo da localização do Data Factory. Você pode encontrar a URL do serviço em **Interface do usuário do ADF** > **Conexões** > **Runtimes de integração** > **Editar runtime de integração auto-hospedada** > **Nós** > **Exibir URLs de serviço**.
            
    A resposta esperada é a seguinte:
            
    ![Resposta do comando do PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se não receber a resposta esperada, use um dos seguintes métodos conforme apropriado para sua situação:
            
    * Se receber uma mensagem “Não foi possível resolver o nome remoto”, há um problema no Sistema de Nomes de Domínio (DNS). Entre em contato com a equipe de rede para corrigir esse problema.
    * Se receber uma mensagem “certificado SSL/TLS não é confiável”, verifique se o certificado para https://wu2.frontend.clouddatahub.net/ é confiável no computador e, em seguida, instale o certificado público usando o Gerenciador de Certificados. Essa ação deve ajudar a resolver o problema.
    * Acesse **Windows** > **Visualizador de Eventos (logs)**  > **Aplicativos e Serviços** > **Runtime de Integração** e verifique se há alguma falha causada pelo DNS, por uma regra de firewall ou por configurações de rede da empresa. (Se encontrar uma falha, force o fechamento da conexão.) Como cada empresa tem configurações de rede personalizadas, entre em contato com a equipe de rede para solucionar esses problemas.

1. Se “proxy” tiver sido configurado no runtime de integração auto-hospedada, verifique se o servidor proxy pode acessar o ponto de extremidade de serviço. Para obter um comando de exemplo, veja [PowerShell, solicitações da Web e proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

A resposta esperada é a seguinte:
            
![Resposta 2 do comando do PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações para proxy:
> *    Verifique se o servidor proxy precisa ser colocado na lista de Destinatários seguros. Nesse caso, verifique se [esses domínios](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) estão na lista de Destinatários seguros.
> *    Verifique se o certificado TLS/SSL “wu2.frontend.clouddatahub.net/” é confiável no servidor proxy.
> *    Se estiver usando a autenticação do Active Directory no proxy, altere a conta de serviço para a conta de usuário que pode acessar o proxy como “Serviço do Runtime de Integração”.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Mensagem de erro: O nó do runtime de integração auto-hospedada/SHIR lógico está no estado Inativo/”Em execução (Limitado)”

#### <a name="cause"></a>Causa 

O nó do runtime de integração auto-hospedada pode ter um status **Inativo**, conforme mostrado na seguinte captura de tela:

![Nó de runtime de integração auto-hospedada inativa](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Esse comportamento ocorre quando os nós não conseguem se comunicar entre si.

#### <a name="resolution"></a>Resolução

1. Faça logon na VM hospedada no nó. Em **Logs de Aplicativos e Serviços** > **Runtime de Integração**, abra o Visualizador de Eventos e filtre todos os logs de erros.

1. Verifique se há um dos logs de erros contém o seguinte erro: 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A question page](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
