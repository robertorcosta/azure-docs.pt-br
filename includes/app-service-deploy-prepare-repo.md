---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67836734"
---
## <a name="prepare-your-repository"></a>Preparar o repositório

Para obter compilações automáticas de Azure App servidor de Build do Service kudu, verifique se a raiz do repositório tem os arquivos corretos em seu projeto.

| Runtime | Arquivos do diretório raiz |
|-|-|
| ASP.NET (somente Windows) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*. sln_ ou _*. csproj_ |
| PHP | _index.php_ |
| Ruby (somente Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ou _package.jsem_ com um script de início |
| Python | _ \* . py_, _requirements.txt_ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| Trabalhos Web | _\<job_name>/Run.\<extension>_ em _ \_ dados/trabalhos de aplicativo/contínuo_ para trabalhos Web contínuos _ou \_ dados/trabalhos de aplicativo/_ disparados para trabalhos Web disparados. Para obter mais informações, consulte a [documentação de trabalhos](https://github.com/projectkudu/kudu/wiki/WebJobs)Web do kudu. |
| Funções | Confira [Implantação contínua para Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar a implantação, você pode incluir um arquivo *.deployment* na raiz do repositório. Para obter mais informações, consulte [Personalizar implantações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script de implantação personalizada](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se você desenvolver no Visual Studio, permita que o [Visual Studio cria um repositório para você](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). O projeto está imediatamente pronto para ser implantado usando o git.
>

