---
title: Entender o repositório de valor-chave de configuração Azure App
description: Entenda como os dados de configuração são armazenados na configuração do Azure App.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 14ff1a00b40d956f369b1978f15f01f113c50270
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050149"
---
# <a name="keys-and-values"></a>Chaves e valores

A configuração do Aplicativo Azure AD e armazena dados de configuração como pares chave-valor. Os pares chave-valor são uma representação simples e flexível das configurações do aplicativo usadas pelos desenvolvedores.

## <a name="keys"></a>Chaves

As chaves servem como identificadores para pares chave-valor e são usadas para armazenar e recuperar valores correspondentes. É uma prática comum organizar chaves em um namespace hierárquico usando um caractere delimitador, como `/` ou `:`. Use uma convenção mais adequada para seu aplicativo. A Configuração de Aplicativo trata as chaves como um todo. Ela não analisa as chaves para descobrir como seus nomes são estruturados nem impõem regras a elas.

Aqui estão dois exemplos de nomes de chave estruturados em uma hierarquia:

* Com base em serviços de componentes

```aspx
        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint
```

* Com base em regiões de implantação

```aspx
        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint
```

O uso de dados de configuração em estruturas de aplicativo pode ditar esquemas de nomenclatura específicos para valores de chave. Por exemplo, a estrutura Spring Cloud do Java define `Environment` recursos que fornecem configurações para um aplicativo Spring.  Eles são parametrizados por variáveis que incluem o *nome do aplicativo* e o *perfil*. As chaves dos dados de configuração relacionados ao Spring Cloud normalmente começarão com esses dois elementos, separadamente por um delimitador.

As chaves armazenadas na Configuração de Aplicativo são cadeias de caracteres baseadas em Unicode que diferenciam maiúsculas de minúsculas. As chaves *app1* e *App1* são distintas em um repositório de Configuração de Aplicativos. Tenha isso em mente ao usar definições de configuração em um aplicativo, pois algumas estruturas manipulam as chaves de configuração sem diferenciar maiúsculas de minúsculas. Não recomendamos o uso de maiúsculas e minúsculas para diferenciar as chaves.

Você pode usar qualquer caractere Unicode em nomes de chave, exceto para `*` , `,` e `\` .  Se você precisar incluir um desses caracteres reservados, use o escape para usá-lo `\{Reserved Character}` . 

Há um limite de tamanho combinado de 10 KB em um par chave-valor. Esse limite inclui todos os caracteres na chave, seu valor e todos os atributos opcionais associados. Dentro desse limite, você pode ter vários níveis hierárquicos para chaves.

### <a name="design-key-namespaces"></a>Criar namespaces de chave

Há duas abordagens gerais para nomear as chaves usadas para dados de configuração: simples ou hierárquica. Esses métodos são muito semelhantes do ponto de vista do uso de um aplicativo, mas a nomenclatura hierárquica oferece algumas vantagens:

* É mais fácil de ler. Os delimitadores em um nome de chave hierárquica funcionam como espaços em uma sentença. Também fornecem quebras naturais entre palavras.
* É mais fácil de gerenciar. Uma hierarquia de nomes de chave representa grupos lógicos de dados de configuração.
* É mais fácil de usar. É mais simples escrever uma consulta cujo padrão é correspondente às chaves em uma estrutura hierárquica e recupera apenas uma parte dos dados de configuração. Além disso, muitas estruturas de programação mais recentes têm suporte nativo para dados de configuração hierárquica, de tal modo que o aplicativo pode fazer uso de conjuntos de configuração específicos.

Você pode organizar as chaves na Configuração de Aplicativo hierarquicamente de muitas maneiras. Considere essas chaves como sendo [URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Cada chave hierárquica é um *caminho* de recurso formado por um ou mais componentes que são unidos por delimitadores. Escolha qual caractere usar como um delimitador de acordo com as necessidades do aplicativo, da linguagem de programação ou da estrutura. Use vários delimitadores para diferentes chaves na Configuração de Aplicativo.

### <a name="label-keys"></a>Chaves de rótulo

Valores de chave na Configuração de Aplicativo podem, opcionalmente, ter um atributo de rótulo. Os rótulos são usados para diferenciar os valores de chave com a mesma chave. Uma chave *app1* com rótulos *A* e *B* forma duas chaves separadas em um repositório de Configuração de Aplicativos. Por padrão, um valor de chave não tem nenhum rótulo. Para referenciar explicitamente um valor de chave sem um rótulo, use `\0` (URL codificado como `%00` ).

O rótulo fornece uma maneira conveniente de criar variantes de uma chave. Um uso comum dos rótulos é especificar vários ambientes para a mesma chave:

```aspx
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Valores de chave de versão

A configuração de aplicativo não tem valores de chave de versão automaticamente. Use rótulos como uma maneira de criar várias versões de um valor de chave. Por exemplo, você pode inserir um número de versão do aplicativo ou uma ID de confirmação do Git em rótulos para identificar valores de chave associados a um build de software específico.

Você pode usar qualquer caractere unicode em rótulos, exceto para `*`, `,`, e `\`. Esses caracteres são reservados. Para incluir um caractere reservado, você precisará fazer escape dele usando `\{Reserved Character}`.

### <a name="query-key-values"></a>Consultar valores de chave

Cada valor de chave é identificado exclusivamente pela sua chave, além de um rótulo que pode ser `null`. Consulte em um repositório de Configuração de Aplicativos para pares chave-valor especificando um padrão. O repositório de Configuração de Aplicativos retorna todos os pares chave-valor que correspondem ao padrão e seus valores e atributos correspondentes. Use os seguintes padrões de chave em chamadas à API REST para a Configuração de Aplicativo:

| Chave | Descrição |
|---|---|
| `key` é omitido ou `key=*` | Corresponde a todas as chaves |
| `key=abc` | Corresponde exatamente ao nome da chave **abc** |
| `key=abc*` | Corresponde aos nomes de chave que começam com **abc** |
| `key=abc,xyz` | Corresponde aos nomes de chave **ABC** ou **XYZ**. Limitado a cinco CSVs |

Inclua também os seguintes padrões de rótulo:

| Rótulo | Descrição |
|---|---|
| `label` é omitido ou `label=*` | Corresponde a qualquer rótulo, que inclui `null` |
| `label=%00` | Corresponde ao rótulo `null` |
| `label=1.0.0` | Corresponde exatamente ao rótulo **1.0.0** |
| `label=1.0.*` | Corresponde aos rótulos que começam com **1.0.** |
| `label=%00,1.0.0` | Corresponde aos rótulos `null` ou **1.0.0**, limitado a cinco CSVs |

## <a name="values"></a>Valores

Os valores atribuídos às chaves também são cadeias de caracteres Unicode. Use todos os caracteres Unicode para valores. Há um tipo de conteúdo definido pelo usuário opcional associado a cada valor. Use esse atributo para armazenar informações sobre um valor que ajuda seu aplicativo a processá-lo corretamente.

Os dados de configuração armazenados em um repositório de configuração de aplicativo são criptografados em repouso e em trânsito. As chaves não são criptografadas em repouso. A Configuração de Aplicativo não é uma solução substituta do Azure Key Vault. Não armazene segredos do aplicativo nele.

## <a name="next-steps"></a>Próximas etapas

* [Instantâneo pontual](./concept-point-time-snapshot.md)  
* [Gerenciamento de recursos](./concept-feature-management.md)  
