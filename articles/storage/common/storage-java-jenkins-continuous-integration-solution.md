---
title: Usando o Armazenamento do Azure com uma solução de integração contínua do Jenkins
description: Este tutorial mostra como usar o serviço blob do Azure como um repositório para artefatos de compilação criados por uma solução de integração contínua Jenkins.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 08/13/2019
ms.subservice: common
ms.openlocfilehash: 72756bd3eb12ca80f419a0d53db76e6637d884fc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839127"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Usando o Armazenamento do Azure com uma solução de integração contínua do Jenkins

Este artigo ilustra como usar o armazenamento de Blob como um repositório de artefatos de criação criados por uma solução de integração contínua (CI) Jenkins ou como uma fonte de arquivos para download a serem usados em um processo de criação. Um dos cenários em que você consideraria essa solução útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outras linguagens), compilações são executadas com base em integração contínua e você precisa de um repositório para seus artefatos de construção. você poderia, por exemplo, compartilhá-los com outros membros da organização, seus clientes ou manter um arquivo. Outro cenário é quando o seu próprio trabalho de compilação requer outros arquivos, por exemplo, dependências a serem baixadas como parte da entrada da compilação.

Neste tutorial, você usará o Plug-in de Armazenamento do Azure para Jenkins CI disponibilizado pela Microsoft.

## <a name="jenkins-overview"></a>Visão geral do Jenkins
A Jenkins permite a integração contínua de um projeto de software, permitindo que os desenvolvedores integrem de forma fácil as alterações de código e fazendo com que as compilações sejam produzidas automaticamente e com frequência, aumentando, assim, a produtividade dos desenvolvedores. As compilações têm uma versão e os artefatos de compilação podem ser carregados em vários repositórios. Este artigo mostra como usar o armazenamento de BLOBs do Azure como repositório de artefatos de compilação. Ele também mostra como baixar dependências no armazenamento de blob do Azure.

Mais informações sobre a Jenkins pode ser encontrada em [Conheça a Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Benefícios do uso do serviço Blob
Alguns dos benefícios de usar o serviço Blob para hospedar seus artefatos de compilação de desenvolvimento ágil incluem:

* Alta disponibilidade de seus artefatos de compilação e/ou dependências baixáveis.
* Desempenho quando sua solução de CI Jenkins carrega seus artefatos de compilação.
* Desempenho quando seus clientes e parceiros baixam seus artefatos de compilação.
* O controle sobre as políticas de acesso do usuário, com uma opção entre acesso anônimo, acesso compartilhado com base em expiração, acesso de assinatura, acesso particular, etc.

## <a name="prerequisites"></a>Pré-requisitos
* Uma solução de integração contínua Jenkins.
  
    Se atualmente você não tiver uma solução Jenkins CI, você pode executá-la usando a técnica a seguir:
  
  1. Em um computador habilitado para Java, baixe Jenkins. war de <https://jenkins-ci.org>.
  2. Em um prompt de comando aberto para a pasta que contém jenkins.war, execute:
     
      `java -jar jenkins.war`

  3. No seu navegador, abra `http://localhost:8080/` para abrir o painel do Jenkins, que será usado para instalar e configurar o plug-in do armazenamento do Azure.
     
      Embora uma solução Jenkins CI típica seria configurada para ser executada como um serviço, executar o Jenkins.war na linha de comando será suficiente para este tutorial.
* Uma conta do Azure. Você pode se inscrever para uma conta do Azure em <https://www.azure.com>.
* Uma conta de armazenamento do Azure. Se você não tiver uma conta de armazenamento, crie uma usando as etapas em [Criar uma Conta de Armazenamento](../common/storage-quickstart-create-account.md).
* Estar familiarizado com a solução de CI Jenkins é recomendável, mas não obrigatório, já que o conteúdo a seguir usará um exemplo básico para mostrar as etapas necessárias ao usar o serviço Blob como um repositório para os artefatos de compilação de CI Jenkins.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Como usar o serviço Blob com a Jenkins CI
Para usar o serviço Blob com a Jenkins, você precisará instalar o plug-in do Armazenamento do Azure, configurá-lo para usar sua conta de armazenamento e criar uma ação de pós-compilação que carregue os artefatos de compilação para a sua conta de armazenamento. Essas etapas estão descritas nas seções a seguir.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in Armazenamento do Azure
1. No painel do Jenkins, selecione **gerenciar Jenkins**.
2. No **gerenciar o Jenkins** página, selecione **gerenciar plug-ins**.
3. Selecione a guia **Disponível**.
4. Na seção **Carregadores de Artefatos**, marque **Plug-in de Armazenamento do Microsoft Azure**.
5. Selecione a **instalar sem reinicialização** ou **baixar agora e instalar após a reinicialização**.
6. Reinicie o Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in Armazenamento do Azure para usar sua conta de armazenamento
1. No painel do Jenkins, selecione **gerenciar Jenkins**.
2. No **gerenciar o Jenkins** página, selecione **configurar o sistema**.
3. Na seção **Configuração da Conta de Armazenamento do Microsoft Azure** :
   1. Insira o nome da conta de armazenamento, que pode ser obtido do [portal do Azure](https://portal.azure.com).
   2. Insira sua chave de conta de armazenamento, também obtida do [portal do Azure](https://portal.azure.com).
   3. Use o valor padrão para **URL de ponto de extremidade de serviço Blob** se você estiver usando a nuvem global do Azure. Se você estiver usando uma nuvem do Azure diferente, use o ponto de extremidade conforme especificado no [portal do Azure](https://portal.azure.com) para sua conta de armazenamento. 
   4. Selecione **Validar credenciais de armazenamento** para validar sua conta de armazenamento. 
   5. [Opcional] Se você tiver contas de armazenamento adicionais que você deseja que estarão disponíveis para a CI Jenkins, selecione **adicionar mais contas de armazenamento**.
   6. Selecione **salvar** para salvar suas configurações.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de pós-compilação que carrega os artefatos de compilação para a sua conta de armazenamento
Para fins de instrução, primeiro você precisa criar um trabalho que criará vários arquivos e, em seguida, adicione a ação pós-compilação para carregar os arquivos para sua conta de armazenamento.

1. No painel do Jenkins, selecione **Novo Item**.
2. Nomeie o trabalho **MyJob**, selecione **criar um projeto de software de estilo livre**e, em seguida, selecione **Ok**.
3. No **construir** seção da configuração do trabalho, selecione **Adicionar etapa de build** e selecione **Windows executar comando do lote**.
4. Em **Comando**, use os seguintes comandos:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. No **ações de pós-build** seção da configuração do trabalho, selecione **Adicionar ação pós-compilação** e selecione **carregar artefatos no armazenamento de BLOBs do Azure**.
6. Em **Nome de conta de armazenamento**, selecione a conta de armazenamento a ser usada.
7. Em **Nome do contêiner**, especifique o nome do contêiner. (O contêiner será criado se ele ainda não existir quando os artefatos de compilação forem carregados.) Você pode usar variáveis de ambiente, portanto, para este exemplo, insira `${JOB_NAME}` como o nome do contêiner.
   
    **Dica**
   
    Abaixo da seção **Comando** em que você inseriu um script para **Executar comando em lote do Windows**, existe um link para as variáveis de ambiente reconhecidas pelo Jenkins. Selecione o link para saber os nomes de variáveis de ambiente e as descrições. Variáveis de ambiente que contêm caracteres especiais, como o **BUILD_URL** variável de ambiente não são permitidas como um nome de contêiner ou o caminho virtual comum.
8. Selecione **Tornar novo contêiner público por padrão** para este exemplo. (Se você quiser usar um contêiner particular, você precisará criar uma assinatura de acesso compartilhado para permitir o acesso, o que está além do escopo deste artigo. Você pode saber mais sobre assinaturas de acesso compartilhado em [Uso de SAS (Assinaturas de Acesso Compartilhado)](storage-sas-overview.md).
9. [Opcional] Selecione **Limpar recipiente antes de fazer o upload** se quiser que o contêiner seja limpo do conteúdo antes que os artefatos de construção sejam carregados (deixe-o desmarcado se não quiser limpar o conteúdo do contêiner).
10. Para **lista de artefatos a serem carregados**, insira `text/*.txt`.
11. Para **caminho virtual comum para artefatos carregados**, para fins deste tutorial, insira `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Selecione **salvar** para salvar suas configurações.
13. No painel do Jenkins, selecione **compilar agora** para ser executado **MyJob**. Examine a saída do console para o status. As mensagens de status para o armazenamento do Azure serão incluídas na saída do console quando a ação de pós-compilação iniciar o carregamento dos artefatos de compilação.
14. Após a conclusão bem-sucedida do trabalho, você poderá examinar os artefatos de compilação abrindo o blob público.
    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Selecione **Armazenamento**.
    3. Selecione o nome da conta de armazenamento que você usou para o Jenkins.
    4. Selecione **Contêineres**.
    5. Selecione o container chamado **myjob**, que é a versão em minúscula do nome do trabalho que você atribuiu quando criou o trabalho do Jenkins. Os nomes de contêineres e de blob são escritos em letra minúscula (e diferenciam maiúsculas de minúsculas) no armazenamento do Azure. Dentro da lista de blobs para o container chamado **myjob**, você deve ver **hello.txt** e **date.txt**. Copie a URL de um desses itens e abra-a em seu navegador. Você visualizará o arquivo de texto carregado como um artefato de compilação.

Somente uma ação pós-compilação que carrega artefatos no armazenamento de blob do Azure pode ser criada por trabalho. A única ação de pós-compilação para carregar artefatos no armazenamento de blobs do Azure pode especificar arquivos diferentes (incluindo curingas) e caminhos para arquivos dentro da **Lista de artefatos a serem carregados** usando um ponto-e-vírgula como separador. Por exemplo, se a sua compilação do Jenkins produzir arquivos JAR e TXT na pasta **build** do seu workspace e você quiser fazer o upload para o armazenamento de blobs do Azure, use o seguinte valor para a **Lista de Artefatos a carregar** opção: `build/\*.jar;build/\*.txt`. Você também pode usar a sintaxe de dois-pontos duplos para especificar um caminho a ser usado dentro do nome do blob. Por exemplo, se você deseja que os JARs sejam carregados usando os **binários** no caminho do blob e os arquivos TXT para serem enviados usando **avisos** no caminho do blob, use o seguinte valor para **Lista de Artefatos para fazer o upload da** opção: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar uma etapa de compilação baixada do armazenamento de blob do Azure
As etapas a seguir ilustram para configurar uma etapa de compilação para baixar itens do armazenamento de blob do Azure, o que é útil se você quiser incluir itens em sua compilação. Um exemplo de uso desse padrão é o JARs que você pode querer persistir no armazenamento de blobs do Azure.

1. Na seção **Criar** da configuração do trabalho, selecione **Adicionar etapa de compilação** e selecione **Fazer o download do armazenamento do Azure Blob**.
2. Em **Nome de conta de armazenamento**, selecione a conta de armazenamento a ser usada.
3. Em **Nome do contêiner**, especifique o nome do contêiner que contém os blobs que você quer baixar. É possível usar variáveis de ambiente.
4. Em **Nome do blob**, especifique o nome do blob. É possível usar variáveis de ambiente. Além disso, você pode usar um asterisco como um curinga depois de especificar a letra inicial do nome do blob. Por exemplo, **project\\** * especificaria todos os BLOBs cujos nomes começam com **Project**.
5. [Opcional] Em **Caminho de download**, especifique o caminho no computador do Jenkins onde você quer baixar arquivos do armazenamento de blob do Azure. Também é possível usar variáveis de ambiente. (Se você não fornecer um valor para **Caminho do download**, os arquivos no armazenamento de blob do Azure serão baixados no workspace da tarefa.)

Se houver itens adicionais que deseja baixar do armazenamento de blobs do Azure, você poderá criar etapas de compilação adicionais.

Depois de executar uma compilação, você pode verificar a saída do console de histórico da compilação ou verificar o local de download para ver se os blobs esperados foram baixados com êxito.  

## <a name="components-used-by-the-blob-service"></a>Componentes usados pelo serviço Blob
Esta seção fornece uma visão geral dos componentes do serviço Blob.

* **Conta de Armazenamento**: todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Uma conta de armazenamento é o nível mais alto do namespace para acessar blobs. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total seja inferior a 100 TB.
* **Contêiner**: o contêiner fornece um agrupamento de conjunto de blobs. Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.
* **Blob**: um arquivo de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no Armazenamento do Azure: blobs de blocos e de páginas. A maioria dos arquivos são blobs de bloco. Um único blob de blocos pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Os blobs de página, um outro tipo de blob, podem ter até 1 TB de tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre os blobs, consulte [Compreendendo os Blobs de Bloco, Blobs de Anexo e Blobs de Página](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato de URL**: os blobs são endereçáveis usando o seguinte formato de URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima aplica-se para a nuvem global do Azure. Se você estiver usando uma nuvem do Azure diferente, use o ponto de extremidade dentro do [portal do Azure](https://portal.azure.com) para determinar o ponto de extremidade da URL.)
  
    No formato acima, `storageaccount` representa o nome da sua conta de armazenamento, `container_name` representa o nome do seu contêiner e `blob_name` representa o nome do seu blob, respectivamente. Dentro do nome do contêiner, é possível ter vários caminhos, separados por uma barra, **/** . O nome do contêiner de exemplo usado para este tutorial foi **MyJob**, e **${BUILD\_ID} / ${BUILD\_número}** foi usado para o caminho virtual comum, resultando no blob tivesse uma URL no seguinte formato:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Solução de problemas do plug-in do Jenkins

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas
* [Conheça Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [SDK de Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java)
* [Referência de SDK do Cliente de Armazenamento do Azure](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog da Equipe do Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)

Para saber mais, visite [Azure para desenvolvedores Java](/java/azure).
