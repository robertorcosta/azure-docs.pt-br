---
title: Execute um pacote SSIS com a atividade do pacote Execute SSIS
description: Este artigo descreve como executar um pacote SSIS (SSIS) sql server integration services (SSIS) em um pipeline azure Data Factory usando a atividade Execute SSIS Package.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: f400463f6102d46d9da48bbb10466ad4ca04a69b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413230"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Executar um pacote SSIS com a atividade Executar pacote SSIS no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar um pacote SSIS (SSIS) sql server integration services (SSIS) em um pipeline azure Data Factory usando a atividade Execute SSIS Package. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Crie um tempo de execução de integração Azure-SSIS (IR) se você ainda não tiver um seguindo as instruções passo a passo no [Tutorial: Provisionamento Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Executar um pacote no portal do Azure
Nesta seção, você usa a interface de usuário (UI) ou aplicativo da Fábrica de Dados para criar um pipeline de Fábrica de Dados com uma atividade execute ssis package que executa seu pacote SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade Executar Pacote do SSIS
Nesta etapa, você usa a UI ou aplicativo Data Factory para criar um pipeline. Adiciona uma atividade Executar pacote SSIS ao pipeline e configure-a para executar o pacote SSIS. 

1. Em sua visão geral da Fábrica de Dados ou na página inicial no portal Azure, selecione o **bloco Autor & Monitor** para iniciar a UI ou aplicativo da Fábrica de Dados em uma guia separada. 

   ![Home page do Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na página **Introdução**, selecione **Criar pipeline**. 

   ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Na **caixa de ferramentas Atividades,** expanda **geral**. Em seguida, arraste uma atividade **do Pacote Execute SSIS** para a superfície do designer de pipeline. 

   ![Arraste uma atividade Executar pacote SSIS para a superfície do designer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Na guia **Geral** para a atividade Executar pacote SSIS, forneça um nome e uma descrição para a atividade. Defina **valores** opcionais de tempo e **repetição.**

   ![Definir propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Na guia **Configurações** para executar a atividade do pacote SSIS, selecione um IR Azure-SSIS onde deseja executar seu pacote. Se o pacote usar a autenticação do Windows para acessar armazenamentos de dados (por exemplo, servidores SQL ou compartilhamentos de arquivos no local ou arquivos do Azure), selecione a caixa de **seleção de autenticação** do Windows. Digite os valores das credenciais de execução do pacote nas caixas **Domínio,** **Nome de Usuário**e **Senha.** 

    Alternativamente, você pode usar segredos armazenados em seu cofre de chaves do Azure como seus valores. Para isso, selecione a caixa de seleção **AZURE KEY VAULT** ao lado da credencial relevante. Selecione ou edite seu serviço vinculado ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome ou versão secreta para o seu valor de credencial.

    Quando você cria ou edita seu serviço vinculado ao cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder acesso de identidade gerenciado da Fábrica de Dados ao seu cofre principal se você ainda não fez isso. Você também pode inserir seus segredos `<Key vault linked service name>/<secret name>/<secret version>`diretamente no seguinte formato: . Se o pacote precisar de tempo de execução de 32 bits, selecione a caixa de seleção **em tempo de execução de 32 Bits.**

   Para **a localização do pacote,** selecione **SSISDB,** **Sistema de arquivos (Pacote)**, **Sistema de arquivos (Projeto)** ou **Pacote Incorporado**. Se você selecionar **o SSISDB** como seu local de pacote, que será automaticamente selecionado se o seu IR Azure-SSIS foi provisionado com o catálogo SSIS (SSISDB) hospedado por um servidor de banco de dados SQL do Azure ou instância gerenciada, especifique seu pacote para executar que foi implantado no SSISDB. 

    Se o IR do Azure-SSIS estiver em execução e a caixa **de seleção de entradas manual** estiver limpa, navegue e selecione suas pastas, projetos, pacotes ou ambientes existentes no SSISDB. Selecione **Atualizar** para buscar suas pastas, projetos, pacotes ou ambientes recém-adicionados do SSISDB para que eles estejam disponíveis para navegação e seleção. Para navegar ou selecionar os ambientes para execuções de pacotes, você deve configurar seus projetos com antecedência para adicionar esses ambientes como referências das mesmas pastas no SSISDB. Para obter mais informações, consulte [Criar e mapear ambientes SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Selecione a **caixa de** seleção personalizada se quiser inserir seu nome de registro personalizado. 

   ![Definir propriedades na guia Configurações — Automático](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Se o seu IR Azure-SSIS não estiver em execução ou a caixa de seleção **de entradas** `<folder name>/<project name>/<package name>.dtsx` manual `<folder name>/<environment name>`estiver selecionada, digite os caminhos do pacote e do ambiente do SSISDB diretamente nos seguintes formatos: e .

   ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Se você selecionar **O Sistema de Arquivos (Pacote)** como o local do pacote, que será automaticamente selecionado se o seu IR Azure-SSIS foi provisionado sem SSISDB, especifique seu pacote para ser executado fornecendo um caminho de Convenção de Nomeação Universal (UNC) para o arquivo do pacote (`.dtsx`) na caixa de caminho do **pacote.** Por exemplo, se você armazenar seu pacote em Arquivos `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`Azure, seu caminho de pacote é . 
   
   Se você configurar seu pacote em um arquivo separado, você também precisará`.dtsConfig`fornecer um caminho UNC para o seu arquivo de configuração ( ) na caixa **de caminho Configuração.** Por exemplo, se você armazenar sua configuração em `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`Arquivos Azure, seu caminho de configuração será .

   ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Se você selecionar **O Sistema de Arquivos (Project)** como local do pacote, especifique o pacote para ser executado fornecendo um caminho UNC para o arquivo do projeto (`.ispac`) na caixa de caminho do **Projeto** e um arquivo de pacote (`.dtsx`) do seu projeto na caixa nome do **pacote.** Por exemplo, se você armazenar seu projeto em Arquivos `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`Azure, seu caminho de projeto é .

   ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Em seguida, especifique as credenciais para acessar seus arquivos de projeto, pacote ou configuração. Se você inseriu anteriormente os valores das credenciais de execução do pacote (veja anterior), você pode reutilizá-los selecionando o mesmo que a **caixa de seleção de credenciais de execução** do pacote. Caso contrário, digite os valores das credenciais de acesso ao pacote nas caixas **Domínio,** **Nome de Usuário**e **Senha.** Por exemplo, se você armazenar seu projeto, pacote ou configuração `Azure`em Arquivos `<storage account name>`Azure, `<storage account key>`o domínio é , o nome de usuário é , e a senha é . 

   Alternativamente, você pode usar segredos armazenados em seu cofre de chaves como seus valores (ver anterior). Essas credenciais são usadas para acessar seus pacotes e pacotes infantis na Execute Package Task, tudo a partir de seu próprio caminho ou do mesmo projeto, bem como configurações, que incluem aquelas especificadas em seus pacotes. 

   Se você selecionar **o pacote Incorporado** como o local do pacote, arraste e solte o pacote para executá-lo ou **carregue-o** de uma pasta de arquivo na caixa fornecida. Seu pacote será automaticamente compactado e incorporado na carga de atividade. Uma vez incorporado, você pode **baixar** seu pacote mais tarde para edição. Você também pode **parametrizar** seu pacote incorporado atribuindo-o a um parâmetro de pipeline que pode ser usado em várias atividades, otimizando assim o tamanho da carga útil do pipeline. Se o pacote incorporado não estiver todo criptografado e detectarmos o uso da Execute Package Task nele, a caixa de **seleção Execute Package Task** será automaticamente selecionada e os pacotes infantis relevantes com suas referências do sistema de arquivos serão automaticamente adicionados para que você também os incorpore. Se não conseguirmos detectar o uso da Execute Package Task, você terá que selecionar manualmente a caixa de **seleção Execute Package Task** e adicionar os pacotes infantis relevantes com as referências do sistema de arquivos um a um para que você também os incorpore. Se os pacotes filho usarem referências do SQL Server, certifique-se de que o SQL Server esteja acessível pelo seu IR Azure-SSIS.  O uso de referências de projetos para pacotes infantis não é suportado no momento.
   
   ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Se você usou o nível de proteção **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou seu pacote via SQL Server Data Tools, digite o valor da sua senha na caixa **de senha de criptografia.** Alternativamente, você pode usar um segredo armazenado em seu cofre de chaves como seu valor (veja anterior). Se você usou o nível de proteção **EncryptSensitiveWithUserKey,** reinsira seus valores sensíveis em arquivos de configuração ou nas **guias SSIS Parameters,** **Connection Managers**ou **Property Overrests** (veja mais tarde). 

   Se você usou o nível de proteção **EncryptAllWithUserKey,** ele não será suportado. Você precisa reconfigurar seu pacote para usar outro nível de proteção através de Ferramentas de Dados do Servidor SQL ou do `dtutil` utilitário de linha de comando. 
   
   Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Selecione a **caixa de** seleção personalizada se quiser inserir seu nome de registro personalizado. Se você quiser registrar suas execuções de pacote além de usar os provedores de log padrão que podem ser especificados em seu pacote, especifique sua pasta de log fornecendo seu caminho UNC na caixa **de caminho De registrar.** Por exemplo, se você armazenar seus logs em Arquivos `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`Azure, seu caminho de registro será . Uma subpasta é criada neste caminho para cada execução de pacote individual e nomeada após o Execute SSIS Package activity execut a ID, no qual os arquivos de log são gerados a cada cinco minutos. 
   
   Finalmente, especifique as credenciais para acessar sua pasta de log. Se você inseriu anteriormente os valores das credenciais de acesso ao pacote (veja anterior), você pode reutilizá-los selecionando o Mesmo que a **caixa de seleção de credenciais de acesso** ao pacote. Caso contrário, digite os valores para suas credenciais de acesso de registro nas caixas **Domínio,** **Nome de Usuário**e **Senha.** Por exemplo, se você armazenar seus logs em `Azure`Arquivos Azure, o domínio é , o nome de usuário é `<storage account name>`, e a senha é `<storage account key>`. 

    Alternativamente, você pode usar segredos armazenados em seu cofre de chaves como seus valores (ver anterior). Essas credenciais são usadas para armazenar seus registros. 
   
   Para todos os caminhos UNC mencionados anteriormente, o nome do arquivo totalmente qualificado deve ter menos de 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

1. Na guia **Parâmetros SSIS** para a atividade executar o pacote SSIS, se o IR do Azure-SSIS estiver em execução, o **SSISDB** será selecionado como local do pacote e a caixa de seleção **de entradas manuais** na guia **Configurações** é limpa, os parâmetros SSIS existentes em seu projeto ou pacote selecionado do SSISDB são exibidos para que você atribua valores a eles. Caso contrário, você pode inseri-los um a um para atribuir valores a eles manualmente. Certifique-se de que eles existem e estão corretamente inseridos para que a execução do pacote seja bem sucedida. 
   
   Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote via SQL Server Data Tools and **File System (Package)** ou **File System (Project)** for selecionado como seu local de pacote, você também precisará reinserir seus parâmetros sensíveis para atribuir valores a eles em arquivos de configuração ou nesta guia. 
   
   Quando você atribui valores aos seus parâmetros, você pode adicionar conteúdo dinâmico usando expressões, funções, variáveis do sistema data factory e parâmetros ou variáveis do pipeline da Fábrica de Dados. Alternativamente, você pode usar segredos armazenados em seu cofre de chaves como seus valores (ver anterior).

   ![Definir propriedades na guia Parâmetros SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Na guia **Gerentes de conexão** para a atividade Executar pacote SSIS, se o IR do Azure-SSIS estiver em execução, o **SSISDB** será selecionado como local do pacote e a caixa de seleção **de entradas manuais** na guia **Configurações** será limpa, os gerentes de conexão existentes em seu projeto ou pacote selecionado do SSISDB serão exibidos para que você atribua valores às suas propriedades. Caso contrário, você pode inseri-los um a um para atribuir valores às suas propriedades manualmente. Certifique-se de que eles existem e estão corretamente inseridos para que a execução do pacote seja bem sucedida. 
   
   Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote via SQL Server Data Tools and **File System (Package)** ou **File System (Project)** for selecionado como seu local de pacote, você também precisa reinserir suas propriedades confidenciais do gerenciador de conexão para atribuir valores a eles em arquivos de configuração ou nesta guia. 
   
   Quando você atribui valores às propriedades do gerenciador de conexões, você pode adicionar conteúdo dinâmico usando expressões, funções, variáveis do sistema Data Factory e parâmetros ou variáveis do pipeline da Fábrica de Dados. Alternativamente, você pode usar segredos armazenados em seu cofre de chaves como seus valores (ver anterior).

   ![Definir propriedades na guia Gerenciadores de Conexões](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Na guia **'Sobrerações de propriedades'** para a atividade Executar pacote SSIS, insira os caminhos das propriedades existentes no pacote selecionado um a um para atribuir valores a eles manualmente. Certifique-se de que eles existem e estão corretamente inseridos para que a execução do pacote seja bem sucedida. Por exemplo, para substituir o valor da variável de usuário, `\Package.Variables[User::<variable name>].Value`insira seu caminho no seguinte formato: . 
   
   Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote via SQL Server Data Tools and **File System (Package)** ou **File System (Project)** for selecionado como seu local de pacote, você também precisa reinserir suas propriedades confidenciais para atribuir valores a eles em arquivos de configuração ou nesta guia. 
   
   Quando você atribui valores às suas propriedades, você pode adicionar conteúdo dinâmico usando expressões, funções, variáveis do sistema Data Factory e parâmetros ou variáveis do pipeline da Fábrica de Dados.

   ![Definir propriedades na guia Substituições de Propriedades](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Os valores atribuídos nos arquivos de configuração e na guia **Parâmetros SSIS** podem ser substituídos usando as guias **'Gerentes de conexão'** ou **sobreposição de propriedades.** Os valores atribuídos na guia **Gerentes de conexão** também podem ser substituídos usando a guia **'Sobrerções de propriedades'.**

1. Para validar a configuração do pipeline, **selecione Validar** na barra de ferramentas. Para fechar o relatório **>>** de validação de **pipeline,** selecione .

1. Para publicar o pipeline na Fábrica de Dados, **selecione Publicar tudo**. 

### <a name="run-the-pipeline"></a>Executar o pipeline
Nesta etapa, você dispara uma execução de pipeline. 

1. Para ativar uma execução de pipeline, **selecione 'Disparar'** na barra de ferramentas e selecione **'Gatilho' agora**. 

   ![Disparar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Execução de Pipeline**, selecione **Concluir**. 

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você vê o pipeline executado e seu status, juntamente com outras informações, como o **tempo de Execução.** Para atualizar a exibição, selecione **Atualizar**.

   ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecione o link **Exibir execuções de atividade** na coluna **Ações**. Você vê apenas uma atividade executada porque o pipeline tem apenas uma atividade. É a atividade do Pacote Execute SSIS.

   ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Execute a seguinte consulta no banco de dados SSISDB em seu servidor SQL para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificar as execuções do pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Você também pode obter o ID de execução do SSISDB da saída da execução da atividade do pipeline e usar o ID para verificar registros de execução mais abrangentes e mensagens de erro no SQL Server Management Studio.

   ![Obter a ID de execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Programar o pipeline com um gatilho

Você também pode criar um gatilho programado para o seu pipeline para que o pipeline seja executado em um horário, como de hora em hora ou diariamente. Para um exemplo, consulte [Criar uma data factory - Interface do Usuário do Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Executar um pacote com o PowerShell
Nesta seção, você usa o Azure PowerShell para criar um pipeline de Fábrica de Dados com uma atividade execute ssis package que executa seu pacote SSIS. 

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções passo a passo em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Crie uma fábrica de dados com o Azure-SSIS IR
Você pode usar uma fábrica de dados existente que já tenha o Azure-SSIS IR provisionado ou criar uma nova fábrica de dados com o Azure-SSIS IR. Siga as instruções passo-a-passo no [Tutorial: Implante pacotes SSIS no Azure via PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade Executar Pacote do SSIS 
Nesta etapa, você cria um pipeline com uma atividade Executar pacote SSIS. A atividade é executada em seu pacote SSIS. 

1. Crie um arquivo JSON chamado *RunSISPackagePipeline.json* na pasta *C:\ADF\RunSSISPackage* com conteúdo semelhante ao exemplo a seguir.

   > [!IMPORTANT]
   > Substitua nomes de objetos, descrições e caminhos, valores de propriedade ou parâmetro, senhas e outros valores variáveis antes de salvar o arquivo. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Para executar pacotes armazenados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos Do Zure, digite os valores das propriedades de localização do pacote e do registro da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes dentro de projetos armazenados em sistemas de arquivos, compartilhamentos de arquivos ou arquivos Do Zure, digite os valores da propriedade de localização do pacote da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes incorporados, digite os valores da propriedade de localização do pacote da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. No Azure PowerShell, mude para a pasta *C:\ADF\RunSSISPackage.*

3. Para criar o pipeline **RunSSISPackagePipeline**, execute o cmdlet **Set-AzDataFactoryV2Pipeline.**

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Aqui está a saída da amostra:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Executar o pipeline
Use o **cmdlet Invoke-AzDataFactoryV2Pipeline** para executar o pipeline. O cmdlet retorna a ID da execução de pipeline para monitoramento futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

Execute o script do PowerShell a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados. Copie ou cole o seguinte script na janela PowerShell e selecione Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Você também pode monitorar o pipeline usando o portal Azure. Para obter instruções passo a passo, consulte [Monitorar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Programar o pipeline com um gatilho
Na etapa anterior, você executou o oleoduto sob demanda. Você também pode criar um gatilho de cronograma para executar o pipeline em um cronograma, como de hora em hora ou diariamente.

1. Crie um arquivo JSON chamado *MyTrigger.json* na pasta *C:\ADF\RunSSISPackage* com o seguinte conteúdo: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. No Azure PowerShell, mude para a pasta *C:\ADF\RunSSISPackage.*
1. Execute o **cmdlet Set-AzDataFactoryV2Trigger,** que cria o gatilho. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Por padrão, o gatilho está no estado interrompido. Inicie o gatilho executando o **cmdlet Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Confirme se o gatilho foi iniciado executando o **cmdlet Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Execute o seguinte comando após a próxima hora. Por exemplo, se a hora atual for 15:25 UTC, execute o comando às 16:00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Execute a seguinte consulta no banco de dados SSISDB em seu servidor SQL para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Próximas etapas
Consulte a postagem blog a seguir:
- [Modernize e amplie seus fluxos de trabalho ETL/ELT com atividades do SSIS em pipelines azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
