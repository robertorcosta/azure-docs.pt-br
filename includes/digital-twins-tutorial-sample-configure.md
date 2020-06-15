---
author: baanders
description: incluir arquivo dos tutoriais dos Gêmeos Digitais do Azure – configurando o projeto de exemplo
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f7b3bf347b15ebad2403f3aa64c0f3fd6c4be052
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611787"
---
## <a name="configure-the-sample-project"></a>Configurar o projeto de exemplo

A seguir, configure um aplicativo cliente de exemplo que vai interagir com sua instância dos Gêmeos Digitais do Azure. Se ainda não tiver baixado o projeto de exemplo, obtenha-o agora [baixando o repositório de exemplos dos Gêmeos Digitais do Azure como um arquivo zip](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip). 

Navegue até o arquivo baixado no computador e descompacte-o.

Na pasta descompactada, navegue até _digital-twins-samples-master/AdtSampleApp/_ . Abra _**AdtE2ESample.sln**_ no Visual Studio 2019. 

No Visual Studio, use o painel do *Gerenciador de Soluções* para criar uma cópia do arquivo _SampleClientApp > **serviceConfig.json.TEMPLATE**_ (você pode usar os menus de seleção com o botão direito do mouse para copiar e colar). Renomeie a cópia como *serviceConfig.json*. Ele funcionará como um arquivo JSON predefinido com as variáveis de configuração necessárias para executar o projeto.

Selecione o novo arquivo para abri-lo na janela de edição. Altere o `tenantId` para a *ID do diretório*, `clientId` para a *ID do aplicativo* e `instanceUrl` para a URL do *nome do host* de sua instância dos Gêmeos Digitais do Azure (com *https://* na frente, conforme mostrado abaixo).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Salve e feche o arquivo. 

Em seguida, configure o arquivo *serviceConfig.json* para ser copiado para o diretório de saída quando você criar o *SampleClientApp*. Para fazer isso, selecione com o botão direito do mouse o arquivo *serviceConfig.json* e escolha *Propriedades*. No inspetor de *Propriedades*, altere o valor da propriedade *Copiar para o Diretório de Saída* para *Copiar se mais recente*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Trecho da janela do Visual Studio mostrando o painel do Gerenciador de Soluções com serviceConfig.json realçado e o painel Propriedades com a propriedade 'Copiar para o Diretório de Saída' definida como 'Copiar se mais recente'" border="false":::

Mantenha o projeto _**AdtE2ESample**_ aberto no Visual Studio para continuar a usá-lo no tutorial.

