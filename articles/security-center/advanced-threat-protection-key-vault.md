---
title: Proteção contra ameaças para o Cofre chave Do Azure
description: Este artigo explica como configurar proteção avançada contra ameaças para o Azure Key Vault no Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914799"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para o Azure Key Vault (visualização)

A proteção avançada contra ameaças do Azure Key Vault fornece uma camada adicional de inteligência de segurança. Esta ferramenta detecta tentativas potencialmente prejudiciais para acessar ou explorar contas do Key Vault. Usando a proteção de ameaças avançadas nativas no Azure Security Center, você pode lidar com ameaças sem ser um especialista em segurança e sem aprender sistemas adicionais de monitoramento de segurança.

Quando o Security Center detecta atividade anômala, ele exibe alertas. Ele também envia e-mails ao administrador de assinaturas com detalhes da atividade suspeita e recomendações de como investigar e remediar as ameaças identificadas.

## <a name="configuring-threat-protection-from-security-center"></a>Configurando proteção contra ameaças do Security Center

Por padrão, a proteção avançada contra ameaças é habilitada para todas as suas contas do Key Vault quando você assina o nível de preços padrão do Security Center. Para saber mais, consulte [Preços](security-center-pricing.md).

Para ativar ou desativar a proteção de uma assinatura específica:

1. No painel esquerdo no Security Center, selecione **Configurações de & de preços**.

1. Selecione a assinatura com as contas de armazenamento para as quais você deseja ativar ou desativar a proteção contra ameaças.

1. Selecione **o nível de preços .**

1. No nível de preços Select por grupo **de tipo de recurso,** encontre a linha **Principais Cofres** e selecione **Ativado** ou **desativado**.

    [![Ativando ou desativando proteção avançada contra ameaças para Key Vault no Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selecione **Salvar**.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como ativar e desativar a proteção avançada contra ameaças para o Azure Key Vault. 

Para material relacionado, consulte os seguintes artigos:

- [Proteção contra ameaças no Azure Security Center](threat-protection.md)--Este artigo descreve as fontes de alertas de segurança no Azure Security Center.
- [Alertas de segurança do Key Vault](alerts-reference.md#alerts-azurekv)--A seção Key Vault da tabela de referência para todos os alertas do Azure Security Center