---
title: Ferramenta de gerenciamento de área de trabalho virtual do Windows-Azure
description: Como solucionar problemas com a ferramenta de gerenciamento de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127495"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Solucionar problemas da ferramenta de gerenciamento da Área de Trabalho Virtual do Windows

Este artigo descreve problemas que podem ocorrer durante a implantação da ferramenta de gerenciamento de área de trabalho virtual do Windows e como corrigi-las.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Erro: serviços da ferramenta de gerenciamento configurados, mas a instalação automatizada falha

Quando você configurar os serviços para a ferramenta de gerenciamento com êxito, mas a instalação automatizada falhar, você verá esta mensagem de erro:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Isso geralmente significa uma das duas coisas a seguir:

- O usuário tem permissões de proprietário em sua assinatura e administrador global no nível do locatário, mas eles não podem entrar no Azure.
- As configurações de conta do usuário têm a autenticação multifator habilitada.

Para corrigir isso:

1. Verifique se o usuário criado para o Azure Active Directory nome principal do usuário tem o nível de assinatura "colaborador".
2. Entre no portal do <. Azure. com > com a conta de UPN para verificar as configurações da conta e se certificar de que a autenticação multifator não esteja ativada. Se ele estiver ativado, desative-o.
3. Visite a página de consentimento da área de trabalho virtual do Windows e verifique se os aplicativos cliente e servidor têm consentimento.
4. Examine o tutorial [implantar ferramenta de gerenciamento](manage-resources-using-ui.md) se o problema continuar e reimplantar a ferramenta.

## <a name="error-job-with-specified-id-already-exists"></a>Erro: o trabalho com a ID especificada já existe

Se o usuário vir a mensagem de erro "o trabalho com a ID especificada já existe", é porque não forneceu um nome exclusivo no parâmetro "nome do aplicativo" ao implantar o modelo.

Para corrigir isso, reimplante a ferramenta de gerenciamento com o parâmetro "nome do aplicativo" preenchido.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Prompt de consentimento atrasado ao abrir a ferramenta de gerenciamento

Quando você implanta a ferramenta de gerenciamento, a solicitação de consentimento pode não abrir imediatamente. Isso significa que o serviço de aplicativo Web do Azure está demorando mais do que o normal para carregar. O prompt deve aparecer depois que a Web do Azure terminar de ser carregada.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>O usuário não pode implantar a ferramenta de gerenciamento na região leste dos EUA

Se um cliente definir a região para o leste dos EUA, ele não poderá implantar a ferramenta de gerenciamento.

Para corrigir isso, implante a ferramenta de gerenciamento em uma região diferente. Reimplantar a ferramenta em uma região diferente não deve afetar a experiência do usuário.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Saiba mais sobre as faixas de escalonamento em [visão geral, comentários e suporte da solução de problemas](troubleshoot-set-up-overview.md).
- Saiba como relatar problemas com as ferramentas de área de trabalho virtual do Windows em [modelos ARM para serviços de área de trabalho remota](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para saber como implantar a ferramenta de gerenciamento, consulte [implantar uma ferramenta de gerenciamento](manage-resources-using-ui.md).