---
title: Tutorial – Criar uma instância do Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar uma instância Azure Active Directory Domain Services usando o portal do Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 536ada668db724ca50d7db820aff173f7222bab2
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336852"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Tutorial: Criar e configurar uma instância do Azure Active Directory Domain Services

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches aos controladores de domínio por conta própria. O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar os grupos e as contas de usuário existentes para proteger o acesso aos recursos.

Este tutorial mostra como criar e configurar um instância do Azure AD DS usando o portal do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Definir as configurações de rede virtual e DNS para um domínio gerenciado
> * Criar uma instância do Azure AD DS
> * Adicionar usuários administrativos ao gerenciamento de domínio
> * Permitir a sincronização de hash de senha

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.

Embora não seja obrigatório para o Azure AD DS, é recomendável [configurar a SSPR (redefinição de senha por autoatendimento)][configure-sspr] para o locatário do Azure AD. Os usuários podem alterar sua senha sem a SSPR, mas a SSPR ajudará se eles esquecerem sua senha e precisarem redefini-la.

> [!IMPORTANT]
> Depois de criar um domínio gerenciado do Azure AD DS, você não poderá mover a instância para outro grupo de recursos, outra rede virtual, outra assinatura etc. Tome cuidado ao selecionar a assinatura, o grupo de recursos, a região e a rede virtual mais apropriados ao implantar a instância do Azure AD DS.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você criará e configurará uma instância do Azure AD DS usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Criar uma instância e definir as configurações básicas

Para iniciar o assistente **Habilitar Azure AD Domain Services**, conclua as seguintes etapas:

1. No canto superior esquerdo do portal do Azure, selecione **+ Criar um recurso**.
1. Insira *Domain Services* na barra de pesquisa e, em seguida, escolha *Azure AD Domain Services* nas sugestões de pesquisa.
1. Na página Azure AD Domain Services, selecione **Criar**. O assistente **Habilitar Azure AD Domain Services** é iniciado.

Ao criar uma instância do Azure AD DS, você especifica um nome DNS. Eis algumas considerações a serem feitas ao escolher o nome DNS:

* **Nome de domínio interno:** Por padrão, o nome de domínio interno do diretório é usado (com sufixo *.onmicrosoft.com*). Se quiser habilitar o acesso LDAP seguro ao domínio gerenciado pela Internet, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft é proprietária do domínio *.onmicrosoft.com*, portanto, nenhuma AC (Autoridade de Certificação) emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, normalmente um que você já tenha e seja roteável. Quando você usa um domínio roteável personalizado, o tráfego pode fluir corretamente conforme necessário para dar suporte aos seus aplicativos.
* **Sufixos de domínio não roteáveis:** De modo geral, recomendamos que você evite sufixos de nome de domínio não roteáveis, tal como *contoso.local*. O sufixo *.local* não é roteável e pode causar problemas com a resolução do DNS.

> [!TIP]
> Se você criar um nome de domínio personalizado, tome cuidado com os namespaces DNS existentes. É recomendável incluir um prefixo exclusivo para o nome de domínio. Por exemplo, se o nome DNS raiz for *contoso.com*, crie um domínio gerenciado do Azure AD DS com o nome de domínio personalizado *corp.contoso.com* ou *ds.contoso.com*. Em um ambiente híbrido com um ambiente do AD DS local, esses prefixos já podem estar em uso. Use um prefixo exclusivo para o Azure AD DS.
>
> Você pode usar o nome DNS raiz para o domínio gerenciado do Azure AD DS, mas talvez precise criar alguns registros DNS adicionais para outros serviços no ambiente. Por exemplo, se você executar um servidor Web que hospeda um site usando o nome DNS raiz, poderá haver conflitos de nomenclatura que exigem entradas DNS adicionais.
>
> Nesses tutoriais e artigos de instruções, o domínio personalizado *contoso.com* é usado como um breve exemplo. Em todos os comandos, especifique seu próprio nome de domínio, que pode incluir um prefixo exclusivo.
>
> Para obter mais informações, confira [Selecionar um prefixo de nomenclatura para o domínio][naming-prefix].

As seguintes restrições de nome DNS também se aplicam:

* **Restrições do prefixo do domínio:** Você não pode criar um domínio gerenciado com um prefixo de mais de 15 caracteres. O prefixo do nome de domínio especificado (por exemplo, *contoso* no nome de domínio *contoso.com*) deve conter até 15 caracteres.
* **Conflitos de nome de rede:** O nome de domínio DNS para seu domínio gerenciado não deve já existir na rede virtual. Especificamente, verifique os seguintes cenários que podem resultar em conflitos de nome:
    * Se você já tiver um domínio do Active Directory com o mesmo nome de domínio DNS na rede virtual do Azure.
    * Se a rede virtual em que você planeja habilitar o domínio gerenciado tiver uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS na rede local.
    * Se você tiver um serviço de nuvem existente do Azure com esse mesmo nome na rede virtual do Azure.

Preencha os campos na janela *Básico* do portal do Azure para criar uma instância do Azure AD DS:

1. Insira um **nome de domínio DNS** para o domínio gerenciado, levando em consideração os pontos anteriores.
1. Selecione a **Assinatura** do Azure na qual você deseja criar o domínio gerenciado.
1. Selecione o **Grupo de recursos** a que o domínio gerenciado deve pertencer. Escolha **Criar** ou selecione um grupo de recursos existente.
1. Escolha o **Local** do Azure no qual o domínio gerenciado deve ser criado.
1. Clique em **OK** para passar à seção **Rede**.

![Definir as configurações básicas para uma instância do Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

## <a name="create-and-configure-the-virtual-network"></a>Criar e configurar a rede virtual

Para fornecer conectividade, são necessárias uma rede virtual do Azure e uma sub-rede dedicada. O Azure AD DS está habilitado na sub-rede desta rede virtual. Neste tutorial, você criará uma rede virtual, embora possa optar por usar uma rede virtual existente. Em qualquer abordagem, você deve criar uma sub-rede dedicada para uso pelo Azure AD DS.

Algumas considerações para essa sub-rede de rede virtual dedicada incluem as seguintes áreas:

* A sub-rede deve ter pelo menos de 3 a 5 endereços IP disponíveis em seu intervalo de endereços para dar suporte aos recursos do Azure AD DS.
* Não selecione a sub-rede do *Gateway* para implantar o Azure AD DS. Não há suporte para implantar o Azure AD DS em uma sub-rede de *Gateway*.
* Não implante nenhuma outra máquina virtual na sub-rede. Os aplicativos e as VMs geralmente usam grupos de segurança de rede para proteger a conectividade. A execução dessas cargas de trabalho em uma sub-rede separada permite que você aplique esses grupos de segurança de rede sem interromper a conectividade com o domínio gerenciado.
* Você não pode mover o domínio gerenciado para uma rede virtual diferente depois que tiver habilitado o Azure AD DS.

Para obter mais informações sobre como planejar e configurar a rede virtual, confira as [considerações de rede para o Azure Active Directory Domain Services][network-considerations].

Preencha os campos na janela *Rede* da seguinte maneira:

1. Na janela **Rede**, escolha **Selecionar rede virtual**.
1. Para este tutorial, escolha **Criar** rede virtual para implantar o Azure AD DS.
1. Insira um nome para a rede virtual, como *myVnet* e, em seguida, forneça um intervalo de endereços, como *10.1.0.0/16*.
1. Crie uma sub-rede dedicada com um nome claro, como *DomainServices*. Forneça um intervalo de endereços, como *10.1.0.0/24*.

    ![Criar uma rede virtual e uma sub-rede para uso com o Azure AD Domain Services](./media/tutorial-create-instance/create-vnet.png)

    Escolha um intervalo de endereço que esteja dentro do seu intervalo de endereços IP privados. Intervalos de endereços IP que não lhe pertencem e estejam no espaço de endereços públicos causam erros no Azure AD DS.

    > [!TIP]
    > Na página **Escolher rede virtual**, são exibidas as redes virtuais existentes que pertencem ao grupo de recursos e ao local do Azure selecionados anteriormente. É necessário [criar uma sub-rede dedicada][create-dedicated-subnet] antes de implantar O Azure AD DS.

1. Com a rede virtual e a sub-rede criadas, a sub-rede deve ser selecionada automaticamente, tal como *DomainServices*. Em vez disso, você pode escolher outra sub-rede existente que faça parte da rede virtual selecionada:

    ![escolha uma sub-rede dedicada dentro da rede virtual](./media/tutorial-create-instance/choose-subnet.png)

1. Selecione **OK** para confirmar a configuração da rede virtual.

## <a name="configure-an-administrative-group"></a>Configurar um grupo administrativo

Um grupo administrativo especial chamado *Administradores do AAD DC* é usado para o gerenciamento do domínio do Azure AD DS. Os membros desse grupo recebem permissões administrativas nas VMs que estão unidas ao domínio gerenciado. Em VMs unidas ao domínio, esse grupo é adicionado ao grupo de administradores locais. Os membros desse grupo também poderão usar a Área de Trabalho Remota para se conectar remotamente às VMs unidas ao domínio.

Você não tem permissões de *Administrador de Domínio* ou de *Administrador Corporativo* em um domínio gerenciado usando o Azure AD DS. Essas permissões são reservadas pelo serviço e não são disponibilizadas aos usuários dentro do locatário. Em vez disso, o grupo de *Administradores do AAD DC* permite que você execute algumas operações privilegiadas. Essas operações incluem se unir aos computadores no domínio, pertencer ao grupo de administração em VMs unidas ao domínio e configurar a Política de Grupo.

O assistente cria automaticamente o grupo de *Administradores do AAD DC* no diretório do Azure AD. Se houver um grupo existente com esse nome em seu diretório do Azure AD, o assistente o selecionará. Como alternativa, você pode optar por adicionar outros usuários a esse grupo de *Administradores do AAD DC* durante o processo de implantação. Essas etapas podem ser concluídas posteriormente.

1. Para adicionar outros usuários a esse grupo de *Administradores do AAD DC*, selecione **Gerenciar associação ao grupo**.
1. Selecione o botão **Adicionar membros** e procure e selecione usuários do diretório do Azure AD. Por exemplo, pesquise sua própria conta e adicione-a ao grupo de *Administradores do AAD DC*.

    ![Configurar a associação ao grupo do grupo de Administradores do AAD DC](./media/tutorial-create-instance/admin-group.png)

1. Quando terminar, selecione **OK**.

## <a name="configure-synchronization"></a>Configurar sincronização

O Azure AD DS permite que você sincronize *todos* os usuários e grupos disponíveis no Azure AD ou uma sincronização *com escopo* incluindo apenas grupos específicos. Se você optar por sincronizar *todos* os usuários e grupos, não será possível optar por executar apenas uma sincronização com escopo no futuro. Para mais informações sobre a sincronização com escopo, confira [Sincronização com do Azure AD Domain Services][scoped-sync].

1. Para este tutorial, escolha sincronizar **Todos** os usuários e grupos. Essa opção de sincronização é a opção padrão.

    ![Execute uma sincronização completa de usuários e grupos do Azure AD](./media/tutorial-create-instance/sync-all.png)

1. Selecione **OK**.

## <a name="deploy-your-managed-domain"></a>Implantar o domínio gerenciado

Na página **Resumo** do assistente, examine as definições de configuração do domínio gerenciado. Você pode voltar para qualquer etapa do assistente para efetuar alterações.

1. Para criar o domínio gerenciado, selecione **Ok**.
1. O processo de provisionamento de seu domínio gerenciado pode levar até uma hora. Uma notificação é exibida no portal, mostrando o andamento da sua implantação do Azure AD DS. Selecione a notificação para ver detalhes do andamento da implantação.

    ![Notificação no portal do Azure da implantação em andamento](./media/tutorial-create-instance/deployment-in-progress.png)

1. Selecione seu grupo de recursos, como *myResourceGroup* e, em seguida, escolha sua instância do Azure AD DS na lista de recursos do Azure, como *contoso.com*. A guia **Visão Geral** mostra que o domínio gerenciado está sendo *implantado* no momento. Você não pode configurar o domínio gerenciado até que ele esteja totalmente provisionado.

    ![Status do Domain Services durante o estado de provisionamento](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Quando o domínio gerenciado está totalmente provisionado, a guia **Visão Geral** mostra o status do domínio como *Em execução*.

    ![Status do Domain Services depois de provisionado com sucesso](./media/tutorial-create-instance/successfully-provisioned.png)

Durante o processo de provisionamento, o Azure AD DS cria Aplicativos Empresariais chamados *Serviços do Controlador de Domínio* e *AzureActiveDirectoryDomainControllerServices* no diretório. Esses Aplicativos Empresariais são necessários para atender o domínio gerenciado. É essencial que esses aplicativos não sejam excluídos em nenhum momento.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as configurações do DNS para a rede virtual do Azure

Com o Azure AD DS implantado com sucesso, agora configure a rede virtual para permitir que outras VMs e aplicativos conectados usem o domínio gerenciado. Para fornecer essa conectividade, atualize as configurações do servidor DNS da sua rede virtual para apontar para os dois endereços IP em que o Azure AD DS está implantado.

1. A guia **Visão geral** do domínio gerenciado mostra algumas **Etapas de configuração obrigatórias**. A primeira etapa de configuração é atualizar as configurações do servidor DNS da rede virtual. Depois que as configurações de DNS forem definidas corretamente, essa etapa não será mais mostrada.

    Os endereços listados são os controladores de domínio para uso na rede virtual. Nesse exemplo, os endereços são *10.1.0.4* e *10.1.0.5*. Posteriormente, você poderá encontrar esses endereços IP na guia **Propriedades**.

    ![Definir as configurações de DNS para sua rede virtual com os endereços IP do Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

1. Para atualizar as configurações do servidor DNS da rede virtual, selecione o botão **Configurar**. As configurações de DNS são configuradas automaticamente para sua rede virtual.

> [!TIP]
> Se você selecionou uma rede virtual existente nas etapas anteriores, todas as VMs conectadas à rede só obterão as novas configurações de DNS após uma reinicialização. Você pode reiniciar as VMs usando o portal do Azure, o Azure PowerShell ou a CLI do Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Habilitar contas de usuário para o Azure AD DS

Para autenticar os usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para a autenticação NTLM (Gerenciador de LAN NT) e Kerberos. O Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos, até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena credenciais de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente essas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

> [!NOTE]
> Uma vez configurado adequadamente, as hashes de senha utilizáveis são armazenadas no domínio gerenciado Azure AD DS. Se você excluir o domínio gerenciado do Azure AD DS, todas as hashes de senha armazenadas nesse ponto também serão excluídas. As informações de credenciais sincronizadas no Azure AD não poderão ser reutilizadas se você criar posteriormente um domínio gerenciado do Azure AD DS – você deve reconfigurar a sincronização de hash de senha para armazenar as hashes de senha novamente. As VMs ou os usuários previamente unidos ao domínio não poderão autenticar imediatamente – o Azure AD precisa gerar e armazenar as hashes de senha no novo domínio gerenciado do Azure AD DS. Para obter mais informações, confira [Processo de sincronização de hash de senha para o Azure AD DS e o Azure AD Connect][password-hash-sync-process].

As etapas para gerar e armazenar esses hashes de senha são diferentes para as contas de usuário somente em nuvem criadas no Azure AD versus as contas de usuário sincronizadas do seu diretório local usando o Azure AD Connect. Uma conta de usuário somente na nuvem é uma conta que foi criada no diretório do Azure AD usando o portal do Azure ou os cmdlets do Azure AD PowerShell. Essas contas de usuário não são sincronizadas de um diretório local. Neste tutorial, vamos trabalhar com uma conta de usuário básica somente de nuvem. Para obter mais informações sobre as etapas adicionais necessárias para usar Azure AD Connect, confira [Sincronizar hashes de senha para contas de usuário sincronizadas do AD local para o domínio gerenciado][on-prem-sync].

> [!TIP]
> Se o locatário do Azure AD tiver uma combinação de usuários somente de nuvem e usuários do AD local, será necessário concluir ambos os conjuntos de etapas.

Para contas de usuário somente de nuvem, os usuários devem alterar suas senhas antes de usar o Azure AD DS. Esse processo de alteração de senhas faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas no Azure AD. Você também pode expirar as senhas de todos os usuários do locatário que precisam usar o Azure AD DS – o que forçaria a troca de senha na próxima entrada – ou instruí-los a alterar as senhas manualmente. Para este tutorial, vamos alterar manualmente a senha do usuário.

Antes que o usuário possa redefinir a senha, o locatário do Azure AD deve ser [configurado para redefinição de senha self-service][configure-sspr].

Para alterar a senha de um usuário somente de nuvem, o usuário deve concluir as seguintes etapas:

1. Vá para a página do Painel de acesso do Azure AD em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **Perfil** no menu suspenso.

    ![Selecionar perfil](./media/tutorial-create-instance/select-profile.png)

1. Na página **Perfil**, selecione **Alterar senha**.
1. Na página **Alterar senha**, insira sua senha existente (antiga) e, em seguida, insira uma nova senha e confirme-a.
1. Selecione **Enviar**.

Leva alguns minutos após você ter alterado sua senha para que a nova senha possa ser usada no Azure AD DS e para que seja possível se conectar com êxito a computadores ingressados no domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Definir as configurações de rede virtual e DNS para um domínio gerenciado
> * Criar uma instância do Azure AD DS
> * Adicionar usuários administrativos ao gerenciamento de domínio
> * Habilitar contas de usuário para o Azure AD DS e gerar hashes de senha

Para ver esse domínio gerenciado em ação, crie e una uma máquina virtual ao domínio.

> [!div class="nextstepaction"]
> [Una uma máquina virtual do Windows Server ao seu domínio gerenciado](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
