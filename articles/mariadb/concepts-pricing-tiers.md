---
title: 'Tipos de preço: Banco de Dados do Azure para MariaDB'
description: Conheça os vários níveis de preços do Azure Database para MariaDB, incluindo gerações de cálculo, tipos de armazenamento, tamanho de armazenamento, vCores, memória e períodos de retenção de backup.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 39af6850810fa471003cea27ed274972fb2ff046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528023"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Banco de dados do Azure para níveis de preços do MariaDB

Você pode criar um Banco de Dados do Azure para o servidor MariaDB em um dos três níveis de preços diferentes: Básico, Uso Geral e Memória Otimizada. Os tipos de preço são diferenciados pela quantidade de computação nos vCores que pode ser provisionada, pela memória por vCore e pela tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor MariaDB. Um servidor pode ter um ou vários bancos de dados.

|    | **Basic** | **Propósito Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Gen 5 |Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Período de retenção do backup de banco de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um tipo de preço, use a tabela a seguir como ponto de partida.

| Tipo de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Basic | Cargas de trabalho que exigem desempenho de E/S e computação leve. Os exemplos incluem servidores usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| Uso Geral | A maioria das cargas de trabalho que exigem a computação e a memória balanceadas com a taxa de transferência de E/S escalonável. Os exemplos incluem servidores para hospedar aplicativos Web e móveis e outros aplicativos empresariais.|
| Otimizado para memória | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para o processamento de transações mais rápido e com simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, o número de vCores e a camada de preços (exceto para e do Basic) podem ser alterados para cima ou para baixo em segundos. Você pode também, independentemente, ajustar a quantidade de armazenamento de backup e o período de retenção de backup para cima ou para baixo sem tempo de inatividade do aplicativo. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. Para obter mais informações, consulte a seção [De recursos de escala.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Gerações de computação e vCores

Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. As CPUs lógicas de 5ª geração são baseadas em processadores E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Armazenamento

O armazenamento que você provisiona é a quantidade de capacidade de armazenamento disponível para seu banco de dados do Azure para o servidor MariaDB. O armazenamento é usado para os arquivos de banco de dados, arquivos temporários, logs de transações e os logs do servidor MariaDB. A quantidade total de armazenamento que você provisiona também define a capacidade disponível para o servidor.

|    | **Basic** | **Propósito Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento básico | Armazenamento para propósitos gerais | Armazenamento para propósitos gerais |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Mín 100 IOPS<br/>Máx 6000 IOPS | 3 IOPS/GB<br/>Mín 100 IOPS<br/>Máx 6000 IOPS |

Você pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor e permitir que o sistema cresça o armazenamento automaticamente com base no consumo de armazenamento de sua carga de trabalho.

>[!NOTE]
> O armazenamento só pode ser ampliado, não para baixo.

A camada Básico não oferece garantia de IOPS. Nos tipos de preço Uso Geral e Otimizado para Memória, o IOPS é dimensionado com o tamanho de armazenamento provisionado a uma taxa de 3:1.

Você pode monitorar o consumo de E/S no Portal do Azure ou usando os comandos da CLI do Azure. As métricas relevantes para monitorar são [limite de armazenamento, porcentagem de armazenamento, armazenamento usado e percentual de IO](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Servidores com menos de 100 GB de armazenamento provisionado são marcados somente de leitura se o armazenamento gratuito for inferior a 5% do tamanho de armazenamento provisionado. Os servidores com mais de 100 GB de armazenamento provisionado serão marcados como somente leitura quando o armazenamento livre for inferior a 5 GB.

Por exemplo, se você tiver provisionado 110 GB de armazenamento e a utilização real for superior a 105 GB, o servidor será marcado somente com leitura. Alternativamente, se você tiver provisionado 5 GB de armazenamento, o servidor será marcado somente com leitura quando o armazenamento gratuito atingir menos de 256 MB.

Enquanto o serviço tenta tornar o servidor somente leitura, todas as novas solicitações de transação de gravação são bloqueadas e as transações ativas existentes continuarão a ser executadas. Quando o servidor é definido como somente leitura, todas as operações de gravação subsequente e a transação comentem falha. Consultas de leitura continuam a funcionar sem interrupções. Após aumentar o armazenamento provisionado, o servidor estará pronto para aceitar transações de gravação novamente.

Recomendamos que você ative o crescimento automático do armazenamento ou para configurar um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do limite para que você possa evitar entrar no estado somente leitura. Para mais informações, consulte a documentação em [como configurar um alerta](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Crescimento automático de armazenamento

O crescimento automático do armazenamento impede que o servidor fique sem armazenamento e se torne somente leitura. Se o crescimento automático do armazenamento for ativado, o armazenamento crescerá automaticamente sem afetar a carga de trabalho. Para servidores com menos de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado é aumentado em 5 GB quando o armazenamento gratuito estiver abaixo de 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento livre está abaixo de 10 GB do tamanho de armazenamento provisionado. Os limites máximos de armazenamento conforme especificado acima se aplicam.

Por exemplo, se você tiver provisionado 1000 GB de armazenamento, e a utilização real for superior a 990 GB, o tamanho do armazenamento do servidor é aumentado para 1050 GB. Alternativamente, se você tiver provisionado 10 GB de armazenamento, o tamanho do armazenamento é aumentar para 15 GB quando menos de 1 GB de armazenamento é gratuito.

Lembre-se que o armazenamento só pode ser ampliado, não para baixo.

## <a name="backup"></a>Backup

O serviço faz backups do servidor automaticamente. Você pode selecionar um período de retenção de um intervalo de 7 a 35 dias. Os servidores otimizados para propósito geral e memória podem optar por ter armazenamento geo-redundante para backups. Saiba mais sobre backups no [artigo de conceitos](concepts-backup.md).

## <a name="scale-resources"></a>Recursos de escala

Após criar o servidor, você poderá alterar separadamente os vCores, a camada de preços (exceto para e para Básico), a quantidade de armazenamento e o período de retenção de backup. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. O número de vCores pode ser dimensionado para cima ou para baixo. Os vCores e o período de retenção de backup podem ser aumentados ou reduzidos de 7 a 35 dias. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou da CLI do Azure. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Ao alterar o número de vCores ou a camada de preços, uma cópia do servidor original será criada com a nova alocação de computação. Depois que o novo servidor entra em execução, as conexões são alternadas para o novo servidor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Esse período varia, mas na maioria dos casos fica abaixo um minuto.

O dimensionamento do armazenamento e a alteração do período de retenção de backup são operações realmente online. Não há nenhum tempo de inatividade e o aplicativo não é afetado. Conforme o IOPS é dimensionado com o tamanho do armazenamento provisionado, você pode aumentar o IOPS disponível para seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/mariadb/) do serviço. Para ver os custos da configuração desejada, o [Portal do Azure](https://portal.azure.com/#create/Microsoft.MariaDBServer) mostra o custo mensal na guia **Tipo de preço** com base nas opções que você seleciona. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), selecione **Adicionar itens**, expanda a categoria **Bancos de dados** e escolha **Banco de Dados do Azure para MariaDB** para personalizar as opções.

## <a name="next-steps"></a>Próximas etapas
- Conheça as [limitações](concepts-limits.md)de serviço .
- Saiba como [criar um servidor do MariaDB no portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
