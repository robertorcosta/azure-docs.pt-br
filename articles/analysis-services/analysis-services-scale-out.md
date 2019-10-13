---
title: Escala horizontal do Azure Analysis Services | Microsoft Docs
description: Replicar os servidores do Azure Analysis Services com escala horizontal
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0e6a234e8b69eb48f00687916d4a7b48d3ba1040
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301176"
---
# <a name="azure-analysis-services-scale-out"></a>Escala horizontal do Azure Analysis Services

Com a expansão, as consultas de cliente podem ser distribuídas entre várias *réplicas de consulta* em um *pool de consultas*, reduzindo os tempos de resposta durante cargas de trabalho de consulta alta. Você também pode separar o processamento do pool de consultas, garantindo que as consultas não são afetadas negativamente pelas operações de processamento. A escala horizontal pode ser configurada no Portal do Azure ou usando a API REST do Azure Analysis Services.

A escala horizontal está disponível para servidores no tipo de preço Standard. Cada réplica de consulta é cobrada com a mesma taxa de seu servidor. Todas as réplicas de consulta são criadas na mesma região que o servidor. O número de réplicas de consulta que você pode configurar é limitado pela região em que o seu servidor está. Para obter mais informações, veja [Disponibilidade por região](analysis-services-overview.md#availability-by-region). A escala horizontal não aumenta a quantidade de memória disponível para o servidor. Para aumentar a memória, você precisa atualizar seu plano. 

## <a name="why-scale-out"></a>Por que escalar horizontalmente?

Em uma implantação típica do servidor, um servidor funciona como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em modelos em seu servidor exceder as QPUs (Unidades de Processamento de Consulta) do plano do servidor ou o processamento de modelo ocorrer ao mesmo tempo que cargas de trabalho de consulta altas, o desempenho poderá diminuir. 

Com a expansão, você pode criar um pool de consultas com até sete recursos adicionais de réplica de consulta (oito no total, incluindo o servidor *primário* ). Você pode dimensionar o número de réplicas no pool de consultas para atender às demandas de QPU em momentos críticos e pode separar um servidor de processamento do pool de consultas a qualquer momento. 

Independentemente do número de réplicas de consulta que você tem em um pool de consulta, as cargas de trabalho de processamento não são distribuídas entre as réplicas de consulta. O servidor primário serve como servidor de processamento. As réplicas de consulta servem somente para os bancos de dados de modelo sincronizados entre o servidor primário e cada réplica no pool de consultas. 

Ao escalar horizontalmente, pode levar até cinco minutos para que novas réplicas de consulta sejam adicionadas incrementalmente ao pool de consulta. Quando todas as novas réplicas de consulta estiverem em funcionamento, novas conexões de cliente serão balanceadas com carga entre os recursos no pool de consultas. As conexões de clientes existentes não são alteradas a partir do recurso ao qual elas estão conectadas atualmente. Ao dimensionar, quaisquer conexões de cliente existentes para um recurso de pool de consulta que está sendo removido do pool de consulta são finalizadas. Os clientes podem se reconectar a um recurso do pool de consultas restante.

## <a name="how-it-works"></a>Como ele funciona

Ao configurar a expansão na primeira vez, os bancos de dados de modelo em seu servidor primário são sincronizados *automaticamente* com novas réplicas em um novo pool de consulta. A sincronização automática ocorre apenas uma vez. Durante a sincronização automática, os arquivos de dados do servidor primário (criptografados em repouso no armazenamento de BLOB) são copiados para um segundo local, também criptografados em repouso no armazenamento de BLOBs. As réplicas no pool de consultas são então *alimentadas* com dados do segundo conjunto de arquivos. 

Embora uma sincronização automática seja executada somente quando você escala horizontalmente um servidor pela primeira vez, você também pode executar uma sincronização manual. A sincronização garante que os dados em réplicas no pool de consulta correspondam ao servidor primário. Ao processar (atualizar) modelos no servidor primário, uma sincronização deve ser executada *após* a conclusão das operações de processamento. Esta sincronização copia os dados atualizados dos arquivos do servidor primário no armazenamento de BLOBs para o segundo conjunto de arquivos. As réplicas no pool de consultas são então alimentadas com dados atualizados do segundo conjunto de arquivos no armazenamento de BLOBs. 

Ao executar uma operação de expansão subsequente, por exemplo, aumentar o número de réplicas no pool de consultas de duas a cinco, as novas réplicas são alimentadas com dados do segundo conjunto de arquivos no armazenamento de BLOBs. Não há sincronização. Se, em seguida, você executar uma sincronização depois de escalar horizontalmente, as novas réplicas no pool de consultas seriam alimentadas duas vezes-uma hidratação redundante. Ao executar uma operação de expansão subsequente, é importante ter em mente:

* Execute uma sincronização *antes da operação de expansão* para evitar hidratação redundantes das réplicas adicionadas. A sincronização simultânea e as operações de expansão em execução ao mesmo tempo não são permitidas.

* Ao automatizar operações de processamento *e* expansão, é importante processar primeiro os dados no servidor primário, executar uma sincronização e, em seguida, executar a operação de expansão. Essa sequência garante um impacto mínimo sobre os recursos de memória e QPU.

* A sincronização é permitida mesmo quando não há réplicas no pool de consultas. Se você estiver expandindo de zero para uma ou mais réplicas com novos dados de uma operação de processamento no servidor primário, execute a sincronização primeiro sem réplicas no pool de consultas e, em seguida, expanda horizontalmente. A sincronização antes de escalar horizontalmente evita hidratação redundantes das réplicas adicionadas recentemente.

* Ao excluir um banco de dados modelo do servidor primário, ele não é excluído automaticamente das réplicas no pool de consultas. Você deve executar uma operação de sincronização usando o comando do PowerShell [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) que remove os arquivos do banco de dados do local de armazenamento de blob compartilhado da réplica e, em seguida, exclui o banco de dados modelo nas réplicas no pool de consultas. Para determinar se um banco de dados modelo existe em réplicas no pool de consultas, mas não no servidor primário, certifique-se de que a configuração **separar o servidor de processamento da consulta do pool** seja **Sim**. Em seguida, use o SSMS para se conectar ao servidor primário usando o qualificador `:rw` para ver se o banco de dados existe. Em seguida, conecte-se às réplicas no pool de consultas conectando-se sem o qualificador `:rw` para ver se o mesmo banco de dados também existe. Se o banco de dados existir em réplicas no pool de consultas, mas não no servidor primário, execute uma operação de sincronização.   

* Ao renomear um banco de dados no servidor primário, há uma etapa adicional necessária para garantir que o banco de dados seja sincronizado corretamente com as réplicas. Após a renomeação, execute uma sincronização usando o comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) especificando o parâmetro `-Database` com o nome antigo do banco de dados. Essa sincronização remove o banco de dados e os arquivos com o nome antigo de qualquer réplica. Em seguida, execute outra sincronização especificando o parâmetro `-Database` com o novo nome do banco de dados. A segunda sincronização copia o banco de dados nomeado recentemente para o segundo conjunto de arquivos e hidratam quaisquer réplicas. Essas sincronizações não podem ser executadas usando o comando sincronizar modelo no Portal.

### <a name="separate-processing-from-query-pool"></a>Separe o processamento do pool de consulta

Para desempenho máximo para operações de processamento e consulta, você pode optar por separar seu servidor de processamento do pool de consulta. Quando separadas, novas conexões de cliente são atribuídas a réplicas de consulta somente no pool de consultas. Se as operações de processamento ocuparem apenas um curto período de tempo, você poderá optar por separar seu servidor de processamento do pool de consulta apenas pelo tempo necessário para executar operações de processamento e sincronização e, em seguida, incluí-lo novamente no pool de consultas. Ao separar o servidor de processamento do pool de consulta ou adicioná-lo de volta ao pool de consultas pode levar até cinco minutos para que a operação seja concluída.

## <a name="monitor-qpu-usage"></a>Monitorar o uso de QPU

Para determinar se a escala horizontal para seu servidor é necessária, monitore o servidor no Portal do Azure usando Métricas. Se a QPU for maximizada regularmente, isso significa que o número de consultas em relação aos modelos está excedendo o limite de QPU do plano. A métrica de comprimento da fila do trabalho do pool de consulta também aumenta quando o número de consultas na fila do pool de thread de consulta excede a QPU disponível. 

Outra boa métrica a ser observada é a média de QPU por ServerResourceType. Essa métrica compara o QPU médio para o servidor primário com o do pool de consulta. 

![Métricas de expansão de consulta](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Para configurar o QPU by ServerResourceType
1. Em um gráfico de linhas de métricas, clique em **Adicionar métrica**. 
2. Em **recurso**, selecione o servidor e, em seguida, em **namespace de métrica**, selecione **Analysis Services métricas padrão**e, em **métrica**, selecione **QPU**e, em **agregação**, selecione **Méd**. 
3. Clique em **aplicar divisão**. 
4. Em **valores**, selecione **ServerResourceType**.  

Para obter mais informações, consulte [Monitorar métricas do servidor](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar a escala horizontal

### <a name="in-azure-portal"></a>No Portal do Azure

1. No portal, clique em **Escalar horizontalmente**. Use o controle deslizante para selecionar o número de servidores de réplica de consulta. O número de réplicas escolhido é além dos servidores existentes.  

2. Em **Separar o servidor de processamento do pool de consulta**, selecione Sim para excluir o servidor de processamento dos servidores de consulta. [As conexões](#connections) de cliente que usam a cadeia de conexão padrão (sem `:rw`) são redirecionadas para réplicas no pool de consultas. 

   ![Controle deslizante da escala horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **Salvar** para provisionar os novos servidores de réplica de consulta. 

Ao configurar a expansão para um servidor na primeira vez, os modelos em seu servidor primário são sincronizados automaticamente com réplicas no pool de consultas. A sincronização automática ocorre apenas uma vez, quando você configura primeiro a expansão para uma ou mais réplicas. Alterações subsequentes no número de réplicas no mesmo servidor *não dispararão outra sincronização automática*. A sincronização automática não ocorrerá novamente mesmo que você defina o servidor como zero réplicas e, em seguida, redimensione horizontalmente para qualquer número de réplicas. 

## <a name="synchronize"></a>Sincronizar 

As operações de sincronização devem ser executadas manualmente ou usando a API REST.

### <a name="in-azure-portal"></a>No Portal do Azure

Em **Visão Geral** > modelo > **Sincronizar modelo**.

![Controle deslizante da escala horizontal](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Use a operação de **sincronização**.

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obter o status de sincronização  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Códigos de status de retorno:


|Código  |DESCRIÇÃO  |
|---------|---------|
|-1     |  Inválido       |
|0     | Replicando        |
|1     |  Reidratar       |
|2     |   Concluído       |
|3     |   Com falha      |
|4     |    Finalizando     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de usar o PowerShell, [Instale ou atualize o módulo de Azure PowerShell mais recente](/powershell/azure/install-az-ps). 

Para executar a sincronização, use [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Para definir o número de réplicas de consulta, use [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro `-ReadonlyReplicaCount` opcional.

Para separar o servidor de processamento do pool de consulta, use [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro `-DefaultConnectionMode` opcional para usar `Readonly`.

Para saber mais, consulte [usando uma entidade de serviço com o módulo AZ. AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>conexões

Na página Visão Geral do servidor, há dois nomes de servidor. Se você ainda não tiver configurado a escala horizontal para um servidor, os dois nomes de servidor funcionam da mesma forma. Depois de configurar a expansão para um servidor, você precisará especificar o nome do servidor adequado dependendo do tipo de conexão. 

Para conexões de cliente do usuário final como o Power BI Desktop, Excel e aplicativos personalizados, use **Nome do servidor**. 

Para SSMS, SSDT e cadeias de conexão no PowerShell, aplicativos do Azure Functions e AMO, use o **Nome do servidor de gerenciamento**. O nome do servidor de gerenciamento inclui um qualificador especial `:rw` (leitura-gravação). Todas as operações de processamento ocorrem no servidor de gerenciamento (primário).

![nomes do servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Reduzir, reduzir verticalmente versus Escalabilidade

Você pode alterar o tipo de preço em um servidor com várias réplicas. O mesmo tipo de preço se aplica a todas as réplicas. Uma operação de escala primeiro desativará todas as réplicas ao mesmo tempo e, em seguida, abrirá todas as réplicas no novo tipo de preço.

## <a name="troubleshoot"></a>Solucionar problemas

**Problema:** usuários obtêm o erro **não é possível localizar o servidor '\<nome do servidor >' instância no modo de conexão 'ReadOnly'.**

**Solução:** Ao selecionar o **servidor de processamento separado da opção pool de consulta** , as conexões de cliente que usam a cadeia de conexão padrão (sem `:rw`) são redirecionadas para as réplicas do pool de consulta. Se as réplicas no pool de consulta estiverem ainda online porque a sincronização ainda não foi concluída, as conexões de cliente redirecionada podem falhar. Para evitar conexões com falha, deve haver, pelo menos, dois servidores no pool de consulta ao executar uma sincronização. Cada servidor é sincronizado individualmente, enquanto os outros permanecem online. Se você optar por não ter o servidor de processamento no pool de consulta durante o processamento, você poderá optar por removê-lo do pool para processamento e, em seguida, adicioná-lo novamente ao pool após a conclusão do processamento, mas antes da sincronização. Use as métricas Memória e QPU para monitorar o status de sincronização.



## <a name="related-information"></a>Informações relacionadas

[Monitorar métricas do servidor](analysis-services-monitor.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md) 
