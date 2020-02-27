---
title: Gerenciar o DNS para Azure AD Domain Services | Microsoft Docs
description: Saiba como instalar as ferramentas do servidor DNS para gerenciar o DNS para um Azure Active Directory Domain Services domínio gerenciado.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 2694a5f250b746748a1b42ac4d211aa28ef1ebad
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613699"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Administrar o DNS em um domínio Azure AD Domain Services gerenciado

No Azure Active Directory Domain Services (AD DS do Azure), um componente de chave é DNS (resolução de nomes de domínio). O AD DS do Azure inclui um servidor DNS que fornece resolução de nomes para o domínio gerenciado. Esse servidor DNS inclui registros e atualizações de DNS internos para os principais componentes que permitem a execução do serviço.

Ao executar seus próprios aplicativos e serviços, talvez seja necessário criar registros DNS para computadores que não são ingressados no domínio, configurar endereços IP virtuais para balanceadores de carga ou configurar encaminhadores de DNS externos. Os usuários que pertencem ao grupo de *Administradores de DC do AAD* recebem privilégios de administração de DNS no domínio gerenciado AD DS do Azure e podem criar e editar registros DNS personalizados.

Em um ambiente híbrido, as zonas e os registros DNS configurados em um ambiente de AD DS local não são sincronizados com o AD DS do Azure. Para definir e usar suas próprias entradas DNS, crie registros no servidor DNS AD DS do Azure ou use encaminhadores condicionais que apontam para servidores DNS existentes em seu ambiente.

Este artigo mostra como instalar as ferramentas de servidor DNS e usar o console DNS para gerenciar registros no Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, conclua o tutorial para [criar uma VM do Windows Server e associá-la a um domínio gerenciado][create-join-windows-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="install-dns-server-tools"></a>Instalar ferramentas de servidor DNS

Para criar e modificar registros DNS no Azure AD DS, você precisa instalar as ferramentas do servidor DNS. Essas ferramentas podem ser instaladas como um recurso no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas em um cliente Windows, consulte instalar o [ferramentas de administração de servidor remoto (RSAT)][install-rsat].

1. Entre na sua VM de gerenciamento. Para obter as etapas sobre como se conectar usando o portal do Azure, consulte [conectar-se a uma VM do Windows Server][connect-windows-server-vm].
1. Se **Gerenciador do Servidor** não abrir por padrão quando você entrar na VM, selecione o menu **Iniciar** e, em seguida, escolha **Gerenciador do Servidor**.
1. No painel *Dashboard* da janela **Gerenciador do Servidor**, selecione **Adicionar Funções e Recursos**.
1. Na página **Antes de Você Começar** do *Assistente de Adição de Funções e Recursos*, selecione **Avançar**.
1. Para o *Tipo de Instalação*, deixe a opção **Instalação baseada em função ou recurso** marcada e selecione **Avançar**.
1. Na página **seleção de servidor** , escolha a VM atual no pool de servidores, como *MyVM.aaddscontoso.com*, e selecione **Avançar**.
1. Na página **Funções do Servidor**, clique em **Avançar**.
1. Na página **Recursos**, expanda o nó **Ferramentas de Administração de Servidor Remoto** e, em seguida, expanda o nó **Ferramentas de Administração de Funções**. Selecione o recurso **Ferramentas do Servidor DNS** na lista de ferramentas de administração de funções.

    ![Opte por instalar as ferramentas do servidor DNS na lista de ferramentas de administração de função disponíveis](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Na página **Confirmação**, selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas de gerenciamento de Política de Grupo.
1. Quando a instalação do recurso for concluída, selecione **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Abra o console de gerenciamento do DNS para administrar o DNS

Com as ferramentas do servidor DNS instaladas, você pode administrar os registros DNS no domínio gerenciado AD DS do Azure.

> [!NOTE]
> Para administrar o DNS em um domínio gerenciado AD DS do Azure, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Na tela iniciar, selecione **Ferramentas administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada, incluindo o **DNS** instalado na seção anterior. Selecione **DNS** para iniciar o console de gerenciamento do DNS.
1. Na caixa de diálogo **conectar ao servidor DNS** , selecione **o seguinte computador**e, em seguida, insira o nome de domínio DNS do domínio gerenciado, como *aaddscontoso.com*:

    ![Conectar-se ao domínio gerenciado AD DS do Azure no console DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. O console DNS se conecta ao domínio gerenciado AD DS do Azure especificado. Expanda as **zonas de pesquisa direta** ou as **zonas de pesquisa inversa** para criar suas entradas DNS necessárias ou editar os registros existentes, conforme necessário.

    ![Console DNS - administrar domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Ao gerenciar registros usando as ferramentas do servidor DNS, certifique-se de não excluir ou modificar os registros DNS internos que são usados pelo Azure AD DS. Os registros DNS internos incluem registros DNS do domínio, registros de servidor de nome e outros registros usados para a localização do controlador de domínio. Se você modificar esses registros, os serviços de domínio serão interrompidos na rede virtual.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como gerenciar o DNS, veja o [artigo sobre ferramentas de DNS no TechNet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
