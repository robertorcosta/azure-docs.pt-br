---
title: Configure proteção avançada contra ameaças
titleSuffix: Azure Storage
description: Configure proteção avançada contra ameaças para o Azure Storage para detectar anomalias na atividade da conta e seja notificado de tentativas potencialmente prejudiciais de acessar sua conta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 27860b8761c565c45a604253efdff5f77606606e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061307"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Configure proteção avançada contra ameaças para armazenamento Azure

A proteção avançada contra ameaças para o Azure Storage fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você aborde ameaças sem ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança.

Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados ao [Azure Security Center](https://azure.microsoft.com/services/security-center/), e também são enviados por e-mail para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças.

> [!NOTE]
> A proteção avançada contra ameaças para o Azure Storage está disponível atualmente apenas para armazenamento Blob. Não está disponível no governo azure e regiões de nuvens soberanas. Para obter detalhes sobre preços, incluindo um teste gratuito de 30 dias, consulte a página de preços do [Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).

A proteção avançada contra ameaças para o Azure Storage ingere registros de diagnóstico de leitura, gravação e exclusão de solicitações ao armazenamento Blob para detecção de ameaças. Para investigar os alertas de proteção avançada contra ameaças, você pode visualizar atividades de armazenamento relacionadas usando o Registro de Análisede Armazenamento. Para obter mais informações, consulte **Configurar o login** no Monitor e monitorar uma conta de armazenamento no portal [Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configure proteção avançada contra ameaças

Você pode configurar proteção avançada contra ameaças de várias maneiras, descritas nas seções a seguir.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie [o portal Azure.](https://portal.azure.com/)
1. Navegue até sua conta de armazenamento azure. Em **Configurações,** selecione **Segurança avançada**.
1. Selecione o link **Configurações** na página de configuração de segurança avançada.
1. Definir **segurança avançada** para **ON**.
1. Clique **em Salvar** para salvar a política nova ou atualizada.

    ![Ativar a proteção avançada contra ameaças do Armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Centro de Segurança Azure](#tab/azure-security-center)

Quando você assina o nível Padrão no Azure Security Center, a proteção avançada contra ameaças é configurada automaticamente em todas as suas contas de armazenamento. Você pode ativar ou desativar proteção avançada contra ameaças para suas contas de armazenamento uma assinatura específica da seguinte forma:

1. Inicie **o Azure Security Center** no portal [Azure](https://portal.azure.com).
1. No menu principal, clique **em Configurações de preços &**.
1. Clique na assinatura que deseja habilitar ou desativar a proteção contra ameaças para suas contas de armazenamento.

    ![Selecionar uma assinatura](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Clique **em Nível de preços .**
1. Na **seção Selecionar nível de preço por** tipo de recurso, na linha Contas de **armazenamento,** clique em **Ativado** ou **desativado**.

    ![Habilitar ATP no Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Clique em **Salvar**.

### <a name="template"></a>[Modelo](#tab/template)

Use um modelo do Azure Resource Manager para implantar uma conta do Azure Storage com proteção avançada de ameaças ativada. Para obter mais informações, consulte [A conta armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Política do Azure](#tab/azure-policy)

Use uma diretiva do Azure para permitir proteção avançada contra ameaças em contas de armazenamento um grupo específico de assinatura ou recurso.

1. Inicie a página **Azure Policy - Definições.**

1. Procure a **política Implantar Proteção avançada contra ameaças em contas de armazenamento.**

     ![Política de pesquisa](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Selecione um grupo de recursos ou assinatura do Azure.

    ![Selecionar assinatura ou grupo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Atribua a apólice.

    ![Página definições de políticas](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Use comandos de API rest para criar, atualizar ou obter a configuração avançada de proteção contra ameaças para uma conta de armazenamento específica.

* [Proteção avançada contra ameaças - Criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada contra ameaças - Obter](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Use os seguintes cmdlets powershell:

* [Habilite a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Obtenha proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Desativar proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Explorar anomalias de segurança

Quando ocorrerem anomalias na atividade de armazenamento, você receberá uma notificação por email com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* A natureza da anomalia
* o nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As causas potenciais
* As etapas da investigação
* As etapas de remediação

O email também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Você pode revisar e gerenciar seus alertas de segurança atuais da telha de [alertas](../../security-center/security-center-managing-and-responding-alerts.md)de segurança do Azure Security Center . Clicar em um alerta específico fornece detalhes e ações para investigar a ameaça atual e corrigir ameaças futuras.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Alertas de segurança

Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Para obter uma lista de alertas para o Armazenamento Azure, consulte a seção **Armazenamento** em [Proteção contra Ameaças para serviços de dados no Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [logs em contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-intro.md)
