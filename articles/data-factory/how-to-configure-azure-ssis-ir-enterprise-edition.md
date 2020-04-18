---
title: Provisionar Enterprise Edition para o Integration Runtime do Azure-SSIS
description: Este artigo descreve os recursos do Enterprise Edition para o Integration Runtime do Azure-SSIS e como provisioná-lo
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: a0e35a309fd30a36263b498b6a8e74a312004a99
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606166"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Provisionar Enterprise Edition para o Integration Runtime do Azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Enterprise Edition do Microsoft Integration Runtime do Azure-SSIS permite que você use os recursos avançados e premium a seguir:
-   Componentes da Captura de Dados de Alterações (CDA)
-   Conectores do Oracle, Teradata e SAP BW
-   Conectores e transformações do SQL Server Analysis Services (SSAS) e do Azure Analysis Services (AAS)
-   Transformações agrupamento difuso e pesquisa difusa
-   Transformações de pesquisa de extração e de termos

Alguns desses recursos exigem a instalação de componentes adicionais para personalizar o Tempo de Execução de Integração do Azure-SSIS. Para obter mais informações sobre como instalar componentes adicionais, consulte [Instalação personalizada para o runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Recursos da empresa

| **Recursos da Empresa** | **Descrições** |
|---|---|
| Componentes da CDA | A fonte da CDC, a tarefa de controle e a transformação de divisor são pré-instaladas no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Para se conectar ao Oracle, você também precisa instalar o CDC Designer e o serviço em outro computador. |
| Conectores Oracle | O Gerenciador de Conexões da Oracle, a fonte e o destino são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Você também precisa instalar o driver Oracle Call Interface (OCI) e, se necessário, configurar o Oracle Transport Network Substrate (TNS), no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Para obter mais informações, consulte [Instalação personalizada para o runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores de teradata | Você precisa instalar o Gerenciador de Conexões Teradata, a fonte e o destino, bem como a API Teradata Parallel Transporter (TPT) e o driver Teradata ODBC, no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Para obter mais informações, consulte [Instalação personalizada para o runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores SAP BW | O Gerenciador de Conexões do SAP BW, a fonte e o destino são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Você também precisa instalar o driver do SAP BW no Tempo de Execução de Integração do Azure-SSIS. Esses conectores oferecem suporte a SAP BW 7.0 ou versões anteriores. Para se conectar a versões posteriores do SAP BW ou outros produtos SAP, você pode comprar e instalar conectores do SAP de ISVs de terceiros no Tempo de Execução de Integração do Azure-SSIS. Para obter mais informações sobre como instalar componentes adicionais, consulte [Instalação personalizada para o runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Componentes do Analysis Services               | O destino de treinamento do modelo de mineração de dados, o destino do processamento de dimensão e o destino de processamento de partições, além da transformação de consulta de mineração de dados, são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Todos esses componentes oferecem suporte a SQL Server Analysis Services (SSAS), mas apenas o destino de processamento de partições oferece suporte ao Azure Analysis Services (AAS). Para se conectar ao SSAS, você também precisa [configurar as credenciais de autenticação do Windows no SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Além desses componentes, a tarefa do Analysis Services Execute DDL, a tarefa de processamento do Analysis Services e a tarefa de consulta de mineração de dados também são pré-instalados no Tempo de Execução de Integração do Azure-SSIS Standard/Enterprise Edition. |
| Transformações agrupamento difuso e pesquisa difusa  | As transformações de agrupamento difuso e pesquisa difusa são pré-instaladas no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Esses componentes oferecem suporte a SQL Server e banco de dados SQL do Azure para armazenar dados de referência. |
| Transformações de pesquisa de extração e de termos | As transformações de extração de termos e pesquisa de termos são pré-instaladas no Tempo de Execução de Integração do Azure-SSIS Enterprise Edition. Esses componentes oferecem suporte a SQL Server e banco de dados SQL do Azure para armazenar dados de referência. |

## <a name="instructions"></a>Instruções

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Baixe e instale o [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Quando você configurar ou reconfigurar o Tempo de Execução de Integração do Azure-SSIS com o PowerShell, execute `Set-AzDataFactoryV2IntegrationRuntime` com **Enterprise** como o valor para o parâmetro **Edição** antes de iniciar o Tempo de Execução de Integração do Azure-SSIS. Veja abaixo um script de exemplo:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Próximas etapas

-   [Instalação personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o runtime de integração do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
