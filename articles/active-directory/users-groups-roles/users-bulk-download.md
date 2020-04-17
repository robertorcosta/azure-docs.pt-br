---
title: Baixe uma lista de usuários no portal do Azure Active Directory | Microsoft Docs
description: Baixe registros de usuários em massa no centro de administradores do Azure no Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532622"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Baixe uma lista de usuários no portal do Azure Active Directory

O Azure Active Directory (Azure AD) suporta operações de importação (create) em massa de usuários.

## <a name="required-permissions"></a>Permissões necessárias

Para baixar a lista de usuários do centro de administração Azure AD, você deve estar conectado com um usuário atribuído a uma ou mais funções de administrador de nível de organização no Azure AD (Administrador de usuário é a função mínima necessária). O convidado e o desenvolvedor de aplicativos não são considerados funções de administrador.

## <a name="to-download-a-list-of-users"></a>Para baixar uma lista de usuários

1. [Faça login na sua organização Azure AD](https://aad.portal.azure.com) com uma conta de administrador de usuário na organização.
2. Navegue até o Azure Active Directory > Usuários. Em seguida, selecione os usuários que deseja incluir no download marcando a caixa na coluna esquerda ao lado de cada usuário. Nota: Neste momento, não há como selecionar todos os usuários para exportação. Cada um deve ser selecionado individualmente.
3. No Azure AD, selecione **Usuários** > **Baixar usuários**.
4. Na página **Download users,** **selecione Iniciar** para receber um arquivo CSV listando propriedades do perfil do usuário. Se houver erros, você pode baixar e visualizar o arquivo de resultados na página de resultados da operação Bulk. O arquivo contém a razão de cada erro.

   ![Selecione onde você quer a lista dos usuários que deseja baixar](./media/users-bulk-download/bulk-download.png)

   O arquivo de download conterá a lista filtrada de usuários.

   Os seguintes atributos de usuário estão incluídos:

   - userPrincipalName
   - displayName
   - sobrenome
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - Serviço Móvel
   - autenticaçãoNúmero do telefone
   - autenticaçãoAlternativePhoneNumber
   - autenticaçãoEmail
   - endereço de e-mail alternativo
   - ageGroup
   - consentProvidedForMinor
   - LegalAgeGroupClassification

## <a name="check-status"></a>Verificar o status

Você pode ver o status de suas solicitações em massa pendentes na página de resultados da **operação Bulk.**

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de download em massa

Cada atividade em massa para criar uma lista de usuários pode ser executada por até uma hora. Isso permite a criação e download de uma lista de pelo menos 500.000 usuários.

## <a name="next-steps"></a>Próximas etapas

- [Adição de Usuários em Massa](users-bulk-add.md)
- [Usuários de exclusão em massa](users-bulk-delete.md)
- [Usuários de restauração em massa](users-bulk-restore.md)
