---
title: Criar um Integration Runtime do Azure-SSIS no Azure Data Factory
description: Aprenda a criar um runtime de integração do Azure-SSIS no Azure Data Factory para poder implantar e executar pacotes do SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 1c3f58d42b6f311e4e238dcffe7da42afd8a5306
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416724"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um Integration Runtime do Azure-SSIS no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece etapas para o provisionamento de um Tempo de execução de Integração de Servidores Azure-SQL (SSIS) na Fábrica de Dados Azure. Um Azure-SSIS IR dá suporte a:

- Execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedados por um servidor do Banco de Dados SQL do Azure ou uma instância gerenciada (modelo de implantação de projeto).
- Execução de pacotes implantados em sistemas de arquivos, compartilhamentos de arquivos ou Arquivos do Azure (modelo de implantação de pacote). 

Depois que um Azure-SSIS IR for provisionado, você poderá usar ferramentas familiares para implantar e executar seus pacotes no Azure. Essas ferramentas incluem o SSDT (SQL Server Data Tools), o SSMS (SQL Server Management Studio) e ferramentas de linha de comando como `dtinstall`, `dtutil` e `dtexec`.

O tutorial [do Provisioning Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) mostra como criar um IR Azure-SSIS através do portal Azure ou do aplicativo Data Factory. O tutorial também mostra como usar opcionalmente um servidor de banco de dados SQL do Azure ou instância gerenciada para hospedar o SSISDB. Este artigo expande no tutorial e descreve como fazer essas tarefas opcionais:

- Use um servidor de banco de dados SQL do Azure com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerenciada com ponto final privado para hospedar o SSISDB. Como pré-requisito, você precisa configurar permissões e configurações de rede virtuais para que seu IR Azure-SSIS se junte a uma rede virtual.

- Use a autenticação do Azure Active Directory (Azure AD) com a identidade gerenciada para sua fábrica de dados para se conectar a um servidor de banco de dados SQL do Azure ou instância gerenciada. Como pré-requisito, você precisa adicionar a identidade gerenciada para sua fábrica de dados como um usuário de banco de dados que pode criar uma instância SSISDB.

- Junte-se ao seu IR Azure-SSIS para uma rede virtual ou configure um IR auto-hospedado como proxy para o seu IR Azure-SSIS para acessar dados no local.

Este artigo mostra como prover um IR Azure-SSIS usando o portal Azure, o Azure PowerShell e um modelo do Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura do Azure**. Se você ainda não tiver uma assinatura, você pode criar uma conta [de avaliação gratuita.](https://azure.microsoft.com/pricing/free-trial/)

- **Servidor de banco de dados Azure SQL ou instância gerenciada (opcional)**. Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. O Data Factory, por sua vez, criará uma instância do SSISDB neste servidor de banco de dados. 

  É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução do SSISDB sem cruzar regiões do Azure.

  Tenha estes pontos em mente:

  - Com base no servidor de banco de dados selecionado, a instância do SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientação na escolha do tipo de servidor de banco de dados para hospedar o SSISDB, consulte o [Compare um banco de dados único do Banco de Dados SQL do Azure, o pool elástico e a](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) seção de instância gerenciada neste artigo. 
  
    Se você usar um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar o IR auto-hospedado, precisará associar o Azure-SSIS IR a uma rede virtual. Para obter mais informações, consulte [Juntar-se a um IR Azure-SSIS em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Confirme se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor de banco de dados. Essa configuração não se aplica quando você usa um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, veja [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md).

  - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador de servidor ou usando a autenticação do Azure AD com a identidade gerenciada para seu data factory. Para o último, você precisa adicionar a identidade gerenciada do data factory a um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, consulte [Ativar a autenticação Azure AD para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Verifique se seu servidor de banco de dados ainda não tem uma instância do SSISDB. O provisionamento do Azure-SSIS IR não dá suporte ao uso de uma instância existente do SSISDB.

- **Rede virtual do Azure Resource Manager (opcional)**. Você precisa ter uma rede virtual do Azure Resource Manager se pelo menos uma das seguintes condições for verdadeira:
  - Você está hospedando o SSISDB em um servidor de banco de dados SQL do Azure com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerenciada com ponto final privado.
  - Você deseja se conectar a armazenamentos de dados locais a partir de pacotes SSIS em execução em seu IR Azure-SSIS sem configurar um IR auto-hospedado.

- **Azure PowerShell (opcional)**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps), caso queira executar um script do PowerShell para provisionar o seu Azure-SSIS IR.

### <a name="regional-support"></a>Suporte regional

Para obter uma lista de regiões do Azure em que a Fábrica de Dados e um IR Azure-SSIS estão disponíveis, consulte a disponibilidade de Fábrica de [Dados e Ir SSIS por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Comparação de um banco de dados único do Banco de Dados SQL, pool elástico e instância gerenciada

A tabela a seguir compara certos recursos de um servidor de banco de dados SQL do Azure e instância gerenciada à medida que se relacionam com o IR Azure-SSIR:

| Recurso | Banco de dados único/piscina elástica| Instância gerenciada |
|---------|--------------|------------------|
| **Agendamento** | O Agente de Servidor SQL não está disponível.<br/><br/>Consulte [Agendar uma execução de pacote em um pipeline de Fábrica de Dados](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| O Agente de Instância Gerenciada está disponível. |
| **Autenticação** | Você pode criar uma instância SSISDB com um usuário de banco de dados contido que represente qualquer grupo AD do Azure com a identidade gerenciada de sua fábrica de dados como membro na **função db_owner.**<br/><br/>Consulte [Ativar a autenticação Azure AD para criar uma instância SSISDB em um servidor de banco de dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Você pode criar uma instância SSISDB com um usuário de banco de dados contido que representa a identidade gerenciada de sua fábrica de dados. <br/><br/>Consulte [Ativar a autenticação ad do Azure para criar uma instância SSISDB em uma instância gerenciada do Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Camada de serviço** | Quando você cria um IR Azure-SSIS com seu servidor de banco de dados SQL Do Azure, você pode selecionar o nível de serviço para SSISDB. Há várias camadas de serviço. | Quando você cria um IR Azure-SSIS com sua instância gerenciada, você não pode selecionar o nível de serviço para SSISDB. Todos os bancos de dados em sua instância gerenciada compartilham o mesmo recurso alocado para essa instância. |
| **Rede virtual** | O IR do Azure-SSIS pode se juntar a uma rede virtual do Azure Resource Manager se você usar um servidor de banco de dados SQL Do Azure com regras de firewall IP/pontos finais de serviço de rede virtual. | O IR do Azure-SSIS pode se juntar a uma rede virtual do Azure Resource Manager se você usar uma instância gerenciada com ponto final privado. A rede virtual é necessária quando você não habilita um ponto final público para sua instância gerenciada.<br/><br/>Se você juntar o seu IR Azure-SSIS à mesma rede virtual que a sua instância gerenciada, certifique-se de que seu IR Azure-SSIS esteja em uma sub-rede diferente da instância gerenciada. Se você juntar seu IR Azure-SSIS a uma rede virtual diferente da sua instância gerenciada, recomendamos uma conexão de rede virtual ou uma conexão rede-rede. Consulte [Conecte seu aplicativo a uma instância gerenciada pelo Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transações distribuídas** | Esse recurso é suportado através de transações elásticas. As transações do Microsoft Distributed Transaction Coordinator (MSDTC) não possuem suporte. Se os pacotes SSIS usarem o MSDTC para coordenar transações distribuídas, considere migrar para transações elásticas para o Banco de Dados SQL do Azure. Para obter mais informações, consulte [Transações distribuídas em bancos de dados em nuvem](../sql-database/sql-database-elastic-transactions-overview.md). | Sem suporte. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Use o portal Azure para criar um tempo de execução de integração

Nesta seção, você usa o portal Azure, especificamente a interface de usuário da Fábrica de Dados (UI) ou aplicativo, para criar um IR Azure-SSIS.

### <a name="create-a-data-factory"></a>Criar uma data factory

Para criar seu data factory por meio do portal do Azure, siga as instruções passo a passo em [Criar um data factory por meio da interface do usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Ao fazer isso, selecione **Fixar no painel** para permitir acesso rápido após a criação do data factory. 

Depois que o data factory for criado, abra a respectiva página de visão geral na portal do Azure. Selecione o **bloco Autor & Monitor** para abrir sua página Vamos **começar** em uma guia separada. Lá, você pode continuar a criar o seu Ir Azure-SSIS.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um runtime de integração do Azure-SSIS

1. Na página de **Introdução**, selecione o bloco **Configurar o Integration Runtime do SSIS**.

   ![Configurar o bloco do Integration Runtime SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Na seção **Configurações Gerais** do painel de **Instalação do Integration Runtime**, conclua as etapas a seguir.

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Para o **Nome**, insira o nome do seu runtime de integração.

   1. Para ** Descrição **, insira a descrição do seu runtime de integração.

   1. Para ** Local **, selecione o local do seu runtime de integração. Somente os locais com suporte são exibidos. Recomendamos que você selecione o mesmo local do seu servidor de banco de dados para hospedar o SSISDB.

   1. Para **Tamanho do nó,** selecione o tamanho do nó no cluster de tempo de execução de integração. Apenas tamanhos de nós suportados são exibidos. Selecione um tamanho de nó grande (escalonamento vertical) se você quiser executar muitos pacotes com uso intensivo de computação ou de memória.

   1. Para ** Número do Nó **, selecione o número de nós em seu cluster de runtime de integração. Somente os números de nós com suporte são exibidos. Selecione um cluster grande com muitos nós (escalonamento horizontal) se você deseja executar muitos pacotes em paralelo.

   1. Para **Edição/Licença,** selecione a edição do SQL Server para o tempo de execução da integração: Padrão ou Empresa. Selecione Enterprise se você quiser usar os recursos avançados/premium em seu runtime de integração.

   1. Para **economizar dinheiro,** selecione a opção Azure Hybrid Benefit para o tempo de execução da integração: **Sim** ou **Não**. Selecione **Sim** se você quiser trazer sua própria licença do SQL Server com garantia de software para se beneficiar da economia de custos com o uso híbrido.

   1. Selecione **Avançar**.

1. Na seção **Configurações de SQL**, execute as seguintes etapas.

   ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Selecione a caixa de seleção **Criar catálogo do SSIS (SSISDB) hospedado pelo servidor do Banco de Dados SQL do Azure/Instância Gerenciada para armazenar seus projetos/pacotes/ambientes/logs de execução** para escolher o modelo de implantação para que os pacotes sejam executados no Azure-SSIS IR. Você escolherá entre o Modelo de Implantação de Projeto (em que os pacotes são implantados no SSISDB hospedado pelo servidor de banco de dados) e o Modelo de Implantação de Pacotes (em que os pacotes são implantados em sistemas de arquivos, compartilhamentos de arquivos ou Arquivos do Azure). 
    
      Se você selecionar a caixa de seleção, você precisará trazer seu próprio servidor de banco de dados para hospedar a instância SSISDB que criaremos e gerenciaremos em seu nome.
   
      1. Para **Assinatura**, selecione a assinatura do Azure que tem o servidor de banco de dados para hospedar o SSISDB. 

      1. Para **Local**, selecione o local do seu servidor de banco de dados para hospedar o SSISDB. É recomendável que você selecione o mesmo local do seu runtime de integração. 

      1. Para **Ponto de extremidade de servidor de banco de dados de catálogo**, selecione o ponto de extremidade do seu servidor de banco de dados para hospedar o SSISDB. 
    
         Com base no servidor de banco de dados selecionado, a instância do SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientação na escolha do tipo de servidor de banco de dados para hospedar o SSISDB, consulte o [Compare um banco de dados único do Banco de Dados SQL do Azure, o pool elástico e a](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) seção de instância gerenciada neste artigo. 
    
         Se você selecionar um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar o IR auto-hospedado, precisará associar o Azure-SSIS IR a uma rede virtual. Para obter mais informações, consulte [Juntar-se a um IR Azure-SSIS em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      1. Selecione a caixa de seleção **Usar autenticação do AAD com a identidade gerenciada para o seu Azure Data Factory** para escolher o método de autenticação para o servidor de banco de dados para hospedar o SSISDB. Você escolherá a autenticação do SQL ou a autenticação do Azure AD com a identidade gerenciada para seu data factory. 
    
         Se selecionar a caixa de seleção, você precisará adicionar a identidade gerenciada do data factory em um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, consulte [Ativar a autenticação Azure AD para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. Para **Nome de Usuário do Administrador**, insira o nome de usuário de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

      1. Para **Senha do Administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

      1. Para **o Catalog Database Service Tier**, selecione o nível de serviço do servidor de banco de dados para hospedar o SSISDB. Selecione a camada Básica, Standard ou Premium ou selecione um nome de pool elástico. 

      1. Selecione **Conexão de teste**. Se o teste for bem-sucedido, selecione **Avançar**. 

1. Na seção **Configurações Avançadas**, conclua as etapas a seguir.

   ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **Execuções Paralelas Máximas por Nó**, selecione o número máximo de pacotes para executar simultaneamente por nó no cluster do runtime de integração. Somente os números de pacotes com suporte são exibidos. Selecione um número baixo, se você quiser usar mais de um núcleo para executar um único pacote grande que tenha uso intensivo de computação ou de memória. Selecione um número alto, se você quiser executar um ou mais pacotes pequenos em um único núcleo.

   1. Marque a caixa de seleção **Personalizar seu Azure-SSIS Integration Runtime com configurações de sistema adicionais/instalações de componentes** para escolher se deseja adicionar as configurações personalizadas padrão/expressas no Azure-SSIS IR. Para obter mais informações, confira [Configuração personalizada para um Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Se você selecionar a caixa de seleção, complete as seguintes etapas.

      ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Para **o contêiner de configuração personalizado SAS URI**, insira o Uri SAS do seu contêiner onde você armazena scripts e arquivos associados para configurações personalizadas padrão.

      1. Para **configuração personalizada expressa,** selecione **Novo** para abrir o painel **de configuração personalizado Adicionar expresso** e, em seguida, selecione quaisquer tipos sob o menu suspenso **tipo de configuração personalizada Express,** por exemplo, **executar o comando cmdkey,** **adicionar variável de ambiente,** **instalar componente licenciado,** etc.

         Se você selecionar o tipo **de componente licenciado Instalar,** você poderá selecionar quaisquer componentes integrados de nossos parceiros ISV no menu suspenso **nome componente** e, se necessário, digite a chave de licença do produto que você comprou deles no campo chave **de licença.**
  
         Suas configurações personalizadas expressas adicionadas serão exibidas na seção **Configurações Avançadas.** Para removê-las, você pode selecionar suas caixas de seleção e, em seguida, selecionar **Excluir**.

   1. Selecione o **Select a VNet para o tempo de execução de integração Do Azure-SSIS para participar, permita que o ADF crie certos recursos de rede e, opcionalmente, traga sua própria caixa de seleção de endereços IP públicos estáticos** para escolher se deseja participar do tempo de execução da integração em uma rede virtual. 

      Selecione-o se você usar um servidor de banco de dados SQL do Azure com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerenciada com ponto final privado para hospedar ssisdb, ou se você precisar de acesso a dados locais (ou seja, você tem fontes ou destinos de dados no local em seus pacotes SSIS) sem configurar um IR auto-hospedado. Para obter mais informações, consulte [O Ir do Azure-SSIS em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Se você selecionar a caixa de seleção, complete as seguintes etapas.

      ![Configurações avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. Para ** Assinatura **, selecione a assinatura do Azure que possui sua rede virtual.

      1. Para ** Local **, o mesmo local do seu runtime de integração é selecionado.

      1. Para **Tipo,** selecione o tipo de sua rede virtual: classic ou Azure Resource Manager. Recomendamos que você selecione uma rede virtual do Azure Resource Manager, pois as redes virtuais clássicas serão depreciadas em breve.

      1. Para ** Nome da VNet **, selecione o nome da sua rede virtual. Ele deve ser o mesmo usado para o servidor De banco de dados SQL do Azure com pontos finais de serviço de rede virtual ou instância gerenciada com ponto final privado para hospedar o SSISDB. Ou deve ser o mesmo conectado à sua rede local. Caso contrário, pode ser qualquer rede virtual para trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR.

      1. Para ** Nome da sub-rede **, selecione o nome da sub-rede da sua rede virtual. Ele deve ser o mesmo usado para o servidor De banco de dados SQL do Azure com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para sua instância gerenciada com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR.

      1. Selecione os endereços IP públicos estáticos para a caixa de seleção **de tempo de execução de integração Azure-SSIS** para escolher se você deseja trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR, para que você possa permitir no firewall para suas fontes de dados.

         Se você selecionar a caixa de seleção, complete as seguintes etapas.

         1. Para **primeiro endereço IP público estático,** selecione o primeiro endereço IP público estático que atenda aos requisitos do seu IR Azure-SSIS. Se você não tiver nenhum, clique em **Criar novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que você possa selecioná-los.
      
         1. Para **segundo endereço IP público estático,** selecione o segundo endereço IP público estático que atende aos requisitos do seu IR Azure-SSIS. Se você não tiver nenhum, clique em **Criar novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que você possa selecioná-los.

   1. Marque a caixa de seleção **Configurar o runtime de integração auto-hospedada como um proxy para o Azure-SSIS Integration Runtime** para escolher se você deseja ou não configurar um IR auto-hospedado como proxy para o seu Azure-SSIS IR. Para obter mais informações, veja [Configurar um IR auto-hospedado como proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Se você selecionar a caixa de seleção, complete as seguintes etapas.

      ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Para **executar o runtime de integração auto-hospedado, selecione**seu IR auto-hospedado existente como um proxy para O IR do Azure-SSIS.

      1. Para **o Staging Storage Linked Service**, selecione o serviço vinculado ao armazenamento Azure Blob existente ou crie um novo para estágio.

      1. Para **Caminho de estadiamento,** especifique um recipiente blob na conta de armazenamento Azure Blob selecionada ou deixe-a vazia para usar uma padrão para a encenação.

   1. Selecione **a validação** > VNet**Continue**. 

1. Na seção **Resumo**, examine todas as configurações de provisionamento, marque os links de documentação recomendados e selecione **Concluir** para iniciar a criação do runtime de integração.

   > [!NOTE]
   > Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos. Mas pode levar de 20 a 30 minutos para o Azure-SSIS IR se juntar a uma rede virtual.
   >
   > Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. Ele também configura permissões e configurações para sua rede virtual, se especificado, e se junta ao seu IR Azure-SSIS à rede virtual.
   > 
   > Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter informações sobre outros componentes que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na guia **Conexões**, alterne para **Runtimes de integração**, se necessário. Selecione **Atualizar** para atualizar o status.

   ![Status de criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Use os links na coluna **Ações** para parar/iniciar, editar ou excluir o runtime de integração. Use o último link para exibir o código JSON para o runtime de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido.

   ![Ações do Azure SSIS IR](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>runtimes de integração do Azure SSIS no portal

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar** e selecione **Conexões**. Em seguida, alterne para a guia **Integration Runtimes** para exibir os runtimes de integração existentes em seu data factory.

   ![Exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um novo IR Azure-SSIS.

   ![runtime de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel **Instalação do Integration Runtime**, selecione o bloco **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, selecione **Avançar**.

   ![Especificar o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para obter as etapas restantes para configurar um IR Azure-SSIS, consulte a disposição de uma seção [de tempo de execução de integração Do Azure SSIS.](#provision-an-azure-ssis-integration-runtime)

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Use o Azure PowerShell para criar um tempo de execução de integração

Nesta seção, você usa o Azure PowerShell para criar um IR Azure-SSIS.

### <a name="create-variables"></a>Criar variáveis

Copie e cole o seguinte script. Especifique valores para as variáveis. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Faça login e selecione uma assinatura

Adicione o seguinte script para fazer login e selecione sua assinatura do Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Validar a conexão ao servidor de banco de dados

Adicione o script a seguir para validar o servidor de banco de dados SQL do Azure ou a instância gerenciada.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Configurar a rede virtual

Adicione o script a seguir para configurar automaticamente permissões e configurações de rede virtuais para que o tempo de execução de integração Azure-SSIS seja adicionado.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo.

Se seu grupo de recursos já existir, não copie este código ao seu script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Criar uma data factory

Execute o comando a seguir para criar um data factory.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Criar um Integration Runtime

Execute os seguintes comandos para criar um runtime de integração do Azure-SSIS que executa pacotes do SSIS no Azure.

Se você não usar o SSISDB, você `CatalogServerEndpoint` `CatalogPricingTier`pode `CatalogAdminCredential` omiti-lo nos parâmetros e parâmetros.

Se você não usar um servidor de banco de dados SQL do Azure com regras de firewall IP/pontos finais de serviço de rede virtual ou uma `VNetId` `Subnet` instância gerenciada com ponto final privado para hospedar ssisdb, ou exigir acesso a dados locais, você pode omitir os e parâmetros ou passar valores vazios para eles. Você também pode omiti-los se configurar um IR auto-hospedado como proxy para o seu Azure-SSIS IR para acessar dados no local. Caso contrário, você não pode omiti-los e deve passar valores válidos da configuração da sua rede virtual. Para obter mais informações, consulte [Juntar-se a um IR Azure-SSIS em uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Se você usar instância gerenciada para hospedar o SSISDB, você pode omitir o `CatalogPricingTier` parâmetro ou passar um valor vazio para ele. Caso contrário, você não pode omiti-lo e deve passar um valor válido da lista de níveis de preços suportados para o Banco de Dados SQL do Azure. Para obter mais informações, consulte [os limites de recursos do SQL Database](../sql-database/sql-database-resource-limits.md).

Se você usar a autenticação Azure AD com a identidade gerenciada para que sua `CatalogAdminCredential` fábrica de dados se conecte ao servidor de banco de dados, você pode omitir o parâmetro. Mas você deve adicionar a identidade gerenciada para sua fábrica de dados em um grupo AD do Azure com permissões de acesso ao servidor de banco de dados. Para obter mais informações, consulte [Ativar a autenticação Azure AD para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Caso contrário, você não pode omiti-lo e deve passar um objeto válido formado a partir do nome de usuário do servidor e senha para autenticação SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

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

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Inicie o tempo de execução da integração

Execute o comando a seguir para iniciar o Integration Runtime do Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos. Mas pode levar de 20 a 30 minutos para o Azure-SSIS IR se juntar a uma rede virtual.
>
> Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. Ele também configura permissões e configurações para sua rede virtual, se especificado, e se junta ao seu IR Azure-SSIS à rede virtual.
> 
> Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter informações sobre outros componentes que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Aqui está o script completo que cria um tempo de execução de integração Azure-SSIS.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

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

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Use um modelo do Azure Resource Manager para criar um tempo de execução de integração

Nesta seção, você usa um modelo do Azure Resource Manager para criar o tempo de execução da integração Azure-SSIS. Aqui está um passo a passo amostral:

1. Crie um arquivo JSON com o seguinte modelo do Azure Resource Manager. Substitua os valores nos suportes angulares (espaços reservados) por seus próprios valores.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Para implantar o modelo do Azure Resource Manager, execute o `New-AzResourceGroupDeployment` comando como mostrado no exemplo a seguir. No exemplo, `ADFTutorialResourceGroup` está o nome do seu grupo de recursos. `ADFTutorialARM.json`é o arquivo que contém a definição JSON para sua fábrica de dados e o IR Azure-SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Este comando cria sua fábrica de dados e O IR do Azure-SSIS nele, mas não inicia o IR.

3. Para iniciar o seu Azure-SSIS IR, execute o `Start-AzDataFactoryV2IntegrationRuntime` comando:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos. Mas pode levar de 20 a 30 minutos para o Azure-SSIS IR se juntar a uma rede virtual.
>
> Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. Ele também configura permissões e configurações para sua rede virtual, se especificado, e se junta ao seu IR Azure-SSIS à rede virtual.
> 
> Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter informações sobre outros componentes que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS

Se você usar o SSISDB, poderá implantar seus pacotes nele e executá-los em seu IR Azure-SSIS usando ferramentas SSDT (SSDT) ou SQL Server Management Studio (SSMS). Essas ferramentas se conectam ao servidor de banco de dados por meio do ponto de extremidade do servidor: 

- Para um servidor do Banco de Dados SQL do Azure, o formato de ponto de extremidade do servidor é `<server name>.database.windows.net`.
- Para uma instância gerenciada com ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.<dns prefix>.database.windows.net`.
- Para uma instância gerenciada com ponto de extremidade público, o formato de ponto de extremidade do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se você não usar o SSISDB, você pode implantar seus pacotes em sistemas de arquivos, compartilhamentos de arquivos ou `dtinstall`arquivos `dtutil`Azure e executá-los em seu Azure-SSIS IR usando as ferramentas de linha de comando e , e. `dtexec` Para saber mais, confira [Implantar pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Nos dois casos, você também pode executar os pacotes implantados no Azure-SSIS IR usando a atividade Executar Pacote SSIS em pipelines do Data Factory. Para obter mais informações, confira [Invocar execução do pacote SSIS como uma atividade de Data Factory de primeira classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Próximas etapas

Veja outros tópicos do Azure-SSIS IR nesta documentação:

- [Tempo de execução da integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações sobre tempos de execução de integração em geral, incluindo o Azure-SSIS IR.
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar e entender informações sobre o seu IR Azure-SSIS.
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou excluir seu IR Azure-SSIS. Ele também mostra como dimensionar seu IR Azure-SSIS adicionando mais nós.
- [Implantar, executar e monitorar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Agendar a execução de pacotes no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)