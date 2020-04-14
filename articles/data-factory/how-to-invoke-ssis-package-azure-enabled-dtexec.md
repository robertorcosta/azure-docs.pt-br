---
title: Execute pacotes SSIS (SSIS) ssis (SSIS) do SQL Server Integration Services (SSIS) com o utilitário dtexec habilitado para Azure
description: Saiba como executar pacotes SQL Server Integration Services (SSIS) com o utilitário dtexec habilitado para Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 006d4fa9ed09170a423e796e893b817e079e861b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261917"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Execute pacotes de serviços de integração do sql server com o utilitário dtexec habilitado para Azure
Este artigo descreve o utilitário de prompt de comando dtexec (AzureDTExec) habilitado para Azure. Ele é usado para executar pacotes SQL Server Integration Services (SSIS) no Azure-SSIS Integration Runtime (IR) na Fábrica de Dados Azure.

O utilitário dtexec tradicional vem com o SQL Server. Para obter mais informações, consulte [o utilitário dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). É frequentemente invocado por orquestradores ou agendadores de terceiros, como ActiveBatch e Control-M, para executar pacotes SSIS no local. 

O utilitário AzureDTExec moderno vem com uma ferramenta SQL Server Management Studio (SSMS). Ele também pode ser invocado por orquestradores ou agendadores de terceiros para executar pacotes SSIS no Azure. Facilita o levantamento e deslocamento ou migração de seus pacotes SSIS para a nuvem. Após a migração, se você quiser continuar usando orquestradores ou agendadores de terceiros em suas operações diárias, eles agora podem invocar o AzureDTExec em vez de dtexec.

O AzureDTExec executa seus pacotes como Executar atividades do Pacote SSIS em pipelines de fábrica de dados. Para obter mais informações, consulte [executar pacotes SSIS como atividades da Fábrica de Dados do Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

O AzureDTExec pode ser configurado via SSMS para usar um aplicativo Azure Active Directory (Azure AD) que gera pipelines em sua fábrica de dados. Ele também pode ser configurado para acessar sistemas de arquivos, compartilhamentos de arquivos ou arquivos Azure onde você armazena seus pacotes. Com base nos valores que você dá para suas opções de invocação, o AzureDTExec gera e executa um pipeline exclusivo da Data Factory com uma atividade execute ssis package nele. Invocando o AzureDTExec com os mesmos valores para suas opções reprisar o pipeline existente.

## <a name="prerequisites"></a>Pré-requisitos
Para usar o AzureDTExec, baixe e instale a versão mais recente do SSMS, que é a versão 18.3 ou posterior. Baixe-o [neste site](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Configure o utilitário AzureDTExec
A instalação de SSMS em sua máquina local também instala o AzureDTExec. Para configurar suas configurações, inicie o SSMS com a opção **Executar como administrador.** Em seguida, selecione **Ferramentas** > **Migrar para o Azure** > **Configure DTExec habilitado para Azure**.

![Configurar menu dtexec habilitado para Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Esta ação abre uma janela **AzureDTExecConfig** que precisa ser aberta com privilégios administrativos para que ela seja escrita no arquivo *de configurações AzureDTExec..* Se você não tiver executado o SSMS como administrador, uma janela UAC (User Account Control, controle de conta de usuário) será aberta. Digite sua senha de admin para elevar seus privilégios.

![Configure as configurações de dtexec habilitadas para Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Na janela **AzureDTExecConfig,** digite as configurações da seguinte forma:

- **ApplicationId**: Digite o identificador exclusivo do aplicativo Azure AD que você cria com as permissões certas para gerar pipelines em sua fábrica de dados. Para obter mais informações, consulte [Criar um aplicativo azure AD e o principal de serviço através do portal Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **AutenticaçãoChave**: Digite a chave de autenticação do seu aplicativo Azure AD.
- **TenantId**: Digite o identificador exclusivo do inquilino Azure AD, sob o qual seu aplicativo Azure AD é criado.
- **DataFactory**: Digite o nome de sua fábrica de dados na qual os pipelines exclusivos com a atividade do Pacote Execute SSIS neles são gerados com base nos valores das opções fornecidas quando você invoca o AzureDTExec.
- **NOME :** Digite o nome do IR Azure-SSIS em sua fábrica de dados, na qual os pacotes especificados em seu caminho de Convenção de Nomeação Universal (UNC) serão executados quando você invocar o AzureDTExec.
- **PipelineNameHashStrLen**: Digite o comprimento das strings hash a serem geradas a partir dos valores de opções que você fornece quando você invoca o AzureDTExec. As strings são usadas para formar nomes exclusivos para os pipelines da Fábrica de Dados que executam seus pacotes no IR Azure-SSIS. Normalmente, um comprimento de 32 caracteres é suficiente.
- **ResourceGroup**: Digite o nome do grupo de recursos do Azure no qual sua fábrica de dados foi criada.
- **SubscriptionId**: Digite o identificador exclusivo da assinatura do Azure, sob o qual sua fábrica de dados foi criada.
- **LogAccessDomain**: Digite a credencial de domínio para acessar sua pasta de log em seu caminho UNC ao gravar arquivos de log, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogAccessPassword**: Digite a credencial de senha para acessar sua pasta de log em seu caminho UNC ao gravar arquivos de log, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogAccessUserName**: Digite a credencial de nome de usuário para acessar sua pasta de log em seu caminho UNC ao gravar arquivos de log, o que é necessário quando **o LogPath** é especificado e **o LogLevel** não é **nulo**.
- **LogLevel**: Digite o escopo selecionado de registro a partir de opções **de nulidade**predefinida, **Básica,** **Verbose**ou **Desempenho** para execuções de pacotes no IR Azure-SSIS.
- **LogPath**: Digite o caminho UNC da pasta log, no qual os arquivos de log das execuções do pacote no IR Azure-SSIS são gravados.
- **PackageAccessDomain**: Digite a credencial de domínio para acessar seus pacotes em seu caminho UNC especificado quando você invoca o AzureDTExec.
- **PackageAccessPassword**: Digite a credencial de senha para acessar seus pacotes em seu caminho UNC especificado quando você invoca o AzureDTExec.
- **PackageAccessUserName**: Digite a credencial de nome de usuário para acessar seus pacotes em seu caminho UNC especificado quando você invoca o AzureDTExec.

Para armazenar seus pacotes e registrar arquivos em sistemas de arquivos ou compartilhamentos de arquivos no local, junte-se ao seu IR Azure-SSIS a uma rede virtual conectada à sua rede local para que possa buscar seus pacotes e gravar seus arquivos de log. Para obter mais informações, consulte [Juntar-se a um IR Azure-SSIS em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Para evitar mostrar valores sensíveis gravados no arquivo *AzureDTExec.settings* em texto simples, codificamos-os em seqüências de codificação Base64. Quando você invoca o AzureDTExec, todas as strings codificadas pelo Base64 são decodificadas de volta aos seus valores originais. Você pode proteger ainda mais o arquivo *azureDTExec.settings* limitando as contas que podem acessá-lo.

## <a name="invoke-the-azuredtexec-utility"></a>Invoque o utilitário AzureDTExec
Você pode invocar o AzureDTExec no prompt de linha de comando e fornecer os valores relevantes para opções específicas no seu cenário de caso de uso.

O utilitário está `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`instalado em . Você pode adicionar seu caminho à variável de ambiente 'PATH' para que ele seja invocado de qualquer lugar.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Invocar o AzureDTExec oferece opções semelhantes como invocar dtexec. Para saber mais, veja [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Aqui estão as opções que são suportadas atualmente:

- **/F[ile]**: Carrega um pacote armazenado no sistema de arquivos, compartilhamento de arquivos ou arquivos Azure. Como o valor para essa opção, você pode especificar o caminho UNC para o arquivo do pacote no sistema de arquivos, compartilhamento de arquivos ou Arquivos Azure com sua extensão .dtsx. Se o caminho UNC especificado contiver qualquer espaço, coloque aspas em todo o caminho.
- **/Conf[igFile]**: Especifica um arquivo de configuração para extrair valores. Usando esta opção, você pode definir uma configuração de tempo de execução para o seu pacote que difere da especificada no momento do projeto. Você pode armazenar diferentes configurações em um arquivo de configuração XML e, em seguida, carregá-las antes da execução do pacote. Para obter mais informações, consulte [as configurações do pacote SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Para especificar o valor para essa opção, use o caminho UNC para o seu arquivo de configuração no sistema de arquivos, compartilhamento de arquivos ou Arquivos Azure com sua extensão dtsConfig. Se o caminho UNC especificado contiver qualquer espaço, coloque aspas em todo o caminho.
- **/Conn[ection]**: Especifica strings de conexão para os gerentes de conexão existentes em seu pacote. Usando essa opção, você pode definir strings de conexão em tempo de execução para os gerentes de conexão existentes em seu pacote que diferem das especificadas no momento do projeto. Especifique o valor para `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`esta opção da seguinte forma: .
- **/Set**: Substitui a configuração de um parâmetro, variável, propriedade, contêiner, provedor de log, enumerador forcada ou conexão em seu pacote. Esta opção pode ser especificada várias vezes. Especifique o valor para `property_path;value`esta opção da seguinte forma: . Por exemplo, `\package.variables[counter].Value;1` substitui o `counter` valor da variável como 1. Você pode usar o assistente **de configuração** do pacote `property_path` para encontrar, copiar e colar o valor dos itens do seu pacote cujo valor você deseja substituir. Para obter mais informações, consulte [o assistente de configuração do pacote](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De[crypt]**: Define a senha de descriptografia do seu pacote configurada com o nível de proteção **EncryptAllWithPassword**/**EncryptSensitiveSensitiveWithPassword.**

> [!NOTE]
> Invocar o AzureDTExec com novos valores para suas opções gera um novo pipeline, exceto pela opção **/De[cript]**.

## <a name="next-steps"></a>Próximas etapas

Depois que os pipelines exclusivos com a atividade execute ssis package neles são gerados e executados quando você invoca o AzureDTExec, eles podem ser monitorados no portal Da Fábrica de Dados. Você também pode atribuir gatilhos de Fábrica de Dados a eles se quiser orquestre/agendar usando a Fábrica de Dados. Para obter mais informações, consulte [executar pacotes SSIS como atividades da Fábrica de Dados](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> Espera-se que o pipeline gerado seja usado apenas pelo AzureDTExec. Suas propriedades ou parâmetros podem mudar no futuro, por isso não modificá-los ou reutilizá-los para quaisquer outros fins. Modificações podem quebrar o AzureDTExec. Se isso acontecer, exclua o oleoduto. O AzureDTExec gera um novo gasoduto na próxima vez que for invocado.