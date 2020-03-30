---
title: Validação de alerta (arquivo de teste EICAR) no Azure Security Center | Microsoft Docs
description: Este documento ajuda a validar os alertas de segurança na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139990"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Validação de alerta (arquivo de teste EICAR) na Central de Segurança do Azure
Este documento ensina você a verificar se o sistema está configurado corretamente para os alertas da Central de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Alertas são as notificações que a Central de Segurança gera quando detecta ameaças em seus recursos. Prioriza e lista os alertas junto com as informações necessárias para investigar rapidamente o problema. A Central de Segurança também fornece recomendações sobre como corrigir um ataque.
Para obter mais informações, consulte [alertas de segurança no Security Center](security-center-alerts-overview.md) e gerenciamento e resposta a [alertas de segurança](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Validação de alerta

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Validar alertas em VMs do Windows<a name="validate-windows"></a>

Depois que o agente do Security Center for instalado no seu computador, siga estas etapas do computador onde você deseja ser o recurso atacado do alerta:

1. Copie um executável (por exemplo **calc.exe)** para a área de trabalho do computador ou outro diretório de sua conveniência e renomeie-o como **ASC_AlertTest_662jfi039N.exe**.
1. Abra o prompt de comando e execute este arquivo com um argumento (apenas um nome de argumento falso), como:```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra Alertas da Central de Segurança. Um alerta semelhante ao [exemplo](#alert-validate) abaixo deve ser exibido:

> [!NOTE]
> Ao revisar este alerta de teste para windows, certifique-se de que a **auditoria de argumentos de** campo habilitada seja **verdadeira**. Se for **falso,** então você precisa ativar a auditoria de argumentos de linha de comando. Para habilitá-lo, use o seguinte comando:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Validar alertas em VMs Linux<a name="validate-linux"></a>

Depois que o agente do Security Center for instalado no seu computador, siga estas etapas do computador onde você deseja ser o recurso atacado do alerta:
1. Copie um executável para um local conveniente e renomeie-o para **./asc_alerttest_662jfi039n,** por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o prompt de comando e execute este arquivo:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra Alertas da Central de Segurança. Um alerta semelhante ao [exemplo](#alert-validate) abaixo deve ser exibido:

### <a name="alert-example"></a>Exemplo de alerta<a name="alert-validate"></a>

![Exemplo de validação de alerta](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Validar alertas em Kubernetes<a name="validate-kubernetes"></a>

Se você estiver usando o recurso de visualização do Security Center de integração do Azure Kubernetes Service, execute o seguinte comando kubectl para testar se seus alertas estão funcionando:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Para obter mais informações sobre a integração do Azure Kubernetes Service e do Azure Security Center, consulte [este artigo](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou a você o processo de validação de alertas. Agora que você está familiarizado com esse tipo de validação, experimente os seguintes artigos:

* [Validando a detecção de ameaças do cofre chave do Azure no Centro de Segurança do Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gerenciamento e resposta a alertas de segurança no Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - Saiba como gerenciar alertas e responder a incidentes de segurança no Security Center.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Entender os alertas de segurança no Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) - Conheça os diferentes tipos de alertas de segurança.
