---
author: baanders
description: incluir arquivo para os Gêmeos Digitais do Azure – cita o problema conhecido com a autenticação do Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92321041"
---
>[!NOTE]
>Atualmente, há um **problema conhecido** no Cloud Shell afetando estes grupos de comandos quando fazemos a execução de *https://shell.azure.com* : `az dt route`, `az dt model`, `az dt twin`.
>
>Para resolver, você pode fazer qualquer um dos seguintes:
> * Execute `az login` no Cloud Shell antes de executar o comando.
> * Abra o painel do Cloud Shell na portal do Azure e conclua o trabalho do Cloud Shell de lá.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Exibição do portal do Azure com o ícone 'Cloud Shell' realçado e o Cloud Shell aparecendo na parte inferior da janela do portal":::
> * Use a [CLI local](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em vez do Cloud Shell.
>
>Para obter mais detalhes sobre esse problema, confira [*Solução de problemas: Problemas conhecidos nos Gêmeos Digitais do Azure*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).