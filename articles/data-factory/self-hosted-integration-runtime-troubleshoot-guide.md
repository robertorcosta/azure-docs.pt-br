---
title: Solução de problemas do runtime de integração auto-hospedada no Azure Data Factory
description: Saiba como solucionar problemas do runtime de integração auto-hospedada no Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 07/19/2020
ms.author: abnarain
ms.openlocfilehash: 521756081db938e749849e6f3630dbd60700d24f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023799"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Solução de problemas do runtime de integração auto-hospedada

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para o runtime de integração auto-hospedada no Azure Data Factory.

## <a name="gather-self-hosted-integration-runtime-logs-from-azure-data-factory"></a>Coletar logs de tempo de execução de integração auto-hospedados de Azure Data Factory

Para atividades com falha em execução no ir de IR/compartilhado auto-hospedado, Azure Data Factory dá suporte à exibição e ao carregamento de logs de erro. Você pode seguir as etapas abaixo para obter a ID do relatório de erros e, em seguida, inserir a ID do relatório para localizar os problemas conhecidos relacionados.

1. Vá para a página **execuções de atividade** .

1. Na coluna **erro** , clique no botão abaixo.

    ![Página execuções de atividade](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Você verá os logs relacionados para a execução da atividade com falha. Clique no botão **enviar logs** para obter mais assistência.

    ![Enviar logs](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Você pode escolher os logs que deseja enviar. Para *ir via hospedagem interna*, você pode carregar logs relacionados à atividade com falha ou a todos os logs no nó ir de hospedagem interna. Para *ir compartilhado*, você só pode carregar logs relacionados à atividade com falha.

    ![Escolher logs](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Quando os logs estiverem sendo carregados, mantenha um registro da ID do relatório se precisar de assistência adicional para resolver o problema.

    ![Carregar logs](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> As solicitações de exibição de log e carregamento serão executadas em todas as instâncias de IR de hospedagem interna online. Certifique-se de que todas as instâncias de IR de hospedagem interna estejam online, caso haja algum log ausente. 


## <a name="common-errors-and-resolutions"></a>Erros e resoluções comuns

### <a name="error-message"></a>Mensagem de erro: 

`Self-hosted integration runtime can't connect to cloud service`

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

### <a name="error-message"></a>Mensagem de erro: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>Causa 

O nó do runtime de integração auto-hospedada pode ter um status **Inativo**, conforme mostrado na seguinte captura de tela:

![Nó de runtime de integração auto-hospedada inativa](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Esse comportamento ocorre quando os nós não conseguem se comunicar entre si.

#### <a name="resolution"></a>Resolução

1. Faça logon na VM hospedada no nó. Em **Logs de Aplicativos e Serviços** > **Runtime de Integração**, abra o Visualizador de Eventos e filtre todos os logs de erros.

1. Verifique se há um dos logs de erros contém o seguinte erro: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Se você vir esse erro, execute o seguinte em uma linha de comando: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Se você receber o erro a seguir, entre em contato com seu departamento de ti para obter ajuda com a correção desse problema. Depois que você puder fazer o telnet com êxito, entre em contato com Suporte da Microsoft se ainda tiver problemas com o status do nó de tempo de execução integradoras.
        
   ![Erro de linha de comando](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Verifique se o log de erros contém o seguinte:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Para resolver o problema, tente um ou ambos os métodos a seguir:
    - Coloque todos os nós no mesmo domínio.
    - Adicione o IP ao mapeamento de host em todos os arquivos de host da VM hospedada.


## <a name="troubleshoot-connectivity-issue"></a>Solucionar problema de conectividade

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Solucionar problemas de conectividade entre o IR e o Data Factory ou a fonte de dados e o coletor auto-hospedados

Para solucionar o problema de conectividade de rede, você deve saber como [coletar o rastreamento de rede](#how-to-collect-netmon-trace), entender como usá-lo e [analisar o rastreamento do Netmon](#how-to-analyze-netmon-trace) antes de aplicar as ferramentas do Netmon em casos reais do ir hospedado internamente.

Às vezes, quando solucionamos problemas de conectividade, como abaixo, entre o IR e o Data Factory de hospedagem interna: 

![Falha na solicitação HTTP](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Ou o único entre o IR e a fonte de dados/coletor auto-hospedados, encontrará os seguintes erros:

**Mensagem de erro:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Mensagem de erro:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Resolução:** Ao encontrar problemas acima, consulte as instruções a seguir para solucionar outros problemas:

Faça o rastreamento do Netmon e analise mais detalhadamente.
- Em primeiro lugar, você pode definir o filtro para ver qualquer redefinição ali do servidor para o lado do cliente. No exemplo abaixo, você pode ver que o lado do servidor é Data Factory Server.

    ![Servidor do data Factory](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Ao obter o pacote de redefinição, você pode encontrar a conversa seguindo o TCP.

    ![Localizar conversa](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Em seguida, você pode obter a conversão entre o cliente e o servidor de Data Factory abaixo removendo o filtro.

    ![Obter conversa](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Com base no rastreamento do Netmon coletado, podemos dizer que o total do TTL (TimeToLive) é 64. De acordo com os **valores de TTL e de limite de salto padrão** mencionados neste [artigo](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (como extraído abaixo), podemos ver que ele é o sistema Linux que redefine o pacote e causa a desconexão.

    Os valores de TTL e limite de salto padrão variam entre sistemas operacionais diferentes, aqui estão os padrões por alguns:
    - Kernel do Linux 2,4 (em 2001): 255 para TCP, UDP e ICMP
    - Kernel do Linux 4,10 (2015): 64 para TCP, UDP e ICMP
    - Windows XP (2001): 128 para TCP, UDP e ICMP
    - Windows 10 (2015): 128 para TCP, UDP e ICMP
    - Windows Server 2008:128 para TCP, UDP e ICMP
    - Windows Server 2019 (2018): 128 para TCP, UDP e ICMP
    - macOS (2001): 64 para TCP, UDP e ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    No entanto, ele é mostrado como 61 em vez de 64 no exemplo acima, porque, quando o pacote de rede chega ao destino, ele precisa passar por saltos diferentes, como roteadores/dispositivos de rede. O número de roteadores/dispositivos de rede será deduzido na TTL final.
    Nesse caso, podemos ver que a redefinição pode ser enviada do sistema Linux com o TTL 64.

- Precisamos verificar o quarto salto do IR auto-hospedado para confirmar de onde o dispositivo de redefinição pode vir.
 
    *Pacote de rede do sistema Linux A com TTL 64-> B TTL 64 menos 1 = 63-> C TTL 63 menos 1 = 62-> TTL 62 menos 1 = 61 IR auto-hospedado*

- Na situação ideal, o TTL será 128, o que significa que o sistema Windows está executando nossa Data Factory. Conforme mostrado no exemplo abaixo, *128 – 107 = 21 saltos*, o que significa que 21 saltos para o pacote foram enviados de data Factory para ir por hospedagem própria durante o handshake TCP 3.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Portanto, você precisa envolver a equipe de rede para verificar o que o quarto salto é do IR do tipo auto-hospedado. Se for o firewall como um sistema Linux, verifique todos os logs em que o dispositivo redefine o pacote após o handshake TCP 3. No entanto, se você não tiver certeza de onde fazer a investigação, tente obter o rastreamento de Netmon do IR e do firewall auto-hospedados juntos durante o tempo problemático para descobrir qual dispositivo pode redefinir esse pacote e causar a desconexão. Nesse caso, você também precisa envolver sua equipe de rede para avançar.

### <a name="how-to-collect-netmon-trace"></a>Como coletar rastreamento do Netmon

1.  Baixe as ferramentas do Netmon deste [site](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)e instale-as no computador do servidor (qualquer servidor que tenha o problema) e cliente (como o ir para hospedagem interna).

2.  Crie uma pasta, por exemplo, no seguinte caminho: *D:\netmon*. Verifique se ele tem espaço suficiente para salvar o log.

3.  Capture as informações de IP e porta. 
    1. Inicie um prompt CMD.
    2. Selecione executar como administrador e execute o seguinte comando:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Capture o rastreamento do Netmon (pacote de rede).
    1. Inicie um prompt CMD.
    2. Selecione executar como administrador e execute o seguinte comando:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Você pode usar três comandos diferentes para capturar a página de rede:
        - Opção A: comando de arquivo RoundRobin (isso capturará apenas um arquivo e substituirá os logs antigos).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Opção B: comando de arquivo encadeado (isso criará um novo arquivo se 200 MB for atingido).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Opção C: comando de arquivo agendado.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Pressione **Ctrl + C** para parar a captura do rastreamento do Netmon.
 
> [!NOTE]
> Se você só puder coletar o rastreamento do Netmon no computador cliente, obtenha o endereço IP do servidor para ajudá-lo a analisar o rastreamento.

### <a name="how-to-analyze-netmon-trace"></a>Como analisar o rastreamento do Netmon

Ao tentar fazer o Telnet **8.8.8.8 888** com o rastreamento do Netmon acima coletado, você deve ver o rastreamento abaixo:

![rastreamento do Netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![rastreamento do Netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Isso significa que não foi possível fazer a conexão TCP com o lado do servidor **8.8.8.8** com base na porta **888**, para que você veja dois pacotes adicionais do **SynReTransmit** lá. Como a **HOST2** de origem não pôde estabelecer a conexão com o **8.8.8.8** no primeiro pacote, ele continuará fazendo a conexão.

> [!TIP]
> - Você pode clicar em filtro de **carregamento**  ->  **padrão filtrar**  ->  **endereços**  ->  **IPv4**.
> - Insira **IPv4. Address = = 8.8.8.8** como filtro e clique em **aplicar**. Depois disso, você verá apenas a comunicação do computador local com o **8.8.8.8**de destino.

![filtrar endereços 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filtrar endereços 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

O exemplo abaixo mostra como seria um bom cenário. 

- Se o Telnet **8.8.8.8 53** estiver funcionando bem sem qualquer problema, você poderá ver o handshake TCP 3 acontecer e, em seguida, a sessão será concluída com o handshake TCP 4.

    ![bom exemplo de cenário 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![bom exemplo de cenário 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Com base no handshake TCP 3 acima, você pode ver o fluxo de trabalho abaixo:

    ![Fluxo de trabalho de handshake TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- O handshake TCP 4 para concluir a sessão e seu fluxo de trabalho serão mostrados como a seguir:

    ![Handshake de TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Fluxo de trabalho de handshake TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de perguntas e respostas da Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento](concepts-data-flow-performance.md)
