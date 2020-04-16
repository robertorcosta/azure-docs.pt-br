---
title: Personalize a configuração para um runtime de integração Azure-SSIS
description: Este artigo descreve como usar a interface de configuração personalizada para um Tempo de Execução de Integração Azure-SSIS para instalar componentes adicionais ou alterar configurações
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 3aabf68b6763e5815a03021cb02683d509c26190
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415063"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalize a configuração para um runtime de integração Azure-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A configuração personalizada para um Azure-SQL Server Integration Services Runtime (Azure-SSIS IR) fornece uma interface para adicionar suas próprias etapas durante a configuração ou reconfiguração do seu IR Azure-SSIS. 

Usando a configuração personalizada, você pode alterar a configuração ou ambiente operacional padrão para, por exemplo, iniciar serviços adicionais do Windows, persistir credenciais de acesso para compartilhamentos de arquivos ou usar criptografia forte/protocolo de rede mais seguro (TLS 1.2). Ou você pode instalar componentes adicionais, como conjuntos, drivers ou extensões, em cada nó do seu IR Azure-SSIS.

Você pode fazer configurações personalizadas no seu Azure-SSIS IR de duas maneiras: 
* **Configuração personalizada expressa sem um script**: Execute algumas configurações comuns do sistema e comandos do Windows ou instale alguns componentes adicionais populares ou recomendados sem usar nenhum script.
* **Configuração personalizada padrão com um script**: Prepare um script e seus arquivos associados e carregue-os todos juntos em um recipiente blob em sua conta de armazenamento Azure. Em seguida, você fornece um Uri (Uniform Resource Identifier, assinatura de acesso compartilhado) para o seu contêiner quando configurar ou reconfigurar seu IR Azure-SSIS. Cada nó do seu Azure-SSIS IR então baixa o script e seus arquivos associados do seu contêiner e executa sua configuração personalizada com permissões elevadas. Quando sua configuração personalizada é concluída, cada nó carrega a saída padrão de execução e outros logs para o seu contêiner.

Você pode instalar componentes gratuitos e não licenciados e componentes pagos com configurações personalizadas expressas e padrão. Se você é um fornecedor de software independente (ISV), consulte [Desenvolver componentes pagos ou licenciados para um IR Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Para se beneficiar de aprimoramentos futuros, recomendamos o uso de séries v3 ou posteriores de nós para o seu Ir Azure-SSIS com configuração personalizada.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam apenas às configurações personalizadas padrão:

- Se você quiser usar *gacutil.exe* em seu script para instalar conjuntos no cache de montagem global (GAC), você precisa fornecer *gacutil.exe* como parte de sua configuração personalizada. Ou você pode usar a cópia fornecida em nosso contêiner *de visualização pública,* discutida mais tarde na seção "Instruções".

- Se você quiser referenciar uma subpasta em seu script, *msiexec.exe* não suporta a notação `.\` para referenciar a pasta raiz. Use um comando `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` como `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`em vez de .

- As ações administrativas, ou compartilhamentos de rede ocultos criados automaticamente pelo Windows, não são suportadas no IR do Azure-SSIS.

- O driver IBM iSeries Access ODBC não é suportado no IR Azure-SSIS. Você pode ver erros de instalação durante sua configuração personalizada. Se você fizer isso, entre em contato com o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar seu IR Azure-SSIS, você precisa dos seguintes itens:

- [Uma assinatura do Azure](https://azure.microsoft.com/)

- [Provisionar o IR do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Uma conta de armazenamento Azure.](https://azure.microsoft.com/services/storage/) Não é necessário para configurações personalizadas expressas. Para configurações personalizadas padrão, você carrega e armazena seu script de configuração personalizado e seus arquivos associados em um recipiente blob. O processo de instalação personalizado também carrega seus logs de execução no mesmo contêiner de blob.

## <a name="instructions"></a>Instruções

1. Se você quiser configurar ou reconfigurar seu Azure-SSIS IR com o PowerShell, baixe e [instale o Azure PowerShell](/powershell/azure/install-az-ps). Para configurações personalizadas expressas, pule para o passo 4.

1. Prepare o script de instalação personalizado e seus arquivos associados (por exemplo, arquivos .bat, .cmd, .exe, .dll, .msi ou .ps1).

   * Você deve ter um arquivo de script chamado *main.cmd*, que é o ponto de entrada da sua configuração personalizada.  
   * Para garantir que o script possa ser executado silenciosamente, recomendamos que você o teste em sua máquina local primeiro.  
   * Se você quiser que logs adicionais gerados por outras ferramentas (por exemplo, *msiexec.exe*) sejam carregados no seu contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR`como a pasta de log em seus scripts (por exemplo, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Baixe, instale e abra [o Azure Storage Explorer](https://storageexplorer.com/). Para fazer isso:

   a. Em **(Local e Anexo)**, clique com o botão direito **do mouse Em Contas de armazenamento**e, em seguida, **selecione Conectar ao armazenamento Azure**.

      ![Conectar-se a um Armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecione **Usar o nome e a chave da conta de armazenamento**e, em seguida, selecione **Next**.

      ![Usar um nome e uma chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Digite o nome e a chave da conta de armazenamento do Azure, selecione **Next**e selecione **Conectar**.

      ![Forneça o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Em sua conta de armazenamento Azure conectada, clique com o botão direito do mouse **Blob Containers,** selecione **Criar recipiente blob**e nomeie o novo contêiner.

      ![Criar um contêiner de blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecione o novo contêiner e carregue seu script de configuração personalizado e seus arquivos associados. Certifique-se de carregar *o main.cmd* no nível superior do seu recipiente, não em nenhuma pasta. Certifique-se também de que o contêiner contenha apenas os arquivos de configuração personalizados necessários, de modo que baixá-los para o seu Azure-SSIS IR mais tarde não levará muito tempo. A duração máxima de uma configuração personalizada é atualmente definida em 45 minutos antes de ser programada. Isso inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no IR Azure-SSIS. Se a configuração exigir mais tempo, aumente um bilhete de suporte.

      ![Carregar arquivos no contêiner de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Clique com o botão direito do mouse no contêiner e **selecione Obter assinatura de acesso compartilhado**.

      ![Obter Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Crie o Uri SAS para o seu contêiner com um tempo de expiração suficientemente longo e com permissão de leitura/gravação/lista. Você precisa que o Uri SAS baixe e execute seu script de configuração personalizado e seus arquivos associados sempre que qualquer nó do seu IR Azure-SSIS for reiniciado ou reiniciado. Você precisa gravar permissão para carregar os logs de execução da instalação.

      > [!IMPORTANT]
      > Certifique-se de que o Uri SAS não expire e que os recursos de configuração personalizados estejam sempre disponíveis durante todo o ciclo de vida do seu IR Azure-SSIS, desde a criação até a exclusão, especialmente se você parar regularmente e iniciar o SEU IR Azure-SSIS durante este período.

      ![Gerar Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copie e salve o URI de SAS do seu contêiner.

      ![Copie e salve a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Ao configurar ou reconfigurar seu IR Azure-SSIS com uma uI de fábrica de dados, você pode adicionar ou remover configurações personalizadas selecionando o **Tempo de integração Do Azure-SSIS com configurações adicionais/instalações de componentes** caixa de seleção na seção **Configurações Avançadas** do painel **de configuração de execução Integração.** 

   Se você quiser adicionar configurações personalizadas padrão, insira o Uri SAS do seu contêiner na caixa **Uri do contêiner de configuração personalizada SAS.** 
   
   Se você quiser adicionar configurações personalizadas expressas, selecione **Novo** para abrir o painel **de configuração personalizado do Add** e, em seguida, selecione um tipo na lista de sossegado do tipo de **configuração personalizada do Expresso:**

   * Se você selecionar o tipo **de comando Executar cmdkey,** poderá persistir as credenciais de acesso para seus compartilhamentos de arquivos ou compartilhamentos de Arquivos Azure no Azure-SSIS IR inserindo o nome do computador ou nome de domínio, nome da conta ou nome de usuário e chave de conta ou senha nas caixas **/Add**/ **/User**e **/Pass.** Isso é semelhante ao executar o comando [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) do Windows em sua máquina local.
   
   * Se você selecionar o tipo **de variável de ambiente Adicionar,** você poderá adicionar variáveis de ambiente do Windows para usar em seus pacotes que são executados no IR Azure-SSIS inserindo o nome e o valor da variável do ambiente nas caixas de valor variável e nome **variável.** **Variable value** Isso é semelhante ao executar o comando [do conjunto](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) Windows na sua máquina local.

   * Se você selecionar o tipo **de componente licenciado Instalar,** você poderá selecionar um componente integrado dos nossos parceiros ISV na lista de parada de **nomes componentes:**

     * Se você selecionar o componente **Fábrica de Tarefas do SentryOne,** poderá instalar o conjunto task [factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) de componentes do SentryOne no seu IR Azure-SSIS inserindo a chave de licença do produto que você comprou deles na caixa de chaves **da Licença.** A versão integrada atual é **2019.4.3**.

     * Se você selecionar o **HEDDA do oh22. Componente IO,** você pode instalar o [HEDDA. Componente de](https://hedda.io/ssis-component/) qualidade/limpeza de dados IO a partir de oh22 em seu Azure-SSIS IR depois de comprar seu serviço. A versão integrada atual é **1.0.13**.

     * Se você selecionar o componente **SQLPhonetics.NET do oh22,** poderá instalar o componente de qualidade/correspondência de dados [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) do oh22 no seu IR Azure-SSIS inserindo a chave de licença do produto que você comprou deles na caixa de chaves **da Licença.** A versão integrada atual é **1.0.43**.

     * Se você selecionar o componente **SSIS Integration Toolkit da KingswaySoft,** poderá instalar o conjunto de conectores [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) para aplicativos crm/ERP/marketing/colaboração, como Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud, etc. da KingswaySoft no seu IR Azure-SSIS inserindo a chave de licença do produto que você comprou deles na caixa de **chaves da Licença.** A versão integrada atual é **2019.2**.

     * Se você selecionar o componente **SSIS Productivity Pack da KingswaySoft,** poderá instalar o conjunto de componentes do [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) da KingswaySoft no seu Azure-SSIS IR inserindo a chave de licença do produto que você comprou deles na caixa **de chaves da Licença.** A versão integrada atual é **10.0**.

     * Se você selecionar o componente **Xtract IS do Software Theobald,** poderá instalar o conjunto [Xtract IS](https://theobald-software.com/en/xtract-is/) de conectores para sistema SAP (ERP, S/4HANA, BW) do Theobald Software em seu Azure-SSIS IR arrastando & queda/upload do arquivo de licença do produto que você comprou deles na caixa de **arquivos de licença.** A versão integrada atual é **6.1.1.3**.

   Suas configurações personalizadas expressas adicionadas serão exibidas na seção **Configurações Avançadas.** Para removê-las, selecione suas caixas de seleção e, em seguida, **selecione Excluir**.

   ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Quando você configurar ou reconfigurar seu IR Azure-SSIS com o PowerShell, você pode `Set-AzDataFactoryV2IntegrationRuntime` adicionar ou remover as configurações personalizadas executando o cmdlet antes de iniciar o Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   Depois que a configuração personalizada padrão terminar e o IR do Azure-SSIS for iniciado, você poderá encontrar a saída padrão de *main.cmd* e outros logs de execução na pasta *principal.cmd.log* do seu contêiner de armazenamento.

1. Para visualizar algumas amostras de configurações personalizadas padrão, conecte-se ao nosso contêiner De pré-visualização pública usando o Azure Storage Explorer.

   a. Em **(Local e conectado)**, clique com botão direito em **Contas de Armazenamento**, selecione **Conectar ao armazenamento do Azure**, selecione **Usar uma cadeia de caracteres de conexão ou uma URI de assinatura de acesso compartilhado** e, em seguida, selecione **Próximo**.

      ![Conecte-se ao armazenamento do Azure com a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecione **Usar um URI SAS** e, em seguida, na caixa **URI,** digite o seguinte URI SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fornecer a Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecione **Next**e selecione **Conectar**.

   d. No painel esquerdo, selecione o contêiner **de visualização pública** conectada e clique duas vezes na pasta *CustomSetupScript.* Nesta pasta, você encontrará os seguintes itens:

      * Uma pasta *Sample,* que contém uma configuração personalizada para instalar uma tarefa básica em cada nó do seu IR Azure-SSIS. A tarefa apenas executará a suspensão por alguns segundos. A pasta também contém uma pasta *gacutil,* cujo conteúdo inteiro (*gacutil.exe*, *gacutil.exe.config*, e *1033\gacutlrc.dll*) pode ser copiado como é para o seu recipiente.

      * Uma pasta *UserScenarios,* que contém várias amostras de configuração personalizadas de cenários reais do usuário.

        ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Clique duas vezes na pasta *UserScenarios* para encontrar os seguintes itens:

      * Uma pasta *.NET FRAMEWORK 3.5,* que contém um script de configuração personalizado *(main.cmd)* para instalar uma versão anterior do .NET Framework que pode ser necessária para componentes personalizados em cada nó do seu Azure-SSIS IR.

      * Uma pasta *BCP,* que contém um script de configuração personalizado *(main.cmd)* para instalar utilitários de linha de comando SQL Server *(MsSqlCmdLUtils.msi),* incluindo o programa de cópia em massa *(bcp),* em cada nó do seu IR Azure-SSIS.

      * Uma pasta *EXCEL,* que contém um script de configuração personalizado *(main.cmd)* para instalar conjuntos c# e bibliotecas que você pode usar em tarefas de script para ler e escrever arquivos Excel dinamicamente em cada nó do seu Ir Azure-SSIS. 
      
        Primeiro, baixe [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), e depois carregue todos eles juntamente com *main.cmd* para o seu contêiner. Alternativamente, se você quiser usar apenas o gerente de conexão padrão do Excel, a origem do Excel e o destino do Excel, o acesso necessário já está pré-instalado no seu IR Azure-SSIS, portanto, você não precisa de nenhuma configuração personalizada.
      
      * Uma pasta *MYSQL ODBC,* que contém um script de configuração personalizado *(main.cmd)* para instalar os drivers MySQL ODBC em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de Conexões ODBC, a origem e o destino para se conectar ao servidor MySQL. 
     
        Primeiro, [baixe as versões mais recentes de 64 bits e 32 bits dos instaladores de driver MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (por exemplo, *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi*), e depois carregue-os todos juntamente com *o principal.cmd* para o seu contêiner.

      * Uma pasta *ORACLE ENTERPRISE,* que contém um script de configuração personalizado *(main.cmd)* e um arquivo de configuração de instalação silenciosa *(client.rsp)* para instalar os conectores Oracle e o driver OCI em cada nó do Seu Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Oracle Connection Manager, origem e destino para se conectar ao servidor Oracle. 
      
        Primeiro, baixe microsoft connectors v5.0 para Oracle (*AttunitySSISOraAdaptersSetup.msi* e *AttunitySSISOraAdaptersSetup64.msi*) do [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e do cliente Oracle mais recente (por exemplo, *winx64_12102_client.zip*) da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), e depois carregue-os todos juntamente com *main.cmd* e *client.rsp* para o seu contêiner. Se você usar tNS para se conectar ao Oracle, você também precisa baixar *tnsnames.ora*, editá-lo e enviá-lo para o seu contêiner, para que ele possa ser copiado para a pasta de instalação Oracle durante a configuração.

      * Uma pasta *ORACLE STANDARD ADO.NET,* que contém um script de configuração personalizado *(main.cmd)* para instalar o driver Oracle ODP.NET em cada nó do seu Ir Azure-SSIS. Essa configuração permite que você use o ADO.NET Connection Manager, origem e destino para se conectar ao servidor Oracle. 
      
        Primeiro, [baixe o driver de ODP.NET oracle mais recente](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (por exemplo, *ODP.NET_Managed_ODAC122cR1.zip),* e depois carregue-o juntamente com *o main.cmd* para o seu contêiner.
       
      * Uma pasta *ODBC PADRÃO ORACLE,* que contém um script de configuração personalizado *(main.cmd)* para instalar o driver Oracle ODBC e configurar o nome de origem de dados (DSN) em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexão ODBC, origem e destino ou Gerenciador de conexão de consulta de energia e origem com o tipo de fonte de dados ODBC para se conectar ao servidor Oracle. 
      
        Primeiro, baixe o mais recente Oracle Instant Client (Basic Package ou Basic Lite Package) e o Pacote ODBC e, em seguida, carregue-os todos juntamente com *o main.cmd* para o seu contêiner:
        * [Baixe pacotes de 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Pacote básico: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Pacote Lite básico: *instantclient-basiclite-windows.x64-18.3.0.0.0.0dbru.zip*; Pacote ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Baixe pacotes de 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Pacote básico: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Pacote Lite básico: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pacote ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Uma pasta *Oracle STANDARD OLEDB,* que contém um script de configuração personalizado *(main.cmd)* para instalar o driver Oracle OLEDB em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões OLEDB, a origem e o destino para se conectar ao servidor Oracle. 
     
        Primeiro, [baixe o driver OLEDB oracle mais recente](https://www.oracle.com/partners/campaign/index-090165.html) (por exemplo, *ODAC122010Xcopy_x64.zip*), e depois carregue-o juntamente com *o main.cmd* para o seu contêiner.

      * Uma pasta *POSTGRESQL ODBC,* que contém um script de configuração personalizado *(main.cmd)* para instalar os drivers PostgreSQL ODBC em cada nó do seu IR Azure-SSIS. Essa configuração permite que você use o Gerenciador de conexões ODBC, a origem e o destino para se conectar ao servidor PostgreSQL. 
     
        Primeiro, [baixe as versões mais recentes de 64 bits e 32 bits dos instaladores de driver PostgreSQL ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (por exemplo, *psqlodbc_x64.msi* e *psqlodbc_x86.msi),* e depois carregue-as todas juntamente com *o main.cmd* para o seu contêiner.

      * Uma pasta *SAP BW,* que contém um script de configuração personalizado *(main.cmd)* para instalar o conjunto de conectorS .NET *(librfc32.dll)* em cada nó do Seu Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Gerenciador de Conexões (BW) do SAP Business Warehouse (BW) para se conectar ao servidor SAP BW. 
      
        Primeiro, carregue a versão de 64 bits ou 32 bits do *librfc32.dll* da pasta de instalação SAP juntamente com *o main.cmd* para o seu contêiner. Em seguida, o script copia o conjunto SAP para a pasta *%windir%\SysWow64* ou *%windir%\System32* durante a configuração.

      * Uma pasta *STORAGE,* que contém um script de configuração personalizado *(main.cmd)* para instalar o Azure PowerShell em cada nó do seu IR Azure-SSIS. Essa configuração permite que você implante e execute pacotes SSIS que executam [scripts PowerShell para manipular sua conta de armazenamento Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Copie *main.cmd*, uma amostra *AzurePowerShell.msi* (ou use a versão mais recente) e *storage.ps1* para o seu contêiner. Use *powerShell.dtsx* como modelo para seus pacotes. O modelo do pacote combina uma [tarefa de download do Azure Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que baixa *storage.ps1* como um script PowerShell modificável e uma [tarefa de processo executar,](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)que executa o script em cada nó.

      * Uma pasta *TERADATA,* que contém um script de configuração personalizado *(main.cmd),* seu arquivo associado *(install.cmd)* e pacotes de instalador *(.msi*). Esses arquivos instalam os conectores Teradata, a API Teradata Parallel Transporter (TPT) e o driver ODBC em cada nó do Seu Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Teradata Connection Manager, origem e destino para se conectar ao servidor Teradata. 
      
        Primeiro, [baixe o arquivo zip Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (por exemplo, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* e depois carregue-o juntamente com os arquivos *.cmd* e *.msi mencionados* anteriormente para o seu contêiner.

      * Uma pasta *TLS 1.2,* que contém um script de configuração personalizado *(main.cmd)* para usar criptografia forte/protocolo de rede mais seguro (TLS 1.2) e desativar versões SSL/TLS mais antigas em cada nó do seu IR Azure-SSIS.

      * Uma pasta *ZULU OPENJDK,* que contém um script de configuração personalizado *(main.cmd)* e arquivo PowerShell *(install_openjdk.ps1)* para instalar o Zulu OpenJDK em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use conectores Azure Data Lake Store e Flexible File para processar arquivos ORC e Parquet. Para obter mais informações, consulte [O Pacote de recursos do Azure para serviços de integração](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Primeiro, [baixe o Zulu OpenJDK mais recente](https://www.azul.com/downloads/zulu/zulu-windows/) (por exemplo, *zulu8.33.0.1-jdk8.0.192-win_x64.zip),* e depois carregue-o junto com *main.cmd* e *install_openjdk.ps1* para o seu contêiner.

        ![Pastas na pasta de cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Para testar essas amostras de configuração personalizadas, copie o conteúdo da pasta selecionada para o contêiner.
   
      Ao configurar ou reconfigurar seu IR Azure-SSIS usando a UI da fábrica de dados, selecione **o Tempo de execução de integração Do Azure-SSIS com configurações adicionais/instalações de componentes** caixa de seleção na seção **Configurações Avançadas** e, em seguida, digite o Uri SAS do seu contêiner na caixa **URI do contêiner de configuração personalizada.**
   
      Quando configurar ou reconfigurar seu IR Azure-SSIS com `Set-AzDataFactoryV2IntegrationRuntime` o PowerShell, execute o cmdlet com `SetupScriptContainerSasUri` o Uri SAS do seu contêiner como o valor para parâmetro.

## <a name="next-steps"></a>Próximas etapas

- [Configure a Edição Corporativa do Tempo de Execução de Integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Desenvolva componentes personalizados pagos ou licenciados para o tempo de execução de integração Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
