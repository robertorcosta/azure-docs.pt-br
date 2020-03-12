---
title: Pool de hosts da Área de Trabalho Virtual do Windows no Azure Marketplace – Azure
description: Como criar um pool de hosts da Área de Trabalho Virtual do Windows usando o Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127992"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Tutorial: criar um pool de hosts usando o Azure Marketplace

Neste tutorial, você aprenderá a criar um pool de hosts em um locatário de área de trabalho virtual do Windows usando uma oferta Microsoft Azure Marketplace.

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

As tarefas neste tutorial incluem:

> [!div class="checklist"]
>
> * Criar um pool de hosts na Área de Trabalho Virtual do Windows.
> * Criar um grupo de recursos com VMs em uma assinatura do Azure.
> * Associar as VMs ao domínio do Active Directory.
> * Registrar as VMs com a Área de Trabalho Virtual do Windows.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Um locatário na área de trabalho virtual. Um [tutorial](tenant-setup-azure-active-directory.md) anterior cria um locatário.
* [Módulo do PowerShell de área de trabalho virtual do Windows](/powershell/windows-virtual-desktop/overview/).

Quando você tiver esse módulo, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Execute a oferta do Azure Marketplace para provisionar um novo pool de hosts

Para executar a oferta do Azure Marketplace para provisionar um novo pool de hosts:

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
1. Insira **Área de Trabalho Virtual do Windows** na janela de pesquisa do Marketplace.
1. Selecione **Área de Trabalho Virtual do Windows – Provisionar um pool de hosts** e selecione **Criar**.

Depois disso, siga as instruções na próxima seção para inserir as informações para as guias apropriadas.

### <a name="basics"></a>Noções básicas

Veja o que você faz para a guia **noções básicas** :

1. Selecione uma **Assinatura**.
1. Para **grupo de recursos**, selecione **criar novo** e forneça um nome para o novo grupo de recursos.
1. Selecione uma **região**.
1. Insira um nome para o pool de hosts que é exclusivo no locatário da área de trabalho virtual do Windows.
1. Selecione **tipo de área de trabalho**. Se você selecionar **pessoal**, cada usuário que se conecta a esse pool de hosts é permanentemente atribuído a uma máquina virtual.
1. Insira os usuários que podem entrar nos clientes de área de trabalho virtual do Windows e acessar uma área de trabalho. Use uma lista separada por vírgulas. Por exemplo, se você quiser atribuir `user1@contoso.com` e `user2@contoso.com` acesso, digite *`user1@contoso.com,user2@contoso.com`*
1. Para **local de metadados de serviço**, selecione o mesmo local que a rede virtual que tem conectividade com o servidor de Active Directory.

   >[!IMPORTANT]
   >Se você estiver usando uma solução Azure Active Directory Domain Services pura (Azure AD DS) e Azure Active Directory (Azure AD), certifique-se de implantar o pool de hosts na mesma região que o seu AD DS do Azure para evitar erros de associação de domínio e de credenciais.

1. Selecione **Avançar: configurar máquinas virtuais**.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Para a guia **configurar máquinas virtuais** :

1. Aceite os padrões ou personalize o número e o tamanho das máquinas virtuais.

    >[!NOTE]
    >Se o tamanho específico da máquina virtual que você está procurando não aparecer no seletor de tamanho, isso é porque ainda não o integramos à ferramenta do Azure Marketplace. Para solicitar um tamanho, crie uma solicitação ou vote em uma solicitação existente no [Fórum de UserVoice da área de trabalho virtual do Windows](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Digite um prefixo para os nomes das máquinas virtuais. Por exemplo, se você inserir o *prefixo*, as máquinas virtuais serão chamadas de **prefixo-0**, **prefixo-1**e assim por diante.
1. Selecione **Avançar: configurações da máquina virtual**.

### <a name="virtual-machine-settings"></a>Configurações da máquina virtual

Para a guia **configurações de máquina virtual** :

1. Para **Origem da imagem**, selecione a origem e insira as informações apropriadas para saber como encontrá-las e como armazená-las. As opções são diferentes para o armazenamento de BLOBs, a imagem gerenciada e a galeria.

   Se você optar por não usar discos gerenciados, selecione a conta de armazenamento que contém o arquivo *. vhd* .
1. Insira o nome principal do usuário e a senha. Essa conta deve ser a conta de domínio que adicionará as máquinas virtuais ao domínio Active Directory. Esse mesmo nome de usuário e senha será criado nas máquinas virtuais como uma conta local. É possível redefinir essas contas locais mais tarde.

   >[!NOTE]
   > Se você estiver unindo suas máquinas virtuais a um ambiente de AD DS do Azure, verifique se o usuário ingressar no domínio é membro do [grupo de administradores de DC do AAD](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > A conta também deve fazer parte do Azure AD DS domínio gerenciado ou locatário do Azure AD. Contas de diretórios externos associadas ao seu locatário do Azure AD não podem ser autenticadas corretamente durante o processo de ingresso no domínio.

1. Selecione a **rede virtual** que tem conectividade com o servidor de Active Directory e, em seguida, escolha uma sub-rede para hospedar as máquinas virtuais.
1. Selecione **Avançar: informações da área de trabalho virtual do Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informações de locatário da Área de Trabalho Virtual do Windows

Para a guia **informações de locatário da área de trabalho virtual do Windows** :

1. Para **Nome do grupo de locatário da Área de Trabalho Virtual do Windows**, digite o nome do grupo de locatário que contém seu locatário. Deixe-o como o padrão, a menos que você tenha recebido um nome de grupo de locatário específico.
1. Para **Nome de locatário da Área de Trabalho Virtual do Windows**, digite o nome do locatário no qual você criará esse pool de hosts.
1. Especifique o tipo de credenciais que você deseja usar para autenticar como o proprietário RDS do locatário da Área de Trabalho Virtual do Windows. Insira o UPN ou a entidade de serviço e uma senha.

   Se você concluiu o [Criar entidades de serviço e atribuições de função com o PowerShell](./create-service-principal-role-powershell.md), selecione **Entidade de Serviço**.

1. Para a **entidade de serviço**, para a ID de **locatário do Azure ad**, insira a conta de administrador de locatários para a instância do Azure AD que contém a entidade de serviço. Há suporte apenas para entidades de serviço com uma credencial de senha.
1. Selecione **Avançar: revisar + criar**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Conclua a instalação e crie a máquina virtual

Em **examinar e criar**, examine as informações de instalação. Se você precisar alterar algo, volte e faça as alterações. Quando estiver pronto, selecione **criar** para implantar o pool de hosts.

Dependendo de quantas máquinas virtuais você está criando, esse processo pode levar 30 minutos ou mais para ser concluído.

>[!IMPORTANT]
> Para ajudar a proteger seu ambiente de área de trabalho virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 em suas máquinas virtuais. A área de trabalho virtual do Windows não requer uma porta de entrada aberta 3389 para que os usuários acessem as máquinas virtuais do pool de hosts.
>
> Se você precisar abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do acesso just-in-time. Para obter mais informações, consulte [proteger suas portas de gerenciamento com acesso just-in-time](../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Atribuir usuários adicionais ao grupo de aplicativos da área de trabalho

Depois que o Azure Marketplace terminar de criar o pool, você poderá atribuir mais usuários ao grupo de aplicativos da área de trabalho. Se você não quiser adicionar mais, ignore esta seção.

Para atribuir usuários ao grupo de aplicativos da área de trabalho:

1. Abra uma janela do PowerShell.

1. Execute o seguinte comando para entrar no ambiente de área de trabalho virtual do Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Adicione usuários ao grupo de aplicativos da área de trabalho usando este comando:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   O UPN do usuário deve corresponder à identidade do usuário no Azure AD, por exemplo, *user1@contoso.com* . Se você quiser adicionar vários usuários, execute o comando para cada usuário.

Os usuários adicionados ao grupo de aplicativos da área de trabalho podem entrar na área de trabalho virtual do Windows com clientes Área de Trabalho Remota com suporte e ver um recurso para uma área de trabalho de sessão.

Veja os clientes com suporte atuais:

* [Cliente da Área de Trabalho Remota para Windows 7 e Windows 10](connect-windows-7-and-10.md)
* [Cliente Web da Área de Trabalho Virtual do Windows](connect-web.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Você criou um pool de hosts e atribuiu usuários para acessar sua área de trabalho. Você pode preencher o pool de hosts com programas RemoteApp. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira este tutorial:

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)
