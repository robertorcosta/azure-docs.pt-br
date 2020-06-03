---
title: Acompanhamento de alterações de dados no SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre o controle de alterações e a captura de dados de alteração no SQL do Azure no Edge (versão prévia)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3e3a6c3e171383d1812f63b945735d1b82f70b9c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235095"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Acompanhamento de alterações de dados no SQL do Azure no Edge (versão prévia)

O SQL do Azure no Edge é compatível com os dois recursos do SQL Server que acompanham alterações nos dados de um banco de dados:[Controle de Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) e [Captura de dados de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Esses recursos permitem que os aplicativos determinem as alterações de DML (operações de inserção, atualização e exclusão) que foram feitas em tabelas de usuários em um banco de dados. A captura de dados de alteração e o controle de alterações podem ser habilitados no mesmo banco de dados; nenhuma consideração especial necessária.

Um requisito importante para a eficiência de alguns aplicativos é a capacidade de consultar os dados que foram alterados em um banco de dados. Em geral, para determinar alterações de dados, os desenvolvedores de aplicativos devem implementar um método personalizado de controle nos aplicativos usando uma combinação de gatilhos, colunas de carimbo de data/hora e tabelas adicionais. A criação desses aplicativos costumava envolve uma implementação muito trabalhosa, leva a atualizações de esquema e frequentemente acarreta uma sobrecarga de alto desempenho. No caso da solução de IoT, em que há a necessidade de mover dados periodicamente da borda para uma nuvem ou data center, o controle de alterações pode ser muito útil. Isso permitiria que fosse usado para consultar com rapidez e eficiência apenas as alterações delta da última sincronização e carregar essas alterações na nuvem ou no data center de destino. Para obter detalhes adicionais sobre os benefícios de usar Controle de Alterações e captura de dados de alterações, consulte [Benefícios de usar a captura de dados de alterações ou o Controle de Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Para entender as diferenças de recursos entre o Controle de Alterações e a captura de dados de alterações, consulte [Diferenças de recursos entre a captura de dados de alterações e o Controle de Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Captura de dados de alterações

Para entender os detalhes de como funciona a captura de dados de alterações, consulte [Sobre captura de dados de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Para entender como habilitar ou desabilitar a captura de dados de alterações, consulte [Habilitar e desabilitar a captura de dados de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Para administrar e monitorar a captura de dados de alterações, consulte [Administrar e monitorar a captura de dados de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Para entender como consultar e trabalhar com os dados alterados, consulte [Trabalhar com dados de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server).

## <a name="change-tracking"></a>Controle de Alterações

Para entender os detalhes de como funciona o Controle de Alterações, consulte [Sobre o Controle de Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Para entender como habilitar ou desabilitar o Controle de Alterações, consulte [Habilitar e desabilitar o Controle de Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Para administrar, monitorar e gerenciar o Controle de Alterações, consulte [Administrar e monitorar o Controle de Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

Para entender como consultar e trabalhar com os dados alterados, consulte [Trabalhar com dados de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server).

## <a name="temporal-tables"></a>Tabelas temporais

Além da compatibilidade com os recursos de Controle de Alterações e de captura de dados de alterações do SQL Server, o SQL do Azure no Edge também é compatível com o recurso de tabelas temporais do SQL Server. Tabelas temporais (também conhecidas como tabelas temporais com versão do sistema) como um recurso de banco de dados com suporte interno para fornecer informações sobre dados armazenados na tabela em qualquer ponto no tempo em vez de apenas os dados que estão corretos no momento atual.

Uma tabela temporal com versão do sistema é um tipo de tabela de usuário criada para manter um histórico completo de alterações de dados e permitir uma análise fácil de pontos no tempo. Esse tipo de tabela temporal é conhecido como tabela temporal com versão do sistema porque o período de validade para cada linha é gerenciado pelo sistema (ou seja, o mecanismo de banco de dados).

Cada tabela temporal tem duas colunas explicitamente definidas, cada uma com um tipo de dados datetime2. Essas colunas são chamadas de colunas de período. Essas colunas de período são usadas exclusivamente pelo sistema para gravar o período de validade para cada linha sempre que uma linha é modificada.

Além dessas colunas de período, uma tabela temporal também contém uma referência a outra tabela com um esquema espelhado. O sistema usa essa tabela para armazenar a versão anterior da linha automaticamente sempre que uma linha na tabela temporal é atualizada ou excluída. Essa tabela adicional é conhecida como a tabela de histórico, enquanto a tabela principal que armazena as versões atuais (reais) das linhas é conhecida como a tabela atual ou simplesmente como a tabela temporal. Durante a criação da tabela temporal, os usuários podem especificar uma tabela de histórico existente (deve ter um esquema compatível) ou deixar que o sistema crie a tabela de histórico padrão.

Para obter mais informações, confira [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables).

## <a name="see-also"></a>Consulte Também

- [Streaming de dados no SQL do Azure no Edge (versão prévia)](stream-data.md)
- [Aprendizado de máquina e IA com ONNX no SQL do Azure no Edge (versão prévia) ](onnx-overview.md)
- [Configurar replicação para o SQL do Azure no Edge (versão prévia) ](configure-replication.md)
- [Backup e restauração de bancos de dados no SQL do Azure no Edge (Versão prévia)](backup-restore.md)



