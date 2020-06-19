---
title: Personalizar instalação para um Azure-SSIS Integration Runtime
description: Este artigo descreve como usar a interface de configuração personalizada para um Azure-SSIS Integration Runtime para instalar componentes adicionais ou alterar as configurações
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
ms.openlocfilehash: d2a5928d8326c4a0628ebc1bfb7eec3cd20f9254
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747512"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalizar instalação para um Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A configuração personalizada de um Azure-SSIS IR (Azure-SQL Server Integration Services Integration Runtime) oferece uma interface para adicionar suas próprias etapas durante a configuração ou reconfiguração do seu Azure-SSIS IR. 

Usando a configuração personalizada, você pode alterar as configurações ou o ambiente operacional padrão para, por exemplo, iniciar serviços Windows adicionais, manter credenciais de acesso para compartilhamentos de arquivo ou usar uma criptografia forte/um protocolo de rede mais seguro (TLS 1.2). Ou você pode instalar mais componentes, como assemblies, drivers ou extensões, em cada nó do seu Azure-SSIS IR.

Você pode executar configurações personalizadas em seu Azure-SSIS IR de duas maneiras: 
* **Expressar configuração personalizada sem um script**: execute algumas configurações comuns do sistema e comandos do Windows ou instale alguns componentes adicionais populares ou recomendados sem usar nenhum script.
* **configuração personalizada padrão com um script**: prepare um script e os arquivos associados e carregue todos eles juntos para um contêiner de blobs em sua conta de armazenamento do Azure. Forneça um URI (Uniform Resource Identifier) de SAS (Assinatura de Acesso Compartilhado) para seu contêiner quando você configurar ou reconfigurar o Azure-SSIS IR. Cada nó do Azure-SSIS IR baixa o script e os arquivos associados do seu contêiner e executa sua configuração personalizada com permissões elevadas. Quando a configuração personalizada for concluída, cada nó carregará a saída de execução padrão e outros logs para seu contêiner.

Você pode instalar os componentes gratuitos e sem licença e os componentes pagos e licenciados com configurações personalizadas expressas e padrão. Se você for um ISV (fornecedor de software independente), confira [Desenvolver componentes pagos ou licenciados para o Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Para se beneficiar de aprimoramentos futuros, recomendamos usar a série v3 ou posterior de nós para seu Azure-SSIS IR com configuração personalizada.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam apenas a configurações personalizadas padrão:

- Se desejar usar *gacutil.exe* em seu script para instalar assemblies no GAC (cache de assembly global), você precisará fornecer *gacutil.exe* como parte de sua configuração personalizada. Ou você poderá usar a cópia fornecida em nosso contêiner de *Versão Prévia Pública*, discutido posteriormente na seção “instruções”.

- Se desejar fazer referência a uma subpasta no script, *msiexec.exe* não dará suporte à notação `.\` para fazer referência à pasta raiz. Use um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- No momento, os compartilhamentos administrativos, ou compartilhamentos de rede ocultos criados automaticamente pelo Windows, não têm suporte em outro Azure-SSIS IR.

- Não há suporte para o driver ODBC iSeries Access da IBM no Azure-SSIS IR. Talvez você encontre erros de instalação durante a configuração personalizada. Se isso acontecer, entre em contato com o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar o Azure-SSIS IR, você precisa dos seguintes itens:

- [Uma assinatura do Azure](https://azure.microsoft.com/)

- [Provisionar o IR do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Uma conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Não é necessário para configurações personalizadas expressas. Para configurações personalizadas expressas, você carrega e armazena seu script de configuração personalizada e seus arquivos associados em um contêiner de blob. O processo de instalação personalizado também carrega seus logs de execução no mesmo contêiner de blob.

## <a name="instructions"></a>Instruções

1. Se desejar instalar ou reconfigurar seu Azure-SSIS IR com o PowerShell, baixe e instale o [Azure PowerShell](/powershell/azure/install-az-ps). Para as configurações personalizadas expressas, passe para a etapa 4.

1. Prepare o script de instalação personalizado e seus arquivos associados (por exemplo, arquivos .bat, .cmd, .exe, .dll, .msi ou .ps1).

   * Você deve ter um arquivo de script chamado *main.cmd*, que é o ponto de entrada de sua configuração personalizada.  
   * Para verificar se o script pode ser executado silenciosamente, é recomendável testá-lo no computador local primeiro.  
   * Se quiser que logs adicionais gerados por outras ferramentas (por exemplo, *msiexec.exe*) sejam carregados em seu contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR`, como a pasta de log em seus scripts (por exemplo, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Baixe, instale e abra o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/). Para fazer isso:

   a. Em **(Local e Anexo)** , clique com o botão direito do mouse em **Contas de Armazenamento** e selecione **Conectar-se ao Armazenamento do Azure**.

      ![Conectar-se a um Armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecione **Usar um nome e uma chave da conta de armazenamento** e selecione **Próximo**.

      ![Usar um nome e uma chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Digite seu nome e a chave da conta de armazenamento do Azure, selecione **Próximo** e, em seguida, selecione **Conectar**.

      ![Fornecer o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Em sua conta de armazenamento do Azure conectada, clique com o botão direito do mouse em **Contêineres de Blob**, selecione **Criar Contêiner de Blob** e dê um nome ao novo contêiner.

      ![Criar um contêiner de blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecione o novo contêiner e carregue o script de configuração personalizada e os arquivos associados. Carregue o *main.cmd* no nível superior de seu contêiner, e não em qualquer pasta. Verifique também se em seu contêiner há somente os arquivos de configuração personalizada necessários; pois assim, baixá-los em seu Azure-SSIS IR mais tarde não levará muito tempo. A duração máxima de uma configuração personalizada está atualmente definida em 45 minutos antes de atingir o tempo limite. Isso inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no Azure-SSIS IR. Se a instalação exigir mais tempo, gere um tíquete de suporte.

      ![Carregar arquivos no contêiner de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Clique com o botão direito do mouse no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**.

      ![Obter Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Crie o URI de SAS para seu contêiner com um período de expiração suficientemente longo e permissões de leitura/gravação/lista. Será necessário que o URI de SAS baixe e execute o script de configuração personalizada e arquivos associados sempre que qualquer nó do Azure-SSIS IR tiver a imagem refeita ou sempre que ele for recriado. Você precisa gravar permissão para carregar os logs de execução da instalação.

      > [!IMPORTANT]
      > Verifique se o URI de SAS não vai expirar e se os recursos de configuração personalizada estarão sempre disponíveis durante o ciclo de vida inteiro do Azure-SSIS IR, da criação à exclusão, especialmente se você regularmente parar e iniciar o Azure-SSIS IR durante esse período.

      ![Gerar Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copie e salve o URI de SAS do seu contêiner.

      ![Copie e salve a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Quando você instala ou reconfigura seu Azure-SSIS IR com uma interface do usuário do data factory, você pode adicionar ou remover configurações personalizadas marcando a caixa de seleção **Personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** na seção **Configurações Avançadas** do painel **Instalação do runtime de integração**. 

   Se você quiser adicionar configurações personalizadas padrão, insira o URI de SAS do seu contêiner na caixa de **URI de SAS do contêiner de configuração personalizada**. 
   
   Se você quiser adicionar configurações personalizadas expressas, selecione **Novo** para abrir o painel **Adicionar configuração personalizada expressa** e, em seguida, selecione um tipo na lista suspensa **Tipo de configuração personalizada expressa**:

   * Se você selecionar o tipo **Executar comando cmdkey**, poderá manter as credenciais de acesso para seus compartilhamentos de arquivos ou compartilhamentos de Arquivos do Azure no Azure-SSIS IR inserindo o nome do computador direcionado ou o nome de domínio, o nome da conta ou o nome de usuário e a chave de conta ou senha nas caixas **/Add**, **/User** e **/Pass**. Isso é semelhante a executar o comando [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) do Windows em seu computador local.
   
   * Se você selecionar o tipo **Adicionar variável de ambiente**, poderá adicionar variáveis de ambiente do Windows a serem usadas em seus pacotes executados no Azure-SSIS IR inserindo o nome e o valor da variável de ambiente nas caixas **Nome da variável** e **Valor da variável**. Isso é semelhante a executar o comando [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) do Windows em seu computador local.

   * Se você selecionar o tipo **Instalar componente licenciado**, poderá selecionar o componente integrado dos nossos parceiros ISV na lista suspensa **Nome do componente**:

     * Se você selecionar o componente **Fábrica de Tarefas da SentryOne**, poderá instalar o conjunto de componentes de [Fábrica de Tarefas](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) da SentryOne em seu Azure-SSIS IR inserindo a chave de licença do produto adquirida deles na caixa **Chave de licença**. A versão integrada atual é **2019.4.3**.

     * Se você selecionar o componente **HEDDA.IO da oh22**, você poderá instalar o componente de qualidade de dados/limpeza [HEDDA.IO](https://hedda.io/ssis-component/) da oh22 em seu Azure-SSIS IR após adquirir o serviço. A versão integrada atual é **1.0.13**.

     * Se você selecionar o componente **SQLPhonetics.NET da oh22**, você poderá instalar o componente de qualidade de dados/correspondência [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) da oh22 em seu Azure-SSIS IR inserindo a chave de licença adquirida deles na caixa **Chave de licença**. A versão integrada atual é **1.0.43**.

     * Se você selecionar o componente **Kit de Ferramentas de Integração SSIS da KingswaySoft**, poderá instalar o conjunto de conectores do [Kit de Ferramentas de Integração SSIS](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) para aplicativos CRM/ERP/marketing/colaboração, como o Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud etc. da KingswaySoft em seu Azure-SSIS IR inserindo a chave de licença do produto que você adquiriu na caixa **Chave de licença**. A versão integrada atual é **2019.2**.

     * Se você selecionar o componente **Pacote de Produtividade SSIS da KingswaySoft**, poderá instalar o conjunto de componentes de [Pacote de Produtividade SSIS](https://www.kingswaysoft.com/products/ssis-productivity-pack) da KingswaySoft em seu Azure-SSIS IR inserindo a chave de licença do produto adquirida deles na caixa **Chave de licença**. A versão integrada atual é **10.0**.

     * Se você selecionar o componente **Xtract IS da Theobald Software**, poderá instalar o conjunto de componentes [Xtract IS](https://theobald-software.com/en/xtract-is/) para o sistema SAP (ERP, S/4HANA, BW) da Theobald Software em seu Azure-SSIS IR arrastando e soltando/carregando o arquivo de licença do produto que você adquiriu na caixa **Arquivo de licença**. A versão integrada atual é **6.1.1.3**.

   Suas configurações personalizadas expressas adicionadas serão exibidas na seção **Configurações Avançadas**. Para removê-los, marque suas caixas de seleção e, em seguida, selecione **Excluir**.

   ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Ao configurar ou reconfigurar seu Azure-SSIS IR com o PowerShell, você pode adicionar ou remover as configurações personalizadas executando o cmdlet `Set-AzDataFactoryV2IntegrationRuntime` antes de iniciar o Azure-SSIS IR.
   
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
   
   Depois de concluir a configuração personalizada padrão e iniciar o Azure-SSIS IR, você pode encontrar a saída padrão de *main.cmd* e outros logs de execução na pasta *main.cmd.log* do seu contêiner de armazenamento.

1. Para exibir alguns exemplos de configurações personalizadas padrão, conecte-se ao nosso contêiner de Versão Prévia Pública usando o Gerenciador de Armazenamento do Azure.

   a. Em **(Local e conectado)** , clique com botão direito em **Contas de Armazenamento**, selecione **Conectar ao armazenamento do Azure**, selecione **Usar uma cadeia de caracteres de conexão ou uma URI de assinatura de acesso compartilhado** e, em seguida, selecione **Próximo**.

      ![Conecte-se ao armazenamento do Azure com a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecione **Usar um URI de SAS** e, em seguida, na caixa **URI**, insira o seguinte URI de SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fornecer a Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecione **Próximo** e selecione **Conectar**.

   d. No painel esquerdo, selecione o contêiner **publicpreview** conectado e clique duas vezes na pasta *CustomSetupScript*. Nesta pasta, você encontrará os seguintes itens:

      * Uma pasta *Sample*, que contém uma configuração personalizada para instalar uma tarefa básica em cada nó do seu Azure-SSIS IR. A tarefa apenas executará a suspensão por alguns segundos. A pasta também contém uma pasta *gacutil*, cujo conteúdo inteiro (*gacutil.exe*, *gacutil.exe.config* e *1033\gacutlrc.dll*) pode ser copiado no estado em que se encontra para seu contêiner.

      * Uma pasta *UserScenarios*, que contém vários exemplos de configurações personalizadas de cenários de usuário real.

        ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Clique duas vezes na pasta *UserScenarios* para localizar os seguintes itens:

      * Uma pasta *.NET FRAMEWORK 3.5*, que contém uma configuração personalizada (*main.cmd*) para instalar uma versão anterior do .NET Framework que pode ser necessária para componentes personalizados em cada nó do seu Azure-SSIS IR.

      * Uma pasta *BCP*, que contém um script de configuração personalizada (*main.cmd*) para instalar utilitários de linha de comando do SQL Server (*MsSqlCmdLnUtils.msi*), incluindo o programa de cópia em massa (*bcp*), em cada nó do seu Azure-SSIS IR.

      * Uma pasta *EXCEL*, que contém um script de configuração personalizada (*main.cmd*) para instalar assemblies e bibliotecas C# que você pode usar em tarefas de script para ler e gravar dinamicamente arquivos do Excel em cada nó do seu Azure-SSIS IR. 
      
        Primeiro, baixe [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/) e carregue-os juntos com *main.cmd* para seu contêiner. Como alternativa, se desejar usar apenas o Gerenciador de Conexões do Excel padrão, a origem do Excel e o destino do Excel, o redistribuível de acesso necessário já estará pré-instalado em seu Azure-SSIS IR; portanto, você não precisa de nenhuma configuração personalizada.
      
      * Uma pasta *MYSQL ODBC*, que contém um script de configuração personalizada (*main.cmd*) para instalar os drivers ODBC do MySQL em cada nó do seu Azure-SSIS IR. Essa instalação permite que você use o Gerenciador de Conexões ODBC, a origem e o destino para se conectar ao servidor MySQL. 
     
        Primeiro, [baixe as versões de 64 bits e 32 bits mais recentes dos instaladores do driver ODBC do MySQL](https://dev.mysql.com/downloads/connector/odbc/) (por exemplo, *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi*) e carregue todas elas juntas com *main.cmd* para o seu contêiner.

      * Uma pasta *ORACLE ENTERPRISE*, que contém um script de configuração personalizada (*main.cmd*) e o arquivo de configuração de instalação silenciosa (*client.rsp*) para instalar os conectores Oracle e o driver OCI em cada nó do seu Azure-SSIS IR Edição Enterprise. Essa instalação permite que você use o Gerenciador de Conexões Oracle, a origem e o destino para se conectar ao servidor Oracle. 
      
        Primeiro, baixe o Microsoft Connectors v5.0 para Oracle (*AttunitySSISOraAdaptersSetup.msi* e *AttunitySSISOraAdaptersSetup64.msi*) do [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o cliente Oracle mais recente (por exemplo, *winx64_12102_client.zip*) da [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) e carregue todos eles juntos com *main.cmd* e *client.rsp* para seu contêiner. Se você usar TNS para se conectar ao Oracle, você também precisa baixar *tnsnames.ora*, editá-lo e carregá-lo no seu contêiner, portanto ele pode ser copiado para a pasta de instalação do Oracle durante a instalação.

      * Uma pasta *ORACLE STANDARD ADO.NET*, que contém um script de configuração personalizada (*main.cmd*) para instalar o driver do Oracle ODP.NET em cada nó do seu Azure-SSIS IR. Essa instalação permite que você use o Gerenciador de Conexões ADO.NET, a origem e o destino para se conectar ao servidor Oracle. 
      
        Primeiro, [baixe o driver do Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (por exemplo, *ODP.NET_Managed_ODAC122cR1.zip*) e carregue-o juntamente com *main.cmd* para o seu contêiner.
       
      * Uma pasta *ORACLE STANDARD ODBC*, que contém um script de configuração personalizada (*main.cmd*) para instalar o driver ODBC da Oracle e configurar o DSN (nome da fonte de dados) em cada nó do seu Azure-SSIS IR. Essa instalação permite usar o Gerenciador de Conexão ODBC, a origem, o destino ou o Gerenciador de Conexões do Power Query e a origem com o tipo de fonte de dados ODBC para se conectar ao servidor Oracle. 
      
        Primeiro, baixe o Cliente Instantâneo Oracle (Pacote Basic ou Basic Lite) e o Pacote ODBC e carregue todos eles juntamente com o *main.cmd* para o seu contêiner:
        * [Baixe os pacotes de 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Pacote Basic: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Pacote Basic Lite: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Pacote ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Baixe os pacotes de 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Pacote Basic: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Pacote Basic Lite: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pacote ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Uma pasta *ORACLE STANDARD OLEDB*, que contém um script de configuração personalizada (*main.cmd*) para instalar o driver do Oracle OLEDB em cada nó do seu Azure-SSIS IR. Essa instalação permite que você use o Gerenciador de Conexões OLEDB, a origem e o destino para se conectar ao servidor Oracle. 
     
        Primeiro, [baixe o driver do Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (por exemplo, *ODAC122010Xcopy_x64.zip*) e carregue-o juntamente com *main.cmd* para o seu contêiner.

      * Uma pasta *POSTGRESQL ODB*, que contém um script de configuração personalizada (*main.cmd*) para instalar os drivers ODBC do PostgreSQL em cada nó do seu Azure-SSIS IR. Essa instalação permite que você use o Gerenciador de Conexões ODBC, a origem e o destino para se conectar ao servidor PostgreSQL. 
     
        Primeiro, [baixe as versões de 64 bits e 32 bits mais recentes dos instaladores do driver ODBC do PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (por exemplo, *psqlodbc_x64.msi* e *psqlodbc_x86.msi*) e carregue todas elas juntas com *main.cmd* para o seu contêiner.

      * Uma pasta *SAP BW*, que contém um script de configuração personalizada (*main.cmd*) para instalar o assembly de conector .NET do SAP (*librfc32.dll*) em cada nó do seu Azure-SSIS IR Edição Enterprise. Essa instalação permite que você use o Gerenciador de Conexões SAP BW (Business Warehouse), o Gerenciador de Conexões, a origem e o destino para se conectar ao servidor SAP BW. 
      
        Primeiro, carregue a versão 64 bits ou 32 bits de *librfc32.dll* da pasta de instalação do SAP juntamente com *main.cmd* para seu contêiner. O script copia o assembly do SAP para a pasta *%windir%\SysWow64* ou *%windir%\System32* durante a instalação.

      * Uma pasta *STORAGE*, que contém um script de configuração personalizada (*main.cmd*) para instalar o Azure PowerShell em cada nó do Azure-SSIS IR. Essa instalação permite que você implante e execute de pacotes SSIS que executam [scripts do PowerShell para manipular a sua conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Copie *main.cmd*, um *AzurePowerShell.msi* de exemplo (ou use a versão mais recente) e *storage.ps1* para seu contêiner. Use *PowerShell.dtsx* como um modelo para os seus pacotes. O modelo de pacote combina uma [tarefa de download do Blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que baixa *storage.ps1* como um script do PowerShell que pode ser modificado, e uma [tarefa Executar processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), que executa o script em cada nó.

      * Uma pasta *TERADATA*, que contém um script de configuração personalizada (*main.cmd*), o arquivo associado (*install.cmd*) e os pacotes do instalador ( *.msi*). Esses arquivos instalam os conectores Teradata, a API TPT (Teradata Parallel Transporter) e o driver ODBC em cada nó do Azure-SSIS IR Edição Enterprise. Essa instalação permite que você use o Gerenciador de Conexões Teradata, a origem e o destino para se conectar ao servidor Teradata. 
      
        Primeiro, [baixe o arquivo zip Ferramentas e Utilitários do Teradata 15.x](http://partnerintelligence.teradata.com) (por exemplo, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) e carregue-o junto com os arquivos *.cmd* e *.msi* mencionados anteriormente para seu contêiner.

      * Uma pasta *TLS 1.2*, que contém um script de configuração personalizada (*main.cmd*) para usar criptografia forte/protocolo de rede segura (TLS 1.2) e desabilitar versões mais antigas de SSL/TLS em cada nó do seu Azure-SSIS IR.

      * Uma pasta *ZULU OPENJDK*, que contém um script de configuração personalizada (*main.cmd*) e um arquivo do PowerShell (*install_openjdk.ps1*) para instalar o Zulu OpenJDK em cada nó do seu Azure-SSIS IR. Essa instalação permite que você use o Azure Data Lake Storage e conectores de Arquivo Flexíveis para processar arquivos ORC e Parquet. Para obter mais informações, confira [Feature Pack do Azure para o Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Primeiro, [baixe o Zulu OpenJDK mais recente](https://www.azul.com/downloads/zulu/zulu-windows/) (por exemplo, *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) e carregue-o junto com *main.cmd* e *install_openjdk.ps1* para o seu contêiner.

        ![Pastas na pasta de cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Para testar esses exemplos de configuração personalizada, copie o conteúdo da pasta selecionada para seu contêiner.
   
      Quando você configura ou reconfigura o Azure-SSIS IR usando a interface do usuário do Data Factory, marque a caixa de seleção **Personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** na seção **Configurações Avançadas** e insira o URI de SAS do seu contêiner na caixa **URI de SAS de contêiner de configuração personalizada**.
   
      Quando você configurar ou reconfigurar o Azure-SSIS IR com o PowerShell, execute o cmdlet `Set-AzDataFactoryV2IntegrationRuntime` com o URI de SAS do seu contêiner como o valor para o parâmetro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a Edição Enterprise do Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Desenvolver componentes personalizados pagos ou licenciados para o Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
