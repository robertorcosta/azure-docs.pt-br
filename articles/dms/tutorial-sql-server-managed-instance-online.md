---
title: 'Tutorial: Migre o SQL Server on-line para uma instância gerenciada pelo SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como realizar uma migração online do SQL Server local para uma instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298901"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: Migre o SQL Server para uma instância gerenciada do Azure SQL Database on-line usando O MS

Use o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server local para uma [instância gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance.md) com tempo de inatividade mínimo. Para obter métodos adicionais que possam exigir algum esforço manual, consulte a migração da [instância do SQL Server do artigo para a instância gerenciada do Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, você migrará o banco de dados **Adventureworks2012** de uma instância local do SQL Server para uma instância gerenciada do Banco de Dados SQL com tempo de inatividade mínimo usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração e inicie uma migração online usando o Serviço de Migração de Banco de Dados do Azure.
> * Monitorar a migração.
> * Realize a substituição da migração quando você estiver pronto.

> [!IMPORTANT]
> Para migrações online do SQL Server para uma instância gerenciada do Banco de Dados SQL usando o Serviço de Migração de Banco de Dados do Azure, é necessário fornecer o backup completo de banco de dados e os backups de log seguintes no compartilhamento de rede SMB que o serviço pode usar para migrar os bancos de dados. O Serviço de Migração de Banco de Dados do Azure não inicia backups, mas em vez disso, usa os backups existentes, que você já possa ter como parte de seu plano de recuperação de desastre, para a migração.
> Lembre-se de fazer [backups usando a opção WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Além disso, lembre-se de não acrescentar vários backups (ou seja, completo e t-log) em uma única mídia de backup; faça cada backup em um arquivo de backup separado. Por fim, você pode usar backups compactados para reduzir a probabilidade de ocorrência de problemas potenciais associados à migração de backups de grande porte.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

> [!IMPORTANT]
> Para uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure do banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

> [!IMPORTANT]
> Reduza ao máximo a duração do processo de migração on-line para minimizar o risco de interrupção causada pela reconfiguração da ocorrência ou manutenção planejada. Em caso de tal evento, o processo de migração começará desde o início. Em caso de manutenção planejada, há um período de carência de 36 horas antes do processo de migração ser reiniciado.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server para uma instância gerenciada do Banco de Dados SQL. Para uma migração offline, confira [Migrar do SQL Server para uma instância gerenciada do Banco de Dados SQL offline usando DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Crie uma rede virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade local a local aos seus servidores de origem no local usando [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) [Saiba mais sobre as topologias de rede para migrações da instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual e, especialmente, os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com peering de rede para a Microsoft, adicione os [seguintes pontos finais de](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.
    >
    >Se você não tiver conectividade site a site entre a rede local e o Azure ou se houver uma largura de banda de conectividade site a site limitada, considere usar o Serviço de Migração de Banco de Dados do Azure no modo híbrido (versão prévia). O modo híbrido beneficia-se de um trabalho de migração local junto com uma instância do Serviço de Migração de Banco de Dados do Azure em execução na nuvem. Para criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido, confira o artigo [Criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido usando o portal do Azure](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > Em relação à conta de armazenamento usada como parte da migração, é necessário:
    > * Optar por permitir que toda a rede acesse a conta de armazenamento.
    > * Ative a [delegação de sub-rede](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) na sub-rede MI e atualize as regras de firewall da Conta de Armazenamento para permitir essa sub-rede.

* Certifique-se de que as regras do Grupo de Segurança de Rede de Rede de rede virtual não bloqueiem as seguintes portas de comunicação de entrada para o Serviço de Migração de Banco de Dados Do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego DE Rede Virtual NSG, consulte o artigo [Filtrar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)o tráfego da rede com grupos de segurança da rede .
* Configure o [Firewall do Windows para acesso ao mecanismo de banco de dados de fonte](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que, por padrão, é a porta TCP 1433.
* Se você estiver executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez precise habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio dos firewalls, de modo que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada no servidor de origem.
* Se você estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez precise adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, bem como arquivos por meio da porta SMB 445.
* Crie uma instância gerenciada do Banco de Dados SQL seguindo os detalhes do artigo [Criar uma instância gerenciada do Banco de Dados SQL do Azure no portal do Azure](https://aka.ms/sqldbmi).
* Verifique se os logons usados para conectar o SQL Server de origem e a instância gerenciada de destino são membros da função de servidor sysadmin.
* Forneça um compartilhamento de rede SMB que contém todos os arquivos de backup completo do banco de dados e os arquivos de backup de log de transações seguintes que o Serviço de Migração de Banco de Dados do Azure pode usar para a migração de banco de dados.
* Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede que você criou e que a conta de computador do servidor de origem tem acesso de leitura/gravação para o mesmo compartilhamento.
* Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado anteriormente. O Azure Database Migration Service representa a credencial do usuário para carregar os arquivos de backup no contêiner Azure Storage para operação de restauração.
* Crie uma ID do aplicativo do Azure Active Directory que gera a chave da ID do aplicativo que pode ser usada pelo Serviço de Migração de Banco de Dados do Azure para se conectar ao contêiner do Armazenamento do Azure e à instância gerenciada do Banco de Dados do Azure de destino. Para obter mais informações, confira o artigo [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar os recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > O Serviço de Migração de Banco de Dados do Azure exige a permissão de Colaborador na assinatura para a ID do aplicativo especificada. Como alternativa, você pode criar funções personalizadas que concedem as permissões específicas que o Serviço de Migração de Banco de Dados do Azure exige. Para obter orientações passo a passo sobre como usar funções personalizadas, confira o artigo [Funções personalizadas para migrações online de instância gerenciada do SQL Server para o Banco de Dados SQL](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Criar ou anote o **Nível de desempenho Standard**, a Conta de Armazenamento do Azure, que permite que o serviço do DMS faça upload dos arquivos de backup do banco de dados para usá-los na migração de bancos de dados.  Crie a conta do Armazenamento do Azure na mesma região da instância do Serviço de Migração de Banco de Dados do Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Procure migração e, em seguida, à direita do **Microsoft.DataMigration,** selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal Azure, selecione + **Crie um recurso,** procure o **Serviço de Migração de Banco de Dados do Azure**e selecione o Serviço de Migração de Banco de **Dados do Azure** na lista de desígência.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso à instância gerenciada do SQL Server de origem e à instância gerenciada do Banco de Dados SQL de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual usando o portal Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, confira o artigo [Topologias de rede para migrações da Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).

6. Selecione um SKU do tipo de preço Premium.

    > [!NOTE]
    > Há suporte para migrações online somente ao usar a camada Premium.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na tela do **Serviço de Migração de Banco de Dados do Azure,** pesquise o nome da instância que você criou e selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto; na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**; na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerenciada do Banco de Dados SQL do Azure** e, em seguida, em **Escolher tipo de atividade**, selecione **Migração de dados online**.

   ![Criar um projeto do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecione **Criar e executar atividade** para criar o projeto.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado auto-assinado não fornecem forte segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados auto-assinados em um ambiente de produção ou em servidores conectados à internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selecione **Salvar**.

4. Na tela **Selecionar bancos de dados de origem**, selecione o banco de dados **Adventureworks2012** para migração.

   ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se você usa o SSIS (SQL Server Integration Services), o DMS não dá suporte, no momento, à migração do banco de dados de catálogo dos seus projetos/pacotes do SSIS (SSISDB) do SQL Server para a instância gerenciada do Banco de Dados SQL do Azure. No entanto, é possível provisionar o SSIS no ADF (Azure Data Factory) e reimplantar os projetos/pacotes do SSIS no SSISDB de destino hospedado pela instância gerenciada do Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selecione **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes de destino de migração**, especifique a **ID do aplicativo** e a **Chave** que a instância de DMS pode usar para se conectar à instância de destino da instância gerenciada do Banco de Dados SQL do Azure e a Conta de Armazenamento do Azure.

    Para obter mais informações, confira o artigo [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar os recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selecione a **assinatura** que contém a instância de destino da instância gerenciada do Azure SQL Database e selecione a instância de destino.

    Se você ainda não tiver provisionado a instância gerenciada do Azure SQL Database, selecione o [link](https://aka.ms/SQLDBMI) para ajudá-lo a provisionar a instância. Quando a instância gerenciada do Banco de Dados SQL do Azure estiver pronta, retorne a este projeto específico para executar a migração.

3. Forneça o **Usuário do SQL** e a **Senha** para se conectar à instância gerenciada do Banco de Dados SQL do Azure.

    ![Selecionar o destino](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Selecione **Salvar**.

## <a name="select-source-databases"></a>Selecionar bancos de dados de origem

1. Na tela **Selecionar bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar.

    ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Selecione **Salvar**.

## <a name="configure-migration-settings"></a>Definir as configurações de migração

1. Na tela **Definir as configurações de migração**, forneça os seguintes detalhes:

    | | |
    |--------|---------|
    |**Compartilhamento da localização de rede SMB** | O compartilhamento de rede SMB local ou o compartilhamento de arquivo do Azure que contém os arquivos de backup do banco de dados completos e os arquivos de backup do log de transações que o Serviço de Migração de Banco de Dados do Azure pode usar para migração. A conta de serviço que executa a instância do SQL Server de origem precisa ter privilégios de leitura\gravação nesse compartilhamento de rede. Forneça um FQDN ou endereços IP do servidor no compartilhamento de rede, por exemplo, “\\\servername.domainname.com\backupfolder' ou '\\\IP address\backupfolder”. Para melhorar o desempenho, recomenda-se usar pasta separada para que cada banco de dados seja migrado. Você pode fornecer o caminho de compartilhamento de arquivos de nível de banco de dados usando a opção **Configurações Avançadas.** |
    |**Nome de usuário** | Certifique-se de que o usuário do Windows possui privilégios de controle total no compartilhamento de rede fornecido acima. O Azure Database Migration Service se passará pela credencial do usuário para carregar os arquivos de backup no contêiner Azure Storage para operação de restauração. Se estiver usando o compartilhamento de arquivo do Azure, use o nome da conta de armazenamento com o prefixo AZURE \ como nome de usuário. |
    |**Senha** | Senha do usuário. Se estiver usando o compartilhamento de arquivo do Azure, use uma chave de conta de armazenamento como senha. |
    |**Assinatura da Conta de Armazenamento do Azure** | Selecione a assinatura que contém a Conta de Armazenamento do Azure. |
    |**Conta de Armazenamento do Azure** | Selecione a Conta de Armazenamento do Azure da qual o DMS pode fazer upload dos arquivos de backup para o compartilhamento de rede do SMB e usá-los para a migração de banco de dados.  É recomendável selecionar a Conta de Armazenamento na mesma região que o serviço do DMS para um desempenho ideal de upload de arquivo. |

    ![Definir as configurações de migração](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Se o Serviço de Migração do Banco de Dados do Azure mostrar erro 'Erro do sistema 53' ou 'Erro do sistema 57', a causa pode resultar de uma incapacidade do Azure Database Migration Service de acessar o compartilhamento de arquivos do Azure. Se encontrar um desses erros, permita acesso à conta de armazenamento por meio da rede virtual usando as instruções disponíveis [aqui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Se a funcionalidade de verificação de loopback estiver ativada e o sql server de origem e o compartilhamento de arquivos estiverem no mesmo computador, então a fonte não poderá acessar os arquivos que usam FQDN. Para corrigir esse problema, desabilite a funcionalidade de verificação de loopback usando as instruções [aqui](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Selecione **Salvar**.

## <a name="review-the-migration-summary"></a>Análise do resumo da migração

1. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

2. Analise e verifique os detalhes associados ao projeto de migração.

    ![Resumo do projeto de migração](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Executar e monitorar a migração

1. Selecione **Executar migração**.

2. Na tela da atividade de migração, selecione **Atualizar** para atualizar a exibição.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    É possível expandir ainda mais as categorias de logon e banco de dados para monitorar o status da migração dos respectivos objetos de servidor.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Executar a substituição de migração

Depois que o backup completo do banco de dados for restaurado na instância de destino da instância gerenciada do Banco de Dados SQL do Azure, o banco de dados estará disponível para executar uma migração de substituição.

1. Quando você estiver pronto para concluir a migração de banco de dados online, selecione **Iniciar substituição**.

2. Interrompa todo o tráfego de entrada para os bancos de dados de origem.

3. Obtenha o [backup da parte final do log], disponibilize o arquivo de backup no compartilhamento de rede SMB e aguarde até que esse backup de log de transações final seja restaurado.

    Nesse ponto, você verá **Alterações pendentes** definido como 0.

4. Selecione **Confirmar**e, em seguida, **Aplicar**.

    ![Preparar para a substituição completa](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando o status de migração do banco de dados **for concluído,** conecte seus aplicativos à nova instância-alvo da instância gerenciada do Banco de Dados SQL do Azure.

    ![Substituição concluída](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter um tutorial mostrando como migrar um banco de dados para uma instância gerenciada usando o comando T-SQL RESTORE, consulte [Restaurar um backup em uma instância gerenciada usando o comando 'restaurar ''Restaurar'.](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)
* Para obter informações sobre instância gerenciada, consulte [O que é uma instância gerenciada](../sql-database/sql-database-managed-instance.md).
* Para obter informações sobre como conectar aplicativos a uma instância gerenciada, consulte [Aplicativos Connect](../sql-database/sql-database-managed-instance-connect-app.md).
