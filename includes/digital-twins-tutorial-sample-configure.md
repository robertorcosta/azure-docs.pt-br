---
author: baanders
description: incluir arquivo dos tutoriais dos Gêmeos Digitais do Azure – configurando o projeto de exemplo
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ff44e4ae45f8b7c6bff3685522440ca7118f337e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92346928"
---
## <a name="configure-the-sample-project"></a>Configurar o projeto de exemplo

A seguir, configure um aplicativo cliente de exemplo que vai interagir com sua instância dos Gêmeos Digitais do Azure.

Navegue no computador até o arquivo baixado anteriormente de [*Amostras de ponta a ponta dos Gêmeos Digitais do Azure*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (e descompacte-o se ainda não tiver feito isso).

Na pasta, navegue até _AdtSampleApp_ . Abra _**AdtE2ESample.sln**_ no Visual Studio 2019. 

No Visual Studio, selecione o arquivo _SampleClientApp > **appsettings.json**_ para abri-lo na janela de edição. Ele funcionará como um arquivo JSON predefinido com as variáveis de configuração necessárias para executar o projeto.

No corpo do arquivo, altere o `instanceUrl` para a URL do *hostName* de sua instância dos Gêmeos Digitais do Azure (com *https://* na frente, conforme mostrado abaixo).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Salve e feche o arquivo. 

Em seguida, configure o arquivo *appSettings.json* para ser copiado para o diretório de saída quando você criar o *SampleClientApp* . Para fazer isso, selecione com o botão direito do mouse o arquivo *appSettings.json* e escolha *Propriedades* . No inspetor de *Propriedades* , altere o valor da propriedade *Copiar para o Diretório de Saída* para *Copiar se mais recente* .

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Trecho da janela do Visual Studio mostrando o painel do Gerenciador de Soluções com appSettings.json realçado e o painel Propriedades com a propriedade 'Copiar para o Diretório de Saída' definida como 'Copiar se mais recente'" border="false":::

Mantenha o projeto _**AdtE2ESample**_ aberto no Visual Studio para continuar a usá-lo no tutorial.

