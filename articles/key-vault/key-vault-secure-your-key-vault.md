---
title: Proteger o acesso ao cofre de chaves – Azure Key Vault | Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Abrange o modelo de autenticação e autorização do Key Vault e descreve como proteger o cofre de chaves.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 04f4a71e6b54100e5a133958845cf732c2286b32
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301069"
---
# <a name="secure-access-to-a-key-vault"></a>Proteger o acesso a um cofre de chaves

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Este artigo fornece uma visão geral do modelo de acesso do Key Vault. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus cofres de chaves.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a um cofre de chaves é controlado por meio de duas interfaces: o **plano de gerenciamento** e o **plano de dados**. É no plano de gerenciamento que você administra o cofre de chaves em si. As operações nesse plano incluem criar e excluir cofres de chaves, recuperando propriedades do Key Vault e atualizando as políticas de acesso. No plano de dados você trabalha com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e alterar chaves, segredos e certificados.

Para acessar um cofre de chaves em qualquer dos planos, todos os chamadores (usuários ou aplicativos) devem ter a autenticação e a autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais operações o chamador pode executar. 

Os dois planos usam o Azure AD (Azure Active Directory) para autenticação. Para receber autorização, o plano de gerenciamento usa o RBAC (controle de acesso baseado em função) e o plano de dados usa uma política de acesso ao Key Vault.

## <a name="active-directory-authentication"></a>Autenticação do Active Directory

Ao criar um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure Active Directory da assinatura. Todos os chamadores em ambos os planos devem se registrar neste locatário e autenticar para acessar o cofre de chaves. Em ambos os casos, o aplicativo pode acessar o Key Vault de duas maneiras:

- **Acesso Aplicativo mais usuário**: o aplicativo acessa o Key Vault em nome do usuário conectado. Exemplos deste tipo de acesso incluem o Azure PowerShell e o portal do Azure. O usuário recebe acesso de duas maneiras. Os usuários podem acessar o Key Vault com qualquer aplicativo ou devem usar um aplicativo específico (conhecido como _identidade composta_).
- **Acesso Somente por aplicativo**: O aplicativo é executado como um trabalho em segundo plano ou serviço daemon. A identidade do aplicativo recebe acesso ao cofre de chaves.

Para os dois tipos de acesso, o aplicativo autentica com o Azure Active Directory. O aplicativo usa qualquer [método de autenticação compatível](../active-directory/develop/authentication-scenarios.md) com base no tipo de aplicativo. O aplicativo adquire um token para um recurso no plano para conceder acesso. O recurso é um ponto de extremidade no plano de gerenciamento ou de dados, com base no ambiente do Azure. O aplicativo usa este token e envia uma solicitação da API REST ao Key Vault. Para saber mais, examine o [fluxo de autenticação completo](../active-directory/develop/v2-oauth2-auth-code-flow.md).

O modelo de mecanismo único para autenticação em ambos os planos tem vários benefícios:

- As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização.
- Se um usuário parte, ele perde instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure Active Directory, como habilitar a autenticação multifator para obter maior segurança.

## <a name="resource-endpoints"></a>Pontos de extremidade do recurso

Os aplicativos acessam os planos por meio de pontos de extremidade. Os controles de acesso dos dois planos trabalham de forma independente. Para conceder acesso a um aplicativo para usar as chaves em um cofre de chaves, você pode conceder acesso ao plano de dados usando uma política de acesso do Key Vault. Para conceder acesso de leitura do usuário para marcas e propriedades do Key Vault, mas não conceder acesso aos dados (chaves, segredos ou certificados), você deve conceder acesso ao plano de gerenciamento com RBAC.

A tabela a seguir mostra os pontos de extremidade para os planos de gerenciamento e de dados.

| Plano de&nbsp;acesso | Pontos de extremidade de acesso | Operações | Mecanismo de controle de&nbsp;acesso |
| --- | --- | --- | --- |
| Plano de gerenciamento | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure Governo dos EUA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e excluir cofres de chaves<br><br>Definir políticas de acesso do Key Vault<br><br>Definir marcas do Key Vault | RBAC do Azure Resource Manager |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Governo dos EUA:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: descriptografar, criptografar,<br> desencapsular, encapsular, verificar, assinar,<br> obter, listar, atualizar, criar,<br> importar, excluir, fazer backup, restaurar<br><br> Segredos: obter, listar, definir, excluir | Política de acesso ao cofre de chaves |

## <a name="management-plane-and-rbac"></a>RBAC e o plano de gerenciamento

No plano de gerenciamento, você usa o RBAC (controle de acesso baseado em função) para autorizar as operações que um chamador pode executar. No modelo de RBAC, cada assinatura do Azure tem uma instância do Azure Active Directory. Você pode conceder acesso a usuários, grupos e aplicativos desse diretório. O acesso é concedido para gerenciar recursos na assinatura do Azure que usa o modelo de implantação do Azure Resource Manager. Para conceder acesso, use o [Portal do Azure](https://portal.azure.com/), a [CLI do Azure](../cli-install-nodejs.md), o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Crie um cofre de chaves em um grupo de recursos e gerencie o acesso usando o Azure Active Directory. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Conceda o acesso em um nível de escopo específico ao atribuir funções RBAC apropriadas. Para conceder acesso a um usuário para gerenciar os cofres de chaves, atribua uma função `key vault Contributor` predefinida ao usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função RBAC:

- **Assinatura**: uma função RBAC atribuída no nível da assinatura que se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: uma função RBAC atribuída no nível do grupo de recursos que se aplica a todos os recursos nesse grupo de recursos.
- **Recursos específicos**: uma função atribuída a um recurso específico que se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para saber mais, confira [RBAC: funções internas](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tem permissões `Contributor` para um plano de gerenciamento de cofre de chaves, pode conceder a si mesmo o acesso ao plano de dados ao definir a política de acesso do Key Vault. Você deve controlar rigorosamente quem tem função de acesso `Contributor` aos cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Políticas de acesso e plano de dados

Conceda acesso ao plano de dados definindo as políticas de acesso ao Key Vault para um cofre de chaves. Para definir essas políticas de acesso, o usuário, grupo ou aplicativo deve ter permissões `Contributor` para o plano de gerenciamento desse cofre de chaves.

Conceda acesso a um usuário, grupo ou aplicativo para executar operações específicas para chaves ou segredos em um cofre de chaves. O Key Vault suporta até 1.024 entradas de política de acesso para um cofre de chaves. Para conceder acesso ao plano de dados a vários usuários, crie um grupo de segurança do Azure Active Directory e adicione usuários a esse grupo.

<a id="key-vault-access-policies"></a> As políticas de acesso ao Key Vault concedem separadamente as permissões a chaves, segredos e certificados. Você pode conceder a um usuário o acesso apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados estão no nível do cofre. As políticas de acesso do Key Vault não aceitam permissões granulares no nível do objeto, como uma chave, um segredo ou um certificado específicos. Para definir as políticas de acesso para um cofre de chaves, use o [Portal do Microsoft Azure](https://portal.azure.com/), a [CLI do Azure](../cli-install-nodejs.md), o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST de gerenciamento do Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> As políticas de acesso ao cofre de chaves aplicam-se no nível do cofre. Quando um usuário tem permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves no cofre de chaves.
>

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual no Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). É possível configurar [regras de firewalls e da rede virtual](key-vault-network-security.md) para ter uma camada adicional de segurança.

## <a name="example"></a>Exemplo

Neste exemplo, estamos desenvolvendo um aplicativo que usa um certificado para SSL, o Armazenamento do Microsoft Azure para armazenamento de dados e uma chave RSA de 2.048 bits para operações de assinatura. Nosso aplicativo é executado em uma máquina virtual (VM) do Azure (ou em um conjunto de dimensionamento de máquinas virtuais). Podemos usar um cofre de chaves para armazenar os segredos do aplicativo. Podemos armazenar o certificado de inicialização usado pelo aplicativo para autenticar com o Azure Active Directory.

Precisamos de acesso aos seguintes segredos e chaves armazenados:
- **Certificado SSL**: Usado para SSL.
- **Chave de armazenamento**: usada para acessar a conta de armazenamento.
- **Chave RSA de 2.048 bits**: Usado para operações de sinalização.
- **Certificado de inicialização**: Usado para autenticar com Azure AD. Após conceder o acesso, podemos buscar a chave de armazenamento e usar a chave RSA para assinatura.

É preciso definir as seguintes funções para especificar quem pode gerenciar, implantar e auditar nosso aplicativo:
- **Equipe de segurança**: a equipe de TI do escritório do Diretor de segurança ou colaboradores equivalentes. A equipe de segurança é responsável por guardar adequadamente os segredos. Os segredos podem incluir certificados SSL, chaves RSA usadas para assinatura, cadeias de conexão e chaves da conta de armazenamento.
- **Desenvolvedores e operadores**: a equipe que desenvolve o aplicativo e o implanta no Azure. Os membros desta equipe não fazem parte da equipe de segurança. Eles não devem ter acesso a dados confidenciais, como certificados SSL e chaves RSA. Somente o aplicativo que eles implantam deve ter acesso aos dados confidenciais.
- **Auditores**: essa função é para colaboradores que não são membros da equipe de desenvolvimento ou de TI geral. Eles analisam o uso e a manutenção de certificados, chaves e segredos para garantir a conformidade com padrões de segurança. 

Há outra função que está fora do escopo do nosso aplicativo: o administrador da assinatura (ou do grupo de recursos). O administrador da assinatura define permissões de acesso inicial para a equipe de segurança. Ele concede acesso à equipe de segurança usando um grupo de recursos que tem os recursos exigidos pelo aplicativo.

É necessário autorizar as seguintes operações para as funções:

**Equipe de segurança**
- Criar cofres de chaves.
- Ativar o registro em log do Key Vault.
- Adicionar chaves e segredos.
- Criar backups de chaves para recuperação de desastre.
- Definir as políticas de acesso do Key Vault para conceder permissões a usuários e aplicativos para operações específicas.
- Distribuir periodicamente as chaves e segredos.

**Desenvolvedores e operadores**
- Obter referências da equipe de segurança para a inicialização e certificados SSL (impressões digitais), a chave de armazenamento (URI do segredo) e a chave RSA (URI da chave) para a assinatura.
- Desenvolver e implantar o aplicativo para acessar chaves e segredos via programação.

**Auditores**
- Examinar os logs do Key Vault para confirmar o uso adequado de chaves e segredos, e a conformidade com padrões de segurança de dados.

A tabela a seguir resume as permissões de acesso para as funções e o aplicativo. 

| Role | Permissões do plano de gerenciamento | Permissões do plano de dados |
| --- | --- | --- |
| Equipe de segurança | Colaborador do Key Vault | Chaves: fazer backup, criar, excluir, obter, importar, listar, restaurar<br>Segredos: todas as operações |
| Desenvolvedores e&nbsp;operadores | Permissão para implantar o Key Vault<br><br> **Observação**: essa permissão possibilita que as VMs implantadas busquem segredos do cofre de chaves. | Nenhum |
| Auditores | Nenhum | Chaves: lista<br>Segredos: lista<br><br> **Observação**: essa permissão possibilita que os auditores inspecionem os atributos (marcas, datas de ativação, datas de validade) de chaves e segredos que não são emitidos nos logs. |
| Aplicativo | Nenhum | Chaves: assinar<br>Segredos: obter |

As três funções da equipe precisam ter acesso a outros recursos e a permissões do Key Vault. Para implantar VMs (ou o recurso de Aplicativos Web do Serviço de Aplicativo do Azure), os desenvolvedores e operadores precisam de acesso `Contributor` a esses tipos de recursos. Os auditores precisam de acesso de leitura para a Conta de armazenamento em que os logs do Key Vault estão armazenados.

Para saber mais sobre como implantar certificados, chaves de acesso e segredos via programação, consulte estes recursos:
- Saiba como [implantar certificados em VMs a partir de um cofre de chaves gerenciado pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (postagem de blog).
- Baixe os [exemplos de cliente do Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Este conteúdo ilustra como usar um certificado de inicialização para autenticação no Microsoft Azure AD a fim de acessar um cofre de chaves.

É possível conceder a maioria das permissões de acesso usando o portal do Azure. Para conceder permissões granulares, você pode usar o Azure PowerShell ou a CLI do Azure.

Os snippets do PowerShell nesta seção são criados com as seguintes suposições:
- O administrador do Azure Active Directory criou grupos de segurança para representar as três funções: Equipe de segurança, DevOps de aplicativo e Auditores de aplicativo da Contoso. O administrador adicionou os usuários aos respectivos grupos.
- Todos os recursos estão localizados no grupo de recursos **ContosoAppRG**.
- Os logs do Key Vault são armazenados na conta de armazenamento **contosologstorage**. 
- O cofre de chaves **ContosoKeyVault** e a conta de armazenamento **contosologstorage** estão no mesmo local do Azure.

O administrador de assinatura atribui as funções `key vault Contributor` e `User Access Administrator` para a equipe de segurança. Essas funções permitem que a equipe de segurança gerencie o acesso a outros recursos e aos cofres de chaves, ambos no grupo de recursos **ContosoAppRG**.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A equipe de segurança cria um cofre de chaves e define as permissões de acesso e registro em log. Para saber mais sobre as permissões da política de acesso ao Key Vault, consulte [Sobre chaves, segredos e certificados do Azure Key Vault](about-keys-secrets-and-certificates.md).

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

As funções personalizadas definidas podem ser atribuídas somente à assinatura em que o grupo de recursos **ContosoAppRG** foi criado. Para usar uma função personalizada para outros projetos em outras assinaturas, adicione outras assinaturas ao escopo da função.

Para a equipe de DevOps, a atribuição de função personalizada para a permissão `deploy/action` do cofre de chaves tem como escopo o grupo de recursos. Somente as VMs criadas no grupo de recursos **ContosoAppRG** têm permissão de acesso aos segredos (certificados de inicialização e SSL). As VMs criadas em outros grupos de recursos por um membro do DevOps não podem acessar esses segredos, mesmo se a VM tiver as URIs do segredo.

Este exemplo descreve um cenário simples. Os cenários da vida real podem ser mais complexos. Você pode ajustar as permissões do cofre de chaves com base em suas necessidades. Vamos assumir que a equipe de segurança forneceu as referências de chave e segredo (URIs e impressões digitais), usadas pela equipe de DevOps nos aplicativos. Desenvolvedores e operadores não precisam de nenhum acesso ao plano de dados. Nos concentramos em proteger seu cofre de chaves. Pense da mesma maneira ao proteger [suas VMs](https://azure.microsoft.com/services/virtual-machines/security/), [contas de armazenamentos](../storage/common/storage-security-guide.md) e outros recursos do Azure.

> [!NOTE]
> Esse exemplo mostra como o acesso ao Key Vault é bloqueado na produção. Os desenvolvedores devem ter sua própria assinatura ou grupo de recursos com permissões totais para gerenciar seus cofres, VMs e a conta de armazenamento onde desenvolvem o aplicativo.

É recomendável que você defina um acesso seguro adicional ao seu cofre de chaves [configurando os firewalls e redes virtuais do Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Recursos

* [RBAC do Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

* [RBAC: funções internas](../role-based-access-control/built-in-roles.md)

* [Entender a implantação do Resource Manager e a implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Gerenciar o RBAC com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [Gerenciar o RBAC com a API REST](../role-based-access-control/role-assignments-rest.md)

* [RBAC no Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Esta vídeo conferência 2015 Microsoft Ignite discute o gerenciamento de acesso e capacidades de relatório no Azure. Ele também explora as melhores práticas para proteger o acesso a assinaturas do Azure usando Azure AD.

* [Autorizar acesso aos aplicativos Web usando OAuth 2.0 e Azure Active Directory](../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [APIs REST de gerenciamento do Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A referência das APIs REST para gerenciar o cofre de chaves via programação, incluindo a configuração de política de acesso ao Key Vault.

* [APIs REST do Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Controle de acesso a chave](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Controle de acesso a segredo](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Definir](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) e [remover](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) a política de acesso ao Key Vault usando o PowerShell.
  
## <a name="next-steps"></a>Próximas etapas

Configurar [redes virtuais e firewalls do Key Vault](key-vault-network-security.md).

Para ver um tutorial de introdução para um administrador, consulte [O que é o Azure Key Vault](key-vault-overview.md).

Para saber mais sobre o log de uso do Key Vault, confira [Log do Azure Key Vault](key-vault-logging.md).

Para saber mais sobre o uso de chaves e segredos com o Azure Key Vault, consulte [Sobre chaves e segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Se você tiver dúvidas sobre o Key Vault, visite os [fóruns](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
