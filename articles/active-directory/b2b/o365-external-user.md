---
title: Compartilhamento externo do Office 365 e colaboração B2B – Azure AD
description: Discute o compartilhamento de recursos com os parceiros externos usando a colaboração B2B do Microsoft Azure Active Directory e de O365.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272262"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Compartilhamento externo do Office 365 e colaboração B2B do Azure Active Directory

O compartilhamento externo no Office 365 (OneDrive, SharePoint Online, Unified Groups etc.) e a colaboração B2B do Azure AD (Azure Active Directory) são tecnicamente a mesma coisa. Todo compartilhamento externo, (com exceção do OneDrive/SharePoint Online), incluindo convidados em Grupos do Office 365, já usa as APIs de convite de colaboração B2B do Azure AD para compartilhamento.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Como o Azure AD B2B difere do compartilhamento externo no SharePoint Online?

O OneDrive/SharePoint Online tem um gerenciador de convite separado. Suporte para compartilhamento externo no OneDrive/SharePoint Online iniciado antes de o Azure AD ter desenvolvido o suporte. Ao longo do tempo, o compartilhamento externo do OneDrive/SharePoint Online acumulou vários recursos e muitos milhões de usuários que usam o padrão de compartilhamento interno do produto. No entanto, existem algumas diferenças sutis entre como funciona o compartilhamento externo do OneDrive / SharePoint Online e como funciona a colaboração B2B do Azure AD. Você pode aprender mais sobre o compartilhamento externo do OneDrive / SharePoint Online em [Visão geral do compartilhamento externo](https://docs.microsoft.com/sharepoint/external-sharing-overview). O processo geralmente é diferente do Azure AD B2B das seguintes maneiras:

- O OneDrive/SharePoint Online adiciona usuários ao diretório depois que os usuários tiverem resgatado seus convites. Portanto, antes de resgate, você não vê o usuário no Portal do Azure AD. Se outro site convidar um usuário durante esse período, um novo convite será gerado. No entanto, quando você usa a colaboração B2B do Azure AD, os usuários são adicionados imediatamente no convite para que eles apareçam em todos os lugares.

- A experiência de resgate no OneDrive/SharePoint Online tem uma aparência diferente da experiência na colaboração B2B do Azure AD. Depois que um usuário resgata um convite, as experiências são parecidas.

- A colaboração B2B do Azure AD convidou usuários que possam ser escolhidos das caixas de diálogo de compartilhamento do OneDrive/SharePoint Online. Os usuários convidados do OneDrive/SharePoint Online também aparecem no Azure AD após o resgate do convite.

- Os requisitos de licenciamento são diferentes. Para cada licença paga do Azure AD, você pode permitir que até 5 usuários convidados acessem seus recursos pagos do Azure AD. Para saber mais sobre licenciamento, consulte [Licenciamento do Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) e ["O que é um usuário externo?" na visão geral de compartilhamento externo do SharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Para gerenciar o compartilhamento externo no OneDrive / SharePoint Online com a colaboração do Azure AD B2B, defina a configuração de compartilhamento externo do OneDrive / SharePoint Online como **Permitir compartilhamento apenas com os usuários externos que já existem no diretório da sua organização**. Os usuários podem acessar sites compartilhados externamente e escolher colaboradores externos adicionados pelo administrador. O administrador pode adicionar os colaboradores externos por meio de APIs de convite de colaboração B2B.


![As configurações de compartilhamento externo do OneDrive/SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

Após habilitar o compartilhamento externo, a capacidade de pesquisar usuários convidados existentes no seletor de pessoas do SPO (SharePoint Online) é DESATIVADA por padrão para corresponder ao comportamento herdado.

Esse recurso pode ser habilitado usando a configuração 'ShowPeoplePickerSuggestionsForGuestUsers' na coleta do site e do locatário. Defina esse recurso usando os cmdlets Set-SPOTenant e SPOSite, que permitem aos membros pesquisar todos os usuários convidados existentes no diretório. Alterações no escopo de locatário não afetam sites SPO já provisionados.

## <a name="next-steps"></a>Próximas etapas

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)
* [Delegação de convites de colaboração B2B](delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](troubleshoot.md)
