---
title: Conexão Azure SQL VM para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite conexões criptografadas e configure o firewall para permitir conexões ao SQL Server em uma máquina virtual Azure (VM) a partir de um indexador na Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1ab2b7860e8a75da5f8acef2fc4fa54d4b73a30d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256956"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configure uma conexão de um indexador de pesquisa cognitiva do Azure ao SQL Server em um VM Azure

Como observado no [ConnectIng Azure SQL Database to Azure Cognitive Search usando indexadores,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)a criação de indexadores contra **o SQL Server em VMs Azure** (ou **VMs SQL Azure** para abreviar) é suportada pelo Azure Cognitive Search, mas existem alguns pré-requisitos relacionados à segurança para cuidar primeiro. 

As conexões desde a Pesquisa Cognitiva do Azure até o SQL Server em uma VM é uma conexão pública com a internet. Todas as medidas de segurança que você normalmente seguiria para essas conexões se aplicam aqui também:

+ Obter um certificado de um [provedor de Autoridade de Certificação](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio totalmente qualificado da instância do SQL Server na VM do Azure.
+ Instalar o certificado na VM e, em seguida, habilitar e configurar conexões criptografadas na VM usando as instruções neste artigo.

## <a name="enable-encrypted-connections"></a>Habilitar conexões criptografadas
O Azure Cognitive Search requer um canal criptografado para todas as solicitações de indexadores por uma conexão pública com a internet. Esta seção lista as etapas para fazer isso funcionar.

1. Verifique as propriedades do certificado para verificar se o nome da entidade é o nome de domínio totalmente qualificado (FQDN) da VM do Azure. Você pode usar uma ferramenta como CertUtils ou o snap-in de certificados para exibir as propriedades. Você pode obter o FQDN na seção Essentials da folha de serviço da VM, no campo **rótulo de nome de DNS/endereço IP público** , no [portal do Azure](https://portal.azure.com/).
   
   * Para VMs criadas usando o mais recente modelo do **Resource Manager**, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`
   * Para VMs antigas criadas como uma VM **clássica**, o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`.

2. Configure o SQL Server para usar o certificado usando o Editor do Registro (regedit). 
   
    Embora o SQL Server Configuration Manager seja geralmente usado para essa tarefa, você não pode usá-lo para esse cenário. Ele não localizará o certificado importado porque o FQDN da VM no Azure não corresponde ao FQDN conforme determinado pela VM (ele identifica o domínio como o computador local ou domínio de rede ao qual ele está associado). Quando os nomes não corresponderem, use regedit para especificar o certificado.
   
   * No regedit, navegue até esta chave do registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     A parte `[MSSQL13.MSSQLSERVER]` varia conforme o nome de da instância e da versão. 
   * Defina o valor da chave **Certificado** para a **impressão digital** do certificado TLS/SSL que você importou para a VM.
     
     Há várias maneiras de obter a impressão digital, algumas melhores do que outras. Se você copiá-la a partir do snap-in de **certificados** do MMC, você provavelmente selecionará um caractere à esquerda invisível [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta em um erro quando tentar uma conexão. Existem várias soluções alternativas para corrigir esse problema. A maneira mais fácil é a apagar e digitar novamente o primeiro caractere da impressão digital para remover o caractere à esquerda no campo de valor de chave no regedit. Como alternativa, você pode usar outra ferramenta para copiar a impressão digital.

3. Conceder permissões à conta de serviço. 
   
    Certifique-se de que a conta de serviço do SQL Server recebe permissão apropriada na chave privada do certificado TLS/SSL. Se você ignorar essa etapa, o SQL Server não será iniciado. Você pode usar o snap-in **Certificados** ou o **CertUtils** para essa tarefa.
    
4. Reinicie o serviço SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade do SQL Server na VM
Depois de configurar a conexão criptografada exigida pelo Azure Cognitive Search, existem etapas adicionais de configuração intrínsecas ao SQL Server nas VMs do Azure. Se você ainda não fez isso, a próxima etapa é concluir a configuração usando qualquer um destes artigos:

* Para uma VM do **Resource Manager** , veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure usando o Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Para uma VM **Clássica** , consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure clássico](../virtual-machines/windows/classic/sql-connect.md).

Em particular, consulte a seção em cada artigo para "conectando pela internet".

## <a name="configure-the-network-security-group-nsg"></a>Configure o Grupo de Segurança de Rede (NSG)
É comum configurar o NSG e o ponto de extremidade ou a ACL (Lista de Controle de Acesso) do Azure correspondente para dar acesso à sua VM do Azure a outras pessoas. É provável que você já tenha feito isso para permitir que a lógica do seu próprio aplicativo se conecte à VM do SQL Azure. Não é diferente para uma conexão de Pesquisa Cognitiva Azure com o seu VM SQL Azure. 

Os links a seguir fornecem instruções sobre a configuração de NSG para implantações de VM. Use essas instruções para acl um ponto final de pesquisa cognitiva do Azure com base em seu endereço IP.

> [!NOTE]
> Para saber mais, consulte [O que é um Grupo de Segurança de Rede?](../virtual-network/security-overview.md)
> 
> 

* Para uma VM do **Gerenciador de Recursos** , consulte [Como criar NSGs para implantações de ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Para uma VM **Clássica** , consulte [Como criar NSGs para implantações Clássicas](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

O endereçamento IP pode apresentar alguns desafios que são facilmente superados se você estiver ciente do problema e possíveis soluções alternativas. As seções restantes fornecem recomendações para lidar com problemas relacionados a endereços IP na ACL.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restringir o acesso à Pesquisa Cognitiva do Azure
Recomendamos fortemente que você restrinja o acesso ao endereço IP `AzureCognitiveSearch` do seu serviço de pesquisa e à faixa de endereço IP da tag de [serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) na ACL, em vez de tornar suas VMs SQL Azure abertas a todas as solicitações de conexão.

Você pode descobrir o endereço IP pingando o FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

Você pode descobrir a faixa `AzureCognitiveSearch` [de](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) serviço do endereço IP usando [arquivos JSON para download](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) ou através da [API de detecção](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)de marca de serviço . A gama de endereços IP é atualizada semanalmente.

#### <a name="managing-ip-address-fluctuations"></a>Gerenciando flutuações de endereço IP
Se o seu serviço de pesquisa tiver apenas uma unidade de pesquisa (isto é, uma réplica e uma partição), o endereço IP poderá mudar durante a reinicialização do serviço de rotina, invalidando uma ACL existente com o endereço IP do seu serviço de pesquisa.

Uma maneira de evitar o erro de conectividade subseqüente é usar mais de uma réplica e uma partição na Pesquisa Cognitiva do Azure. Isso aumenta o custo, mas também resolve o problema de endereço IP. Na Pesquisa Cognitiva do Azure, os endereços IP não mudam quando você tem mais de uma unidade de pesquisa.

Uma segunda abordagem é permitir que a conexão falhe e, em seguida, reconfigurar as ACLs no NSG. Em média, você pode esperar que os endereços IP sejam alterados todas as semanas. Para clientes que fazem a indexação controlada raramente, essa abordagem pode ser viável.

Uma terceira abordagem viável (mas não particularmente segura) é especificar o intervalo de endereços IP da região do Azure em que o serviço de pesquisa é provisionado. A lista de intervalos IP do qual os endereços IP públicos são alocados a recursos do Azure é publicada em [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Inclua os endereços IP do portal de pesquisa cognitiva do Azure
Se você estiver usando o portal Azure para criar um indexador, a lógica do portal de busca cognitiva do Azure também precisa acessar seu VM SQL Azure durante o tempo de criação. Os endereços IP do portal de pesquisa `stamp2.search.ext.azure.com`cognitiva do Azure podem ser encontrados pingando .

## <a name="next-steps"></a>Próximas etapas
Com a configuração fora do caminho, agora você pode especificar um SQL Server no Azure VM como a fonte de dados de um indexador de pesquisa cognitiva do Azure. Consulte [Conectando o banco de dados SQL do Azure à Pesquisa Cognitiva do Azure usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.

