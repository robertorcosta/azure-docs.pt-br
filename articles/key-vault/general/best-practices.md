---
title: Práticas recomendadas para usar key vault - Azure Key Vault | Microsoft Docs
description: Este documento explica algumas das melhores práticas para usar o Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 57bb897978f47a66adebac069d8892d596ba78f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430858"
---
# <a name="best-practices-to-use-key-vault"></a>Práticas recomendadas para usar key vault

## <a name="control-access-to-your-vault"></a>Controle o acesso ao seu cofre

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Este [artigo](secure-your-key-vault.md)) fornece uma visão geral do modelo de acesso do Key Vault. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus cofres de chaves.

Sugestões ao controlar o acesso ao seu cofre são as seguintes:
1. Bloqueie o acesso à sua assinatura, grupo de recursos e Key Vaults (RBAC)
2. Crie políticas de acesso para cada cofre
3. Use o principal de acesso menos privilegiado para conceder acesso
4. Ativar firewall e [pontos finais de serviço VNET](overview-vnet-service-endpoints.md))

## <a name="use-separate-key-vault"></a>Use o cofre de chaves separado

Nossa recomendação é usar um cofre por aplicativo por ambiente (Desenvolvimento, Pré-Produção e Produção). Isso ajuda você a não compartilhar segredos entre ambientes e também reduz a ameaça em caso de violação.

## <a name="backup"></a>Backup

Certifique-se de fazer backups regulares do [cofre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) em atualização/exclusão/criação de objetos dentro de um Cofre.

## <a name="turn-on-logging"></a>Ativar registro

[Ativar o registro](logging.md)) para o seu Cofre. Também configure alertas.

## <a name="turn-on-recovery-options"></a>Ativar opções de recuperação

1. Ligue [o Soft Delete](overview-soft-delete.md)).
2. Ligue a proteção contra expurgo se quiser se proteger contra a exclusão da força do segredo / cofre mesmo depois que a exclusão suave é ligada.
