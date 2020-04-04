---
title: Perguntas frequentes sobre multi-sessões do Windows 10 Enterprise - Azure
description: Perguntas frequentes e práticas recomendadas para usar o Windows 10 Enterprise multi-sessão para Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637092"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Perguntas frequentes sobre o Windows 10 Enterprise de várias sessões

Este artigo responde a perguntas frequentes e explica as práticas recomendadas para o Windows 10 Enterprise multi-sessão.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>O que é multi-sessão do Windows 10 Enterprise?

O Windows 10 Enterprise multi-session, anteriormente conhecido como Windows 10 Enterprise for Virtual Desktops (EVD), é um novo Host de sessão de desktop remoto que permite várias sessões interativas simultâneas. Anteriormente, apenas o Windows Server poderia fazer isso. Esse recurso oferece aos usuários uma experiência familiar do Windows 10, enquanto a TI pode se beneficiar das vantagens de custo de várias sessões e usar o licenciamento windows existente por usuário em vez de CALs (RDS Client Access Licenses, licenças de acesso ao cliente RDS). Para obter mais informações sobre licenças e preços, consulte [os preços do Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Quantos usuários podem ter simultaneamente uma sessão interativa no Windows 10 Enterprise multi-sessão?

Quantas sessões interativas que podem estar ativas ao mesmo tempo dependem dos recursos de hardware do seu sistema (vCPU, memória, disco e vGPU), como seus usuários usam seus aplicativos durante a entrada em uma sessão e quão pesada é a carga de trabalho do sistema. Sugerimos que você valide o desempenho do seu sistema para entender quantos usuários você pode ter no Windows 10 Enterprise multi-sessão. Para saber mais, consulte [os preços do Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Por que meu aplicativo relata a multisessão do Windows 10 Enterprise como um sistema operacional Server?

O Windows 10 Enterprise é uma edição virtual do Windows 10 Enterprise. Uma das diferenças é que este sistema operacional (OS) relata o [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) como tendo um valor de 3, o mesmo valor do Windows Server. Essa propriedade mantém o sistema operacional compatível com ferramentas de gerenciamento RDSH existentes, aplicativos com reconhecimento de várias sessões RDSH e, principalmente, otimizações de desempenho de sistema de baixo nível para ambientes RDSH. Alguns instaladores de aplicativos podem bloquear a instalação no Windows 10 multi-sessão, dependendo se eles detectarem que o ProductType está definido como Cliente. Se o aplicativo não for instalado, entre em contato com o fornecedor do aplicativo para obter uma versão atualizada. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Posso executar várias sessões do Windows 10 Enterprise no local?

A multisessão Windows 10 Enterprise não pode ser executada em ambientes de produção no local porque é otimizada para o serviço de desktop virtual do Windows para o Azure. É contra o acordo de licenciamento executar o Windows 10 Enterprise multi-sessão fora do Azure para fins de produção. O Windows 10 Enterprise multi-sessão não será ativado contra os PRINCIPAIS Serviços de Gerenciamento de Chaves (KMS) no local.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Como personalizar a imagem multi-sessão do Windows 10 Enterprise para minha organização?

Você pode iniciar uma máquina virtual (VM) no Azure com o Windows 10 Windows 10 Enterprise multi-sessão e personalizá-la instalando aplicativos LOB, sysprep/generalize e, em seguida, criar uma imagem usando o portal Azure.  
 
Para começar, crie uma VM no Azure com multi-sessão do Windows 10 Enterprise. Em vez de iniciar o VM no Azure, você pode baixar o VHD diretamente. Depois disso, você poderá usar o VHD que você baixou para criar uma nova Geração 1 VM em um PC com Windows 10 com Hiper-V ativado.

Personalize a imagem para suas necessidades instalando aplicativos LOB e sysprep a imagem. Quando terminar de personalizar, carregue a imagem para o Azure com o VHD dentro. Depois disso, obtenha o Windows Virtual Desktop no Azure Marketplace e use-o para implantar um novo pool de hostcom a imagem personalizada.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Como faço para gerenciar a multisessão do Windows 10 Enterprise após a implantação?

Você pode usar qualquer ferramenta de configuração suportada, mas recomendamos a versão 1906 do Configuration Manager porque suporta multi-sessão do Windows 10 Enterprise. No momento, estamos trabalhando no suporte ao Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>O Windows 10 Enterprise pode ser o Azure Active Directory (AD) aderido?

Atualmente, o Windows 10 Enterprise é suportado para ser aderido pelo Azure AD híbrido. Depois que a multisessão do Windows 10 Enterprise for acompanhada pelo domínio, use o Objeto de Diretiva de Grupo existente para habilitar o registro ad do Azure. Para obter mais informações, consulte [Planejar a implementação do Azure Active Directory híbrido](../active-directory/devices/hybrid-azuread-join-plan.md).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Onde posso encontrar a imagem multi-sessão do Windows 10 Enterprise?

A multisessão Windows 10 Enterprise está na galeria DoZure. Para encontrá-lo, navegue até o portal Azure e procure a versão do Windows 10 Enterprise para Desktops Virtuais. Para uma imagem integrada ao Office Pro Plus, acesse o portal Dozure e procure o Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Qual imagem de várias sessões do Windows 10 Enterprise devo usar?

A galeria Azure tem vários lançamentos, incluindo windows 10 Enterprise multi-session, versão 1809, e Windows 10 Enterprise multi-session, versão 1903. Recomendamos o uso da versão mais recente para melhorar o desempenho e a confiabilidade.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Quais versões multi-sessões do Windows 10 Enterprise são suportadas?

Windows 10 Enterprise multi-sessão, versões 1809 e posteriores são suportadas e estão disponíveis na galeria Azure. Essas versões seguem a mesma política de ciclo de vida do Windows 10 Enterprise, o que significa que a versão de primavera é suportada por 18 meses e a versão de outono por 30 meses.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Qual solução de gerenciamento de perfil devo usar para multi-sessão do Windows 10 Enterprise?

Recomendamos que você use recipientes de perfil FSLogix quando configurar o Windows 10 Enterprise em ambientes não persistentes ou outros cenários que precisem de um perfil armazenado centralmente. O FSLogix garante que o perfil do usuário esteja disponível e atualizado para cada sessão de usuário. Também recomendamos que você use seu contêiner de perfil FSLogix para armazenar um perfil de usuário em qualquer compartilhamento de SMB com permissões apropriadas, mas você pode armazenar perfis de usuário no armazenamento blob da página Do Zure, se necessário. Os usuários do Windows Virtual Desktop podem usar o FSLogix sem nenhum custo adicional.
 
Para obter mais informações sobre como configurar um contêiner de perfil FSLogix, consulte [Configurar o contêiner de perfil FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Qual licença eu preciso para acessar o Windows 10 Enterprise multi-sessão?

Para obter uma lista completa de licenças aplicáveis, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Por que meus aplicativos desaparecem depois que eu saio?

Isso acontece porque você está usando o Windows 10 Enterprise multi-sessão com uma solução de gerenciamento de perfil como o FSLogix. Sua solução de administrador ou perfil configurou seu sistema para excluir perfis de usuário quando os usuários saem. Essa configuração significa que, quando o sistema exclui seu perfil de usuário após a saída, ele também remove todos os aplicativos instalados durante a sessão. Se você quiser manter os aplicativos instalados, você precisará pedir ao seu assistente para provisionar esses aplicativos para todos os usuários em seu ambiente Windows Virtual Desktop.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Como faço para garantir que os aplicativos não desapareçam quando os usuários saem?

A maioria dos ambientes virtualizados são configurados por padrão para impedir que os usuários instalem aplicativos adicionais em seus perfis. Se você quiser garantir que um aplicativo não desapareça quando o usuário sair do Windows Virtual Desktop, você tem que provisionar esse aplicativo para todos os perfis de usuário em seu ambiente. Para obter mais informações sobre aplicativos de provisionamento, confira esses recursos:

- [Publique aplicativos incorporados no Windows Virtual Desktop](publish-apps.md)
- [Opções de linha de comando de pacote de aplicativos DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Pacote de provisionamento de aplicativos](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Como garantir que os usuários não baixem e instalem aplicativos da Microsoft Store?

Você pode desativar o aplicativo da Microsoft Store para garantir que os usuários não baixem aplicativos extras além dos aplicativos que você já disponibilizou para eles.

Para desativar o aplicativo Loja:

1. Crie uma nova política de grupo.
2. Selecione**modelos administrativos** > de **configuração** > de computador**componentes do Windows**.
3. Selecione **Repositório**.
4. Selecione **aplicativo da loja**.
5. Selecione **Disabled,** e selecione **OK**.
6. Escolha **Aplicar**.
 
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Windows Virtual Desktop e o Windows 10 Enterprise multi-session:

- Leia nossa [documentação de visualização de área de trabalho virtual do Windows](overview.md)
- Visite nossa [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Configure sua implantação do Windows Virtual Desktop com os [tutoriais do Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
