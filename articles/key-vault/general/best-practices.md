---
title: Práticas recomendadas para usar o Key Vault-Azure Key Vault | Microsoft Docs
description: Este documento explica algumas das práticas recomendadas para usar Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 2a81d28998c531339a1b719e37e4b90004157edf
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005843"
---
# <a name="best-practices-to-use-key-vault"></a>Práticas recomendadas para usar Key Vault

## <a name="control-access-to-your-vault"></a>Controlar o acesso ao seu cofre

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Este [artigo](secure-your-key-vault.md) fornece uma visão geral do modelo de acesso Key Vault. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus cofres de chaves.

As sugestões ao controlar o acesso ao cofre são as seguintes:
1. Bloquear o acesso à sua assinatura, grupo de recursos e cofres de chaves (RBAC)
2. Criar políticas de acesso para cada cofre
3. Use a entidade de acesso de privilégios mínimos para conceder acesso
4. Ativar [pontos de extremidade de serviço de rede virtual](overview-vnet-service-endpoints.md) e firewall

## <a name="use-separate-key-vault"></a>Usar Key Vault separadas

Nossa recomendação é usar um cofre por aplicativo por ambiente (desenvolvimento, pré-produção e produção). Isso ajuda você a não compartilhar segredos entre ambientes e também reduz a ameaça em caso de uma violação.

## <a name="backup"></a>Backup

Certifique-se de fazer backups regulares de seu cofre em atualizar/excluir/criar objetos em um cofre.

## <a name="turn-on-logging"></a>Ativar registro em log

[Ative o registro em log](logging.md) para seu cofre. Configure também alertas.

## <a name="turn-on-recovery-options"></a>Ativar opções de recuperação

1. Ative a [exclusão reversível](overview-soft-delete.md).
2. Ative a proteção de limpeza se você quiser se proteger contra a exclusão forçada do segredo/cofre mesmo depois que a exclusão reversível estiver ativada.
