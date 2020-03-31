---
title: Migrar dados para a conta API Azure Cosmos DB Cassandra usando striim
description: Aprenda a usar o Striim para migrar dados de um banco de dados Oracle para uma conta API Azure Cosmos DB Cassandra.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: af99f369245d6006d0d4784e572020b820e406a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266050"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migrar dados para a conta API Azure Cosmos DB Cassandra usando striim

A imagem Striim no mercado Azure oferece movimentação contínua de dados em tempo real de data warehouses e bancos de dados para o Azure. Ao mover os dados, você pode executar a desnormalização em linha, a transformação de dados, a ativação de análises em tempo real e cenários de relatórios de dados. É fácil começar com a Striim para mover continuamente dados corporativos para a API Azure Cosmos DB Cassandra. O Azure oferece uma oferta de marketplace que facilita a implantação do Striim e a migração de dados para o Azure Cosmos DB. 

Este artigo mostra como usar o Striim para migrar dados de um banco de **dados Oracle** para uma **conta API Azure Cosmos DB Cassandra**.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma [assinatura do Azure,](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Um banco de dados Oracle executando no local com alguns dados nele.

## <a name="deploy-the-striim-marketplace-solution"></a>Implantar a solução de mercado Striim

1. Inscreva-se no [portal Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** e procurar por **Striim** no mercado Azure. Selecione a primeira opção e **Crie**.

   ![Encontre o item do mercado Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Em seguida, digite as propriedades de configuração da instância Striim. O ambiente Striim é implantado em uma máquina virtual. No painel **Básico,** digite o **nome de usuário vm**, **vm senha** (esta senha é usada para SSH na VM). Selecione os **detalhes** **de assinatura,** **grupo de recursos**e localização onde você gostaria de implantar o Striim. Uma vez concluído, selecione **OK**.

   ![Configure as configurações básicas para Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. No painel **de configurações do Striim Cluster,** escolha o tipo de implantação de Striim e o tamanho da máquina virtual.

   |Configuração | Valor | Descrição |
   | ---| ---| ---|
   |Tipo de implantação de Striim |Autônomo | O Striim pode ser executado em um tipo de implantação **standalone** ou **cluster.** O modo autônomo implantará o servidor Striim em uma única máquina virtual e você poderá selecionar o tamanho das VMs dependendo do volume de dados. O modo cluster implantará o servidor Striim em duas ou mais VMs com o tamanho selecionado. Ambientes de cluster com mais de 2 nós oferecem alta disponibilidade automática e failover.</br></br> Neste tutorial, você pode selecionar a opção Autônomo. Use a VM padrão de tamanho "Standard_F4s". | 
   | Nome do cluster Striim|    <Striim_cluster_Name Striim_cluster_Name>|  Nome do aglomerado Striim.|
   | Senha do cluster Striim|   Striim_cluster_password Striim_cluster_password> <|  Senha para o cluster.|

   Depois de preencher o formulário, selecione **OK** para continuar.

1. No **painel de configurações de acesso Striim,** configure o **endereço IP público** (escolha os valores padrão), nome de domínio para **Striim**, **senha de administrador** que você gostaria de usar para fazer login na UI Striim. Configure uma VNET e uma sub-rede (escolha os valores padrão). Depois de preencher os detalhes, selecione **OK** para continuar.

   ![Configurações de acesso striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. O Azure validará a implantação e garantirá que tudo fique bem; a validação leva alguns minutos para ser concluída. Depois que a validação for concluída, selecione **OK**.
  
1. Por fim, revise os termos de uso e selecione **Criar** para criar sua instância Striim. 

## <a name="configure-the-source-database"></a>Configure o banco de dados de origem

Nesta seção, você configura o banco de dados Oracle como a fonte para movimentação de dados.  Você precisará do [driver Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para se conectar à Oracle. Para ler as alterações do banco de dados Oracle de origem, você pode usar o [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) ou as [APIs do XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). O driver Oracle JDBC deve estar presente no classpath Java da Striim para ler, escrever ou persistir dados do banco de dados Oracle.

Baixe o [driver ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) na sua máquina local. Você irá instalá-lo no cluster Striim mais tarde.

## <a name="configure-target-database"></a>Configurar banco de dados de destino

Nesta seção, você configurará a conta Api Azure Cosmos DB Cassandra como o alvo para a movimentação de dados.

1. Crie [uma conta api Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account) usando o portal Azure.

1. Navegue até o painel **data explorer** em sua conta do Azure Cosmos. Selecione **Nova tabela** para criar um novo contêiner. Assuma que você está migrando *produtos* e *encomenda* dados do banco de dados Oracle para o Azure Cosmos DB. Crie um novo Espaço-Chave chamado **StriimDemo** com um contêiner Orders. Provisão o contêiner com **1000 RUs**(este exemplo usa 1000 RUs, mas você deve usar o throughput estimado para sua carga de trabalho) e **/ORDER_ID** como a chave principal. Esses valores diferem dependendo dos dados de origem. 

   ![Criar conta de API de Cassandra](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configurar o fluxo de dados Oracle para Azure Cosmos DB

1. Agora, vamos voltar para Striim. Antes de interagir com o Striim, instale o driver Oracle JDBC que você baixou anteriormente.

1. Navegue até a instância Striim que você implantou no portal Azure. Selecione o botão **Conectar** na barra superior do menu e na guia **SSH,** copie a URL em Login usando o campo **de conta local da VM.**

   ![Obtenha a URL SSH](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Abra uma nova janela de terminal e execute o comando SSH que você copiou do portal Azure. Este artigo usa terminal em um MacOS, você pode seguir as instruções semelhantes usando PuTTY ou um cliente SSH diferente em uma máquina Windows. Quando solicitado, digite **sim** para continuar e digite a **senha** definida para a máquina virtual na etapa anterior.

   ![Conecte-se ao Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Agora, abra uma nova guia de terminal para copiar o arquivo **ojdbc8.jar** que você baixou anteriormente. Use o seguinte comando SCP para copiar o arquivo de jarda da sua máquina local para a pasta tmp da instância Striim em execução no Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Copie o arquivo Jar da máquina de localização para Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Em seguida, navegue de volta para a janela onde você fez SSH para a instância Striim e Login como sudo. Mova o arquivo **ojdbc8.jar** do diretório **/tmp** para o diretório **lib** da sua instância Striim com os seguintes comandos:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Mova o arquivo Jar para a pasta lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Na mesma janela de terminal, reinicie o servidor Striim executando os seguintes comandos:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim vai levar um minuto para começar. Se quiser ver o status, execute o seguinte comando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Agora, navegue de volta ao Azure e copie o endereço IP público da sua VM Striim. 

   ![Copiar endereço IP Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Para navegar até a Interface do Usuário da Striim, abra uma nova guia em um navegador e copie o IP público seguido por: 9080. Faça login usando o nome de usuário do **administrador,** juntamente com a senha de administrador especificada no portal Azure.

   ![Faça login em Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Agora você vai chegar na página inicial de Striim. Existem três painéis diferentes – **Dashboards**, **Apps**e **SourcePreview**. O painel Dashboards permite mover dados em tempo real e visualizá-los. O painel Aplicativos contém seus pipelines de dados de streaming ou fluxos de dados. No lado direito da página está SourcePreview onde você pode visualizar seus dados antes de movê-los.

1. Selecione o painel **apps,** vamos focar neste painel por enquanto. Existem uma variedade de aplicativos de exemplo que você pode usar para aprender sobre Striim, no entanto, neste artigo você criará o nosso próprio. Selecione o botão **Adicionar aplicativo** no canto superior direito.

   ![Adicione o aplicativo Striim](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Existem algumas maneiras diferentes de criar aplicativos Striim. Selecione **Start from Scratch** para este cenário.

   ![Inicie o aplicativo do zero](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Dê um nome amigável para o seu aplicativo, algo como **oraToCosmosDB** e selecione **Salvar**.

   ![Crie um novo aplicativo](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. Você chegará ao Flow Designer, onde poderá arrastar e soltar os conectores da caixa para criar seus aplicativos de streaming. Digite **o Oracle** na barra de pesquisa, arraste e solte a fonte **oracle CDC** na tela do aplicativo.  

   ![Fonte Oracle CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Digite as propriedades de configuração de origem da instância Oracle. O nome de origem é apenas uma convenção de nomeação para o aplicativo Striim, você pode usar um nome como **src_onPremOracle**. Digite também outros detalhes como tipo de adaptador, URL de conexão, nome de usuário, senha, nome da tabela. Selecione **Salvar** para continuar.

   ![Configurar parâmetros de origem](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. Agora, clique no ícone de onda do fluxo para conectar a instância DB do Azure Cosmos. 

   ![Conectar-se ao destino](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. Antes de configurar o alvo, certifique-se de ter adicionado um [certificado de raiz de Baltimore ao ambiente Java da Striim](/azure/java/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store).

1. Digite as propriedades de configuração da instância DB do Azure Cosmos e selecione **Salvar** para continuar. Aqui estão os principais parâmetros a serem observados:

   * **Adaptador** - Use **DatabaseWriter**. Ao escrever para a API Azure Cosmos DB Cassandra, o DatabaseWriter é necessário. O motorista Cassandra 3.6.0 está empacotado com Striim. Se o DatabaseWriter exceder o número de RUs provisionados no contêiner Azure Cosmos, o aplicativo falhará.

   * **URL de conexão** - Especifique sua URL de conexão Azure Cosmos DB JDBC. A URL está no formato`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Nome de usuário** - Especifique o nome da sua conta do Azure Cosmos.
   
   * **Senha** - Especifique a chave principal da sua conta do Azure Cosmos.

   * **Tabelas** - As tabelas de destino devem ter teclas primárias e as teclas primárias não podem ser atualizadas.

   ![Configurar propriedades de destino](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Configurar propriedades de destino](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Agora, vamos em frente e executar a aplicação Striim. Na barra de menu superior selecione **Criado**e, em seguida, **implantar o aplicativo**. Na janela de implantação, você pode especificar se deseja executar certas partes do aplicativo em partes específicas da topologia de implantação. Uma vez que estamos executando em uma simples topologia de implantação através do Azure, usaremos a opção padrão.

   ![Implantar o aplicativo](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Agora, vamos em frente e visualizar o fluxo para ver os dados fluindo através do Striim. Clique no ícone de onda e clique no ícone de olho ao lado dele. Após a implantação, você pode visualizar o fluxo para ver os dados fluindo. Selecione o ícone **de onda** e o **globo ocular** ao lado. Selecione o botão **Implantado** na barra de menu superior e selecione **Iniciar aplicativo**.

   ![Iniciar o aplicativo](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. Usando um leitor **CDC (Change Data Capture),** o Striim captará apenas novas alterações no banco de dados. Se você tiver dados fluindo através de suas tabelas de origem, você verá. No entanto, como esta é uma tabela de amostras, a fonte que não está conectada a nenhuma aplicação. Se você usar um gerador de dados de exemplo, você pode inserir uma cadeia de eventos em seu banco de dados Oracle.

1. Você verá os dados fluindo através da plataforma Striim. Striim pega todos os metadados associados à sua tabela também, o que é útil para monitorar os dados e certificar-se de que os dados aterrissem no alvo certo.

   ![Configure o gasoduto CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Finalmente, vamos entrar no Azure e navegar até sua conta do Azure Cosmos. Atualize o Data Explorer e você pode ver que os dados chegaram. 

Usando a solução Striim no Azure, você pode migrar continuamente dados para o Azure Cosmos DB de várias fontes como Oracle, Cassandra, MongoDB e várias outras para o Azure Cosmos DB. Para saber mais, visite o site da [Striim,](https://www.striim.com/) [baixe um teste gratuito de 30 dias do Striim](https://go2.striim.com/download-free-trial)e para quaisquer problemas ao configurar o caminho de migração com o Striim, faça uma solicitação de [suporte.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Próximas etapas

* Se você está migrando dados para a API AqL AQL do Azure Cosmos, veja [como migrar dados para a conta da API cassandra usando o Striim](cosmosdb-sql-api-migrate-data-striim.md)

* [Monitore e depura seus dados com métricas DB do Azure Cosmos](use-metrics.md)
