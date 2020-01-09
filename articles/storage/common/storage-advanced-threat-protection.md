---
title: Configurar a proteção avançada contra ameaças
titleSuffix: Azure Storage
description: Configure a proteção avançada contra ameaças para o armazenamento do Azure para detectar anomalias na atividade de conta e ser notificado de tentativas potencialmente prejudiciais de acessar sua conta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 8efbf2107104f31bcfc5e4e5669dbed1835a034b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457403"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Configurar a proteção avançada contra ameaças para o armazenamento do Azure

A proteção avançada contra ameaças para o armazenamento do Azure fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança.

Os alertas de segurança são disparados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/)e também são enviados por email para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças.

> [!NOTE]
> A proteção avançada contra ameaças para o armazenamento do Azure está disponível no momento apenas para o armazenamento de BLOBs. Ele não está disponível nas regiões do Azure governamental e do soberanas Cloud. Para obter detalhes de preços, incluindo uma avaliação gratuita de 30 dias, consulte a [página de preços da central de segurança do Azure]( https://azure.microsoft.com/pricing/details/security-center/).

A proteção avançada contra ameaças para o armazenamento do Azure ingere logs de diagnóstico de solicitações de leitura, gravação e exclusão para o armazenamento de BLOB para detecção de ameaças. Para investigar os alertas da proteção avançada contra ameaças, você pode exibir a atividade de armazenamento relacionada usando o log de Análise de Armazenamento. Para obter mais informações, consulte **Configurar o log** em [monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurar a proteção avançada contra ameaças

A proteção avançada contra ameaças é habilitada para sua conta de armazenamento por padrão. Você pode configurar a proteção avançada contra ameaças de várias maneiras, descritas nas seções a seguir.

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Inicie o [Portal do Azure](https://portal.azure.com/).
1. Navegue até sua conta de armazenamento do Azure. Em **configurações**, selecione **segurança avançada**.
1. Selecione o link **configurações** na página configuração de segurança avançada.
1. Defina **segurança avançada** como **ativado**.
1. Clique em **salvar** para salvar a política nova ou atualizada.

    ![Ativar a proteção avançada contra ameaças do Armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-centertabazure-security-center"></a>[Central de Segurança do Azure](#tab/azure-security-center)

Quando você assina a camada Standard na central de segurança do Azure, a proteção avançada contra ameaças é configurada automaticamente em todas as suas contas de armazenamento. Você pode habilitar ou desabilitar a proteção avançada contra ameaças para suas contas de armazenamento em uma assinatura específica da seguinte maneira:

1. Inicie a **central de segurança do Azure** no [portal do Azure](https://portal.azure.com).
1. No menu principal, clique em **preços & configurações**.
1. Clique na assinatura que você deseja habilitar ou desabilitar a proteção contra ameaças para suas contas de armazenamento.

    ![Selecionar uma assinatura](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Clique em **Tipo de preço**.
1. Na seção **selecionar camada de preços por tipo de recurso** , na **linha contas de armazenamento** , clique em **habilitado** ou **desabilitado**.

    ![Habilitar ATP na central de segurança](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Clique em **Save** (Salvar).

### <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Use um modelo de Azure Resource Manager para implantar uma conta de armazenamento do Azure com a proteção avançada contra ameaças habilitada. Para obter mais informações, consulte [conta de armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policytabazure-policy"></a>[Azure Policy](#tab/azure-policy)

Use um Azure Policy para habilitar a proteção avançada contra ameaças em contas de armazenamento em uma assinatura ou grupo de recursos específico.

1. Inicie a página de **definições de política** do Azure.

1. Procure a política **implantar proteção avançada contra ameaças em contas de armazenamento** .

     ![Política de pesquisa](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Selecione uma assinatura do Azure ou um grupo de recursos.

    ![Selecionar assinatura ou grupo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Atribua a política.

    ![Página Definições de política](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-apitabrest-api"></a>[REST API](#tab/rest-api)

Use comandos da API REST para criar, atualizar ou obter a configuração de proteção avançada contra ameaças para uma conta de armazenamento específica.

* [Proteção avançada contra ameaças – criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada contra ameaças-obter](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Use os seguintes cmdlets do PowerShell:

* [Habilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Obtenha proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Desabilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Explorar anomalias de segurança

Quando ocorrerem anomalias na atividade de armazenamento, você receberá uma notificação por email com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* A natureza da anomalia
* o nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As possíveis causas
* As etapas de investigação
* As etapas de correção

O email também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

É possível examinar e gerenciar seus alertas de segurança atuais no [bloco Alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md) da Central de Segurança do Azure. Clicar em um alerta específico fornece detalhes e ações para investigar a ameaça atual e corrigir ameaças futuras.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertas de proteção

Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Para obter uma lista de alertas para o armazenamento do Azure, consulte a seção **armazenamento** em [detecção de ameaças para serviços de dados nos alertas da central de segurança do Azure](../../security-center/security-center-alerts-data-services.md#azure-storage)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [logs em contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-intro.md)
