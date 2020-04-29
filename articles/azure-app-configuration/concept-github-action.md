---
title: Sincronizar o repositório GitHub com a configuração do aplicativo
description: Use as ações do GitHub para atualizar automaticamente sua instância de configuração de aplicativo quando você atualizar o repositório GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585491"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Sincronizar o repositório GitHub com a configuração do aplicativo

As equipes que desejam continuar usando suas práticas de controle do código-fonte existentes podem usar ações do GitHub para sincronizar automaticamente seu repositório GitHub com o repositório de configuração do aplicativo. Isso permite que você faça alterações nos arquivos de configuração como faria normalmente, ao mesmo tempo que obtém os benefícios da configuração do aplicativo, como: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configuração centralizada fora do seu código <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Atualizando a configuração sem reimplantar seu aplicativo inteiro <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integração com serviços como Azure App serviço e funções. 

Um fluxo de [trabalho](https://help.github.com/articles/about-github-actions#workflow) de ações do GitHub define um processo automatizado em um repositório github. A ação de *sincronização de configuração de Azure app* dispara atualizações para uma instância de configuração de aplicativo quando são feitas alterações no repositório de origem. Ele usa um arquivo YAML (. yml) encontrado no `/.github/workflows/` caminho do seu repositório para definir as etapas e os parâmetros. Você pode disparar atualizações de configuração ao enviar, revisar ou ramificar arquivos de configuração de aplicativo exatamente como faz com o código do aplicativo.

A [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) do GitHub fornece uma visão detalhada dos fluxos de trabalho e ações do github. 

## <a name="enable-github-actions-in-your-repository"></a>Habilitar ações do GitHub em seu repositório
Para começar a usar essa ação do GitHub, vá para o repositório e selecione a guia **ações** . clique em **novo fluxo de trabalho**e, em seguida, **Configure um fluxo de trabalho por conta própria**. Por fim, pesquise "Azure App sincronização de configuração" no Marketplace.
> [!div class="mx-imgBorder"]
> ![Selecione a guia ação](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecionar a ação de sincronização de configuração de aplicativo](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizar arquivos de configuração após um envio por push
Essa ação sincroniza Azure App arquivos de configuração quando uma alteração é enviada para `appsettings.json`o. Quando um desenvolvedor envia uma alteração para `appsettings.json`, a ação de sincronização de configuração de aplicativo atualiza a instância de configuração de aplicativo com os novos valores.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada `appsettings.json` *em* um *Push* que contém o Branch *mestre* . A segunda seção lista os trabalhos executados quando a ação é disparada. A ação verifica os arquivos relevantes e atualiza a instância de configuração do aplicativo usando a cadeia de conexão armazenada como um segredo no repositório.  Para obter mais informações sobre como usar segredos no GitHub, consulte o [artigo do GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sobre como criar e usar segredos criptografados.

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
A ação anterior atualiza a instância de configuração do `appsettings.json` aplicativo sempre que o é atualizado. Essa ação insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronização possa ser identificada exclusivamente e permitindo que as alterações de código sejam mapeadas para as alterações de configuração.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada `appsettings.json` *em* um *Push* que contém o Branch *mestre* . A segunda seção executa um trabalho que cria um rótulo exclusivo para a atualização de configuração com base no hash de confirmação. Em seguida, o trabalho atualiza a instância de configuração do aplicativo com os novos valores e o rótulo exclusivo para essa atualização.

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


| Nome de entrada | Necessário? | Valor |
|----|----|----|
| configurationFile | Sim | Caminho relativo para o arquivo de configuração no repositório.  Os padrões glob têm suporte e podem incluir vários arquivos. |
| format | Sim | Formato de arquivo do arquivo de configuração.  Os formatos válidos são: JSON, YAML, Properties. |
| connectionString | Sim | Cadeia de conexão para a instância de configuração do aplicativo. A cadeia de conexão deve ser armazenada como um segredo no repositório do GitHub e somente o nome do segredo deve ser usado no fluxo de trabalho. |
| separator | Sim | Separador usado ao mesclar o arquivo de configuração para pares de chave-valor.  Os valores válidos são:. , ; : - _ __ / |
| prefixo | Não | Prefixo a ser adicionado ao início das chaves. |
| label | Não | Rótulo usado ao definir pares de chave-valor. Se não for especificado, será usado um rótulo nulo. |
| strict | Não | Um valor booliano que determina se o modo estrito está habilitado. O valor padrão é false. |
| Detalhado | Não | Profundidade máxima para mesclar o arquivo de configuração.  A profundidade deve ser um número positivo.  O padrão não terá nenhuma profundidade máxima. |
| marcas | Não | Especifica a marca definida em pares chave-valor.  O formato esperado é uma forma em cadeias de um objeto JSON da seguinte forma: {[propertyName: String]: String;} Cada nome de propriedade-valor torna-se uma marca. |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a ação de sincronização de configuração de aplicativo do GitHub e como ela pode ser usada para automatizar atualizações para sua instância de configuração de aplicativo. Para saber como Azure App configuração reage às alterações em pares chave-valor, vá para o próximo [artigo](./concept-app-configuration-event.md).
