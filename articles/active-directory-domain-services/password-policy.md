---
title: Criar e usar políticas de senha nos Serviços de Domínio ad do Azure | Microsoft Docs
description: Saiba como e por que usar políticas de senha de grão fino para proteger e controlar senhas de conta em um domínio gerenciado pelo Azure AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4402c1ce2f051c8d1911e7c0332d4cac787ce1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613204"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Políticas de senha e de bloqueio de conta em domínios gerenciados

Para gerenciar a segurança do usuário no Azure Active Directory Domain Services (Azure AD DS), você pode definir políticas de senha de grão fino que controlam as configurações de bloqueio da conta ou o comprimento mínimo de senha e complexidade. Uma política padrão de senha fina é criada e aplicada a todos os usuários em um domínio gerenciado pelo Azure AD DS. Para fornecer controle granular e atender a necessidades específicas de negócios ou conformidade, políticas adicionais podem ser criadas e aplicadas a grupos específicos de usuários.

Este artigo mostra como criar e configurar uma política de senha de grãofino no Azure AD DS usando o Centro Administrativo do Diretório Ativo.

> [!NOTE]
> As políticas de senha estão disponíveis apenas para domínios gerenciados pelo Azure AD DS criados usando o modelo de implantação do Gerenciador de recursos. Para domínios gerenciados mais antigos criados usando [classic, migre do modelo de rede virtual Classic para o Gerenciador de recursos][migrate-from-classic].

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
  * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
  * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
  * Se necessário, complete o tutorial para [criar e configurar uma instância de Serviços de Domínio do Diretório Ativo do Azure][create-azure-ad-ds-instance].
  * A ocorrência azure AD DS deve ter sido criada usando o modelo de implantação do Gerenciador de recursos. Se necessário, [migre do modelo de rede virtual classicpara o Gerenciador de recursos][migrate-from-classic].
* Uma VM de gerenciamento do Windows Server que é acompanhada pelo domínio gerenciado pelo Azure AD DS.
  * Se necessário, complete o tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="default-password-policy-settings"></a>Configurações de diretiva de senha padrão

As políticas de senha de grão fino (FGPPs) permitem aplicar restrições específicas para políticas de bloqueio de senha e conta a diferentes usuários em um domínio. Por exemplo, para proteger contas privilegiadas, você pode aplicar configurações mais rigorosas de bloqueio de contas do que contas não privilegiadas regulares. Você pode criar vários FGPPs dentro de um domínio gerenciado pelo Azure AD DS e especificar a ordem de prioridade para aplicá-los aos usuários.

As políticas são distribuídas por meio de associação de grupo em um domínio gerenciado pelo Azure AD DS, e quaisquer alterações que você fizer são aplicadas no próximo login do usuário. Alterar a diretiva não desbloqueia uma conta de usuário que já está bloqueada.

As políticas de senha se comportam um pouco diferente, dependendo de como a conta de usuário a que são aplicadas foi criada. Existem duas maneiras de criar uma conta de usuário no Azure AD DS:

* A conta de usuário pode ser sincronizada a partir do Azure AD. Isso inclui contas de usuário somente em nuvem criadas diretamente no Azure e contas de usuários híbridos sincronizadas a partir de um ambiente AD DS no local usando o Azure AD Connect.
    * A maioria das contas de usuário no Azure AD DS são criadas através do processo de sincronização do Azure AD.
* A conta de usuário pode ser criada manualmente em um domínio gerenciado pelo Azure AD DS e não existe no Azure AD.

Todos os usuários, independentemente de como são criados, têm as seguintes políticas de bloqueio de conta aplicadas pela política de senha padrão no Azure AD DS:

* **Duração do bloqueio da conta:** 30
* **Número de tentativas de logon fracassadas permitidas:** 5
* **Redefinir a contagem de tentativas de logon com falha após:** 30 minutos
* **Idade máxima de senha (vida útil):** 90 dias

Com essas configurações padrão, as contas de usuário ficam bloqueadas por 30 minutos se cinco senhas inválidas forem usadas dentro de 2 minutos. As contas são desbloqueadas automaticamente depois de 30 minutos.

Os bloqueios de conta só ocorrem dentro do domínio gerenciado. As contas de usuário só são bloqueadas no Azure AD DS, e somente devido a tentativas de login falhadas contra o domínio gerenciado. As contas de usuário sincronizadas no Azure AD ou no local não estão bloqueadas em seus diretórios de origem, apenas no Azure AD DS.

Se você tiver uma política de senha AD do Azure que especifica uma idade máxima de senha superior a 90 dias, essa idade de senha será aplicada à política padrão no Azure AD DS. Você pode configurar uma política de senha personalizada para definir uma idade máxima de senha diferente no Azure AD DS. Tome cuidado se você tiver uma idade máxima de senha mais curta configurada em uma política de senha AD DS do que no Azure AD ou em um ambiente AD DS no local. Nesse cenário, a senha de um usuário pode expirar no Azure AD DS antes que eles sejam solicitados a mudar no Azure AD ou em um ambiente AD DS no local.

Para contas de usuário criadas manualmente em um domínio gerenciado pelo Azure AD DS, as seguintes configurações adicionais de senha também são aplicadas a partir da diretiva padrão. Essas configurações não se aplicam a contas de usuário sincronizadas no Azure AD, pois um usuário não pode atualizar sua senha diretamente no Azure AD DS.

* **Comprimento mínimo de senha (caracteres):** 7
* **As senhas devem atender a requisitos de complexidade**

Você não pode modificar as configurações de bloqueio de conta ou senha na diretiva de senha padrão. Em vez disso, os membros do grupo *Administradores AAD DC* podem criar políticas de senha personalizadas e configurá-las para substituir (ter precedência sobre) a diretiva incorporada padrão, conforme mostrado na próxima seção.

## <a name="create-a-custom-password-policy"></a>Crie uma política de senha personalizada

À medida que você cria e executa aplicativos no Azure, você pode querer configurar uma política de senha personalizada. Por exemplo, você pode criar uma diretiva para definir diferentes configurações de diretiva de bloqueio de conta.

As políticas de senha personalizadas são aplicadas a grupos em um domínio gerenciado pelo Azure AD DS. Essa configuração substitui efetivamente a política padrão.

Para criar uma política de senha personalizada, você usa as Ferramentas Administrativas do Diretório Ativo a partir de uma VM aderida por domínio. O Centro Administrativo do Diretório Ativo permite visualizar, editar e criar recursos em um domínio gerenciado pelo Azure AD DS, incluindo UOs.

> [!NOTE]
> Para criar uma política de senha personalizada em um domínio gerenciado pelo Azure AD DS, você deve estar conectado a uma conta de usuário que seja membro do grupo *Administradores AAD DC.*

1. Na tela Iniciar, selecione **Ferramentas Administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada que foram instaladas no tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
1. Para criar e gerenciar UUs, selecione **Centro Administrativo de Diretório Ativo** na lista de ferramentas administrativas.
1. No painel esquerdo, escolha o domínio gerenciado do Azure AD DS, como *aaddscontoso.com*.
1. Abra o **recipiente do sistema** e, em seguida, o contêiner **configurações de senha**.

    Uma política de senha incorporada para o domínio Gerenciado pelo Azure AD DS é mostrada. Você não pode modificar essa política incorporada. Em vez disso, crie uma diretiva de senha personalizada para substituir a política padrão.

    ![Crie uma política de senha no Centro Administrativo do Diretório Ativo](./media/password-policy/create-password-policy-adac.png)

1. No painel **Tarefas** à direita, selecione **Configurações de senha de nova >**.
1. Na caixa de diálogo **Criar configurações de senha,** digite um nome para a diretiva, como *MyCustomFGPP*.
1. Quando existem várias políticas de senha, a política com maior precedência, ou prioridade, é aplicada a um usuário. Quanto menor o número, maior a prioridade. A política de senha padrão tem uma prioridade de *200*.

    Defina a precedência de sua política de senha personalizada para substituir o padrão, como *1*.

1. Editar outras configurações de diretiva de senha conforme desejado. Lembre-se dos seguintes pontos-chave:

    * Configurações como complexidade de senha, idade ou tempo de expiração apenas para usuários criados manualmente em um domínio gerenciado pelo Azure AD DS.
    * As configurações de bloqueio de conta se aplicam a todos os usuários, mas só fazem efeito dentro do domínio gerenciado e não no próprio Azure AD.

    ![Crie uma política personalizada de senha de grãas finas](./media/how-to/custom-fgpp.png)

1. Desmarcar **Proteger contra exclusão acidental**. Se esta opção estiver selecionada, você não poderá salvar o FGPP.
1. Na **seção Aplicar diretamente para,** selecione o botão **Adicionar.** Na caixa de diálogo **Selecionar usuários ou grupos,** selecione o botão **Locais.**

    ![Selecione os usuários e grupos para aplicar a política de senha s](./media/how-to/fgpp-applies-to.png)

1. As políticas de senha só podem ser aplicadas a grupos. Na caixa de diálogo **Locais,** expanda o nome de domínio, como *aaddscontoso.com,* e selecione um OU, como **Usuários AADDC**. Se você tiver um OU personalizado que contenha um grupo de usuários que deseja aplicar, selecione esse OU.

    ![Selecione o OU ao qual o grupo pertence](./media/how-to/fgpp-container.png)

1. Digite o nome do grupo ao que deseja aplicar a diretiva e, em seguida, **selecione ''''Marcar nomes'** para validar a existência do grupo.

    ![Pesquise e selecione o grupo para aplicar o FGPP](./media/how-to/fgpp-apply-group.png)

1. Com o nome do grupo que você selecionou agora exibido na seção **Aplicar diretamente para,** selecione **OK** para salvar sua política de senha personalizada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre políticas de senha e usar o Active Directory Administration Center, consulte os seguintes artigos:

* [Saiba mais sobre políticas de senhas com grãos finos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configure políticas de senha de grãofino usando o AD Administration Center](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
