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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565071"
---
[Os arquivos do Azure](../articles/storage/files/storage-files-introduction.md) oferecem suporte à autenticação baseada em identidade por meio do protocolo SMB por meio de [Active Directory (](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) versão prévia) e [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Este artigo se concentra em como os arquivos do Azure podem aproveitar os serviços de domínio, no local ou no Azure, para dar suporte ao acesso baseado em identidade aos arquivos do Azure por SMB. Isso permite que você substitua facilmente seus servidores de arquivos existentes por arquivos do Azure e continue a usar o serviço de diretório existente, mantendo o acesso contínuo de usuário aos compartilhamentos. 

Os arquivos do Azure impõe a autorização no acesso do usuário para o nível do compartilhamento e do diretório/arquivo. A atribuição de permissão de nível de compartilhamento pode ser atribuída a usuários ou grupos do Azure AD gerenciados por meio do modelo de [RBAC (controle de acesso baseado em função)](../articles/role-based-access-control/overview.md) típico. Com o RBAC, as credenciais usadas para o acesso ao arquivo devem estar disponíveis ou sincronizadas com o Azure AD. Você pode atribuir funções RBAC internas como o leitor de compartilhamento SMB de dados de arquivo de armazenamento a usuários ou grupos no Azure AD para conceder acesso de leitura a um compartilhamento de arquivos do Azure.

No nível de diretório/arquivo, os arquivos do Azure dão suporte à preservação, herança e imposição de [DACLs do Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) , assim como qualquer servidor de arquivos do Windows. Se você copiar dados sobre o SMB de um compartilhamento de arquivos para arquivos do Azure, ou vice-versa, você pode optar por manter as DACLs do Windows. Se você planeja impor a autorização ou não, pode aproveitar os arquivos do Azure para fazer backup de ACLs junto com seus dados. 
