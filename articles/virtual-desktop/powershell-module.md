---
title: Módulo do PowerShell área de trabalho virtual do Windows-Azure
description: Como instalar e configurar o módulo do PowerShell para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 54501e7e00ba8a28dd7cb421232b9a9587604338
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653128"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Configurar o módulo do PowerShell para área de trabalho virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows.
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O módulo do PowerShell da área de trabalho virtual do Windows é integrado ao módulo Azure PowerShell. Este artigo lhe dirá como configurar o módulo do PowerShell para que você possa executar cmdlets para a área de trabalho virtual do Windows.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

Para começar a usar o módulo, primeiro instale a [versão mais recente do PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). Os cmdlets da área de trabalho virtual do Windows atualmente funcionam com o PowerShell Core.

Em seguida, você precisará instalar o módulo DesktopVirtualization para usar em sua sessão do PowerShell.

Execute o seguinte cmdlet do PowerShell no modo elevado para instalar o módulo:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Se esse cmdlet não funcionar, tente executá-lo novamente com permissões elevadas.

Em seguida, execute o seguinte cmdlet para se conectar ao Azure:

```powershell
Connect-AzAccount
```

Entrar em sua conta do Azure requer um código que é gerado quando você executa o cmdlet Connect. Para entrar, vá para <https://microsoft.com/devicelogin>, insira o código e, em seguida, entre usando suas credenciais de administrador do Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Isso conectará você diretamente à assinatura que é padrão para suas credenciais de administrador.

## <a name="change-the-default-subscription"></a>Alterar a assinatura padrão

Se você quiser alterar a assinatura padrão depois de entrar, execute este cmdlet:

```powershell
Select-AzureSubscription -SubscriptionName <preferredsubscriptionname>
```

Quando você seleciona uma nova assinatura a ser usada, não é necessário especificar a ID da assinatura nos cmdlets executados posteriormente. Por exemplo, o seguinte cmdlet recupera um host de sessão específico sem precisar da ID de assinatura:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Você também pode alterar as assinaturas de acordo com cada cmdlet adicionando o nome de assinatura desejado como um parâmetro. O próximo cmdlet é o mesmo que o exemplo anterior, exceto com a ID de assinatura adicionada como um parâmetro para alterar a assinatura que o cmdlet usa.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Obter locais

O parâmetro Location é obrigatório para todos os cmdlets **New-AzWVD** que criam novos objetos.

Execute o seguinte cmdlet para obter uma lista de locais aos quais sua assinatura dá suporte:

```powershell
Get-AzLocation
```

A saída para **Get-AzLocation** terá a seguinte aparência:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Depois de saber o local da sua conta, você pode usá-la em um cmdlet. Por exemplo, aqui está um cmdlet que cria um pool de hosts no local "southeastasia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o módulo do PowerShell, pode executar cmdlets para fazer todos os tipos de coisas na área de trabalho virtual do Windows. Aqui estão alguns dos locais em que você pode usar seu módulo:

- Execute os [tutoriais de área de trabalho virtual do Windows]() para configurar seu próprio ambiente de área de trabalho virtual do Windows.
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)
- [Configurar o método de balanceamento de carga da Área de Trabalho Virtual do Windows](configure-host-pool-load-balancing.md)
- [Configurar o tipo de atribuição de pool de hosts de área de trabalho pessoal](configure-host-pool-personal-desktop-assignment-type.md)
- E muito mais!

Se você tiver problemas, confira nosso artigo de [solução de problemas do PowerShell](troubleshoot-powershell.md) para obter ajuda.

