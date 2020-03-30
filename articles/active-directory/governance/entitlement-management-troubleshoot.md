---
title: Gerenciamento de direitos de solução de problemas - Azure AD
description: Conheça alguns itens que você deve verificar para ajudá-lo a solucionar problemas no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128466"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Solucionando problemas gerenciamento de direitos ad do Azure

Este artigo descreve alguns itens que você deve verificar para ajudá-lo a solucionar problemas no gerenciamento de direitos do Azure Active Directory (Azure AD).

## <a name="administration"></a>Administração

* Se você receber uma mensagem negada de acesso ao configurar o gerenciamento de direitos e for um administrador Global, certifique-se de que seu diretório tenha uma [licença Azure AD Premium P2 (ou EMS E5).](entitlement-management-overview.md#license-requirements)

* Se você receber uma mensagem negada de acesso ao criar ou visualizar pacotes de acesso e for membro de um grupo criador do Catálogo, você deve [criar um catálogo](entitlement-management-catalog-create.md) antes de criar seu primeiro pacote de acesso.

## <a name="resources"></a>Recursos

* As funções para aplicativos são definidas pelo próprio aplicativo e são gerenciadas no Azure AD. Se um aplicativo não tiver nenhuma função de recurso, o gerenciamento de direitos atribui aos usuários uma **função de acesso padrão.**

    Observe que o portal Azure também pode mostrar os principais de serviços para serviços que não podem ser selecionados como aplicativos.  Em particular, **exchange online** e **SharePoint Online** são serviços, não aplicativos que têm funções de recurso no diretório, portanto não podem ser incluídos em um pacote de acesso.  Em vez disso, use o licenciamento baseado em grupo para estabelecer uma licença apropriada para um usuário que precise acessar esses serviços.

* Para que um grupo seja um recurso em um pacote de acesso, ele deve poder ser modificável no Azure AD.  Grupos que se originem em um Active Directory local não podem ser atribuídos como recursos porque seus atributos de proprietário ou membro não podem ser alterados no Azure AD.   Grupos que se originem no Exchange Online como Grupos de distribuição também não podem ser modificados no Azure AD. 

* As bibliotecas de documentos do SharePoint Online e os documentos individuais não podem ser adicionados como recursos.  Em vez disso, crie um [grupo de segurança Ad do Azure,](../fundamentals/active-directory-groups-create-azure-portal.md)inclua esse grupo e uma função de site no pacote de acesso e, no SharePoint Online, use esse grupo para controlar o acesso à biblioteca de documentos ou documento.

* Se houver usuários que já tenham sido atribuídos a um recurso que você deseja gerenciar com um pacote de acesso, verifique se os usuários estão atribuídos ao pacote de acesso com uma política apropriada. Por exemplo, talvez você queira incluir um grupo em um pacote de acesso que já tenha usuários no grupo. Se esses usuários no grupo exigirem acesso contínuo, eles deverão ter uma política apropriada para os pacotes de acesso para que não percam seu acesso ao grupo. Você pode atribuir o pacote de acesso tanto pedindo que os usuários solicitem o pacote de acesso que contém esse recurso quanto atribuindo-os diretamente ao pacote de acesso. Para obter mais informações, consulte [Alterar as configurações de solicitação e aprovação de um pacote de acesso](entitlement-management-access-package-request-policy.md).

* Quando você remove um membro de uma equipe, ele também é removido do grupo do Office 365. A remoção da funcionalidade de chat da equipe pode ser atrasada. Para obter mais informações, consulte [Membros do Grupo](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Verifique se o diretório não está configurado para várias áreas geográficas. No momento, o gerenciamento de direitos não dá suporte a locais de várias áreas geográficas para o SharePoint Online. Os sites do SharePoint Online devem estar na localização geográfica padrão para serem governados com o gerenciamento de direitos. Para obter mais informações, consulte [multi-geo recursos no OneDrive e no SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="access-packages"></a>Pacotes de acesso

* Se você tentar excluir um pacote de acesso ou política e ver uma mensagem de erro que diz que existem atribuições ativas, se você não ver nenhum usuário com atribuições, verifique se algum usuário recentemente excluído ainda tem atribuições. Durante a janela de 30 dias após a exclusão de um usuário, a conta de usuário pode ser restaurada.   

## <a name="external-users"></a>Usuários externos

* Quando um usuário externo quiser solicitar acesso a um pacote de acesso, certifique-se de que está usando o link do **portal Meu Acesso** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md). Se um usuário externo apenas visitar **myaccess.microsoft.com** e não usar o link completo do portal Meu Acesso, então ele verá os pacotes de acesso disponíveis para eles em sua própria organização e não em sua organização.

* Se um usuário externo não puder solicitar acesso a um pacote de acesso ou não conseguir acessar recursos, certifique-se de verificar suas [configurações para usuários externos](entitlement-management-external-users.md#settings-for-external-users).

* Se um novo usuário externo, que não tenha entrado anteriormente no seu diretório, receber um pacote de acesso com um site do SharePoint Online, o pacote de acesso dele será exibido como não totalmente entregue até que a conta seja provisionada no SharePoint Online. Para obter mais informações sobre configurações de compartilhamento, consulte [Revise as configurações de compartilhamento externo do SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Requests

* Quando um usuário quiser solicitar acesso a um pacote de acesso, certifique-se de que está usando o link do **portal Meu Acesso** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md).

* Se você abrir o portal Meus Acessos com seu navegador definido como modo privado ou anônimo, isso poderá entrar em conflito com o comportamento de entrada. Recomendamos que você não use o modo privado ou anônimo do seu navegador ao visitar o portal Meus Acessos.

* Quando um usuário que ainda não está no seu diretório entrar no portal Meus Acessos para solicitar um pacote de acesso, verifique se ele se autenticou usando uma conta organizacional. A conta organizacional pode ser uma conta tanto no diretório de recursos quanto em um diretório incluído em uma das políticas do pacote de acesso. Se a conta do usuário não for uma conta organizacional ou se o diretório em que ele se autenticar não estiver incluído na política, o usuário não verá o pacote de acesso. Para obter mais informações, consulte [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md).

* Se um usuário estiver impedido de entrar no diretório de recursos, ele não poderá solicitar acesso no portal Meus Acessos. Antes que o usuário possa solicitar acesso, você deve remover o bloco de entrada do perfil do usuário. Para remover o bloco de login, no portal Azure, clique em **Azure Active Directory**, clique **em Usuários,** clique no usuário e, em seguida, clique em **Perfil**. Editar a seção **Configurações** e alterar **o login do bloco em** **Não**. Para obter mais informações, consulte [Adicionar ou atualizar as informações do perfil de um usuário usando o Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Você também pode verificar se o usuário foi bloqueado devido a uma [política de proteção de identidade](../identity-protection/howto-unblock-user.md).

* No portal Meu Acesso, se um usuário for um solicitante e um aprovador, ele não verá sua solicitação de um pacote de acesso na página **Aprovações.** Esse comportamento é intencional: um usuário não pode aprovar a própria solicitação. Verifique se o pacote de acesso sendo solicitado tem aprovadores adicionais configurados na política. Para obter mais informações, consulte [Alterar as configurações de solicitação e aprovação de um pacote de acesso](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Exibir os erros de entrega de uma solicitação

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique **em Solicitações**.

1. Selecione a solicitação que deseja visualizar.

    Se a solicitação tiver algum erro de entrega, o status da solicitação será **não entregue** ou **entregue parcialmente**.

    Se houver algum erro de entrega, no painel de detalhes da solicitação, haverá uma contagem de erros de entrega.

1. Clique na contagem para ver todos os erros de entrega da solicitação.

### <a name="reprocess-a-request"></a>Reprocessar uma solicitação

Se uma solicitação encontrar um erro, você pode reprocessar a solicitação para experimentá-la novamente. Você só pode reprocessar uma solicitação que tenha um status de **Entrega falho** ou **parcialmente entregue** e uma data completa de menos de uma semana.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique **em Solicitações**.

1. Clique na solicitação que deseja reprocessar.

1. No painel de detalhes da solicitação, clique em **Reprocessar solicitação**.

    ![Reprocesse uma solicitação com falha](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Você só pode cancelar uma solicitação pendente que ainda não foi entregue ou cuja entrega falhou.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique **em Solicitações**.

1. Clique na solicitação que deseja cancelar.

1. No painel de detalhes da solicitação, clique em **Cancelar solicitação**.

## <a name="multiple-policies"></a>Múltiplas políticas

* A gestão de direitos segue as melhores práticas de privilégios. Quando um usuário solicita acesso a um pacote de acesso que tem várias políticas que se aplicam, o gerenciamento de direitos inclui lógica para ajudar a garantir que políticas mais rigorosas ou mais específicas sejam priorizadas sobre políticas genéricas. Se uma política for genérica, o gerenciamento de direitos pode não exibir a política ao solicitante ou selecionar automaticamente uma política mais rigorosa.

* Por exemplo, considere um pacote de acesso com duas políticas para funcionários internos em que ambas as políticas se aplicam ao solicitante. A primeira política é para usuários específicos que incluem o solicitante. A segunda política é para todos os usuários em um diretório do que o solicitante é membro. Nesse cenário, a primeira política é automaticamente selecionada para o solicitante porque é mais rigorosa. O solicitante não tem a opção de selecionar a segunda política.

* Quando várias políticas se aplicam, a diretiva que é selecionada automaticamente ou as políticas exibidas ao solicitante são baseadas na seguinte lógica de prioridade:

    | Prioridade política | Escopo |
    | --- | --- |
    | P1 | Usuários e grupos específicos em seu diretório OU organizações conectadas específicas |
    | P2 | Todos os membros em seu diretório (excluindo convidados) |
    | P3 | Todos os usuários em seu diretório (incluindo convidados) ou organizações conectadas específicas |
    | P4 | Todas as organizações conectadas OU todos os usuários (todas as organizações conectadas + quaisquer novos usuários externos) |
    
    Se alguma política estiver em uma categoria de maior prioridade, as categorias de menor prioridade são ignoradas. Para um exemplo de como várias políticas com a mesma prioridade são exibidas ao solicitante, consulte [Selecionar uma diretiva](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Próximas etapas

- [Administrar o acesso de usuários externos](entitlement-management-external-users.md)
- [Veja relatórios de como os usuários tiveram acesso na gestão de direitos](entitlement-management-reports.md)
