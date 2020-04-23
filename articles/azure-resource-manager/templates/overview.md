---
title: Visão geral de modelos
description: Descreve os benefícios usando modelos do Azure Resource Manager para implantação de recursos.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086327"
---
# <a name="what-are-arm-templates"></a>O que são modelos ARM?

Com a mudança para a nuvem, muitas equipes adotaram métodos de desenvolvimento ágeis. Essas equipes iteram rapidamente. Eles precisam implantar repetidamente suas soluções na nuvem, e saber que sua infra-estrutura está em um estado confiável. Como a infra-estrutura se tornou parte do processo iterativo, a divisão entre operações e desenvolvimento desapareceu. As equipes precisam gerenciar a infra-estrutura e o código de aplicativos através de um processo unificado.

Para enfrentar esses desafios, você pode automatizar implantações e usar a prática da infra-estrutura como código. Em código, você define a infra-estrutura que precisa ser implantada. O código de infra-estrutura passa a fazer parte do seu projeto. Assim como o código do aplicativo, você armazena o código de infra-estrutura em um repositório de origem e o versão. Qualquer um da sua equipe pode executar o código e implantar ambientes semelhantes.

Para implementar a infra-estrutura como código para suas soluções Do Azure, use modelos ARM (Azure Resource Manager). O modelo é um arquivo JSON (JavaScript Object Notation, notação de objeto sustal) que define a infra-estrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite declarar o que você pretende implantar sem precisar gravar a sequência de comandos de programação para criá-lo. No modelo, você especifica os recursos a serem implantados e as propriedades desses recursos.

## <a name="why-choose-arm-templates"></a>Por que escolher modelos ARM?

Se você está tentando decidir entre usar modelos ARM e uma das outras infra-estruturas como serviços de código, considere as seguintes vantagens de usar modelos:

* **Sintaxe declarativa**: os modelos ARM permitem que você crie e implante toda uma infra-estrutura Azure declarativamente. Por exemplo, você pode implantar não apenas máquinas virtuais, mas também a infra-estrutura de rede, sistemas de armazenamento e quaisquer outros recursos que você possa precisar.

* **Resultados repetíveis**: Implante repetidamente sua infra-estrutura durante todo o ciclo de vida do desenvolvimento e tenha confiança de que seus recursos são implantados de forma consistente. Os modelos são impotentes, o que significa que você pode implantar o mesmo modelo muitas vezes e obter os mesmos tipos de recursos no mesmo estado. Você pode desenvolver um modelo que represente o estado desejado, em vez de desenvolver muitos modelos separados para representar atualizações.

* **Orquestração**: Você não precisa se preocupar com as complexidades das operações de ordenação. O Gerenciador de Recursos orquestra a implantação de recursos interdependentes para que sejam criados na ordem correta. Quando possível, o Resource Manager implanta recursos em paralelo para que suas implantações terminem mais rápido do que implantações seriais. Você implanta o modelo através de um comando, em vez de através de vários comandos imperativos.

   ![Comparação de implantação de modelos](./media/overview/template-processing.png)

* **Arquivos modulares**: Você pode quebrar seus modelos em componentes menores e reutilizáveis e vinculá-los no momento da implantação. Você também pode aninhar um modelo dentro de outros modelos.

* **Crie qualquer recurso do Azure**: Você pode usar imediatamente novos serviços e recursos do Azure em modelos. Assim que um provedor de recursos introduz novos recursos, você pode implantar esses recursos através de modelos. Você não precisa esperar que ferramentas ou módulos sejam atualizados antes de usar os novos serviços.

* **Extensibilidade**: Com [scripts de implantação,](deployment-script-template.md)você pode adicionar scripts PowerShell ou Bash aos seus modelos. Os scripts de implantação ampliam sua capacidade de configurar recursos durante a implantação. Um script pode ser incluído no modelo, ou armazenado em uma fonte externa e referenciado no modelo. Os scripts de implantação dão a você a capacidade de concluir sua configuração de ambiente de ponta a ponta em um único modelo ARM.

* **Teste**: Você pode certificar-se de que seu modelo segue as diretrizes recomendadas testando-o com o kit de ferramentas de modelo ARM (arm-ttk). Este kit de teste é um script PowerShell que você pode baixar do [GitHub](https://github.com/Azure/arm-ttk). O kit de ferramentas facilita o desenvolvimento de conhecimentos usando a linguagem do modelo.

* **Visualizar alterações**: Você pode usar a [operação e se](template-deploy-what-if.md) para obter uma visualização das alterações antes de implantar o modelo. Com o e-if, você vê quais recursos serão criados, atualizados ou excluídos, e quaisquer propriedades de recursos que serão alteradas. A operação e se verifica o estado atual do seu ambiente e elimina a necessidade de gerenciar o estado.

* **Validação incorporada**: Seu modelo é implantado somente após passar na validação. O Gerenciador de recursos verifica o modelo antes de iniciar a implantação para garantir que a implantação será bem sucedida. Sua implantação é menos provável de parar em um estado semi-acabado.

* **Implantações rastreadas**: No portal Azure, você pode rever o histórico de implantações e obter informações sobre a implantação do modelo. Você pode ver o modelo que foi implantado, os valores de parâmetro passados e quaisquer valores de saída. Outras infra-estruturas como serviços de código não são rastreadas através do portal.

   ![Histórico de implantações](./media/overview/deployment-history.png)

* **Política como código**: [A Política Do Azure](../../governance/policy/overview.md) é uma política como estrutura de código para automatizar a governança. Se você estiver usando as políticas do Azure, a remediação de políticas é feita em recursos não compatíveis quando implantada através de modelos.

* **Plantas de implantação**: Você pode aproveitar [os projetos fornecidos](../../governance/blueprints/overview.md) pela Microsoft para atender aos padrões normatéticos e de conformidade. Esses projetos incluem modelos pré-construídos para várias arquiteturas.

* **Integração CI/CD**: Você pode integrar modelos em suas ferramentas de integração contínua e implantação contínua (CI/CD), que podem automatizar seus pipelines de liberação para atualizações rápidas e confiáveis de aplicativos e infra-estrutura. Ao usar a tarefa de modelo Do Azure DevOps e Do Gerenciador de Recursos, você pode usar o Azure Pipelines para construir e implantar continuamente projetos de modelo saqueadores do ARM. Para saber mais, consulte [o projeto VS com pipelines](add-template-to-azure-pipelines.md) e [Tutorial: Integração contínua dos modelos do Azure Resource Manager com o Azure Pipelines](./deployment-tutorial-pipeline.md).

* **Código exportável**: Você pode obter um modelo para um grupo de recursos existente exportando o estado atual do grupo de recursos ou visualizando o modelo usado para uma determinada implantação. A exibição do [modelo exportado](export-template-portal.md) é uma maneira útil de saber mais sobre a sintaxe do modelo.

* **Ferramentas de autoria**: Você pode escrever modelos com [Visual Studio Code](use-vs-code-to-create-template.md) e a extensão da ferramenta de modelo. Você tem intellisense, destaque de sintaxe, ajuda em linha e muitas outras funções linguísticas. Além do código visual studio, você também pode usar [o Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>Arquivo de modelo

Dentro do seu modelo, você pode escrever [expressões de modelo](template-expressions.md) que ampliam os recursos do JSON. Essas expressões fazem uso das [funções fornecidas](template-functions.md) pelo Resource Manager.

O modelo tem as seguintes seções:

* [Parâmetros](template-parameters.md) - Fornecer valores durante a implantação que permitem que o mesmo modelo seja usado em diferentes ambientes.

* [Variáveis](template-variables.md) - Defina valores que são reutilizados em seus modelos. Eles podem ser construídos a partir de valores de parâmetros.

* [Funções definidas pelo usuário](template-user-defined-functions.md) - Crie funções personalizadas que simplifiquem seu modelo.

* [Recursos](template-syntax.md#resources) - Especifique os recursos a serem implantados.

* [Saídas](template-outputs.md) - Valores de retorno dos recursos implantados.

## <a name="template-deployment-process"></a>Processo de implantação de modelos

Quando você implanta um modelo, o Resource Manager converte o modelo em operações de API REST. Por exemplo, quando o Gerenciador de Recursos recebe um modelo com a seguinte definição de recurso:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Converte a definição para a seguinte operação de API REST, que é enviada para o provedor de recursos Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Design do modelo

Como você define grupos de recursos e modelos é de sua responsabilidade e de como você deseja gerenciar a sua solução. Por exemplo, você pode implantar seu aplicativo de três camadas por meio de um único modelo para um único grupo de recursos.

![modelo de três camadas](./media/overview/3-tier-template.png)

Mas, você não precisa definir toda a sua infraestrutura em um único modelo. Muitas vezes, faz sentido dividir seus requisitos de implantação em um conjunto de modelos com destinação e fins específicos. Você pode reutilizar esses modelos facilmente para soluções diferentes. Para implantar uma solução específica, você deve criar um modelo mestre que vincule todos os modelos necessários. A imagem a seguir mostra como implantar uma solução de três camadas com o modelo pai que inclui três modelos aninhados.

![modelo de camadas aninhadas](./media/overview/nested-tiers-template.png)

Ao prever suas camadas com ciclos de vida separados, você pode implantar os três níveis para separar grupos de recursos. Observe que os recursos ainda podem ser vinculados aos recursos em outros grupos de recursos.

![modelo de camada](./media/overview/tier-templates.png)

Para obter informações sobre modelos aninhados, confira [Usando modelos vinculados com o Azure Resource Manager](linked-templates.md).

## <a name="next-steps"></a>Próximas etapas

* Para um tutorial passo-a-passo que orienta o processo de criação de um modelo, consulte [Tutorial: Crie e implante seu primeiro modelo ARM](template-tutorial-create-first-template.md).
* Para obter informações sobre as propriedades nos arquivos de modelo, consulte [Entender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para saber mais sobre a exportação de modelos, consulte [Quickstart: Crie e implante modelos ARM usando o portal Azure](quickstart-create-templates-use-the-portal.md).
