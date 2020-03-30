---
title: Tutorial para configurar um tempo de execução de integração Azure-SSIS para se juntar a uma rede virtual
description: Saiba como participar de um tempo de execução de integração Azure-SSIS em uma rede virtual Do Zure.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 065610a9de4898d012cef8a16849c09a81f0774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841090"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Configure um Tempo de execução (SSIS) de integração de servidores Azure-SQL para ingressar em uma rede virtual

Este tutorial fornece etapas básicas para usar o portal Azure para configurar um Tempo de execução de integração de servidores Azure-SQL (SSIS) para se juntar a uma rede virtual.

As etapas são as seguintes:

- Configure uma rede virtual.
- Junte-se ao IR Azure-SSIS para uma rede virtual do portal Azure Data Factory.

## <a name="prerequisites"></a>Pré-requisitos

- **Tempo de execução da integração Azure-SSIS**. Se você não tiver um tempo de execução de integração Azure-SSIS, [provisione um tempo de execução de integração Azure-SSIS na Fábrica de Dados Azure](tutorial-deploy-ssis-packages-azure.md) antes de começar.

- **Permissão do usuário**. O usuário que cria o IR Azure-SSIS deve ter a [atribuição da função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) pelo menos no recurso azure Data Factory com uma das opções abaixo:

    - Use a função interna de Colaborador de Rede. Essa função vem com a permissão _Microsoft.Network/\*_, que tem um escopo muito maior do que o necessário.
    - Crie uma função personalizada que inclua apenas a permissão _Microsoft.Network/virtualNetworks/\*/join/action_. Se você também quiser trazer seus próprios endereços IP públicos para o Azure-SSIS IR ao mesmo tempo em que se junta a uma rede virtual do Azure Resource Manager, inclua também a _permissão Microsoft.Network/publicIPAddresses/*/join/action_ na função.

- **Rede virtual**.

    - Se você não tiver uma rede virtual, [crie uma rede virtual usando o portal Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Certifique-se de que o grupo de recursos da rede virtual possa criar e excluir certos recursos de rede do Azure.
    
        O Azure-SSIS IR precisa criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Esses recursos incluem:
        - Um balanceador de carga Do Zure, com o nome * \<Guid>-azurebatch-cloudserviceloadbalancer*
        - Um grupo de segurança de\<rede, com o nome * Guid>-azurebatch-cloudservicesecuritygroup
        - Um endereço IP público do Azure, com o nome -azurebatch-cloudservicepublicip
    
        Esses recursos serão criados quando o seu IR Azure-SSIS for iniciado. Eles serão excluídos quando o seu Azure-SSIS IR parar. Para evitar que o Azure-SSIS IR pare, não reutilize esses recursos de rede em seus outros recursos.

    - Certifique-se de que não tem [bloqueio de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) no grupo de recursos/assinatura ao qual a rede virtual pertence. Se você configurar um bloqueio somente leitura/exclusão, iniciar e interromper o Azure-SSIS IR falhará ou ele deixará de responder.

    - Certifique-se de que você não tem uma política do Azure que impeça a criação dos seguintes recursos o grupo de recursos/assinatura ao qual a rede virtual pertence:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Os cenários de **configuração** de rede abaixo não são abordados neste tutorial:
    - Se você trouxer seus próprios endereços IP públicos para o Azure-SSIS IR.
    - Se você usar seu próprio servidor DNS (Domain Name System, sistema de nome de domínio).
    - Se você usar um grupo de segurança de rede (NSG) na sub-rede.
    - Se você usar o Azure ExpressRoute ou uma rota definida pelo usuário (UDR).
    - Se você usar o Azure-SSIS IR personalizado.
    
    Para obter mais informações, verifique a [configuração da rede virtual](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Use o portal Azure para configurar uma rede virtual antes de tentar aderir a um IR Azure-SSIS para ele.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas esses navegadores da Web suportam a interface do usuário da Fábrica de Dados.

1. Faça login no [portal Azure](https://portal.azure.com).

1. Selecione **Mais serviços**. Filtre e selecione **Redes virtuais**.

1. Filtre e selecione sua rede virtual na lista.

1. Na página **Rede virtual**, selecione **Propriedades**.

1. Selecione o botão de cópia para que a **ID DO RECURSO** copie a ID do recurso da rede virtual para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo.

1. No menu esquerdo, selecione **Sub-redes**.

    - Certifique-se de que a sub-rede selecionada tenha espaço de endereço disponível suficiente para o IR Azure-SSIS usar. Deixe endereços IP disponíveis por pelo menos duas vezes o número do nó IR. O Azure reserva alguns endereços IP em cada sub-rede. Esses endereços não podem ser usados. Os primeiros e últimos endereços IP das sub-redes são reservados para conformidade com o protocolo, e mais três endereços são usados para serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Não selecione a GatewaySubnet para implantar um IR Azure-SSIS. É dedicado para gateways de rede virtuais.
    - Não use uma sub-rede que seja ocupada exclusivamente por outros serviços do Azure (por exemplo, instância gerenciada do Banco de Dados SQL, Serviço de Aplicativo e assim por diante).

1. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou registre o provedor de lotes Do zure. Se você já tem uma conta do Azure Batch em sua assinatura, sua assinatura está registrada no Azure Batch. (Se você criar o IR do Azure-SSIS no portal do Azure Data Factory,  o provedor do lote do Microsoft Azure é registrado automaticamente para você.)

   1. No portal Azure, no menu à esquerda, **selecione Assinaturas**.

   1. Selecione sua assinatura.

   1. À esquerda, selecione **provedores de**recursos e confirme se **o Microsoft.Batch** é um provedor registrado.

   ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unir o IR do Azure-SSIS a uma rede virtual

Depois de configurar sua rede virtual do Azure Resource Manager ou uma rede virtual clássica, você pode se juntar ao IR Azure-SSIS à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas esses navegadores da Web suportam a interface do usuário da Fábrica de Dados.

1. No [portal Azure](https://portal.azure.com), no menu esquerdo, selecione **fábricas de Dados**. Se você não ver **fábricas de dados** no menu, selecione Mais **serviços**e, em seguida, na seção **INTELIGÊNCIA + ANALYTICS,** selecione **fábricas de dados**.

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione sua fábrica de dados com o IR Azure-SSIS na lista. Você verá a home page do seu data factory. Selecione o **bloco Autor & Monitor.** Você visualiza a interface do usuário do Data Factory em uma guia separada.

   ![Página inicial do data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na interface de usuário do Data Factory, alterne para a guia **Editar**, selecione **Conexões** e alterne para a guia **Runtimes de integração**.

   ![Guia "runtimes de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o IR do Azure-SSIS estiver sendo executado, na lista **'Tempos de execução de integração',** na coluna **Ações',** selecione o botão **Parar** para o seu IR Azure-SSIS. Você não pode editar seu Azure-SSIS IR até pará-lo.

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista **'Tempos de execução de integração',** na coluna **Ações,** selecione o botão **Editar** para o IR Do Azure-SSIS.

   ![Editar o runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de configuração de tempo de execução de integração, avance nas seções **Configurações Gerais** e **Configurações SQL,** selecionando o botão **Seguinte.**

1. Na seção **Configurações Avançadas:**
   1. Selecione o **Select a VNet para o tempo de execução de integração Do Azure-SSIS para participar, permita que o ADF crie certos recursos de rede e, opcionalmente, traga sua própria caixa de seleção de endereços IP públicos estáticos.**

   1. Para ** Assinatura **, selecione a assinatura do Azure que possui sua rede virtual.

   1. Para ** Local **, o mesmo local do seu runtime de integração é selecionado.

   1. Para **Tipo,** selecione o tipo de sua rede virtual: classic ou Azure Resource Manager. Recomendamos que você selecione uma rede virtual do Azure Resource Manager, pois as redes virtuais clássicas serão depreciadas em breve.

   1. Para ** Nome da VNet **, selecione o nome da sua rede virtual. Ele deve ser o mesmo usado para o servidor De banco de dados SQL do Azure com pontos finais de serviço de rede virtual ou instância gerenciada com ponto final privado para hospedar o SSISDB. Ou deve ser o mesmo conectado à sua rede local. Caso contrário, pode ser qualquer rede virtual para trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR.

   1. Para ** Nome da sub-rede **, selecione o nome da sub-rede da sua rede virtual. Ele deve ser o mesmo usado para o servidor De banco de dados SQL do Azure com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para sua instância gerenciada com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR.

   1. Selecione **validação VNet**. Se a validação for bem sucedida, selecione **Continuar**.

   ![Configurações avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na seção **Resumo,** revise todas as configurações do seu IR Azure-SSIS. Em seguida, **selecione Atualizar**.

1. Inicie seu IR Azure-SSIS selecionando o botão **Iniciar** na coluna **Ações** para o SEU IR Azure-SSIS. Leva cerca de 20 a 30 minutos para iniciar o IR Azure-SSIS que se junta a uma rede virtual.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como aderir ao Azure-SSIS IR para uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).
