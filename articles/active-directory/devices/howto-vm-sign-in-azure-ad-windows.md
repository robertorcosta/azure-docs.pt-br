---
title: Faça login na máquina virtual do Windows no Azure usando o Azure Active Directory (Preview)
description: Azure AD faça login em uma VM Azure executando o Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050069"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Faça login na máquina virtual do Windows no Azure usando a autenticação do Azure Active Directory (Preview)

As organizações agora podem utilizar a autenticação do Azure Active Directory (AD) para suas máquinas virtuais (VMs) do Azure executando **a edição do Datacenter do Windows Server 2019** ou **o Windows 10 1809** e posterior. Usar o Azure AD para autenticar as VMs fornece uma maneira de controlar e aplicar políticas centralmente. Ferramentas como o RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure) e acesso condicional aazure AD permitem controlar quem pode acessar uma VM. Este artigo mostra como criar e configurar uma VM do Windows Server 2019 para usar a autenticação Azure AD.

|     |
| --- |
| O login do Azure AD para o Azure Windows VMs é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Existem muitos benefícios de usar a autenticação Azure AD para fazer login em VMs do Windows no Azure, incluindo:

- Utilize as mesmas credenciais Ad azure federadas ou gerenciadas que você normalmente usa.
- Não precisa mais gerenciar contas de administradores locais.
- O Azure RBAC permite que você conceda o acesso apropriado às VMs com base na necessidade e remova-a quando não for mais necessária.
- Antes de permitir o acesso a uma VM, o Azure AD Conditional Access pode impor requisitos adicionais, como: 
   - Autenticação multifator
   - Verificação de risco de entrada
- Automatize e dimensione o Azure AD junto com as VMs do Azure Windows que fazem parte das suas implantações de VDI.

> [!NOTE]
> Uma vez que você habilitar esse recurso, suas VMs do Windows no Azure serão aderidas ao Azure AD. Você não pode se juntar a ele a outros domínios, como AD ou Azure AD DS. Se você precisar fazer isso, você precisará desconectar a VM do seu inquilino Azure AD desinstalando a extensão.

## <a name="requirements"></a>Requisitos

### <a name="supported-azure-regions-and-windows-distributions"></a>Regiões azure suportadas e distribuições do Windows

As seguintes distribuições do Windows são suportadas atualmente durante a visualização deste recurso:

- Windows Server 2019 Datacenter
- Windows 10 1809 e posterior

> [!IMPORTANT]
> A conexão remota com VMs unidos ao Azure AD só é permitida a partir de PCs do Windows 10 que são Azure AD unidos ou Azure AD híbrido saem ao **mesmo** diretório que a VM. 

No momento, há suporte para as seguintes regiões do Azure durante a versão prévia desse recurso:

- Todas as regiões globais do Azure

> [!IMPORTANT]
> Para usar esse recurso de visualização, apenas implante uma distribuição suportada do Windows e em uma região Azure suportada. Atualmente, o recurso não é suportado no Governo Azure ou em nuvens soberanas.

### <a name="network-requirements"></a>Requisitos de rede

Para habilitar a autenticação Azure AD para suas VMs do Windows no Azure, você precisa garantir que a configuração da rede vMs permita o acesso de saída aos seguintes pontos finais na porta TCP 443:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Habilitando o login do Azure AD para O Windows VM no Azure

Para usar o login do Azure AD para O Windows VM no Azure, você precisa primeiro ativar a opção de login Azure AD para sua VM windows e, em seguida, você precisa configurar atribuições de função RBAC para usuários que estão autorizados a fazer login na VM.
Existem várias maneiras de habilitar o login do Azure AD para o Seu VM do Windows:

- Usando a experiência do portal Azure ao criar uma VM do Windows
- Usando a experiência Azure Cloud Shell ao criar uma VM windows **ou para uma VM Windows existente**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Usando o portal Azure, crie experiência em VM para permitir o login do Azure AD

Você pode habilitar o login do Azure AD para o Windows Server 2019 Datacenter ou windows 10 1809 e imagens posteriores da VM. 

Para criar um VM do Data center do Windows Server 2019 no Azure com logon Azure AD: 

1. Faça login no [portal Azure,](https://portal.azure.com)com uma conta que tenha acesso para criar VMs, e selecione **+ Crie um recurso**.
1. Digite **o Windows Server** na barra de pesquisa Do Marketplace.
   1. Clique **no Windows Server** e escolha o Centro de Dados Windows Server **2019** de Selecionar uma parada de plano de software.
   1. Clique em **Criar**.
1. Na guia "Gerenciamento", habilite a opção **de login com credenciais AAD (Preview)** na seção Diretório ativo do Azure de off para **on**.
1. Certifique-se de que a **identidade gerenciada atribuída pelo Sistema** a seção Identidade está definida como **Em**. Essa ação deve acontecer automaticamente depois de ativar o Login com credenciais Azure AD.
1. Passe pelo resto da experiência de criar uma máquina virtual. Durante essa visualização, você terá que criar um nome de usuário e senha do administrador para a VM.

![Login com credenciais Ad do Azure criar uma VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Para fazer login na VM usando sua credencial Azure AD, você precisará primeiro configurar atribuições de função para a VM, conforme descrito em uma das seções abaixo.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Usando a experiência Azure Cloud Shell para permitir o login do Azure AD

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ferramentas comuns do Azure estão pré-instaladas e configuradas para uso com sua conta. Basta selecionar o botão Copiar para copiar o código, colá-lo no Cloud Shell e pressionar Enter para executá-lo. Há algumas maneiras de abrir o Cloud Shell:

Selecione Experimente no canto superior direito de um bloco de código.
Abra o Cloud Shell em seu navegador.
Selecione o botão Cloud Shell no menu no canto superior direito do [Portal do Azure](https://portal.azure.com).

Se você optar por instalar e usar o CLI localmente, este artigo requer que você esteja executando a versão 2.0.31 do Azure CLI ou posterior. Execute az --version para localizar a versão. Se você precisar instalar ou atualizar, consulte o artigo [Instale o Azure CLI](/cli/azure/install-azure-cli).

1. Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). 
1. Crie uma VM com [az vm criar](/cli/azure/vm#az-vm-create) usando uma distribuição suportada em uma região suportada. 
1. Instale a extensão VM de login azure AD. 

O exemplo a seguir implanta uma VM chamada myVM que usa o Win2019Datacenter, em um grupo de recursos chamado myResourceGroup, na região do centro sul. Nos exemplos a seguir, você pode fornecer seu próprio grupo de recursos e seus próprios nomes de VM, conforme o necessário.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> É necessário que você habilite a identidade gerenciada atribuída ao Sistema em sua máquina virtual antes de instalar a extensão VM de login Azure AD.

A criação da VM e dos recursos de suporte demora alguns minutos.

Finalmente, instale a extensão VM de login azure AD para permitir o login do Azure AD para O Windows VM. As extensões de VM são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas máquinas virtuais do Azure. Use o conjunto [de extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) para instalar a extensão AADLoginForWindows na VM chamada myVM no grupo de recursos myResourceGroup:

> [!NOTE]
> Você pode instalar a extensão AADLoginForWindows em um Windows Server 2019 ou Windows 10 1809 e posterior vm para habilitá-lo para autenticação Azure AD. Um exemplo de Cli AZ é mostrado abaixo.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O `provisioningState` `Succeeded` de é mostrado, uma vez que a extensão é instalada na VM.

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de função para a VM

Agora que você criou a VM, você precisa configurar a política RBAC do Azure para determinar quem pode fazer login na VM. Duas funções RBAC são usadas para autorizar o logon na VM:

- **Login do administrador de máquina virtual**: Os usuários com essa função atribuída podem fazer login em uma máquina virtual do Azure com privilégios de administrador.
- **Logon de usuário da máquina virtual**: os usuários com essa função atribuído podem fazer logon uma máquina virtual do Azure com privilégios de usuários regulares.

> [!NOTE]
> Para permitir que um usuário faça login na VM via RDP, você deve atribuir o login do administrador virtual ou a função de login do usuário da máquina virtual. Um usuário do Azure com as funções Proprietário ou Contribuinte atribuídos a uma VM não tem automaticamente privilégios de fazer login na VM sobre RDP. Isso é para proporcionar uma separação auditada entre o conjunto de pessoas que controlam máquinas virtuais versus o conjunto de pessoas que podem acessar máquinas virtuais.

Existem várias maneiras de configurar atribuições de função para VM:

- Usando a experiência do Portal Azure AD
- Usando a experiência Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Usando a experiência do Azure AD Portal

Para configurar as atribuições de função para o Azure AD habilitado para O Windows Server 2019 Datacenter VMs:

1. Navegue até a página de visão geral da máquina virtual específica
1. Selecione **controle de acesso (IAM)** nas opções de menu
1. Selecione **Adicionar**, **Adicionar atribuição de função** para abrir o painel de atribuição adicionar função.
1. Na lista **de** itens de baixa, selecione uma função como login de administrador de máquina **virtual** ou login de usuário da **máquina virtual**.
1. No campo **Selecionar,** selecione um usuário, grupo, diretor de serviço ou identidade gerenciada. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.
1. Selecione **Salvar,** para atribuir a função.

Após alguns instantes, a entidade de segurança é atribuída a função no escopo selecionado.

![Atribuir funções aos usuários que acessarão a VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Usando a experiência Azure Cloud Shell

O exemplo a seguir usa [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) para atribuir a função Logon de Administrador da Máquina Virtual para a VM ao usuário atual do Azure. O nome de usuário da conta do Azure ativa é obtido com [az account show](/cli/azure/account#az-account-show) e o escopo é definido para a VM criada na etapa anterior com [az vm show](/cli/azure/vm#az-vm-show). O escopo também pode ser atribuído no nível do grupo de recursos ou da assinatura, e as permissões de herança de RBAC normais são aplicadas. Para obter mais informações, consulte [Controles de acesso baseados em função](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o domínio AAD e o domínio de nome de usuário de logon `--assignee-object-id`não corresponderem, você `--assignee`deve especificar o ID de objeto da sua conta de usuário com o , não apenas o nome de usuário para . É possível obter a ID de objeto para a conta de usuário com [az ad user list](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como usar o RBAC para gerenciar o acesso aos recursos de assinatura do Azure, consulte os seguintes artigos:

- [Gerenciar o acesso aos recursos do Azure usando o RBAC e a CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](/azure/role-based-access-control/role-assignments-portal)
- [Gerencie o acesso aos recursos do Azure usando o RBAC e o Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="using-conditional-access"></a>Usando acesso condicional

Você pode impor políticas de acesso condicional, como autenticação multifatorial ou verificação de risco de login do usuário antes de autorizar o acesso a VMs do Windows no Azure que estão habilitados com o login do Azure AD. Para aplicar a política de Acesso Condicional, você deve selecionar o aplicativo "Azure Windows VM Sign-In" a partir da opção de atribuição de aplicativos ou ações na nuvem e, em seguida, usar o risco de login como condição e/ou exigir autenticação multifatorial como um controle de acesso de subvenção. 

> [!NOTE]
> Se você usar "Exigir autenticação multifatorial" como um controle de acesso de concessão para solicitar acesso ao aplicativo "Azure Windows VM Sign-In", então você deve fornecer a reivindicação de autenticação de vários fatores como parte do cliente que inicia a sessão RDP para o Windows de destino VM em Azure. A única maneira de conseguir isso em um cliente windows 10 é usar o Windows Hello for Business PIN ou authenication biométrica com o cliente RDP. O suporte para autenticação biométrica foi adicionado ao cliente RDP na versão 1809 do Windows 10. Desktop remoto usando a autenticação do Windows Hello for Business só está disponível para implantações que usam o modelo cert trust e atualmente não está disponível para o modelo de confiança chave.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Faça login usando credenciais Azure AD para uma VM do Windows

> [!IMPORTANT]
> A conexão remota com VMs unidos ao Azure AD só é permitida a partir de PCs do Windows 10 que são Azure AD unidos ou Azure AD híbrido saem ao **mesmo** diretório que a VM. Além disso, para rdP usando credenciais Azure AD, o usuário deve pertencer a uma das duas funções RBAC, Login de Administrador de Máquina Virtual ou Login de Usuário da Máquina Virtual. Neste momento, o Azure Bastion não pode ser usado para fazer login usando a autenticação do Azure Active Directory com a extensão AADLoginForWindows. Apenas rdp direto é suportado.

Para fazer login na sua máquina virtual do Windows Server 2019 usando o Azure AD: 

1. Navegue até a página de visão geral da máquina virtual habilitada com o logon Azure AD.
1. Selecione **Conectar** para abrir o Connect para a lâmina da máquina virtual.
1. Selecione **Baixar arquivo RDP**.
1. Selecione **Abrir** para iniciar o cliente Remote Desktop Connection.
1. Selecione **Conectar** para iniciar a caixa de diálogo de logon do Windows.
1. Logon usando suas credenciais azure AD.

Agora você está conectado à máquina virtual do Windows Server 2019 Azure com as permissões de função atribuídas, como VM User ou VM Administrator. 

> [!NOTE]
> Você pode salvar o . Arquivo RDP localmente em seu computador para iniciar futuras conexões remotas de desktop para sua máquina virtual em vez de ter que navegar para a página de visão geral da máquina virtual no portal Azure e usando a opção de conexão.

## <a name="troubleshoot"></a>Solução de problemas

### <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

A extensão AADLoginForWindows deve ser instalada com sucesso para que a VM complete o processo de adesão do Azure AD. Execute as seguintes etapas se a extensão VM não for instalada corretamente.

1. RDP para a VM usando a conta do administrador local e examinar o CommandExecuti'n.log em  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Se a extensão for reiniciada após a falha inicial, o log com o erro de implantação será salvo como CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Abra um prompt de comando na VM e verifique essas consultas no Ponto Final de Serviço de Metadados de Ocorrência (IMDS) em execução nos retornos do host Azure:

   | Comando para executar | Saída esperada |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Informações corretas sobre a VM do Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | ID de inquilino válido associado à assinatura do Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Token de acesso válido emitido pelo Azure Active Directory para a identidade gerenciada atribuída a esta VM |

   > [!NOTE]
   > O token de acesso pode ser [http://calebb.net/](http://calebb.net/)decodificado usando uma ferramenta como . Verifique se o "appid" no token de acesso corresponde à identidade gerenciada atribuída à VM.

1. Certifique-se de que os pontos finais necessários estão acessíveis a partir da VM usando a linha de comando:
   
   - enrolar https:\//login.microsoftonline.com/ -D -
   - cacho\/https:`<TenantID>`/login.microsoftonline.com/ / -D -

   > [!NOTE]
   > Substitua pelo `<TenantID>` ID do Inquilino Azure AD que está associado à assinatura do Azure.

   - cacho\/https: /enterpriseregistration.windows.net/ -D -
   - enrolar https:\//device.login.microsoftonline.com/ -D -
   - enrolar https:\//pas.windows.net/ -D -

1. O estado do dispositivo `dsregcmd /status`pode ser visualizado executando . O objetivo é que o `AzureAdJoined : YES`Estado do Dispositivo mostre como .

   > [!NOTE]
   > A atividade de adesão do Azure AD é capturada no visualizador de eventos o registro do dispositivo do usuário\registro de administrador.

Se a extensão do AADLoginForWindows falhar com determinado código de erro, você poderá executar as seguintes etapas:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problema 1: A extensão AADLoginForWindows falha ao instalar com o código de erro do terminal '1007' e código de saída: -2145648574.

Este código de saída se traduz em DSREG_E_MSI_TENANTID_UNAVAILABLE porque a extensão não pode consultar as informações do Inquilino AD do Azure.

1. Verifique se o Azure VM pode recuperar o TenantID do Serviço de Metadados de Ocorrência.

   - RDP para a VM como administrador local e verificar o ponto final retorna iD de inquilino válido executando este comando a partir de uma linha de comando elevada na VM:
      
      - cacho -H Metadados:verdadeirohttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. O administrador da VM tenta instalar a extensão AADLoginForWindows, mas uma identidade gerenciada atribuída ao sistema não habilitou a VM primeiro. Navegue até a lâmina de identidade da VM. A partir da guia atribuída ao Sistema, verifique se o status é alternado para On.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problema 2: A extensão AADLoginForWindows falha em instalar com código de saída: -2145648607

Este código Exit se traduz em DSREG_AUTOJOIN_DISC_FAILED porque `https://enterpriseregistration.windows.net` a extensão não é capaz de alcançar o ponto final.

1. Verifique se os pontos finais necessários estão acessíveis a partir da VM usando a linha de comando:

   - enrolar https:\//login.microsoftonline.com/ -D -
   - cacho\/https:`<TenantID>`/login.microsoftonline.com/ / -D -
   
   > [!NOTE]
   > Substitua pelo `<TenantID>` ID do Inquilino Azure AD que está associado à assinatura do Azure. Se você precisar encontrar o ID do inquilino, você pode passar o tempo sobre o nome da sua conta para obter o diretório/iD do inquilino, ou selecionar o ID > do Diretório > do Diretório Ativo do Azure no portal Azure.

   - cacho\/https: /enterpriseregistration.windows.net/ -D -
   - enrolar https:\//device.login.microsoftonline.com/ -D -
   - enrolar https:\//pas.windows.net/ -D -

1. Se algum dos comandos falhar com `<URL>`"Não foi possível resolver host", tente executar este comando para determinar o servidor DNS que está sendo usado pela VM.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Substitua pelos `<URL>` nomes de domínio totalmente qualificados usados pelos pontos finais, como "login.microsoftonline.com".

1. Em seguida, veja se a especificação de um servidor DNS público permite que o comando tenha sucesso:

   `nslookup <URL> 208.67.222.222`

1. Se necessário, altere o servidor DNS atribuído ao grupo de segurança de rede ao qual o Azure VM pertence.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problema 3: A extensão Do AADLoginForWindows não consegue instalar com o código de saída: 51

O código de saída 51 traduz-se em "Esta extensão não é suportada no sistema operacional da VM".

Na Visualização Pública, a extensão AADLoginForWindows destina-se apenas a ser instalada no Windows Server 2019 ou no Windows 10 (Build 1809 ou posterior). Certifique-se de que a versão do Windows seja suportada. Se a compilação do Windows não for suportada, desinstale a extensão VM.

### <a name="troubleshoot-sign-in-issues"></a>Solucionar problemas de entrada

Alguns erros comuns quando você tenta RDP com credenciais Azure AD incluem nenhuma função RBAC atribuída, cliente não autorizado ou método de login 2FA necessário. Use as seguintes informações para corrigir esses problemas.

O dispositivo e o estado SSO podem ser visualizados executando `dsregcmd /status`. O objetivo é que o `AzureAdJoined : YES` `SSO State` Estado `AzureAdPrt : YES`do Dispositivo mostre e mostre .

Além disso, o login RDP usando contas Azure AD é capturado no visualizador de eventos os registros de eventos AAD\Operational.

#### <a name="rbac-role-not-assigned"></a>Função RBAC não atribuída

Se você ver a seguinte mensagem de erro ao iniciar uma conexão remota de desktop à Sua VM: 

- Sua conta está configurada para evitar que você use este dispositivo. Para obter mais informações, entre em contato com o administrador do sistema

![Sua conta está configurada para evitar que você use este dispositivo.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Verifique se você [configurou as políticas de RBAC](../../virtual-machines/linux/login-using-aad.md) para a VM que concede ao usuário o login do administrador da máquina virtual ou a função de login do usuário da máquina virtual:
 
#### <a name="unauthorized-client"></a>Cliente não autorizado

Se você ver a seguinte mensagem de erro ao iniciar uma conexão remota de desktop à Sua VM: 

- Suas credenciais não funcionaram.

![Suas credenciais não funcionaram.](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Verifique se o PC do Windows 10 que você está usando para iniciar a conexão remota da área de trabalho é aquele que está junto ao Azure AD ou o Azure AD híbrido juntou-se ao mesmo diretório Azure AD onde sua VM está conectada. Para obter mais informações sobre a identidade do dispositivo, consulte o artigo [O que é uma identidade de dispositivo](/azure/active-directory/devices/overview).

> [!NOTE]
> O Windows 10 20H1, adicionará suporte para o Azure AD Registered PC para iniciar a conexão remota da área de trabalho à sua VM. Junte-se ao Programa Windows Insider para testar isso e explorar novos recursos do Windows 10.

Além disso, verifique se a extensão AADLoginForWindows não foi desinstalada após a adesão ao Azure AD.
 
#### <a name="mfa-sign-in-method-required"></a>Método de login mfa necessário

Se você ver a seguinte mensagem de erro ao iniciar uma conexão remota de desktop à Sua VM: 

- O método de login que você está tentando usar não é permitido. Tente um método de login diferente ou entre em contato com o administrador do sistema.

![O método de login que você está tentando usar não é permitido.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Se você configurou uma política de acesso condicional que requer autenticação multifatorial (MFA) antes de acessar o recurso, então você precisa garantir que o PC do Windows 10 instida a conexão remota da área de trabalho aos seus sinais de VM em uso de um forte método de autenticação, como o Windows Hello. Se você não usar um método de autenticação forte para sua conexão remota de desktop, você verá o erro anterior.

Se você não implantou o Windows Hello for Business e se isso não for uma opção por enquanto, você pode excluir o requisito do MFA configurando a política de Acesso Condicional que exclui o aplicativo "Azure Windows VM Sign-In" da lista de aplicativos em nuvem que requerem MFA. Para saber mais sobre o Windows Hello for Business, consulte [Windows Hello for Business Overview](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> A autenticação do Windows Hello for Business PIN com RDP tem sido suportada pelo Windows 10 para várias versões, no entanto o suporte para autenticação biométrica com RDP foi adicionado na versão 1809 do Windows 10. O uso do Windows Hello for Business auth durante o RDP só está disponível para implantações que usam o modelo cert trust e atualmente não está disponível para o modelo de confiança chave.
 
## <a name="preview-feedback"></a>Comentários de visualização

Compartilhe seu feedback sobre esse recurso de visualização ou problemas de relatório usando-o no [fórum de feedback Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Active Directory, confira [O que é o Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
