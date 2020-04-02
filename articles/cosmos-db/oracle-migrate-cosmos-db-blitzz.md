---
title: Migrar dados da Oracle para a Azure Cosmos DB Cassandra API usando blitzz
description: Saiba como migrar dados do banco de dados Oracle para a Api Azure Cosmos DB Cassandra usando blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 43d15a7252819a3e4f7635e37458b75e9b7ecca7
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546280"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrar dados da Oracle para a conta API do Azure Cosmos DB Cassandra usando blitzz

Cassandra API no Azure Cosmos DB tornou-se uma ótima escolha para cargas de trabalho corporativas que estão sendo executados na Oracle por uma variedade de razões, tais como:

* **Melhor escalabilidade e disponibilidade:** Elimina pontos únicos de falha, melhor escalabilidade e disponibilidade para seus aplicativos.

* **Redução significativa de custos:** Você pode economizar custos com o Azure Cosmos DB, que inclui o custo das VM's, largura de banda e quaisquer licenças Oracle aplicáveis. Além disso, você não precisa gerenciar os custos de data centers, servidores, armazenamento de SSD, rede e eletricidade.

* **Sem sobrecarga de gerenciamento e monitoramento:** Como um serviço de nuvem totalmente gerenciado, o Azure Cosmos DB remove a sobrecarga de gerenciamento e monitoramento de uma miríade de configurações.

Existem várias maneiras de migrar cargas de trabalho de banco de dados de uma plataforma para outra. [Blitzz](https://www.blitzz.io) é uma ferramenta que oferece uma maneira segura e confiável de realizar a migração sem tempo de inatividade de uma variedade de bancos de dados para o Azure Cosmos DB. Este artigo descreve as etapas necessárias para migrar dados do banco de dados Oracle para a Api Azure Cosmos DB Cassandra usando blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Benefícios usando blitzz para migração

A solução de migração da Blitzz segue uma abordagem passo a passo para migrar cargas de trabalho operacionais complexas. A seguir, alguns dos principais aspectos do plano de migração de tempo zero da Blitzz:

* Oferece migração automática da lógica de negócios (tabelas, índices, visualizações) do banco de dados Oracle para o Azure Cosmos DB. Você não tem que criar esquemas manualmente.

* Blitzz oferece replicação de banco de dados de alto volume e paralelo. Ele permite que as plataformas de origem e de destino estejam em sincronia durante a migração usando uma técnica chamada Change-Data-Capture (CDC). Ao usar o CDC, o Blitzz retira continuamente um fluxo de alterações do banco de dados de origem (Oracle) e aplica-o ao banco de dados de destino (Azure Cosmos DB).

* É tolerante a falhas e garante exatamente uma vez a entrega de dados mesmo durante uma falha de hardware ou software no sistema.

* Ele protege os dados durante o trânsito usando uma variedade de metodologias de segurança, como TLS/SSL, criptografia.

* Oferece serviços para converter lógica de negócios complexa escrita em PL/SQL para lógica de negócios equivalente no Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Etapas para migrar dados

Esta seção descreve as etapas necessárias para configurar o Blitzz e migra dados do banco de dados Oracle para o Azure Cosmos DB.

1. A partir do computador onde você planeja instalar o replicante Blitzz, adicione um certificado de segurança. Este certificado é exigido pelo replicante Blitzz para estabelecer uma conexão TLS com a conta DB do Azure Cosmos especificada. Você pode adicionar o certificado com as seguintes etapas:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou pode obter a instalação blitzz e os arquivos binários, solicitando uma demonstração no [site blitzz](https://www.blitzz.io). Alternativamente, você também pode enviar um [e-mail](mailto:success@blitzz.io) para a equipe.

   ![Download de ferramentas replicantes blitzz](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Arquivos replicantes blitzz](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. A partir do terminal CLI, configure a configuração do banco de dados de origem. Abra o arquivo **`vi conf/conn/oracle.yml`** de configuração usando o comando e adicione uma lista separada por comma de endereços IP dos nós oracle, número da porta, nome de usuário, senha e quaisquer outros detalhes necessários. O código a seguir mostra um arquivo de configuração de exemplo:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   ![Editor de conexão Oracle aberto](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Configuração de conexão Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   Depois de preencher os detalhes da configuração, salve e feche o arquivo.

1. Opcionalmente, você pode configurar o arquivo de filtro do banco de dados de origem. O arquivo do filtro especifica quais esquemas ou tabelas devem migrar. Abra o arquivo **`vi filter/oracle_filter.yml`** de configuração usando o comando e digite os seguintes detalhes de configuração:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Depois de preencher os detalhes do filtro do banco de dados, salve e feche o arquivo.

1. Em seguida, você configurará a configuração do banco de dados de destino. Antes de definir a configuração, [crie uma conta API Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account). Escolha a chave de [partição certa](partitioning-overview.md#choose-partitionkey) de seus dados e, em seguida, crie um Espaço-chave e uma tabela para armazenar os dados migrados.

1. Antes de migrar os dados, aumente o throughput do contêiner para a quantidade necessária para que sua aplicação migre rapidamente. Por exemplo, você pode aumentar o throughput para 100000 RUs. O dimensionamento do throughput antes de iniciar a migração ajudará você a migrar seus dados em menos tempo. 

   ![Scale Azure Cosmos contêiner em todo](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   Você deve diminuir o throughput depois que a migração estiver concluída. Com base na quantidade de dados armazenados e RUs necessários para cada operação, você pode estimar o throughput necessário após a migração de dados. Para saber mais sobre como estimar as RUs [necessárias, consulte Provision throughput em contêineres e bancos de dados](set-throughput.md) e [Estimar RU/s usando os artigos do planejador de capacidade Azure Cosmos DB.](estimate-ru-with-capacity-planner.md)

1. Obtenha o **Ponto de Contato, Porta, Nome**de Usuário e Senha **Primária** da sua conta Do Azure Cosmos no painel **Conexão String.** Você usará esses valores no arquivo de configuração.

1. A partir do terminal CLI, configure a configuração do banco de dados de destino. Abra o arquivo **`vi conf/conn/cosmosdb.yml`** de configuração usando o comando e adicione uma lista separada por comuma de URI do host, número da porta, nome de usuário, senha e outros parâmetros necessários. A seguir, um exemplo de conteúdo no arquivo de configuração:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Em seguida, migrar os dados usando Blitzz. Você pode executar o replicante Blizz no modo **completo** ou **instantâneo:**

   * **Modo completo** – Neste modo, o replicante continua a ser executado após a migração e ouve quaisquer alterações no sistema Oracle de origem. Se detectar qualquer alteração, elas serão replicadas na conta alvo do Azure Cosmos em tempo real.

   * **Modo instantâneo** – Neste modo, você pode executar a migração de esquemas e a replicação única de dados. A replicação em tempo real não é suportada com essa opção.


   Usando os dois modos acima, a migração pode ser realizada com tempo de inatividade zero.

1. Para migrar dados, a partir do terminal CLI replicante blitzz, execute o seguinte comando:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   A ui replicante mostra o progresso da replicação. Uma vez que a migração do esquema e a operação de instantâneo são feitas, o progresso mostra 100%. Depois que a migração estiver concluída, você pode validar os dados no banco de dados azure Cosmos de destino.

   ![Saída de migração de dados Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Como você usou o modo completo para migração, você pode executar operações como inserir, atualizar ou excluir dados no banco de dados Oracle de origem. Mais tarde, você pode validar que eles são replicados em tempo real no banco de dados azure Cosmos alvo. Após a migração, certifique-se de diminuir o throughput configurado para o contêiner Azure Cosmos.

1. Você pode parar o replicante em qualquer ponto e reiniciá-lo com o interruptor **de currículo.** A replicação é retomada a partir do ponto em que parou sem comprometer a consistência dos dados. O comando a seguir mostra como usar o switch de currículo.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Para saber mais sobre a migração de dados para o destino, migração em tempo real, consulte a [demonstração replicante blitzz](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Próximas etapas

* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 
* [Práticas recomendadas das principais práticas de partição](partitioning-overview.md#choose-partitionkey)
* [Estimar RU/s usando os artigos do planejador de capacidade Azure Cosmos DB](estimate-ru-with-capacity-planner.md)