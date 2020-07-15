---
author: baanders
description: incluir arquivo dos tutoriais dos Gêmeos Digitais do Azure – configurando o projeto de exemplo
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6edc6facc884fc6469b75b63216d6e5036321ea8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277924"
---
## <a name="configure-the-sample-project"></a>Configurar o projeto de exemplo

A seguir, configure um aplicativo cliente de exemplo que vai interagir com sua instância dos Gêmeos Digitais do Azure. Se você ainda não tiver baixado o projeto de exemplo, obtenha-o agora na página de aterrissagem [Exemplos de Gêmeos Digitais do Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples), selecionando o botão *Baixar ZIP* abaixo do título.

Navegue até o arquivo baixado no computador e descompacte-o.

Na pasta descompactada, navegue até _AdtSampleApp/_ . Abra _**AdtE2ESample.sln**_ no Visual Studio 2019. 

No Visual Studio, use o painel do *Gerenciador de Soluções* para criar uma cópia do arquivo _SampleClientApp > **serviceConfig.json.TEMPLATE**_ (você pode usar os menus de seleção com o botão direito do mouse para copiar e colar). Renomeie a cópia como *serviceConfig.json*. Ele funcionará como um arquivo JSON predefinido com as variáveis de configuração necessárias para executar o projeto.

Selecione o arquivo *serviceConfig.json* para abri-lo na janela de edição. Altere o `tenantId` para a *ID do diretório*, `clientId` para a *ID do aplicativo* e `instanceUrl` para a URL do *nome do host* de sua instância dos Gêmeos Digitais do Azure (com *https://* na frente, conforme mostrado abaixo).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Salve e feche o arquivo. 

Em seguida, configure o arquivo *serviceConfig.json* para ser copiado para o diretório de saída quando você criar o *SampleClientApp*. Para fazer isso, selecione com o botão direito do mouse o arquivo *serviceConfig.json* e escolha *Propriedades*. No inspetor de *Propriedades*, altere o valor da propriedade *Copiar para o Diretório de Saída* para *Copiar se mais recente*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Trecho da janela do Visual Studio mostrando o painel do Gerenciador de Soluções com serviceConfig.json realçado e o painel Propriedades com a propriedade 'Copiar para o Diretório de Saída' definida como 'Copiar se mais recente'" border="false":::

Mantenha o projeto _**AdtE2ESample**_ aberto no Visual Studio para continuar a usá-lo no tutorial.

