---
title: Ferramenta de gerenciamento de desktop virtual do Windows - Azure
description: Como solucionar problemas com a ferramenta de gerenciamento do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127495"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Solucionar problemas da ferramenta de gerenciamento da Área de Trabalho Virtual do Windows

Este artigo descreve problemas que podem ocorrer durante a implantação da ferramenta de gerenciamento do Windows Virtual Desktop e como corrigi-los.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Erro: Serviços de ferramenta de gerenciamento configurados, mas falha na configuração automatizada

Quando você configurar com sucesso serviços para a ferramenta de gerenciamento, mas a configuração automatizada falha, você verá esta mensagem de erro:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Isso geralmente significa uma das duas coisas a seguir:

- O usuário tem permissões do proprietário em sua assinatura e administrador global no nível de inquilino, mas não pode fazer login no Azure.
- As configurações da conta do usuário têm a autenticação multifatorial ativada.

Para corrigir isso:

1. Certifique-se de que o usuário criado para o Nome Principal do Usuário do Diretório Ativo do Azure tenha o nível de assinatura "Contribuinte".
2. Faça login para <portal.azure.com> com a conta UPN para verificar as configurações da conta e garantir que a autenticação multifatorial não esteja em dia. Se estiver ligado, desligue.
3. Visite a página de consentimento da área de trabalho virtual do Windows e certifique-se de que os aplicativos do servidor e do cliente tenham consentimento.
4. Revise o [tutorial implantar uma ferramenta de gerenciamento](manage-resources-using-ui.md) se o problema continuar e reimplantar a ferramenta.

## <a name="error-job-with-specified-id-already-exists"></a>Erro: O trabalho com ID especificado já existe

Se o usuário vir a mensagem de erro "Trabalho com ID especificado já existe", é porque ele não forneceu um nome único no parâmetro "Nome do aplicativo" ao implantar o modelo.

Para corrigir isso, reimplante a ferramenta de gerenciamento com o parâmetro "Nome do aplicativo" preenchido.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Solicitação de consentimento atrasada ao abrir ferramenta de gerenciamento

Quando você implantar a ferramenta de gerenciamento, o prompt de consentimento pode não abrir imediatamente. Isso significa que o serviço de aplicativo Web do Azure está demorando mais do que o normal para carregar. O prompt deve aparecer depois que o Azure Web terminar de carregar.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>O usuário não pode implantar a ferramenta de gerenciamento na região leste dos EUA

Se um cliente define a região para o Leste dos EUA, ele não pode implantar a ferramenta de gerenciamento.

Para corrigir isso, implante a ferramenta de gestão em uma região diferente. A reimplantação da ferramenta em uma região diferente não deve afetar a experiência do usuário.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as faixas de escalonamento em [Visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de solução de problemas .
- Saiba como relatar problemas com as ferramentas de desktop virtual do Windows em [modelos ARM para serviços de desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Para obter uma visão geral sobre a solução de problemas do Windows Virtual Desktop e as faixas de escalonamento, consulte [Visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de solução de problemas.
- Para saber como implantar a ferramenta de gerenciamento, consulte [Implantar uma ferramenta de gerenciamento](manage-resources-using-ui.md).