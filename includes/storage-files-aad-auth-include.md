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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536567"
---
[O Azure Files](../articles/storage/files/storage-files-introduction.md) suporta autenticação baseada em identidade sobre o SMB (Server Message Block) através [de Serviços de Domínio de Diretório Ativo (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (visualização) e Serviços de [Domínio do Diretório Ativo do Azure (Azure AD DS).](../articles/active-directory-domain-services/overview.md) Este artigo se concentra em como os compartilhamentos de arquivos do Azure podem usar serviços de domínio, no local ou no Azure, para suportar o acesso baseado em identidade a compartilhamentos de arquivos Do Azure sobre o SMB. A habilitação do acesso baseado em identidade para os compartilhamentos de arquivos do Azure permite que você substitua os servidores de arquivos existentes por compartilhamentos de arquivos Do Azure sem substituir o serviço de diretório existente, mantendo o acesso perfeito do usuário aos compartilhamentos. 

O Azure Files impõe a autorização no acesso do usuário ao compartilhamento e aos níveis de diretório/arquivo. A atribuição de permissão em nível de compartilhamento pode ser executada em usuários ou grupos do Azure Active Directory (Azure AD) gerenciados através do modelo [RBAC (Role-based Access Control, controle de acesso baseado em função).](../articles/role-based-access-control/overview.md) Com o RBAC, as credenciais que você usa para acesso a arquivos devem estar disponíveis ou sincronizadas com o Azure AD. Você pode atribuir funções de RBAC incorporadas, como o Storage File Data SMB Share Reader, a usuários ou grupos no Azure AD para conceder acesso à leitura de um compartilhamento de arquivos Azure.

No nível de diretório/arquivo, o Azure Files suporta preservar, herdar e aplicar [OS DACLs do Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) como qualquer servidor de arquivos windows. Você pode optar por manter os DACLs do Windows ao copiar dados sobre SMB entre o compartilhamento de arquivos existente e os compartilhamentos de arquivos do Azure. Se você planeja impor a autorização ou não, você pode usar os compartilhamentos de arquivos do Azure para fazer backup de ACLs junto com seus dados. 
