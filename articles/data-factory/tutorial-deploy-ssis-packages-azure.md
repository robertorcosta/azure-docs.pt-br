---
title: Provisionar o Azure-SSIS Integration Runtime | Microsoft Docs
description: Saiba como provisionar o tempo de execução de integração do Azure SSIS no Azure Data Factory para que você possa implantar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 62b11fbf19c803d54c699d3a59454677ac3423f1
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326071"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Provisionar o Azure-SSIS Integration Runtime no Azure Data Factory

Este tutorial fornece etapas de como usar o portal do Azure para provisionar um Azure-SSIS (SQL Server Integration Services) IR (Integration Runtime) no Azure Data Factory. Um Azure-SSIS IR dá suporte a:

- Execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedados por um servidor do Banco de Dados SQL do Azure ou uma instância gerenciada (modelo de implantação de projeto).
- Execução de pacotes implantados em sistemas de arquivos, compartilhamentos de arquivos ou Arquivos do Azure (modelo de implantação de pacote). 

Depois que um Azure-SSIS IR for provisionado, você poderá usar ferramentas familiares para implantar e executar seus pacotes no Azure. Essas ferramentas incluem o SQL Server Data Tools, o SQL Server Management Studio e ferramentas de linha de comando como `dtinstall`, `dtutil` e `dtexec`.

Para obter informações conceituais sobre IRs do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, você completa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Provisionar um tempo de execução de integração do Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Servidor do banco de dados SQL do Azure (opcional)** . Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. O Data Factory, por sua vez, criará uma instância do SSISDB neste servidor de banco de dados. 

  É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução do SSISDB sem cruzar regiões do Azure.

  Tenha estes pontos em mente:

  - Com base no servidor de banco de dados selecionado, a instância do SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter diretrizes sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, confira [Comparar um banco de dados individual do Banco de Dados SQL do Azure, um pool elástico e uma instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Se você usar um servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar o IR auto-hospedado, precisará associar o Azure-SSIS IR a uma rede virtual. Para obter mais informações, confira [Criar o Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Confirme se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor de banco de dados. Essa configuração não se aplica quando você usa um servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidade privado para hospedar o SSISDB. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, veja [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md).
  - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador de servidor ou usando a autenticação do Azure AD com a identidade gerenciada para seu data factory. Para o último, você precisa adicionar a identidade gerenciada do data factory a um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, confira [Criar Azure-SSIS IR com a autenticação do Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Verifique se seu servidor de banco de dados ainda não tem uma instância do SSISDB. O provisionamento do Azure-SSIS IR não dá suporte ao uso de uma instância existente do SSISDB.


> [!NOTE]
> Para obter uma lista de regiões do Azure nas quais o Data Factory e um Azure-SSIS IR estão disponíveis, confira [Disponibilidade do Data Factory + SSIS IR por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Criar uma data factory

Para criar seu data factory por meio do portal do Azure, siga as instruções passo a passo em [Criar um data factory por meio da interface do usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Ao fazer isso, selecione **Fixar no painel** para permitir acesso rápido após a criação do data factory. 

Depois que o data factory for criado, abra a respectiva página de visão geral na portal do Azure. Selecione o bloco **Criar e Monitorar** para abrir a página de **Introdução** em uma guia separada. Nela você pode continuar a criar seu Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>Da visão geral do Data Factory

1. Na página de **Introdução**, selecione o bloco **Configurar o Integration Runtime do SSIS**. 

   ![Bloco "Configurar o bloco do Integration Runtime SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para conferir o restante das etapas de configuração de um IR do Azure-SSIS. 

### <a name="from-the-authoring-ui"></a>Da interface do usuário de criação

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar** e selecione **Conexões**. Em seguida, alterne para a guia **Integration Runtimes** para exibir os runtimes de integração existentes em seu data factory. 

   ![Seleções para exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um novo IR do Azure SSIS. 

   ![Tempo de execução de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Na janela **Instalação do Integration Runtime**, selecione **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, selecione **Avançar**. 

   ![Especificar o tipo de tempo de execução de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para conferir o restante das etapas de configuração de um IR do Azure-SSIS. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um tempo de execução de integração do Azure-SSIS

1. Na página **Configurações Gerais** da **Instalação do Integration Runtime**, conclua as etapas a seguir. 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Para **Name**, insira o nome do seu tempo de execução de integração. 

   b. Para **Descrição**, insira a descrição do seu tempo de execução de integração. 

   c. Para **Local**, selecione o local do seu tempo de execução de integração. Somente os locais com suporte são exibidos. Recomendamos que você selecione o mesmo local do seu servidor de banco de dados para hospedar o SSISDB. 

   d. Para **Tamanho do Nó**, selecione o tamanho do nó no cluster de tempo de execução de integração. Apenas tamanhos de nós suportados são exibidos. Selecione um tamanho de nó grande (escalonamento vertical) se você quiser executar muitos pacotes com uso intensivo de computação ou de memória. 

   e. Para **Número do Nó**, selecione o número de nós em seu cluster de tempo de execução de integração. Somente os números de nós com suporte são exibidos. Selecione um cluster grande com muitos nós (escalonamento horizontal) se você deseja executar muitos pacotes em paralelo. 

   f. Para **Edição/Licença**, selecione a edição do SQL Server para seu runtime de integração: Standard ou Enterprise. Selecione Enterprise se você quiser usar os recursos avançados/premium em seu runtime de integração. 

   g. Para **Economizar**, selecione a opção do Benefício Híbrido do Azure para o runtime de integração: **Sim** ou **Não**. Selecione **Sim** se você quiser colocar sua própria licença do SQL Server com o Software Assurance para aproveitar a redução de custos com o uso híbrido. 

   h. Selecione **Avançar**. 

1. Na página **Configurações de SQL**, execute as seguintes etapas. 

   ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Selecione a caixa de seleção **Criar catálogo SSIS...** para escolher o modelo de implantação dos pacotes a serem executados no Azure-SSIS IR. Você escolherá entre o Modelo de Implantação de Projeto (em que os pacotes são implantados no SSISDB hospedado pelo servidor de banco de dados) e o Modelo de Implantação de Pacotes (em que os pacotes são implantados em sistemas de arquivos, compartilhamentos de arquivos ou Arquivos do Azure).
   
   Se selecionar a caixa de seleção, você precisará trazer seu próprio servidor de banco de dados para hospedar o SSISDB que criaremos e gerenciaremos em seu nome.
   
   b. Para **Assinatura**, selecione a assinatura do Azure que tem o servidor de banco de dados para hospedar o SSISDB. 

   c. Para **Local**, selecione o local do seu servidor de banco de dados para hospedar o SSISDB. É recomendável que você selecione o mesmo local do seu tempo de execução de integração.

   d. Para **Ponto de extremidade de servidor de banco de dados de catálogo**, selecione o ponto de extremidade do seu servidor de banco de dados para hospedar o SSISDB. 
   
   Com base no servidor de banco de dados selecionado, a instância do SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter diretrizes sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, confira [Comparar um banco de dados individual do Banco de Dados SQL do Azure, um pool elástico e uma instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

   Se você selecionar um servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar o IR auto-hospedado, precisará associar o Azure-SSIS IR a uma rede virtual. Para obter mais informações, confira [Criar o Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

   e. Selecione a caixa de seleção **Usar autenticação do AAD com a identidade gerenciada para o seu Azure Data Factory** para escolher o método de autenticação para o servidor de banco de dados para hospedar o SSISDB. Você escolherá a autenticação do SQL ou a autenticação do Azure AD com a identidade gerenciada para seu data factory.

   Se selecionar a caixa de seleção, você precisará adicionar a identidade gerenciada do data factory em um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, confira [Criar Azure-SSIS IR com a autenticação do Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
   f. Para **Nome de Usuário do Administrador**, insira o nome de usuário de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   g. Para **Senha do Administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   h. Para **Camada de Serviço de Banco de Dados de Catálogo**, selecione a camada de serviço para o seu servidor de banco de dados na qual hospedar o SSISDB. Selecione a camada Básica, Standard ou Premium ou selecione um nome de pool elástico.

   i. Selecione **Testar Conexão**. Se o teste for bem-sucedido, selecione **Avançar**. 

1. Na página **Configurações Avançadas**, conclua as etapas a seguir. 

   ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Para **Execuções Paralelas Máximas por Nó**, selecione o número máximo de pacotes para executar simultaneamente por nó no cluster do runtime de integração. Somente os números de pacotes com suporte são exibidos. Selecione um número baixo, se você quiser usar mais de um núcleo para executar um único pacote grande que tenha uso intensivo de computação ou de memória. Selecione um número alto, se você quiser executar um ou mais pacotes pequenos em um único núcleo. 

   b. Para o **URI SAS de Contêiner de Configuração Personalizada**, insira opcionalmente o URI (Uniform Resource Identifier) de SAS (Assinatura de Acesso Compartilhado) do seu contêiner de Armazenamento de Blobs do Azure em que seu script de instalação e os arquivos associados estão armazenados. Para obter mais informações, confira [Configuração personalizada para um Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   c. Marque a caixa de seleção **Selecionar uma VNet para seu Azure-SSIS Integration Runtime para ingressar e permitir que o Data Factory crie determinados recursos de rede** para escolher se deseja ingressar o seu runtime de integração em uma rede virtual.

   Selecione se você requer acesso a dados locais sem configurar um IR auto-hospedado ou então se você usa um servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidade privado para hospedar o SSISDB. Para obter mais informações, confira [Criar o Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   d. Selecione a caixa de seleção **Configurar o runtime de integração auto-hospedada como um proxy para o Azure-SSIS Integration Runtime** para escolher se você deseja ou não configurar um IR auto-hospedado como um proxy para o seu Azure-SSIS IR. Para obter mais informações, confira [Configurar um IR auto-hospedado como um proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

1. Selecione **Avançar**. 

1. Na página **Resumo**, examine todas as configurações de provisionamento, marque os links de documentação recomendados e selecione **Concluir** para iniciar a criação do runtime de integração. 

   > [!NOTE]
   > Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos.
   >
   > Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. 
   > 
   > Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter informações sobre outros componentes que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na guia **Conexões**, alterne para **Tempos de Execução de Integração**, se necessário. Selecione **Atualizar** para atualizar o status. 

   ![Status de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Use os links na coluna **Ações** para parar/iniciar, editar ou excluir o tempo de execução de integração. Use o último link para exibir o código JSON para o tempo de execução de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido. 

   ![Links na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS

Se usar o SSISDB, você poderá implantar seus pacotes nele e executá-los no Azure-SSIS IR usando as SQL Server Data Tools ou as ferramentas do SQL Server Management Studio. Essas ferramentas se conectam ao servidor de banco de dados por meio do ponto de extremidade do servidor: 

- Para um servidor do Banco de Dados SQL do Azure com um ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.database.windows.net`.
- Para uma instância gerenciada com um ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.<dns prefix>.database.windows.net`.
- Para uma instância gerenciada com um ponto de extremidade público, o formato de ponto de extremidade do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se não usar o SSISDB, você poderá implantar seus pacotes em sistemas de arquivos, compartilhamentos de arquivos ou Arquivos do Azure. Em seguida, você poderá executá-los no Azure-SSIS IR usando as ferramentas de linha de comando `dtinstall`, `dtutil` e `dtexec`. Para saber mais, confira [Implantar pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Nos dois casos, você também pode executar seus pacotes implantados no Azure-SSIS IR usando a atividade Executar Pacote SSIS em pipelines do Data Factory. Para obter mais informações, confira [Invocar execução do pacote SSIS como uma atividade de Data Factory de primeira classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Confira também a documentação do SSIS a seguir: 

- [Implantar, executar e monitorar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar a execução de pacotes no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como personalizar o tempo de execução de integração do Azure-SSIS, avance para o artigo a seguir: 

> [!div class="nextstepaction"]
> [Personalizar um Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)