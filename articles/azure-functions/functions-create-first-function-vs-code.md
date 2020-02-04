---
title: Criar sua primeira função no Azure usando o Visual Studio Code
description: Crie e publique uma função disparada por HTTP simples no Azure usando a extensão Azure Functions no Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842079"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Início Rápido: Criar um projeto do Azure Functions usando o Visual Studio Code

Neste artigo, você usará o Visual Studio Code para criar uma função que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions. A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure. 

Também há uma [versão baseada na CLI](functions-create-first-azure-function-azure-cli.md) deste artigo.

## <a name="prerequisites"></a>Prerequisites

+ [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ A [Extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) para Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) ou [Python 3.6](https://www.python.org/downloads/release/python-368/), que são compatíveis com o Azure Functions. Ainda não há suporte ao Python 3.8. 

+ A [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ O [SDK do .NET Core 2.2+](https://www.microsoft.com/net/download)

+ A [Extensão do PowerShell para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ A [Extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. 

+ Uma [conta do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) com uma assinatura ativa. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-an-azure-functions-project"></a>Criar seu projeto local 

Nesta seção, você usará o Visual Studio Code para criar um projeto local do Azure Functions em sua linguagem escolhida. Mais adiante neste artigo, você publicará o código de função no Azure. 

1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions** e selecione o ícone **Criar projeto...** .

    ![Escolher Criar um projeto](media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o workspace do projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estas etapas foram projetadas para serem concluídas fora de um workspace. Nesse caso, não selecione uma pasta de projeto que faz parte de um workspace.

1. Forneça as seguintes informações nos prompts:

    ::: zone pivot="programming-language-csharp"

    | Prompt | Valor | 
    | ------ | ----- | 
    | Selecione uma linguagem de programação para o seu projeto de função | C# |
    | Selecione uma versão | Azure Functions v2 | 
    | Selecione um modelo para a primeira função do projeto | Gatilho HTTP | 
    | Forneça um nome de função | HttpExample | 
    | Forneça um namespace | My.Functions | 
    | Nível de autorização | Anônima | 
    | Selecione como você gostaria de abrir seu projeto | Adicionar ao workspace |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Prompt | Valor | 
    | ------ | ----- | 
    | Selecione uma linguagem de programação para o seu projeto de função | JavaScript | 
    | Selecione uma versão | Azure Functions v2 | 
    | Selecione um modelo para a primeira função do projeto | Gatilho HTTP | 
    | Forneça um nome de função | HttpExample | 
    | Nível de autorização | Anônima | 
    | Selecione como você gostaria de abrir seu projeto | Adicionar ao workspace |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Prompt | Valor | 
    | ------ | ----- | 
    | Selecione uma linguagem de programação para o seu projeto de função | TypeScript | 
    | Selecione uma versão | Azure Functions v2 | 
    | Selecione um modelo para a primeira função do projeto | Gatilho HTTP | 
    | Forneça um nome de função | HttpExample | 
    | Nível de autorização | Anônima | 
    | Selecione como você gostaria de abrir seu projeto | Adicionar ao workspace |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Prompt | Valor | 
    | ------ | ----- | 
    | Selecione uma linguagem de programação para o seu projeto de função | PowerShell | 
    | Selecione uma versão | Azure Functions v2 | 
    | Selecione um modelo para a primeira função do projeto | Gatilho HTTP | 
    | Forneça um nome de função | HttpExample | 
    | Nível de autorização | Anônima | 
    | Selecione como você gostaria de abrir seu projeto | Adicionar ao workspace |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Prompt | Valor | 
    | ------ | ----- | 
    | Selecione uma linguagem de programação para o seu projeto de função | Python | 
    | Selecione uma versão | Azure Functions v2 | 
    | Selecione um alias do Python para criar um ambiente virtual | Alias do Python | 
    | Selecione um modelo para a primeira função do projeto | Gatilho HTTP | 
    | Forneça um nome de função | HttpExample | 
    | Nível de autorização | Anônima | 
    | Selecione como você gostaria de abrir seu projeto | Adicionar ao workspace | 

    ::: zone-end

1. O Visual Studio Code faz o seguinte:

    + Cria um projeto do Azure Functions em um novo workspace, que contém os arquivos de configuração [host.json](functions-host-json.md) e [local.settings.json](functions-run-local.md#local-settings-file). 

    ::: zone pivot="programming-language-csharp"

    + Cria um [arquivo de biblioteca de classes HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) que implementa a função.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Cria um arquivo package.json na pasta raiz.

    + Cria uma pasta HttpExample que contém o [arquivo de definição function.json](functions-reference-node.md#folder-structure) e o [arquivo index.js](functions-reference-node.md#exporting-a-function), um arquivo Node.js que contém o código de função.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Cria um arquivo package.json e um arquivo tsconfig.json na pasta raiz.

    + Cria uma pasta HttpExample que contém o [arquivo de definição function.json](functions-reference-node.md#folder-structure) e o [arquivo index.js](functions-reference-node.md#typescript), um arquivo TypeScript que contém o código de função.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Cria uma pasta HttpExample que contém o [arquivo de definição function.json](functions-reference-python.md#programming-model) e o arquivo run.ps1, que contém o código de função.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Cria um arquivo requirements.txt de nível de projeto que lista os pacotes exigidos pelo Functions.
    
    + Cria uma pasta HttpExample que contém o [arquivo de definição function.json](functions-reference-python.md#programming-model) e o arquivo \_\_init\_\_.py, que contém o código de função.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Após verificar se a função foi executada corretamente no computador local, é hora de usar o Visual Studio Code para publicar o projeto diretamente no Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Executar a função no Azure

1. Copie a URL do gatilho de HTTP do painel **Saída**. Novamente, adicione a cadeia de caracteres de consulta `name` como `?name=<yourname>` ao final dessa URL e execute a solicitação.

    A URL que chama a função HTTP disparada deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. O seguinte exemplo mostra a resposta no navegador à solicitação GET remota retornada pela função: 

    ![Resposta da função no navegador](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você passar para a próxima etapa, [Adicionar uma associação de fila do Armazenamento do Azure à sua função](functions-add-output-binding-storage-queue-vs-code.md), você precisará manter todos os seus recursos em vigor para se basear no que você já fez.

Caso contrário, você poderá usar as etapas a seguir para excluir o aplicativo de funções e recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Para saber mais sobre os custos do Functions, confira [Estimar os custos do plano de consumo](functions-consumption-costs.md).

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio Code para criar um aplicativo de funções com uma função disparada por HTTP simples. No próximo artigo, você pode expandir essa função adicionando uma associação de saída. Essa associação grava a cadeia de caracteres da solicitação HTTP em uma mensagem em uma fila do Armazenamento de Filas do Azure. 

> [!div class="nextstepaction"]
> [Adicionar uma associação de fila do Armazenamento do Azure à sua função](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
