---
title: Como gerenciar segredos ao trabalhar com um Azure Dev Space
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: 49f53683b2499e790414d139dcb0bc0833005647
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280000"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerenciar segredos ao trabalhar com um Azure Dev Space

Os serviços podem exigir determinadas senhas, cadeias de conexão e outros segredos, como bancos de dados, ou outros serviços seguros do Azure. Ao definir os valores desses segredos nos arquivos de configuração, será possível disponibilizá-los no código como variáveis de ambiente.  Estes devem ser manipulados com cuidado para evitar comprometer a segurança dos segredos.

O Azure Dev Spaces fornece duas opções recomendadas e simplificadas para armazenar segredos em gráficos Helm gerados pela Azure Dev Spaces ferramentas de cliente: no arquivo Values. dev. YAML e embutido diretamente em azds. YAML. Não é recomendável armazenar segredos em values.yaml. Fora das duas abordagens para gráficos Helm gerados pela ferramenta do cliente definida neste artigo, se você criar seu próprio gráfico do Helm, poderá usar o gráfico do Helm diretamente para gerenciar e armazenar segredos.

## <a name="method-1-valuesdevyaml"></a>Método 1: values.dev.yaml
1. Abra o VS Code com o projeto que está habilitado para o Azure Dev Spaces.
2. Adicione um arquivo chamado _Values. dev. YAML_ na mesma pasta que o _azds. YAML_ existente e defina sua chave secreta e valores, como no exemplo a seguir:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. YAML_ já faz referência ao arquivo _Values. dev. YAML_ , se existir. Se você preferir um nome de arquivo diferente, atualize a seção install. Values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Modifique o código de serviço para referir-se a esses segredos como variáveis de ambiente, como no exemplo a seguir:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

    ```
    azds up
    ```
 
6. (Opcional) Na linha de comando, verifique se estes segredos foram criados:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Certifique-se de adicionar _values.dev.yaml_ ao arquivo _.gitignore_ para evitar confirmar segredos no controle de origem.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Método 2: Em linha diretamente em azds.yaml
1.  Em _azds.yaml_, configure os segredos na seção de yaml configurações/desenvolver/instalar. Embora não seja possível inserir valores secretos diretamente, isso não é recomendável porque _azds.yaml_ está selecionado no controle do código-fonte. Em vez disso, adicione espaços reservados usando a sintaxe "$PLACEHOLDER".

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  Crie um arquivo _.env_ na mesma pasta que _azds.yaml_. Insira segredos usando a notação chave-valor padrão. Não confirme o arquivo _.env_ para o controle do código-fonte. (Para omitir do controle do código-fonte em sistemas de controle de versão baseados em git, adicione-o ao arquivo _. gitignore_ .) O exemplo a seguir mostra um arquivo _. env_ :

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Modifique o código-fonte de serviço para referenciar esses segredos no código, como no exemplo a seguir:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

    ```
    azds up
    ```

4.  (opcional) Ver segredos do kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Próximas etapas

Com esses métodos, é possível se conectar com segurança a um banco de dados, um Cache do Azure para Redis ou acessar serviços seguros do Azure.
 
