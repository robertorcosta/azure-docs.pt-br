---
title: Criar um espaço de trabalho Azure Databricks em uma rede virtual
description: Este artigo descreve como implantar Azure Databricks em sua rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 69afe2aab3c10707f7160d727b970ad73d59a952
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791553"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Início rápido: criar um espaço de trabalho Azure Databricks em uma rede virtual

Este guia de início rápido mostra como criar um Azure Databricks espaço de trabalho em uma rede virtual. Você também criará um cluster Apache Spark dentro desse espaço de trabalho.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

> [!Note]
> Este tutorial não pode ser realizado usando a **Assinatura de avaliação gratuita do Azure**.
> Se você tiver uma conta gratuita, acesse seu perfil e altere para uma assinatura **pré-paga**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) e [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para as vCPUs da sua região. Quando você cria seu espaço de trabalho do Azure Databricks, pode selecionar o tipo de preço **Versão de avaliação (Premium - DBUs gratuitas por 14 dias)** para conceder ao espaço de trabalho acesso gratuito aos DBUs do Premium Azure Databricks por 14 dias.

## <a name="create-a-virtual-network"></a>Crie uma rede virtual

1. Na portal do Azure, selecione **criar um recurso** > **rede** > **rede virtual**.

2. Em **criar rede virtual**, aplique as seguintes configurações: 

    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |name|databricks-início rápido|Selecione um nome para sua rede virtual.|
    |Espaço de endereço|10.1.0.0/16|O intervalo de endereços da rede virtual na notação CIDR.|
    |Subscription|\<Sua assinatura\>|Selecione a assinatura do Azure que você deseja usar.|
    |Resource group|databricks-início rápido|Selecione **criar novo** e insira um novo nome de grupo de recursos para sua conta.|
    |Location|\<Selecione a região mais próxima de seus usuários\>|Selecione uma localização geográfica na qual você pode hospedar sua rede virtual. Use a localização mais próxima dos usuários.|
    |Nome da sub-rede|padrão|Selecione um nome para a sub-rede padrão em sua rede virtual.|
    |Intervalo de endereços da sub-rede|10.1.0.0/24|O intervalo de endereços da sub-rede na notação CIDR. Ele deve estar contido no espaço de endereço da rede virtual. O intervalo de endereços de uma sub-rede que está em uso não pode ser editado.|

    ![Criar uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Depois que a implantação for concluída, navegue até sua rede virtual e selecione **espaço de endereço** em **configurações**. Na caixa que diz *Adicionar intervalo de endereços adicional*, insira `10.179.0.0/16` e selecione **salvar**.

    ![Espaço de endereço de rede virtual do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

1. Na portal do Azure, selecione **criar um recurso** > **Analytics** > **databricks**.

2. Em **serviço Azure Databricks**, aplique as seguintes configurações:

    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Nome do workspace|databricks-início rápido|Selecione um nome para seu espaço de trabalho Azure Databricks.|
    |Subscription|\<Sua assinatura\>|Selecione a assinatura do Azure que você deseja usar.|
    |Resource group|databricks-início rápido|Selecione o mesmo grupo de recursos que você usou para a rede virtual.|
    |Location|\<Selecione a região mais próxima de seus usuários\>|Escolha o mesmo local da sua rede virtual.|
    |Camada de preços|Escolha entre Standard ou Premium.|Para obter mais informações sobre tipos de preço, consulte a [página de preços do databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Implantar Azure Databricks espaço de trabalho em sua rede virtual|SIM|Essa configuração permite que você implante um espaço de trabalho Azure Databricks em sua rede virtual.|
    |Rede virtual|databricks-início rápido|Selecione a rede virtual que você criou na seção anterior.|
    |Nome da sub-rede pública|sub-rede pública|Use o nome da sub-rede pública padrão.|
    |Intervalo de CIDR da sub-rede pública|10.179.64.0/18|O intervalo CIDR para essa sub-rede deve estar entre/18 e/26.|
    |Nome da sub-rede privada|privada-sub-rede|Use o nome da sub-rede privada padrão.|
    |Intervalo de CIDR de sub-rede privada|10.179.0.0/18|O intervalo CIDR para essa sub-rede deve estar entre/18 e/26.|

    ![Criar um espaço de trabalho Azure Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Quando a implantação for concluída, navegue até o recurso Azure Databricks. Observe que o emparelhamento de rede virtual está desabilitado. Observe também o grupo de recursos e o grupo de recursos gerenciados na página Visão geral. 

    ![Azure Databricks visão geral no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    O grupo de recursos gerenciado contém o local físico da conta de armazenamento (DBFS), Worker-SG (grupo de segurança de rede), Works-vnet (rede virtual). Também é o local onde as máquinas virtuais, o disco, o endereço IP e a interface de rede serão criados. Esse grupo de recursos está bloqueado por padrão; no entanto, quando um cluster é iniciado na rede virtual, uma interface de rede é criada entre os trabalhadores-vnet no grupo de recursos gerenciados e a rede virtual "Hub".

    ![Azure Databricks grupo de recursos gerenciados](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Criar um cluster

> [!NOTE]
> Para usar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, vá até o seu perfil e altere sua assinatura para **pré-pago**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. Retorne ao serviço de Azure Databricks e selecione **Iniciar espaço de trabalho** na página **visão geral** .

2. Selecione **clusters** >  **+ criar cluster**. Em seguida, crie um nome de cluster, como *databricks-QuickStart-cluster*, e aceite as configurações padrão restantes. Selecione **Criar Cluster**.

    ![Criar Azure Databricks cluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Depois que o cluster estiver em execução, retorne ao grupo de recursos gerenciados no portal do Azure. Observe as novas máquinas virtuais, discos, endereço IP e interfaces de rede. Uma interface de rede é criada em cada uma das sub-redes públicas e privadas com endereços IP.  

    ![Azure Databricks grupo de recursos gerenciado após a criação do cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Retorne ao seu espaço de trabalho Azure Databricks e selecione o cluster que você criou. Em seguida, navegue até a guia **executores** na página **da interface do usuário do Spark** . Observe que os endereços para o driver e os executores estão no intervalo de sub-rede privada. Neste exemplo, o driver é 10.179.0.6 e executores são 10.179.0.4 e 10.179.0.5. Seus endereços IP podem ser diferentes.

    ![Azure Databricks executores da interface do usuário do Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar o artigo, você poderá encerrar o cluster. Para isso, no workspace do Azure Databricks, no painel esquerdo, selecione **Clusters**. No cluster que deseja encerrar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**. Isso interrompe o cluster.

Se você não encerrar o cluster manualmente, ele será interrompido automaticamente, desde que você tenha selecionado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster é interrompido automaticamente se ficar inativo durante o tempo especificado.

Se você não quiser reutilizar o cluster, poderá excluir o grupo de recursos criado na portal do Azure.

## <a name="next-steps"></a>Próximos passos

Neste artigo, você criou um cluster Spark no Azure Databricks implantado em uma rede virtual. Avance para o próximo artigo para saber como consultar um contêiner SQL Server do Docker do Linux na rede virtual usando JDBC em um notebook Azure Databricks.  

> [!div class="nextstepaction"]
>[Consultar um SQL Server contêiner do Docker do Linux em uma rede virtual de um notebook Azure Databricks](vnet-injection-sql-server.md)
