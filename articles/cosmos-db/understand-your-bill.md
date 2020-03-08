---
title: Entendendo sua cobrança do Azure Cosmos DB
description: Este artigo ajuda você a entender sua fatura do Azure Cosmos DB com alguns exemplos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 74a4279d347be92b1047a9cf361e233ecc7fcff8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674313"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Entendendo sua fatura do Azure Cosmos DB

Como um serviço de banco de dados nativo de nuvem totalmente gerenciado, o Azure Cosmos DB simplifica a cobrança cobrando apenas a taxa de transferência provisionada e o armazenamento consumido. Não há taxas de licença adicionais, hardware, custos do utilitário ou custos de instalações em comparação com alternativas locais ou de IaaS. Quando você considera os recursos de várias regiões de Azure Cosmos DB, o serviço de banco de dados fornece uma redução significativa nos custos em comparação com as soluções locais ou IaaS existentes.

Com o Azure Cosmos DB, você será cobrado por hora com base na taxa de transferência provisionada e no armazenamento consumido. Para a taxa de transferência provisionada, a unidade de cobrança é 100 RU/s por hora, cobrada a US$ 0,008 por hora, levando em conta o preço público padrão; consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/). Para o armazenamento consumido, você é cobrado US$ 0,25 por 1 GB de armazenamento por mês. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Este artigo usa alguns exemplos para ajudá-lo a entender os detalhes encontrados na fatura mensal. Os números mostrados nos exemplos podem ser diferentes se seus contêineres do Azure Cosmos têm uma taxa de transferência provisionada diferente, se eles abrangem várias regiões ou são executados em regiões diferentes por algum tempo durante o mês.

> [!NOTE]
> A cobrança é para qualquer parte de uma hora do relógio de parede, não uma duração de 60 minutos.

## <a name="billing-examples"></a>Exemplos de cobrança

### <a name="billing-example---throughput-on-a-container-full-month"></a>Exemplo de cobrança: taxa de transferência em um contêiner (mês completo)

* Vamos supor que você configurou uma taxa de transferência de 1.000 RU/s em um contêiner e ele existe por 24 horas * 30 dias do mês = 720 horas no total.  

* 1\.000 RU/s representa 10 unidades de 100 RU/s por hora para cada hora de existência do contêiner (ou seja, 1.000/100 = 10). 

* Multiplicando 10 unidades por hora ao custo de US$ 0,008 (por 100 RU/s por hora) = US$ 0,08 por hora. 

* Multiplicando US$ 0,08 por hora pelo número de horas do mês, dá US$ 0,08 * 24 horas * 30 dias = US$ 57,60 por mês.  

* O total de sua fatura mensal mostrará 7.200 unidades (de 100 RUs), o que custará US$ 57,60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Exemplo de cobrança: taxa de transferência em um contêiner (mês parcial)

* Vamos supor que criamos um contêiner com taxa de transferência provisionada de 2.500 RU/s. O contêiner existe por 24 horas ao longo do mês (por exemplo, podemos excluí-lo 24 horas depois de o criarmos).  

* Em seguida, vamos ver 600 unidades na fatura (2.500 RU/s / 100 RU/s/unidade * 24 horas). O custo será de US$ 4,80 (600 unidades * US$ 0,008/unidade).

* A fatura total para o mês será de US$ 4,80.

### <a name="billing-rate-if-storage-size-changes"></a>Taxa de cobrança em caso de mudança do tamanho de armazenamento

A capacidade de armazenamento é cobrada em unidades da quantidade máxima por hora de dados armazenados (em GB) durante um período de um mês. Por exemplo, se você usar 100 GB de armazenamento na primeira metade do mês e 50 GB na segunda metade, será cobrado pelo equivalente a 75 GB de armazenamento durante o mês em questão.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Taxa de cobrança quando o contêiner ou um conjunto de contêineres fica ativo por menos de uma hora

Será cobrada a taxa fixa por hora de existência do contêiner ou do banco de dados, independentemente do uso ou de o contêiner ou banco de dados ficar ativo por menos de uma hora. Por exemplo, se você criar um contêiner ou banco de dados e o excluir depois de 5 minutos, sua fatura incluirá uma hora.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Taxa de cobrança quando a taxa de transferência em um contêiner ou banco de dados é aumentada ou reduzida

Se às 9h30 você aumentar a taxa de transferência provisionada de 400 RU/s para 1.000 RU/s e voltar a diminuí-la às 10h45 para 400 RU/s, será cobrado por duas horas de 1.000 RU/s. 

Se você aumentar a taxa de transferência provisionada para um contêiner ou um conjunto de contêineres às 9h30 de 100 mil RU/s para 200 mil RU/s e, em seguida, reduzi-la às 10h45 para 100 mil RU/s, será cobrado por duas horas de 200 mil RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Exemplo de cobrança: vários contêineres, cada um com modo de taxa de transferência provisionada dedicada

* Se você criar no Leste dos EUA 2 uma conta do Azure Cosmos com dois contêineres com a taxa de transferência provisionada de 500 RU/s e 700 RU/s, respectivamente, terá uma taxa de transferência total de 1.200 RU/s.  

* Você será cobrado 1.200/100 * US$ 0,008 = US$ 0,096/hora. 

* Se suas necessidades de taxa de transferência forem alteradas e você aumentar a capacidade de cada contêiner em 500 RU/s e ainda criar um novo contêiner ilimitado com 20.000 RU/s, sua capacidade provisionada geral será de 22.200 RU/s (1.000 RU/s + 1.200 RU/s + 20.000 RU/s).  

* Nesse caso, sua fatura será alterada para: US$ 0,008  x 222 =  US$ 1.776/hora. 

* Em um mês de 720 horas (24 horas * 30 dias), se for 500 horas, a taxa de transferência provisionada foi 1.200 RU/seg e, para a taxa de transferência provisionada restante de 220 horas, foi de 22.200 RU/seg, sua fatura mensal mostrará: 500 x $0.096/hora + 220 x $1.776/hora = $438.72/mês.

![Exemplo de cobrança da taxa de transferência dedicada](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Exemplo de cobrança: contêineres com modo de taxa de transferência compartilhada

* Se você criar uma conta do Azure Cosmos no Leste dos EUA 2 com dois bancos de dados Azure Cosmos (com um conjunto de contêineres que compartilham a taxa de transferência no nível do banco de dados) e taxas de transferência provisionadas de 50 mil RU/s e 70 mil RU/s, respectivamente, terá um total de taxa de transferência provisionada de 120 mil RU/s.  

* Você será cobrado 1.200 x US$ 0,008 = US$ 9,60 por hora. 

* Se suas necessidades de taxa de transferência forem alteradas e você aumentar a taxa de transferência provisionada de cada banco de dados em 10 mil RU/s para cada um, e adicionar um novo contêiner ao primeiro banco de dados com o modo de taxa de transferência dedicada de 15 mil RU/s para o banco de dados com taxa de transferência compartilhada, a capacidade provisionada geral será de 155 mil RU/s (60 mil RU/s + 80 mil RU/s + 15 mil RU/s).  

* Em seguida, sua fatura mudaria para: 1.550 * $0.08 = $12.40/hora.  

* Em um mês de 720 horas, se for 300 horas, a taxa de transferência provisionada foi 120-K RU/seg e, para a taxa de transferência provisionada restante de 420 horas, foi 155-K RU/seg, sua fatura mensal mostrará: 300 x $9,60/hora + 420 x $12.40/hora = $2880 + $5208 = $8088/mês. 

![Exemplo de cobrança da taxa de transferência compartilhada](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Exemplos de cobrança com replicação geográfica e de vários mestres  

Você pode adicionar/remover regiões do Azure em qualquer lugar do mundo para sua conta de banco de dados Cosmos do Azure a qualquer momento. A taxa de transferência que você configurou para vários contêineres e bancos de dados Cosmos do Azure será reservada em cada uma das regiões do Azure associadas à sua conta de banco de dados Cosmos do Azure. Se a soma das taxas de transferência provisionadas (RU/s) configuradas em todos os bancos de dados e contêineres dentro da conta do banco de dados Azure Cosmos (provisionadas por hora) for T e o número de regiões do Azure associadas à conta do banco de dados for N, o total da taxa de transferência provisionada para qualquer hora, para sua conta de banco de dados, (a) configurada com uma única região de gravação será igual a T x N RU/s e (b) configurada com todas as regiões sendo capazes de processar gravações será igual a T x (N + 1) RU/s, respectivamente. A taxa de transferência provisionada (região de gravação única) custa US$ 0,008 por hora por 100 RU/s e a taxa de transferência provisionada com várias regiões graváveis (configuração de vários mestres) custa US$ 0,016 / por hora por 100 RU/s (consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)). Seja com região de gravação única ou várias regiões de gravação, o Azure Cosmos DB permite a leitura de dados de qualquer região.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Exemplo de cobrança: conta do Azure Cosmos multirregiões, com gravação em uma única região

Vamos supor que você tenha um contêiner do Azure Cosmos no Oeste dos EUA. O contêiner é criado com uma taxa de transferência de 10K RU/s e você armazenou 1 TB de dados este mês. Vamos supor que você adicionou três regiões (Leste dos EUA, Norte da Europa e Leste da Ásia) à conta do Azure Cosmos, cada uma com o mesmo armazenamento e a mesma taxa de transferência. O valor total da sua fatura mensal será (considerando 30 dias em um mês). Sua fatura ficaria da seguinte maneira: 

|**Item** |**Uso (por mês)** |**Tarifa** |**Custo mensal** |
|---------|---------|---------|-------|
|Cobrança da taxa de transferência para o contêiner no Oeste dos EUA      | 10 mil RU/s * 24 * 30    |US$ 0,008 por 100 RU/s por hora   |US$ 576|
|Cobrança da taxa de transferência para três regiões adicionais: Leste dos EUA, Norte da Europa e Leste da Ásia       | 3 * 10 mil RU/s * 24 * 30    |US$ 0,008 por 100 RU/s por hora  |US$ 1.728|
|Cobrança de armazenamento para o contêiner no Oeste dos EUA      | 250 GB    |US$ 0,25/GB  |US$ 62,50|
|Cobrança de armazenamento para três regiões adicionais – Leste dos EUA, Norte da Europa e Leste da Ásia      | 3 * 250 GB    |US$ 0,25/GB  |US$ 187,50|
|**Total**     |     |  |**US$ 2.554**|

*Vamos supor também que você egresso 100 GB de dados todos os meses do contêiner no oeste dos EUA para replicar dados no leste dos EUA, Europa Setentrional e Ásia Oriental. Você será cobrado pela saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Exemplo de cobrança: conta do Azure Cosmos multirregiões, com gravação em várias regiões

Vamos supor que você criou um contêiner do Azure Cosmos no Oeste dos EUA. O contêiner é criado com uma taxa de transferência de 10K RU/s e você armazenou 1 TB de dados este mês. Vamos supor que você adicionou três regiões (Leste dos EUA, Norte da Europa e Leste da Ásia), cada uma com o mesmo armazenamento e com a mesma taxa de transferência, e deseja ter capacidade de gravação para os contêineres em todas as regiões associadas à conta do Azure Cosmos. O valor total da sua fatura mensal será (considerando 30 dias em um mês) o seguinte:

|**Item** |**Uso (por mês)**|**Tarifa** |**Custo mensal** |
|---------|---------|---------|-------|
|Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)       | 10 mil RU/s * 24 * 30    |US$ 0,016 por 100 RU/s por hora    |US$ 1.152 |
|Cobrança da taxa de transferência para três regiões adicionais: Leste dos EUA, Norte da Europa e Leste da Ásia (todas as regiões são graváveis)        | (3 + 1) * 10 mil RU/s * 24 * 30    |US$ 0,016 por 100 RU/s por hora   |US$ 4.608 |
|Cobrança de armazenamento para o contêiner no Oeste dos EUA      | 250 GB    |US$ 0,25/GB  |US$ 62,50|
|Cobrança de armazenamento para três regiões adicionais – Leste dos EUA, Norte da Europa e Leste da Ásia      | 3 * 250 GB    |US$ 0,25/GB  |US$ 187,50|
|**Total**     |     |  |**US$ 6.010**|

*Vamos supor também que você egresso 100 GB de dados todos os meses do contêiner no oeste dos EUA para replicar dados no leste dos EUA, Europa Setentrional e Ásia Oriental. Você será cobrado pela saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Exemplo de cobrança: conta do Azure cosmos com a taxa de transferência de nível de banco de dados de vários mestres, incluindo modo de taxa de transferência dedicada para alguns contêineres

Vamos considerar o exemplo a seguir, onde temos uma conta do Azure Cosmos multirregiões em que todas as regiões são graváveis (configuração de vários mestres). Vamos pressupor que o tamanho de armazenamento permanece constante e não é alterado, e vamos omiti-lo aqui para simplificar a explicação. A taxa de transferência durante o mês variou da seguinte maneira (supondo-se 30 dias ou 720 horas): 

[0 a 100 horas]:  

* Criamos uma conta do Azure Cosmos com três regiões (Oeste dos EUA, Leste dos EUA, Norte da Europa), em que todas as regiões são graváveis 

* Criamos um banco de dados (D1) com taxa de transferência compartilhada de 10 mil RU/s 

* Criamos um banco de dados (D2) com taxa de transferência compartilhada de 30 mil RU/s e  

* Criamos um contêiner (C1) com taxa de transferência dedicada de 20 mil RU/s 

[101 a 200 horas]:  

* Expandimos o banco de dados (D1) para 50 mil RU/s 

* Expandimos o banco de dados (D2) para 70 mil RU/s  

* Excluímos o contêiner (C1)  

[201 a 300 horas]:  

* Criamos o contêiner (C1) novamente com taxa de transferência dedicada de 20 mil RU/s 

[301 a 400 horas]:  

* Removemos uma das regiões da conta do Azure Cosmos (o número de regiões graváveis agora é 2) 

* Reduzimos verticalmente o banco de dados (D1) para 10 mil RU/s 

* Expandimos o banco de dados (D2) para 80 mil RU/s  

* Excluímos o contêiner (C1) novamente 

[401 a 500 horas]:  

* Reduzimos verticalmente o banco de dados (D2) para 10 mil RU/s  

* Criamos o contêiner (C1) novamente com taxa de transferência dedicada de 20 mil RU/s 

[501 a 700 horas]:  

* Expandimos o banco de dados (D1) para 20 mil RU/s  

* Expandimos o banco de dados (D2) para 100 mil RU/s  

* Excluímos o contêiner (C1) novamente  

[701 a 720 horas]:  

* Reduzimos verticalmente o banco de dados (D2) para 50 mil RU/s  

Visualmente, as alterações na taxa de transferência provisionada total durante 720 horas no mês são mostradas na figura abaixo: 

![Exemplo da vida real](./media/understand-your-bill/bill-example3.png)

A fatura mensal total (supondo 30 dias/720 horas em um mês) será calculada da seguinte maneira:

|**Horas**  |**RU/s** |**Item** |**Uso (por hora)** |**Custo** |
|---------|---------|---------|-------|-------|
|[0 a 100] |D1:10 mil <br/>D2:30 mil <br/>C1:20 mil |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |US$ 960  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |US$ 2.880  |
|[101 a 200] |D1:50 mil <br/>D2:70 mil <br/>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |US$ 1.920  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |US$ 5.760  |
|[201 a 300]  |D1:50 mil <br/>D2:70 mil <br/>C1:20 mil |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2,240  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |US$ 6.720 |
|[301 a 400] |D1:10 mil <br/>D2:80 mil <br/>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |US$ 1.440   |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |US$ 2.880  |
|[401 a 500] |D1:10 mil <br>D2:10 mil <br>C1:20 mil |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |US$ 640  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |US$ 1.280  |
|[501 a 700] |D1:20 mil <br>D2:100 mil <br>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |US$ 3.840  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |US$ 7.680  |
|[701 a 720] |D1:20 mil <br/>D2:50 mil <br/>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |U$ 224  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |U$ 224  |
|| |**Custo mensal total**  | |**US$ 38.688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Exemplos de cobrança com contas de camada gratuita
Com Azure Cosmos DB camada gratuita, você obterá os primeiros 400 RU/s e 5 GB de armazenamento em sua conta gratuitamente, aplicados no nível da conta. Qualquer RU/s e armazenamento além de 400 RU/s e 5 GB serão cobrados com as tarifas de preços regulares por página de preços. Na fatura, você não verá um item de cobrança ou de linha para os 400 ru/s gratuitos e 5 GB, somente o RU/s e o armazenamento além do que é coberto pela camada gratuita. O 400 RU/s se aplica a qualquer tipo de taxa de transferência de RU/s-provisionada, AutoPilot (versão prévia) e vários mestres.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Exemplo de cobrança-contêiner ou banco de dados com taxa de transferência provisionada
- Vamos supor que criamos um banco de dados ou contêiner em uma conta de camada gratuita com 400 RU/s e 5 GB de armazenamento.
- Sua fatura não mostrará nenhuma cobrança para esse recurso. Seu custo por hora e mensal será de $0.
- Agora, vamos supor que, na mesma conta, adicionamos outro banco de dados ou contêiner com 1000 RU/s e 10 GB de armazenamento.
- Agora, sua fatura mostrará um encargo para 1000 RU/s e 10 GB de armazenamento. 

### <a name="billing-example---container-or-database-with-autopilot-throughput-preview"></a>Exemplo de cobrança-contêiner ou banco de dados com taxa de transferência do AutoPilot (versão prévia)
- Vamos supor que em uma conta de camada gratuita, criamos um banco de dados ou contêiner com o piloto automático habilitado, com um máximo de RU/s de 4000 RU/s. Este recurso será dimensionado automaticamente entre 400 RU/s-4000 RU/s. 
- Suponha que, na hora 1 até a hora 10, o recurso esteja no mínimo de 400 RU/s. Durante 11 de hora, o recurso escala até 1000 RU/s e, em seguida, volta a 400 RU/s dentro da hora.
- Nas horas 1 a 10, você será cobrado $0 para taxa de transferência, pois as 400 RU/s foram cobertas pela camada gratuita. 
- Na hora 11, você será cobrado por um efetivo de 1000 RU/s-400 RU/s = 600 RU/s, pois esse é o mais alto RU/s na hora. Essas serão 6 unidades de 100 RU/s para a hora, portanto, o custo total de taxa de transferência para a hora será de 6 unidades * $0.12 = $0.72. 
- Qualquer armazenamento além dos primeiros 5 GB será cobrado em taxas de armazenamento normais. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Exemplo de cobrança-várias regiões, conta de região de gravação única
- Vamos supor que, em uma conta de camada gratuita, criamos um banco de dados ou contêiner com 1200 RU/s e 10 GB de armazenamento. Nós replicamos a conta para três regiões e temos uma única conta de mestre (única região de gravação).
- No total, sem a camada gratuita, seria cobrado por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de camada gratuita, após a remoção de 400 RU/s e 5 GB de armazenamento, será cobrado um valor de 3200 RU/s (unidades 32) em vigor de taxa de transferência provisionada na taxa de região de gravação única e 25 GB de armazenamento.
- O custo mensal para RU/s seria: 32 unidades * $0.08 * 24 horas * 31 dias = $190.46. O custo mensal para o armazenamento seria: 25 GB * 0,25/GB = $6.25. O custo total seria $190.46 + $6.25 = $196.71.
- Observação: se o preço unitário para RU/s ou armazenamento for diferente nas regiões, a camada gratuita 400 RU/s e 5 GB refletirá as taxas da conta em que a região foi criada.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Exemplo de cobrança – conta de várias regiões, vários mestres (várias regiões de gravação)

Este exemplo reflete os [preços de vários mestres](https://azure.microsoft.com/pricing/details/cosmos-db/) para contas criadas após 1º de dezembro de 2019. 
- Vamos supor que, em uma conta de camada gratuita, criamos um banco de dados ou contêiner com 1200 RU/s e 10 GB de armazenamento. Nós replicamos a conta para três regiões e temos uma conta de vários mestres (várias regiões de gravação). 
- No total, sem a camada gratuita, seria cobrado por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de camada gratuita, depois de remover 400 RU/s e 5 GB de armazenamento, será cobrado por uma 3200 RU/s (unidades 32) em vigor de taxa de transferência provisionada na taxa de várias regiões de gravação e 25 GB de armazenamento.
- O custo mensal para RU/s seria: 32 unidades * $0.16 * 24 horas * 31 dias = $380.93. O custo mensal para o armazenamento seria: 25 GB * 0,25/GB = $6.25. O custo total seria $380.93 + $6.25 = $387.18.

## <a name="proactively-estimating-your-monthly-bill"></a>Estimando proativamente sua fatura mensal  

Vamos considerar outro exemplo, em que você deseja proativamente estimar sua fatura antes do final do mês. É possível estimar sua fatura da seguinte maneira:

|**Custo de armazenamento** | |
|----|----|
|Tamanho médio dos registros (KB) |1 |
|Número de registros  |100.000.000  |
|Armazenamento total (GB)  |100 |
|Custo mensal por GB  |US$ 0,25  |
|Custo mensal esperado pelo armazenamento   |US$ 25,00  |

<br>

|**Custo de taxa de transferência** | | | |
|----|----|----|----|
|Tipo de operação| Solicitações/s| Média de RU/solicitação| RUs necessárias|
|Gravar| 100 | 5 | 500|
|Ler| 400| 1| 400|

Total de RU/s: 500 + 400 = 900 custo por hora: 900/100 * $0.08 = $0.72 custo mensal esperado para taxa de transferência (supondo 31 dias): $0.72 * 24 * 31 = $53.57

**Custo mensal total**

Custo mensal total = custo mensal pelo armazenamento + custo mensal pela taxa de transferência Custo mensal total = US$ 25,00 + US$ 53,57 = US$ 78,57

*Os preços podem variar por região. Para obter os preços atualizados, consulte a página de [preços](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Cobrança com capacidade reservada do Azure Cosmos DB

Azure Cosmos DB capacidade reservada permite que você compre a taxa de transferência provisionada antecipadamente (uma capacidade reservada ou uma reserva) que pode ser aplicada a todos os bancos de dados e contêineres do Azure Cosmos (para qualquer API ou modelo de dado) em todas as regiões do Azure. Visto que o preço da taxa de transferência provisionada varia de acordo com a região, vale pensar na capacidade reservada como um crédito monetário adquirido com desconto, que pode ser utilizado na taxa de transferência provisionada pelo preço respectivo de cada região. Por exemplo, digamos que você tenha uma conta do Azure Cosmos com um único contêiner provisionado com 50 mil RU/s e replicou globalmente duas regiões: Leste dos EUA e Leste do Japão. Se você escolher a opção de pagamento conforme o uso, pagará:  

* No Leste dos EUA: por 50 mil RU/s à taxa de US$ 0,008 por 100 RU/s nessa região 

* No leste do Japão: por 50 mil RU/s à taxa de US$ 0,009 por 100 RU/s nessa região

Sua fatura total (sem capacidade reservada) seria (supondo-se 30 dias ou 720 horas): 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**|**Valor cobrado (por hora)**| **Valor cobrado (por mês)**|
|----|----|----|----|----|
|Leste dos EUA|US$ 0,008 |50 mil|US$ 4|US$ 2.880 |
|Leste do Japão|US$ 0,009 |50 mil| US$ 4,50 |US$ 3.240 |
|Total|||US$ 8,50|US$ 6.120 |

Vamos supor que você tenha comprado capacidade reservada. Você pode comprar capacidade reservada para 100 mil RU/s ao preço de US$ 56.064 por um ano (com 20% de desconto), ou US$ 6,40 por hora. Consulte os preços de capacidade reservada na [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Custo de taxa de transferência (pago conforme o uso): 100.000 RU/s/100 * $0.008/hora * 8760 horas em um ano = $70080 

* Custo de taxa de transferência (com capacidade reservada) US$ 70.080 com desconto de 20% = US$ 56.064 

O que você efetivamente comprou é um crédito de US$ 8 por hora, para 100 mil RU/s usando o preço de lista no Leste dos EUA, ao preço de US$ 6,40 por hora. Você pode consumir essa reserva pré-paga de taxa de transferência por hora na capacidade da taxa de transferência provisionada em qualquer região global do Azure de acordo com os respectivos valores da lista regional definidos para sua assinatura. Neste exemplo, em que você provisiona 50.000 RU/s cada no Leste dos EUA e no Leste do Japão, poderá consumir US$ 8,00 de taxa de transferência provisionada por hora e será cobrado pelo excedente de US$ 0,50 por hora (ou US$ 360 por mês). 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**| **Valor cobrado (por hora)**| **Valor cobrado (por mês)**|
|----|----|----|----|----|
|Leste dos EUA|US$ 0,008 |50 mil|US$ 4|US$ 2.880 |
|Leste do Japão|US$ 0,009 |50 mil| US$ 4,50 |US$ 3.240 |
|||Pago conforme o uso|US$ 8,50|US$ 6.120|
|Capacidade reservada comprada|US$ 0,0064 (20% de desconto) |Capacidade de 100 RU/s ou US$ 8 pré-adquirida |-US$ 8|-US$ 5.760 |
|Valor líquido|||US$ 0,50 |US$ 360 |

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Como o modelo de preços do Cosmos DB é econômico para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)
