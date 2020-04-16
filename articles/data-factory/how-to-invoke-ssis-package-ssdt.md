---
title: Executar pacotes SSIS do SSDT
description: Saiba como executar pacotes SSIS no Azure a partir do SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399438"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Executar pacotes SSIS no Azure do SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve o recurso dos projetos SSIS (SSIS) do SQL Server Integration Services (SSIS) habilitados para Azure no SQL Server Data Tools (SSDT), que permite executar pacotes no Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF).  Você pode usar esse recurso para testar seus pacotes SSIS existentes antes de levantar & shift/migrate-los para o Azure ou para desenvolver novos pacotes SSIS para serem executados no Azure.

Com esse recurso, você pode criar um novo IR Azure-SSIS ou anexar um já existente a projetos SSIS e, em seguida, executar seus pacotes nele.  Apoiamos a execução de pacotes a serem implantados no catálogo SSIS (SSISDB) no Project Deployment Model e aqueles a serem implantados em sistemas de arquivos/compartilhamentos de arquivos/Arquivos Azure no Modelo de Implantação de Pacotes. 

## <a name="prerequisites"></a>Pré-requisitos
Para usar este recurso, baixe e instale a mais recente extensão ssdt com projetos SSIS para Visual Studio [daqui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ou como instalador autônomo a partir [daqui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Projetos SSIS habilitados para azure
No SSDT, você pode criar novos projetos SSIS habilitados para Azure usando o modelo **De Serviços de Integração (Habilitado para Azure).**

![Novo projeto SSIS habilitado para Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Alternativamente, você pode habilitar o Azure para habilitar seus projetos SSIS existentes clicando com o botão direito do mouse no nó do projeto no painel Solution Explorer do SSDT para aparecer um menu e, em seguida, selecionar o item de menu **habilitado para O Zure.**

![Projeto SSIS existente do Azure-Enable](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Habilitar o Azure para seus projetos SSIS existentes exige que você defina a versão do servidor-alvo para ser a mais recente suportada pelo Azure-SSIS IR, que atualmente é **o SQL Server 2017**, portanto, se você ainda não fez isso, uma janela de diálogo aparecerá para fazer isso.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Conecte projetos habilitados para Azure ao SSIS na fábrica de dados do Azure
Ao conectar seus projetos habilitados para Azure ao SSIS no ADF, você pode carregar seus pacotes em Arquivos Azure e executá-los no Azure-SSIS IR.  Para isso, siga estas etapas:

1. Clique com o botão direito do mouse no projeto ou no nó **Linked Azure Resources** no painel SSDT do Solution Explorer para aparecer em um menu e selecionar o item **de menu Conectar ao SSIS no menu Azure Data Factory** para iniciar o **SSIS no ADF Connection Wizard**.

   ![Conecte-se ao SSIS no ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Na página **SSIS in ADF Introduction,** revise a introdução e clique no botão **Seguinte** para continuar.

   ![SSIS em Introdução ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Na página **Selecionar SSIS IR no ADF,** selecione o ADF e o Azure-SSIS IR existentes para executar pacotes ou criar novos se você não tiver nenhum.
   - Para selecionar o IR Azure-SSIS existente, selecione primeiro a assinatura azure e o ADF relevantes.
   - Se você selecionar o Seu ADF existente que não possui nenhum IR Azure-SSIS, clique no botão **Criar IR SSIS** para criar um novo no portal/aplicativo da ADF.
   - Se você selecionar sua assinatura Azure existente que não possui nenhum ADF, clique no botão **Criar Ir SSIS** para iniciar o **Assistente de Criação de Tempo de Execução de Integração**, onde você pode inserir o local e o prefixo para criarmos automaticamente um novo Azure Resource Group, Data Factory e SSIS IR em seu nome, nomeado no seguinte padrão: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Selecione SSIS IR no ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Na página **Selecionar armazenamento do Azure,** selecione sua conta de armazenamento Azure existente para carregar pacotes em Arquivos Azure ou criar um novo se você não tiver nenhum.
   - Para selecionar sua conta azure Storage existente, selecione primeiro a assinatura azure relevante.
   - Se você selecionar a mesma assinatura do Azure que o Azure-SSIS IR que não possui nenhuma conta do Azure Storage, clique no botão **Criar o Armazenamento Do Zure** para criar mos automaticamente um novo em seu nome no mesmo local que o seu IR Azure-SSIS, nomeado pela combinação de um prefixo do seu nome IR Azure-SSIS e sua data de criação.
   - Se você selecionar uma assinatura diferente do Azure que não tenha nenhuma conta do Azure Storage, clique no botão **Criar armazenamento do Azure** para criar uma nova no portal Azure.
   
   ![Selecione Armazenamento do Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Clique no botão **Conectar** para completar sua conexão.  Exibiremos sua conta selecionada do Azure-SSIS IR e do Azure Storage sob o nó **Linked Azure Resources** no painel Solution Explorer do SSDT.  Também atualizaremos o status do seu IR Azure-SSIS, enquanto você pode gerenciá-lo clicando com o botão direito do mouse em seu nó para aparecer um menu e, em seguida, selecionando o item de menu **Start\Stop\Manage** que leva você ao portal/aplicativo aDF para fazê-lo.

## <a name="execute-ssis-packages-in-azure"></a>Executar pacotes SSIS no Azure
### <a name="starting-package-executions"></a>Execuções de pacotes iniciais
Depois de conectar seus projetos ao SSIS no ADF, você pode executar pacotes no Azure-SSIS IR.  Você tem duas opções para iniciar execuções de pacotes:
-  Clique no botão **Iniciar** na barra de ferramentas SSDT para baixar um menu e selecionar o item Executar no menu **Azure** 

   ![Executar no Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Clique com o botão direito do mouse no nó do pacote no painel Do Explorador de Solução do SSDT para aparecer em um menu e selecionar o item execute package no menu **Azure.**

   ![Executar pacote no Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> A execução de seus pacotes no Azure requer que você tenha um IR Azure-SSIS em execução, portanto, se o seu IR Azure-SSIS for interrompido, uma janela de diálogo aparecerá para iniciá-lo.  Excluindo qualquer tempo de configuração personalizado, esse processo deve ser concluído dentro de 5 minutos, mas pode levar aproximadamente 20 a 30 minutos para o Azure-SSIS IR aderir a uma rede virtual.  Depois de executar seus pacotes no Azure, você pode parar seu IR Azure-SSIS para gerenciar seu custo de execução clicando com o botão direito do mouse em seu nó no painel SSDT solution Explorer para aparecer um menu e, em seguida, selecionando o item de menu **Start\Stop\Manage** que leva você ao portal/aplicativo adf para fazê-lo.

### <a name="checking-package-execution-logs"></a>Verificando registros de execução de pacotes
Quando você iniciar a execução do pacote, formamos e exibiremos seu login na janela Progresso do SSDT.  Para um pacote de longa duração, atualizaremos periodicamente seu registro por minutos.  Você pode interromper a execução do pacote clicando no botão **Parar** na barra de ferramentas SSDT que irá cancelá-la imediatamente.  Você também pode encontrar temporariamente os dados brutos de log `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`em seu caminho da Convenção de Nomeação Universal (UNC): , mas vamos limpá-los depois de um dia.

### <a name="switching-package-protection-level"></a>Comutação do nível de proteção do pacote
A execução de pacotes SSIS no Azure não suporta **cripto-criptografiaComousuárioUserKey**/**EncryptAllWithUserKey** níveis de proteção.  Consequentemente, se seus pacotes estiverem configurados com eles, os alternaremos temporariamente em **EncryptSensitiveWithPasswordEncryptAllWithPassword**/**EncryptAllWithPassword**, respectivamente, com senhas geradas aleatoriamente quando carregamos seus pacotes em Arquivos Azure para execução em seu IR Azure-SSIS.

> [!NOTE]
> Se seus pacotes contiverem Execute Tarefas de pacote sitiantes que se referem a outros pacotes configurados com **Cripto-formaComUserKey**/**CriptografarOsNíveisOUsuárioComUserChave,** você precisa reconfigurar manualmente esses outros pacotes para usar **EncryptSensitiveWithPasswordEncryptAllWithPassword,**/**EncryptAllWithPassword**respectivamente, antes de executar seus pacotes.

Se seus pacotes já estiverem configurados com os níveis de proteção **EncryptSensitiveWithPassword**/**EncryptAllWithPassword,** manteremos-os inalterados, mas ainda usaremos senhas geradas aleatoriamente quando carregarmos seus pacotes em Arquivos Azure para execução em seu IR Azure-SSIS.

### <a name="using-package-configuration-file"></a>Usando o arquivo de configuração do pacote
Se você usar arquivos de configuração de pacote no Modelo de Implantação de Pacotes para alterar valores variáveis em tempo de execução, carregaremos automaticamente esses arquivos com seus pacotes em Arquivos Azure para execução em seu Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Usando a tarefa executar pacote
Se seus pacotes contiverem Tarefas de pacote execute que se referem a outros pacotes armazenados em sistemas de arquivos locais, você precisará executar as seguintes configurações adicionais:

1. Carregue os outros pacotes em Arquivos Azure sob a mesma conta do Azure Storage conectada aos seus projetos e obtenha seu novo caminho UNC, por exemplo.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Substitua o caminho de arquivo desses outros pacotes no Gerenciador de conexão de arquivos de Executar tarefas de pacotes pelo seu novo caminho UNC
   - Se a máquina que executa o SSDT não puder acessar o novo caminho UNC, você poderá alterar o caminho do arquivo no painel Propriedades do File Connection Manager
   - Alternativamente, você pode usar uma variável para o caminho do arquivo para atribuir o valor certo no tempo de execução

Se seus pacotes contiverem Execute Tarefas de pacote que se referem a outros pacotes no mesmo projeto, nenhuma configuração adicional será necessária.

## <a name="next-steps"></a>Próximas etapas
Uma vez satisfeito com a execução de seus pacotes no Azure a partir do SSDT, você pode implantá-los e executá-los como executar atividades do pacote SSIS em pipelines ADF, consulte [executar pacotes SSIS como executar atividades de pacote SSIS em pipelines ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
