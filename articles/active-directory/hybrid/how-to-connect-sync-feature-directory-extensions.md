---
title: 'Sincronização do Azure AD Connect: extensões do Directory | Microsoft Docs'
description: Este tópico descreve o recurso de extensões de diretório no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917906"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: extensões do Directory
É possível usar extensões de diretório para estender o esquema no Azure AD (Microsoft Azure Active Directory) com seus próprios atributos do Active Directory local. Esse recurso permite compilar aplicativos LOB ao consumir atributos que continuam gerenciando localmente. Esses atributos podem ser consumidos através de [extensões.](https://docs.microsoft.com/graph/extensibility-overview
) Você pode ver os atributos disponíveis usando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Você também pode usar esse recurso para criar grupos dinâmicos no Azure AD.

No momento, nenhuma carga de trabalho do Office 365 consome esses atributos.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Personalize quais atributos para sincronizar com o Azure AD

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação.

>[!NOTE]
>A caixa Atributos Disponíveis diferencia maiúsculas de minúsculas.

![Assistente de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

 A instalação mostra os seguintes atributos, que são candidatos válidos:

* Tipos de objeto de Usuário e de Grupo
* Atributos de valor único: String, Boolean, Integer, Binary
* Atributos de vários valores: String, Binary


>[!NOTE]
> Embora o Azure AD Connect dê suporte à sincronização de atributos com valores múltiplos do Active Directory com o Azure AD como extensões de diretório com valores múltiplos, atualmente, não há nenhuma maneira de recuperar/consumir os dados carregados em atributos da extensão de diretório com valores múltiplos.

A lista de atributos é lida a partir do cache de esquema criado durante a instalação do Azure AD Connect. Se você estendeu o esquema do Active Directory com atributos adicionais, será necessário [atualizar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema), antes que esses novos atributos fiquem visíveis.

Um objeto no Azure Active Directory pode ter até 100 atributos para extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for maior, o mecanismo de sincronização irá truncá-lo.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Alterações de configuração no Azure AD feitas pelo assistente

Durante a instalação do Azure AD Connect, é registrado um aplicativo no qual esses atributos estão disponíveis. Você pode ver esse aplicativo no Portal do Azure. Seu nome é sempre **Tenant Schema Extension App**.

![Aplicativo de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Certifique-se de selecionar **Todos os aplicativos** para ver este aplicativo.

Os atributos são prefixados com **extensão \_\_{ApplicationId}**. O ApplicationId tem o mesmo valor para todos os atributos do seu inquilino Azure AD. Você precisará deste valor para todos os outros cenários neste tópico.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Visualização de atributos usando a API do Microsoft Graph

Esses atributos estão agora disponíveis através da API do Microsoft Graph, usando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Na API do Microsoft Graph, você precisa pedir que os atributos sejam devolvidos. Selecione explicitamente os atributos como este: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`.
>
> Para obter mais informações, consulte [Microsoft Graph: usar parâmetros de consulta](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Use os atributos em grupos dinâmicos

Um dos cenários mais úteis é usar esses atributos em grupos de segurança dinâmica ou Office 365.

1. Crie um novo grupo no Azure AD. Dê-lhe um bom nome e certifique-se de que o **tipo de Associação** é Usuário **Dinâmico**.

   ![Captura de tela com um novo grupo](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selecione para **adicionar consulta dinâmica**. Se você olhar para as propriedades, então você não verá esses atributos estendidos. Você precisa adicioná-los primeiro. Clique **em Obter propriedades de extensão personalizadas,** digite o ID do aplicativo e clique em Atualizar **propriedades**.

   ![Captura de tela onde extensões de diretório foram adicionadas](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Abra a propriedade para baixo e observe que os atributos adicionados agora estão visíveis.

   ![Captura de tela com novos atributos aparecendo na ui](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Complete a expressão de acordo com suas necessidades. Em nosso exemplo, a regra é definida como **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Vendas e marketing")**.

4. Depois que o grupo for criado, dê ao Azure AD algum tempo para preencher os membros e, em seguida, revisar os membros.

   ![Captura de tela com membros do grupo dinâmico](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [a integração de suas identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
