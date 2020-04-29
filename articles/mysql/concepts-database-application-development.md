---
title: Desenvolvimento de aplicativos-banco de dados do Azure para MySQL
description: Apresenta considerações de design que um desenvolvedor deve seguir ao escrever o código de um aplicativo para se conectar ao Banco de Dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 02ce6b00b6555f849d162b9f3b381c0ab358d712
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532834"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Visão geral de desenvolvimento de aplicativo para o Banco de Dados do Azure para MySQL 
Este artigo discute considerações de design que um desenvolvedor deve seguir ao escrever o código de um aplicativo para se conectar ao Banco de Dados do Azure para MySQL. 

> [!TIP]
> Para ver um tutorial que mostra como criar um servidor, criar um firewall baseado em servidor, exibir propriedades do servidor, criar um banco de dados, conectar e consultar usando o Workbench e mysql.exe, consulte [Criar seu primeiro Banco de Dados do Azure para MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Linguagem e plataforma
Há exemplos de código disponíveis para uma variedade de plataformas e linguagens de programação. Encontre links para exemplos de código em: [Bibliotecas de conectividade usadas para se conectar ao Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Ferramentas
O Banco de Dados do Azure para MySQL usa a versão de comunidade do MySQL, que é compatível com ferramentas de gerenciamento comuns do MySQL, como o Workbench, ou utilitários do MySQL, como o mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql) e outros. Você também pode usar o Portal do Azure, a CLI do Azure e APIs REST para interagir com o serviço de banco de dados.

## <a name="resource-limitations"></a>Limitações de recursos
O Banco de Dados do Azure para MySQL gerencia os recursos disponíveis para um servidor usando dois mecanismos diferentes: 
- Governança de recursos.
- Imposição de limites.

## <a name="security"></a>Segurança
O Banco de Dados do Azure para MySQL fornece recursos para limitar o acesso, proteger os dados, configurar usuários e funções e monitorar atividades em um banco de dados MySQL.

## <a name="authentication"></a>Autenticação
O Banco de Dados do Azure para MySQL dá suporte à autenticação de usuários e logons do servidor.

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório ao se conectar ao banco de dados MySQL, seu código deverá repetir a chamada. Recomendamos que a lógica de repetição use a lógica de retirada, de modo que ela não sobrecarregue o banco de dados SQL com vários clientes realizando novas tentativas ao mesmo tempo.

- Exemplos de código: para obter exemplos de código que ilustram a lógica de repetição, confira os exemplos para o idioma de sua preferência em: [Bibliotecas de conexão usadas para se conectar ao Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Gerenciando conexões
Conexões de banco de dados são um recurso limitado, portanto, recomendamos o uso adequado de conexões ao acessar o banco de dados MySQL para melhorar o desempenho.
- Acesse o banco de dados usando o pooling de conexão ou conexões persistentes.
- Acesse o banco de dados usando uma conexão de curta duração. 
- Use a lógica de repetição em seu aplicativo no ponto da tentativa de conexão, para detectar falhas causadas pelas conexões simultâneas que atingiram o máximo permitido. Na lógica de repetição, defina um pequeno atraso e aguarde um período aleatório antes das tentativas de conexão adicionais.