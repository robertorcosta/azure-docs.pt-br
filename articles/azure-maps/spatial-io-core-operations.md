---
title: Operações de Core IO | Mapas do Microsoft Azure
description: Aprenda a ler e escrever dados XML e delimitados de forma eficiente usando bibliotecas principais do módulo io espacial.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371450"
---
# <a name="core-io-operations"></a>Operações de Core IO

Além de fornecer ferramentas para ler arquivos de dados espaciais, o módulo espacial de IO expõe bibliotecas subjacentes ao núcleo para ler e gravar XML e delimitar dados de forma rápida e eficiente.

O `atlas.io.core` namespace contém duas classes de baixo nível que podem ler e gravar rapidamente dados CSV e XML. Essas classes base alimentam os leitores e escritores de dados espaciais no módulo IO espacial. Sinta-se livre para usá-los para adicionar suporte adicional de leitura e escrita para arquivos CSV ou XML.
 
## <a name="read-delimited-files"></a>Ler arquivos delimitados

A `atlas.io.core.CsvReader` classe lê strings que contêm conjuntos de dados delimitados. Esta classe fornece dois métodos para leitura de dados:

- A `read` função lerá o conjunto de dados completo e retornará uma matriz bidimensional de strings representando todas as células do conjunto de dados delimitado.
- A `getNextRow` função lê cada linha de texto em um conjunto de dados delimitado e retorna uma matriz de string representando todas as células nessa linha de dados. O usuário pode processar a linha e descartar qualquer memória desnecessária dessa linha antes de processar a próxima linha. Então, a função é mais eficiente na memória.

Por padrão, o leitor usará o caractere de comma como delimitador. No entanto, o delimitador pode ser `'auto'`alterado para qualquer caractere ou definido para . Quando definido `'auto'`para , o leitor analisará a primeira linha de texto na seqüência. Em seguida, ele selecionará o caractere mais comum da tabela abaixo para usar como delimitador.

| | |
| :-- | :-- |
| Vírgula | `,` |
| Tab | `\t` |
| Pipe | `|` |

Este leitor também suporta qualificadores de texto que são usados para lidar com células que contêm o caractere delimitador. O caractere`'"'`quote ( ) é o qualificador de texto padrão, mas pode ser alterado para qualquer caractere.

## <a name="write-delimited-files"></a>Escrever arquivos delimitados

O `atlas.io.core.CsvWriter` escreve uma matriz de objetos como uma seqüência delimitada. Qualquer caractere pode ser usado como um delimitador ou um qualificador de texto. O delimitador padrão é`','`comma ( ) e`'"'`o qualificador de texto padrão é o caractere quote ( ).

Para usar esta classe, siga as etapas abaixo:

- Crie uma instância da classe e defina opcionalmente um delimitador personalizado ou um qualificador de texto.
- Escreva dados para a `write` classe `writeRow` usando a função ou a função. Para `write` a função, passe uma matriz bidimensional de objetos representando várias linhas e células. Para usar `writeRow` a função, passe uma matriz de objetos representando uma linha de dados com várias colunas.
- Ligue `toString` para a função para recuperar a seqüência delimitada. 
- Opcionalmente, chame `clear` o método para tornar o escritor reutilizável `delete` e reduzir sua alocação de recursos, ou chamar o método para descartar a instância do escritor.

> [!Note]
> O número de colunas escritas será limitado ao número de células na primeira linha de dados passadas ao escritor.

## <a name="read-xml-files"></a>Leia arquivos XML

A `atlas.io.core.SimpleXmlReader` classe é mais rápida em `DOMParser`analisar arquivos XML do que . No entanto, a `atlas.io.core.SimpleXmlReader` classe exige que os arquivos XML sejam bem formatados. Arquivos XML que não estão bem formatados, por exemplo, faltando tags de fechamento, provavelmente resultarão em um erro.

O código a seguir `SimpleXmlReader` demonstra como usar a classe para analisar uma seqüência XML em um objeto JSON e serializá-la em um formato desejado.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Escrever arquivos XML

A `atlas.io.core.SimpleXmlWriter` classe escreve XML bem formatado de forma eficiente na memória.

O código a seguir `SimpleXmlWriter` demonstra como usar a classe para gerar uma seqüência XML bem formatado.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

O XML gerado a partir do código acima se pareceria com o seguinte.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)