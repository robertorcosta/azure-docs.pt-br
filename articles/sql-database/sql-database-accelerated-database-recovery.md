---
title: Recuperação de banco de dados acelerada
description: O Banco de Dados SQL do Azure tem um novo recurso que fornece recuperação de banco de dados rápida e consistente, reversão de transação instantânea e truncamento de log agressivo para bancos de dados individuais e em pool em Banco de Dados SQL do Azure, bem como bancos de dados no SQL Data Warehouse do Azure.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80238670"
---
# <a name="accelerated-database-recovery"></a>Recuperação acelerada de banco de dados

A **ADR (recuperação de banco de dados acelerada)** é um recurso do mecanismo de banco de dados SQL que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longa execução, remodelando o processo de recuperação do mecanismo de banco de dados SQL. O ADR está disponível no momento para o banco de dados SQL do Azure, um pool elástico e uma instância gerenciada, além de bancos dados no Azure SQL Data Warehouse (atualmente em visualização). Os principais benefícios do ADR são:

- **Recuperação de banco de dados rápida e consistente**

  Com a ADR, as transações de execução prolongada não afetam o tempo de recuperação geral, permitindo uma recuperação de banco de dados rápida e consistente, independentemente do número de transações ativas no sistema ou do tamanho delas.

- **Reversão de transação instantânea**

  Com ADR, a reversão de transação é instantânea, independentemente do tempo em que a transação esteve ativa ou do número de atualizações que ela realizou.

- **Truncamento agressivo do log**

  Com ADR, o log de transações é truncado agressivamente, mesmo na presença de transações de execução longa ativas, o que impede o crescimento fora do controle.

## <a name="the-current-database-recovery-process"></a>O processo de recuperação de banco de dados atual

A recuperação do banco de dados no SQL Server segue o modelo de recuperação [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama a seguir e explicadas com mais detalhes após o diagrama.

![processo de recuperação atual](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Encaminhe a verificação do log de transações desde o início do último ponto de verificação bem-sucedido (ou o LSN de página sujo mais antigo) até o final, para determinar o estado de cada transação no momento SQL Server parado.

- **Fase refazer**

  Encaminhe a varredura do log de transações da transação não confirmada mais antiga até o fim, para colocar o banco de dados no estado em que estava no momento da falha, refazendo todas as operações confirmadas.

- **Fase desfazer**

  Para cada transação que estava ativa no momento da falha, o registro é revertido, desfazendo as operações executadas por essa transação.

Com base nesse design, o tempo que o mecanismo do banco de dados SQL leva para recuperar-se de uma reinicialização inesperada é (aproximadamente) proporcional ao tamanho da transação ativa mais longa no sistema no momento da falha. A recuperação requer uma reversão de todas as transações incompletas. O período de tempo necessário é proporcional para o trabalho que a transação foi executado e o tempo ele esteve ativo. Portanto, o processo de recuperação de SQL Server pode levar muito tempo na presença de transações de longa execução (como operações de inserção em massa grandes ou operações de compilação de índice em uma tabela grande).

Além disso, o cancelamento / reversão de uma transação grande com base nesse design também pode levar um longo tempo, já que está usando a mesma fase de recuperação Desfazer, conforme descrito acima.

Além disso, o mecanismo do banco de dados SQL não pode truncar o log de transações quando há transações de longa execução, pois seus registros de log correspondentes são necessários para os processos de recuperação e reversão. Como resultado desse design do mecanismo de banco de dados SQL, alguns clientes usaram o problema de que o tamanho do log de transações cresce muito grande e consome enormes quantidades de espaço em disco.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação de banco de dados acelerada

O ADR soluciona os problemas acima, redesenhando completamente o processo de recuperação do mecanismo de banco de dados SQL para:

- Torne constante o tempo / instantâneo, evitando ter que escanear o log de / para o início da transação ativa mais antiga. Com ADR, o log de transações só é processado a partir do último ponto de verificação bem-sucedido (ou LSN (número de sequência de log) de página sujo mais antigo). Como resultado, o tempo de recuperação não é afetado por longa execução de transações.
- Minimize o espaço de log de transações necessário, pois não há mais necessidade de processar o log para toda a transação. Como resultado, o log de transações pode ser truncado de forma agressiva à medida que os pontos de verificação e backups ocorrem.

Em um nível alto, ADR alcança a recuperação rápida do banco de dados ao realizar o controle de versão de todas as modificações físicas do banco de dados e desfazer apenas operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estava ativa no momento de uma falha é marcada como abortada e, portanto, qualquer versão gerada por essas transações pode ser ignorada por consultas de usuários simultâneas.

O processo de recuperação ADR tem as mesmas três fases que o processo de recuperação atual. Como essas fases operam com ADR é ilustrado no diagrama a seguir e explicado com mais detalhes seguindo o diagrama.

![Processo de recuperação ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo que antes com a adição da recriação de sLog e a cópia de registros de log para operações sem controle de versão.
  
- Fase de **refazer**

  Dividido em duas fases (P)
  - Fase 1

      Refazer de sLog (transação não confirmada mais antiga até o último ponto de verificação). O Redo é uma operação rápida, pois precisa apenas processar alguns registros do sLog.
      
  - Fase 2

     Refazer a partir do Log de transações inicia a partir do último ponto de verificação (em vez da transação não confirmada mais antiga)
     
- **Fase desfazer**

   A fase Desfazer com ADR é concluída quase instantaneamente usando sLog para desfazer operações sem versão e Armazenamento de Versão Persistente (PVS) com Revolução Lógica para executar a Desfazer baseada em versão no nível da linha.

## <a name="adr-recovery-components"></a>Componentes de recuperação ADR

Os quatro componentes principais da ADR são:

- **Store de versão persistentes (PVS)**

  O armazenamento de versão persistente é um novo mecanismo de mecanismo de banco de dados SQL para persistir as versões de linha geradas no próprio banco de dados, em vez do tradicional `tempdb` armazenamento de versão. PVS habilita o isolamento de recursos, bem como melhora a disponibilidade de secundários legíveis.

- **Reversão lógica**

  A reversão lógica é o processo assíncrono responsável por executar desfazer baseado em versão de linha, fornecendo reversão de transação instantânea e desfazer para todas as operações com controle de versão. A reversão lógica é realizada por:

  - Manter o controle de todas as transações anuladas e marcá-las invisíveis para outras transações. 
  - Executar Rollback usando PVS para todas as transações de usuário, em vez de verificar fisicamente o log de transações e desfazer as alterações uma de cada vez.
  - Liberando todos os bloqueios imediatamente após a anulação da transação. Como Abort envolve simplesmente marcar alterações na memória, o processo é muito eficiente e, portanto, os bloqueios não precisam ser mantidos por muito tempo.

- **sLog**

  O sLog é um fluxo de log na memória secundário que armazena registros de log para operações sem controle de versão (tais como invalidação de cache de metadados, aquisições de bloqueio e assim por diante). O sLog é:

  - de baixo volume e na memória;
  - persistido no disco ao ser serializado durante o processo de ponto de verificação;
  - truncado periodicamente conforme as transações são confirmadas;
  - acelera as operações refazer e desfazer ao processar apenas as operações sem controle de versão;  
  - habilita o truncamento agressivo de log de transações ao preservar apenas os registros de log necessários.

- **Limpador**

  O limpador é o processo assíncrono que é ativado periodicamente e limpa as versões de página que não são necessárias.

## <a name="accelerated-database-recovery-patterns"></a>Padrões de recuperação de banco de dados acelerados

Os seguintes tipos de cargas de trabalho se beneficiam da ADR:

- Cargas de trabalho com transações de longa execução.
- Cargas de trabalho que viram casos em que as transações ativas estão fazendo com que o log de transações cresça significativamente.  
- Cargas de trabalho que tiveram longos períodos de indisponibilidade de banco de dados devido a SQL Server recuperação de longa execução (como reinicialização de SQL Server inesperada ou reversão de transação manual).

