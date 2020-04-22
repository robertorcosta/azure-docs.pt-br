---
title: Bibliotecas clientes do Azure Analysis Services | Microsoft Docs
description: Descreve as bibliotecas de cliente necessárias às ferramentas e ao aplicativos cliente para conectar o Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: be6ee594896384bce86d892c9a7811e09a0f4efd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676573"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Bibliotecas de clientes para conexão com serviços de análise

Bibliotecas de cliente são necessárias para que ferramentas e aplicativos cliente se conectem aos servidores do Analysis Services. Os aplicativos clientes da Microsoft como Power BI Desktop, Excel, SQL Server Management Studio (SSMS) e serviços de análise para o Visual Studio instalam todas as três bibliotecas de clientes e as atualizam, juntamente com atualizações regulares de aplicativos. Em alguns casos, você pode precisar instalar versões mais recentes das bibliotecas clientes. Aplicativos clientepersonalizados personalizados também exigem que bibliotecas de clientes sejam instaladas.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Baixar as bibliotecas de clientes mais recentes (Windows Installer)  

|Baixar  |Versão do produto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.36.38    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.36.38       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.6.0.1    |
|[Adomd](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.6.0.1     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO e ADOMD (pacotes do NuGet)

As bibliotecas de clientes AMO e ADOMD estão disponíveis como pacotes instalados [NuGet.org](https://www.nuget.org/). Recomenda-se migrar para referências NuGet em vez de usar o Windows Installer. 

|Pacote  | Versão do produto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.6.0.1     |
|[Adomd](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.6.0.1      |

Os assemblies de pacotes NuGet AssemblyVersion seguem a versão semântica: MAJOR.MINOR.PATCH. As referências de NuGet carregam a versão esperada, mesmo que haja uma versão diferente no GAC (resultante da instalação do MSI). PATCH é incrementado para cada versão. As versões AMO e ADOMD são mantidas em sincronia.

## <a name="understanding-client-libraries"></a>Noções básicas sobre bibliotecas de clientes

O Analysis Services utiliza três bibliotecas de clientes, também conhecidas como provedores de dados. O ADOMD.NET e o AMO (Objetos de Gerenciamento do Analysis Services) são bibliotecas de clientes gerenciadas. O Provedor Analysis Services OLE DB (MSOLAP DLL) é uma biblioteca de clientes nativa. Normalmente, as três bibliotecas são instaladas ao mesmo tempo. **O Azure Analysis Services exige as versões mais recentes das três bibliotecas**. 

Os aplicativos clientes da Microsoft, como o Power BI Desktop e o Excel, instalam todas as três bibliotecas de clientes e as atualizam quando novas versões estão disponíveis. Dependendo da versão ou da frequência de atualizações, algumas bibliotecas de clientes podem não ser as versões mais recentes exigidas pelo Azure Analysis Services. O mesmo se aplica a aplicativos personalizados ou outras interfaces, como AsCmd, TOM, ADOMD.NET. Esses aplicativos exigem a instalação manual ou programática das bibliotecas. As bibliotecas de cliente para instalação manual estão incluídas nos pacotes de recursos do SQL Server como pacotes distribuíveis. No entanto, essas bibliotecas de cliente estão vinculadas à versão do SQL Server e talvez não sejam as mais recentes.  

As bibliotecas de cliente para conexões de cliente são diferentes dos provedores de dados necessários para se conectar de um servidor Azure Analysis Services a uma fonte de dados. Para saber mais sobre conexões de origem de dados, consulte [conexões de origem de dados](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipos de biblioteca de clientes

### <a name="analysis-services-ole-db-provider-msolap"></a>Provedor OLE DB do Analysis Services (MSOLAP) 

 O MSOLAP (Provedor Analysis Services OLE DB) é a biblioteca de clientes nativa para conexões de banco de dados do Analysis Services. Ele é usado indiretamente pelo ADOMD.NET e pelo AMO, delegando solicitações de conexão ao provedor de dados. Você também pode chamar o provedor OLE DB diretamente do código do aplicativo.  
  
 O Provedor Analysis Services OLE DB é instalado automaticamente pela maioria das ferramentas e dos aplicativos clientes usados para acessar bancos de dados do Analysis Services. Ele deve ser instalado nos computadores usados para acessar dados do Analysis Services.  
  
 Os provedores OLE DB costumam ser especificados em cadeias de conexão. Uma cadeia de conexão do Analysis Services usa uma nomenclatura diferente para referenciar o provedor OLE DB: MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 O AMO é uma biblioteca de clientes gerenciada usada para administração de servidor e definição de dados. Ele é instalado e usado por aplicativos clientes e ferramentas. Por exemplo, o SSMS (SQL Server Management Studio) usa o AMO para se conectar ao Analysis Services. Uma conexão usando o AMO geralmente é mínima, consistindo em `"data source=\<servername>"`. Depois que uma conexão é estabelecida, você usa a API para trabalhar com coleções de banco de dados e grandes objetos. Tanto o Visual Studio quanto o SSMS usam amo para se conectar a uma instância de Serviços de Análise.  

  
### <a name="adomd"></a>ADOMD

 O ADOMD.NET é uma biblioteca de clientes de dados gerenciada usada para consultar dados do Analysis Services. Ele é instalado e usado por aplicativos clientes e ferramentas. 
  
 Ao se conectar a um banco de dados, as propriedades de cadeia de conexão das três bibliotecas são semelhantes. Quase todas as cadeias de conexão que você define para o ADOMD.NET usando [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) também funcionam para o AMO e o MSOLAP (Provedor Analysis Services OLE DB). Para saber mais, confira [Propriedades de cadeia de Conexão & #40;Analysis Services& #41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Como determinar a versão da biblioteca de clientes   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Ir para `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Se você tiver mais de uma pasta, escolha o número mais alto.
  
2.  Com o botão direito do mouse **msolap.dll** > **Properties** > **Details**. Se o nome do arquivo for msolap140.dll, é mais antigo que a versão mais recente e deverá ser atualizado.
    
    ![Detalhes da biblioteca de clientes](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Ir para `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Se você tiver mais de uma pasta, escolha o número mais alto.
2. Clique com o botão direito do mouse **microsoft.analysisServices** > **Properties** > **Details**.  

### <a name="adomd"></a>ADOMD

1. Ir para `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Se você tiver mais de uma pasta, escolha o número mais alto.
2. Clique com o botão direito do mouse **Microsoft.AnalysisServices.AdomdDetalhes** > **de propriedades do** > cliente **.**  


## <a name="next-steps"></a>Próximas etapas
[Conecte-se com o Excel](analysis-services-connect-excel.md)    
[Conectar com Power BI](analysis-services-connect-pbi.md)
