---
title: Instalar pacotes no Jupyter notebooks – versão prévia do Azure Notebooks
description: 'Saiba como instalar pacotes python, R e F # de dentro de um notebook Jupyter em execução no Azure.'
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 43d38c9f3a4b0095967ab3e103ea729ec86ea2bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831209"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Instalar pacotes de dentro do Azure Notebooks versão prévia

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Embora você possa configurar o [ambiente para o notebook no nível do projeto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), talvez você queira instalar pacotes diretamente dentro de um notebook individual.

Os pacotes instalados do notebook aplicam-se apenas à sessão atual do servidor. As instalações de pacote não persistem quando o servidor é desligado.

## <a name="python"></a>Python

Os pacotes em Python podem ser instalados usando o pip ou o conda com comandos dentro de células de código:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Se a saída do comando indicar que o requisito já for atendido, os Azure Notebooks poderão incluir o pacote por padrão. O pacote também pode ser instalado por meio de uma [etapa de configuração do ambiente do projeto](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Os pacotes em R podem ser instalados do CRAN ou do GitHub usando a função `install.packages` em uma célula de código:

```r
install.packages("package_name")
```

Você também pode instalar versões de pré-lançamento e outros pacotes de desenvolvimento do GitHub usando a biblioteca devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Os pacotes em F# podem ser instalados do [nuget.org](https://www.nuget.org) chamando o gerenciador de dependências Paket de dentro das células de código. Primeiro, carregue o gerenciador Paket:

```fsharp
#load "Paket.fsx"
```

Em seguida, instale os pacotes:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Em seguida, carregue o gerador de paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Abra a biblioteca:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Próximas etapas

- [Como: configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Como: apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
