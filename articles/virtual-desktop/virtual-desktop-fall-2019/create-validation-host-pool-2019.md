---
title: Atualizações de serviço do pool de host da Área de Trabalho Virtual do Windows (clássica) – Azure
description: Como criar um pool de host de validação para monitorar atualizações de serviço antes de distribuir atualizações para a produção.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f5a8d925a9875187358dc96b1770916974d1c6bd
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007124"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>Tutorial: Criar um pool de host para validar atualizações de serviço no Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../create-validation-host-pool.md).

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Antes de implantar pools de host para seu ambiente de produção, é altamente recomendável criar um pool de host de validação. As atualizações são aplicadas primeiro para pools de host de validação, permitindo que você monitore atualizações de serviço antes de implantá-las em seu ambiente de produção. Sem um pool de host de validação, você pode não detectar alterações que apresentam erros, o que pode resultar em tempo de inatividade para os usuários em seu ambiente de produção.

Para garantir que seus aplicativos funcionem com as atualizações mais recentes, o pool de host de validação deve ser o mais semelhante possível aos pools de host em seu ambiente de produção. Os usuários devem se conectar com frequência ao pool de host de validação como fazem com o pool de host de produção. Se você tiver testes automatizados em seu pool de host, deverá incluí-los no pool de host de validação.

Você pode depurar problemas no pool de host de validação com o [recurso de diagnóstico](diagnostics-role-service-2019.md) ou com os [artigos de para solucionar problemas da Área de Trabalho Virtual do Windows](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> É recomendável deixar o pool de host de validação em vigor para testar todas as atualizações futuras.

Antes de começar, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/), caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Criar seu pool de host

Você pode criar um pool de host seguindo as instruções em qualquer um dos seguintes artigos:
- [Tutorial: Criar um pool de host com o Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Criar um pool de host com um modelo do Azure Resource Manager](create-host-pools-arm-template.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definir seu pool de host como um pool de host de validação

Execute os seguintes cmdlets do PowerShell para definir o novo pool de host como um pool de host de validação. Substitua os valores entre aspas pelos valores relevantes à sua sessão:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Execute o seguinte cmdlet do PowerShell para confirmar que a propriedade de validação foi definida. Substitua os valores entre aspas pelos valores relevantes para sua sessão.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Os resultados do cmdlet devem ser semelhantes a esta saída:

```
    TenantName          : contoso
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Agenda de atualização

As atualizações de serviço acontecem mensalmente. Se houver grandes problemas, atualizações críticas serão fornecidas a um ritmo mais frequente.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um pool de hosts de validação, você pode aprender a usar a Integridade do Serviço do Azure para monitorar a implantação de Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](set-up-service-alerts-2019.md)
