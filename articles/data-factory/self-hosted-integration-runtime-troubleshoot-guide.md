---
title: Solução de problemas do runtime de integração auto-hospedada no Azure Data Factory
description: Saiba como solucionar problemas do runtime de integração auto-hospedada no Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: abnarain
ms.openlocfilehash: a6a0a62bd857dff575e17f47f1e2394375b08c45
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033652"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Solução de problemas do runtime de integração auto-hospedada

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para o runtime de integração auto-hospedada no Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Coletar logs de IR hospedados internamente de Azure Data Factory

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


## <a name="self-hosted-ir-general-failure-or-error"></a>Erro ou falha geral de IR de infravermelho auto-hospedado

### <a name="tlsssl-certificate-issue"></a>Problema de certificado TLS/SSL

#### <a name="symptoms"></a>Sintomas

Ao tentar habilitar o certificado TLS/SSL (avançado) do **ir auto-hospedado Configuration Manager**  ->  **acesso remoto da intranet**, depois de selecionar o certificado TLS/SSL, abaixo do erro aparecerá:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

No caso acima, o usuário está usando o certificado com "microsoft.com" como último item.

#### <a name="cause"></a>Causa

Este é um problema conhecido no WCF: a validação do WCF TLS/SSL verifica apenas a última DNSName em SAN. 

#### <a name="resolution"></a>Resolução

Há suporte para o certificado curinga no IR do Azure Data Factory v2 auto-hospedado. Esse problema normalmente ocorre porque o certificado SSL não está correto. O último DNSName em SAN deve ser válido. Siga as etapas abaixo para verificá-lo. 
1.  Abra o console de gerenciamento, verifique duas vezes o *assunto* e o *nome alternativo da entidade* nos detalhes do certificado. No caso acima, por exemplo, o último item no *nome alternativo da entidade*, que é "nome DNS = Microsoft.com.com", não é legítimo.
2.  Contate a empresa de problemas de certificado para remover o nome DNS errado.

### <a name="concurrent-jobs-limit-issue"></a>O problema de limite de trabalhos simultâneos

#### <a name="symptoms"></a>Sintomas

Ao tentar aumentar o limite de trabalhos simultâneos da interface do usuário do Azure Data Factory, ele é interrompido como *atualização* para sempre.
O valor máximo de trabalhos simultâneos foi definido como 24 e você deseja aumentar a contagem para que os trabalhos possam ser executados mais rapidamente. O valor mínimo que você pode inserir é 3 e o valor máximo que você pode inserir é 32. Você aumentou o valor de 24 para 32 e acertou no botão *Atualizar* , na interface do usuário que ele parou na *atualização* , como você pode ver abaixo. Após a atualização, o cliente ainda viu o valor como 24 e ele nunca foi atualizado para 32.

![Atualizando status](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Causa

Há uma limitação para a configuração à medida que o valor depende do computador logicCore e da memória, você pode apenas ajustá-lo a um valor menor, como 24, e ver o resultado.

> [!TIP] 
> - Para obter detalhes sobre o que é a contagem de núcleos de lógica e como localizar a contagem de núcleos de lógica do computador, consulte [Este artigo](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Para obter detalhes sobre como calcular o Math. log, consulte [Este artigo](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problema de certificado SSL de HA de IR via auto-hospedado

#### <a name="symptoms"></a>Sintomas

O nó de trabalho do IR auto-hospedado relatou o erro abaixo:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Causa

Quando manipulamos casos relacionados ao handshake SSL/TLS, podemos encontrar alguns problemas relacionados à verificação da cadeia de certificados. 

#### <a name="resolution"></a>Resolução

- Aqui está uma maneira rápida e intuitiva de solucionar problemas de falha de compilação da cadeia de certificados X. 509.
 
    1. Exporte o certificado, que precisa ser verificado. Vá para gerenciar certificado de computador e localize o certificado que você deseja verificar e clique com o botão direito do mouse em **todas as tarefas**  ->  **Exportar**.
    
        ![Exportar tarefas](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copie o certificado exportado para o computador cliente. 
    3. No lado do cliente, execute o comando abaixo no CMD. Certifique-se de que você substituiu abaixo *\<certificate path>* e *\<output txt file path>* espaços reservados com caminhos relacionados.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Por exemplo:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Verifique se há algum erro no arquivo txt de saída. Você pode encontrar o resumo de erros no final do arquivo txt.

        Por exemplo: 

        ![Resumo do erro](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Se você não vir nenhum erro no final do arquivo de log, conforme mostrado abaixo, você pode considerar a cadeia de certificados criada com êxito no computador cliente.
        
        ![Nenhum erro no arquivo de log](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Se houver AIA, CDP e OCSP configurados no arquivo de certificado. Podemos verificá-lo de uma maneira mais intuitiva.
 
    1. Você pode obter essas informações verificando os detalhes de um certificado.
    
        ![Detalhes do certificado](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Execute o comando abaixo. Verifique se você substituiu *\<certificate path>* o espaço reservado pelo caminho relacionado do certificado.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Em seguida, a **ferramenta de recuperação de URL** será aberta. Você pode verificar os certificados de AIA, CDP e OCSP clicando no botão **recuperar** .

        ![Botão de recuperação](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        A cadeia de certificados pode ser criada com êxito se o certificado de AIA for "verificado" e o certificado de CDP ou OCSP for "verificado".

        Se você encontrar uma falha ao recuperar o AIA, CDP, trabalhe com a equipe de rede para deixar o computador cliente pronto para se conectar à URL de destino. Ele será suficiente se o caminho http ou o caminho LDAP puder ser verificado.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>O IR auto-hospedado não pôde carregar o arquivo ou o assembly

#### <a name="symptoms"></a>Sintomas

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Por exemplo: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Causa

Se você usar o Process Monitor, poderá ver o seguinte resultado:

[![Monitor de processos](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Você pode definir o filtro conforme mostrado na captura de tela abaixo.
> Ele nos informa que o dll **System. ValueTuple** não está localizado na pasta relacionada ao GAC, ou em *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway*ou em *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* pasta.
> Basicamente, ele carregará a DLL da pasta do *GAC* primeiro e, em seguida, de uma pasta de *Gateway* *compartilhada* e finalmente. Portanto, você pode colocar a dll em qualquer caminho que possa ser útil.

![Configurar filtros](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Resolução

Você pode descobrir que o **System.ValueTuple.dll** está localizado em *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* pasta. Copie o **System.ValueTuple.dll** para *C:\Arquivos de Programas\Microsoft Integration Runtime\4.0\Gateway* pasta para resolver o problema.

Você pode usar o mesmo método para resolver problemas ausentes de outro arquivo ou assembly.

#### <a name="more-information"></a>Mais informações

O motivo pelo qual você vê o System.ValueTuple.dll em *%windir%\Microsoft.NET\assembly* e *%windir%\assembly* é que ele é um comportamento do .net. 

A partir do erro abaixo, você pode ver claramente o assembly *System. ValueTuple* não está lá. Assim, esse problema ocorre quando o aplicativo tenta verificar o assembly *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Para obter mais informações sobre o GAC, consulte [Este artigo](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Como auditar a chave de IR via hospedagem interna ausente

#### <a name="symptoms"></a>Sintomas

O tempo de execução de integração auto-hospedado deixa de repente offline sem chave, abaixo da mensagem de erro mostrada no log de eventos: `Authentication Key is not assigned yet`

![Chave de autenticação ausente](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Causa

- O nó IR auto-hospedado ou o IR via auto-hospedado lógico no portal é excluído.
- Uma desinstalação limpa é feita.

#### <a name="resolution"></a>Resolução

Se nenhuma das causas acima for aplicável, você poderá ir para a pasta: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway*e verificar se o arquivo chamado **configurações** foi excluído. Se ele for excluído, siga as instruções [aqui](https://www.netwrix.com/how_to_detect_who_deleted_file.html) para auditar quem exclui o arquivo.

![Verificar arquivo de configurações](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Não é possível usar o IR auto-hospedado para ligar dois armazenamentos de dados locais

#### <a name="symptoms"></a>Sintomas

Depois de criar o IRs autohospedado para os armazenamentos de dados de origem e destino, você deseja conectar os dois IRs juntos para concluir uma cópia. Se os armazenamentos de dados estiverem configurados em diferentes VNETs ou não conseguirem entender o mecanismo de gateway, você encontrará erros como: *o driver de origem não pode ser encontrado no ir de destino*; *a origem não pode ser acessada pelo ir de destino*.
 
#### <a name="cause"></a>Causa

O IR auto-hospedado é projetado como um nó central de uma atividade de cópia, não um agente cliente que precisa ser instalado para cada armazenamento de dados.
 
No caso acima, o serviço vinculado para cada repositório de dados deve ser criado com o mesmo IR e o IR deve ser capaz de acessar ambos os armazenamentos de dados por meio da rede. Independentemente de o IR ser instalado com o armazenamento de dados de origem, o armazenamento de dados de destino ou em um terceiro computador, se dois serviços vinculados forem criados com o IRs diferente, mas forem usados na mesma atividade de cópia, o IR de destino será usado e os drivers para os dois repositórios de dados precisarão ser instalados no computador IR de destino.

#### <a name="resolution"></a>Resolução

Instale os drivers para a origem e o destino no IR de destino e verifique se ele pode acessar o armazenamento de dados de origem.
 
Se o tráfego não puder passar pela rede entre dois repositórios de dados (por exemplo, eles estiverem configurados em duas VNETs), você não poderá concluir a cópia em uma atividade mesmo com o IR instalado. Nesse caso, você pode criar duas atividades de cópia com dois IRs, cada um em um ventilação: 1 IR para copiar do armazenamento de dados 1 para o armazenamento de BLOBs do Azure, outro para copiar do armazenamento de BLOBs do Azure para o armazenamento de dados 2. Isso poderia simular a necessidade de usar o IR para criar uma ponte que conecta dois armazenamentos de dados desconectados.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>O problema de sincronização de credenciais causa a perda de credenciais da HA

#### <a name="symptoms"></a>Sintomas

A credencial de fonte de dados "XXXXXXXXXX" é excluída do nó de Integration Runtime atual com carga "quando você exclui o serviço de link em portal do Azure ou a tarefa tem a carga incorreta, crie um novo serviço de link com sua credencial novamente".

#### <a name="cause"></a>Causa

O IR auto-hospedado é criado no modo de alta disponibilidade com dois nós, mas eles não estão no estado de sincronização de credenciais, o que significa que as credenciais armazenadas no nó Dispatcher não são sincronizadas com outros nós de trabalho. Se qualquer failover ocorrer do nó Dispatcher para o nó de trabalho, mas as credenciais existirem somente no nó Dispatcher anterior, a tarefa falhará ao tentar acessar as credenciais e você terá atingido o erro acima.

#### <a name="resolution"></a>Resolução

A única maneira de evitar esse problema é verificar se dois nós estão no estado de sincronização de credenciais. Caso contrário, você precisará reinserir as credenciais para o novo Dispatcher.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Não é possível escolher o certificado devido à chave privada ausente

#### <a name="symptoms"></a>Sintomas

1.  Importe um arquivo PFX para o repositório de certificados.
2.  Ao selecionar o certificado por meio da interface do usuário do Configuration Manager IR, você encontra o erro abaixo:

    ![Chave privada ausente](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Causa

- A conta de usuário está com privilégios baixos e não pode acessar a chave privada.
- O certificado foi gerado como assinatura, mas não como troca de chaves.

#### <a name="resolution"></a>Resolução

1.  Use uma conta com privilégios que possa acessar a chave privada para operar a interface do usuário.
2.  Execute o comando abaixo para importar o certificado:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Configuração de IR auto-hospedado

### <a name="the-integration-runtime-registration-error"></a>O erro de registro de Integration Runtime 

#### <a name="symptoms"></a>Sintomas

Às vezes, queremos executar o IR auto-hospedado em uma conta diferente pelos motivos como abaixo:
- A política da empresa não permite a conta de serviço.
- Algumas autenticações são necessárias.

Depois de alterar a conta de serviço no painel de serviço, você pode achar que o Integration Runtime para de funcionar.

![Erro de registro de IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Causa

Há muitos recursos que só são concedidos à conta de serviço. Ao alterar a conta de serviço para outra conta, a permissão de todos os recursos dependentes permanece a mesma.

#### <a name="resolution"></a>Resolução

Vá para o log de eventos Integration Runtime para verificar o erro.

![Log de eventos IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Se o erro aparecer como acima de *UnauthorizedAccessException*, siga as instruções abaixo:


1. Verifique a conta de serviço de logon do *DIAHostService* no painel de serviço do Windows.

    ![Conta de serviço de logon](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Verifique se a conta de serviço de logon tem a permissão R/W na pasta: *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway*.

    - Por padrão, se a conta de logon do serviço não tiver sido alterada, ela deverá ter a permissão de R/W.

        ![Permissão de serviço](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Se você alterou a conta de logon do serviço, siga as etapas abaixo para atenuar o problema:
        1. Limpar desinstalar o IR auto-hospedado atual.
        1. Instale os bits de IR hospedados internamente.
        1. Siga as instruções abaixo para alterar a conta de serviço: 
            1. Acesse a pasta de instalação do selfhosted IR, alterne para a pasta: *Microsoft Integration Runtime\4.0\Shared*.
            1. Inicie uma linha de comando usando privilégios elevados. Substitua *\<user>* e *\<password>* por seu próprio nome de usuário e senha e, em seguida, execute o comando abaixo:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Se você quiser alterar para a conta LocalSystem, certifique-se de usar um formato correto para essa conta. Abaixo está um exemplo do formato correto:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Não **use o** formato, conforme mostrado abaixo:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. Para uma alternativa, como o sistema local tem privilégios mais altos do que o administrador, você também pode alterá-lo diretamente em "serviços".
            1. Você pode usar o usuário local/domínio para a conta de logon do serviço IR.            
        1. Registre o Integration Runtime.

Se o erro aparecer como: o *serviço ' Integration Runtime Service ' (DIAHostService) falhou ao iniciar. Verifique se você tem privilégios suficientes para iniciar os serviços do sistema*, siga as instruções abaixo:

1. Verifique a conta de serviço de logon do *DIAHostService* no painel de serviço do Windows.
   
    ![Conta de serviço de logon](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Verifique se a conta de serviço de logon tem a permissão **fazer logon como um serviço** para iniciar o serviço do Windows:

    ![Fazer logon como serviço](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Mais informações

Se nenhum dos dois padrões acima na resolução se aplicar no seu caso, tente coletar os logs de eventos do Windows abaixo: 
- Logs de aplicativos e serviços-> Integration Runtime
- Logs do Windows-> aplicativo

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Não é possível localizar o botão registrar para registrar um IR auto-hospedado    

#### <a name="symptoms"></a>Sintomas

Não foi possível encontrar o botão **registrar** na interface do usuário do Configuration Manager ao registrar um ir via hospedagem interna.

![Nenhum botão registrar](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Causa

Desde o lançamento do *Integration Runtime 3,0*, o botão **registrar** em um nó Integration Runtime existente foi removido para habilitar um ambiente mais limpo e seguro. Se um nó foi registrado em algum Integration Runtime (online ou não), para registrá-lo novamente em outro Integration Runtime, desinstale o nó anterior e, em seguida, instale e registre o nó.

#### <a name="resolution"></a>Resolução

1. Vá para o painel de controle para desinstalar o Integration Runtime existente.

    > [!IMPORTANT] 
    > No processo abaixo, selecione Sim. Não mantenha os dados durante o processo de desinstalação.

    ![Excluir dados](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Se você não tiver o MSI do Integration Runtime Installer, vá para o [centro de download](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) para baixar o Integration Runtime mais recente.
1. Instale o MSI e registre o Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Não é possível registrar o IR auto-hospedado interno devido a localhost    

#### <a name="symptoms"></a>Sintomas

Não é possível registrar o IR auto-hospedado em um novo computador quando get_LoopbackIpOrName.

**Depurar:** Ocorreu um erro de tempo de execução.
O inicializador de tipo para ' Microsoft. DataTransfer. DIAgentHost. DataSourceCache ' lançou uma exceção.
Ocorreu um erro não recuperável durante uma pesquisa de banco de dados.
 
**Detalhe da exceção:** System. TypeInitializationException: o inicializador de tipo para ' Microsoft. DataTransfer. DIAgentHost. DataSourceCache ' lançou uma exceção. ---> System .net. Sockets. SocketException: ocorreu um erro não recuperável durante uma pesquisa de banco de dados em System .net. DNS. GetAddrInfo (nome da cadeia de caracteres).

#### <a name="cause"></a>Causa

O problema geralmente acontece ao resolver o localhost.

#### <a name="resolution"></a>Resolução

Use localhost 127.0.0.1 para hospedar o arquivo e resolver esse problema.


### <a name="self-hosted-setup-failed"></a>Falha na instalação auto-hospedada    

#### <a name="symptoms"></a>Sintomas

Não é possível desinstalar um IR existente ou instalar um novo IR ou atualizar um IR existente para um novo IR.

#### <a name="cause"></a>Causa

A instalação depende do serviço de Windows Installer. Há motivos de variante que podem causar problemas de instalação:
- Não há espaço em disco suficiente
- Falta de permissões
- O serviço NT está bloqueado por algum motivo
- A utilização da CPU é muito alta
- O arquivo MSI está hospedado em um local de rede lento
- Alguns arquivos do sistema ou registros foram tocadas sem intenção


## <a name="self-hosted-ir-connectivity-issues"></a>Problemas de conectividade de IR via hospedagem interna

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>O tempo de execução de integração auto-hospedado não pode se conectar ao serviço de nuvem

#### <a name="symptoms"></a>Sintomas

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

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Problema de conectividade entre o IR e o Data Factory ou a fonte de dados e o coletor auto-hospedados

Para solucionar o problema de conectividade de rede, você deve saber como coletar o rastreamento de rede, entender como usá-lo e [analisar o rastreamento do Netmon](#how-to-analyze-netmon-trace) antes de aplicar as ferramentas do Netmon em casos reais do ir hospedado internamente.

#### <a name="symptoms"></a>Sintomas

Às vezes, quando solucionamos problemas de conectividade, como abaixo, entre o IR e o Data Factory de hospedagem interna: 

![Falha na solicitação HTTP](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Ou o único entre o IR e a fonte de dados/coletor auto-hospedados, encontrará os seguintes erros:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Solução:

Ao encontrar problemas acima, consulte as instruções a seguir para solucionar outros problemas:

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

1.  Baixe as ferramentas do Netmon deste [site](https://cnet-downloads.com/network-monitor)e instale-as no computador do servidor (qualquer servidor que tenha o problema) e cliente (como o ir para hospedagem interna).

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

> [!NOTE] 
> A instrução abaixo é aplicável ao rastreamento do Netmon. Como o Netmon Trace está atualmente sem suporte, você pode aproveitar o Wireshark da mesma forma.

Ao tentar fazer o Telnet **8.8.8.8 888** com o Netmon Trace coletado, você deve ver o rastreamento abaixo:

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


## <a name="self-hosted-ir-sharing"></a>Compartilhamento do IR auto-hospedado

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Não há suporte para compartilhamento de IR auto-hospedado de um locatário diferente 

#### <a name="symptoms"></a>Sintomas

Você pode observar outras fábricas de dados (em locatários diferentes) durante a tentativa de compartilhar o IR auto-hospedado da interface do usuário Azure Data Factory, mas não pode compartilhar o IR hospedado internamente em fábricas de dados que estão em locatários diferentes.

#### <a name="cause"></a>Causa

O IR auto-hospedado não pode ser compartilhado entre locatários.


## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de perguntas e respostas da Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento](concepts-data-flow-performance.md)
