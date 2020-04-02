---
title: Decodificação lógica - Banco de dados Azure para PostgreSQL - Servidor Único
description: Descreve decodificação lógica e wal2json para captura de dados de alteração no Banco de Dados Azure para PostgreSQL - Servidor Único
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522141"
---
# <a name="logical-decoding"></a>Decodificação lógica
 
[A decodificação lógica no PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) permite que você transmita alterações de dados para consumidores externos. A decodificação lógica é popularmente usada para streaming de eventos e alterar cenários de captura de dados.

A decodificação lógica usa um plugin de saída para converter o registro de gravação ahead (WAL) do Postgres em um formato legível. O Banco de Dados Azure para PostgreSQL fornece dois plugins de saída: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) e [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> A decodificação lógica está em visualização pública no Banco de Dados Azure para PostgreSQL - Single Server.


## <a name="set-up-your-server"></a>Configure seu servidor
Para começar a usar a decodificação lógica, habilite seu servidor para salvar e transmitir o WAL. 

1. Defina azure.replication_support para `logical` usar o Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Se você usar réplicas de leitura, `logical` azure.replication_support definido para também permitir que réplicas seja executadas. Se você parar de usar a decodificação lógica, altere a configuração de volta para `replica`. 


2. Reinicie o servidor para aplicar as alterações.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Iniciar decodificação lógica

A decodificação lógica pode ser consumida através do protocolo de streaming ou da interface SQL. Ambos os métodos usam [slots de replicação](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Um slot representa um fluxo de alterações de um único banco de dados.

O uso de um slot de replicação requer privilégios de replicação do Postgres. Neste momento, o privilégio de replicação só está disponível para o usuário de admin do servidor. 

### <a name="streaming-protocol"></a>Protocolo de streaming
O consumo de alterações usando o protocolo de streaming é muitas vezes preferível. Você pode criar seu próprio consumidor/ conector, ou usar uma ferramenta como [debezium](https://debezium.io/). 

Visite a documentação wal2json para [um exemplo usando o protocolo de streaming com pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Interface SQL
No exemplo abaixo, usamos a interface SQL com o plugin wal2json.
 
1. Crie um slot.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Emitir comandos SQL. Por exemplo: 
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Consuma as mudanças.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   A saída será como:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Solte o slot assim que terminar de usá-lo.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Slots de monitoramento

Você deve monitorar a decodificação lógica. Qualquer slot de replicação não utilizado deve ser descartado. Os slots se agarram aos registros do Postgres WAL e aos catálogos relevantes do sistema até que as alterações tenham sido lidas por um consumidor. Se o seu consumidor falhar ou não tiver sido configurado corretamente, os registros não consumidos se acumularão e encherão seu armazenamento. Além disso, os logs não consumidos aumentam o risco de envoltório de ID de transação. Ambas as situações podem fazer com que o servidor fique indisponível. Portanto, é fundamental que os slots de replicação lógica sejam consumidos continuamente. Se um slot de replicação lógico não for mais usado, solte-o imediatamente.

A coluna 'ativa' na pg_replication_slots exibir indicará se há um consumidor conectado a um slot.
```SQL
SELECT * FROM pg_replication_slots;
```

[Defina alertas](howto-alert-on-metric.md) sobre *o armazenamento usado* e o Max *decole entre as métricas de réplicas* para notificá-lo quando os valores aumentarem os limites normais. 

> [!IMPORTANT]
> Você deve soltar slots de replicação não utilizados. Não fazê-lo pode levar à indisponibilidade do servidor.

## <a name="how-to-drop-a-slot"></a>Como soltar um slot
Se você não estiver consumindo ativamente um slot de replicação, você deve solhá-lo.

Para soltar um slot `test_slot` de replicação chamado usando SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Se você parar de usar decodificação lógica, `replica` `off`altere azure.replication_support de volta ou . Os detalhes wal `logical` retidos por são mais verbosos, e devem ser desativados quando a decodificação lógica não está em uso. 

 
## <a name="next-steps"></a>Próximas etapas

* Visite a documentação do Postgres para [saber mais sobre decodificação lógica.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)
* Entre em contato com [nossa equipe](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) se você tiver dúvidas sobre decodificação lógica.
* Saiba mais sobre [réplicas de leitura](concepts-read-replicas.md).

