---
title: Azure defender para Key Vault-os benefícios e recursos
description: Saiba mais sobre os benefícios e recursos do Azure defender para Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 78a522922f8580003e2d2ff588cbf10dbf5fff9d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301764"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Introdução ao Azure defender para Key Vault

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

Habilite o **Azure defender para Key Vault** para proteção de ameaças avançadas e nativas do azure para Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. 

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Refere|O **Azure defender para Key Vault** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Quais são os benefícios do Azure defender para Key Vault?

O Azure defender detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Key Vault. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança e sem precisar gerenciar sistemas de monitoramento de segurança de terceiros.  

Quando ocorrem atividades anômalas, o Azure defender mostra alertas e, opcionalmente, os envia por email para os membros relevantes da sua organização. Esses alertas incluem detalhes das atividades suspeitas e recomendações sobre como investigar e corrigir ameaças. 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure defender para alertas de Key Vault
Quando receber um alerta do Azure defender para Key Vault, recomendamos que você investigue e responda ao alerta conforme descrito em [responder ao Azure defender para Key Vault](defender-for-key-vault-usage.md). O Azure defender para Key Vault protege aplicativos e credenciais, portanto, mesmo que você esteja familiarizado com o aplicativo ou o usuário que disparou o alerta, é importante verificar a situação ao redor de cada alerta.

Os alertas são exibidos na página **segurança** do Key Vault, no painel do Azure defender e na página de alertas da central de segurança.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Página de segurança do Azure Key Vault":::


> [!TIP]
> Você pode simular os alertas do Azure defender para Key Vault seguindo as instruções em [validando Azure Key Vault detecção de ameaças na central de segurança do Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure defender para Key Vault.

Para obter material relacionado, consulte os seguintes artigos: 

- [Key Vault alertas de segurança](alerts-reference.md#alerts-azurekv)– a seção Key Vault da tabela de referência para todos os alertas da central de segurança do Azure
- [Exportando alertas para um SIEM](continuous-export.md)
- [Suprimir alertas do Azure defender](alerts-suppression-rules.md)