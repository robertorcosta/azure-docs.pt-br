---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945141"
---
## <a name="create-a-project-zip-file"></a>Criar um projeto de arquivo zip

>[!NOTE]
> Se você fez o download dos arquivos em um arquivo zip, primeiro extraia os arquivos. Por exemplo, se você fez o download de um arquivo zip do GitHub, não será possível implantar esse arquivo. O GitHub adiciona diretórios aninhados adicionais que não funcionam com o Serviço de Aplicativo. 
>

Em uma janela do terminal local, navegue até o diretório raiz do projeto do aplicativo. 

Esse diretório deve conter o arquivo de entrada no seu aplicativo Web, como _index.html_, _index.php_ e _app.js_. Além disso, é possível conter arquivos de gerenciamento de pacotes como _project.json_, _composer.json_, _package.json_, _bower.json_ e _requirements.txt_.

A menos que você queira que o serviço de aplicativo execute a automação de implantação para você, execute todas as `npm`tarefas `bower`de `gulp`compilação `composer`(por `pip`exemplo,,,, e) e verifique se você tem todos os arquivos necessários para executar o aplicativo. Esta etapa será necessária se você quiser [executar o pacote diretamente](../articles/app-service/deploy-run-package.md).

Criar um arquivo zip de tudo em seu projeto. O comando a seguir usa a ferramenta padrão em seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

