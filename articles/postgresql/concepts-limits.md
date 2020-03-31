---
title: Limites - Banco de dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve limites no Banco de Dados Azure para PostgreSQL - Single Server, como número de opções de mecanismo de conexão e armazenamento.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836449"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limites no banco de dados Azure para PostgreSQL - Servidor Único
As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados. Se você quiser aprender sobre os níveis de recursos (computação, memória, armazenamento), consulte o artigo [dos níveis de preços.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>Número máximo de conexões
O número máximo de conexões por nível de preço e vCores são mostrados abaixo. O sistema do Azure exige cinco conexões para monitorar o Banco de Dados do Azure para o servidor PostgreSQL. 

|**Nível de preços**| **vCore(s)**| **Máximo de conexões** | **Conexões de usuário máximas** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Uso Geral| 2| 150| 145|
|Uso Geral| 4| 250| 245|
|Uso Geral| 8| 480| 475|
|Uso Geral| 16| 950| 945|
|Uso Geral| 32| 1500| 1495|
|Uso Geral| 64| 1900| 1895|
|Otimizado para memória| 2| 300| 295|
|Otimizado para memória| 4| 500| 495|
|Otimizado para memória| 8| 960| 955|
|Otimizado para memória| 16| 1900| 1895|
|Otimizado para memória| 32| 1987| 1982|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: já existem muitos clientes

> [!IMPORTANT]
> Para obter a melhor experiência, recomendamos que você use um pooler de conexão como o pgBouncer para gerenciar conexões com eficiência.

Uma conexão PostgreSQL, mesmo ociosa, pode ocupar cerca de 10MB de memória. Além disso, criar novas conexões leva tempo. A maioria dos aplicativos solicita muitas conexões de curta duração, o que agrava essa situação. O resultado é menos recursos disponíveis para sua carga de trabalho real, levando a uma diminuição do desempenho. Um pooler de conexões que diminui as conexões ociosas e reutiliza as conexões existentes ajudará a evitar isso. Para saber mais, visite nosso [blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Atualmente, não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

> Note que antes do PostgreSQL versão 10, a [política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão principal_ para ser um aumento no primeiro _ou_ segundo número (por exemplo, 9,5 para 9,6 foi considerado um _grande_ upgrade de versão).
> A partir da versão 10, apenas uma mudança no primeiro número é considerada uma atualização de versão principal (por exemplo, 10.0 para 10.1 é uma atualização de versão _menor,_ e 10 para 11 é uma atualização de versão _principal)._

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="restoring-a-server"></a>Restaurando um servidor
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações de camadas de preços que o servidor em que é baseado.
- O novo servidor criado durante uma restauração não possui as regras de firewall existentes no servidor original. As regras de firewall precisam ser configuradas separadamente para esse novo servidor.
- Não há suporte para restaurar um servidor eliminado.

### <a name="utf-8-characters-on-windows"></a>Caracteres UTF-8 no Windows
- Em alguns cenários, não há suporte completo para caracteres UTF-8 no PostgreSQL de software livre no Windows, o que afeta o Banco de Dados do Azure para PostgreSQL. Confira o thread [Bug nº 15476 nos arquivos do PostgreSQL](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
