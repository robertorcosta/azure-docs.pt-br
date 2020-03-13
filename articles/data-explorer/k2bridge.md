---
title: Visualizar dados do Azure Data Explorer usando o Kibana
description: Neste artigo, você aprenderá a configurar o Azure Data Explorer como uma fonte de dados para Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164807"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualizar dados do Azure Data Explorer no Kibana com o conector de código-fonte aberto do K2Bridge

K2Bridge (ponte Kibana-Kusto) permite que você use o Azure Data Explorer como a fonte de dados e visualize esses dados no Kibana. K2Bridge é um aplicativo em contêineres [de](https://github.com/microsoft/K2Bridge) software livre que atua como um proxy entre uma instância de Kibana e um cluster de data Explorer do Azure. Este artigo descreve como usar o K2Bridge para criar essa conexão.

K2Bridge traduz as consultas Kibana para a linguagem de consulta Kusto (KQL) e envia os resultados de Data Explorer do Azure de volta para Kibana. 

   ![gráfico](media/k2bridge/k2bridge-chart.png)

O K2Bridge dá suporte à guia Discover do Kibana, onde você pode:
* Pesquisar e explorar os dados
* Resultados do filtro
* Adicionar ou remover campos na grade de resultados
* Exibir conteúdo do registro
* Salvar e compartilhar pesquisas

A imagem abaixo mostra uma instância de Kibana associada ao Azure Data Explorer pelo K2Bridge. A experiência do usuário no Kibana é inalterada.

   [Página ![Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

Antes de poder visualizar dados do Azure Data Explorer no Kibana, tenha o seguinte pronto:

* [Helm v3](https://github.com/helm/helm#install), o Gerenciador de pacotes do kubernetes
* O cluster AKS (serviço kubernetes do Azure) ou qualquer outro cluster kubernetes (versão 1,14 para a versão 1,16 foi testado e verificado). Se você precisar de um cluster AKS, consulte Implantar um cluster AKS [usando o CLI do Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) ou [usando o portal do Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Um [cluster data Explorer do Azure](create-cluster-database-portal.md), incluindo:
    * A URL do cluster de Data Explorer do Azure 
    * Nome do banco de dados
    
* Uma entidade de serviço do Azure AD autorizada a exibir dados no Azure Data Explorer, incluindo:
    * A ID do cliente 
    * O segredo do cliente

    É recomendável uma entidade de serviço com a permissão ' Visualizador '. Não é recomendável usar permissões mais altas.

    * [Defina as permissões de exibição do cluster para a entidade de serviço do Azure ad](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Para obter mais informações sobre a entidade de serviço do Azure AD, consulte [criar uma entidade de serviço do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Executar o K2Bridge no serviço kubernetes do Azure (AKS)

Por padrão, K2Bridges's Helm Chart faz referência a uma imagem publicamente disponível localizada no registro de contêiner da Microsoft (MCR). O MCR não exige nenhuma credencial e funciona pronto para uso.

1. Baixe os gráficos Helm necessários.

1. Adicione a dependência Elasticsearch a Helm. 
    O motivo da dependência Elasticsearch é que o K2Bridge usa uma instância pequena Elasticsearch interna para atender a solicitações relacionadas a metadados (como padrões de índice e consultas salvas). Nenhum dado comercial é salvo nessa instância interna e pode ser considerado como um detalhe de implementação. 

    1. Para adicionar a dependência Elasticsearch a Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Para obter o gráfico K2Bridge sob o diretório de gráficos do repositório GitHub:
        1. Clone o repositório do [GitHub](https://github.com/microsoft/K2Bridge).
        1. Vá para o diretório do repositório raiz do K2Bridges.
        1. Execute:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Implantar K2Bridge:

    1. Defina as variáveis com os valores corretos para o seu ambiente:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Adicional Habilitar telemetria do Aplicativo Azure insights. 
        Se esta for a primeira vez que você está usando o Aplicativo Azure insights, primeiro [crie um recurso de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Você precisará [copiar a chave de instrumentação](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) para uma variável: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Instale o gráfico K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        Em [configuração](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) , você pode encontrar o conjunto completo de opções de configuração.

    1. A saída do comando irá sugerir o próximo comando Helm a ser executado para implantar o Kibana. Opcionalmente, execute:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Use o encaminhamento de porta para acessar o Kibana no localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Conecte-se ao Kibana navegando até http://127.0.0.1:5601.

    1. Expor Kibana aos usuários finais. Há vários métodos para fazer isso. O método usado em grande parte depende de seu caso de uso.

        Por exemplo:

        Expor o serviço como um serviço de balanceador de carga. Para fazer isso, adicione o seguinte parâmetro ao comando de instalação K2Bridge Helm ([acima](#install-k2bridge-chart)):

        `--set service.type=LoadBalancer`
    
        Em seguida, execute:

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        A saída deve ser assim: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        Você pode usar o IP externo gerado que aparece e usá-lo para acessar o Kibana abrindo um navegador para: `\<EXTERNAL-IP>:5601`.

1. Configure os padrões de índice para acessar seus dados:  
Em uma nova instância de Kibana:
     1. Abra Kibana.
     1. Navegue até gerenciamento.
     1. Selecione **padrões de índice**. 
     1. Crie um padrão de índice.
O nome do índice deve corresponder exatamente ao nome da tabela ou nome da função, sem um asterisco. Você pode copiar a linha relevante da lista.

> [!Note]
> Para executar em outros provedores de kubernetes, altere o Elasticsearch storageClassName no `values.yaml` para se ajustar ao que foi sugerido pelo provedor.

## <a name="visualize-data"></a>Visualizar dados

Quando o Data Explorer do Azure é configurado como uma fonte de dados para Kibana, você pode usar o Kibana para explorar os dados. 

1. No Kibana, no menu à esquerda, selecione a guia **descobrir** .

1. Na lista suspensa à esquerda, selecione um padrão de índice (nesse caso, uma tabela de Data Explorer do Azure), que define a fonte de dados que você deseja explorar.
    
   ![Selecionar um padrão de índice](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Se os dados tiverem um campo de filtro de hora, você poderá especificar o intervalo de tempo. No canto superior direito da página, defina um filtro de tempo. Por padrão, o Discover mostra dados dos últimos 15 minutos.

   ![Filtro de tempo](media/k2bridge/k2bridge-time-filter.png)
    
1. A tabela de resultados mostra os primeiros 500 registros. Você pode expandir um documento para examinar seus dados de campo em formatos de tabela ou JSON.

   ![Expandir um registro](media/k2bridge/k2bridge-expand-record.png)

1. Por padrão, a tabela de resultados inclui colunas para o documento _source e o campo de hora (se existir). Você pode escolher colunas específicas a serem adicionadas à tabela de resultados selecionando **Adicionar** ao lado do nome do campo na barra lateral esquerda.

   ![Colunas específicas](media/k2bridge/k2bridge-specific-columns.png)
    
1. Na barra de consulta, você pode pesquisar os dados por:
    * Inserindo um termo de pesquisa
    * Usando a sintaxe de consulta Lucene. 
    Por exemplo:
        * Pesquise "Error" para localizar todos os registros que contêm esse valor. 
        * Pesquise "status: 200" para obter todos os registros com um valor de status de 200. 
    * Usando operadores lógicos (e, ou, não)
    * Usando curingas (asterisco "\*" ou ponto de interrogação "?") Por exemplo:
        * O `"destination_city: L*"` de consulta corresponderá a registros em que o valor de cidade de destino começa com "l" (K2Bridge não diferencia maiúsculas de minúsculas).

    ![Executar consulta](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > Na [pesquisa](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md), você pode encontrar mais regras de pesquisa e lógica.

1. Para filtrar os resultados da pesquisa, use a **lista de campos** na barra lateral direita da página. 
    A lista de campos é onde você pode ver:
    * Os cinco valores principais para o campo
    * O número de registros que contêm o campo
    * A porcentagem de registros que contêm cada valor. 
    
    >[!Tip]
    > Use o ícone de lupa (+) para localizar todos os registros que têm um valor específico.
    
    ![Lista de campos](media/k2bridge/k2bridge-field-list.png)
   
    Você também pode filtrar os resultados usando o ícone de lupa (+) no modo de exibição de formato de tabela de resultados de cada registro na tabela de resultados.
    
     ![Lista de tabelas](media/k2bridge/k2bridge-table-list.png)
    
1. Selecione para **salvar** ou **compartilhar** sua pesquisa.

     ![Salvar pesquisa](media/k2bridge/k2bridge-save-search.png)
