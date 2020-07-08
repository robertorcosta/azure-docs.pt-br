---
title: Perguntas frequentes e problemas conhecidos de identidades gerenciadas – Azure AD
description: Problemas conhecidos com identidades gerenciadas para recursos do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 6f18c9fe43b0b714e5709b014c051520b3722138
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855130"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).


### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Como você pode encontrar recursos que têm uma identidade gerenciada?

Você pode encontrar a lista de recursos que têm uma identidade gerenciada atribuída pelo sistema usando o seguinte comando de CLI do Azure: 

`az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table`




### <a name="do-managed-identities-have-a-backing-app-object"></a>Identidades gerenciadas têm um objeto de aplicativo de backup?

Não. As identidades gerenciadas e os registros de Aplicativo Azure AD não são a mesma coisa no diretório. 

Registros de aplicativo tem dois componentes: um objeto de aplicativo + um objeto de entidade de serviço. Identidades gerenciadas para recursos do Azure têm apenas um desses componentes: um objeto de entidade de serviço. 

Identidades gerenciadas não têm um objeto de aplicativo no diretório, que é comumente usado para conceder permissões de aplicativo para o MS Graph. Em vez disso, as permissões do MS Graph para identidades gerenciadas precisam ser concedidas diretamente à entidade de serviço.  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>As identidades gerenciadas dos recursos do Azure funcionam com os serviços de nuvem do Azure?

Não, não há planos para suportar identidades gerenciadas para recursos do Azure no Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>As identidades gerenciadas dos recursos do Azure funcionam com a ADAL (Biblioteca de Autenticação do Active Directory) ou com a MSAL (Microsoft Authentication Library)?

Não, as identidades gerenciadas dos recursos do Azure ainda não estão integradas com o ADAL ou o MSAL. Para obter detalhes sobre como adquirir um token de identidades gerenciadas para recursos do Azure usando o ponto de extremidade REST, confira [Como usar identidades gerenciadas para recursos em uma VM do Azure para adquirir um token de acesso](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>O que é o limite de segurança de identidades gerenciadas para recursos do Azure?

O limite de segurança da identidade é o recurso ao qual ele está anexado. Por exemplo, o limite de segurança de uma Máquina Virtual com identidades gerenciadas para recursos do Azure ativados é a Máquina Virtual. Qualquer código em execução nessa VM pode chamar as identidades gerenciadas do ponto de extremidade de recursos do Azure e solicitar tokens. É a experiência semelhante com outros recursos que suportam identidades gerenciadas para recursos do Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Qual identidade será o padrão IMDS se não for especificada a identidade na solicitação?

- Se a identidade gerenciada atribuída do sistema estiver habilitada e nenhuma identidade for especificada na solicitação, o IMDS será o padrão para a identidade gerenciada atribuída ao sistema.
- Se a identidade gerenciada atribuída do sistema não e nenhum existir identidade gerenciada atribuída ao usuário, o IMDS padrão será a identidade gerenciada atribuída ao usuário único. 
- Se a identidade gerenciada atribuída ao sistema não estiver habilitada e vários de usuário atribuídos a identidades gerenciadas existirem, é necessário, em seguida, especificar uma identidade gerenciada na solicitação.



### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Identidades gerenciadas serão recriadas automaticamente se eu mover uma assinatura para outro diretório?

Não. Se você mover uma assinatura para outro diretório, precisará recriá-los manualmente para conceder as atribuições de função de RBAC do Azure novamente.
- Para sistema atribuído a identidades gerenciadas: desabilite e habilite novamente. 
- Para identidades gerenciadas atribuídas ao usuário: exclua, recrie e anexe-os novamente para os recursos necessários (por exemplo, máquinas virtuais)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Posso usar uma identidade gerenciada para acessar um recurso em um diretório/locatário diferente?

Não. Identidades gerenciadas não têm suporte a cenários entre diretórios. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quais permissões do RBAC do Azure são necessárias para a identidade gerenciada em um recurso? 

- Identidade gerenciada atribuída ao sistema: Você precisa de permissões de gravação sobre o recurso. Por exemplo, para máquinas virtuais, você precisa do Microsoft.Compute/virtualMachines/write. Essa ação é incluída em funções internas específicas do recurso, como [Colaborador da máquina virtual](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Identidade gerenciada atribuída ao usuário: Você precisa de permissões de gravação sobre o recurso. Por exemplo, para máquinas virtuais, você precisa do Microsoft.Compute/virtualMachines/write. Também é necessário atribuir a função de [Operador da identidade gerenciada](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) sobre essa identidade.



## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar esquemas para identidades gerenciadas para a extensão de recursos do Azure

Quando as identidades gerenciadas dos recursos do Azure são habilitadas em uma VM, o seguinte erro é exibido ao tentar usar o recurso "Script de automação" para a VM ou seu grupo de recursos:

![Identidades gerenciadas para erro de exportação de script de automação de recursos do Azure](./media/msi-known-issues/automation-script-export-error.png)

As identidades gerenciadas da extensão de VM dos recursos do Azure (que deverão ser preteridas em janeiro de 2019) não dão suporte atualmente à capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar identidades gerenciadas para recursos do Azure no recurso. Essas seções podem ser adicionadas manualmente seguindo os exemplos em [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema se torna disponível para as identidades gerenciadas da extensão de VM dos recursos do Azure (que deverão ser preteridas em janeiro de 2019), ela será listada em [Exportando grupos de recursos que contêm extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A VM falha ao iniciar depois de ser movida do grupo de recursos ou assinatura

Se você mover uma máquina virtual no estado de execução, ela continua a ser executada durante a movimentação. No entanto, após a movimentação, se a VM for interrompida e reiniciada, ela falhará ao iniciar. Esse problema ocorre porque a VM não está atualizando a referência às identidades gerenciadas para a identidade de recursos do Azure e continua apontando para ela no grupo de recursos antigo.

**Solução alternativa** 
 
Acione uma atualização na VM para obter valores corretos para as identidades gerenciadas dos recursos do Azure. Você pode fazer uma alteração de propriedade de VM para atualizar a referência às identidades gerenciadas para a identidade de recursos do Azure. Por exemplo, você pode definir um novo valor de marca na VM com o seguinte comando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova marca "fixVM" com um valor 1 na VM. 
 
Ao definir essa propriedade, a VM atualiza com as identidades gerenciadas corretas para o URI de recurso de recursos do Azure e, em seguida, você deve poder iniciar a VM. 
 
Depois que a VM for iniciada, a marca poderá ser removida usando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```



### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferindo uma assinatura entre diretórios do Azure Active Directory

Identidades gerenciadas não são atualizadas quando uma assinatura for movida/transferida para outro diretório. Como resultado, quaisquer identidades gerenciadas atribuídas pelo sistema ou designadas pelo usuário serão quebradas. 

Para identidades gerenciadas em uma assinatura movidas para outro diretório, confira as seguintes soluções alternativas:

 - Para sistema atribuído a identidades gerenciadas: desabilite e habilite novamente. 
 - Para identidades gerenciadas atribuídas ao usuário: exclua, recrie e anexe-os novamente para os recursos necessários (por exemplo, máquinas virtuais)

Para obter mais informações, consulte [transferir uma assinatura do Azure para um diretório diferente do Azure AD (versão prévia)](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Mover a identidade gerenciada atribuída pelo usuário para uma assinatura/um grupo de recursos diferente

Não há suporte à ação de mover a identidade gerenciada atribuída pelo usuário para uma assinatura/um grupo de recursos diferente.
