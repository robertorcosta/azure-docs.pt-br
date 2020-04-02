---
title: Criar, alterar ou excluir um Grupo de Segurança de Rede do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou excluir um Grupo de Segurança de Rede.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 9ed4ce6befda76069e965501a320dc110129a024
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521025"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou excluir um Grupo de Segurança de Rede

As regras de segurança em grupos de segurança de rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e fora das sub-redes da rede virtual e dos adaptadores de rede. Para saber mais sobre grupos de segurança de rede, confira [Visão geral do grupo de segurança de rede](security-overview.md). Em seguida, complete o tutorial [de tráfego de rede Filter](tutorial-filter-network-traffic.md) para obter alguma experiência com grupos de segurança de rede.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se você não tiver uma, configure uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Complete uma dessas tarefas antes de iniciar o restante deste artigo:

- **Usuários do portal**: Faça login no [portal Azure](https://portal.azure.com) com sua conta do Azure.

- **Usuários do PowerShell**: Execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell)ou execute o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Na guia do navegador Azure Cloud Shell, encontre a lista de itens de lista de itens de parada do **ambiente Select** e escolha **o PowerShell** se ele ainda não estiver selecionado.

    Se estiver executando o PowerShell localmente, use a versão 1.0.0 do módulo Azure PowerShell ou posterior. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Execute `Connect-AzAccount` para criar uma conexão com o Azure.

- **Usuários da interface de linha de comando do Azure (CLI):** Execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash)ou execute o CLI a partir do seu computador. Use a versão 2.0.28 do Azure CLI ou posterior se estiver executando o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se você precisar instalar ou atualizar, consulte [Install Azure CLI](/cli/azure/install-azure-cli). Execute `az login` para criar uma conexão com o Azure.

A conta com a qual você faz login ou se conecta ao Azure deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) da Rede ou a uma função [Personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Você pode criar, [exibir todos](#view-all-network-security-groups), [exibir os detalhes](#view-details-of-a-network-security-group), [alterar](#change-a-network-security-group) e [excluir](#delete-a-network-security-group) um Grupo de Segurança de Rede. Você também pode [associar ou desassociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) um Grupo de Segurança de Rede de um adaptador de rede ou sub-rede.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Há um limite para quantos grupos de segurança de rede você pode criar para cada local e assinatura do Azure. Para saber mais, consulte os limites de assinatura e serviço do [Azure, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No menu do [portal Azure](https://portal.azure.com) ou na **página inicial,** selecione **Criar um recurso**.

2. Selecione **Rede**e selecione **Grupo de segurança de rede**.

3. Na página **Criar grupo de segurança de rede,** na guia **Básico,** defina valores para as seguintes configurações:

    | Configuração | Ação |
    | --- | --- |
    | **Assinatura** | Escolha sua assinatura. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente ou selecione **Criar um novo** para criar um novo grupo de recursos. |
    | **Nome** | Digite uma seqüência de texto única dentro de um grupo de recursos. |
    | **Região** | Escolha o local que deseja. |

4. Selecione **Revisão + criar**.

5. Depois de ver a **mensagem aprovada pela Validação,** selecione **Criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Exibir todos os grupos de segurança de rede

Vá ao [portal Azure](https://portal.azure.com) para ver seus grupos de segurança da rede. Procure e selecione **grupos de segurança da rede**. A lista de grupos de segurança da rede aparece para sua assinatura.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg lista](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Exibir os detalhes de um Grupo de Segurança de Rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver seus grupos de segurança da rede. Procure e selecione **grupos de segurança da rede**.

2. Selecione o nome do seu grupo de segurança de rede.

In the menu bar of the network security group, under **Settings**, you can view the **Inbound security rules**, **Outbound security rules**, **Network interfaces**, and **Subnets** that the network security group is associated to.

Em **Monitoramento,** você pode ativar ou desativar **as configurações de diagnóstico**. Em **Suporte + Solução de problemas,** você pode visualizar regras de segurança **eficazes**. Para saber mais, consulte [O registro de diagnóstico para um grupo de segurança de rede](virtual-network-nsg-manage-log.md) e diagnostice um problema de filtro de tráfego de rede [VM](diagnose-network-traffic-filter-problem.md).

Para saber mais sobre as configurações comuns do Azure listadas, veja as seguintes informações:

- [Registro de atividades](../azure-monitor/platform/platform-logs-overview.md)
- [Controle de acesso (IAM)](../role-based-access-control/overview.md)
- [Marcas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Fechaduras](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script de automação](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Criar um Grupo de Segurança de Rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver seus grupos de segurança da rede. Procure e selecione **grupos de segurança da rede**.

2. Selecione o nome do grupo de segurança de rede que deseja alterar.

As mudanças mais comuns são [adicionar uma regra de segurança,](#create-a-security-rule)remover uma [regra](#delete-a-security-rule)e associar [ou dissociar um grupo de segurança de rede para ou a partir de uma sub-rede ou interface de rede](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg atualização](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou desassociar um Grupo de Segurança de Rede de uma sub-rede ou adaptador de rede

Para associar ou desassociar um Grupo de Segurança de Rede de um adaptador de rede, consulte [Associar ou desassociar um Grupo de Segurança de Rede de um adaptador de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar ou desassociar um Grupo de Segurança de Rede de uma sub-rede, consulte [Alterar as configurações de sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Excluir um Grupo de Segurança de Rede

Se um grupo de segurança de rede estiver associado a quaisquer sub-redes ou interfaces de rede, ele não poderá ser excluído. Desassocie um grupo de segurança de rede de todas as sub-redes e adaptadores de rede antes de tentar excluí-lo.

1. Vá ao [portal Azure](https://portal.azure.com) para ver seus grupos de segurança da rede. Procure e selecione **grupos de segurança da rede**.

2. Selecione o nome do grupo de segurança de rede que deseja excluir.

3. Na barra de ferramentas do grupo de segurança de rede, **selecione Excluir**. Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [rede az nsg excluir](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um Grupo de Segurança de Rede contém zero ou mais regras de segurança. Você pode criar, [exibir todas](#view-all-security-rules), [exibir os detalhes](#view-details-of-a-security-rule), [alterar](#change-a-security-rule) e [excluir](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Há um limite para quantas regras por grupo de segurança de rede você pode criar para cada local e assinatura do Azure. Para saber mais, consulte os limites de assinatura e serviço do [Azure, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Vá ao [portal Azure](https://portal.azure.com) para ver seus grupos de segurança da rede. Procure e selecione **grupos de segurança da rede**.

2. Selecione o nome do grupo de segurança de rede ao que deseja adicionar uma regra de segurança.

3. Na barra de menus do grupo de segurança de rede, escolha **as regras de segurança de entrada** ou as regras de segurança de **saída**.

    Várias regras existentes estão listadas, incluindo algumas que você pode não ter adicionado. Quando você cria um grupo de segurança de rede, várias regras de segurança padrão são criadas nele. Para saber mais, consulte [regras de segurança padrão](security-overview.md#default-security-rules).  Não é possível excluir as regras de segurança padrão, porém você pode substituí-las por regras com prioridade mais alta.

4. <a name="security-rule-settings"></a>Selecione **Adicionar**. Selecione ou adicione valores para as seguintes configurações e selecione **OK**:

    | Configuração | Valor | Detalhes |
    | ------- | ----- | ------- |
    | **Fonte** | Um destes:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Tag de serviço** (regra de segurança de entrada) ou **VirtualNetwork** (regra de segurança de saída)</li><li>**Grupo&nbsp;&nbsp;de segurança de aplicativos**</li></ul> | <p>Se você escolher **endereços IP,** você também deve especificar **endereços IP de origem/faixas CIDR**.</p><p>Se você escolher **A tag de serviço,** você também poderá escolher uma **tag de serviço Origem**.</p><p>Se você escolher **o grupo de segurança do aplicativo,** você também deve escolher um grupo de segurança de aplicativo existente. Se você escolher **o grupo de segurança do aplicativo** para **Origem** e **Destino,** as interfaces de rede dentro de ambos os grupos de segurança do aplicativo devem estar na mesma rede virtual.</p> |
    | **Endereços IP de origem/faixas CIDR** | Uma lista delimitada de endereços IP e séries DE ROTEAmento interdomínio sem classe (CIDR) | <p>Essa configuração será exibida se você alterar **a origem** para **endereços IP**. Você deve especificar um único valor ou uma lista separada por comuma de múltiplos valores. Um exemplo de `10.0.0.0/16, 192.188.1.1`múltiplos valores é . Há limites para o número de valores que podem ser especificados. Para obter mais detalhes, consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Se o endereço IP especificado for atribuído a uma VM do Azure, especifique seu endereço IP privado, não seu endereço IP público. O Azure processa as regras de segurança depois de traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, mas antes de traduzir um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, consulte [Tipos de endereços IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Tag de serviço de origem** | Uma tag de serviço da lista de paradas | Esta configuração opcional será exibida se você definir **A marca Origem** para Serviço **para** uma regra de segurança de entrada. Uma marcação de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as tags de serviço disponíveis e o que cada tag representa, consulte [Tags de serviço](security-overview.md#service-tags). |
    | **Grupo de segurança de aplicativos de origem** | Um grupo de segurança de aplicativos existente | Essa configuração será exibida se você definir **'Origem** para **grupo de segurança do aplicativo ''Aplicativo'.** Selecione um grupo de segurança de aplicativo que existe na mesma região que a interface de rede. Saiba como [criar um grupo de segurança do aplicativo](#create-an-application-security-group). |
    | **Intervalos de portas de origem** | Um destes:<ul><li>Uma única porta, como`80`</li><li>Uma série de portas, como`1024-65535`</li><li>Uma lista separada por comuma de portas únicas e/ou intervalos de portas, como`80, 1024-65535`</li><li>Um asterisco`*`( ) para permitir o tráfego em qualquer porta</li></ul> | Esta configuração especifica as portas nas quais a regra permite ou nega o tráfego. Há limites para o número de portas que podem ser especificadas. Para obter mais detalhes, consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Destino** | Um destes:<ul><li>**Qualquer**</li><li>**Endereços IP**</li><li>**Tag de serviço** (regra de segurança de saída) ou **VirtualNetwork** (regra de segurança de entrada)</li><li>**Grupo&nbsp;&nbsp;de segurança de aplicativos**</li></ul> | <p>Se você escolher **endereços IP,** então também especifique **os endereços IP de destino/faixas CIDR**.</p><p>Se você escolher **VirtualNetwork,** o tráfego será permitido em todos os endereços IP dentro do espaço de endereços da rede virtual. **VirtualNetwork** é uma tag de serviço.</p><p>Se você selecionar **o grupo de segurança do aplicativo,** você deve selecionar um grupo de segurança de aplicativo existente. Saiba como [criar um grupo de segurança do aplicativo](#create-an-application-security-group).</p> |
    | **Endereços IP de destino/faixas CIDR** | Uma lista delimitada de vírgulas de endereços IP e faixas CIDR | <p>Essa configuração será exibida se você alterar **destino** para **endereços IP**. Semelhante aos endereços IP de **origem** e **fonte/faixas CIDR,** você pode especificar endereços ou intervalos únicos ou múltiplos. Há limites para o número que você pode especificar. Para obter mais detalhes, consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Se o endereço IP especificado for atribuído a uma VM do Azure, certifique-se de especificar seu IP privado, não seu endereço IP público. O Azure processa as regras de segurança depois de traduzir o endereço IP público para um endereço IP privado para regras de segurança de entrada, mas antes que o Azure traduza um endereço IP privado para um endereço IP público para regras de saída. Para saber mais sobre endereços IP públicos e privados no Azure, consulte [Tipos de endereços IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Marca de serviço de destino** | Uma tag de serviço da lista de paradas | Esta configuração opcional será exibida se você alterar **destino** para **tag de serviço** para uma regra de segurança de saída. Uma marcação de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre as tags de serviço disponíveis e o que cada tag representa, consulte [Tags de serviço](security-overview.md#service-tags). |
    | **Grupo de segurança de aplicativos de destino** | Um grupo de segurança de aplicativos existente | Essa configuração será exibida se você definir **Destino** para **Grupo de segurança do aplicativo**. Selecione um grupo de segurança de aplicativo que existe na mesma região que a interface de rede. Saiba como [criar um grupo de segurança do aplicativo](#create-an-application-security-group). |
    | **Faixas portuárias de destino** | Um destes:<ul><li>Uma única porta, como`80`</li><li>Uma série de portas, como`1024-65535`</li><li>Uma lista separada por comuma de portas únicas e/ou intervalos de portas, como`80, 1024-65535`</li><li>Um asterisco`*`( ) para permitir o tráfego em qualquer porta</li></ul> | Como nas **faixas de porta De origem,** você pode especificar portas e intervalos únicos ou múltiplos. Há limites para o número que você pode especificar. Para obter mais detalhes, consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protocolo** | **Qualquer**, **TCP**, **UDP,** ou **ICMP** | Você pode restringir a regra ao Protocolo de Controle de Transmissão (TCP), ao Protocolo de Datagrama do Usuário (UDP) ou ao Protocolo de Mensagem de Controle da Internet (ICMP). O padrão é que a regra se aplique a todos os protocolos. |
    | **Ação** | **Permitir** ou **Negar** | Esta configuração especifica se esta regra permite ou nega o acesso à configuração de origem e destino fornecida. |
    | **Priority** | Um valor entre 100 e 4096 que é único para todas as regras de segurança dentro do grupo de segurança da rede | O Azure processa regras de segurança em ordem prioritária. Quanto menor o número, maior a prioridade. Recomendamos que você deixe uma lacuna entre números prioritários quando criar regras, como 100, 200 e 300. Deixar lacunas facilita a adição de regras no futuro, para que você possa dar-lhes prioridade maior ou menor do que as regras existentes. |
    | **Nome** | Um nome único para a regra dentro do grupo de segurança da rede | O nome pode ter até 80 caracteres. Ele deve começar com uma letra ou número, e deve terminar com uma letra, número ou sublinhado. O nome pode conter apenas letras, números, sublinhados, períodos ou hífens. |
    | **Descrição** | Uma descrição de texto | Você pode especificar opcionalmente uma descrição de texto para a regra de segurança. |

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Exibir todas as regras de segurança

Um grupo de segurança de rede contém zero ou mais regras. Para saber mais sobre as informações listadas ao exibir regras, consulte a [Visão geral de Grupo de Segurança de Rede](security-overview.md).

1. Vá ao [portal Azure](https://portal.azure.com) para ver as regras de um grupo de segurança de rede. Procure e selecione **grupos de segurança da rede**.

2. Selecione o nome do grupo de segurança da rede para o que deseja visualizar as regras.

3. Na barra de menus do grupo de segurança de rede, escolha **as regras de segurança de entrada** ou as regras de segurança de **saída**.

A lista contém todas as regras criadas e as [regras de segurança padrão](security-overview.md#default-security-rules)do grupo de segurança da rede .

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Exibir os detalhes de uma regra de segurança

1. Vá ao [portal Azure](https://portal.azure.com) para ver as regras de um grupo de segurança de rede. Procure e selecione **grupos de segurança da rede**.

2. Selecione o nome do grupo de segurança da rede para o que deseja visualizar os detalhes de uma regra.

3. Na barra de menus do grupo de segurança de rede, escolha **as regras de segurança de entrada** ou as regras de segurança de **saída**.

4. Selecione a regra cujos detalhes você deseja exibir. Para obter uma explicação de todas as configurações, consulte [Configurações de regra](#security-rule-settings)de segurança .

    > [!NOTE]
    > Este procedimento só se aplica a uma regra de segurança personalizada. Não funciona se você escolher uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg regra mostrar](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Altere as configurações conforme necessário e selecione **Salvar**. Para obter uma explicação de todas as configurações, consulte [Configurações de regra](#security-rule-settings)de segurança .

    > [!NOTE]
    > Este procedimento só se aplica a uma regra de segurança personalizada. Você não tem permissão para mudar uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Excluir uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).

2. Selecione **Excluir** e, em seguida, selecione **Sim**.

    > [!NOTE]
    > Este procedimento só se aplica a uma regra de segurança personalizada. Você não tem permissão para excluir uma regra de segurança padrão.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nsg regra excluir](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicativo

Um grupo de segurança de aplicativo contém zero ou mais adaptadores de rede. Para saber mais, consulte [grupos de segurança de aplicativo](security-overview.md#application-security-groups). Todos os adaptadores de rede em um grupo de segurança do aplicativo precisam existir na mesma rede virtual. Para saber como adicionar um adaptador de rede a um grupo de segurança de aplicativo, consulte [Adicionar um adaptador de rede a um grupo de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança de aplicativo

1. No menu do [portal Azure](https://portal.azure.com) ou na **página inicial,** selecione **Criar um recurso**.

2. Na caixa de pesquisa, *insira grupo de segurança de aplicativos*.

3. Na página do **grupo de segurança do aplicativo,** selecione **Criar**.

4. Na **página Criar um grupo de segurança de aplicativo,** na guia **Basics,** defina valores para as seguintes configurações:

    | Configuração | Ação |
    | --- | --- |
    | **Assinatura** | Escolha sua assinatura. |
    | **Grupo de recursos** | Escolha um grupo de recursos existente ou selecione **Criar um novo** para criar um novo grupo de recursos. |
    | **Nome** | Digite uma seqüência de texto única dentro de um grupo de recursos. |
    | **Região** | Escolha o local que deseja. |

5. Selecione **Revisão + criar**.

6. Na guia **'''Revisar + criar',** depois de ver a mensagem **aprovada pela Validação,** selecione **Criar**.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg criar](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Exibir todos os grupos de segurança de aplicativo

Vá ao [portal Azure](https://portal.azure.com) para ver seus grupos de segurança de aplicativos. Procure e selecione **grupos de segurança do aplicativo**. O portal Azure exibe uma lista de seus grupos de segurança de aplicativos.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg lista](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Exibir detalhes de um grupo de segurança de aplicativo específico

1. Vá ao [portal Azure](https://portal.azure.com) para ver um grupo de segurança de aplicativos. Procure e selecione **grupos de segurança do aplicativo**.

2. Selecione o nome do grupo de segurança do aplicativo do que deseja visualizar os detalhes.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicativo

1. Vá ao [portal Azure](https://portal.azure.com) para ver um grupo de segurança de aplicativos. Procure e selecione **grupos de segurança do aplicativo**.

2. Selecione o nome do grupo de segurança do aplicativo que deseja alterar.

3. Selecione **alterar** ao lado da configuração que deseja modificar. Por exemplo, você pode adicionar ou remover **Tags,** ou pode alterar o **grupo Recurso** ou **Assinatura**.

    > [!NOTE]
    > Você não pode mudar a localização.

    Na barra de menu, você também pode selecionar **controle de acesso (IAM)**. Na página **de controle de acesso (IAM),** você pode atribuir ou remover permissões para o grupo de segurança do aplicativo.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg atualização](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Sem cmdlet PowerShell |

### <a name="delete-an-application-security-group"></a>Excluir um grupo de segurança de aplicativo

Você não pode excluir um grupo de segurança de aplicativo se ele contiver quaisquer interfaces de rede. Para remover todas as interfaces de rede do grupo de segurança do aplicativo, altere as configurações da interface de rede ou exclua as interfaces de rede. Para saber mais, consulte [Adicionar ou remover de grupos de segurança de aplicativos](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou Excluir uma interface de [rede](virtual-network-network-interface.md#delete-a-network-interface).

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar seus grupos de segurança de aplicativos. Procure e selecione **grupos de segurança do aplicativo**.

2. Selecione o nome do grupo de segurança do aplicativo que deseja excluir.

3. Selecione **Excluir** e, em seguida, selecione **Sim** para excluir o grupo de segurança do aplicativo.

#### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede asg excluir](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Permissões

Para fazer tarefas em grupos de segurança de rede, regras de segurança e grupos de segurança de aplicativos, sua conta deve ser atribuída à função [de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) da rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que atribuias as permissões apropriadas conforme listado nas tabelas a seguir:

### <a name="network-security-group"></a>Grupo de segurança de rede

| Ação                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Obter Grupo de Segurança de Rede                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Criar ou atualizar um Grupo de Segurança de Rede                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Excluir Grupo de Segurança de Rede                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associar um grupo de segurança de rede a uma interface de sub-rede ou rede 

### <a name="network-security-group-rule"></a>Regra do grupo de segurança de rede

| Ação                                                        |   Nome                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Obter regra                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Criar ou atualizar regra                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Excluir regra                                                         |

### <a name="application-security-group"></a>Grupo de segurança do aplicativo

| Ação                                                                     | Nome                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Entrar uma configuração IP a um grupo de segurança do aplicativo|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Entrar em uma regra de segurança aos grupos de segurança de aplicativo    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obter um grupo de segurança de aplicativo                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicativo           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Excluir um grupo de segurança de aplicativo                     |

## <a name="next-steps"></a>Próximas etapas

- Crie um grupo de segurança de rede ou aplicativo usando scripts de exemplo [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md) ou [modelos](template-samples.md) do Azure Resource Manager
- Criar e aplicar a [Política do Azure](policy-samples.md) para redes virtuais
