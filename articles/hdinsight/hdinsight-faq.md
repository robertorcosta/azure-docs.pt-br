---
title: Azure HDInsight perguntas freqüentemente feitas
description: Perguntas frequentes sobre HDInsight
keywords: perguntas freqüentemente feitas, faq
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a37e1b9bc4a0b953dc727dbab2813dd938ed576
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652227"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Perguntas frequentes

Este artigo fornece respostas para algumas das perguntas mais comuns sobre como executar [o Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Como criar ou excluir clusters HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Como provisão um cluster HDInsight?

Para revisar os tipos de clusters HDInsight disponíveis e os métodos de provisionamento, consulte [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Como excluir um cluster HDInsight existente?

Para saber mais sobre a exclusão de um cluster quando ele não estiver mais em uso, consulte [Excluir um cluster HDInsight](hdinsight-delete-cluster.md).

Deixe pelo menos 30 a 60 minutos entre criar e excluir as operações. Caso contrário, a operação pode falhar com a seguinte mensagem de erro:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Como seleciono o número correto de núcleos ou nós para minha carga de trabalho?

O número adequado de núcleos e outras opções de configuração dependem de vários fatores.

Para obter mais informações, consulte [O planejamento de capacidade para clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>O que posso fazer quando o provisionamento de cluster falhar devido a um problema de capacidade?

Erros comuns de problemade capacidade e técnicas de mitigação são fornecidos nesta seção.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Erro: A implantação excederia a cota de '800'

O Azure tem um limite de cota de 800 implantações por grupo de recursos. Diferentes cotas são aplicadas por grupo de recursos, assinatura, conta ou outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Se você tentar implantar uma máquina virtual que tenha mais núcleos do que o valor permitido, você receberá uma mensagem de erro que afirma que a cota foi excedida.

Para resolver esse problema, exclua as implantações que não são mais necessárias usando o portal Azure, CLI ou PowerShell.

Para obter mais informações, consulte [Solucionar erros de cotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Erro: O nó máximo excedeu os núcleos disponíveis nesta região

Sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Se você tentar implantar um recurso que tenha mais núcleos do que o valor permitido, você receberá uma mensagem de erro que afirma que a cota foi excedida.

Para solicitar um aumento de cota, siga essas etapas:

1. Vá para o [portal Azure](https://portal.azure.com)e selecione **Ajuda + suporte**.
   
1. Selecione **Nova solicitação de suporte**.
   
1. Na guia Noções básicas da página **De solicitação de suporte Novo,** forneça as **seguintes** informações:
   
   - **Tipo de problema:** Selecione **limites de serviço e assinatura (cotas)**.
   - **Assinatura:** Selecione a assinatura que deseja modificar.
   - **Tipo de cota:** Selecione **HDInsight**.

Para obter mais informações, consulte [Criar um tíquete de suporte para aumentar o núcleo](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quais são os vários tipos de nós em um cluster HDInsight?

Os clusters Azure HDInsight têm diferentes tipos de máquinas virtuais ou nós. Cada tipo de nó desempenha um papel na operação do sistema.

Para obter mais informações, consulte [os tipos de recursos nos clusters Azure HDInsight](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Componentes individuais

### <a name="can-i-install-additional-components-on-my-cluster"></a>Posso instalar componentes adicionais no meu cluster?

Sim. Para instalar componentes adicionais ou personalizar a configuração do cluster, use:

- Scripts durante ou após a criação. Os scripts são invocados via [ação de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), que é uma opção de configuração que você pode usar a partir do portal Azure, cmdlets HDInsight Windows PowerShell ou do HDInsight .NET SDK. Esta opção de configuração pode ser usada a partir do portal Azure, cmdlets HDInsight Windows PowerShell ou do HDInsight .NET SDK.

- [Plataforma de aplicativos HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) para instalar aplicativos de ecossistema.

Para obter uma lista de componentes suportados, [consulte Quais são os componentes e versões do Apache Hadoop disponíveis no HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Posso atualizar os componentes individuais pré-instalados no cluster?

Se você atualizar componentes ou aplicativos incorporados pré-instalados em seu cluster, a configuração resultante não será suportada pela Microsoft. Essas configurações do sistema não foram testadas pela Microsoft. Tente usar uma versão diferente do cluster HDInsight que pode já ter a versão atualizada do componente pré-instalado.

Por exemplo, não é suportado o upgrade da Colmeia como um componente individual. HdInsight é um serviço gerenciado, e muitos serviços são integrados com o servidor Ambari e testados. Atualizar uma Colmeia por si só faz com que os binários indexados de outros componentes mudem e causará problemas de integração de componentes em seu cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark e Kafka podem ser executados no mesmo cluster HDInsight?

Não, não é possível executar Apache Kafka e Apache Spark no mesmo cluster HDInsight. Crie clusters separados para Kafka e Spark para evitar problemas de contenção de recursos.

### <a name="how-do-i-change-timezone-in-ambari"></a>Como mudo o fuso horário em Ambari?

1. Abra a UI da `https://CLUSTERNAME.azurehdinsight.net`Web Ambari em , onde CLUSTERNAME é o nome do seu cluster.
2. No canto superior direito, selecione o admin | Configurações. 

   ![Configurações ambari](media/hdinsight-faq/ambari-settings.png)

3. Na janela Configurações do usuário, selecione o novo fuso horário do fuso horário a ser derrubado e clique em Salvar.

   ![Configurações do usuário Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Como posso migrar do metastore existente para o Azure SQL Server? 

Para migrar do SQL Server para o Azure SQL Server, consulte [Tutorial: Migre o SQL Server para um único banco de dados ou banco de dados agrupado no Banco de Dados SQL do Azure offline usando DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>O metastore da Colmeia é excluído quando o cluster é excluído?

Depende do tipo de metastore que seu cluster está configurado para usar.

Para um metastore padrão: o metastore padrão faz parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore e os metadados correspondentes também são excluídos.

Para um metastore personalizado: o ciclo de vida do metastore não está vinculado ao ciclo de vida de um cluster. Portanto, você pode criar e excluir clusters sem perder metadados. Metadados como seus esquemas de Colméia persistem mesmo depois de você excluir e recriar o cluster HDInsight.

Para obter mais informações, consulte [Usar armazenamentos de metadados externos no Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>A migração de um metastore da Colmeia também migra as políticas padrão do banco de dados ranger?

Não, a definição de política está no banco de dados dos Rangers, então migrar o banco de dados dos Rangers vai migrar sua política.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Você pode migrar um metastore do Hive de um cluster Enterprise Security Package (ESP) para um cluster não-ESP e vice-versa?

Sim, você pode migrar um metastore hive de um ESP para um cluster não-ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Como posso estimar o tamanho de um banco de dados de metaloja da Colmeia?

Um metastore da Colmeia é usado para armazenar os metadados para fontes de dados que são usadas pelo servidor Hive. Os requisitos de tamanho dependem em parte do número e complexidade de suas fontes de dados da Colmeia, e não podem ser estimados antecipadamente. Como descrito nas diretrizes do [metastore da Hive,](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)você pode começar com um nível S2, que fornece 50 DTU e 250 GB de armazenamento, e se você ver um gargalo, você pode aumentar o banco de dados.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Você suporta qualquer outro banco de dados que não seja o Azure SQL Database como um metastore externo?

Não, a Microsoft suporta apenas o Azure SQL Database como um metastore personalizado externo.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Posso compartilhar uma metaloja em vários clusters?

Sim, você pode compartilhar metastore personalizado em vários clusters, desde que eles estejam usando a mesma versão do HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Conectividade e redes virtuais  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quais são as implicações do bloqueio das portas 22 e 23 na minha rede?

Se você bloquear as portas 22 e a porta 23, você não terá acesso ssh ao cluster. Essas portas não são usadas pelo serviço HDInsight.

Para obter mais informações, consulte um dos seguintes documentos:

- [Controlando o tráfego da rede](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Tráfego de entrada seguro para clusters HDInsight em uma rede virtual com ponto final privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Endereços IP de gerenciamento HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Posso implantar uma máquina virtual adicional dentro da mesma sub-rede que um cluster HDInsight?

Sim, você pode implantar uma máquina virtual adicional dentro da mesma sub-rede que um cluster HDInsight. As seguintes configurações são possíveis:

- Nó de borda: Você pode adicionar outro nó de borda ao cluster, conforme descrito em [Usar nós de borda vazios em clusters Apache Hadoop no HDInsight](hdinsight-apps-use-edge-node.md).

- Nóautônomo: Você pode adicionar uma máquina virtual autônoma à mesma sub-rede e acessar o cluster `https://<CLUSTERNAME>-int.azurehdinsight.net`a partir dessa máquina virtual usando o ponto final privado . Para obter mais informações, consulte [Controlando o tráfego da rede](hdinsight-plan-virtual-network-deployment.md#networktraffic).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Devo armazenar dados no disco local de um nó de borda?

Não, armazenar dados em um disco local não é uma boa idéia. Se o nó falhar, todos os dados armazenados localmente serão perdidos. Recomendamos armazenar dados no armazenamento Azure Data Lake Storage Gen2 ou No Zure Blob, ou montando um compartilhamento de Arquivos Azure para armazenar os dados.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Posso adicionar um cluster HDInsight existente a outra rede virtual?

Não, você não pode. A rede virtual deve ser especificada no momento do provisionamento. Se nenhuma rede virtual for especificada durante o provisionamento, a implantação criará uma rede interna que não está acessível de fora. Para obter mais informações, consulte [Adicionar HDInsight a uma rede virtual existente](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Segurança e certificados

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Quais são as recomendações para proteção contra malware nos clusters Azure HDInsight?

Para obter informações sobre proteção contra malware, consulte [o Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Como criar uma guia de tecla para um cluster HDInsight ESP?

Crie uma aba-chave Kerberos para o nome de usuário do seu domínio. Mais tarde, você pode usar essa guia de tecla para autenticar em clusters remotos unidos por domínio sem inserir uma senha. O nome de domínio é maiúsculo:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Posso usar um inquilino existente do Azure Active Directory para criar um cluster HDInsight que tenha o ESP?

Você deve habilitar o Azure Active Directory Domain Services (Azure AD DS) antes de criar um cluster HDInsight com ESP. Hadoop de código aberto conta com Kerberos para autenticação (em oposição a OAuth).

Para juntar VMs a um domínio, você deve ter um controlador de domínio. O Azure AD DS é o controlador de domínio gerenciado e é considerado uma extensão do Azure Active Directory que fornece todos os requisitos do Kerberos para construir um cluster Hadoop seguro de forma gerenciada. O HDInsight como serviço gerenciado integra-se ao Azure AD DS para fornecer segurança de ponta a ponta.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Posso usar um certificado auto-assinado em uma configuração LDAP segura AAD-DS e provisionar um cluster ESP?

Recomenda-se o uso de um certificado emitido por uma autoridade certificadora, mas o uso de um certificado auto-assinado também é suportado no ESP. Para obter mais informações, consulte:

- [Habilitar o Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Tutorial: Configure lDAP seguro para um domínio gerenciado do Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Como posso puxar a atividade de login mostrada no Ranger?

Para atender aos requisitos, a Microsoft recomenda habilitar os logs do Monitor Do Azure conforme descrito nos [registros do Use Azure Monitor para monitorar os clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Posso desativar clamscan no meu cluster?

Clamscan é o software antivírus que é executado no cluster HDInsight e é usado pela segurança do Azure (azsecd) para proteger seus clusters contra ataques de vírus. A Microsoft recomenda fortemente que os usuários se abstenham de fazer quaisquer alterações na configuração padrão do Clamscan.

Esse processo não interfere ou tira nenhum ciclo de outros processos. Sempre cederá a outros processos. Os picos de CPU de Clamscan só devem ser vistos quando o sistema estiver ocioso.  

Em cenários em que você deve controlar o cronograma, você pode usar as seguintes etapas:

1. Desativar a execução automática usando o seguinte comando:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Adicione um trabalho Cron que execute o seguinte comando como raiz:
   
   `/usr/local/bin/azsecd manual -s clamav`

Para obter mais informações sobre como configurar e executar um trabalho de cron, [veja como faço para configurar um trabalho Cron](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Por que o LLAP está disponível em clusters Spark ESP?
Nos clusters ESP Spark, o LLAP é habilitado por razões de segurança (ou seja, Apache Ranger), não por desempenho. Você deve usar VMs de nó maior para acomodar o uso de recursos do LLAP (por exemplo, D13V2 mínimo). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Como posso adicionar grupos AAD adicionais depois de criar um cluster ESP?
Há duas maneiras de conseguir isso: 1- Você pode recriar o cluster e adicionar o grupo adicional no momento da criação do cluster. Se você estiver usando sincronização escopo em AAD-DS, certifique-se de que o grupo B está incluído na sincronização escopo.
2- Adicione o grupo como um subgrupo aninhado do grupo anterior que foi usado para criar o cluster ESP. Por exemplo, se você criou um cluster `A`ESP com grupo, você pode adicionar grupo `B` mais tarde como um subgrupo aninhado de e depois de `A` aproximadamente uma hora ele será sincronizado e disponível no cluster automaticamente. 

## <a name="storage"></a>Armazenamento

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Posso adicionar um Azure Data Lake Storage Gen2 a um cluster HDInsight existente como uma conta de armazenamento adicional?

Não, atualmente não é possível adicionar uma conta de armazenamento Azure Data Lake Storage Gen2 a um cluster que tem o armazenamento blob como seu armazenamento principal. Para obter mais informações, consulte [Comparar opções de armazenamento](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Como posso encontrar o diretor de serviço atualmente vinculado para uma conta de armazenamento do Data Lake?

Você pode encontrar suas configurações no **acesso Data Lake Storage Gen1** sob suas propriedades de cluster no portal Azure. Para obter mais informações, consulte [Verificar configuração de cluster](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Como posso calcular o uso de contas de armazenamento e recipientes blob para meus clusters HDInsight?

Realize um dos seguintes procedimentos:

- [Usar o PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Encontre o tamanho do */user/hive/. Lixo/pasta* no cluster HDInsight, usando a seguinte linha de comando:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Como posso configurar a auditoria para minha conta de armazenamento blob?

Para auditar contas de armazenamento blob, configure o monitoramento usando o procedimento no [Monitor de uma conta de armazenamento no portal Azure](../storage/common/storage-monitor-storage-account.md). Um registro de auditoria hdfs fornece apenas informações de auditoria apenas para o sistema de arquivos HDFS local apenas (hdfs://mycluster).  Ele não inclui operações que são feitas em armazenamento remoto.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Como posso transferir arquivos entre um recipiente blob e um nó de cabeça HDInsight?

Execute um script semelhante ao seguinte script shell no nó da cabeça:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> O *filefilenames.txt* terá o caminho absoluto dos arquivos nos recipientes blob.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Há algum plugin ranger para armazenamento?

Atualmente, não existe nenhum plugin Ranger para armazenamento blob e Azure Data Lake Storage Gen1 ou Gen2. Para clusters ESP, você deve usar o Azure Data Lake Storage, porque você pode pelo menos definir permissões de grãos finos manualmente no nível do sistema de arquivos usando ferramentas HDFS. Além disso, ao usar o Azure Data Lake Storage, os clusters ESP farão parte do controle de acesso ao sistema de arquivos usando o Azure Active Directory no nível de cluster. 

Você pode atribuir políticas de acesso de dados aos grupos de segurança de seus usuários usando o Azure Storage Explorer. Para obter mais informações, consulte:

- [Como definir permissões para usuários do Azure AD consultar dados no Data Lake Storage Gen2 usando hive ou outros serviços?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Definir permissões no nível do arquivo e do diretório usando o Gerenciador de Armazenamento do Azure com o Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Posso aumentar o armazenamento HDFS em um cluster sem aumentar o tamanho do disco dos nós do trabalhador?

Não, você não pode aumentar o tamanho do disco de qualquer nó do trabalhador, então a única maneira de aumentar o tamanho do disco é soltar o cluster e recriá-lo com VMs maiores do trabalhador. Não use o HDFS para armazenar qualquer um dos seus dados do HDInsight, porque os dados são excluídos se você excluir seu cluster. Em vez disso, armazene seus dados no Azure. O dimensionamento do cluster também pode adicionar capacidade adicional ao seu cluster HDInsight.

## <a name="edge-nodes"></a>Nós de borda

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Posso adicionar um nó de borda depois que o cluster foi criado?

Cluster HDInsight ou para um novo cluster ao criar o cluster. Para obter mais informações, confira o artigo [Usar nós de borda vazios em clusters Apache Hadoop no HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Como posso me conectar a um nó de borda?

Depois de criar um nó de borda, você pode se conectar a ele usando SSH na porta 22. Você pode encontrar o nome do nó de borda a partir do portal do cluster. Os nomes geralmente terminam com *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Por que os scripts persistidos não são executados automaticamente em nós de borda recém-criados?

Você usa scripts persistidos para personalizar novos nós de trabalhador adicionados ao cluster através de operações de dimensionamento. Scripts persistidos não se aplicam a nós de borda.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Quais são as chamadas da API REST para puxar uma visualização de consulta Tez do cluster?

Você pode usar os seguintes pontos finais REST para puxar as informações necessárias no formato JSON. Use cabeçalhos básicos de autenticação para fazer as solicitações.

- Exibição de tez query: *https:\//\<nome de cluster>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Tez Dag View: *https:\//\<nome de cluster>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Como recuperar os detalhes de configuração do cluster HDI usando um usuário do Azure Active Directory?

Para negociar tokens de autenticação adequados com seu usuário AAD, passe pelo gateway usando o seguinte formato:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Como usar a API Ambari Restful para monitorar o desempenho do YARN?

Se você chamar o comando Curl na mesma rede virtual ou em uma rede virtual peered, o comando será:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Se você chamar o comando de fora da rede virtual ou de uma rede virtual não-peered, o formato de comando será:

- Para um cluster não-ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Para um cluster ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl pedirá uma senha. Você deve inserir uma senha válida para o nome de usuário de login do cluster.

## <a name="billing"></a>Cobrança

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Quanto custa para implantar um cluster HDInsight?

Para obter mais informações sobre preços e perguntas frequentes relacionadas ao faturamento, consulte a página de preços do [Azure HDInsight.](https://azure.microsoft.com/pricing/details/hdinsight/)

### <a name="when-does-hdinsight-billing-start--stop"></a>Quando o faturamento do HDInsight começa & parar?

A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. O faturamento é proporcional por minuto.

### <a name="how-do-i-cancel-my-subscription"></a>Como cancelar minha assinatura?

Para obter informações sobre como cancelar sua assinatura, consulte [Cancelar sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Para assinaturas de pagamento, o que acontece depois que eu cancelar minha assinatura?

Para obter informações sobre sua assinatura depois que ela é cancelada, veja [o que acontece depois que eu cancelar minha assinatura?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Por que a versão hive aparece como 1.2.1000 em vez de 2.1 na Ambari UI mesmo que eu esteja executando um cluster HDInsight 3.6?

Embora apenas 1.2 apareça na Ambari UI, o HDInsight 3.6 contém tanto a Colmeia 1.2 quanto a Colmeia 2.1.

## <a name="other-faq"></a>Outras perguntas frequentes

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>O que o HDInsight oferece em termos de recursos de processamento de fluxo em tempo real?

Para obter informações sobre os recursos de integração do processamento de fluxo no Azure HDInsight, consulte [Escolhendo uma tecnologia de processamento de fluxo no Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Existe uma maneira de terminar dinamicamente o nó de cabeça do cluster quando o cluster está ocioso por um período específico?

Você não pode fazer isso com clusters HDInsight. Você pode usar a Fábrica de Dados Do Azure para esses cenários.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Que ofertas de conformidade o HDInsight oferece?

Para obter informações de conformidade, consulte o [Microsoft Trust Center](https://www.microsoft.com/trust-center) e a visão geral da conformidade do Microsoft [Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
