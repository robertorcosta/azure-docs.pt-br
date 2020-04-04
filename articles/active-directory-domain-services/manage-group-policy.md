---
title: Criar e gerenciar a política de grupo nos Serviços de Domínio Ad do Azure | Microsoft Docs
description: Aprenda a editar os GPOs (Group Policy Objects, objetos de diretiva de grupo incorporados) e criar suas próprias políticas personalizadas em um domínio gerenciado pelo Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 742d716ecdfff6ab67dedc281aa6134020f57add
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655044"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Administrar a política do grupo em um domínio azure AD Domain Services

As configurações para objetos de usuário e computador no Azure Active Directory Domain Services (Azure AD DS) são frequentemente gerenciadas usando GPOs (Group Policy Objects, objetos de política de grupo). O Azure AD DS inclui GPOs incorporados para os *contêineres AADDC Users* e *AADDC Computers.* Você pode personalizar esses GPOs incorporados para configurar a Diretiva de Grupo conforme necessário para o seu ambiente. Os membros do grupo de *administradores azure AD DC* têm privilégios de administração de diretiva de grupo no domínio Azure AD DS, e também podem criar GPOs personalizados e unidades organizacionais (OUs). Mais informações sobre o que é política de grupo e como ela funciona, consulte [visão geral da Política de Grupo][group-policy-overview].

Em um ambiente híbrido, as políticas de grupo configuradas em um ambiente AD DS no local não são sincronizadas com o Azure AD DS. Para definir as configurações de configuração para usuários ou computadores no Azure AD DS, edite um dos GPOs padrão ou crie um GPO personalizado.

Este artigo mostra como instalar as ferramentas de gerenciamento de políticas de grupo, em seguida, editar os GPOs incorporados e criar GPOs personalizados.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, complete o tutorial para [criar e configurar uma instância de Serviços de Domínio do Diretório Ativo do Azure][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é acompanhada pelo domínio gerenciado pelo Azure AD DS.
    * Se necessário, complete o tutorial para [criar uma VM do Windows Server e junte-a a um domínio gerenciado][create-join-windows-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

> [!NOTE]
> Você pode usar modelos administrativos de políticas de grupo copiando os novos modelos para a estação de trabalho de gerenciamento. Copie os arquivos *.admx* `%SYSTEMROOT%\PolicyDefinitions` e copie os arquivos *.adml* específicos do local para `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`, onde `Language-CountryRegion` corresponda ao idioma e região dos arquivos *.adml.*
>
> Por exemplo, copie a versão em inglês, dos Estados `\en-us` Unidos dos arquivos *.adml* na pasta.
>
> Alternativamente, você pode armazenar centralmente seu Modelo Administrativo de Política de Grupo nos controladores de domínio que fazem parte do domínio gerenciado pelo Azure AD DS. Para obter mais informações, consulte [Como criar e gerenciar a Loja Central para Modelos Administrativos de Políticas de Grupo no Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Instale ferramentas de gerenciamento de políticas de grupo

Para criar e configurar GPOs (Group Policy Object, objeto de política de grupo), você precisa instalar as ferramentas de Gerenciamento de Políticas de Grupo. Essas ferramentas podem ser instaladas como um recurso no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas em um cliente Windows, consulte instalar [o RSAT (Remote Server Administration Tools, ferramentas de administração de servidores remotos).][install-rsat]

1. Faça login na sua VM de gestão. Para obter etapas sobre como se conectar usando o portal Azure, consulte [Conecte-se a uma VM do Servidor Windows][connect-windows-server-vm].
1. O **Gerenciador do Servidor** deve abrir por padrão ao entrar na VM. Caso contrário, no menu **Iniciar**, selecione **Gerenciador do Servidor**.
1. No painel *Dashboard* da janela **Gerenciador do Servidor**, selecione **Adicionar Funções e Recursos**.
1. Na página **Antes de Você Começar** do *Assistente de Adição de Funções e Recursos*, selecione **Avançar**.
1. Para o *Tipo de Instalação*, deixe a opção **Instalação baseada em função ou recurso** marcada e selecione **Avançar**.
1. Na página **Seleção do servidor,** escolha a VM atual no pool de servidores, como *myvm.aaddscontoso.com,* e selecione **Next**.
1. Na página **Funções do Servidor**, clique em **Avançar**.
1. Na página **Recursos**, selecione o recurso **Gerenciamento de Política de Grupo**.

    ![Instale a 'Gerenciamento de políticas de grupo' na página Recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na página **Confirmação**, selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas de Gerenciamento de Políticas de Grupo.
1. Quando a instalação do recurso for concluída, selecione **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Abra o Console de Gerenciamento de Políticas de Grupo e edite um objeto

Os GPOs (Default Group Policy Objects, na diretiva de grupo padrão) existem para usuários e computadores em um domínio gerenciado pelo Azure AD DS. Com o recurso De gerenciamento de políticas de grupo instalado na seção anterior, vamos visualizar e editar um GPO existente. Na próxima seção, você cria um GPO personalizado.

> [!NOTE]
> Para administrar a política de grupo em um domínio gerenciado pelo Azure AD DS, você deve estar conectado a uma conta de usuário que seja membro do grupo *Administradores AAD DC.*

1. Na tela Iniciar, selecione **Ferramentas Administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada, incluindo o **Gerenciamento de Políticas de Grupo** instalado na seção anterior.
1. Para abrir o GPMC (Group Policy Management Console, console de gerenciamento de políticas de grupo), escolha **gerenciamento de políticas de grupo**.

    ![O Console de Gerenciamento de Políticas de Grupo abre pronto para editar objetos de diretiva de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Existem dois GPOs (Group Policy Objects, objetos de diretiva de grupo) incorporados em um domínio gerenciado pelo Azure AD DS - um para o contêiner *AADDC Computers* e outro para o contêiner *AADDC Users.* Você pode personalizar esses GPOs para configurar a política de grupo conforme necessário no domínio gerenciado pelo Azure AD DS.

1. No console **De Gestão de Políticas de Grupo,** expanda o nó **Floresta: aaddscontoso.com.** Em seguida, expanda os **nós Domínios.**

    Existem dois contêineres incorporados para *computadores AADDC* e *usuários AADDC*. Cada um desses recipientes tem um GPO padrão aplicado a eles.

    ![GPOs incorporados aplicados aos contêineres 'Computadores AADDC' e 'Usuários AADDC' padrão](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Esses GPOs incorporados podem ser personalizados para configurar políticas de grupo específicas no domínio gerenciado pelo Azure AD DS. Selecione um dos GPOs, como *o AADDC Computers GPO,* e escolha **Editar...**.

    ![Escolha a opção de 'Editar' um dos GPOs incorporados](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. A ferramenta Group Policy Management Editor é aberta para permitir que você personalize o GPO, como *Políticas de conta*:

    ![Personalize GPO para configurar as configurações conforme necessário](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Quando estiver pronto, escolha **'> salvar para** salvar a diretiva.' Os computadores atualizam a Diretiva de Grupo por padrão a cada 90 minutos e aplicam as alterações feitas.

## <a name="create-a-custom-group-policy-object"></a>Crie um objeto de política de grupo personalizado

Para agrupar configurações de diretiva semelhantes, muitas vezes você cria GPOs adicionais em vez de aplicar todas as configurações necessárias no GPO único e padrão. Com o Azure AD DS, você pode criar ou importar seus próprios objetos de política de grupo personalizados e vinculá-los a um OU personalizado. Se você precisar primeiro criar um OU personalizado, consulte [criar um OU personalizado em um domínio gerenciado pelo Azure AD DS](create-ou.md).

1. No console **De gestão de políticas de grupo,** selecione sua unidade organizacional personalizada (OU), como *MyCustomOU*. Selecione o OU e escolha **Criar um GPO neste domínio e vinculá-lo aqui...**:

    ![Crie um GPO personalizado no console de gerenciamento de políticas de grupo](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Especifique um nome para o novo GPO, como *My custom GPO*, em seguida, selecione **OK**. Você pode, opcionalmente, basear este GPO personalizado em um GPO existente e no conjunto de opções de política.

    ![Especifique um nome para o novo GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. O GPO personalizado é criado e vinculado ao seu OU personalizado. Para agora configurar as configurações de diretiva, selecione à direita o GPO personalizado e escolha **Editar...**:

    ![Escolha a opção para 'Editar' seu GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. O **Editor de Gerenciamento de Políticas de Grupo** é aberto para permitir que você personalize o GPO:

    ![Personalize GPO para configurar as configurações conforme necessário](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Quando estiver pronto, escolha **'> salvar para** salvar a diretiva.' Os computadores atualizam a Diretiva de Grupo por padrão a cada 90 minutos e aplicam as alterações feitas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as configurações de diretiva de grupo disponíveis que você pode configurar usando o Console de Gerenciamento de Políticas de Grupo, consulte [Trabalhar com itens de preferência de diretiva de grupo][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
