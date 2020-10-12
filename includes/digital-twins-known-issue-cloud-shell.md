---
author: baanders
description: incluir arquivo para os Gêmeos Digitais do Azure – cita o problema conhecido com a autenticação do Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290083"
---
>[!NOTE]
>Atualmente, há um **problema conhecido** no Cloud Shell afetando estes grupos de comandos: `az dt route`, `az dt model` e `az dt twin`.
>
>Para resolver, execute `az login` no Cloud Shell antes de executar o comando ou use a [CLI local](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em vez do Cloud Shell. Para obter mais detalhes sobre isso, confira [*Solução de problemas: Problemas conhecidos nos Gêmeos Digitais do Azure*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).