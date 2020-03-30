---
title: Faça login em uma VM Linux com credenciais do Azure Active Directory
description: Aprenda a criar e configurar uma VM Linux para fazer login usando a autenticação do Azure Active Directory.
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: 2731693667d2129a72da72455c6bbdd74c277697
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366486"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Pré-visualização: Faça login em uma máquina virtual Linux no Azure usando a autenticação do Azure Active Directory

Para melhorar a segurança das VMs (máquinas virtuais) do Linux no Azure, você pode fazer a integração com a autenticação do AD (Azure Active Directory). Ao usar a autenticação do Azure AD para VMs do Linux, você controla e impõe de forma central as políticas que permitem ou negam acesso às VMs. Este artigo mostra como criar e configurar uma VM do Linux para usar a autenticação do Azure AD.


> [!IMPORTANT]
> A autenticação do Azure Active Directory está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Use esse recurso em uma máquina virtual de teste que você pretende descartar após o teste.
>


Há muitos benefícios de usar a autenticação do Azure AD para fazer logon em VMs do Linux no Azure, como:

- **Segurança aprimorada:**
  - Você pode usar suas credenciais corporativas do AD para fazer logon em VMs do Linux no Azure. Não é necessário criar contas de administrador local e gerenciar o tempo de vida da credencial.
  - Dependendo menos da contas de administrador local, você não precisa se preocupar com a perda ou o roubo de credenciais, credenciais fracas configuradas pelos usuários, etc.
  - As políticas de complexidade e de tempo de vida de senha configuradas para o diretório do Azure AD também ajudam a proteger as VMs do Linux.
  - Para proteger ainda mais o logon nas máquinas virtuais do Azure, você pode configurar a autenticação multifator.
  - A capacidade de fazer logon em VMs do Linux com Azure Active Directory também funciona para clientes que usam os [Serviços de Federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Colaboração contínua:** com RBAC (controle de acesso baseado em função), você pode especificar quem pode entrar em uma determinada VM como um usuário normal ou com privilégios de administrador. Quando os usuários entram na equipe ou saem dela, você pode atualizar a política RBAC da VM para conceder acesso conforme o necessário. Essa experiência é muito mais simples do que ter que limpar as VMs para remover as chaves públicas SSH desnecessárias. Quando os funcionários saem da organização e a conta de usuário é desabilitada ou removida do Azure AD, eles deixam de ter acesso aos recursos.

## <a name="supported-azure-regions-and-linux-distributions"></a>Regiões do Azure e distribuições do Linux com suporte

No momento, há suporte para as seguintes distribuições do Linux durante a versão prévia desse recurso:

| Distribuição | Versão |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 e Ubuntu Server 18.04 |


No momento, há suporte para as seguintes regiões do Azure durante a versão prévia desse recurso:

- Todas as regiões globais do Azure

>[!IMPORTANT]
> Para usar esse recurso de versão prévia, somente implante uma distribuição do Linux com suporte em uma região do Azure com suporte. Não há suporte para o recurso no Azure Governamental nem nas nuvens soberanas.
>
> Não é suportado usar essa extensão em clusters Azure Kubernetes Service (AKS). Para obter mais informações, consulte [Políticas de suporte para AKS](../../aks/support-policies.md).


Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Requisitos de rede

Para habilitar a autenticação Azure AD para suas VMs Linux no Azure, você precisa garantir que a configuração da rede de VMs permita o acesso de saída aos seguintes pontos finais na porta TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> Atualmente, os grupos de segurança da rede Azure não podem ser configurados para VMs habilitados com autenticação Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual Linux

Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create) e, em seguida, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create) usando uma distribuição com suporte em uma região com suporte. O exemplo a seguir implanta uma VM denominada *myVM* que usa o *Ubuntu 16.04 LTS* em um grupo de recursos denominado *myResourceGroup* na região *southcentralus*. Nos exemplos a seguir, você pode fornecer seu próprio grupo de recursos e seus próprios nomes de VM, conforme o necessário.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos.

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalar a extensão da VM de logon do Azure AD

> [!NOTE]
> Se implantar esta extensão em uma VM criada anteriormente garantir que a máquina tenha pelo menos 1GB de memória alocada, a extensão não será instalada

Para fazer login em uma VM Linux com credenciais Azure AD, instale a extensão VM de login do Azure Active Directory. As extensões de VM são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas máquinas virtuais do Azure. Use [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) para instalar a extensão *AADLoginForLinux* na VM denominada *myVM* no grupo de recursos *myResourceGroup*:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O *provisionamentoState* of *Succeeded* é mostrado assim que a extensão é instalada com sucesso na VM. A VM precisa de um agente VM em execução para instalar a extensão. Para obter mais informações, consulte [Visão geral do agente VM](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de função para a VM

A política de RBAC (controle de acesso baseado em função) do Azure determina quem pode fazer logon na VM. Duas funções RBAC são usadas para autorizar o logon na VM:

- **Logon de administrador da máquina virtual**: os usuários com essa função atribuída podem fazer logon em uma máquina virtual do Azure com privilégios de usuário de administrador do Windows ou raiz do Linux.
- **Logon de usuário da máquina virtual**: os usuários com essa função atribuído podem fazer logon uma máquina virtual do Azure com privilégios de usuários regulares.

> [!NOTE]
> Para permitir que um usuário faça logon na VM por SSH, você precisa atribuir a função *Logon de Administrador da Máquina Virtual* ou *Logon de Usuário da Máquina Virtual*. Um usuário do Azure com a função *Proprietário* ou *Colaborador* atribuída para uma VM não tem automaticamente privilégios para fazer logon na VM por SSH.

O exemplo a seguir usa [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) para atribuir a função *Logon de Administrador da Máquina Virtual* para a VM ao usuário atual do Azure. O nome de usuário da conta do Azure ativa é obtido com [az account show](/cli/azure/account#az-account-show) e o *escopo* é definido para a VM criada na etapa anterior com [az vm show](/cli/azure/vm#az-vm-show). O escopo também pode ser atribuído no nível do grupo de recursos ou da assinatura, e as permissões de herança de RBAC normais são aplicadas. Para obter mais informações, confira [Controles de acesso baseado em função](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o domínio do AAD e o domínio do nome de usuário de logon não corresponderem, especifique a ID de objeto da conta de usuário com *--assignee-object-id*, não apenas o nome de usuário de *--assignee*. É possível obter a ID de objeto para a conta de usuário com [az ad user list](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como usar o RBAC para gerenciar o acesso aos recursos da sua assinatura do Azure, confira o uso da [CLI do Azure](../../role-based-access-control/role-assignments-cli.md), do [portal do Azure](../../role-based-access-control/role-assignments-portal.md) ou do [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Você também pode configurar o Azure AD para exigir a autenticação multifator de um usuário específico para que ele entre na máquina virtual do Linux. Para obter mais informações, confira [Introdução ao Servidor de Autenticação Multifator do Microsoft Azure na nuvem](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Fazer logon na máquina virtual do Linux

Primeiro, exiba o endereço IP público da VM com [az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Faça logon na máquina virtual do Linux no Azure usando suas credenciais do Azure AD. O parâmetro `-l` permite que você especifique seu próprio endereço da conta do Azure AD. Substitua a conta de exemplo pela sua. Endereços de conta devem ser inseridos em letras minúsculas. Substitua o endereço IP de exemplo pelo endereço IP público da VM do comando anterior.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Você é solicitado a entrar no Azure AD com [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)um código de uso único em . Copie e cole o código de uso único na página de login do dispositivo.

Quando solicitado, insira suas credenciais de logon do Azure AD na página de logon. 

A seguinte mensagem é mostrada no navegador da Web quando você tiver autenticado com sucesso:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Feche a janela do navegador, retorne para o prompt do SSH e pressione a tecla **Enter**. 

Agora, você está conectado à máquina virtual do Linux no Azure com as permissões de função atribuídas, como *Usuário da VM* ou *Administrador da VM*. Se a sua conta de usuário for atribuída à função *de login do administrador de máquina virtual,* você poderá usar `sudo` para executar comandos que requerem privilégios de raiz.

## <a name="sudo-and-aad-login"></a>Logon do AAD e Sudo

Na primeira vez que você executar o sudo, será solicitado que você autentique uma segunda vez. Se você não quiser ter que se autenticar novamente para executar o sudo, edite o arquivo sudoers `/etc/sudoers.d/aad_admins` e substitua esta linha:

```bash
%aad_admins ALL=(ALL) ALL
```

Por esta linha:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Solucionar problemas de entrada

Alguns erros comuns ao tentar usar SSH com as credenciais do Azure AD incluem a ausência de uma função RBAC atribuída e prompts repetidos para entrar. Use as seções a seguir para corrigir esses problemas.

### <a name="access-denied-rbac-role-not-assigned"></a>Acesso negado: função RBAC não atribuída

Se o erro a seguir aparecer no prompt do SSH, verifique se você configurou políticas de RBAC para a VM que concedem ao usuário a função *Logon de Administrador da Máquina Virtual* ou *Logon de Usuário da Máquina Virtual*:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Prompts contínuos de entrada de SSH

Ao concluir com êxito a etapa de autenticação em um navegador da Web, você poderá ser imediatamente solicitado a entrar novamente com um novo código. Este erro normalmente é causado por uma incompatibilidade entre o nome de entrada especificado no prompt de SSH e a conta usada para entrar no Azure AD. Para corrigir esse problema:

- Verifique se o nome de entrada especificado no prompt de SSH está correto. Um erro de digitação no nome de entrada pode causar uma incompatibilidade entre o nome de entrada especificado no prompt de SSH e a conta usada para entrar no Azure AD. Por exemplo, você digitou *contoso.onmicrosoft.com\@azuresuer* em vez de *contoso.onmicrosoft.com azureuser\@*.
- Se você tiver várias contas de usuário, não forneça uma conta de usuário diferente na janela do navegador ao entrar no Azure AD.
- O Linux é um sistema operacional que diferencia maiúsculas de minúsculas. Há uma diferença entre 'Azureuser@contoso.onmicrosoft.com' e 'azureuser@contoso.onmicrosoft.com', que pode causar uma incompatibilidade. Especifique o UPN com a diferenciação correta de maiúsculas de minúsculas no prompt de SSH.

### <a name="other-limitations"></a>Outras limitações

Os usuários que herdam os direitos de acesso através de grupos aninhados ou atribuições de função não são suportados no momento. O usuário ou grupo deve ser atribuído diretamente às [atribuições de função necessárias.](#configure-role-assignments-for-the-vm) Por exemplo, o uso de grupos de gerenciamento ou atribuições de função de grupo aninhada não concederá as permissões corretas para permitir que o usuário faça login.

## <a name="preview-feedback"></a>Comentários de visualização

Compartilhe seus comentários sobre este recurso de visualização ou informe problemas usando-o no [Fórum de comentários do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Active Directory, confira [O que é o Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
