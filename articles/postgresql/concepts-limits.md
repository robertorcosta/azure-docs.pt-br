---
title: Limites no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve os limites no banco de dados do Azure para PostgreSQL-servidor único, como o número de opções de mecanismo de armazenamento e conexão.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b9cef4753b6fd324b38d7254139fe288463a0c0c
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123900"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limites no banco de dados do Azure para PostgreSQL-servidor único
As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados. Se você quiser saber mais sobre as camadas de recurso (computação, memória, armazenamento), consulte o artigo [tipos de preço](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Número máximo de conexões
O número máximo de conexões por tipo de preço e vCores é o seguinte: 

|**Camada de preços**| **vCore(s)**| **Máximo de conexões** | **Máximo de conexões de usuário** |
|---|---|---|---|
|Básica| 1| 55 | 50|
|Básica| 2| 105 | 100|
|Uso geral| 2| 150| 145|
|Uso geral| 4| 250| 245|
|Uso geral| 8| 480| 475|
|Uso geral| 16| 950| 945|
|Uso geral| 32| 1500| 1495|
|Uso geral| 64| 1900| 1895|
|Otimizado para memória| 2| 300| 295|
|Otimizado para memória| 4| 500| 495|
|Otimizado para memória| 8| 960| 955|
|Otimizado para memória| 16| 1900| 1895|
|Otimizado para memória| 32| 1987| 1982|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: já existem muitos clientes

O sistema do Azure exige cinco conexões para monitorar o Banco de Dados do Azure para o servidor PostgreSQL. 

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Atualmente, não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

> Observe que, antes do PostgreSQL versão 10, [a política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número (por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ ).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal (por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ e 10 a 11 é uma atualização de versão _principal_ ).

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizado para memória.

### <a name="restoring-a-server"></a>Restaurando um servidor
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações de camadas de preços que o servidor em que é baseado.
- O novo servidor criado durante uma restauração não tem as regras de firewall que existiam no servidor original. Regras de firewall precisam ser definidas separadamente para esse novo servidor.
- Não há suporte para restaurar um servidor eliminado.

### <a name="utf-8-characters-on-windows"></a>Caracteres UTF-8 no Windows
- Em alguns cenários, não há suporte completo para caracteres UTF-8 no PostgreSQL de software livre no Windows, o que afeta o Banco de Dados do Azure para PostgreSQL. Confira o thread [Bug nº 15476 nos arquivos do PostgreSQL](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
