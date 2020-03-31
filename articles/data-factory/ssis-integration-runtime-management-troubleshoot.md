---
title: Soluciona problemas Gerenciamento de tempo de execução de integração do SSIS
description: Este artigo fornece orientação para solução de problemas de gerenciamento do SSIS Integration Runtime (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 52b1d93935e6428563c72361655893ffddf8a507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74941841"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Soluciona problemas Gerenciamento de tempo de execução da integração do SSIS na fábrica de dados do Azure

Este artigo fornece orientação para solução de problemas de gerenciamento no SSIS (Integration Desis) Integration Runtime (IR) do Azure-SQL Server Services (SSIS), também conhecido como SSIS IR.

## <a name="overview"></a>Visão geral

Se você encontrar qualquer problema durante o provisionamento ou desprovisionamento do SSIS IR, você verá uma mensagem de erro no portal Microsoft Azure Data Factory ou um erro retornado de um cmdlet PowerShell. O erro sempre aparece no formato de um código de erro com uma mensagem de erro detalhada.

Se o código de erro for InternalServerError, o serviço tem problemas transitórios e você deve tentar novamente a operação mais tarde. Se uma nova tentativa não ajudar, entre em contato com a equipe de suporte da Fábrica de Dados Do Azure.

Caso contrário, três grandes dependências externas podem causar erros: um servidor de banco de dados SQL do Azure ou uma instância gerenciada, um script de configuração personalizado e uma configuração de rede virtual.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database server ou problemas de instância gerenciada

Um servidor de banco de dados SQL do Azure ou uma instância gerenciada será necessária se você estiver provisionando o SSIS IR com um banco de dados de catálogo do SSIS. O IR do SSIS deve ser capaz de acessar o servidor do banco de dados SQL do Azure ou a instância gerenciada. Além disso, a conta do servidor de banco de dados SQL do Azure ou da instância gerenciada deve ter permissão para criar um SSISDB (banco de dados de catálogo do SSIS). Se houver um erro, um código de erro com uma mensagem de exceção SQL detalhada será mostrado no portal de Data Factory. Use as informações na lista a seguir para solucionar problemas de códigos de erro.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Você poderá ver esse problema quando estiver provisionando um novo IR do SSIS ou enquanto o IR estiver em execução. Se tiver esse erro durante o provisionamento de IR, você poderá obter uma mensagem de SqlException detalhada na mensagem de erro que indica um dos seguintes problemas:

* Um problema de conexão de rede. Verifique se o nome do host da instância gerenciada ou SQL Server está acessível. Verifique também se nenhum firewall ou grupo de segurança de rede (NSG) está bloqueando o acesso do SSIS IR ao servidor.
* Falha no logon durante a autenticação do SQL. A conta fornecida não pode entrar no banco de dados do SQL Server. Certifique-se de fornecer a conta de usuário correta.
* Falha de logon durante a autenticação do Microsoft Azure Active Directory (AD do Azure) (identidade gerenciada). Adicione a identidade gerenciada de sua fábrica a um grupo do AAD e verifique se a identidade gerenciada tem permissões de acesso ao seu servidor de banco de dados de catálogo.
* Tempo limite de conexão. Esse erro é sempre causado por uma configuração relacionada à segurança. É recomendável que você:
  1. Crie uma nova VM.
  1. Junte-se à VM para a mesma Rede Virtual microsoft azure de IR se o IR estiver em uma rede virtual.
  1. Instale o SSMS e verifique o servidor de banco de dados SQL do Azure ou o status de instância gerenciada.

Para outros problemas, corrija o problema mostrado na mensagem de erro de exceção SQL detalhada. Se você ainda tiver problemas, entre em contato com o servidor do banco de dados SQL do Azure ou com a equipe de suporte da instância gerenciada.

Se você vir o erro quando o IR estiver em execução, as alterações de firewall ou grupo de segurança de rede provavelmente impedirão que o nó de trabalho do SSIS IR acesse o servidor do banco de dados SQL ou a instância gerenciada. Libere o nó de trabalho do IR do SSIS para que ele possa acessar o servidor do banco de dados SQL do Azure ou a instância gerenciada.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Eis como esse tipo de mensagem de erro pode parecer: "O banco de dados 'SSISDB' atingiu sua cota de tamanho. Partição ou exclusão de dados, índices de queda ou consultar a documentação para possíveis resoluções." 

Entre as soluções possíveis estão:
* Aumente o tamanho da cota do seu SSISDB.
* Altere a configuração do SSISDB para reduzir o tamanho:
   * Reduzindo o período de retenção e o número de versões do projeto.
   * Reduzindo o período de retenção do log.
   * Alterando o nível padrão do log.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Esse erro significa que o servidor de banco de dados SQL do Azure ou a instância gerenciada já tem um SSISDB e que está sendo usado por outro IR. Você precisa fornecer um servidor de banco de dados SQL do Azure diferente ou uma instância gerenciada ou excluir o SSISDB existente e reiniciar o novo IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Esse problema pode ocorrer por um dos seguintes motivos:

* A conta de usuário configurada para o IR do SSIS não tem permissão para criar o banco de dados. Você pode conceder ao usuário permissão para criar o banco de dados.
* Um tempo limite ocorre durante a criação do banco de dados, como um tempo limite de execução ou um tempo limite de operação de BD. Você deve repetir a operação mais tarde. Se você ainda tiver problemas, entre em contato com o servidor do banco de dados SQL do Azure ou com a equipe de suporte da instância gerenciada.

Para outros problemas, verifique a mensagem de erro de exceção do SQL e corrija o problema mencionado nos detalhes do erro. Se você ainda tiver problemas, entre em contato com o servidor do banco de dados SQL do Azure ou com a equipe de suporte da instância gerenciada.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Esse tipo de mensagem de erro se parece com isso: "Nome de objeto inválido 'catalog.catalog_properties'." Nesta situação, ou você já tem um banco de dados chamado SSISDB, mas não foi criado pelo SSIS IR, ou o banco de dados está em um estado inválido que é causado por erros no último provisionamento de IR ssis. Você pode remover o banco de dados existente com o nome SSISDB ou pode configurar um novo servidor de banco de dados SQL do Azure ou uma instância gerenciada para o IR.

## <a name="custom-setup-issues"></a>Problemas de configuração personalizados

A configuração personalizada fornece uma interface para adicionar suas próprias etapas de configuração durante o provisionamento ou reconfigurar o IR do Azure-SSIS. Para obter mais informações, confira [Configuração personalizada para o Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Verifique se o contêiner contém apenas os arquivos de configuração personalizados necessários; todos os arquivos no contêiner serão baixados no nó de trabalho do SSIS IR. Recomendamos que você teste o script de configuração personalizada em um computador local para corrigir qualquer problema de execução de script antes de executar o script no SSIS IR.

O contêiner de script de configuração personalizada será verificado enquanto o IR estiver em execução, pois o SSIS IR é atualizado regularmente. Essa atualização requer acesso ao contêiner para baixar o script de configuração personalizada e instalá-lo novamente. O processo também verifica se o contêiner está acessível e se o arquivo main.cmd existe.

Para qualquer erro que envolva configuração personalizada, você verá um código de erro CustomSetupScriptFailure com código sub-code como CustomSetupScriptBlobContainerInaccessible ou CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Esse erro significa que o SSIS IR não pode acessar seu contêiner de blob do Azure para a configuração personalizada. Verifique se o URI de SAS do contêiner está acessível e se não expirou.

Pare o IR se ele estiver em execução, reconfigure o IR com o novo URI de SAS do contêiner de configuração personalizada e reinicie o IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Esse erro significa que o SSIS IR não consegue encontrar um script de configuração personalizada (main.cmd) em seu contêiner de BLOBs. Verifique se main.cmd existe no contêiner, que é o ponto de entrada para configuração personalizada da instalação.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Esse erro significa que a execução do script de instalação personalizada (main.cmd) falhou. Experimente o script no computador local primeiro ou verifique os logs de execução da configuração personalizada em seu contêiner de BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Esse erro indica um tempo limite de execução de script de configuração personalizada. Verifique se o script pode ser executado silenciosamente e nenhuma entrada interativa é necessária e se o contêiner de blob contém apenas os arquivos de instalação personalizados necessários. É recomendável testar o script no computador local primeiro. Você também deve verificar os logs de execução da configuração personalizada em seu contêiner de BLOB. O período máximo para a configuração personalizada é de 45 minutos antes de atingir o tempo limite e o período máximo inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no IR do SSIS. Se você precisar de um período mais longo, gere um tíquete de suporte.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Esse erro significa que a tentativa de carregar logs de execução de configuração personalizada para seu contêiner de BLOBs falhou. Esse problema ocorre porque o IR do SSIS não tem permissões de gravação para o contêiner de BLOB ou devido a problemas de armazenamento ou rede. Se a configuração personalizada for bem-sucedida, esse erro não afetará nenhuma função do SSIS, mas os logs estarão ausentes. Se a configuração personalizada falhar com outro erro e o log não for carregado, relataremos esse erro primeiro para que o log possa ser carregado para análise. Além disso, depois que esse problema for resolvido, relataremos quaisquer problemas mais específicos. Se esse problema não for resolvido após uma nova tentativa, entre em contato com a equipe de suporte do Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Quando você ingressa no SSIS IR para a rede virtual do Azure, o SSIS IR usa a rede virtual que está sob a assinatura do usuário. Para obter mais informações, consulte [Unir o Azure-SSIS Integration Runtime a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Quando houver um problema relacionado à rede virtual, você verá um dos erros a seguir.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Esse erro pode ocorrer por vários motivos. Para solucionar problemas, consulte as seções [Proibido](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Proibido

Esse tipo de erro pode se assemelhar a isso: "O SubnetId não está habilitado para conta corrente. O provedor de recursos Microsoft.Batch não está registrado a mesma assinatura do VNet.

Esses detalhes significam que o lote do Azure não pode acessar sua rede virtual. Registre o provedor de recursos Microsoft.Batch na mesma assinatura que a rede virtual.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Esse tipo de erro pode ser semelhante a este: 

- "Ou o VNet especificado não existe ou o serviço Batch não tem acesso a ele."
- "A sub-rede especificada xxx não existe."

Esses erros significam que a rede virtual não existe, o serviço de lote do Azure não pode acessá-la ou a sub-rede fornecida não existe. Verifique se a rede virtual e a sub-rede existem e se o lote do Azure pode acessá-las.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Esse tipo de mensagem de erro pode ser assim: "Falha ao provisionar o Tempo de Execução de Integração no VNet. Se as configurações do servidor DNS ou DO NSG estiverem configuradas, certifique-se de que o servidor DNS esteja acessível e o NSG esteja configurado corretamente."

Nessa situação, você provavelmente tem uma configuração personalizada do servidor DNS ou das configurações de NSG, o que impede que o nome do servidor do Azure exigido pelo SSIS IR seja resolvido ou acessado. Para obter mais informações, consulte [Configuração de rede virtual IR do SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se você ainda tiver problemas, entre em contato com a equipe de suporte do Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

O IR do SSIS será atualizado automaticamente regularmente. Um novo pool do lote do Azure é criado durante a atualização e o pool do lote do Azure antigo é excluído. Além disso, os recursos relacionados à rede virtual para o pool antigo são excluídos e os novos recursos relacionados à rede virtual são criados em sua assinatura. Esse erro significa que a exclusão de recursos relacionados à rede virtual para o pool antigo falhou devido a um bloqueio de exclusão no nível de assinatura ou grupo de recursos. Como o cliente controla e define o bloqueio de exclusão, ele deve remover o bloqueio de exclusão nessa situação.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se o provisionamento de IR do SSIS falhar, todos os recursos que foram criados serão excluídos. No entanto, se houver um bloqueio de exclusão de recurso no nível de assinatura ou grupo de recursos, os recursos de rede virtual não serão excluídos conforme o esperado. Para corrigir esse erro, remova o bloqueio de exclusão e reinicie o IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando você parar o IR do SSIS, todos os recursos relacionados à rede virtual serão excluídos. Mas a exclusão poderá falhar se houver um bloqueio de exclusão de recurso no nível de assinatura ou grupo de recursos. Aqui também, o cliente controla e define o bloqueio de exclusão. Portanto, eles devem remover o bloqueio de exclusão e parar o IR do SSIS novamente.

### <a name="nodeunavailable"></a>NodeUnavailable

Esse erro ocorre quando o IR está em execução e significa que o IR se tornou não íntegro. Esse erro é sempre causado por uma alteração no servidor DNS ou na configuração NSG que impede que o IR do SSIS se conecte a um serviço necessário. Como a configuração do servidor DNS e do NSG é controlada pelo cliente, o cliente deve corrigir os problemas de bloqueio em seu final. Para obter mais informações, consulte [Configuração de rede virtual IR do SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se você ainda tiver problemas, entre em contato com a equipe de suporte do Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configuração de endereços IP públicos estáticos

Quando você se junta ao Ir Azure-SSIS para a Rede Virtual Azure, você também pode trazer seus próprios endereços IP públicos estáticos para o IR para que o IR possa acessar fontes de dados que limitam o acesso a endereços IP específicos. Para obter mais informações, consulte [Unir o Azure-SSIS Integration Runtime a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Além dos problemas de rede virtuais acima, você também pode atender a problemas de endereços IP públicos estáticos. Verifique os seguintes erros para obter ajuda.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPEspecificado

Esse erro pode ocorrer por uma variedade de razões ao iniciar o IR Azure-SSIS:

| Mensagem de erro | Solução|
|:--- |:--- |
| O endereço IP público estático fornecido já está sendo usado, por favor, forneça dois não utilizados para o seu Tempo de Execução de Integração Azure-SSIS. | Você deve selecionar dois endereços IP públicos estáticos não utilizados ou remover referências atuais ao endereço IP público especificado e, em seguida, reiniciar o IR Azure-SSIS. |
| O endereço IP público estático fornecido não tem nome DNS, por favor forneça a dois deles o nome DNS para o seu Tempo de Execução de Integração Azure-SSIS. | Você pode configurar o nome DNS do endereço IP público no portal Azure, como mostra a imagem abaixo. As etapas específicas são as seguintes: (1) Portal Open Azure e acesse a página de recursos deste endereço IP público; (2) Selecione a seção **Configuração** e configure o nome DNS e clique **em Salvar** botão; (3) Reinicie seu IR Azure-SSIS. |
| Os endereços IP públicos estáticos fornecidos para o Seu Tempo de Execução de Integração Azure-SSIS devem estar no mesmo local. | De acordo com os requisitos da Rede Azure, o endereço IP público estático e a rede virtual devem estar no mesmo local e assinatura. Por favor, forneça dois endereços IP públicos estáticos válidos e reinicie o IR Azure-SSIS. |
| O endereço IP público estático fornecido é básico, por favor, forneça dois padrão para o seu Tempo de Execução de Integração Azure-SSIS. | Consulte [sKUs de endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) para obter ajuda. |

![IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Se o provisionamento de IR do Azure-SSIS falhar, todos os recursos que foram criados serão excluídos. No entanto, se houver um bloqueio de exclusão de recursos no nível de assinatura ou grupo de recursos (que contém seu endereço IP público estático), os recursos da rede não serão excluídos como esperado. Para corrigir o erro, remova o bloqueio de exclusão e reinicie o IR.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Quando você parar o Azure-SSIS IR, todos os recursos de rede criados no grupo de recursos que contém seu endereço IP público serão excluídos. Mas a exclusão pode falhar se houver um bloqueio de exclusão de recursos no nível de assinatura ou grupo de recursos (que contém seu endereço IP público estático). Por favor, remova o bloqueio de exclusão e reinicie o IR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

O Azure-SSIS IR é atualizado automaticamente regularmente. Novos nós de IR são criados durante a atualização e os nós antigos serão excluídos. Além disso, os recursos de rede criados (por exemplo, o balanceador de carga e o grupo de segurança da rede) para os nós antigos são excluídos, e os novos recursos de rede são criados sua assinatura. Esse erro significa que a exclusão dos recursos de rede para os nós antigos falhou devido a um bloqueio de exclusão no nível de assinatura ou grupo de recursos (que contém o seu endereço IP público estático). Por favor, remova o bloqueio de exclusão para que possamos limpar os nós antigos e liberar o endereço IP público estático para os nós antigos. Caso contrário, o endereço IP público estático não pode ser liberado e não poderemos atualizar ainda mais o seu IR.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotusableDuringUpgrade

Quando você quiser trazer seus próprios endereços IP públicos estáticos, dois endereços IP públicos devem ser fornecidos. Um deles será usado para criar os nódulos de IR imediatamente e outro será usado durante a atualização do IR. Esse erro pode ocorrer quando o outro endereço IP público estiver inutilizável durante a atualização. Consulte [InvalidPublicIPEspecificado](#InvalidPublicIPSpecified) para possíveis causas.