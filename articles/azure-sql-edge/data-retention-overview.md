---
title: Visão geral da política de retenção de dados – Azure SQL Edge (visualização)
description: Saiba mais sobre a política de retenção de dados no Azure SQL Edge (versão prévia)
keywords: Borda do SQL, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 3649d4f77e5b57ab14accacd87fbaa867ba2742f
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89550596"
---
# <a name="data-retention-policy-overview"></a>Visão geral da política de retenção de dados

A coleta e o armazenamento de dados de dispositivos IoT conectados são importantes para impulsionar e obter informações operacionais e de negócios. No entanto, dado o volume de dados provenientes desses dispositivos, é importante que as organizações planejem cuidadosamente a quantidade de dados que desejam reter e em que granularidade. Embora manter todos os dados em toda a granularidade seja desejável, nem sempre é prático. Além disso, o volume de dados que pode ser retido é restrito pela quantidade de armazenamento disponível nos dispositivos IoT ou Edge. 

O Azure SQL Edge (visualização) CTP 2.3 adiciona um novo recurso que permite que os administradores de banco de dados definam a política de retenção em um banco de dado SQL Edge e suas tabelas subjacentes. Depois que a política de retenção de dados for definida, uma tarefa de sistema em segundo plano será executada para limpar os dados obsoletos (antigos) das tabelas de usuário. 

> [!Note]
> Dados depois de limpos da tabela, não são recuperáveis. A única maneira possível de recuperar os dados limpos é restaurá-lo de um backup mais antigo.

Inícios Rápidos:

- [Habilitar e desabilitar políticas de retenção de dados](data-retention-enable-disable.md)
- [Gerenciar dados históricos com a política de retenção](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Como a retenção de dados funciona

Para configurar a retenção de dados, você pode usar instruções DDL. Para obter mais informações, [habilite e desabilite as políticas de retenção de dados](data-retention-enable-disable.md). Para a exclusão automática dos registros obsoletos, a retenção de dados deve primeiro ser habilitada para o banco de dado e para as tabelas que você deseja que sejam limpas nesse banco de dados. 

Depois que a retenção de dados é configurada para uma tabela, uma tarefa em segundo plano é executada para identificar os registros obsoletos em uma tabela e excluir esses registros. Se, por algum motivo, a limpeza automática das tarefas não estiver em execução ou não puder acompanhar as exclusões, uma operação de limpeza manual poderá ser executada nessas tabelas. Para obter mais informações sobre as limpezas automáticas e manuais, consulte [limpeza automática e manual](data-retention-cleanup.md).

## <a name="limitations-and-restrictions"></a>Limitações e restrições

- A retenção de dados, se habilitada, é automaticamente desabilitada quando o banco de dado é restaurado de um backup completo ou é reanexado. 
- A retenção de dados não pode ser habilitada para uma tabela de histórico temporal

## <a name="next-steps"></a>Próximas etapas

- [Machine learning e inteligência artificial com o ONNX no SQL no Edge](onnx-overview.md).
- [Como criar uma solução de IoT de ponta a ponta com o SQL no Edge usando o IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados no Azure SQL Edge](stream-data.md)
