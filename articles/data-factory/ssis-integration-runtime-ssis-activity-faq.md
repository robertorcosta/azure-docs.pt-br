---
title: Solucionar problemas na execução do pacote no tempo de execução da integração do SSIS
description: Este artigo fornece orientação de solução de problemas para a execução do pacote SSIS no tempo de execução da integração do SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 1c2db107302e4851641ef430db61ec9b29ee151f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187475"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Solucionar problemas na execução do pacote no tempo de execução da integração do SSIS

Este artigo inclui os erros mais comuns que você pode encontrar quando está executando pacotes SSIS (SSIS) no tempo de execução de integração do SSIS. Descreve as possíveis causas e ações para resolver os erros.

## <a name="where-to-find-logs-for-troubleshooting"></a>Onde encontrar registros para solução de problemas

Use o portal Azure Data Factory para verificar a saída da atividade de execução do pacote SSIS. A saída inclui o resultado da execução, mensagens de erro e iD de operação. Para obter detalhes, consulte [Monitorar o pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Use o catálogo SSIS (SSISDB) para verificar os registros de detalhes para a execução. Para obter detalhes, consulte [Monitor's Running Packages and Other Operations](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Erros, causas e soluções comuns

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Mensagem de erro: "Tempo de conexão expirado" ou "O serviço encontrou um erro no processamento da sua solicitação. Tente novamente.”

Aqui estão as causas potenciais e as ações recomendadas:
* A fonte de dados ou destino está sobrecarregado. Verifique a carga na sua fonte de dados ou destino e veja se ela tem capacidade suficiente. Por exemplo, se você usou o Azure SQL Database, considere aumentar se o banco de dados provavelmente se apagar.
* A rede entre o tempo de execução da integração do SSIS e a fonte de dados ou destino é instável, especialmente quando a conexão é transversal ou entre o local e o Azure. Aplique o padrão de repetição no pacote SSIS seguindo estas etapas:
  * Certifique-se de que seus pacotes SSIS podem ser reexecutados em falha sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
  * Configure o intervalo **De tentativa** e **repetição** da atividade ![do pacote Execute **SSIS** na guia **Geral.** Defina propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Para obter um componente de origem ou destino de ADO.NET e OLE DB, defina **ConnectRetryCount** e **ConnectRetryInterval** no Connection Manager no pacote SSIS ou atividade SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Mensagem de erro: "ADO NET Source falhou em adquirir a conexão '...'" com "Ocorreu um erro relacionado à rede ou instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível."

Normalmente, esse problema significa que a fonte de dados ou o destino está inacessível pelo runtime de integração do SSIS. Os motivos podem variar. Tente estas ações:
* Certifique-se de que está passando a fonte de dados ou nome de destino/IP corretamente.
* Certifique-se de que o firewall está configurado corretamente.
* Certifique-se de que sua rede virtual esteja configurada corretamente se sua fonte de dados ou destino estiver no local:
  * Você pode verificar se o problema é da configuração de rede virtual, provisionando uma VM Azure na mesma rede virtual. Em seguida, verifique se a origem ou destino dos dados pode ser acessado a partir da VM do Azure.
  * Você pode encontrar mais detalhes sobre o uso de uma rede virtual com um tempo de execução de integração SSIS em [Participar de um tempo de execução de integração Azure-SSIS em uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Mensagem de erro: "ADO NET Source falhou em adquirir a conexão '...'" com "Não foi possível criar um gerenciador de conexão.".

A causa potencial é que o ADO.NET provedor usado no pacote não está instalado no tempo de execução de integração do SSIS. Você pode instalar o provedor usando uma configuração personalizada. Você pode encontrar mais detalhes sobre configuração personalizada na [configuração Personalizar para o tempo de execução de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Mensagem de erro: "A conexão '...' não é encontrado"

Um problema conhecido em versões mais antigas do SQL Server Management Studio (SSMS) pode causar esse erro. Se o pacote contiver um componente personalizado (por exemplo, SSIS Azure Feature Pack ou componentes de parceiro) que não esteja instalado no computador em que o SSMS é usado para fazer a implantação, o SSMS removerá o componente e causará o erro. Atualize o [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para a versão mais recente que tem o problema corrigido.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Mensagem de erro:"Código de saída do executor SSIS: -1073741819."

* Possível causa e ação recomendada:
  * Esse erro pode ser devido à limitação para a origem e destino do Excel quando várias fontes ou destinos do Excel estão sendo executados em paralelo em vários segmentos. Você pode contornar essa limitação alterando seus componentes do Excel para executá-los em seqüência ou separá-los em diferentes pacotes e acioná-los através de "Execute Package Task" com executeOutOfProcess definido como True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Mensagem de erro: "Não há espaço suficiente no disco"

Esse erro significa que o disco local está usado no nó de tempo de execução de integração ssis. Verifique se o pacote ou a configuração personalizada estão consumindo muito espaço em disco:
* Se o disco for consumido pelo pacote, ele será liberado após o término da execução do pacote.
* Se o disco for consumido pela sua configuração personalizada, você precisará interromper o tempo de execução da integração do SSIS, modificar seu script e iniciar o tempo de execução de integração novamente. Todo o recipiente de blob do Azure especificado para configuração personalizada será copiado para o nó de tempo de execução de integração ssis, então verifique se há algum conteúdo desnecessário esse contêiner.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Mensagem de erro: "Falha ao recuperar recurso do mestre. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Descrição:Falha no arquivo de carga "***".

* Possível causa e ação recomendada:
  * Se o SSIS Activity estiver executando o pacote do sistema de arquivos (arquivo de pacote ou arquivo de projeto), esse erro ocorrerá se o arquivo de projeto, pacote ou configuração não estiver acessível com a credencial de acesso ao pacote que você forneceu na Atividade SSIS
    * Se você estiver usando o Arquivo Azure:
      * O caminho do \\ \\ \<arquivo deve\>começar\\\<com o nome da conta de armazenamento .file.core.windows.net caminho de compartilhamento de arquivos\>
      * O domínio deve ser "Azure"
      * O nome de \<usuário deve ser nome da conta de armazenamento\>
      * A senha \<deve ser a chave de acesso de armazenamento\>
    * Se estiver usando o arquivo local, verifique se o VNet, a credencial de acesso ao pacote e a permissão estão configuradas corretamente para que o tempo de execução da integração do Azure-SSIS possa acessar o compartilhamento de arquivos no local

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Mensagem de erro: "O nome do arquivo '...' especificado na conexão não era válido"

* Possível causa e ação recomendada:
  * Um nome de arquivo inválido é especificado
  * Certifique-se de que você está usando FQDN (Nome de Domínio Totalmente Qualificado) em vez de pouco tempo em seu gerenciador de conexão

### <a name="error-message-cannot-open-file-"></a>Mensagem de erro: "Não é possível abrir o arquivo '...'"

Esse erro ocorre quando a execução do pacote não consegue encontrar um arquivo no disco local no tempo de execução da integração Do SSIS. Tente estas ações:
* Não use o caminho absoluto no pacote que está sendo executado no tempo de execução de integração do SSIS. Use o diretório de trabalho de execução atual (.) ou a pasta temporária (%TEMP%) Ao invés.
* Se você precisar persistir alguns arquivos nos nós de tempo de execução de integração do SSIS, prepare os arquivos conforme descrito na [configuração Personalizar](how-to-configure-azure-ssis-ir-custom-setup.md). Todos os arquivos do diretório de trabalho serão limpos após a execução ser concluída.
* Use arquivos Azure em vez de armazenar o arquivo no nó de tempo de execução de integração do SSIS. Para obter detalhes, consulte [Usar compartilhamentos de arquivos Do Zure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Mensagem de erro: "O banco de dados 'SSISDB' atingiu sua cota de tamanho"

Uma causa provável é que o banco de dados SSISDB criado no banco de dados SQL do Azure ou uma instância gerenciada atingiu a cota durante a criação de um Integration Runtime do SSIS. Tente estas ações:
* Considere aumentar a DTU do seu banco de dados. Você pode encontrar os detalhes em [Limites de recursos de Banco de Dados SQL para um servidor do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Verifique se o pacote geraria muitos logs. Nesse caso, você pode configurar um trabalho elástico para limpar esses logs. Para obter detalhes, consulte [Limpar os logs do SSISDB com trabalhos de Banco de Dados Elástico do Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Mensagem de erro: "O limite de solicitação para o banco de dados é ... e foi alcançado.

Se muitos pacotes estiverem sendo executados em paralelo no tempo de execução da integração do SSIS, esse erro pode ocorrer porque o SSISDB atingiu seu limite de solicitação. Considere aumentar o DTC do SSISDB para resolver esse problema. Você pode encontrar os detalhes em [Limites de recursos de Banco de Dados SQL para um servidor do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Mensagem de erro: "A operação SSIS falhou com o status de operação inesperado: ..."

O erro é causado principalmente por um problema transitório, então tente executar novamente a execução do pacote. Aplique o padrão de repetição no pacote SSIS seguindo estas etapas:

* Certifique-se de que seus pacotes SSIS podem ser reexecutados em falha sem efeitos colaterais (por exemplo, perda de dados ou duplicação de dados).
* Configure o intervalo **De tentativa** e **repetição** da atividade ![do pacote Execute **SSIS** na guia **Geral.** Defina propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Para obter um componente de origem ou destino de ADO.NET e OLE DB, defina **ConnectRetryCount** e **ConnectRetryInterval** no Connection Manager no pacote SSIS ou atividade SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Mensagem de erro: "Não há trabalhador ativo."

Esse erro geralmente significa que o tempo de execução de integração do SSIS tem um status insalubre. Verifique no portal do Azure o status e erros detalhados. Para obter mais informações, consulte [o tempo de execução da integração Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Mensagem de erro: "O tempo de execução da integração não pode ser atualizado e eventualmente parará de funcionar, uma vez que não podemos acessar o contêiner Azure Blob que você forneceu para configuração personalizada."

Esse erro ocorre quando o tempo de execução de integração do SSIS não pode acessar o armazenamento configurado para configuração personalizada. Verifique se o URI de assinatura de acesso compartilhado (SAS) fornecido é válido e não expirou.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Mensagem de erro: "Provedor de Serviços de Análise Do Microsoft OLE DB. 'Hresult: 0x80004005 Descrição:' Erro com: erro COM: mscorlib; A exceção foi lançada pelo alvo de uma invocação"

Uma causa potencial é que o nome de usuário ou senha com autenticação multifatorial do Azure ativado é configurado para autenticação do Azure Analysis Services. Essa autenticação não é suportada no tempo de execução da integração do SSIS. Tente usar um princípio de serviço para autenticação do Azure Analysis Services:

1. Prepare um diretor de serviço como descrito em [Automação com diretores de serviços.](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
2. No Gerenciador de conexões, configure **Use um nome de usuário e senha específicos**: defina o **AppID** como nome de usuário e **clienteSecret** como a senha.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Mensagem de erro: "A Fonte ADONET falhou em adquirir a conexão {GUID} com a seguinte mensagem de erro: Falha de login para o usuário 'NT AUTHORITY\ANONYMOUS LOGON'" ao usar uma identidade gerenciada

Certifique-se de que você não configure o método de autenticação do Gerenciador de conexões como **autenticação** de senha de diretório ativo quando o parâmetro *ConnectUsingUsedIdentity* for **True**. Em vez disso, você pode configurá-lo como **Autenticação SQL,** o que é ignorado se *ConnectUsingManagedIdentity* estiver definido.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Mensagem de erro: "0xC020801F em ..., OData Source [...]: Não é possível adquirir uma conexão gerenciada do gerenciador de conexão em tempo de execução"

Uma causa potencial é que o TLS (Transport Layer Security, segurança de camada de transporte) não está habilitado no tempo de execução de integração do SSIS, o que é exigido pela sua fonte OData. Você pode ativar o TLS no tempo de execução de integração do SSIS usando a configuração Personalizar. Mais detalhes podem ser encontrados no [Can't Connect Project Online Odata from SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) e [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Mensagem de erro: "Solicitar tarefa de preparação com guia de operação ... falha desde o erro: Falha ao despachar a operação de preparação com mensagem de erro: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Falha ao carregar proxy de dados."

Certifique-se de que o tempo de execução da integração Azure-SSIS esteja configurado com o tempo de execução de integração auto-hospedado. Mais detalhes podem ser encontrados no [Configure Self-Hosted IR como um proxy para Azure-SSIS IR no ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Mensagem de erro: "Status da tarefa de preparação: Falha. Erro de tarefa de preparação: ErrorCode: 2010, ErrorMessage: The Self-hosted Integration Runtime ... está offline"

Certifique-se de que o tempo de execução da integração auto-hospedado seja instalado e iniciado. Mais detalhes podem ser encontrados no [Criar e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Mensagem de erro: "Erro de tarefa de preparação: ErrorCode: 2906, ErrorMessage: Falha na execução do pacote., Saída: {"OperationErrorMessages": "Erro: o provedor OLE DB solicitado ... não está registrado. Se o driver de 64 bits não estiver instalado, execute o pacote no modo de 32 bits..."

Certifique-se de que os conectores OLE DB usados pelos conectores OLE DB no pacote estejam instalados corretamente na máquina de tempo de execução de integração auto-hospedada. Mais detalhes podem ser encontrados no [Configure Self-Hosted IR como um proxy para Azure-SSIS IR no ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Mensagem de erro: "Erro de tarefa de preparação: ErroCódigo: 2906, ErroMensagem: Falha na execução do pacote., Saída: {"OperationErrorMessages": "Erro: System.IO.FileLoadException: Não foi possível carregar arquivo ou montagem 'Microsoft.WindowsAzure.Armazenamento, Version=..., Culture=neutro, PublicKeyToken=31bf3856ad3644e35' ou uma de suas dependências. A definição de manifesto do conjunto localizado não corresponde à referência de montagem.' ..."

Uma causa em potencial é que o tempo de execução da integração auto-hospedado não é instalado ou atualizado corretamente. Sugerir baixar e reinstalar o tempo de execução de integração auto-hospedado mais recente. Mais detalhes podem ser encontrados no [Criar e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Mensagem de erro: "Uma conexão é necessária ao solicitar metadados. Se você estiver trabalhando offline, desmarque Work Offline no menu SSIS para ativar a conexão"

* Possível causa e ação recomendada:
  * Se houver também uma mensagem de aviso "O componente não suporta o uso do gerenciador de conexão com a configuração de valor ConnectByProxy verdadeira" no registro de execução, isso significa que um gerenciador de conexões é usado em um componente que ainda não ofereceu suporte a "ConnectByProxy". Os componentes suportados podem ser encontrados no [Configure Self-Hosted IR como um proxy para Azure-SSIS IR no ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * O registro de execução pode ser encontrado no [relatório SSMS](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) ou na pasta de log especificada na atividade de execução do pacote SSIS.
  * O vNet também pode ser usado para acessar dados locais como uma alternativa. Mais detalhes podem ser encontrados no [Join a azure-SSIS integration runtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Mensagem de erro: "Status da tarefa de preparação: Falha. Erro de tarefa de preparação: ErrorCode: 2906, ErrorMessage: Falha na execução do pacote., Saída: {"OperationErrorMessages": "Código de saída do executor SSIS: -1.\n", "LogLocation": "... \\SSISTelemetria\\ExecutionLog\\...", "efetivaIntegraçãoTempo": "...", "execuçãoDuração": ..., "duraçãoInQueue": { "integraçãoRuntimeQueue": ... }}"

Certifique-se de que o tempo de execução do Visual C++ esteja instalado na máquina de tempo de execução de integração auto-hospedada. Mais detalhes podem ser encontrados no [Configure Self-Hosted IR como um proxy para Azure-SSIS IR no ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Execuções de vários pacotes são desencadeadas inesperadamente

* Possível causa e ação recomendada:
  * A atividade de procedimento armazenada no ADF ou a atividade de aparência são usadas para desencadear a execução do pacote SSIS. O comando t-sql pode atingir o problema transitório e acionar a reprise, o que causaria várias execuções de pacotes.
  * Use ExecuteSSISAtividade do pacote, em vez disso, que garante que a execução do pacote não será reexecutada, a menos que o usuário defina a contagem de repetições em atividade. Detalhes podem ser encontrados em[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Refinar seu comando t-sql para poder reexecutar verificando se uma execução já foi acionada

### <a name="package-execution-takes-too-long"></a>A execução do pacote leva muito tempo

Aqui estão as causas potenciais e as ações recomendadas:

* Muitas execuções de pacotes foram programadas no tempo de execução da integração do SSIS. Todas essas execuções estarão esperando em uma fila para sua vez.
  * Determine o máximo usando esta fórmula:

    Contagem máxima de execução paralela por IR = Contagem de nós * Execução Paralela Máxima por Nó
  * Para saber como definir a contagem de nós e a execução paralela máxima por nó, consulte [Criar um tempo de execução de integração Azure-SSIS na Fábrica de Dados Azure](create-azure-ssis-integration-runtime.md).
* O tempo de execução da integração do SSIS é interrompido ou tem um status insalubre. Para saber como verificar o status e os erros de tempo de execução de integração do SSIS, consulte [o tempo de execução da integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Também recomendamos que você defina um ![tempo na guia](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) **Geral:** Defina propriedades na guia Geral .

### <a name="poor-performance-in-package-execution"></a>Desempenho ruim na execução de pacotes

Tente estas ações:

* Certifique-se de que o tempo de execução da integração do SSIS esteja na mesma região que a fonte de dados e o destino.

* Defina o nível de registro da execução do pacote como **Desempenho** para coletar informações de duração de cada componente na execução. Para obter detalhes, consulte [o registro de Serviços de Integração (SSIS).](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Verifique o desempenho do nó IR no portal Azure:
  * Para obter informações sobre como monitorar o tempo de execução da integração do SSIS, consulte [o tempo de execução da integração Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Você pode encontrar o histórico de CPU/memória para o tempo de execução de integração do SSIS, visualizando as métricas da fábrica de dados no portal Azure.
    ![Monitorar métricas do tempo de execução da integração do SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
