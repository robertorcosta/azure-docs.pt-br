---
title: Solucionar problemas de execução de integração auto-hospedada na Fábrica de Dados do Azure
description: Saiba como solucionar problemas de tempo de execução de integração auto-hospedados na Fábrica de Dados do Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414886"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Solucionar problemas de execução de integração auto-hospedada

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para tempo de execução de integração auto-hospedado na Fábrica de Dados Do Azure.

## <a name="common-errors-and-resolutions"></a>Erros comuns e resoluções

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Mensagem de erro: o tempo de execução de integração auto-hospedado não pode se conectar ao serviço de nuvem

![Problema de conexão de RI auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O tempo de execução de integração auto-hospedado não pode se conectar ao serviço Data Factory (backend). Esse problema é normalmente causado por configurações de rede no firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de tempo de execução de integração está sendo executado.
    
   ![Status de execução do serviço de RI auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se o serviço estiver em execução, vá para a etapa 3.

1. Se não houver proxy configurado no tempo de execução de integração auto-hospedado (que é a configuração padrão), execute o seguinte comando PowerShell na máquina onde o tempo de execução de integração auto-hospedado é instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A URL do serviço pode variar, dependendo da localização da Fábrica de Dados. Você pode encontrar a URL de serviço em**execuçãos de** > **integração** > de conexões **aDF UI** > Editar**URLs**de exibição de serviço de exibição**de dedos** > de IR > **auto-hospedados**.
            
    A seguir, a resposta esperada:
            
    ![Resposta de comando PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se você não receber a resposta esperada, use um dos seguintes métodos conforme apropriado à sua situação:
            
    * Se você receber uma mensagem "Nome remoto não pode ser resolvido", haverá um problema no Sistema de Nome de Domínio (DNS). Entre em contato com sua equipe de rede para corrigir esse problema.
    * Se você receber uma mensagem "ssl/tls cert não https://wu2.frontend.clouddatahub.net/ é confiável", verifique se o certificado é confiável na máquina e, em seguida, instale o certificado público usando o Certificate Manager. Essa ação deve atenuar a questão.
    * Vá ao **Visualizador** > de eventos do Windows **(logs)** > **Aplicativos e serviços Logs** > **Integração Tempo de execução** e verifique se há alguma falha causada pelo DNS, uma regra de firewall ou configurações da rede da empresa. (Se você encontrar tal falha, feche à força a conexão.) Como cada empresa tem configurações de rede personalizadas, entre em contato com sua equipe de rede para solucionar esses problemas.

1. Se "proxy" tiver sido configurado no tempo de execução de integração auto-hospedado, verifique se o servidor proxy pode acessar o ponto final do serviço. Para obter um comando de exemplo, consulte [PowerShell, solicitações da Web e proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

A seguir, a resposta esperada:
            
![Resposta de comando Powershell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações por proxy:
> *    Verifique se o servidor proxy precisa ser colocado na lista Destinatários Seguros. Nesse caso, certifique-se de que [esses domínios](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) estão na lista Destinatários Seguros.
> *    Verifique se o certificado TLS/SSL "wu2.frontend.clouddatahub.net/" é confiável no servidor proxy.
> *    Se você estiver usando a autenticação do Active Directory no proxy, altere a conta de serviço para a conta de usuário que pode acessar o proxy como "Serviço de execução de integração".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Mensagem de erro: O nó de tempo de execução de integração auto-hospedado/ sHIR lógico está no estado Inativo/ "Em execução (Limitada)"

#### <a name="cause"></a>Causa 

O nó de tempo de execução integrado auto-hospedado pode ter um status **inativo,** como mostrado na captura de tela a seguir:

![Nó IR auto-hospedado inativo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Esse comportamento ocorre quando os nódulos não podem se comunicar uns com os outros.

#### <a name="resolution"></a>Resolução

1. Faça login na VM hospedada no nó. Em **Aplicativos e Serviços Logs** > **Integração Tempo de execução,** abra o Visualizador de Eventos e filtre todos os registros de erro.

1. Verifique se um registro de erro contém o seguinte erro: 
    
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
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
