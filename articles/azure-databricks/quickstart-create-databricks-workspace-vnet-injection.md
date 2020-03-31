---
title: Crie um espaço de trabalho do Azure Databricks em sua própria Rede Virtual quickstart
description: Este artigo descreve como implantar o Azure Databricks em sua rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132672"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Quickstart: Crie um espaço de trabalho do Azure Databricks em sua própria rede virtual

A implantação padrão do Azure Databricks cria uma nova rede virtual gerenciada pelo Databricks. Esse quickstart mostra como criar um espaço de trabalho do Azure Databricks em sua própria rede virtual. Você também cria um aglomerado Apache Spark dentro desse espaço de trabalho. 

Para obter mais informações sobre por que você pode optar por criar um espaço de trabalho do Azure Databricks em sua própria rede virtual, consulte [Implantar tijolos de dados do Azure em sua Rede Virtual Azure (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no [portal Azure](https://portal.azure.com/).

> [!Note]
> Este tutorial não pode ser realizado usando a **Assinatura de avaliação gratuita do Azure**.
> Se você tiver uma conta gratuita, acesse seu perfil e altere para uma assinatura **pré-paga**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) e [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para as vCPUs da sua região. Quando você cria seu espaço de trabalho do Azure Databricks, pode selecionar o tipo de preço **Versão de avaliação (Premium - DBUs gratuitas por 14 dias)** para conceder ao espaço de trabalho acesso gratuito aos DBUs do Premium Azure Databricks por 14 dias.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. Em seguida, **selecione Rede > Rede Virtual**.

    ![Crie uma rede virtual no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Em **Criar rede virtual,** aplique as seguintes configurações: 

    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Subscription|\<Sua assinatura\>|Selecione a assinatura do Azure que você deseja usar.|
    |Resource group|databricks-quickstart|Selecione **Criar novo** e digite um novo nome de grupo de recursos para sua conta.|
    |Nome|databricks-quickstart|Selecione um nome para sua rede virtual.|
    |Região|\<Selecione a região mais próxima de seus usuários\>|Selecione um local geográfico onde você pode hospedar sua rede virtual. Use a localização mais próxima dos usuários.|

    ![Noções básicas para uma rede virtual no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Selecione **A seguir: Endereços IP >** e aplique as seguintes configurações. Em seguida, **selecione 'Revisar + criar '''**
    
    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Espaço de endereço IPv4|10.2.0.0/16|O intervalo de endereços da rede virtual na notação CIDR. A faixa CIDR deve estar entre /16 e /24|
    |Nome da sub-rede|default|Selecione um nome para a sub-rede padrão em sua rede virtual.|
    |Intervalo de endereços da sub-rede|10.2.0.0/24|O intervalo de endereços da sub-rede na notação CIDR. Deve ser contido pelo espaço de endereço da rede virtual. O intervalo de endereços de uma sub-rede em uso não pode ser editado.|

    ![Definir configurações IP para uma rede virtual no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Na guia **'Revisar + criar',** selecione **Criar** para implantar a rede virtual. Uma vez concluída a implantação, navegue até sua rede virtual e selecione **Espaço de endereço** em **Configurações**. Na caixa que diz Adicionar intervalo `10.179.0.0/16` de endereço *adicional,* insira e selecione **Salvar**.

    ![Espaço de endereço de rede virtual do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

1. No menu do portal do Azure, selecione **Criar um recurso**. Em seguida, **selecione Analytics > Databricks**.

    ![Crie um espaço de trabalho do Azure Databricks no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. Em **Azure Databricks Service,** aplique as seguintes configurações:

    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Nome do workspace|databricks-quickstart|Selecione um nome para o seu espaço de trabalho Azure Databricks.|
    |Subscription|\<Sua assinatura\>|Selecione a assinatura do Azure que você deseja usar.|
    |Resource group|databricks-quickstart|Selecione o mesmo grupo de recursos usado para a rede virtual.|
    |Location|\<Selecione a região mais próxima de seus usuários\>|Escolha o mesmo local que sua rede virtual.|
    |Camada de preços|Escolha entre o cluster Standard e o Premium.|Para obter mais informações sobre os níveis de preços, consulte a [página de preços databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Crie um básico de espaço de trabalho do Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Depois de terminar de inserir configurações na página **Básico,** selecione **Next: Networking >** e aplique as seguintes configurações:

    |Configuração|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Implantar o espaço de trabalho do Azure Databricks em sua Rede Virtual (VNet)|Sim|Essa configuração permite que você implante um espaço de trabalho do Azure Databricks em sua rede virtual.|
    |Rede Virtual|databricks-quickstart|Selecione a rede virtual criada na seção anterior.|
    |Nome da sub-rede pública|sub-rede pública|Use o nome da sub-rede pública padrão.|
    |Faixa PÚBLICA CIDR da Sub-rede|10.179.64.0/18|Use uma faixa CIDR até e incluindo /26.|
    |Nome da sub-rede privada|sub-rede privada|Use o nome da sub-rede privada padrão.|
    |Faixa CIDR da sub-rede privada|10.179.0.0/18|Use uma faixa CIDR até e incluindo /26.|

    ![Adicione informações da VNet ao espaço de trabalho do Azure Databricks no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Uma vez concluída a implantação, navegue até o recurso Azure Databricks. Observe que o peering de rede virtual está desativado. Observe também o grupo de recursos e o grupo de recursos gerenciados na página visão geral. 

    ![Visão geral do Azure Databricks no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    O grupo de recursos gerenciados não é modificável e não é usado para criar máquinas virtuais. Você só pode criar máquinas virtuais no grupo de recursos que você gerencia.

    ![Grupo de recursos gerenciado pelo Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    Quando uma implantação de espaço de trabalho falha, o espaço de trabalho ainda é criado em um estado com falha. Exclua o espaço de trabalho com falha e crie um novo espaço de trabalho que resolva os erros de implantação. Quando você exclui o espaço de trabalho com falha, o grupo de recursos gerenciado e quaisquer recursos implantados com sucesso também são excluídos.

## <a name="create-a-cluster"></a>Criar um cluster

> [!NOTE]
> Para usar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, vá até o seu perfil e altere sua assinatura para **pré-pago**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. Retorne ao serviço Azure Databricks e selecione **Iniciar espaço de trabalho** na página Visão **geral.**

2. Selecionar **clusters** > **+ criar clusters**. Em seguida, crie um nome de cluster, como *databricks-quickstart-cluster*e aceite as configurações padrão restantes. Selecione **Criar Cluster**.

    ![Criar cluster Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Uma vez que o cluster esteja em execução, retorne ao grupo de recursos gerenciado no portal Azure. Observe as novas máquinas virtuais, discos, endereço IP e interfaces de rede. Uma interface de rede é criada em cada uma das sub-redes públicas e privadas com endereços IP.  

    ![Azure Databricks gerenciou grupo de recursos após a criação de clusters](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Retorne ao seu espaço de trabalho do Azure Databricks e selecione o cluster que você criou. Em seguida, navegue até a guia **Executores** na página **Spark UI.** Observe que os endereços do motorista e dos executores estão na faixa de sub-rede privada. Neste exemplo, o motorista é 10.179.0.6 e os executores são 10.179.0.4 e 10.179.0.5. Seus endereços IP poderiam ser diferentes.

    ![Azure Databricks Spark UI executores](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar o artigo, você poderá encerrar o cluster. Para isso, no workspace do Azure Databricks, no painel esquerdo, selecione **Clusters**. No cluster que deseja encerrar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**. Isso pára o aglomerado.

Se você não terminar manualmente o cluster, ele irá parar automaticamente, desde que você selecione a **caixa de seleção Terminar após \_ \_ minutos de inatividade** enquanto cria o cluster. Nesse caso, o cluster é interrompido automaticamente se ficar inativo durante o tempo especificado.

Se você não quiser reutilizar o cluster, você pode excluir o grupo de recursos que você criou no portal Azure.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um cluster Spark no Azure Databricks que você implantou em uma rede virtual. Avance para o próximo artigo para saber como consultar um contêiner SQL Server Linux Docker na rede virtual usando JDBC a partir de um notebook Azure Databricks.  

> [!div class="nextstepaction"]
>[Consultar um contêiner do Docker do Linux do SQL Server em uma rede virtual de um notebook do Azure Databricks](vnet-injection-sql-server.md)
