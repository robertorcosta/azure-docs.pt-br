---
title: Práticas recomendadas para usar o Key Vault-Azure Key Vault | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas para Azure Key Vault, incluindo o controle de acesso, quando usar cofres de chaves separados, backup, log e opções de recuperação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4a522e58677e2c71cb52ad7e04b288622ad62d91
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287326"
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

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell comandos de backup

* [Certificado de backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [Chave de backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [Segredo de backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>CLI do Azure comandos de backup

* [Certificado de backup](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [Chave de backup](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [Segredo de backup](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Ativar registro em log

[Ative o registro em log](logging.md) para seu cofre. Configure também alertas.

## <a name="turn-on-recovery-options"></a>Ativar opções de recuperação

1. Ative a [exclusão reversível](soft-delete-overview.md).
2. Ative a proteção de limpeza se você quiser se proteger contra a exclusão forçada do segredo/cofre mesmo depois que a exclusão reversível estiver ativada.