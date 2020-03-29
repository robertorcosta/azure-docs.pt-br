---
title: Cenários comuns de implantação para serviços de domínio Azure AD | Microsoft Docs
description: Conheça alguns dos cenários comuns e casos de uso dos Serviços de Domínio do Diretório Ativo do Azure para fornecer valor e atender às necessidades dos negócios.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917223"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casos de uso comuns e cenários para serviços de domínio do Diretório Ativo do Azure

O Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como participação em domínio, política de grupo, protocolo de acesso a diretórios leves (LDAP) e autenticação Kerberos/NTLM. O Azure AD DS se integra ao locatário existente do Azure AD, o que permite aos usuários entrarem usando suas credenciais atuais. Você usa esses serviços de domínio sem a necessidade de implantar, gerenciar e corrigir controladores de domínio na nuvem, o que fornece um levantamento e deslocamento mais suaves de recursos no local para o Azure.

Este artigo descreve alguns cenários de negócios comuns onde o Azure AD DS fornece valor e atende a essas necessidades.

## <a name="secure-administration-of-azure-virtual-machines"></a>Administração segura de máquinas virtuais Azure

Para permitir que você use um único conjunto de credenciais AD, as máquinas virtuais (VMs) do Azure podem ser unidas a um domínio gerenciado pelo Azure AD DS. Essa abordagem reduz problemas de gerenciamento de credenciais, como a manutenção de contas de administrador locais em cada VM ou contas separadas e senhas entre ambientes.

As VMs que são unidas a um domínio gerenciado pelo Azure AD DS também podem ser gerenciadas e protegidas usando a diretiva de grupo. As linhas de base de segurança necessárias podem ser aplicadas às VMs para bloqueá-las de acordo com as diretrizes de segurança corporativa. Por exemplo, você pode usar recursos de gerenciamento de políticas de grupo para restringir os tipos de aplicativos que podem ser lançados na VM.

![Administração simplificada de máquinas virtuais do Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Vamos olhar para um cenário de exemplo comum. À medida que servidores e outras infra-estruturas atingem o fim da vida útil, contoso quer mover aplicativos atualmente hospedados em instalações para a nuvem. O padrão de TI atual determina que os servidores que hospedam aplicativos corporativos devem ser unidos pelo domínio e gerenciados usando a política de grupo. O administrador de TI da Contoso prefere participar de VMs implantados no Azure para facilitar a administração, pois os usuários podem então fazer login usando suas credenciais corporativas. Quando unidos por domínio, as VMs também podem ser configuradas para cumprir as linhas de base de segurança necessárias usando GPOs (Group Policy Objects, objetos de diretiva de grupo). Contoso prefere não implantar, monitorar e gerenciar seus próprios controladores de domínio no Azure.

Azure AD DS é um ótimo ajuste para este caso de uso. Um domínio gerenciado pelo Azure AD DS permite que você participe das VMs, use um único conjunto de credenciais e aplique política de grupo. Como um domínio gerenciado, você não precisa configurar e manter os controladores de domínio você mesmo.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este caso de uso de exemplo:

* Os domínios gerenciados do Azure AD DS usam uma estrutura única e plana da Unidade Organizacional (OU) por padrão. Todas as VMs aderidas por domínio estão em um único OU. Se desejar, você pode criar OUs personalizados.
* O Azure AD DS usa um GPO integrado para os usuários e contêineres de computadores. Para controle adicional, você pode criar GPOs personalizados e direcioná-los para OUs personalizados.
* O Azure AD DS suporta o esquema de objeto do computador AD base. Você não pode estender o esquema do objeto do computador.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Aplicativos de elevação e mudança no local que usam autenticação de vinculação LDAP

Como cenário amostral, Contoso tem um aplicativo local que foi comprado de um ISV há muitos anos. O aplicativo está atualmente em modo de manutenção pelo ISV e solicitar alterações no aplicativo é proibitivamente caro. Este aplicativo tem um frontend baseado na Web que coleta credenciais do usuário usando um formulário web e, em seguida, autentica os usuários realizando um vínculo LDAP ao ambiente AD DS local.

![Associação LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso gostaria de migrar este aplicativo para o Azure. O aplicativo deve continuar a funcionar como está, sem necessidade de alterações. Além disso, os usuários devem ser capazes de autenticar usando suas credenciais corporativas existentes e sem treinamento adicional. Deve ser transparente para os usuários finais onde o aplicativo está sendo executado.

Para este cenário, o Azure AD DS permite que os aplicativos executem vínculos LDAP como parte do processo de autenticação. Os aplicativos legados no local podem levantar e mudar para o Azure e continuar a autenticar perfeitamente os usuários sem qualquer alteração na configuração ou experiência do usuário.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este caso de uso de exemplo:

* Certifique-se de que o aplicativo não precisa modificar/gravar no diretório. O acesso à gravação do LDAP a um domínio gerenciado pelo Azure AD DS não é suportado.
* Você não pode alterar senhas diretamente contra um domínio gerenciado pelo Azure AD DS. Os usuários finais podem alterar suas senhas ou usando o mecanismo de alteração de senha de autoatendimento do Azure AD ou no diretório local. Essas alterações são sincronizadas automaticamente e disponíveis no domínio gerenciado pelo Azure AD DS.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Aplicativos de elevação e mudança no local que usam leitura LDAP para acessar o diretório

Como o cenário de exemplo anterior, vamos supor que a Contoso tenha um aplicativo de linha de negócios (LOB) que foi desenvolvido há quase uma década. Este aplicativo tem conhecimento do diretório e foi projetado para usar o LDAP para ler informações/atributos sobre usuários do AD DS. O aplicativo não modifica atributos ou de outra forma escreve para o diretório.

Contoso quer migrar esse aplicativo para o Azure e aposentar o hardware local de envelhecimento que atualmente hospeda este aplicativo. O aplicativo não pode ser reescrito para usar APIs de diretório modernos, como a API do Microsoft Graph baseada em REST. Uma opção de elevação e mudança é desejada onde o aplicativo pode ser migrado para ser executado na nuvem, sem modificar o código ou reescrever o aplicativo.

Para ajudar com esse cenário, o Azure AD DS permite que os aplicativos realizem leituras lDAP contra o domínio gerenciado para obter as informações de atributo de que precisa. O aplicativo não precisa ser reescrito, então um elevador e uma mudança no Azure permite que os usuários continuem a usar o aplicativo sem perceber que há uma mudança no local em que ele é executado.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este caso de uso de exemplo:

* Certifique-se de que o aplicativo não precisa modificar/gravar no diretório. O acesso à gravação do LDAP a um domínio gerenciado pelo Azure AD DS não é suportado.
* Certifique-se de que o aplicativo não precisa de um esquema de diretório ativo personalizado/estendido. As extensões de esquema não são suportadas no Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migre um serviço no local ou aplicativo daemon para o Azure

Alguns aplicativos incluem vários níveis, onde um dos níveis precisa executar chamadas autenticadas para um nível backend, como um banco de dados. Contas de serviço sd são comumente usadas nesses cenários. Quando você levanta e transfere aplicativos para o Azure, o Azure AD DS permite que você continue a usar contas de serviço da mesma forma. Você pode optar por usar a mesma conta de serviço sincronizada do seu diretório local para o Azure AD ou criar um OU personalizado e, em seguida, criar uma conta de serviço separada nesse OU. Com qualquer abordagem, os aplicativos continuam funcionando da mesma maneira para fazer chamadas autenticadas para outros níveis e serviços.

![Conta de serviço usando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Neste cenário de exemplo, o Contoso possui um aplicativo de cofre de software personalizado que inclui um front-end web, um servidor SQL e um servidor FTP back-end. A autenticação integrada do Windows usando contas de serviço autentica a front-end da Web para o servidor FTP. O front-end da Web é configurado para execução como uma conta de serviço. O servidor back-end está configurado para autorizar o acesso da conta de serviço para o front-end da Web. Contoso não quer implantar e gerenciar suas próprias VMs controladoras de domínio na nuvem para mover este aplicativo para o Azure.

Para este cenário, os servidores que hospedam o front-end web, o servidor SQL e o servidor FTP podem ser migrados para VMs do Azure e unidos a um domínio gerenciado pelo Azure AD DS. As VMs podem então usar a mesma conta de serviço em seu diretório local para fins de autenticação do aplicativo, que é sincronizada através do Azure AD connect.

### <a name="deployment-notes"></a>Observações de implantação

As seguintes considerações de implantação se aplicam a este caso de uso de exemplo:

* Certifique-se de que os aplicativos usem um nome de usuário e senha para autenticação. A autenticação baseada em certificadoou smartcard não é suportada pelo Azure AD DS.
* Você não pode alterar senhas diretamente contra um domínio gerenciado pelo Azure AD DS. Os usuários finais podem alterar suas senhas ou usando o mecanismo de alteração de senha de autoatendimento do Azure AD ou no diretório local. Essas alterações são sincronizadas automaticamente e disponíveis no domínio gerenciado pelo Azure AD DS.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implantações de serviços de desktop remoto do Windows Server no Azure

Você pode usar o Azure AD DS para fornecer serviços de domínio gerenciados para servidores de desktop remotos implantados no Azure. Para obter mais informações sobre esse cenário de implantação, consulte [como integrar os Serviços de Domínio Ad do Azure com a sua implantação RDS][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Clusters HDInsight ingressados no domínio

Você pode configurar um cluster Azure HDInsight que está unido a um domínio gerenciado pelo Azure AD DS com o Apache Ranger ativado. Você pode criar e aplicar políticas de Colméia através do Apache Ranger, e permitir que os usuários, como cientistas de dados, se conectem à Colméia usando ferramentas baseadas em ODBC, como Excel ou Tableau. Continuamos trabalhando para adicionar outras cargas de trabalho, como HBase, Spark e Storm, ao HDInsight, que se juntou ao domínio.

Para obter mais informações sobre esse cenário de implantação, veja [como configurar clusters HDInsight unidos por domínio][hdinsight]

## <a name="next-steps"></a>Próximas etapas

Para começar, [crie e configure uma instância de serviços de domínio do Diretório Ativo do Azure][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
