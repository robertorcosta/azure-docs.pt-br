---
title: Grupos IP no Firewall Azure
description: Os grupos IP permitem que você agrupe e gerencie endereços IP para as regras do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444479"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Grupos IP (visualização) no Firewall Do Azure

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Os Grupos IP permitem que você agrupe e gerencie endereços IP para as regras do Firewall do Azure das seguintes maneiras:

- Como endereço de origem nas regras do DNAT
- Como uma fonte ou endereço de destino nas regras da rede
- Como um endereço de origem nas regras do aplicativo


Um grupo IP pode ter um único endereço IP, vários endereços IP ou uma ou mais faixas de endereçoIP.

Os Grupos IP podem ser reutilizados no DNAT do Firewall Do Azure, nas regras de rede e aplicativos para vários firewalls em regiões e assinaturas no Azure. Os nomes de grupo devem ser únicos. Você pode configurar um Grupo IP no portal Azure, Azure CLI ou Rest API. Um modelo de exemplo é fornecido para ajudá-lo a começar.

## <a name="sample-format"></a>Formato de exemplo

Os seguintes exemplos de formato de endereço IPv4 são válidos para uso em Grupos IP:

- Endereço único: 10.0.0.0
- Notação CIDR: 10.1.0.0/32
- Faixa de endereço: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Criar um grupo IP

Um Grupo IP pode ser criado usando o portal Azure, Azure CLI ou Rest API. Para obter mais informações, consulte [Criar um grupo IP (visualização)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Procurar grupos IP
1. Na barra de pesquisa do portal Azure, digite **Grupos IP** e selecione-o. Você pode ver a lista dos grupos IP ou selecionar **Adicionar** para criar um novo grupo IP.
2. Selecione um grupo IP para abrir a página de visão geral. Você pode editar, adicionar ou excluir endereços IP ou Grupos IP.

   ![Visão geral dos grupos IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gerencie um grupo IP

Você pode ver todos os endereços IP no Grupo IP e as regras ou recursos associados a ele. Para excluir um grupo IP, você deve primeiro dissociar o Grupo IP do recurso que o está usando.

1. Para exibir ou editar os endereços IP, selecione **Endereços IP** em **Configurações** no painel esquerdo.
2. Para adicionar um endereço IP único ou múltiplo(es), selecione **Adicionar endereços IP**. Isso abre a página **Arrastar ou Procurar** para um upload, ou você pode inserir o endereço manualmente.
3.  Selecionando as elipses (**...**) à direita de editar ou excluir endereços IP. Para editar ou excluir vários endereços IP, selecione as caixas e selecione **Editar** ou **Excluir** na parte superior.
4. Finalmente, pode exportar o arquivo no formato de arquivo CSV.

> [!NOTE]
> Se você excluir todos os endereços IP em um Grupo IP enquanto ele ainda estiver em uso em uma regra, essa regra será ignorada.


## <a name="use-an-ip-group"></a>Use um grupo IP

Agora, você pode selecionar **o Grupo IP** como um tipo de **origem** ou tipo **de destino** para o endereço IP(es) quando criar o DNAT, aplicativo ou regras de rede do Firewall Do Azure.

> [!NOTE]
> Os grupos IP não são suportados na Diretiva de Firewall. Atualmente, ele só é suportado com regras tradicionais de firewall.

![Grupos IP em Firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Disponibilidade de região

Os grupos IP estão atualmente disponíveis nas seguintes regiões:

- Oeste dos EUA
- Oeste dos EUA 2
- Leste dos EUA
- Leste dos EUA 2
- Centro dos EUA
- Centro-Norte dos EUA
- Centro-Oeste dos EUA
- Centro-Sul dos Estados Unidos
- Canadá Central
- Norte da Europa
- Europa Ocidental
- França Central
- Sul do Reino Unido
- Leste da Austrália
- Austrália Central
- Sudeste da Austrália

## <a name="related-azure-powershell-cmdlets"></a>Cmdlets Azure PowerShell relacionados

Os seguintes cmdlets Do Azure PowerShell podem ser usados para criar e gerenciar grupos IP:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).