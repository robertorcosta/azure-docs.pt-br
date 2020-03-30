---
title: Visualize dados do Azure Data Explorer usando Kibana
description: Neste artigo, você aprende como configurar o Azure Data Explorer como fonte de dados para kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065605"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualize dados do Azure Data Explorer em Kibana com o conector de código aberto K2Bridge

K2Bridge (Kibana-Kusto Bridge) permite que você use o Azure Data Explorer como fonte de dados e visualize esses dados em Kibana. K2Bridge é um aplicativo contêiner [de código aberto](https://github.com/microsoft/K2Bridge) que atua como um proxy entre uma instância kibana e um cluster Azure Data Explorer. Este artigo descreve como usar o K2Bridge para criar essa conexão.

O K2Bridge traduz as consultas do Kibana para o Kusto Query Language (KQL) e envia os resultados do Azure Data Explorer de volta para Kibana. 

   ![gráfico](media/k2bridge/k2bridge-chart.png)

O K2Bridge suporta a guia Discover da Kibana, onde você pode:
* Pesquise e explore os dados
* Resultados do filtro
* Adicionar ou remover campos na grade de resultados
* Exibir conteúdo de registro
* Salvar e compartilhar pesquisas

A imagem abaixo mostra uma ocorrência de Kibana vinculada ao Azure Data Explorer pela K2Bridge. A experiência do usuário em Kibana é inalterada.

   [![Página de Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

Antes de visualizar dados do Azure Data Explorer em Kibana, tenha o seguinte pronto:

* [Helm V3](https://github.com/helm/helm#install), o gerenciador de pacotes Kubernetes
* O cluster Azure Kubernetes Service (AKS) ou qualquer outro cluster Kubernetes (versão 1.14 para a versão 1.16 foram testados e verificados). Se você precisar de um cluster AKS, consulte Implantar um cluster AKS [usando o Cli do Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) ou usando o portal [Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Um [cluster Azure Data Explorer,](create-cluster-database-portal.md)incluindo:
    * URL do cluster Azure Data Explorer 
    * Nome do banco de dados
    
* Um diretor de serviço azure AD autorizado a visualizar dados no Azure Data Explorer, incluindo:
    * A ID do cliente 
    * O segredo do cliente

    Recomenda-se um diretor de serviço com permissão 'Viewer'. É desencorajado usar permissões mais altas.

    * [Defina as permissões de exibição do cluster para o principal de serviço Azure AD](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Para obter mais informações sobre o diretor de serviços azure AD, consulte [Criar um diretor de serviço AD do Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Execute o K2Bridge no Azure Kubernetes Service (AKS)

Por padrão, o gráfico Helm da K2Bridges faz referência a uma imagem disponível publicamente localizada no MCR (Container Registry, registro de contêineres da Microsoft). McR não requer credenciais, e funciona fora da caixa.

1. Baixe os gráficos de Helm necessários.

1. Adicione a dependência do Elasticsearch ao Helm. 
    A razão para a dependência do Elasticsearch é que o K2Bridge usa uma pequena instância do Elasticsearch interno para atender solicitações relacionadas a metadados (como padrões de índice e consultas salvas). Nenhum dado de negócios é salvo nesta instância interna, e pode ser considerado como um detalhe de implementação. 

    1. Para adicionar a dependência do Elasticsearch ao Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Para obter o gráfico K2Bridge o diretório de gráficos do repositório GitHub:
        1. Clone o repositório do [GitHub](https://github.com/microsoft/K2Bridge).
        1. Vá para o diretório de repositório raiz K2Bridges.
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

    1. (Opcional) Habilite a telemetria do Azure Application Insights. 
        Se esta é a primeira vez que você está usando o Azure Application Insights, você deve primeiro [criar um recurso application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Você precisará [copiar a chave de instrumentação](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) para uma variável: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Instale o gráfico do K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        Em [Configuração](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) você pode encontrar o conjunto completo de opções de configuração.

    1. A saída de comando sugerirá o próximo comando Helm para executar para implantar Kibana. Opcionalmente, execute:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Use o encaminhamento da porta para acessar o Kibana no host local: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Conecte-se a Kibana navegando para http://127.0.0.1:5601.

    1. Exponha kibana aos usuários finais. Existem vários métodos para fazê-lo. O método que você usa depende em grande parte do seu caso de uso.

        Por exemplo: 

        Exponha o serviço como um serviço LoadBalancer. Para isso, adicione `--set service.type=LoadBalancer` o parâmetro ao comando K2Bridge Helm install[(acima).](#install-k2bridge-chart)        
    
        Em seguida, execute:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        A saída deve ser assim: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        Em seguida, você pode usar o IP EXTERNO gerado que aparece e `<EXTERNAL-IP>:5601`usá-lo para acessar o Kibana abrindo um navegador para .

1. Configure padrões de índice para acessar seus dados:  
Em um novo exemplo de Kibana:
     1. Abra Kibana.
     1. Navegue até a gerência.
     1. Selecione **Padrões de índice**. 
     1. Crie um padrão de índice.
O nome do índice deve corresponder exatamente ao nome da tabela ou nome da função, sem um asterisco. Você pode copiar a linha relevante da lista.

> [!Note]
> Para executar em outros provedores kubernetes, altere o armazenamento ElasticsearchClassName `values.yaml` para se adequar ao sugerido pelo provedor.

## <a name="visualize-data"></a>Visualizar dados

Quando o Azure Data Explorer é configurado como uma fonte de dados para kibana, você pode usar o Kibana para explorar os dados. 

1. Em Kibana, no menu à esquerda, selecione a guia **Descobrir.**

1. Na lista de baixa esquerda, selecione um padrão de índice (neste caso, uma tabela do Azure Data Explorer), que define a fonte de dados que você deseja explorar.
    
   ![Selecione um padrão de índice](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Se seus dados têm um campo de filtro de tempo, você pode especificar o intervalo de tempo. No canto superior direito da página, defina um filtro de tempo. Por padrão, o Discover mostra dados dos últimos 15 minutos.

   ![Filtro de tempo](media/k2bridge/k2bridge-time-filter.png)
    
1. A tabela de resultados mostra os primeiros 500 registros. Você pode expandir um documento para examinar seus dados de campo em formatos JSON ou de tabela.

   ![Expandir um disco](media/k2bridge/k2bridge-expand-record.png)

1. Por padrão, a tabela de resultados inclui colunas para o documento _source e o campo de tempo (se ele existir). Você pode escolher colunas específicas a serem adicionadas à tabela de resultados selecionando **adicionar** ao lado do nome do campo na barra lateral esquerda.

   ![Colunas específicas](media/k2bridge/k2bridge-specific-columns.png)
    
1. Na barra de consulta, você pode pesquisar os dados por:
    * Inserindo um termo de pesquisa
    * Usando a sintaxe de consulta lucene. 
    Por exemplo: 
        * Pesquise "erro" para encontrar todos os registros que contenham esse valor. 
        * Procure por "status: 200", para obter todos os registros com um valor de status de 200. 
    * Usando operadores lógicos (E, OU, NÃO)
    * Usando curingas (asterisco " \* " ou ponto de interrogação "?") Por exemplo:
        * A consulta `"destination_city: L*"` corresponderá aos registros onde o valor da cidade de destino começa com "l" (K2Bridge não é sensível a maiúsculas e minúsculas).

    ![Executar consulta](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > Em [Pesquisar,](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)você pode encontrar mais regras de pesquisa e lógica.

1. Para filtrar os resultados da pesquisa, use a lista de **campo** na barra lateral direita da página. 
    A lista de campo é onde você pode ver:
    * Os cinco melhores valores para o campo
    * O número de registros que contêm o campo
    * A porcentagem de registros que contêm cada valor. 
    
    >[!Tip]
    > Use o ícone de lupa (+) para encontrar todos os registros que tenham um valor específico.
    
    ![Lista de campos](media/k2bridge/k2bridge-field-list.png)
   
    Você também pode filtrar os resultados usando o ícone de lupa (+) na exibição do formato da tabela de resultados de cada registro na tabela de resultados.
    
     ![Lista de tabelas](media/k2bridge/k2bridge-table-list.png)
    
1. Selecione para **salvar** ou **compartilhar** sua pesquisa.

     ![Salvar pesquisa](media/k2bridge/k2bridge-save-search.png)
