---
title: Solução de problemas da conta de automação
description: Aprenda a solucionar problemas e resolver problemas com uma conta do Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300999"
---
# <a name="automation-account-troubleshooting"></a>Solução de problemas da conta de automação

Este artigo discute soluções para problemas que você pode encontrar quando usa uma conta de Automação. As seções a seguir destacam mensagens de erro específicas e possíveis resoluções para cada uma. Para obter informações gerais sobre contas de automação, consulte [Criar uma conta do Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Não é possível registrar o Provedor de Recursos de Automação para assinaturas

### <a name="issue"></a>Problema

Quando você trabalha com soluções de gerenciamento em sua conta de Automação, você encontra o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O Provedor de Recursos de Automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para registrar o Provedor de Recursos de Automação, siga estas etapas no portal do Azure:

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. Navegue até **assinaturas** e selecione sua assinatura na página Assinaturas.   

3. Em **Configurações,** selecione **Provedores de recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos **Microsoft.Automation** está registrado.

5. Se não estiver listado, registre o provedor **Microsoft.Automation** seguindo as etapas em [Resolver erros para registro de provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Próximas etapas

Se você não viu o seu problema ou não conseguiu resolver o seu problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.