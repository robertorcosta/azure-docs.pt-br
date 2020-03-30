---
title: Tutorial - Crie uma confiança florestal nos serviços de domínio azure AD | Microsoft Docs
description: Aprenda a criar uma floresta de saída única para um domínio AD DS no portal Azure para serviços de domínio Azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5620d1cdc7dc71bdac17057b9a13a74150b12d5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612521"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Tutorial: Crie uma confiança florestal de saída para um domínio local no Azure Active Directory Domain Services (visualização)

Em ambientes onde você não pode sincronizar hashes de senha, ou você tem usuários que fazem login exclusivamente usando cartões inteligentes para que eles não saibam sua senha, você pode usar uma floresta de recursos no Azure Active Directory Domain Services (AD DS). Uma floresta de recursos usa um fundo de saída único do Azure AD DS para um ou mais ambientes AD DS no local. Essa relação de confiança permite que usuários, aplicativos e computadores sejam autenticados contra um domínio local do domínio gerenciado pelo Azure AD DS. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

![Diagrama da confiança florestal do Azure AD DS para o AD DS no local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configure o DNS em um ambiente AD DS local para suportar a conectividade Azure AD DS
> * Crie uma confiança florestal de entrada unidirecional em um ambiente AD DS no local
> * Crie uma confiança florestal de saída única no Azure AD DS
> * Teste e valide a relação de confiança para autenticação e acesso a recursos

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado de domínio do Azure Active Directory Domain services criado usando uma floresta de recursos e configurado no seu inquilino Azure AD.
    * Se necessário, [crie e configure uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Certifique-se de criar um domínio gerenciado pelo Azure AD DS usando uma floresta *de recursos.* A opção padrão cria uma floresta *de usuários.* Apenas florestas de recursos podem criar trusts para ambientes AD DS on-prem. Você também precisa usar um mínimo de *Enterprise* SKU para o seu domínio gerenciado. Se necessário, [altere o SKU para um domínio gerenciado pelo Azure AD DS][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você cria e configura o fundo florestal de saída do Azure AD DS usando o portal Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerações de rede

A rede virtual que hospeda o azure AD DS precisa de conectividade de rede ao seu Active Directory no local. Aplicativos e serviços também precisam de conectividade de rede para a rede virtual que hospeda a floresta de recursos Azure AD DS. A conectividade de rede à floresta de recursos Azure AD DS deve estar sempre acesa e estável, caso contrário, os usuários podem não autenticar ou acessar recursos.

Antes de configurar uma confiança florestal no Azure AD DS, certifique-se de que sua rede entre o Azure e o ambiente local atenda aos seguintes requisitos:

* Use endereços IP privados. Não confie no DHCP com atribuição dinâmica de endereço IP.
* Evite sobrepor espaços de endereçoip para permitir que peering de rede virtual e roteamento se comuniquem com sucesso entre o Azure e os locais.
* Uma rede virtual do Azure precisa de uma sub-rede de gateway para configurar uma conexão VPN ou ExpressRoute de site para site (Site-to-site) ou ExpressRoute
* Crie sub-redes com endereços IP suficientes para suportar seu cenário.
* Certifique-se de que o Azure AD DS tenha sua própria sub-rede, não compartilhe essa sub-rede virtual com vMs e serviços de aplicativos.
* As redes virtuais peered NÃO são transitivas.
    * Os peerings de rede virtual do Azure devem ser criados entre todas as redes virtuais que você deseja usar o azure AD DS resource forest trust para o ambiente AD DS local.
* Forneça conectividade contínua de rede à floresta de diretórios ativos no local. Não use conexões demanda.
* Certifique-se de que há resolução contínua de nomes (DNS) entre o nome da floresta de recursos Azure AD DS e o nome da floresta do Active Directory no local.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configure o DNS no domínio local

Para resolver corretamente o domínio gerenciado pelo Azure AD DS a partir do ambiente local, talvez seja necessário adicionar encaminhadores aos servidores DNS existentes. Se você não configurar o ambiente local para se comunicar com o domínio gerenciado pelo Azure AD DS, complete as seguintes etapas de uma estação de trabalho de gerenciamento para o domínio AD DS no local:

1. Selecione **Start | Ferramentas Administrativas | DNS**
1. Servidor DNS de direita, como *myAD01*, selecione **Propriedades**
1. Escolha **encaminhados**e **edite** para adicionar atacantes adicionais.
1. Adicione os endereços IP do domínio gerenciado pelo Azure AD DS, como *10.0.1.4* e *10.0.1.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Criar confiança florestal de entrada no domínio local

O domínio AD DS no local precisa de um fundo florestal de entrada para o domínio gerenciado do Azure AD DS. Essa confiança deve ser criada manualmente no domínio AD DS no local, não pode ser criada a partir do portal Azure.

Para configurar a confiança de entrada no domínio AD DS no local, complete as seguintes etapas de uma estação de trabalho de gerenciamento para o domínio AD DS no local:

1. Selecione **Start | Ferramentas Administrativas | Domínios e trustções de diretórios ativos**
1. Domínio de seleção de direito, como *onprem.contoso.com,* selecione **Propriedades**
1. Escolha a guia **Trusts** e, em seguida, **New Trust**

   > [!NOTE]
   > Se você não ver a opção de menu **Trusts,** verifique em **Propriedades** para o *tipo Floresta*. Apenas florestas *de recursos* podem criar trusts. Se o tipo de floresta é *Usuário,* você não pode criar confianças. Atualmente, não há como mudar o tipo florestal de um domínio gerenciado pelo Azure AD DS. Você precisa excluir e recriar o domínio gerenciado como uma floresta de recursos.

1. Digite o nome no nome de domínio Azure AD DS, como *aaddscontoso.com,* em seguida, selecione **Next**
1. Selecione a opção para criar um **fundo Forest**e, em seguida, crie uma **única maneira: confiança recebida.**
1. Escolha criar a confiança apenas para **Este domínio**. Na etapa seguinte, você cria a confiança no portal Azure para o domínio gerenciado do Azure AD DS.
1. Escolha usar a **autenticação em toda a floresta**e, em seguida, digite e confirme uma senha de confiança. Essa mesma senha também é inserida no portal Azure na próxima seção.
1. Passe pelas próximas janelas com opções padrão e escolha a opção de **Não, não confirme a confiança de saída**.
1. Selecionar **Concluir**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Criar confiança florestal de saída no Azure AD DS

Com o domínio AD DS no local configurado para resolver o domínio gerenciado do Azure AD DS e um fundo florestal de entrada criado, agora criou o fundo florestal de saída. Essa confiança florestal de saída completa a relação de confiança entre o domínio AD DS no local e o domínio gerenciado pelo Azure AD DS.

Para criar a confiança de saída para o domínio gerenciado pelo Azure AD DS no portal Azure, complete as seguintes etapas:

1. No portal Azure, procure e selecione **Os Serviços de Domínio Azure AD**e selecione seu domínio gerenciado, como *aaddscontoso.com*
1. No menu no lado esquerdo do domínio gerenciado pelo Azure AD DS, selecione **Trusts**e escolha **+ Adicione** uma confiança.
1. Digite um nome de exibição que identifique sua confiança e, em seguida, o nome de DNS da floresta confiável no local, como *onprem.contoso.com*
1. Forneça a mesma senha de confiança usada ao configurar o fundo florestal de entrada para o domínio AD DS no local na seção anterior.
1. Forneça pelo menos dois servidores DNS para o domínio AD DS no local, como *10.0.2.4* e *10.0.2.5*
1. Quando estiver pronto, **salve** a confiança da floresta de saída

    [Crie confiança florestal de saída no portal Azure](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Validar a autenticação de recursos

Os seguintes cenários comuns permitem validar que a confiança na floresta autentica corretamente os usuários e o acesso aos recursos:

* [Autenticação do usuário no local da floresta de recursos Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Recursos de acesso na floresta de recursos Azure AD DS usando usuário local](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Habilite o compartilhamento de arquivos e impressoras](#enable-file-and-printer-sharing)
    * [Crie um grupo de segurança e adicione membros](#create-a-security-group-and-add-members)
    * [Crie um compartilhamento de arquivos para acesso a florestas cruzadas](#create-a-file-share-for-cross-forest-access)
    * [Validar a autenticação entre florestas em um recurso](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticação do usuário no local da floresta de recursos Azure AD DS

Você deve ter a máquina virtual do Windows Server juntada ao domínio de recursos Azure AD DS. Use esta máquina virtual para testar seu usuário local pode autenticar em uma máquina virtual.

1. Conecte-se ao Windows Server VM junto à floresta de recursos Azure AD DS usando o Remote Desktop e suas credenciais de administrador AD DS do Azure. Se você tiver um erro nla (CLevel) de autenticação de nível de rede, verifique se a conta de usuário usada não é uma conta de usuário de domínio.

    > [!NOTE]
    > Para se conectar com segurança às suas VMs unidas aos Serviços de Domínio Azure AD, você pode usar o [Serviço de Host do Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) em regiões Azure suportadas.

1. Abra um prompt de `whoami` comando e use o comando para mostrar o nome distinto do usuário autenticado no momento:

    ```console
    whoami /fqdn
    ```

1. Use `runas` o comando para autenticar como usuário do domínio local. No comando seguinte, `userUpn@trusteddomain.com` substitua-o pelo UPN de um usuário do domínio in-premises confiável. O comando solicita a senha do usuário:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se a autenticação for um sucesso, um novo prompt de comando será aberto. O título do novo prompt `running as userUpn@trusteddomain.com`de comando inclui .
1. Use `whoami /fqdn` no novo prompt de comando para exibir o nome distinto do usuário autenticado do Diretório Ativo no local.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Recursos de acesso na floresta de recursos Azure AD DS usando usuário local

Usando o Windows Server VM unido à floresta de recursos Azure AD DS, você pode testar o cenário em que os usuários podem acessar recursos hospedados na floresta de recursos quando autenticam a partir de computadores no domínio local com usuários do domínio local. Os exemplos a seguir mostram como criar e testar vários cenários comuns.

#### <a name="enable-file-and-printer-sharing"></a>Habilite o compartilhamento de arquivos e impressoras

1. Conecte-se ao Windows Server VM junto à floresta de recursos Azure AD DS usando o Remote Desktop e suas credenciais de administrador AD DS do Azure. Se você tiver um erro nla (CLevel) de autenticação de nível de rede, verifique se a conta de usuário usada não é uma conta de usuário de domínio.

    > [!NOTE]
    > Para se conectar com segurança às suas VMs unidas aos Serviços de Domínio Azure AD, você pode usar o [Serviço de Host do Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) em regiões Azure suportadas.

1. Abra **as configurações do Windows**e, em seguida, procure e selecione Rede e Centro de **Compartilhamento**.
1. Escolha a opção Alterar configurações **avançadas de compartilhamento.**
1. No **perfil de domínio,** **selecione Ativar compartilhamento de arquivos e impressoras** e, em seguida, **Salvar alterações**.
1. Fechar **o Centro de Rede e Compartilhamento**.

#### <a name="create-a-security-group-and-add-members"></a>Crie um grupo de segurança e adicione membros

1. Abra **Usuários e Computadores do Active Directory**.
1. Selecione o nome de domínio, escolha **Novo**e selecione **Unidade Organizacional**.
1. Na caixa nome, digite *LocalObjects*e selecione **OK**.
1. Selecione e clique com o botão direito do **mouse LocalObjects** no painel de navegação. Selecione **Novo** e, em seguida, **Grupo**.
1. Digite *FileServerAccess* na caixa **nome do grupo.** Para o **escopo de grupo,** selecione **Domínio local**e escolha **OK**.
1. No painel de conteúdo, clique duas vezes **em FileServerAccess**. Selecione **Membros,** escolha **adicionar**e selecione **Locais**.
1. Selecione seu diretório ativo no local na exibição **Local** e escolha **OK**.
1. Digite *Os Usuários de Domínio* no Inserir os nomes do objeto para selecionar **a** caixa. Selecione **'''Nomes de verificação',** forneça credenciais para o Diretório Ativo no local e selecione **OK**.

    > [!NOTE]
    > Você deve fornecer credenciais porque a relação de confiança é apenas uma maneira. Isso significa que os usuários do Azure AD DS não podem acessar recursos ou procurar usuários ou grupos no domínio confiável (on-premises).

1. O grupo Usuários de **Domínio** do seu Diretório Ativo no local deve ser um membro do grupo **FileServerAccess.** Selecione **OK** para salvar o grupo e feche a janela.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Crie um compartilhamento de arquivos para acesso a florestas cruzadas

1. No Windows Server VM juntou-se à floresta de recursos Azure AD DS, crie uma pasta e forneça nomes como *CrossForestShare*.
1. Selecione à direita a pasta e escolha **Propriedades**.
1. Selecione a guia **Segurança** e, em seguida, escolha **Editar**.
1. Na caixa *de diálogo Permissões para CrossForestShare,* selecione **Adicionar**.
1. Digite *FileServerAccess* in **Enter the object names to select**, then select **OK**.
1. Selecione *FileServerAccess* na lista **De Grupos ou nomes de usuário.** Na lista **Permissões para FileServerAccess,** escolha *Permitir* as permissões **Modificar** e **Gravar** e, em seguida, selecione **OK**.
1. Selecione a guia **Compartilhar** e, em seguida, escolha **Compartilhamento avançado...**
1. Escolha **Compartilhar esta pasta**e digite um nome memorável para o compartilhamento de arquivos no nome de **compartilhamento,** como *CrossForestShare*.
1. Selecione **Permissões**. Na lista **Permissões para Todos,** escolha **Permitir** a permissão **De alterar.**
1. Selecione **OK** duas vezes e, em seguida, **Feche**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Validar a autenticação entre florestas em um recurso

1. Faça login em um computador Windows junto ao seu Diretório Ativo no local usando uma conta de usuário do seu Diretório Ativo no local.
1. Usando **o Windows Explorer,** conecte-se ao compartilhamento que você `\\fs1.aaddscontoso.com\CrossforestShare`criou usando o nome do host totalmente qualificado e a parte, como .
1. Para validar a permissão de gravação, selecione à direita na pasta, escolha **Novo**e selecione **Documento de texto**. Use o nome padrão **Novo Documento de Texto**.

    Se as permissões de gravação forem definidas corretamente, um novo documento de texto será criado. As etapas a seguir serão abertas, editadas e excluem o arquivo conforme apropriado.
1. Para validar a permissão de leitura, abra **novo documento de texto**.
1. Para validar a permissão de modificação, adicione texto ao arquivo e feche o **Bloco de Notas**. Quando solicitado a salvar alterações, escolha **Salvar**.
1. Para validar a permissão de exclusão, selecione **o novo documento de texto** e escolha **Excluir**. Escolha **Sim** para confirmar a exclusão do arquivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configure o DNS em um ambiente AD DS local para suportar a conectividade Azure AD DS
> * Crie uma confiança florestal de entrada unidirecional em um ambiente AD DS no local
> * Crie uma confiança florestal de saída única no Azure AD DS
> * Teste e valide a relação de confiança para autenticação e acesso a recursos

Para obter informações mais conceituais sobre os tipos de florestas no Azure AD DS, veja quais são as [florestas][concepts-trust] [de recursos?][concepts-forest]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
