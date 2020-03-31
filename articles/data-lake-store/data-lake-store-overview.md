---
title: O que é Azure Data Lake Storage Gen1? | Microsoft Docs
description: Visão geral do Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store), e o valor que ele fornece sobre outros armazenamentos de dados
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118796"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>O que é Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

O Azure Data Lake Storage Gen1 é um repositório de hiperescala em toda a empresa para cargas de trabalho analíticas de big data. O Azure Data Lake permite que você capture dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias.

O Data Lake Storage Gen1 pode ser acessado a partir do Hadoop (disponível com o cluster HDInsight) usando as APIs REST compatíveis com WebHDFS. Ele foi projetado para permitir análises nos dados armazenados e é ajustado para desempenho para cenários de análise de dados. O Data Lake Storage Gen1 inclui todos os recursos de nível corporativo: segurança, capacidade de gerenciamento, escalabilidade, confiabilidade e disponibilidade.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Principais recursos

Alguns dos principais recursos do Data Lake Storage Gen1 incluem o seguinte.

### <a name="built-for-hadoop"></a>Desenvolvido para Hadoop

Data Lake Storage Gen1 é um sistema de arquivos Apache Hadoop compatível com o Hadoop Distributed File System (HDFS), e funciona com o ecossistema Hadoop. Seus aplicativos ou serviços existentes do HDInsight que usam a API WebHDFS podem ser facilmente integrados ao Data Lake Storage Gen1. O Data Lake Storage Gen1 também exibe uma interface REST compatível com WebHDFS para aplicativos.

Você pode analisar facilmente dados armazenados no Data Lake Storage Gen1 usando frameworks analíticos Hadoop, como MapReduce ou Hive. Você pode fornecer clusters Azure HDInsight e configurá-los para acessar diretamente os dados armazenados no Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, arquivos em petabytes

Data Lake Storage Gen1 fornece armazenamento ilimitado e pode armazenar uma variedade de dados para análise. Ele não impõe limites aos tamanhos da conta, tamanhos de arquivos ou a quantidade de dados que podem ser armazenados em um lago de dados. Arquivos individuais podem variar de kilobyte a petabytes em tamanho. Os dados são armazenados duramente fazendo várias cópias. Não há limite de tempo para o qual os dados podem ser armazenados no lago de dados.

### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho ajustado para a análise de big data

O Data Lake Storage Gen1 foi criado para executar sistemas analíticos em larga escala que requerem throughput maciço para consultar e analisar grandes quantidades de dados. O Data Lake espalha partes de um arquivo por vários servidores de armazenamento individuais. Isso melhora a produtividade da leitura do arquivo em paralelo para a realização de análises de dados.

### <a name="enterprise-ready-highly-available-and-secure"></a>Empresa pronta: Altamente disponível e segura

O Data Lake Storage Gen1 fornece disponibilidade e confiabilidade padrão do setor. Seus ativos de dados são armazenados permanentemente por meio de cópias redundantes, a fim de se proteger contra quaisquer falhas inesperadas.

O Data Lake Storage Gen1 também fornece segurança de nível corporativo para os dados armazenados. Para obter mais informações, consulte [Protegendo dados no Armazenamento de dados do Lake Azure Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Todos os dados

Data Lake Storage Gen1 pode armazenar qualquer dado em seu formato nativo, sem exigir quaisquer transformações prévias. O Data Lake Storage Gen1 não exige que um esquema seja definido antes de os dados serem carregados, deixando a estrutura analítica individual interpretar os dados e definir um esquema no momento da análise. A capacidade de armazenar arquivos de tamanhos e formatos arbitrários permite que o Data Lake Storage Gen1 manuseie dados estruturados, semiestruturados e não estruturados.

Os contêineres Data Lake Storage Gen1 para dados são essencialmente pastas e arquivos. Você opera nos dados armazenados usando SDKs, o portal Azure e o Azure Powershell. Se você colocar seus dados na loja usando essas interfaces e usando os recipientes apropriados, você pode armazenar qualquer tipo de dados. O Data Lake Storage Gen1 não executa nenhum tratamento especial de dados com base no tipo de dados que armazena.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Proteção de dados

O Data Lake Storage Gen1 usa o Azure Active Directory (Azure AD) para autenticação e listas de controle de acesso (ACLs) para gerenciar o acesso aos seus dados.

| Recurso | Descrição |
| --- | --- |
| Autenticação |Data Lake Storage Gen1 integra-se ao Azure AD para gerenciamento de identidade e acesso para todos os dados armazenados no Data Lake Storage Gen1. Devido à integração, o Data Lake Storage Gen1 se beneficia de todos os recursos do Azure AD, como autenticação multifatorial, acesso condicional, controle de acesso baseado em função, monitoramento de uso de aplicativos, monitoramento e alerta de segurança, e assim por diante. O Data Lake Storage Gen1 oferece suporte ao protocolo OAuth 2.0 para autenticação na interface REST. Consulte [data lake storage gen1 autenticação](data-lakes-store-authentication-using-azure-active-directory.md).|
| Controle de acesso |O Data Lake Storage Gen1 fornece controle de acesso, suportando permissões no estilo POSIX expostas pelo protocolo WebHDFS. Você pode habilitar ACLs na pasta raiz, em subpastas e em arquivos individuais. Para obter mais informações sobre como as ACLs funcionam no contexto do Data Lake Storage Gen1, consulte [o controle de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Criptografia |Data Lake Storage Gen1 também fornece criptografia para dados armazenados na conta. Você especifica as configurações de criptografia ao criar uma conta do Data Lake Storage Gen1. É possível optar por ter os dados criptografados ou não escolher nenhuma criptografia. Para obter mais informações, consulte [Criptografia no Data Lake Storage Gen1](data-lake-store-encryption.md). Para obter instruções sobre como fornecer configuração relacionada à criptografia, consulte [Comece com o Data Lake Storage Gen1 usando o portal Azure](data-lake-store-get-started-portal.md). |

Para obter instruções sobre como proteger dados no Data Lake Storage Gen1, consulte [Protegendo dados no Armazenamento de dados do Azure Data Lake Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Compatibilidade de aplicativos

O Azure Data Lake Storage Gen1 é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop. Também se integra bem com outros serviços do Azure. Para saber mais sobre como você pode usar o Data Lake Storage Gen1 com componentes de código aberto e outros serviços do Azure, use os seguintes links:

- Confira [Aplicativos e serviços compatíveis com o Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicativos de software livre interoperáveis com o Data Lake Storage Gen1.
- Consulte [Integrando com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para entender como usar o Data Lake Storage Gen1 com outros serviços do Azure para permitir uma ampla gama de cenários.
- Veja [Cenários para usar o Data Lake Storage Gen1](data-lake-store-data-scenarios.md) para aprender a usar o Data Lake Storage Gen1 em cenários como a ingestão de dados, o processamento de dados, o download de dados e a visualização de dados.

## <a name="data-lake-storage-gen1-file-system"></a>Sistema de arquivos Data Lake Storage Gen1

Data Lake Storage Gen1 pode ser acessado através do sistema de arquivos AzureDataLakeFilesystem (adl://) em ambientes Hadoop (disponível com o cluster HDInsight). Aplicativos e serviços que usam adl:// podem aproveitar outras otimizações de desempenho que não estão disponíveis atualmente no WebHDFS. Como resultado, o Data Lake Storage Gen1 oferece a você a flexibilidade de fazer uso do melhor desempenho com a opção recomendada de usar adl:// ou manter o código existente, continuando a usar diretamente a API WebHDFS. O Azure HDInsight aproveita totalmente o AzureDataLakeFilesystem para fornecer o melhor desempenho no Data Lake Storage Gen1.

Você pode acessar seus dados no Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Para obter mais informações sobre como acessar os dados no Data Lake Storage Gen1, consulte [Exibir propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Próximas etapas

- [Comece com data lake storage Gen1 usando o portal Azure](data-lake-store-get-started-portal.md)
- [Comece com data lake storage Gen1 usando .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)