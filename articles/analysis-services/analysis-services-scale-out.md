---
title: Escala horizontal do Azure Analysis Services | Microsoft Docs
description: Replicar servidores do Azure Analysis Services com escala.. As consultas do cliente podem ser distribuídas entre várias réplicas de consulta em um pool de consulta sem escala.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247985"
---
# <a name="azure-analysis-services-scale-out"></a>Escala horizontal do Azure Analysis Services

Com a saída de escala, as consultas do cliente podem ser distribuídas entre várias *réplicas de consulta* em um *pool de consultas,* reduzindo os tempos de resposta durante cargas de trabalho de alta consulta. Você também pode separar o processamento do pool de consultas, garantindo que as consultas não são afetadas negativamente pelas operações de processamento. A escala horizontal pode ser configurada no Portal do Azure ou usando a API REST do Azure Analysis Services.

A escala horizontal está disponível para servidores no tipo de preço Standard. Cada réplica de consulta é cobrada com a mesma taxa de seu servidor. Todas as réplicas de consulta são criadas na mesma região que o servidor. O número de réplicas de consulta que você pode configurar é limitado pela região em que o seu servidor está. Para obter mais informações, veja [Disponibilidade por região](analysis-services-overview.md#availability-by-region). A escala horizontal não aumenta a quantidade de memória disponível para o servidor. Para aumentar a memória, você precisa atualizar seu plano. 

## <a name="why-scale-out"></a>Por que escalar?

Em uma implantação típica do servidor, um servidor funciona como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em modelos em seu servidor exceder as QPUs (Unidades de Processamento de Consulta) do plano do servidor ou o processamento de modelo ocorrer ao mesmo tempo que cargas de trabalho de consulta altas, o desempenho poderá diminuir. 

Com a escala ção, você pode criar um pool de consultas com até sete recursos adicionais de réplica de consulta (oito no total, incluindo seu servidor *principal).* Você pode dimensionar o número de réplicas no pool de consultas para atender às demandas de QPU em momentos críticos e pode separar um servidor de processamento do pool de consultas a qualquer momento. 

Independentemente do número de réplicas de consulta que você tem em um pool de consulta, as cargas de trabalho de processamento não são distribuídas entre as réplicas de consulta. O servidor principal serve como servidor de processamento. As réplicas de consulta servem apenas consultas contra os bancos de dados de modelo sincronizados entre o servidor principal e cada réplica no pool de consultas. 

Ao fazer o dimensionamento, pode levar até cinco minutos para que novas réplicas de consulta sejam adicionadas incrementalmente ao pool de consultas. Quando todas as novas réplicas de consulta estão em funcionamento, novas conexões de cliente são carregadas equilibradas entre os recursos no pool de consultas. As conexões de clientes existentes não são alteradas a partir do recurso ao qual elas estão conectadas atualmente. Ao dimensionar, quaisquer conexões de cliente existentes para um recurso de pool de consulta que está sendo removido do pool de consulta são finalizadas. Os clientes podem se reconectar a um recurso restante do pool de consultas.

## <a name="how-it-works"></a>Como ele funciona

Ao configurar a escala na primeira vez, os bancos de dados de modelo no servidor principal são *sincronizados automaticamente* com novas réplicas em um novo pool de consultas. A sincronização automática ocorre apenas uma vez. Durante a sincronização automática, os arquivos de dados do servidor principal (criptografados em repouso no armazenamento blob) são copiados para um segundo local, também criptografados em repouso no armazenamento blob. As réplicas no pool de consultas são então *hidratadas* com dados do segundo conjunto de arquivos. 

Enquanto uma sincronização automática é realizada apenas quando você dimensiona um servidor pela primeira vez, você também pode executar uma sincronização manual. A sincronização garante que os dados em réplicas no pool de consultas correspondam aos do servidor principal. Ao processar (atualizar) modelos no servidor principal, uma sincronização deve ser realizada *após a* conclusão das operações de processamento. Esta sincronização copia dados atualizados dos arquivos do servidor principal no armazenamento blob para o segundo conjunto de arquivos. As réplicas no pool de consultas são então hidratadas com dados atualizados do segundo conjunto de arquivos em blob storage. 

Ao realizar uma operação de escala subseqüente, por exemplo, aumentando o número de réplicas no pool de consultas de dois para cinco, as novas réplicas são hidratadas com dados do segundo conjunto de arquivos em blob storage. Não há sincronização. Se você fizer uma sincronização após a escalação, as novas réplicas na piscina de consulta seriam hidratadas duas vezes - uma hidratação redundante. Ao realizar uma operação de escala subseqüente, é importante ter em mente:

* Realize uma sincronização *antes da operação de escala* para evitar a hidratação redundante das réplicas adicionadas. As operações simultâneas de sincronização e escala em execução ao mesmo tempo não são permitidas.

* Ao automatizar as operações de processamento *e* de dimensionamento, é importante primeiro processar os dados no servidor principal, em seguida, executar uma sincronização e, em seguida, executar a operação de dimensionamento. Esta seqüência garante um impacto mínimo nos recursos de QPU e memória.

* A sincronização é permitida mesmo quando não há réplicas no pool de consultas. Se você estiver expandindo de zero para uma ou mais réplicas com novos dados de uma operação de processamento no servidor principal, execute a sincronização primeiro sem réplicas no pool de consultas e, em seguida, scale-out. Sincronizar antes de dimensionar evita a hidratação redundante das réplicas recém-adicionadas.

* Ao excluir um banco de dados de modelo do servidor principal, ele não é excluído automaticamente de réplicas no pool de consultas. Você deve executar uma operação de sincronização usando o comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell que remove o arquivo/s para esse banco de dados do local de armazenamento compartilhado da réplica e, em seguida, exclui o banco de dados do modelo nas réplicas no pool de consultas. Para determinar se existe um banco de dados de modelo em réplicas no pool de consultas, mas não no servidor principal, certifique-se de que o **servidor de processamento da configuração do pool de consulta** esteja em **Sim**. Em seguida, use o SSMS `:rw` para se conectar ao servidor principal usando o qualificador para ver se o banco de dados existe. Em seguida, conecte-se a réplicas `:rw` no pool de consultas conectando-se sem o qualificador para ver se o mesmo banco de dados também existe. Se o banco de dados existir em réplicas no pool de consultas, mas não no servidor principal, execute uma operação de sincronização.   

* Ao renomear um banco de dados no servidor principal, há uma etapa adicional necessária para garantir que o banco de dados esteja devidamente sincronizado com quaisquer réplicas. Depois de renomear, execute uma sincronização usando o comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) especificando o `-Database` parâmetro com o nome do banco de dados antigo. Essa sincronização remove o banco de dados e os arquivos com o nome antigo de quaisquer réplicas. Em seguida, execute outra `-Database` sincronização especificando o parâmetro com o novo nome do banco de dados. A segunda sincronização copia o banco de dados recém-nomeado para o segundo conjunto de arquivos e hidrata todas as réplicas. Essas sincronizações não podem ser realizadas usando o comando Sincronizar modelo no portal.

### <a name="synchronization-mode"></a>Modo de sincronização

Por padrão, as réplicas de consulta são rehidratadas na íntegra, não incrementalmente. A reidratação acontece em etapas. Eles são separados e conectados dois de cada vez (supondo que haja pelo menos três réplicas) para garantir que pelo menos uma réplica seja mantida on-line para consultas a qualquer momento. Em alguns casos, os clientes podem precisar se reconectar a uma das réplicas online enquanto esse processo está ocorrendo. Usando a configuração **ReplicaSyncMode** (in Preview), agora você pode especificar que a sincronização da réplica de consulta ocorre em paralelo. A sincronização paralela oferece os seguintes benefícios: 

- Redução significativa no tempo de sincronização. 
- Os dados entre réplicas são mais propensos a serem consistentes durante o processo de sincronização. 
- Como os bancos de dados são mantidos on-line em todas as réplicas durante todo o processo de sincronização, os clientes não precisam se reconectar. 
- O cache na memória é atualizado incrementalmente apenas com os dados alterados, que podem ser mais rápidos do que reidratar totalmente o modelo. 

#### <a name="setting-replicasyncmode"></a>Configuração do replicasyncMode

Use SSMS para definir replicaSyncMode em propriedades avançadas. Os valores possíveis são: 

- `1`(padrão): Reidratação completa do banco de dados de réplicas em estágios (incrementais). 
- `2`: Sincronização otimizada em paralelo. 

![Configuração relicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Ao configurar **replicaSyncMode=2**, dependendo de quanto do cache precisa ser atualizado, a memória adicional pode ser consumida pelas réplicas de consulta. Para manter o banco de dados on-line e disponível para consultas, dependendo de quanto dos dados foram alterados, a operação pode exigir até *o dobro da memória* na réplica, pois tanto os segmentos antigo quanto o novo são mantidos na memória simultaneamente. Os nódulos de réplica têm a mesma alocação de memória que o nó principal, e normalmente há memória extra no nó principal para operações de atualização, por isso pode ser improvável que as réplicas fiquem sem memória. Além disso, o cenário comum é que o banco de dados é atualizado incrementalmente no nó primário e, portanto, a exigência de o dobro da memória deve ser incomum. Se a operação Sincronizar encontrar um erro fora da memória, ele tentará novamente usando a técnica padrão (anexar/desprender dois de cada vez). 

### <a name="separate-processing-from-query-pool"></a>Separe o processamento do pool de consulta

Para desempenho máximo para operações de processamento e consulta, você pode optar por separar seu servidor de processamento do pool de consulta. Quando separadas, novas conexões de cliente são atribuídas apenas às réplicas de consulta no pool de consultas. Se as operações de processamento ocuparem apenas um curto período de tempo, você poderá optar por separar seu servidor de processamento do pool de consulta apenas pelo tempo necessário para executar operações de processamento e sincronização e, em seguida, incluí-lo novamente no pool de consultas. Ao separar o servidor de processamento do pool de consultas ou adicioná-lo de volta ao pool de consultas pode levar até cinco minutos para que a operação seja concluída.

## <a name="monitor-qpu-usage"></a>Monitorar o uso de QPU

Para determinar se a escala para o servidor é necessária, [monitore seu servidor](analysis-services-monitor.md) no portal Azure usando Métricas. Se a QPU for maximizada regularmente, isso significa que o número de consultas em relação aos modelos está excedendo o limite de QPU do plano. A métrica de comprimento da fila do trabalho do pool de consulta também aumenta quando o número de consultas na fila do pool de thread de consulta excede a QPU disponível. 

Outra boa métrica para assistir é o QPU médio do ServerResourceType. Essa métrica compara qpu médio para o servidor principal com o pool de consulta. 

![Métricas de escala de consulta](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Para configurar qPU por ServerResourceType**

1. Em um gráfico de linha Métricas, clique **em Adicionar métrica**. 
2. Em **RESOURCE,** selecione seu servidor, em seguida, em **METRIC NAMESPACE,** selecione **Métricas padrão de Serviços de Análise,** em seguida, em **METRIC,** selecione **QPU**, e, em seguida, em **AGGREGAÇÃO,** selecione **Avg**. 
3. Clique **em Aplicar divisão**. 
4. Em **VALUES,** selecione **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Registro de diagnóstico detalhado

Use os logs do Monitor do Azure para diagnósticos mais detalhados dos recursos do servidor dimensionados. Com logs, você pode usar consultas do Log Analytics para quebrar QPU e memória por servidor e réplica. Para saber mais, consulte o exemplo de consultas no [registro de diagnósticos do Analysis Services](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Configurar a escala horizontal

### <a name="in-azure-portal"></a>No Portal do Azure

1. No portal, clique em **Escala - Out**. Use o controle deslizante para selecionar o número de servidores de réplica de consulta. O número de réplicas escolhido é além dos servidores existentes.  

2. Em **Separar o servidor de processamento do pool de consulta**, selecione Sim para excluir o servidor de processamento dos servidores de consulta. As [conexões do](#connections) cliente usando `:rw`a seqüência de conexões padrão (sem) são redirecionadas para réplicas no pool de consultas. 

   ![Controle deslizante da escala horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **Salvar** para provisionar os novos servidores de réplica de consulta. 

Ao configurar a escala para um servidor pela primeira vez, os modelos no servidor principal são sincronizados automaticamente com réplicas no pool de consultas. A sincronização automática só ocorre uma vez, quando você configura a escala para uma ou mais réplicas. Alterações subseqüentes no número de réplicas no mesmo servidor *não desencadearão outra sincronização automática*. A sincronização automática não ocorrerá novamente mesmo se você definir o servidor como zero réplicas e, em seguida, novamente dimensionar para qualquer número de réplicas. 

## <a name="synchronize"></a>Sincronizar 

As operações de sincronização devem ser realizadas manualmente ou usando a API REST.

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


|Código  |Descrição  |
|---------|---------|
|-1     |  Inválido       |
|0     | Replicating        |
|1     |  Rehydrating       |
|2     |   Concluído       |
|3     |   Falhou      |
|4     |    Finalizar     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de usar o PowerShell, [instale ou atualize o módulo PowerShell mais recente do Azure](/powershell/azure/install-az-ps). 

Para executar a sincronização, use [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Para definir o número de réplicas de consulta, use [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro `-ReadonlyReplicaCount` opcional.

Para separar o servidor de processamento do pool de consultas, use [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro opcional `-DefaultConnectionMode` a ser usado `Readonly`.

Para saber mais, [consulte Usando um diretor de serviço com o módulo Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>conexões

Na página Visão Geral do servidor, há dois nomes de servidor. Se você ainda não tiver configurado a escala horizontal para um servidor, os dois nomes de servidor funcionam da mesma forma. Depois de configurar a expansão para um servidor, você precisará especificar o nome do servidor adequado dependendo do tipo de conexão. 

Para conexões de cliente sé-lo, como Power BI Desktop, Excel e aplicativos personalizados, use **o nome do servidor**. 

Para SSMS, Visual Studio e strings de conexão em PowerShell, aplicativos azure function e AMO, use **o nome do servidor de gerenciamento**. O nome do servidor de gerenciamento inclui um qualificador especial `:rw` (leitura-gravação). Todas as operações de processamento ocorrem no servidor de gerenciamento (primário).

![nomes do servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Scale-up, Scale-down vs. Scale-out

Você pode alterar o nível de preços em um servidor com várias réplicas. O mesmo nível de preços se aplica a todas as réplicas. Uma operação em escala primeiro derrubará todas as réplicas de uma só vez e, em seguida, trará todas as réplicas no novo nível de preços.

## <a name="troubleshoot"></a>Solução de problemas

**Problema:** usuários obtêm o erro **não é possível localizar o servidor '\<nome do servidor >' instância no modo de conexão 'ReadOnly'.**

**Solução:** Ao selecionar o **Servidor De separação do servidor de processamento da** opção pool `:rw`de consultas, as conexões do cliente usando a seqüência de conexões padrão (sem) são redirecionadas para réplicas do pool de consultas. Se as réplicas no pool de consulta estiverem ainda online porque a sincronização ainda não foi concluída, as conexões de cliente redirecionada podem falhar. Para evitar conexões com falha, deve haver, pelo menos, dois servidores no pool de consulta ao executar uma sincronização. Cada servidor é sincronizado individualmente, enquanto os outros permanecem online. Se você optar por não ter o servidor de processamento no pool de consulta durante o processamento, você poderá optar por removê-lo do pool para processamento e, em seguida, adicioná-lo novamente ao pool após a conclusão do processamento, mas antes da sincronização. Use as métricas Memória e QPU para monitorar o status de sincronização.



## <a name="related-information"></a>Informações relacionadas

[Monitorar métricas do servidor](analysis-services-monitor.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md) 
