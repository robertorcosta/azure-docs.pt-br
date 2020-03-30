---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565071"
---
[O Azure Files](../articles/storage/files/storage-files-introduction.md) suporta autenticação baseada em identidade sobre o SMB (Server Message Block) através do [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (visualização) e [do Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Este artigo se concentra em como o Azure Files pode aproveitar os serviços de domínio, seja no local ou no Azure, para suportar o acesso baseado em identidade aos Arquivos Azure sobre SMB. Isso permite que você substitua facilmente seus servidores de arquivos existentes por arquivos Do Zure e continue a usar seu serviço de diretório existente, mantendo o acesso perfeito do usuário a compartilhamentos. 

O Azure Files impõe a autorização no acesso do usuário ao compartilhamento e ao nível de diretório/arquivo. A atribuição de permissão em nível de compartilhamento pode ser atribuída a usuários ou grupos ad azure gerenciados através do modelo típico [de controle de acesso baseado em função (RBAC).](../articles/role-based-access-control/overview.md) Com o RBAC, as credenciais que você usa para acesso a arquivos devem estar disponíveis ou sincronizadas com o Azure AD. Você pode atribuir funções de RBAC incorporadas, como o Storage File Data SMB Share Reader, a usuários ou grupos no Azure AD para conceder acesso à leitura de um compartilhamento de arquivos Azure.

No nível de diretório/arquivo, o Azure Files suporta preservar, herdar e aplicar [OS DACLs do Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) como qualquer servidor de arquivos windows. Se você copiar dados sobre SMB de um compartilhamento de arquivos para Arquivos Azure, ou vice-versa, você pode optar por manter OS DACLs do Windows. Se você planeja impor a autorização ou não, você pode aproveitar os Arquivos Azure para fazer backup de ACLs junto com seus dados. 
