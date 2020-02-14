---
title: Personalizar a instalação para um Azure-SSIS Integration Runtime
description: Este artigo descreve como usar a interface de instalação personalizada para um Azure-SSIS Integration Runtime instalar componentes adicionais ou alterar configurações
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
ms.date: 02/01/2020
ms.openlocfilehash: c4502cc4a808b4a44f70c1f96fe38fd0ddeebdee
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187722"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalizar a instalação para um Azure-SSIS Integration Runtime

A configuração personalizada para um Integration Runtime de SQL Server Integration Services do Azure (Azure-SSIS IR) fornece uma interface para adicionar suas próprias etapas durante a instalação ou reconfiguração de seu Azure-SSIS IR. 

Usando a configuração personalizada, você pode alterar a configuração operacional padrão ou o ambiente para, por exemplo, iniciar serviços adicionais do Windows ou manter as credenciais de acesso para compartilhamentos de arquivos. Ou você pode instalar componentes adicionais, como assemblies, drivers ou extensões, em cada nó do seu Azure-SSIS IR.

Você pode fazer configurações personalizadas no seu Azure-SSIS IR de uma das duas maneiras: 
* **Instalação personalizada expressa sem um script**: execute algumas configurações comuns do sistema e comandos do Windows ou instale alguns componentes adicionais populares ou recomendados sem usar nenhum script.
* **Configuração personalizada padrão com um script**: Prepare um script e seus arquivos associados e carregue-os juntos em um contêiner de BLOB em sua conta de armazenamento do Azure. Em seguida, forneça uma SAS (assinatura de acesso compartilhado) Uniform Resource Identifier (URI) para seu contêiner quando você configurar ou reconfigurar seu Azure-SSIS IR. Cada nó do seu Azure-SSIS IR, em seguida, baixa o script e seus arquivos associados do seu contêiner e executa a instalação personalizada com permissões elevadas. Quando a instalação personalizada for concluída, cada nó carregará a saída padrão de execução e outros logs em seu contêiner.

Você pode instalar componentes gratuitos e não licenciados e os componentes licenciados e pagos com configurações personalizadas expressas e padrão. Se você for um fornecedor independente de software (ISV), consulte [desenvolver componentes pagos ou licenciados para um Azure-SSIS ir](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Como os nós da série v2 de um Azure-SSIS IR não são adequados para a instalação personalizada, use os nós da série V3 em vez disso. Se você já estiver usando nós da série v2, alterne para nós da série V3 assim que possível.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam somente a configurações personalizadas padrão:

- Se você quiser usar o *Gacutil. exe* em seu script para instalar ASSEMBLIES no GAC (cache de assembly global), será necessário fornecer o *Gacutil. exe* como parte da instalação personalizada. Ou você pode usar a cópia que é fornecida em nosso contêiner de *Visualização pública* , discutida posteriormente na seção "instruções".

- Se você quiser fazer referência a uma subpasta em seu script, o *msiexec. exe* não oferece suporte à notação de `.\` para fazer referência à pasta raiz. Use um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Os compartilhamentos administrativos ou os compartilhamentos de rede ocultos criados automaticamente pelo Windows atualmente não têm suporte no Azure-SSIS IR.

- Não há suporte para o driver ODBC do IBM iSeries Access no Azure-SSIS IR. Você poderá ver erros de instalação durante a instalação personalizada. Se você fizer isso, entre em contato com o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar seu Azure-SSIS IR, você precisará dos seguintes itens:

- [Uma assinatura do Azure](https://azure.microsoft.com/)

- [Provisionar o IR do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Uma conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Não é necessário para configurações personalizadas expressas. Para configurações personalizadas padrão, você carrega e armazena seu script de instalação personalizada e seus arquivos associados em um contêiner de BLOB. O processo de instalação personalizado também carrega seus logs de execução no mesmo contêiner de blob.

## <a name="instructions"></a>Instruções

1. Se você quiser configurar ou reconfigurar seu Azure-SSIS IR com o PowerShell, baixe e instale o [Azure PowerShell](/powershell/azure/install-az-ps). Para as configurações personalizadas expressas, pule para a etapa 4.

1. Prepare o script de instalação personalizado e seus arquivos associados (por exemplo, arquivos .bat, .cmd, .exe, .dll, .msi ou .ps1).

   * Você deve ter um arquivo de script chamado *Main. cmd*, que é o ponto de entrada da configuração personalizada.  
   * Para garantir que o script possa ser executado silenciosamente, é recomendável testá-lo no computador local primeiro.  
   * Se você quiser que logs adicionais gerados por outras ferramentas (por exemplo, *msiexec. exe*) sejam carregados no contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR`, como a pasta de log em seus scripts (por exemplo, *msiexec/i XXX. msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Baixe, instale e abra [Gerenciador de armazenamento do Azure](https://storageexplorer.com/). Para fazer isso:

   a. Em **(local e anexado)** , clique com o botão direito do mouse em **contas de armazenamento**e selecione **conectar ao armazenamento do Azure**.

      ![Conectar-se a um Armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecione **usar um nome de conta de armazenamento e uma chave**e, em seguida, selecione **Avançar**.

      ![Usar um nome e uma chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Insira o nome e a chave da conta de armazenamento do Azure, selecione **Avançar**e, em seguida, selecione **conectar**.

      ![Fornecer o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Em sua conta de armazenamento do Azure conectada, clique com o botão direito do mouse em **contêineres de blob**, selecione **criar contêiner de blob**e nomeie o novo contêiner.

      ![Criar um contêiner de blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecione o novo contêiner e carregue seu script de instalação personalizada e seus arquivos associados. Certifique-se de carregar *Main. cmd* no nível superior do seu contêiner, não em nenhuma pasta. Além disso, certifique-se de que o contêiner contenha apenas os arquivos de instalação personalizados necessários, para que baixá-los para seu Azure-SSIS IR posterior não demorem muito tempo. A duração máxima de uma instalação personalizada está atualmente definida em 45 minutos antes de expirar. Isso inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no Azure-SSIS IR. Se a instalação exigir mais tempo, gere um tíquete de suporte.

      ![Carregar arquivos no contêiner de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Clique com o botão direito do mouse no contêiner e selecione **obter assinatura de acesso compartilhado**.

      ![Obter Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Crie o URI de SAS para seu contêiner com um tempo de expiração suficientemente longo e com permissão de leitura/gravação/lista. Você precisa do URI de SAS para baixar e executar o script de instalação personalizada e seus arquivos associados sempre que qualquer nó de sua Azure-SSIS IR for refeita a imagem ou reiniciado. Você precisa gravar permissão para carregar os logs de execução da instalação.

      > [!IMPORTANT]
      > Verifique se o URI SAS não expira e se os recursos de instalação personalizada estão sempre disponíveis durante todo o ciclo de vida do seu Azure-SSIS IR, desde a criação até a exclusão, especialmente se você parar e iniciar regularmente o Azure-SSIS IR durante esse período.

      ![Gerar Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copie e salve o URI de SAS do seu contêiner.

      ![Copie e salve a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Quando você configura ou reconfigura seu Azure-SSIS IR com uma interface do usuário do data factory, você pode adicionar ou remover configurações personalizadas marcando a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com definições de sistema/instalações de componentes adicionais** na seção **Configurações avançadas** do painel **instalação do Integration Runtime** . 

   Se você quiser adicionar configurações personalizadas padrão, insira o URI de SAS do seu contêiner na caixa **URI de SAS do contêiner de instalação personalizada** . 
   
   Se você quiser adicionar configurações personalizadas expressas, selecione **novo** para abrir o painel **Adicionar instalação expressa personalizada** e selecione um tipo na lista suspensa tipo de **instalação personalizada expressa** :

   * Se você selecionar o tipo de **comando ' executar cmdkey** ', poderá manter as credenciais de acesso para seus compartilhamentos de arquivos ou compartilhamentos de arquivos do Azure no Azure-SSIS ir inserindo o nome do computador ou o nome de domínio de destino, o nome da conta ou o nome de usuário e a chave de conta ou senha nas caixas **/Add**, **/User**e **/Pass** . Isso é semelhante à execução do comando [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) do Windows no computador local.
   
   * Se você selecionar o tipo de **variável adicionar ambiente** , poderá adicionar variáveis de ambiente do Windows para usar em seus pacotes executados no Azure-SSIS ir inserindo o nome e o valor da variável de ambiente nas caixas **nome da variável** e **valor da variável** . Isso é semelhante à execução do comando [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) do Windows no computador local.

   * Se você selecionar o tipo de **componente de instalação licenciado** , poderá selecionar um componente integrado de nossos parceiros ISV na lista suspensa **nome do componente** :

     * Se você selecionar o componente de **fábrica de tarefas do SentryOne** , poderá instalar o pacote de [tarefas da fábrica](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) de componentes do SentryOne na sua Azure-SSIS ir inserindo a chave de licença do produto que você comprou a partir deles na caixa chave de **licença** . A versão integrada atual é **2019.4.3**.

     * Se você selecionar o **OH22'S HEDDA. Componente de e/s** , você pode instalar o [HEDDA. ](https://hedda.io/ssis-component/)Componente de qualidade de dados de es/limpeza do oh22 no seu Azure-SSIS ir depois de comprar seu serviço. A versão integrada atual é **1.0.13**.

      * Se você selecionar o componente **oh22's SQLPhonetics.net** , poderá instalar o componente de correspondência/qualidade de dados do [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) do oh22 no seu Azure-SSIS ir inserindo a chave de licença do produto que você comprou a partir deles na caixa **chave de licença** . A versão integrada atual é **1.0.43**.
   
   Suas configurações personalizadas expressas adicionadas aparecerão na seção **Advanced Settings** . Para removê-los, marque suas caixas de seleção e, em seguida, selecione **excluir**.

   ![Configurações avançadas com instalações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Ao configurar ou reconfigurar seu Azure-SSIS IR com o PowerShell, você pode adicionar ou remover as configurações personalizadas executando o cmdlet `Set-AzDataFactoryV2IntegrationRuntime` antes de iniciar o Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
   
   Depois que a instalação personalizada padrão for concluída e o Azure-SSIS IR for iniciado, você poderá encontrar a saída padrão de *Main. cmd* e outros logs de execução na pasta *Main. cmd. log* do seu contêiner de armazenamento.

1. Para exibir alguns exemplos de configurações personalizadas padrão, conecte-se ao nosso contêiner de visualização pública usando Gerenciador de Armazenamento do Azure.

   a. Em **(Local e conectado)** , clique com botão direito em **Contas de Armazenamento**, selecione **Conectar ao armazenamento do Azure**, selecione **Usar uma cadeia de caracteres de conexão ou uma URI de assinatura de acesso compartilhado** e, em seguida, selecione **Próximo**.

      ![Conecte-se ao armazenamento do Azure com a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecione **usar um URI de SAS** e, em seguida, na caixa **URI** , insira o seguinte URI de SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Fornecer a Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecione **Avançar**e, em seguida, selecione **conectar**.

   d. No painel esquerdo, selecione o contêiner **publicpreview** conectado e clique duas vezes na pasta *CustomSetupScript* . Nesta pasta, você encontrará os seguintes itens:

      * Uma pasta de *exemplo* , que contém uma instalação personalizada para instalar uma tarefa básica em cada nó do seu Azure-SSIS ir. A tarefa apenas executará a suspensão por alguns segundos. A pasta também contém uma pasta *Gacutil* , cujo conteúdo inteiro (*Gacutil. exe*, *Gacutil. exe. config*e *1033 \ gacutlrc. dll*) pode ser copiado como está em seu contêiner.

      * Uma pasta *Userscenarios* , que contém vários exemplos de instalação personalizada de cenários de usuário reais.

        ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Clique duas vezes na pasta *Userscenarios* para localizar os seguintes itens:

      * Uma pasta do *.net FRAMEWORK 3,5* , que contém uma instalação personalizada para instalar uma versão anterior do .NET Framework que pode ser necessária para componentes personalizados em cada nó do seu Azure-SSIS ir.

      * Uma pasta *bcp* , que contém uma instalação personalizada para instalar SQL Server utilitários de linha de comando (*MsSqlCmdLnUtils. msi*), incluindo o*bcp*(programa de cópia em massa), em cada nó do seu Azure-SSIS ir.

      * Uma pasta do *Excel* , que contém um script de instalação personalizada (*Main. cmd*) C# para instalar assemblies e bibliotecas que você pode usar em tarefas de script para ler e gravar dinamicamente arquivos do EXCEL em cada nó do seu Azure-SSIS ir. 
      
        Primeiro, baixe [*ExcelDataReader. dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat. OPENXML. dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)e, em seguida, carregue-os juntos com *Main. cmd* em seu contêiner. Como alternativa, se você quiser usar apenas o Gerenciador de conexões padrão do Excel, a origem do Excel e o destino do Excel, o acesso redistribuível necessário já está pré-instalado no seu Azure-SSIS IR, portanto, você não precisa de nenhuma configuração personalizada.
      
      * Uma pasta do *ODBC do MySQL* , que contém um script de instalação personalizada (*Main. cmd*) para instalar os drivers ODBC do MySQL em cada nó do seu Azure-SSIS ir. Essa configuração permite que você use o Gerenciador de conexões ODBC, a origem e o destino para se conectar ao servidor MySQL. 
     
        Primeiro, [Baixe as versões mais recentes de 64 bits e 32 bits dos instaladores do driver ODBC do MySQL](https://dev.mysql.com/downloads/connector/odbc/) (por exemplo, *MySQL-Connector-ODBC-8.0.13-winx64. msi* e *MySQL-Connector-ODBC-8.0.13-Win32. msi*) e, em seguida, carregue-as junto com *Main. cmd* para seu contêiner.

      * Uma pasta *Oracle Enterprise* , que contém um script de instalação personalizado (*Main. cmd*) e um arquivo de configuração de instalação silenciosa (*Client. rsp*) para instalar os conectores Oracle e o driver de OCI em cada nó do seu Azure-SSIS ir Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões Oracle, a origem e o destino para se conectar ao servidor Oracle. 
      
        Primeiro, baixe o Microsoft Connectors v 5.0 para Oracle (*AttunitySSISOraAdaptersSetup. msi* e *AttunitySSISOraAdaptersSetup64. msi*) do [centro de download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o cliente Oracle mais recente (por exemplo, *winx64_12102_client. zip*) do [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)e, em seguida, carregue-os juntos com *Main. cmd* e *Client. rsp* em seu contêiner. Se você usar TNS para se conectar ao Oracle, também precisará baixar *arquivo tnsnames. Ora*, editá-lo e carregá-lo em seu contêiner, para que ele possa ser copiado para a pasta de instalação do Oracle durante a instalação.

      * Uma pasta *Oracle STANDARD ADO.net* , que contém um script de instalação personalizada (*Main. cmd*) para instalar o Driver Oracle odp.net em cada nó do seu Azure-SSIS ir. Essa configuração permite que você use o Gerenciador de conexões do ADO.NET, a origem e o destino para se conectar ao servidor Oracle. 
      
        Primeiro, [Baixe o driver do Oracle odp.net mais recente](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (por exemplo, *odp. NET_Managed_ODAC122cR1. zip*) e, em seguida, carregue-o junto com *Main. cmd* para seu contêiner.
       
      * Uma pasta *ODBC padrão do Oracle* , que contém um script de instalação personalizada (*Main. cmd*) para instalar o driver ODBC Oracle e configurar o DSN (nome da fonte de dados) em cada nó do seu Azure-SSIS ir. Essa configuração permite que você use o Gerenciador de conexões ODBC, a origem e o destino ou o Gerenciador de conexões Power Query e a origem com o tipo de fonte de dados ODBC para se conectar ao servidor Oracle. 
      
        Primeiro, baixe o pacote mais recente do Oracle Instant Client (pacote básico ou Basic Lite) e ODBC e, em seguida, carregue-os juntos com *Main. cmd* em seu contêiner:
        * [Baixar pacotes de 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pacote básico: *instantclient-Basic-Windows. x64-18.3.0.0.0 dbru. zip*; Pacote Basic Lite: *instantclient-basiclite-Windows. x64-18.3.0.0.0 dbru. zip*; Pacote ODBC: *instantclient-ODBC-Windows. x64-18.3.0.0.0 dbru. zip*) 
        * [Baixar pacotes de 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pacote básico: *instantclient-Basic-NT-18.3.0.0.0 dbru. zip*; Pacote Basic Lite: *instantclient-basiclite-NT-18.3.0.0.0 dbru. zip*; Pacote ODBC: *instantclient-ODBC-NT-18.3.0.0.0 dbru. zip*)

      * Uma pasta *OLEDB do Oracle Standard* , que contém um script de instalação personalizada (*Main. cmd*) para instalar o Driver Oracle DB em cada nó do seu Azure-SSIS ir. Essa configuração permite que você use o Gerenciador de conexões OLEDB, a origem e o destino para se conectar ao servidor Oracle. 
     
        Primeiro, [Baixe o Driver Oracle OLEDB mais recente](https://www.oracle.com/partners/campaign/index-090165.html) (por exemplo, *ODAC122010Xcopy_x64. zip*) e, em seguida, carregue-o junto com *Main. cmd* para seu contêiner.

      * Uma pasta do *ODBC do PostgreSQL* , que contém um script de instalação personalizada (*Main. cmd*) para instalar os drivers ODBC do PostgreSQL em cada nó do seu Azure-SSIS ir. Essa configuração permite que você use o Gerenciador de conexões ODBC, a origem e o destino para se conectar ao servidor PostgreSQL. 
     
        Primeiro, [Baixe as versões mais recentes de 64 bits e 32 bits dos instaladores de driver ODBC do PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (por exemplo, *psqlodbc_x64. msi* e *psqlodbc_x86. msi*) e, em seguida, carregue-as junto com *Main. cmd* para seu contêiner.

      * Uma pasta *SAP BW* , que contém um script de instalação personalizada (*Main. cmd*) para instalar o assembly do conector do SAP .net (*librfc32. dll*) em cada nó de sua Azure-SSIS ir Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões do SAP Business Warehouse (BW), a origem e o destino para se conectar ao servidor de SAP BW. 
      
        Primeiro, carregue a versão de 64 bits ou de 32 bits do *librfc32. dll* da pasta de instalação do SAP junto com *Main. cmd* para seu contêiner. Em seguida, o script copia o assembly do SAP para a pasta *%windir%\SysWow64* ou *%windir%\System32* durante a instalação.

      * Uma pasta de *armazenamento* , que contém uma instalação personalizada para instalar Azure PowerShell em cada nó do seu Azure-SSIS ir. Essa configuração permite implantar e executar pacotes do SSIS que executam [scripts do PowerShell para manipular sua conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Copie *Main. cmd*, um exemplo de *AzurePowerShell. msi* (ou use a versão mais recente) e *Storage. ps1* para seu contêiner. Use o *PowerShell. dtsx* como um modelo para seus pacotes. O modelo de pacote combina uma [tarefa de download de blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que baixa o *Storage. ps1* como um script do PowerShell modificável e uma [tarefa Executar processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), que executa o script em cada nó.

      * Uma pasta *Teradata* , que contém um script de instalação personalizada (*Main. cmd*), seu arquivo associado (*install. cmd*) e pacotes do instalador ( *. msi*). Esses arquivos instalam os conectores do Teradata, a API de transporte paralelo do Teradata (TPT) e o driver ODBC em cada nó do seu Azure-SSIS IR Enterprise Edition. Essa configuração permite que você use o Gerenciador de conexões Teradata, a origem e o destino para se conectar ao servidor Teradata. 
      
        Primeiro, [Baixe o arquivo zip de ferramentas e utilitários do Teradata 15. x](http://partnerintelligence.teradata.com) (por exemplo, *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00. zip*) e, em seguida, carregue-o junto com os arquivos *. cmd* e *. msi* mencionados anteriormente para o seu contêiner.

      * Uma pasta *Zulu OPENJDK* , que contém um script de instalação personalizado (*Main. cmd*) e um arquivo do PowerShell (*install_openjdk. ps1*) para instalar o Zulu OPENJDK em cada nó do seu Azure-SSIS ir. Essa configuração permite que você use Azure Data Lake Store e conectores de arquivo flexíveis para processar arquivos ORC e parquet. Para obter mais informações, consulte [Azure Feature Pack para Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Primeiro, [Baixe o Zulu OpenJDK mais recente](https://www.azul.com/downloads/zulu/zulu-windows/) (por exemplo, *Zulu 8.33.0.1-jdk 8.0.192-win_x64. zip*) e, em seguida, carregue-o junto com *Main. cmd* e *install_openjdk. ps1* ao seu contêiner.

        ![Pastas na pasta de cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Para testar esses exemplos de instalação personalizada, copie o conteúdo da pasta selecionada para o contêiner.
   
      Quando você configurar ou reconfigurar seu Azure-SSIS IR usando a interface do usuário do Data Factory, marque a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** na seção **Configurações avançadas** e, em seguida, insira o URI SAS do seu contêiner na caixa **URI de SAS do contêiner de instalação personalizada** .
   
      Quando você configurar ou reconfigurar seu Azure-SSIS IR com o PowerShell, execute o cmdlet `Set-AzDataFactoryV2IntegrationRuntime` com o URI de SAS do seu contêiner como o valor do parâmetro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a Enterprise Edition do Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Desenvolva componentes personalizados pagos ou licenciados para o Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
