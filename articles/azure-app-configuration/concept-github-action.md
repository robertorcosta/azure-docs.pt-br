---
title: Usar ações do GitHub com a sincronização de configuração do Azure App
description: Usar ações do GitHub para disparar uma atualização para sua instância de configuração de aplicativo quando as ações definidas forem executadas em um repositório GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523706"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Sincronizar a instância da Configuração de Aplicativos usando o GitHub Actions
Azure App configuração usa ações do GitHub para disparar atualizações para uma instância de configuração de aplicativo com base em ações executadas em um repositório GitHub. Os fluxos de trabalho do GitHub disparam atualizações de configuração, permitindo a integração dessas atualizações no mesmo fluxo de trabalho usado para atualizar o código do aplicativo.

Um fluxo de [trabalho](https://help.github.com/articles/about-github-actions#workflow) de ações do GitHub define um processo automatizado em um repositório github. Esse processo informa ao GitHub como criar e implantar seu projeto do GitHub. Azure App configuração fornece a ação de *sincronização de configuração de Azure app* para habilitar atualizações em uma instância de configuração de aplicativo quando são feitas alterações no repositório de origem. 

Um arquivo YAML (. yml) encontrado no caminho `/.github/workflows/` do repositório define seu fluxo de trabalho. Essa definição contém as etapas e os parâmetros do fluxo de trabalho.

Os eventos do GitHub, como um envio por push para um repositório, podem disparar um fluxo de trabalho de ação do GitHub.  A ação de *sincronização de configuração de Azure app* permite disparar uma atualização de uma instância de configuração de aplicativo quando ocorre uma ação do GitHub especificada. Você pode disparar atualizações de configuração ao enviar, revisar ou ramificar arquivos de configuração de aplicativo exatamente como faz com o código do aplicativo.

A [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) do GitHub fornece uma visão detalhada dos fluxos de trabalho e ações do github. 

## <a name="enable-github-actions-in-your-repository"></a>Habilitar ações do GitHub em seu repositório
Para começar a usar essa ação do GitHub, vá para o repositório e selecione a guia **ações** . clique em **novo fluxo de trabalho**e, em seguida, **Configure um fluxo de trabalho por conta própria**. Por fim, pesquise "Azure App sincronização de configuração" no Marketplace.
> [!div class="mx-imgBorder"]
> ![selecione a guia ação](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![selecionar a ação de sincronização de configuração de aplicativo](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizar arquivos de configuração após um envio por push
Essa ação sincroniza Azure App arquivos de configuração quando uma alteração é enviada por push para `appsettings.json`. Quando um desenvolvedor envia uma alteração para `appsettings.json`, a ação de sincronização de configuração de aplicativo atualiza a instância de configuração de aplicativo com os novos valores.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada *em* um *Push* contendo `appsettings.json` para a Branch *mestre* . A segunda seção lista os trabalhos executados quando a ação é disparada. A ação verifica os arquivos relevantes e atualiza a instância de configuração do aplicativo usando a cadeia de conexão armazenada como um segredo no repositório.  Para obter mais informações sobre como usar segredos no GitHub, consulte o [artigo do GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sobre como criar e usar segredos criptografados.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Usar um rótulo dinâmico na sincronização
A ação anterior atualiza a instância de configuração do aplicativo sempre que `appsettings.json` é atualizada. Essa ação insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronização possa ser identificada exclusivamente e permitindo que as alterações de código sejam mapeadas para as alterações de configuração.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada *em* um *Push* contendo `appsettings.json` para a Branch *mestre* . A segunda seção executa um trabalho que cria um rótulo exclusivo para a atualização de configuração com base no hash de confirmação. Em seguida, o trabalho atualiza a instância de configuração do aplicativo com os novos valores e o rótulo exclusivo para essa atualização.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Usar a sincronização estrita
Quando o modo estrito está habilitado, a sincronização garante que a instância de configuração do aplicativo corresponda ao arquivo de configuração para o prefixo e rótulo fornecidos exatamente. Pares de chave-valor com o mesmo prefixo e rótulo que não estão no arquivo de configuração são excluídos. 
 
Se o modo estrito não estiver habilitado, a sincronização definirá somente valores de chave do arquivo de configuração. Nenhum par chave-valor será excluído. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Usar a profundidade máxima para limitar a ação do GitHub
O comportamento padrão para atributos JSON aninhados é mesclar o objeto inteiro.  O JSON abaixo define esse par de chave-valor:

| Chave | Valor |
| --- | --- |
| Objeto: interno: InnerKey | InnerException |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Se o objeto aninhado for destinado ao valor enviado por push para a instância de configuração, você poderá usar o valor de *profundidade* para interromper o nivelamento na profundidade apropriada. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Dada uma profundidade de 2, o exemplo acima agora retorna o seguinte par chave-valor:

| Chave | Valor |
| --- | --- |
| Objeto: interno | {"InnerKey": "InnerException"} |

## <a name="understand-action-inputs"></a>Entender as entradas da ação
Os parâmetros de entrada especificam os dados usados pela ação durante o tempo de execução.  A tabela a seguir contém parâmetros de entrada aceitos pela sincronização de configuração de aplicativo e os valores esperados para cada um.  Para obter mais informações sobre entradas de ação para ações do GitHub, consulte a [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)do github.

> [!Note]
> As IDs de entrada não diferenciam maiúsculas de minúsculas.


| Nome de entrada | Obrigatório? | Valor |
|----|----|----|
| configurationFile | Sim | Caminho relativo para o arquivo de configuração no repositório.  Os padrões glob têm suporte e podem incluir vários arquivos. |
| format | Sim | Formato de arquivo do arquivo de configuração.  Os formatos válidos são: JSON, YAML, Properties. |
| connectionString | Sim | Cadeia de conexão para a instância de configuração do aplicativo. A cadeia de conexão deve ser armazenada como um segredo no repositório do GitHub e somente o nome do segredo deve ser usado no fluxo de trabalho. |
| separator | Sim | Separador usado ao mesclar o arquivo de configuração para pares de chave-valor.  Os valores válidos são:. , ; : - _ __ / |
| prefixo | Não | Prefixo a ser adicionado ao início das chaves. |
| label | Não | Rótulo usado ao definir pares de chave-valor. Se não for especificado, será usado um rótulo nulo. |
| Strict | Não | Um valor booliano que determina se o modo estrito está habilitado. O valor padrão é false. |
| Detalhado | Não | Profundidade máxima para mesclar o arquivo de configuração.  A profundidade deve ser um número positivo.  O padrão não terá nenhuma profundidade máxima. |
| marcas | Não | Especifica a marca definida em pares chave-valor.  O formato esperado é uma forma em cadeias de um objeto JSON da seguinte forma: {[propertyName: String]: String;} Cada nome de propriedade-valor torna-se uma marca. |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a ação de sincronização de configuração de aplicativo do GitHub e como ela pode ser usada para automatizar atualizações para sua instância de configuração de aplicativo. Para saber como Azure App configuração reage às alterações em pares chave-valor, vá para o próximo [artigo](./concept-app-configuration-event.md).
