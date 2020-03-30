---
title: Habilite os Serviços de Domínio Do Azure DS usando um modelo | Microsoft Docs
description: Saiba como configurar e ativar os serviços de domínio do Azure Active Directory usando um modelo do Azure Resource Manager
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612784"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Crie um domínio gerenciado pelo Azure Active Directory Domain Services usando um modelo do Azure Resource Manager

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches aos controladores de domínio por conta própria. O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar os grupos e as contas de usuário existentes para proteger o acesso aos recursos.

Este artigo mostra como ativar o Azure AD DS usando um modelo do Azure Resource Manager. Os recursos de suporte são criados usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Instalar e configurar o PowerShell do Azure.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e conecte-se à sua assinatura Do Azure](/powershell/azure/install-az-ps).
    * Certifique-se de fazer login na sua assinatura do Azure usando o cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure AD PowerShell e conecte-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de fazer login no seu inquilino Azure AD usando o [cmdlet Connect-AzureAD.][Connect-AzureAD]
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.

## <a name="dns-naming-requirements"></a>Requisitos de nomeação de DNS

Ao criar uma instância do Azure AD DS, você especifica um nome DNS. Eis algumas considerações a serem feitas ao escolher o nome DNS:

* **Nome de domínio incorporado:** Por padrão, o nome de domínio incorporado do diretório é usado (um sufixo *.onmicrosoft.com).* Se quiser habilitar o acesso LDAP seguro ao domínio gerenciado pela Internet, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft é proprietária do domínio *.onmicrosoft.com*, portanto, nenhuma AC (Autoridade de Certificação) emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, tipicamente um que você já possui e é roteável. Quando você usa um domínio roteável personalizado, o tráfego pode fluir corretamente conforme necessário para dar suporte aos seus aplicativos.
* **Sufixos de domínio não routable:** Geralmente recomendamos que você evite um sufixo de nome de domínio não routable, como *contoso.local*. O sufixo *.local* não é roteável e pode causar problemas com a resolução do DNS.

> [!TIP]
> Se você criar um nome de domínio personalizado, tome cuidado com os namespaces DNS existentes. Recomenda-se usar um nome de domínio separado de qualquer espaço de nome DNS existente ou no local.
>
> Por exemplo, se você tiver um espaço de nome DNS existente *de contoso.com,* crie um domínio Gerenciado pelo Azure AD DS com o nome de domínio personalizado de *aaddscontoso.com*. Se você precisar usar LDAP seguro, você deve registrar e possuir este nome de domínio personalizado para gerar os certificados necessários.
>
> Você pode precisar criar alguns registros adicionais de DNS para outros serviços em seu ambiente ou encaminhadores de DNS condicionalentre os espaços de nome DNS existentes em seu ambiente. Por exemplo, se você executar um servidor Web que hospeda um site usando o nome DNS raiz, poderá haver conflitos de nomenclatura que exigem entradas DNS adicionais.
>
> Nestes tutoriais e artigos de como fazer, o domínio personalizado de *aaddscontoso.com* é usado como um exemplo curto. Em todos os comandos, especifique seu próprio nome de domínio.

As seguintes restrições de nome DNS também se aplicam:

* **Restrições de prefixo de domínio:** Você não pode criar um domínio gerenciado com um prefixo maior que 15 caracteres. O prefixo do seu nome de domínio especificado (como *aaddscontoso* no *aaddscontoso.com* nome de domínio) deve conter 15 ou menos caracteres.
* **Conflitos de nome de rede:** O nome de domínio DNS para seu domínio gerenciado já não deveria existir na rede virtual. Especificamente, verifique os seguintes cenários que podem resultar em conflitos de nome:
    * Se você já tiver um domínio do Active Directory com o mesmo nome de domínio DNS na rede virtual do Azure.
    * Se a rede virtual em que você planeja habilitar o domínio gerenciado tiver uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS na rede local.
    * Se você tiver um serviço de nuvem existente do Azure com esse mesmo nome na rede virtual do Azure.

## <a name="create-required-azure-ad-resources"></a>Criar recursos AD do Azure obrigatórios

O Azure AD DS requer um diretor de serviço e um grupo AD do Azure. Esses recursos permitem que o domínio gerenciado pelo Azure AD DS sincronize dados e defina quais usuários têm permissões administrativas no domínio gerenciado.

Primeiro, registre o provedor de recursos azure AD Domain Services usando o cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Crie um principal de serviço Azure AD usando o [cmdlet New-AzureADServicePrincipal][New-AzureADServicePrincipal] para o Azure AD DS para se comunicar e autenticar-se. Um ID de aplicativo específico é usado chamado *Serviços de Controlador de Domínio* com um ID de *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Não altere este ID de aplicação.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora crie um grupo Azure AD chamado *Administradores AAD DC* usando o cmdlet [do New-AzureADGroup.][New-AzureADGroup] Os usuários adicionados a esse grupo recebem permissões para executar tarefas de administração no domínio gerenciado pelo Azure AD DS.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo *AAD DC Administrators* criado, adicione um usuário ao grupo usando o [cmdlet Add-AzureADGroupMember.][Add-AzureADGroupMember] Primeiro você recebe o ID de objeto do grupo *AAD DC Administrators* usando o cmdlet [Get-AzureADGroup][Get-AzureADGroup] e, em seguida, o ID de objeto do usuário desejado usando o cmdlet [Get-AzureADUser.][Get-AzureADUser]

No exemplo a seguir, o ID do objeto `admin@aaddscontoso.onmicrosoft.com`do usuário para a conta com uma UPN de . Substitua esta conta de usuário pelo UPN do usuário que deseja adicionar ao grupo *Administradores AAD DC:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Finalmente, crie um grupo de recursos usando o cmdlet [do New-AzResourceGroup.][New-AzResourceGroup] No exemplo a seguir, o grupo de recursos é chamado *myResourceGroup* e é criado na região *de westus.* Use seu próprio nome e região desejada:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos do Azure AD DS serão distribuídos entre as zonas para proporcionar redundância adicional. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

Não é preciso configurar nada para que o Azure AD DS seja distribuído entre as zonas. A plataforma do Azure lida automaticamente com a distribuição de recursos na zona. Para obter mais informações e ver a disponibilidade da região, consulte [Quais são as zonas de disponibilidade no Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Definição de recursos para Azure AD DS

Como parte da definição de recurso do Resource Manager, são necessários os seguintes parâmetros de configuração:

| Parâmetro               | Valor |
|-------------------------|---------|
| domainName              | O nome de domínio DNS para seu domínio gerenciado, levando em consideração os pontos anteriores sobre a nomeação de prefixos e conflitos. |
| filtradoSync            | O Azure AD DS permite que você sincronize *todos* os usuários e grupos disponíveis no Azure AD ou uma sincronização *com escopo* incluindo apenas grupos específicos. Se você optar por sincronizar todos os usuários e grupos, não será possível optar por executar apenas uma sincronização com escopo no futuro.<br /> Para mais informações sobre a sincronização com escopo, confira [Sincronização com do Azure AD Domain Services][scoped-sync].|
| notificationSettings    | Se houver quaisquer alertas gerados no domínio gerenciado pelo Azure AD DS, notificações de e-mail podem ser enviadas. <br />*Os administradores globais* do inquilino do Azure e os membros do grupo *Administradores AAD DC* podem ser *habilitados* para essas notificações.<br /> Se desejar, você pode adicionar destinatários adicionais para notificações quando houver alertas que requerem atenção.|
| domínioConfiguraçãodeconfiguração | Por padrão, um domínio gerenciado do Azure AD DS é criado como uma floresta de *Usuários*. Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local. Você não precisa especificar um *valor de configuração de domínio* para criar uma floresta de usuários.<br /> Uma floresta de *Recursos* sincroniza apenas usuários e grupos criados diretamente no Azure AD. Atualmente, as florestas de recursos estão em versão prévia. Defina o valor como *ResourceTrusting* para criar uma floresta de recursos.<br />Para saber mais sobre florestas de *Recursos*, incluindo por que você usaria uma e como criar relações de confiança das floresta com domínios locais do AD DS, confira [Visão geral das florestas de recursos do Azure AD DS][resource-forests].|

A definição de parâmetros condensados a seguir mostra como esses valores são declarados. Uma floresta de usuários chamada *aaddscontoso.com* é criada com todos os usuários do Azure AD sincronizados com o domínio gerenciado pelo Azure AD DS:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

O seguinte tipo de recurso de recurso do Gerenciador de recursos condensado é então usado para definir e criar o domínio gerenciado do Azure AD DS. Uma rede virtual e uma sub-rede do Azure já devem existir ou ser criadas como parte do modelo do Gerenciador de recursos. O domínio gerenciado pelo Azure AD DS está conectado a esta sub-rede.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Esses parâmetros e o tipo de recurso podem ser usados como parte de um modelo mais amplo do Gerenciador de recursos para implantar um domínio gerenciado, conforme mostrado na seção a seguir.

## <a name="create-a-managed-domain-using-sample-template"></a>Criar um domínio gerenciado usando o modelo de amostra

O modelo de exemplo completo do Gerenciador de recursos a seguir cria um domínio gerenciado pelo Azure AD DS e as regras de rede virtual, sub-rede e grupo de segurança de rede de suporte. As regras do grupo de segurança da rede são necessárias para proteger o domínio gerenciado e garantir que o tráfego possa fluir corretamente. Uma floresta de usuários com o nome DNS de *aaddscontoso.com* é criada, com todos os usuários sincronizados a partir do Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Este modelo pode ser implantado usando seu método de implantação preferido, como o [portal Azure,][portal-deploy] [o Azure PowerShell][powershell-deploy]ou um pipeline de CI/CD. O exemplo a seguir usa o [cmdlet New-AzResourceGroupDeployment.][New-AzResourceGroupDeployment] Especifique o nome do seu próprio grupo de recursos e o nome do arquivo do modelo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Leva alguns minutos para criar o recurso e retornar o controle ao prompt powershell. O domínio gerenciado pelo Azure AD DS continua a ser provisionado em segundo plano e pode levar até uma hora para concluir a implantação. No portal Azure, a página **Visão geral** do domínio gerenciado pelo Azure AD DS mostra o status atual durante esta fase de implantação.

Quando o portal Azure mostra que o domínio gerenciado pelo Azure AD DS terminou o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado pelo Azure AD DS no portal. Na **janela Visão geral,** você é solicitado a configurar automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha satisco no Azure AD Domain Services para](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="next-steps"></a>Próximas etapas

Para ver o domínio gerenciado pelo Azure AD DS em ação, você pode [participar de um VM do Windows,][windows-join] [configurar lDAP seguro][tutorial-ldaps]e configurar [sincronização de hash de senha][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
