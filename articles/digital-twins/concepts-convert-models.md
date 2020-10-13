---
title: Convertendo modelos padrão do setor
titleSuffix: Azure Digital Twins
description: Entenda o padrão para converter modelos de padrão do setor (RDF/OWL) para DTDL
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 76d1fd91053216103ef6ace0e56979c57eca569f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002620"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Converter modelos padrão do setor em DTDL para o gêmeos digital do Azure

Os modelos no Azure digital gêmeos são representados na [**DTDL (digital gêmeos Definition Language)**](concepts-models.md)baseada em JSON-ld. Se você tiver modelos existentes fora do Azure digital gêmeos que se baseiam em um padrão do setor, como RDF ou OWL, você precisará **convertê-los em DTDL** para usá-los com o gêmeos digital do Azure. A versão do DTDL se tornará a fonte de verdade para o modelo no gêmeos digital do Azure.

Este artigo descreve um padrão para converter modelos do setor baseados em RDF ou personalizados em DTDL. Ele inclui:
* **Diretrizes de nível estratégico** que podem ser aplicadas a uma variedade de padrões e tipos de modelo
* [**Código de exemplo** para um conversor específico do RDF](#sample-converter-application)

## <a name="industry-models"></a>Modelos do setor  

Usar modelos do setor fornece um ponto de partida rico ao projetar seu modelo de gêmeos digital do Azure. O uso de modelos do setor também ajuda na padronização e no compartilhamento de informações. 

Alguns modelos comuns do setor incluem:  

| Vertical do setor | Modelar |
| --- | --- | 
| Criação/gerenciamento de instalações | [RealEstateCore](https://www.realestatecore.io/)<br>[Esquema de BRICK](https://brickschema.org/ontology/1.1/)<br>[Criando ontologia de topologia (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Rede do sensor semântico](https://www.w3.org/TR/vocab-ssn/)<br>[IFC (buildingSmart Industry Foundation classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Cidades inteligentes | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Referência de aplicativos inteligentes (SAREF)](https://saref.etsi.org/) |
| Grade de energia | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968) | 

Dependendo de suas necessidades, você também pode usar o DTDL para personalizar ou estender modelos do setor ou desenvolver seu próprio modelo personalizado do zero. 

## <a name="create-and-edit-models"></a>Criar e editar modelos

A primeira etapa na modelagem é criar seus modelos. Você pode criar e concluir a edição de seus modelos padrão do setor antes de convertê-los em DTDL ou pode convertê-los no DTDL cedo e continuar editando-os como documentos DTDL.

### <a name="with-industry-standards"></a>Com os padrões do setor

A maioria dos modelos do setor (também conhecidos como **ontologies**) baseia-se em padrões da Web semânticos, como [Owl](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Em alguns casos, talvez você queira criar ou editar um modelo usando ferramentas de modelo baseadas em OWL. Você pode usar qualquer número de ferramentas de criação de modelo para criar um modelo baseado em OWL, incluindo o seguinte.
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) e [protégé desktop](https://protege.stanford.edu/products.php#desktop-protege) são exemplos populares. Você pode importar modelos do setor em vários formatos, editar ou estender um modelo e exportar o modelo. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) é outra ferramenta popular para visualizar modelos. 

Se você criar um modelo usando algum padrão do setor que não seja DTDL, será necessário convertê-lo em DTDL e carregá-lo no Azure digital gêmeos. 

#### <a name="common-model-file-formats"></a>Formatos de arquivo de modelo comuns 

RDF, OWL e RDFS são os blocos de construção básicos da Web semântica. 

O **RDF** fornece uma estrutura conceitual para descrever as coisas, na forma de **processamentos**. As corridas consistem em três partes: **assunto**, **predicado**e **objeto**. Os objetos podem ser compostos por URIs. 

Aqui estão alguns exemplos de corridas de RDF:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Esses exemplos são todos os RDF válidos, mas a última instrução é semanticamente inválida. Essa situação é onde a especificação OWL entra na imagem. **Owl** define o que você pode escrever com o RDF para ter um ontologia válido.

Veja um exemplo que utiliza OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

O **RDFS** fornece semântica adicional de vocabulário que ajuda você a definir e descrever classes. Por exemplo, uma dessas classes é `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

Os modelos podem ser salvos, importados e exportados em vários formatos de arquivo, incluindo:  
* RDF/XML 
* Tartaruga (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>Com DTDL

O Azure digital gêmeos usa a **DTDL (linguagem de definição de entrelaça digital)** baseada em JSON-LD para modelagem. Qualquer modelo que será usado com o gêmeos digital do Azure precisará ser originalmente gravado ou convertido em DTDL.

Ao trabalhar com modelos no DTDL, você pode usar a [**extensão DTDL**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   disponível para [Visual Studio Code](https://code.visualstudio.com/), que fornece validação de sintaxe e outros recursos para facilitar a criação de modelos de DTDL.

Você pode ler mais sobre os modelos de gêmeos digitais do Azure e seus componentes em [*conceitos: modelos personalizados*](concepts-models.md) e [*instruções: gerenciar modelos personalizados*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Converter modelos em DTDL

Para usar um modelo com o gêmeos digital do Azure, ele deve estar no formato DTDL. Esta seção aborda como converter modelos baseados em RDF em DTDL para que eles possam ser usados com o gêmeos digital do Azure.

Há várias bibliotecas de terceiros que podem ser usadas ao converter modelos baseados em RDF em DTDL. Algumas dessas bibliotecas permitem que você carregue seu arquivo de modelo em um grafo. Você pode executar um loop no grafo procurando construções RDFS e OWL específicas e convertê-las em DTDL.   

A tabela a seguir é um exemplo de como as construções RDFS e OWL podem ser mapeadas para DTDL. 

| Conceito de RDFS/OWL | Construção RDFS/OWL | Conceito de DTDL | Construção DTDL |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>Sufixo IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subclasses | `owl:Class`<br>Sufixo IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Propriedades de DataType | `owl:DatatypeProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:label`<br>`rdfs:range` | Propriedades de interface | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Propriedades de objeto | `owl:ObjectProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (ou omitido se não `rdfs:range` )<br>`comment`<br>`displayName`<br>

O trecho de código C# a seguir mostra como um arquivo de modelo RDF é carregado em um grafo e convertido em DTDL, usando a biblioteca [**dotNetRDF**](https://www.dotnetrdf.org/) . 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>Validar e carregar modelos DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Depois que um modelo é convertido e validado, você pode **carregá-lo em sua instância de gêmeos digital do Azure**. Para obter mais informações sobre esse processo, consulte a seção [*carregar modelos*](how-to-manage-model.md#upload-models) de *como: gerenciar modelos personalizados*.

## <a name="sample-converter-application"></a>Aplicativo de conversor de exemplo 

Há um aplicativo de exemplo disponível que converte um arquivo de modelo baseado em RDF para [DTDL (versão 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para uso pelo serviço gêmeos do Azure digital. O exemplo é um aplicativo de linha de comando do .NET Core chamado **RdfToDtdlConverter**.

Você pode obter o exemplo aqui: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Para baixar o código em seu computador, clique no botão *baixar zip* abaixo do título na página de aterrissagem de exemplo. Isso fará o download de um arquivo *zip* sob o nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, que você pode descompactar e explorar.

Você pode usar este exemplo para ver os padrões de conversão no contexto e para ter como um bloco de construção para seus próprios aplicativos executando conversões de modelo de acordo com suas necessidades específicas.

## <a name="next-steps"></a>Próximas etapas 

Leia mais sobre como projetar e gerenciar modelos de entrelaçamento digital:
 
* [*Conceitos: modelos personalizados*](concepts-models.md)
* [*Como gerenciar modelos personalizados*](how-to-manage-model.md)
* [*Como: analisar e validar modelos*](how-to-parse-models.md)