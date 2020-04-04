---
title: Criar uma unidade organizacional (OU) no Azure AD Domain Services | Microsoft Docs
description: Saiba como criar e gerenciar uma Unidade Organizacional (OU) personalizada em um domínio gerenciado do Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 61f951c0dd6561fc8d5a5de6b80e3759fd42eb78
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655553"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Criar uma Unidade Organizacional (OU) em um domínio gerenciado do Azure AD Domain Services

As unidades organizacionais (OUs) no Active Directory Domain Services (AD DS) permitem que você agrupe logicamente objetos como contas de usuário, contas de serviço ou contas de computador. Em seguida, você pode atribuir administradores a OUs específicos e aplicar a diretiva de grupo para impor configurações de configuração direcionadas.

Os domínios gerenciados do Azure AD DS incluem os seguintes dois OUs incorporados:

* *AADDC Computers* - contém objetos de computador para todos os computadores que estão unidos ao domínio gerenciado.
* *AADDC Users* - inclui usuários e grupos sincronizados do inquilino Azure AD.

À medida que você cria e executa cargas de trabalho que usam o Azure AD DS, você pode precisar criar contas de serviço para que os aplicativos se autentiquem. Para organizar essas contas de serviço, muitas vezes você cria um OU personalizado no domínio gerenciado pelo Azure AD DS e, em seguida, cria contas de serviço dentro desse OU.

Em um ambiente híbrido, as OUs criadas em um ambiente AD DS no local não são sincronizadas com o Azure AD DS. Os domínios gerenciados do Azure AD DS usam uma estrutura OU plana. Todas as contas e grupos de usuários são armazenados no contêiner *Usuários AADDC,* apesar de serem sincronizados de diferentes domínios ou florestas locais, mesmo se você tiver configurado uma estrutura hierárquica de OU lá.

Este artigo mostra como criar um OU no seu domínio gerenciado pelo Azure AD DS.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, complete o tutorial para [criar e configurar uma instância de Serviços de Domínio do Diretório Ativo do Azure][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é acompanhada pelo domínio gerenciado pelo Azure AD DS.
    * Se necessário, complete o tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Considerações e limitações personalizadas da U

Quando você cria OUs personalizados em um domínio gerenciado pelo Azure AD DS, você ganha flexibilidade de gerenciamento adicional para gerenciamento de usuários e aplicação de política de grupo. Em comparação com um ambiente AD DS no local, existem algumas limitações e considerações ao criar e gerenciar uma estrutura ou personalizada no Azure AD DS:

* Para criar OUs personalizados, os usuários devem ser um membro do grupo *Administradores AAD DC.*
* Um usuário que cria um OU personalizado recebe privilégios administrativos (controle total) sobre esse OU e é o proprietário dos recursos.
    * Por padrão, o grupo *Administradores AAD DC* também tem controle total do OU personalizado.
* Um OU padrão para *usuários AADDC* é criado que contém todas as contas de usuário sincronizadas do seu inquilino Azure AD.
    * Você não pode mover usuários ou grupos do OU usuários do *AADDC* para OUs personalizados que você cria. Apenas contas de usuário ou recursos criados no domínio gerenciado pelo Azure AD DS podem ser movidos para OUs personalizados.
* Contas de usuário, grupos, contas de serviço e objetos de computador que você cria sob OUs personalizados não estão disponíveis no seu inquilino Azure AD.
    * Esses objetos não aparecem usando a API do Microsoft Graph ou na UI AD do Azure; eles só estão disponíveis no seu domínio gerenciado pelo Azure AD DS.

## <a name="create-a-custom-ou"></a>Criar um OU personalizado

Para criar um OU personalizado, você usa as Ferramentas Administrativas do Diretório Ativo a partir de uma VM aderida por domínio. O Centro Administrativo do Diretório Ativo permite visualizar, editar e criar recursos em um domínio gerenciado pelo Azure AD DS, incluindo UOs.

> [!NOTE]
> Para criar um OU personalizado em um domínio gerenciado pelo Azure AD DS, você deve estar conectado a uma conta de usuário que seja membro do grupo *Administradores AAD DC.*

1. Faça login na sua VM de gestão. Para obter etapas sobre como se conectar usando o portal Azure, consulte [Conecte-se a uma VM do Servidor Windows][connect-windows-server-vm].
1. Na tela Iniciar, selecione **Ferramentas Administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada que foram instaladas no tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
1. Para criar e gerenciar UUs, selecione **Centro Administrativo de Diretório Ativo** na lista de ferramentas administrativas.
1. No painel esquerdo, escolha o domínio gerenciado do Azure AD DS, como *aaddscontoso.com*. Uma lista de UOs e recursos existentes é mostrada:

    ![Selecione o domínio gerenciado do Azure AD DS no Centro Administrativo do Diretório Ativo](./media/create-ou/create-ou-adac-overview.png)

1. O painel **Tarefas** é mostrado no lado direito do Centro Administrativo do Diretório Ativo. Sob o domínio, como *aaddscontoso.com,* selecione **Nova > Unidade Organizacional**.

    ![Selecione a opção para criar um novo U no Centro Administrativo do Diretório Ativo](./media/create-ou/create-ou-adac-new-ou.png)

1. Na caixa de diálogo **Criar unidade organizacional,** especifique um **nome** para o novo OU, como *MyCustomOu*. Forneça uma breve descrição para o OU, como *ou personalizado para contas de serviço*. Se desejar, você também pode definir o campo **Gerenciado por** por ou. Para criar o OU personalizado, selecione **OK**.

    ![Crie um U personalizado a partir do Centro Administrativo do Diretório Ativo](./media/create-ou/create-ou-dialog.png)

1. De volta ao Centro Administrativo do Diretório Ativo, o OU personalizado está agora listado e está disponível para uso:

    ![UA personalizado disponível para uso no Centro Administrativo do Diretório Ativo](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar as ferramentas administrativas ou criar e usar contas de serviços, consulte os seguintes artigos:

* [Centro Administrativo do Active Directory: introdução](https://technet.microsoft.com/library/dd560651.aspx)
* [Guia passo a passo de contas de serviço](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
