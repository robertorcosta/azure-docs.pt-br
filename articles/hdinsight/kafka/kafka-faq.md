---
title: FAQ sobre Apache Kafka no Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre apache Kafka no Azure HDInsight, um serviço gerenciado de nuvem Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: ff4079263fd7afb02e132a798997687fad7e9961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206972"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Perguntas frequentes sobre Apache Kafka no Azure HDInsight

Este artigo aborda algumas perguntas comuns sobre o uso do Apache Kafka no Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Quais versões kafka são suportadas pelo HDInsight?

Saiba mais informações sobre as versões de componentes suportadas oficialmente pelo HDInsight em [Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions) Recomendamos sempre usar a versão mais recente para garantir o melhor desempenho possível e a experiência do usuário.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Quais recursos são fornecidos em um cluster HDInsight Kafka e para quais recursos sou cobrado?

Um cluster HDInsight Kafka inclui os seguintes recursos:

* Nós de cabeçalho
* Nós do Zookeeper
* Nódulos corretores (trabalhadores) 
* Discos gerenciados do Azure anexados aos nós de corretor
* Nós de gateway

Todos esses recursos são cobrados com base no nosso [modelo de preços HDInsight,](https://azure.microsoft.com/pricing/details/hdinsight/)exceto nos nós de gateway. Você não é cobrado por nós de gateway.

Para obter uma descrição mais detalhada de vários tipos de nós, consulte [a arquitetura de rede virtual Azure HDInsight](../hdinsight-virtual-network-architecture.md). Os preços são baseados no uso por nó de minuto. Os preços variam dependendo do tamanho do nó, número de nós, tipo de disco gerenciado usado e região.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>As APIs Apache Kafka funcionam com o HDInsight?

Sim, o HDInsight usa APIs kafka nativas. O código do aplicativo do cliente não precisa mudar. Veja [tutorial: Use as APIs do Produtor e Consumidor Apache Kafka](./apache-kafka-producer-consumer-api.md) para ver como você pode usar APIs de produtor/consumidor baseadas em Java com seu cluster.

## <a name="can-i-change-cluster-configurations"></a>Posso alterar as configurações do cluster?

Sim, através do portal Ambari. Cada componente no portal tem uma seção **de configurações,** que pode ser usada para alterar as configurações dos componentes. Algumas alterações podem exigir reinicialização do corretor.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Que tipo de autenticação o HDInsight suporta para Apache Kafka?

Usando [o Enterprise Security Package (ESP),](../domain-joined/apache-domain-joined-architecture.md)você pode obter segurança no nível de tópico para seus clusters Kafka. Veja [tutorial: Configure as políticas apache kafka no HDInsight com o Enterprise Security Package (Preview)](../domain-joined/apache-domain-joined-run-kafka.md), para obter mais informações.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Meus dados estão criptografados? Posso usar minhas próprias chaves?

Todas as mensagens Kafka nos discos gerenciados são criptografadas com [o Azure Storage Service Encryption (SSE).](../../storage/common/storage-service-encryption.md) Os dados em trânsito (por exemplo, os dados que estão sendo transmitidos dos clientes para os corretores e o contrário) não são criptografados por padrão. É possível criptografar esse tráfego [configurando o SSL por conta própria.](./apache-kafka-ssl-encryption-authentication.md) Além disso, o HDInsight permite que você gerencie suas próprias chaves para criptografar os dados em repouso. Consulte [a criptografia de disco de chave gerenciada pelo cliente](../disk-encryption.md)para obter mais informações.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Como conectar clientes ao meu cluster?

Para que os clientes kafka se comuniquem com os corretores kafka, eles devem ser capazes de chegar aos corretores pela rede. Para clusters HDInsight, a Rede Virtual (VNet) é o limite de segurança. Assim, a maneira mais fácil de conectar clientes ao seu cluster HDInsight é criar clientes dentro do mesmo VNet que o cluster. Outros cenários incluem:

* Conectando clientes em um Azure VNet diferente – Pesquise o cluster VNet e o vnet cliente e configure o cluster para [publicidade IP](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Ao usar publicidade IP, os clientes Kafka devem usar endereços IP do Broker para se conectar com os corretores, em vez de Nomes de Domínio Totalmente Qualificados (FQDNs).

* Conectando clientes locais – Usando uma rede VPN e configurando servidores DNS personalizados conforme descrito no [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Criando um ponto final público para o seu serviço Kafka – Se os requisitos de segurança da empresa permitirem, você pode implantar um ponto final público para seus corretores Kafka ou um ponto final DE RÉ de código aberto autogerenciado com um ponto final público.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Posso adicionar mais espaço em disco em um cluster existente?

Para aumentar a quantidade de espaço disponível para mensagens Kafka, você pode aumentar o número de nódulos. Atualmente, a adição de mais discos a um cluster existente não é suportada.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Um cluster Kafka pode funcionar com databricks? 

Sim, os clusters Kafka podem trabalhar com databricks desde que estejam no mesmo VNet. Para usar um cluster Kafka com Databricks, crie um VNet com um cluster HDInsight Kafka nele e, em seguida, especifique esse VNet quando criar seu espaço de trabalho Databricks e usar a injeção VNet. Para obter mais informações, consulte [Implantar o Azure Databricks em sua Rede Virtual do Azure (Injeção de VNet)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Você precisará fornecer os nomes dos corretores bootstrap do cluster Kafka ao criar o espaço de trabalho Databricks. Para obter informações sobre como recuperar os nomes dos corretores Kafka, consulte [Obter as informações do anfitrião Apache Zookeeper e Broker](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Como posso ter a durabilidade máxima dos dados?

A durabilidade dos dados permite que você atinja o menor risco de perda de mensagens. Para atingir a máxima durabilidade dos dados, recomendamos as seguintes configurações:

* usar um fator de replicação mínimo de 3 na maioria das regiões
* usar um fator de replicação mínimo de 4 em regiões com apenas dois domínios de falha
* desativar eleições líder imundo
* definir **min.insync.replicas** para 2 ou mais - isso altera o número de réplicas que devem estar completamente em sincronia com o líder antes que uma gravação possa prosseguir
* definir a propriedade **acks** para **todos** - esta propriedade requer todas as réplicas para reconhecer todas as mensagens

A configuração do Kafka para maior consistência de dados afeta a disponibilidade dos corretores para produzir solicitações.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Posso replicar meus dados em vários clusters?

Sim, os dados podem ser replicados em vários clusters usando kafka MirrorMaker. Veja detalhes sobre a configuração do MirrorMaker pode ser encontrado nos [tópicos Mirror Apache Kafka](apache-kafka-mirroring.md). Além disso, existem outras tecnologias e fornecedores de código aberto auto-gerenciados que podem ajudar a alcançar a replicação em vários clusters, como [o Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Posso atualizar meu cluster? Como devo atualizar meu cluster?

No momento, não temos suporte a upgrades de versão de cluster no local. Para atualizar seu cluster para uma versão kafka mais alta, crie um novo cluster com a versão que você deseja e migre seus clientes Kafka para usar o novo cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Como eu monitoro meu aglomerado Kafka?

Use o monitor Azure para analisar seus [registros Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Próximas etapas

* [Configure criptografia e autenticação de Camada de Soquetes Seguros (SSL) para Apache Kafka no Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Usar o MirrorMaker para replicar tópicos do Apache Kafka com Kafka no HDInsight](./apache-kafka-mirroring.md)
