---
title: Proteção contra ameaças para Azure Key Vault
description: Este artigo explica como configurar a proteção avançada contra ameaças para Azure Key Vault na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914799"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para Azure Key Vault (versão prévia)

A proteção avançada contra ameaças para Azure Key Vault fornece uma camada adicional de inteligência de segurança. Essa ferramenta detecta tentativas potencialmente perigosas de acessar ou explorar contas de Key Vault. Usando a proteção de ameaças avançadas nativa na central de segurança do Azure, você pode lidar com ameaças sem ser um especialista em segurança e sem aprender sistemas de monitoramento de segurança adicionais.

Quando a central de segurança detecta a atividade anômala, ela exibe alertas. Ele também envia emails ao administrador da assinatura com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças identificadas.

## <a name="configuring-threat-protection-from-security-center"></a>Configurando a proteção contra ameaças na central de segurança

Por padrão, a proteção avançada contra ameaças está habilitada para todas as suas contas de Key Vault quando você assina o tipo de preço Standard da central de segurança. Para saber mais, consulte [Preços](security-center-pricing.md).

Para habilitar ou desabilitar a proteção para uma assinatura específica:

1. No painel esquerdo na central de segurança, selecione **preços & configurações**.

1. Selecione a assinatura com as contas de armazenamento para as quais você deseja habilitar ou desabilitar a proteção contra ameaças.

1. Selecione **Tipo de preço**.

1. No grupo **selecionar camada de preços por tipo de recurso** , localize a linha **cofres de chaves** e selecione **habilitado** ou **desabilitado**.

    [![habilitar ou desabilitar a proteção avançada contra ameaças para Key Vault na central de segurança do Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selecione **Salvar**.


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste artigo, você aprendeu a habilitar e desabilitar a proteção avançada contra ameaças para Azure Key Vault. 

Para obter material relacionado, consulte os seguintes artigos:

- [Proteção contra ameaças na central de segurança do Azure](threat-protection.md)– este artigo descreve as fontes de alertas de segurança na central de segurança do Azure.
- [Key Vault alertas de segurança](alerts-reference.md#alerts-azurekv)– a seção Key Vault da tabela de referência para todos os alertas da central de segurança do Azure