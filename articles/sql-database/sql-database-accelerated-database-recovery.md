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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238670"
---
# <a name="accelerated-database-recovery"></a>Recuperação acelerada de banco de dados

**O Accelerated Database Recovery (ADR)** é um recurso do mecanismo de banco de dados SQL que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longo prazo, redesenhando o processo de recuperação do mecanismo de banco de dados SQL. Atualmente, o ADR está disponível para o Azure SQL Database single, elástico pool e instância gerenciada, e bancos de dados no Azure SQL Data Warehouse (atualmente em visualização). Os principais benefícios do ADR são:

- **Recuperação de banco de dados rápida e consistente**

  Com a ADR, as transações de execução prolongada não afetam o tempo de recuperação geral, permitindo uma recuperação de banco de dados rápida e consistente, independentemente do número de transações ativas no sistema ou do tamanho delas.

- **Reversão de transação instantânea**

  Com ADR, a reversão de transação é instantânea, independentemente do tempo em que a transação esteve ativa ou do número de atualizações que ela realizou.

- **Truncamento agressivo do log**

  Com o ADR, o registro de transações é agressivamente truncado, mesmo na presença de transações ativas de longo prazo, o que o impede de crescer fora de controle.

## <a name="the-current-database-recovery-process"></a>O processo de recuperação de banco de dados atual

A recuperação do banco de dados no SQL Server segue o modelo de recuperação [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama a seguir e explicadas com mais detalhes após o diagrama.

![processo de recuperação atual](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Consulte o registro de transações do início do último ponto de verificação bem sucedido (ou a página suja mais antiga do LSN) até o final, para determinar o estado de cada transação no momento em que o SQL Server parou.

- **Fase refazer**

  Faça a varredura do registro da transação mais antiga até o final, para trazer o banco de dados ao estado em que estava no momento do acidente, refazendo todas as operações comprometidas.

- **Fase desfazer**

  Para cada transação que estava ativa no momento da falha, o registro é revertido, desfazendo as operações executadas por essa transação.

Com base nesse design, o tempo que o mecanismo do banco de dados SQL leva para recuperar-se de uma reinicialização inesperada é (aproximadamente) proporcional ao tamanho da transação ativa mais longa no sistema no momento da falha. A recuperação requer uma reversão de todas as transações incompletas. O período de tempo necessário é proporcional para o trabalho que a transação foi executado e o tempo ele esteve ativo. Portanto, o processo de recuperação do SQL Server pode levar muito tempo na presença de transações de longo prazo (como grandes operações de inserção a granel ou operações de compilação de índice contra uma tabela grande).

Além disso, o cancelamento / reversão de uma transação grande com base nesse design também pode levar um longo tempo, já que está usando a mesma fase de recuperação Desfazer, conforme descrito acima.

Além disso, o mecanismo de banco de dados SQL não pode truncar o registro de transações quando há transações de longa duração porque seus registros de registro correspondentes são necessários para os processos de recuperação e reversão. Como resultado desse projeto do mecanismo de banco de dados SQL, alguns clientes costumavam enfrentar o problema de que o tamanho do registro de transações cresce muito grande e consome enormes quantidades de espaço de unidade.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação de banco de dados acelerada

O ADR soluciona os problemas acima, redesenhando completamente o processo de recuperação do mecanismo de banco de dados SQL para:

- Torne constante o tempo / instantâneo, evitando ter que escanear o log de / para o início da transação ativa mais antiga. Com o ADR, o log de transação só é processado a partir do último ponto de verificação bem sucedido (ou número de seqüência de log (LSN) da página suja mais antiga). Como resultado, o tempo de recuperação não é afetado por longa execução de transações.
- Minimize o espaço de log de transações necessário, pois não há mais necessidade de processar o log para toda a transação. Como resultado, o log de transações pode ser truncado de forma agressiva à medida que os pontos de verificação e backups ocorrem.

Em um Alto Nível, o ADR consegue uma rápida recuperação do banco de dados, versiando todas as modificações físicas do banco de dados e apenas desfazendo operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estava ativa no momento de uma falha é marcada como abortada e, portanto, qualquer versão gerada por essas transações pode ser ignorada por consultas de usuários simultâneas.

O processo de recuperação ADR tem as mesmas três fases que o processo de recuperação atual. Como essas fases operam com ADR é ilustrado no diagrama a seguir e explicado com mais detalhes seguindo o diagrama.

![Processo de recuperação ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo de antes, com a adição de sLog e cópia de registros de registros para operações não-versões.
  
- **Fase de refazer**

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

  Logical revert é o processo assíncrono responsável pela execução do Desfazer baseado em versão em nível de linha - fornecendo reversão de transação instantânea e desfazer para todas as operações com versões. A revertência lógica é realizada por:

  - Manter o controle de todas as transações abortadas e marcá-las invisíveis a outras transações. 
  - Realizar a reversão usando PVS para todas as transações do usuário, em vez de digitalizar fisicamente o registro de transações e desfazer alterações uma de cada vez.
  - Liberando todos os bloqueios imediatamente após o abortamento da transação. Como o abortamento envolve simplesmente marcar mudanças na memória, o processo é muito eficiente e, portanto, os bloqueios não devem ser mantidos por muito tempo.

- **sLog**

  O sLog é um fluxo de log na memória secundário que armazena registros de log para operações sem controle de versão (tais como invalidação de cache de metadados, aquisições de bloqueio e assim por diante). O sLog é:

  - de baixo volume e na memória;
  - persistido no disco ao ser serializado durante o processo de ponto de verificação;
  - truncado periodicamente conforme as transações são confirmadas;
  - acelera as operações refazer e desfazer ao processar apenas as operações sem controle de versão;  
  - habilita o truncamento agressivo de log de transações ao preservar apenas os registros de log necessários.

- **Limpador**

  O limpador é o processo assíncrono que é ativado periodicamente e limpa as versões de página que não são necessárias.

## <a name="accelerated-database-recovery-patterns"></a>Padrões acelerados de recuperação de banco de dados

Os seguintes tipos de cargas de trabalho beneficiam mais do ADR:

- Cargas de trabalho com transações de longo prazo.
- Cargas de trabalho que viram casos em que transações ativas estão fazendo com que o registro de transações cresça significativamente.  
- Cargas de trabalho que experimentaram longos períodos de indisponibilidade no banco de dados devido à recuperação de longa duração do SQL Server (como reinicialização inesperada do SQL Server ou reversão manual da transação).

