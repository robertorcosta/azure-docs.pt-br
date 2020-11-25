---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995450"
---
O SMB Multichannel para compartilhamentos de arquivos do Azure atualmente tem as seguintes restrições:
- Só pode ser usado com contas de armazenamento de repositórios localmente redundantes.
- Somente com suporte para clientes do Windows. 
- O número máximo de canais é quatro.
- Não há suporte para SMB Direct.
- Para contas de armazenamento com Active Directory Domain Services local (AD DS) ou [autenticação baseada em identidade](../articles/storage/files/storage-files-active-directory-overview.md) do Azure AD DS habilitada para arquivos do Azure, os clientes SMB não poderão usar o explorador de arquivos do Windows para configurar permissões NTFS em diretórios e arquivos.
    - Use a ferramenta [icacls](/windows-server/administration/windows-commands/icacls) do Windows ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) em vez de configurar permissões.

