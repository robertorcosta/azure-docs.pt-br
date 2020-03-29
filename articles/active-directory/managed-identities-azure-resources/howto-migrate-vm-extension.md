---
title: Pare de usar extensão VM de identidade gerenciada - Azure AD
description: Instruções passo a passo para parar de usar a extensão VM e começar a usar o IMDS (Azure Instance Metadata Service, serviço de metadados de instância do Azure) para autenticação.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049060"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Como parar de usar a extensão de identidades gerenciadas pela máquina virtual e começar a usar o Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensão de máquina virtual para identidades gerenciadas

A extensão da máquina virtual para identidades gerenciadas é usada para solicitar tokens para uma identidade gerenciada dentro da máquina virtual. O fluxo de trabalho consiste nas seguintes etapas:

1. Primeiro, a carga de trabalho dentro `http://localhost/oauth2/token` do recurso chama o ponto final local para solicitar um token de acesso.
2. A extensão da máquina virtual usa então as credenciais para a identidade gerenciada, para solicitar um token de acesso do Azure AD.. 
3. O token de acesso é devolvido ao chamador e pode ser usado para autenticar serviços que suportam a autenticação Azure AD, como o Azure Key Vault ou o Azure Storage.

Devido a várias limitações descritas na próxima seção, a extensão VM de identidade gerenciada foi preterida em favor do uso do ponto final equivalente no Azure Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>Provisão da prorrogação 

Quando você configura uma máquina virtual ou uma escala de máquina virtual definida para ter uma identidade gerenciada, você pode `-Type` optar opcionalmente por provisionar as identidades gerenciadas para a extensão VM de recursos do Azure usando o parâmetro no cmdlet [Set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Você pode `ManagedIdentityExtensionForWindows` passar `ManagedIdentityExtensionForLinux`ou, dependendo do tipo de máquina `-Name` virtual, e nomeá-la usando o parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Você também pode usar o modelo de implantação do Azure Resource Manager para `resources` provisionar a extensão VM, adicionando o JSON a seguir à seção ao modelo (use `ManagedIdentityExtensionForLinux` para o nome e elementos de tipo para a versão Linux).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Se você estiver trabalhando com conjuntos de escala de máquinavirtual, você também pode provisionar as identidades gerenciadas para a extensão do conjunto de escala de máquina virtual do Azure usando o cmdlet [Add-AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) Você pode `ManagedIdentityExtensionForWindows` passar `ManagedIdentityExtensionForLinux`ou, dependendo do tipo de conjunto de `-Name` escala da máquina virtual, e nomeá-lo usando o parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Para provisionar a extensão de conjunto de escala de máquina virtual com `extensionpProfile` o modelo de `ManagedIdentityExtensionForLinux` implantação do Azure Resource Manager, adicione o JSON a seguir à seção ao modelo (use para o nome e os elementos de tipo para a versão Linux).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

O provisionamento da extensão da máquina virtual pode falhar devido a falhas na procurar DNS. Se isso acontecer, reinicie a máquina virtual e tente novamente. 

### <a name="remove-the-extension"></a>Remover a extensão 
Para remover a `-n ManagedIdentityExtensionForWindows` extensão, use `-n ManagedIdentityExtensionForLinux` ou alterne (dependendo do tipo de máquina virtual) com exclusão de [extensão az vm,](https://docs.microsoft.com/cli/azure/vm/)ou [exclusão de extensão az vmss](https://docs.microsoft.com/cli/azure/vmss) para conjuntos de escala de máquina virtual usando a Cli do Azure, ou `Remove-AzVMExtension` para o Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Adquira um token usando a extensão da máquina virtual

A seguir está uma solicitação de amostra usando as identidades gerenciadas para recursos do Azure VM Extension Endpoint:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | As identidades gerenciadas do ponto de extremidade de recursos do Azure, em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração `aud` (público) do token emitido. Este exemplo solicita um token para acessar o Azure Resource Manager, que tem um URI de ID do aplicativo de `https://management.azure.com/`. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido por identidades gerenciadas para recursos do Azure como uma atenuação contra ataque SSRF (Server Side Request Forgery). Esse valor deve ser definido como "true", com todas as letras minúsculas.|
| `object_id` | (Opcional) Um parâmetro de string de consulta, indicando o object_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|
| `client_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando o client_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|


Exemplo de resposta:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrição |
| ------- | ----------- |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `refresh_token` | Não usado por identidades gerenciadas para recursos do Azure. |
| `expires_in` | O número de segundos que o token de acesso continua sendo válido antes da expiração desde o momento da emissão. A hora da emissão pode ser encontrada na declaração `iat` do token. |
| `expires_on` | O período de expiração do token de acesso. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `exp` do token). |
| `not_before` | O período para o token de acesso entrar em vigor e poder ser aceito. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode conceder acesso ao portador desse token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Solucionar problemas na extensão da máquina virtual 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Reinicie a extensão da máquina virtual após uma falha

No Windows e em determinadas versões do Linux, se a extensão for interrompida, o cmdlet a seguir poderá ser usado para reiniciá-lo:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- O nome e o tipo de extensão para Windows são:`ManagedIdentityExtensionForWindows`
- Nome de extensão e tipo para Linux é:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar esquemas para identidades gerenciadas para a extensão de recursos do Azure

Quando as identidades gerenciadas para recursos do Azure são ativadas em uma máquina virtual, o erro a seguir é mostrado ao tentar usar o recurso "Script de automação" para a máquina virtual ou seu grupo de recursos:

![Identidades gerenciadas para erro de exportação de script de automação de recursos do Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

As identidades gerenciadas para a extensão de máquina virtual de recursos do Azure não suportam atualmente a capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar identidades gerenciadas para recursos do Azure no recurso. Essas seções podem ser adicionadas manualmente seguindo os exemplos em [Configure identidades gerenciadas para recursos do Azure em uma máquina virtual Do Azure usando um modelo](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema saturar as identidades gerenciadas para a extensão da máquina virtual de recursos do Azure (prevista para depreciação em janeiro de 2019), ela será listada em [Grupos de Recursos Exportadores que contenham extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitações da extensão da máquina virtual 

Existem várias limitações importantes para o uso da extensão da máquina virtual. 

 * A limitação mais séria é o fato de que as credenciais usadas para solicitar tokens são armazenadas na máquina virtual. Um invasor que viola com sucesso a máquina virtual pode exfiltrar as credenciais. 
 * Além disso, a extensão da máquina virtual ainda não é suportada por várias distribuições Linux, com um enorme custo de desenvolvimento para modificar, construir e testar a extensão em cada uma dessas distribuições. Atualmente, apenas as seguintes distribuições Linux são suportadas: 
    * CoreOS Estável
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Há um impacto de desempenho na implantação de máquinas virtuais com identidades gerenciadas, já que a extensão da máquina virtual também precisa ser provisionada. 
 * Finalmente, a extensão da máquina virtual só pode suportar ter 32 identidades gerenciadas pelo usuário por máquina virtual. 

## <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

O [IMDS (Azure Instance Metadata Service, serviço de metadados de instâncias do Azure)](/azure/virtual-machines/windows/instance-metadata-service) é um ponto final REST que fornece informações sobre a execução de instâncias de máquinas virtuais que podem ser usadas para gerenciar e configurar suas máquinas virtuais. O ponto final está disponível em um endereço IP`169.254.169.254`não routable bem conhecido ( ) que pode ser acessado apenas a partir de dentro da máquina virtual.

Existem várias vantagens em usar o Azure IMDS para solicitar tokens. 

1. O serviço é externo à máquina virtual, portanto, as credenciais usadas pelas identidades gerenciadas não estão mais presentes na máquina virtual. Em vez disso, eles são hospedados e protegidos na máquina hospedeira da máquina virtual Azure.   
2. Todos os sistemas operacionais Windows e Linux suportados no Azure IaaS podem usar identidades gerenciadas.
3. A implantação é mais rápida e fácil, já que a extensão vm não precisa mais ser provisionada.
4. Com o ponto final do IMDS, até 1000 identidades gerenciadas pelo usuário podem ser atribuídas a uma única máquina virtual.
5. Não há nenhuma mudança significativa nas solicitações que utilizam o IMDS em oposição às que usam a extensão da máquina virtual, portanto é bastante simples portar sobre as implantações existentes que atualmente usam a extensão da máquina virtual.

Por essas razões, o serviço IMDS do Azure será a maneira de solicitar tokens, uma vez que a extensão da máquina virtual é preterida. 


## <a name="next-steps"></a>Próximas etapas

* [Como usar identidades gerenciadas para recursos do Azure em uma máquina virtual do Azure para adquirir um token de acesso](how-to-use-vm-token.md)
* [Serviço de metadados de instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
