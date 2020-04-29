---
title: Solução de problemas da conta de automação
description: Saiba como solucionar problemas de uma conta do Azure e solucioná-los.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679391"
---
# <a name="troubleshoot-the-automation-account"></a>Solucionar problemas da conta de automação

Este artigo aborda soluções para problemas que você pode encontrar ao usar uma conta de automação. As seções a seguir realçam mensagens de erro específicas e as possíveis resoluções para cada uma. Para obter informações gerais sobre contas de automação, consulte [criar uma conta do Azure](../automation-quickstart-create-account.md).

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

2. Navegue até **assinaturas** e selecione sua assinatura na página assinaturas.   

3. Em **configurações**, selecione **provedores de recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos **Microsoft. Automation** está registrado.

5. Se o provedor não estiver listado, registre-o conforme descrito em [resolver erros para o registro do provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar o problema acima ou não conseguir resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.