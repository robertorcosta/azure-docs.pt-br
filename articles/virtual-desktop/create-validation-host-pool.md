---
title: Atualizações de serviço de pool de hosts da Área de Trabalho Virtual do Windows – Azure
description: Como criar um pool de host de validação para monitorar atualizações de serviço antes de distribuir atualizações para a produção.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8eac40ad958a10b8c853304ee2be8b2dc27af1a2
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008705"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Criar um pool de hosts para validar as atualizações de serviço

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Antes de implantar pools de host para seu ambiente de produção, é altamente recomendável criar um pool de host de validação. As atualizações são aplicadas primeiro para pools de host de validação, permitindo que você monitore atualizações de serviço antes de implantá-las em seu ambiente de produção. Sem um pool de host de validação, você pode não detectar alterações que apresentam erros, o que pode resultar em tempo de inatividade para os usuários em seu ambiente de produção.

Para garantir que seus aplicativos funcionem com as atualizações mais recentes, o pool de host de validação deve ser o mais semelhante possível aos pools de host em seu ambiente de produção. Os usuários devem se conectar com frequência ao pool de host de validação como fazem com o pool de host de produção. Se você tiver testes automatizados em seu pool de host, deverá incluí-los no pool de host de validação.

Você pode depurar problemas no pool de host de validação com o [recurso de diagnóstico](diagnostics-role-service.md) ou com os [artigos de para solucionar problemas da Área de Trabalho Virtual do Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> É recomendável deixar o pool de host de validação em vigor para testar todas as atualizações futuras.

>[!IMPORTANT]
>Atualmente, a Área de Trabalho Virtual do Windows com integração ao Gerenciamento de Recursos do Azure tem problemas para habilitar e desabilitar ambientes de validação. Atualizaremos este artigo quando resolvermos o problema.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Configurar o módulo do PowerShell da Área de Trabalho Virtual do Windows](powershell-module.md) para configurar o módulo do PowerShell e entrar no Azure.

## <a name="create-your-host-pool"></a>Criar seu pool de host

Você pode criar um pool de host seguindo as instruções em qualquer um dos seguintes artigos:
- [Tutorial: Criar um pool de host com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definir seu pool de host como um pool de host de validação

Execute os seguintes cmdlets do PowerShell para definir o novo pool de host como um pool de host de validação. Substitua os valores entre colchetes pelos valores relevantes para sua sessão:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Execute o seguinte cmdlet do PowerShell para confirmar que a propriedade de validação foi definida. Substitua os valores entre colchetes pelos valores relevantes para sua sessão.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Os resultados do cmdlet devem ser semelhantes a esta saída:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Agenda de atualização

As atualizações de serviço acontecem mensalmente. Se houver grandes problemas, atualizações críticas serão fornecidas a um ritmo mais frequente.

Se houver atualizações de serviço, você deverá ter pelo menos um pequeno grupo de usuários entrando a cada dia para validar o ambiente. Recomendamos que você visite regularmente nosso site [TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) e siga as postagens com o WVDUPdate para se manter informado sobre as atualizações de serviço.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um pool de hosts de validação, você pode aprender a usar a Integridade do Serviço do Azure para monitorar a implantação de Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](./set-up-service-alerts.md)
