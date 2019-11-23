---
title: Nome de servidor de alias do Azure Analysis Services | Microsoft Docs
description: Saiba como criar Azure Analysis Services aliases de nome de servidor. Os usuários podem, então, se conectar ao seu servidor com um nome de alias mais curto em vez do nome do servidor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572286"
---
# <a name="alias-server-names"></a>Nome de servidor de alias

Usando um alias de nome de servidor, os usuários podem se conectar ao seu servidor do Azure Analysis Services com um *alias* mais curto em vez do nome do servidor. Ao conectar-se usando um aplicativo cliente, o alias é especificado como um ponto de extremidade usando o formato de protocolo **link://** . O ponto de extremidade então retorna o nome de servidor real para se conectar.

Nomes de servidor de alias são bons para:

- Migrar modelos entre servidores sem afetar os usuários. 
- Nomes amigáveis de servidor são mais fáceis de serem lembrados pelos usuários. 
- Direcionar os usuários para diferentes servidores em diferentes momentos do dia. 
- Direcionar os usuários em regiões diferentes para instâncias que são geograficamente mais próximas, como ao usar o Azure Traffic Manager. 

Qualquer ponto de extremidade HTTPS que retorne um nome de servidor válido do Azure Analysis Services pode atender como um alias. O ponto de extremidade deve dar suporte a HTTPS na porta 443 e a porta não deve ser especificada no URI.

![Alias usando o formato de link](media/analysis-services-alias/aas-alias-browser.png)

Ao conectar-se usando um cliente, o nome do servidor de alias é inserido usando o formato do protocolo **link://** . Por exemplo, no Power BI Desktop:

![Conexão do Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Criar um alias

Para criar um ponto de extremidade de alias, você pode usar qualquer método que retorne um nome de servidor válido do Azure Analysis Services. Por exemplo, uma referência a um arquivo no Armazenamento de Blobs do Azure que contenha o nome real do servidor, ou criar e publicar um aplicativo Web Forms do ASP.NET.

Neste exemplo, um aplicativo Web Forms do ASP.NET é criado no Visual Studio. A referência da página mestra e o controle de usuário são removidos da página Default.aspx. O conteúdo de Default.aspx é simplesmente a seguinte diretiva de página:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

O evento Page_Load em Default.aspx.cs usa o método Response.Write() para retornar o nome do servidor do Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Consulte também

[Bibliotecas do cliente](analysis-services-data-providers.md)   
[Conectar-se a partir do Power BI Desktop](analysis-services-connect-pbi.md)
