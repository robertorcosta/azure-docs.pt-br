---
title: incluir arquivo
description: incluir arquivo
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461207"
---
## <a name="use-cli-shell"></a>Usar o shell da CLI

É recomendável usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) para executar comandos da CLI. O **Cloud Shell** é um shell interativo e grátis que pode ser usado para executar as etapas deste artigo. Ferramentas comuns do Azure estão pré-instaladas e configuradas para uso com sua conta. Ele dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha. Usuários do Linux podem optar por uma experiência com o Bash, enquanto usuários do Windows podem optar pelo PowerShell.

Você também pode instalar a CLI localmente. Confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções para sua plataforma.

### <a name="sign-in"></a>Entrar

O uso de uma instalação local da CLI exige que você entre no Azure. Essa etapa não é necessária para o Azure Cloud Shell. Entre com o comando `az login`.

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, será necessário abrir uma página do navegador e seguir as instruções na linha de comando para inserir um código de autorização depois de navegar até https://aka.ms/devicelogin no navegador.

### <a name="specify-location-of-files"></a>Especificar o local dos arquivos

Muitos comandos de CLI dos Serviços de Mídia permitem que você passe um parâmetro com um nome de arquivo. Se você estiver usando o **Cloud Shell**, você pode carregar seu arquivo no seu clouddrive (usando Bash ou PowerShell). 

![Carregar arquivos]

Se você estiver usando uma CLI local ou o **Cloud Shell**, você precisa especificar o caminho do arquivo de acordo com o sistema operacional ou o Cloud Shell (Bash ou PowerShell) que você está usando. Veja abaixo alguns exemplos:

Caminho relativo para a iteração (todos os sistemas operacionais)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Caminho de arquivo absoluto no sistema operacional Linux/Mac e Windows

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Use `{file}` se o comando estiver solicitando um caminho para o arquivo. Por exemplo, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Use `@{file}` se o comando for carregar o arquivo especificado. Por exemplo, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Carregar arquivos]: ./media/media-services-cli/upload-download-files.png
