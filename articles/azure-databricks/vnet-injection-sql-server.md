---
title: Consulta um contêiner SQL Server Linux Docker com tijolos de dados do Azure
description: Este artigo descreve como implantar o Azure Databricks em sua rede virtual, também conhecida como injeção VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747670"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Tutorial: Consulta um contêiner SQL Server Linux Docker em uma rede virtual a partir de um notebook Azure Databricks

Este tutorial ensina como integrar o Azure Databricks com um contêiner SQL Server Linux Docker em uma rede virtual. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar um espaço de trabalho do Azure Databricks em uma rede virtual
> * Instale uma máquina virtual Linux em uma rede pública
> * Instalar o Docker
> * Instale o Microsoft SQL Server no contêiner docker Linux
> * Consultar o SQL Server usando JDBC a partir de um notebook Databricks

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [espaço de trabalho Databricks em uma rede virtual](quickstart-create-databricks-workspace-vnet-injection.md).

* Instale [o Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Baixe [o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual Linux

1. No portal Azure, selecione o ícone para **Máquinas Virtuais**. Em seguida, selecione **+ Adicionar**.

    ![Adicionar nova máquina virtual Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na guia **Básico,** escolha o Servidor Ubuntu 18.04 LTS e altere o tamanho da VM para B2s. Escolha um nome de usuário e senha do administrador.

    ![Guia básica da nova configuração da máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navegue até a guia **Rede.** Escolha a rede virtual e a sub-rede pública que inclui o cluster Azure Databricks. Selecione **'Revisar + criar'** e **criar** para implantar a máquina virtual.

    ![Guia de rede da nova configuração da máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Quando a implantação estiver concluída, navegue até a máquina virtual. Observe o endereço IP público e a rede/sub-rede virtual na **visão geral**. Selecione o **endereço IP público**

    ![Visão geral da máquina virtual](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Altere a **Atribuição** para **Estática** e digite um **rótulo de nome DNS**. Selecione **Salvar**e reiniciar a máquina virtual.

    ![Configuração pública de endereço IP](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selecione a guia **'Rede'** em **Configurações**. Observe que o grupo de segurança de rede criado durante a implantação do Azure Databricks está associado à máquina virtual. Selecione **Adicionar regra de porta de entrada**.

7. Adicione uma regra para abrir a porta 22 para SSH. Use as configurações a seguir:
    
    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Fonte|Endereços IP|Os endereços IP especificam que o tráfego de entrada de um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Endereços IP da fonte|<seu ip público\>|Digite o seu endereço IP público. Você pode encontrar seu endereço IP público visitando [bing.com](https://www.bing.com/) e procurando **por "meu IP".**|
    |Intervalos de portas de origem|*|Permitir o tráfego de qualquer porta.|
    |Destino|Endereços IP|Os endereços IP especificam que o tráfego de saída para um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Endereços IP de destino|<seu ip público vm\>|Digite o endereço IP público da sua máquina virtual. Você pode encontrá-lo na página **Visão Geral** da sua máquina virtual.|
    |Intervalos de portas de destino|22|Abra a porta 22 para SSH.|
    |Prioridade|290|Dê prioridade à regra.|
    |Nome|ssh-databricks-tutorial-vm|Dê um nome à regra.|


    ![Adicionar regra de segurança de entrada para a porta 22](./media/vnet-injection-sql-server/open-port.png)

8. Adicionar uma regra para abrir a porta 1433 para SQL com as seguintes configurações:

    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Fonte|Qualquer|A fonte especifica que o tráfego de entrada de um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Intervalos de portas de origem|*|Permitir o tráfego de qualquer porta.|
    |Destino|Endereços IP|Os endereços IP especificam que o tráfego de saída para um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Endereços IP de destino|<seu ip público vm\>|Digite o endereço IP público da sua máquina virtual. Você pode encontrá-lo na página **Visão Geral** da sua máquina virtual.|
    |Intervalos de portas de destino|1433|Abra a porta 22 para o SQL Server.|
    |Prioridade|300|Dê prioridade à regra.|
    |Nome|sql-databricks-tutorial-vm|Dê um nome à regra.|

    ![Adicionar regra de segurança de entrada para a porta 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Execute o SQL Server em um contêiner Docker

1. Abra [o Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)ou qualquer outra ferramenta que permita que você entre na máquina virtual. Navegue até sua máquina virtual no portal Azure e selecione **Conectar** para obter o comando SSH que você precisa conectar.

    ![Conectar-se à máquina virtual](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Digite o comando no terminal Ubuntu e digite a senha de admin que você criou quando configurou a máquina virtual.

    ![Login SSH do terminal Ubuntu](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Use o seguinte comando para instalar o Docker na máquina virtual.

    ```bash
    sudo apt-get install docker.io
    ```

    Verifique a instalação do Docker com o seguinte comando:

    ```bash
    sudo docker --version
    ```

4. Instale a imagem.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Veja as imagens.

    ```bash
    sudo docker images
    ```

5. Execute o recipiente da imagem.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Verifique se o recipiente está funcionando.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Criar um Banco de Dados SQL

1. Abra o SQL Server Management Studio e conecte-se ao servidor usando o nome do servidor e a Autenticação SQL. O sinal de usuário no nome de usuário é **SA** e a senha é a senha definida no comando Docker. A senha no comando `Password1234`exemplo é .

    ![Conecte-se ao SQL Server usando o SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Depois de conectado com sucesso, selecione **Nova consulta** e digite o seguinte trecho de código para criar um banco de dados, uma tabela e inserir alguns registros na tabela.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Consulta para criar um banco de dados SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Consulta SQL Server do Azure Databricks

1. Navegue até o seu espaço de trabalho do Azure Databricks e verifique se você criou um cluster como parte dos pré-requisitos. Em seguida, **selecione Criar um notebook**. Dê um nome ao notebook, selecione *Python* como idioma e selecione o cluster que você criou.

    ![Novas configurações do notebook Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Use o seguinte comando para pingar o endereço IP interno da máquina virtual SQL Server. Este ping deve ser bem sucedido. Caso assim, verifique se o contêiner está em execução e revise a configuração do Grupo de Segurança de Rede (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Você também pode usar o comando nslookup para revisar.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Depois de ter pingado com sucesso o SQL Server, você pode consultar o banco de dados e as tabelas. Execute o seguinte código python:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessário, exclua o grupo de recursos, o workspace do Azure Databricks e todos os recursos relacionados. Excluir o trabalho evita cobrança desnecessária. Se você está planejando usar o workspace do Azure Databricks no futuro, pode parar o cluster e reiniciá-lo mais tarde. Se você não pretende continuar a usar esse workspace do Azure Databricks, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do grupo de recursos criado.

2. Em sua página de grupo de recursos, selecione **Excluir**, digite o nome do recurso a ser excluído na caixa de texto e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como extrair, transformar e carregar dados usando o Azure Databricks.
> [!div class="nextstepaction"]
> [Tutorial: Extraia, transforme e carregue dados usando o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
