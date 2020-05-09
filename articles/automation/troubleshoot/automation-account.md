---
title: Solução de problemas da conta de automação do Azure
description: Saiba como solucionar problemas de uma conta do Azure e solucioná-los.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864123"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Solucionar problemas de uma conta de automação do Azure

Este artigo aborda soluções para problemas que você pode encontrar ao usar uma conta de automação do Azure. Para obter informações gerais sobre contas de automação, consulte [criar uma conta do Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: não é possível registrar o provedor de recursos de automação para assinaturas

### <a name="issue"></a>Problema

Ao trabalhar com soluções de gerenciamento em sua conta de automação, você encontrará o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O provedor de recursos de automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para registrar o provedor de recursos de automação, siga estas etapas na portal do Azure:

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. Vá para **assinaturas**e selecione sua assinatura.   

3. Em **configurações**, selecione **provedores de recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos **Microsoft. Automation** está registrado.

5. Se o provedor não estiver listado, registre-o conforme descrito em [resolver erros para o registro do provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Próximas etapas

Se este artigo não resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com. Esta é a conta de Microsoft Azure oficial para conectar a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**.