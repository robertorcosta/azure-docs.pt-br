---
title: Sincronize seu repositório gitHub para configuração de aplicativos
description: Use as ações do GitHub para atualizar automaticamente a instância de configuração do aplicativo quando atualizar seu repositório do GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585491"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Sincronize seu repositório gitHub para configuração de aplicativos

As equipes que desejam continuar usando suas práticas de controle de origem existentes podem usar o GitHub Actions para sincronizar automaticamente seu repositório GitHub com sua loja de configuração de aplicativos. Isso permite que você faça alterações em seus arquivos de configuração como você normalmente faria, ao mesmo tempo em que recebe benefícios de configuração do aplicativo, como: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configuração centralizada fora do seu código <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Atualizar a configuração sem reimplantar todo o seu aplicativo <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integração com serviços como Serviço de Aplicativos e Funções do Azure. 

Um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) do GitHub Actions define um processo automatizado em um repositório do GitHub. O *Azure App Configuration Sync* Action aciona atualizações para uma instância de configuração do aplicativo quando são feitas alterações no repositório de origem. Ele usa um arquivo YAML (.yml) encontrado no caminho do `/.github/workflows/` seu repositório para definir as etapas e parâmetros. Você pode ativar atualizações de configuração ao empurrar, revisar ou ramificar arquivos de configuração de aplicativos, assim como faz com o código do aplicativo.

A [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) do GitHub fornece uma visão aprofundada dos fluxos de trabalho e ações do GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Habilite as ações do GitHub em seu repositório
Para começar a usar essa ação do GitHub, vá para o repositório e selecione a guia **Ações.** Clique em **Novo fluxo de trabalho**e, em seguida, **configure um fluxo de trabalho você mesmo**. Finalmente, pesquise no mercado o "Azure App Configuration Sync".
> [!div class="mx-imgBorder"]
> ![Selecione a guia Ação](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecione a sincronização de configuração do aplicativo Ação](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizar arquivos de configuração após um empurrão
Esta ação sincroniza arquivos de configuração do aplicativo `appsettings.json`Azure quando uma alteração é empurrada para . Quando um desenvolvedor empurra `appsettings.json`uma alteração para , a ação Sincronia de Configuração do Aplicativo atualiza a instância de configuração do aplicativo com os novos valores.

A primeira seção deste fluxo de trabalho especifica que `appsettings.json` a ação é acionada *em* um *push* contendo o ramo *principal.* A segunda seção lista os trabalhos executados assim que a ação é acionada. A ação verifica os arquivos relevantes e atualiza a instância de configuração do aplicativo usando a seqüência de conexões armazenada como um segredo no repositório.  Para obter mais informações sobre o uso de segredos no GitHub, consulte [o artigo do GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sobre a criação e o uso de segredos criptografados.

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

## <a name="use-a-dynamic-label-on-sync"></a>Use um rótulo dinâmico em sincronia
A ação anterior atualiza a `appsettings.json` instância de configuração do aplicativo sempre que é atualizada. Esta ação insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronização possa ser identificada exclusivamente e permitindo que as alterações de código sejam mapeadas para configurar alterações.

A primeira seção deste fluxo de trabalho especifica que `appsettings.json` a ação é acionada *em* um *push* contendo o ramo *principal.* A segunda seção executa um trabalho que cria um rótulo exclusivo para a atualização de configuração com base no hash commit. O trabalho então atualiza a instância de configuração do aplicativo com os novos valores e o rótulo exclusivo para esta atualização.

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

## <a name="use-strict-sync"></a>Use sincronização rigorosa
Quando o modo estrito é ativado, a sincronização garante que a instância configuração do aplicativo corresponda ao arquivo de configuração para o prefixo e o rótulo dado exatamente. Os pares de valor de chave com o mesmo prefixo e rótulo que não estão no arquivo de configuração são excluídos. 
 
Se o modo estrito não estiver ativado, a sincronização só definirá valores-chave a partir do arquivo de configuração. Nenhum par de valor-chave será excluído. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Use profundidade máxima para limitar o GitHub Action
O comportamento padrão para atributos JSON aninhados é achatar todo o objeto.  O JSON abaixo define este par de valor-chave:

| Chave | Valor |
| --- | --- |
| objeto:Interno:Chave interna | Valor Interno |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Se o objeto aninhado for destinado a ser o valor empurrado para a instância de configuração, você poderá usar o valor de *profundidade* para impedir o achatamento na profundidade apropriada. 

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

Dada uma profundidade de 2, o exemplo acima agora retorna o seguinte par de valor-chave:

| Chave | Valor |
| --- | --- |
| Objeto:Interior | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Entenda os inputs de ação
Os parâmetros de entrada especificam os dados usados pela ação durante o tempo de execução.  A tabela a seguir contém parâmetros de entrada aceitos pelo App Configuration Sync e os valores esperados para cada um.  Para obter mais informações sobre entradas de ação para as ações do GitHub, consulte a [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)do GitHub .

> [!Note]
> Os IDs de entrada são insensíveis ao caso.


| Nome de entrada | Obrigatório? | Valor |
|----|----|----|
| Configurationfile | Sim | Caminho relativo ao arquivo de configuração no repositório.  Padrões glob são suportados e podem incluir vários arquivos. |
| format | Sim | Formato do arquivo do arquivo de configuração.  Os formatos válidos são: JSON, YAML, propriedades. |
| connectionString | Sim | String conexão para a instância de configuração do aplicativo. A seqüência de conexões deve ser armazenada como um segredo no repositório do GitHub, e apenas o nome secreto deve ser usado no fluxo de trabalho. |
| separator | Sim | Separador usado ao achatar o arquivo de configuração para pares de valor-chave.  Os valores válidos são: . , ; : - _ __ / |
| prefixo | Não | Prefixo a ser adicionado ao início das teclas. |
| label | Não | Etiqueta usada ao definir pares de valor de tecla. Se não for especificado, um rótulo nulo é usado. |
| strict | Não | Um valor booleano que determina se o modo estrito está ativado. O valor padrão é false. |
| Profundidade | Não | Profundidade máxima para achatar o arquivo de configuração.  A profundidade deve ser um número positivo.  O padrão não terá profundidade máxima. |
| marcas | Não | Especifica o conjunto de etiquetas em pares de valor-chave.  O formato esperado é uma forma stringified de um objeto JSON da seguinte forma: { [propriedadeNome: string]: string; } Cada valor de nome de propriedade torna-se uma tag. |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a configuração do aplicativo Sync GitHub Action e como ele pode ser usado para automatizar atualizações na instância de configuração do aplicativo. Para saber como a configuração do aplicativo Azure reage às alterações nos pares de valor-chave, continue até o próximo [artigo](./concept-app-configuration-event.md).
