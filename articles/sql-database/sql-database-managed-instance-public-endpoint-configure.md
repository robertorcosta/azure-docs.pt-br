---
title: Configurar ponto final público - instância gerenciada
description: Saiba como configurar um ponto final público para instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256153"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurar pontos de extremidade públicos na instância gerenciada no Banco de Dados SQL do Azure

O ponto final público para uma [instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) permite o acesso de dados à sua instância gerenciada de fora da [rede virtual](../virtual-network/virtual-networks-overview.md). Você pode acessar sua instância gerenciada a partir de serviços azure de vários inquilinos, como Power BI, Azure App Service ou uma rede local. Ao usar o ponto final público em uma instância gerenciada, você não precisa usar uma VPN, o que pode ajudar a evitar problemas de throughput de VPN.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> - Habilite o ponto final público para sua instância gerenciada no portal Azure
> - Habilite o ponto final público para sua instância gerenciada usando o PowerShell
> - Configure o grupo de segurança de rede de instância gerenciada para permitir o tráfego no ponto final público da instância gerenciada
> - Obter a seqüência de conexão de ponto final público de instância gerenciada

## <a name="permissions"></a>Permissões

Devido à sensibilidade dos dados que estão em uma instância gerenciada, a configuração para habilitar o ponto final público de instância gerenciada requer um processo de duas etapas. Esta medida de segurança adere à separação de direitos (SoD):

- A habilitação do ponto final público em uma instância gerenciada precisa ser feita pelo administrado por instância gerenciada. O admin de instância gerenciada pode ser encontrado na página **Visão geral** do recurso de instância gerenciada SQL.
- Permitindo o tráfego usando um grupo de segurança de rede que precisa ser feito por um admin de rede. Para obter mais informações, consulte [as permissões do grupo de segurança da rede](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Habilitando o ponto final público para uma instância gerenciada no portal Azure

1. Inicie o portal Azure em<https://portal.azure.com/.>
1. Abra o grupo de recursos com a instância gerenciada e selecione a **instância gerenciada sql** que você deseja configurar ponto final público.
1. Nas configurações **de Segurança,** selecione a guia **Rede Virtual.**
1. Na página de configuração de rede Virtual, **selecione Ativar** e, em seguida, o ícone **Salvar** para atualizar a configuração.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Habilitando o ponto final público para uma instância gerenciada usando o PowerShell

### <a name="enable-public-endpoint"></a>Habilitar o ponto de extremidade público

Execute os comandos do PowerShell a seguir. Substitua **o id de assinatura** pelo seu ID de assinatura. Substitua também o **nome rg** pelo grupo de recursos para sua instância gerenciada e substitua o nome **mi com** o nome da sua instância gerenciada.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Desativar o ponto final público

Para desativar o ponto final público usando o PowerShell, você executaria o seguinte comando (e também não se esqueça de fechar o NSG para a porta de entrada 3342 se você tiver configurado):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Permitir tráfego de ponto final público no grupo de segurança da rede

1. Se você tiver a página de configuração da instância gerenciada ainda aberta, navegue até a guia **Visão geral.** Caso contrário, volte para o recurso **de instância gerenciada sql.** Selecione o link **Rede Virtual/sub-rede,** que o levará à página de configuração de rede virtual.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Selecione a guia **Subnets** no painel de configuração esquerda da rede Virtual e anote o GRUPO DE **SEGURANÇA** para sua instância gerenciada.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Volte para o seu grupo de recursos que contém sua instância gerenciada. Você deve ver o nome do **grupo de segurança da Rede** anotado acima. Selecione o nome para entrar na página de configuração do grupo de segurança da rede.

1. Selecione a guia **'Inbound's security rules'** e **adicione** uma regra com prioridade superior à **regra deny_all_inbound** com as seguintes configurações: </br> </br>

    |Configuração  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    |**Fonte**     |Qualquer endereço IP ou tag de serviço         |<ul><li>Para serviços do Azure como Power BI, selecione a tag de serviço em nuvem do Azure</li> <li>Para o seu computador ou Azure VM, use o endereço IP NAT</li></ul> |
    |**Intervalos de portas de origem**     |*         |Deixe isso para * (qualquer) como as portas de origem são geralmente dinamicamente alocadas e, como tal, imprevisíveis |
    |**Destino**     |Qualquer         |Deixando o destino como Qualquer para permitir o tráfego na sub-rede de instância gerenciada |
    |**Faixas portuárias de destino**     |3342         |Porta de destino de escopo para 3342, que é o ponto final público tds de instância gerenciada |
    |**Protocolo**     |TCP         |A instância gerenciada usa o protocolo TCP para TDS |
    |**Ação**     |Allow         |Permitir que o tráfego de entrada gerencie a instância através do ponto final público |
    |**Priority**     |1300         |Certifique-se de que esta regra é de maior prioridade do que a regra **deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > A porta 3342 é usada para conexões de ponto final públicos para instância gerenciada, e não pode ser alterada neste momento.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Obtenção da seqüência de conexão de ponto final público de instância gerenciada

1. Navegue até a página de configuração de instância gerenciada SQL habilitada para o ponto final público. Selecione a guia **Conexão strings** na configuração **Configurações.**
1. Observe que o nome de host de ponto final público vem no formato <mi_name>. **público**.<dns_zone>.database.windows.net e que a porta usada para a conexão é 3342.

    ![mi-público-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [usar a instância gerenciada do Azure SQL Database com segurança com ponto final público](sql-database-managed-instance-public-endpoint-securely.md).