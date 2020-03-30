---
title: Backup e restauração do HANA no SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como realizar o backup e a restauração do HANA no SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430080"
---
# <a name="backup-and-restore"></a>Backup e restauração

>[!IMPORTANT]
>Este artigo não é um substituto para a documentação de administração SAP HANA ou Notas SAP. Esperamos que você tenha uma compreensão sólida e experiência em administração e operações sap HANA, especialmente para backup, restauração, alta disponibilidade e recuperação de desastres. Neste artigo, capturas de tela do SAP HANA Studio são mostradas. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

É importante que você execute as etapas e os processos realizados em seu ambiente e com as liberações e versões do HANA. Alguns processos descritos neste artigo são simplificados para uma melhor compreensão geral. Eles não foram feitos para serem usados como passos detalhados para eventuais manuais de operação. Se você quiser criar manuais de operação para suas configurações, teste e exerça seus processos e documente os processos relacionados às suas configurações específicas. 

Um dos aspectos mais importantes das bases de dados operacionais é protegê-los de eventos catastróficos. A causa desses eventos pode ser qualquer coisa, desde desastres naturais a simples erros de usuário.

Fazer backup de um banco de dados, com a capacidade de restaurá-lo a qualquer momento, como antes de alguém excluir dados críticos, permite a restauração de um estado o mais próximo possível da maneira como era antes da interrupção.

Dois tipos de backups devem ser realizados para obter a capacidade de restauração:

- Backups de banco de dados: backups completos, incrementais ou diferenciais
- Backups do log de transações

Além de backups completos do banco de dados realizados em um nível de aplicativo, é possível realizar backups com instantâneos de armazenamento. Os instantâneos de armazenamento não substituem os backups do registro de transações. Os backups de log de transações continuam sendo importantes para restaurar o banco de dados para um determinado ponto no tempo ou para esvaziar os logs de transações já confirmadas. Os instantâneos de armazenamento podem acelerar a recuperação fornecendo rapidamente uma imagem de roll-forward do banco de dados. 

O SAP HANA no Azure (Instâncias Grandes) oferece duas opções de backup e de restauração:

- **DIY (Faça Você Mesmo).** Depois de ter certeza de que há espaço em disco suficiente, execute o banco de dados completo e faça backups usando um dos seguintes métodos de backup de disco. Você pode fazer backup diretamente em volumes anexados às unidades HANA Large Instance ou em ações NFS que estão configuradas em uma máquina virtual Azure (VM). No último caso, os clientes configuram uma VM do Linux no Azure, anexam o Armazenamento do Azure à VM e compartilham o armazenamento por meio de um servidor NFS configurado na VM. Se você executar o backup contra volumes que se anexam diretamente às unidades HANA Large Instance, copie os backups para uma conta de armazenamento Do Zure. Faça isso depois de configurar uma VM Azure que exporta ações NFS baseadas no Armazenamento Azure. Você também pode usar um cofre de backup do Azure ou um armazenamento a frio do Azure. 

   Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar os backups depois que eles forem copiados para uma conta de armazenamento do Azure. A opção de backup DIY também pode ser necessária para dados que você precisa armazenar por períodos mais longos de tempo para fins de conformidade e auditoria. Em todos os casos, os backups são copiados para compartilhamentos NFS representados por meio de uma VM e do Armazenamento do Azure.

- **Funcionalidade de backup e restauração de infraestrutura.** Você também pode usar a funcionalidade de backup e restauração que a infra-estrutura subjacente do SAP HANA no Azure (Instâncias Grandes) fornece. Essa opção atende a necessidade de backups e restaurações rápidas. O restante desta seção aborda a funcionalidade de backup e de restauração oferecida com Instâncias Grandes HANA. Esta seção também abrange o relacionamento que o backup e a restauração têm com a funcionalidade de recuperação de desastres oferecida pelo HANA Large Instances.

> [!NOTE]
>   A tecnologia de instantâneo usada pela infra-estrutura subjacente do HANA Large Instances tem uma dependência de instantâneos SAP HANA. Neste ponto, os instantâneos SAP HANA não funcionam em conjunto com vários inquilinos de contêineres de banco de dados multilocatários SAP HANA. Se apenas um inquilino for implantado, os instantâneos SAP HANA funcionam e você pode usar este método.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Use instantâneos de armazenamento do SAP HANA no Azure (Instâncias grandes)

A infraestrutura de armazenamento subjacente ao SAP HANA no Azure (Instâncias Grandes) dá suporte a instantâneos de armazenamento de volumes. Há suporte para o backup e para a restauração de volumes, com as seguintes considerações:

- Em vez de backups de banco de dados completos, instantâneos de volume de armazenamento são executados com frequência.
- Quando um snapshot é acionado sobre /hana/data e /hana/shared, que inclui /usr/sap, volumes, a tecnologia de snapshot inicia um snapshot SAP HANA antes de executá-lo o snapshot de armazenamento. Esse instantâneo SAP HANA é o ponto de instalação para restaurações de log eventual após a recuperação do instantâneo de armazenamento. Para que um instantâneo HANA seja bem sucedido, você precisa de uma instância HANA ativa. Em um cenário de HSR, um instantâneo de armazenamento não é suportado em um nó secundário atual onde um snapshot HANA não pode ser executado.
- Depois que o instantâneo de armazenamento é executado com sucesso, o snapshot SAP HANA é excluído.
- Backups de log de transações são usados frequentemente e armazenados no volume /hana/logbackups ou no Azure. É possível disparar o volume /hana/logbackups que contém os backups de log de transações para usar um instantâneo separadamente. Nesse caso, você não precisa executar um instantâneo HANA.
- Se você deve restaurar um banco de dados em um determinado ponto no tempo, para uma interrupção de produção, solicite que o Microsoft Azure Support ou o SAP HANA no Azure restaurem um determinado instantâneo de armazenamento. Um exemplo é uma restauração planejada de um sistema de área restrita para seu estado original.
- O instantâneo SAP HANA incluído no instantâneo de armazenamento é um ponto de compensação para a aplicação de backups de log de transação que foram armazenados e foram armazenados após a tomada do instantâneo de armazenamento.
- Esses backups de log de transações são aplicados para restaurar o banco de dados para um determinado ponto no tempo.

Você pode executar instantâneos de armazenamento que visam três classes de volumes:

- Um instantâneo combinado sobre /hana/data e /hana/compartilhado, que inclui /usr/sap. Esse instantâneo exige a criação de um instantâneo do SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo SAP HANA garante que o banco de dados esteja em um estado consistente do ponto de vista do armazenamento. Para o processo de restauração, esse é um ponto a ser configurado.
- Um instantâneo separado em /hana/logbackups.
- Uma partição do sistema operacional.

Para obter os scripts e documentação de instantâneos mais recentes, consulte [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Quando você baixa o pacote de script de instantâneo do [GitHub,](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)você recebe três arquivos. Um dos arquivos está documentado em um PDF para a funcionalidade fornecida. Depois de baixar o conjunto de ferramentas, siga as instruções em "Obter as ferramentas de snapshot".

## <a name="storage-snapshot-considerations"></a>Considerações sobre o instantâneo de armazenamento

>[!NOTE]
>Os instantâneos de armazenamento consomem espaço de armazenamento alocado nas unidades HANA Large Instance. Considere os seguintes aspectos do agendamento de instantâneos de armazenamento e quantos instantâneos de armazenamento manter. 

A mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (Instâncias Grandes) inclui:

- Um instantâneo de armazenamento específico no momento em que é tomado consome pouco armazenamento.
- À medida que o conteúdo dos dados muda e o conteúdo nos arquivos de dados SAP HANA mudam no volume de armazenamento, o snapshot precisa armazenar o conteúdo original do bloco e as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existe, quanto maior o instantâneo de armazenamento ficar.
- Quanto mais alterações são feitas no volume de banco de dados do SAP HANA durante o tempo de vida de um instantâneo de armazenamento, maior o consumo de espaço do instantâneo de armazenamento.

O SAP HANA no Azure (Instâncias Grandes) vem com tamanhos de volume fixo para o volume de dados e de log do SAP HANA. A execução de instantâneos desses volumes consome seu espaço de volume. Você precisa:

- Determine quando agendar instantâneos de armazenamento.
- Monitore o consumo de espaço dos volumes de armazenamento. 
- Gerencie o número de instantâneos que você armazena. 

É possível desabilitar os instantâneos de armazenamento quando você importa muitos dados ou realiza outras alterações significativas no banco de dados do HANA. 


As seções a seguir fornecem informações para a execução desses instantâneos e incluem recomendações gerais:

- Embora o hardware possa sustentar 255 instantâneos por volume, você quer ficar bem abaixo deste número. A recomendação é de 250 ou menos.
- Antes de executar instantâneos de armazenamento, monitore e mantenha o controle do espaço livre.
- Reduza o número de instantâneos de armazenamento com base em espaço livre. É possível reduzir o número de instantâneos que você mantém ou aumentar os volumes. É possível solicitar armazenamento adicional em unidades de 1 terabyte.
- Durante atividades como a movimentação de dados em SAP HANA com ferramentas de migração da plataforma SAP (R3load) ou a restauração de bancos de dados do SAP HANA com base em backups, desabilite instantâneos de armazenamento no volume /hana/data. 
- Durante reorganizações maiores de tabelas SAP HANA, evite instantâneos de armazenamento, se possível.
- Os instantâneos de armazenamento são um pré-requisito para usar os recursos de recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para usar os instantâneos de armazenamento de autoatendimento

Para garantir que o script de instantâneo seja executado com sucesso, certifique-se de que Perl esteja instalado no sistema operacional Linux no servidor HANA Large Instances. Perl vem pré-instalado em sua unidade HANA Large Instance. Para verificar a versão do Perl, use a linha de comando a seguir:

`perl -v`

![A chave pública é copiada executando este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar instantâneos de armazenamento com HANA Large Instances, siga essas etapas.
1. Verifique se o Perl está instalado no sistema operacional Linux no servidor das Instâncias Grandes HANA.
1. Modifique /etc/ssh/ssh\_config para adicionar a linha _MACs hmac-sha1_.
1. Crie uma conta de usuário de backup SAP HANA no nó mestre para cada instância SAP HANA que você executar, se aplicável.
1. Instale o cliente SAP HANA HDB em todos os servidores de Instâncias Grandes do SAP HANA.
1. No primeiro servidor de Instâncias Grandes do SAP HANA de cada região, crie uma chave pública para acessar a infraestrutura de armazenamento subjacente que controla a criação de instantâneos.
1. Copie os scripts e o arquivo de configuração do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) para o local do **hdbsql** na instalação do SAP HANA.
1. Modifique o arquivo *HANABackupDetails.txt* conforme necessário para as especificações apropriadas do cliente.

Obtenha os scripts de instantâneo e a documentação mais recentes do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Para as etapas listadas anteriormente, consulte [as ferramentas de instantâneo da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Consideração para cenários de MCOD
Se você executar um [cenário MCOD](https://launchpad.support.sap.com/#/notes/1681092) com várias instâncias SAP HANA em uma unidade HANA Large Instance, você terá volumes de armazenamento separados provisionados para cada uma das instâncias sap HANA. Para obter mais informações sobre o MDC e outras considerações, consulte "Coisas importantes a serem lembradas" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Etapa 1: Instalar o cliente do SAP HANA HDB

O sistema operacional Linux instalado no SAP HANA no Azure (Instâncias Grandes) inclui as pastas e scripts necessários para executar instantâneos de armazenamento SAP HANA para fins de backup e recuperação de desastres. Verifique se há lançamentos mais recentes no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

É sua responsabilidade instalar o cliente SAP HANA HDB nas unidades HANA Large Instance enquanto você instala o SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Etapa 2: Alterar o /etc/ssh/ssh\_config

Esta etapa é descrita em "Ativar comunicação com armazenamento" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Etapa 3: Criar uma chave pública

Para habilitar o acesso às interfaces de snapshot de armazenamento do seu inquilino HANA Large Instance, estabeleça um procedimento de login através de uma chave pública. 

No primeiro servidor SAP HANA no Azure (Instâncias Grandes) em seu inquilino, crie uma chave pública para acessar a infra-estrutura de armazenamento. Com uma chave pública, uma senha não é necessária para entrar nas interfaces de snapshot de armazenamento. Você também não precisa manter credenciais de senha com uma chave pública. 

Para gerar uma chave pública, consulte "Ativar comunicação com armazenamento" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Etapa 4: Criar uma conta de usuário do SAP HANA

Para iniciar a criação de instantâneos SAP HANA, crie uma conta de usuário no SAP HANA que os scripts de snapshot de armazenamento podem usar. Crie uma conta de usuário do SAP HANA no SAP HANA Studio para essa finalidade. O usuário deve ser criado o SYSTEMDB e *não* o banco de dados SID para MDC. No ambiente de contêiner único, o usuário é criado no banco de dados do inquilino. Esta conta deve ter **privilégios de Backup Admin** e **Catalog Read.** 

Para configurar e usar uma conta de usuário, consulte "Ativar comunicação com o SAP HANA" no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Etapa 5: Autorizar a conta de usuário do SAP HANA

Nesta etapa, você autoriza a conta de usuário SAP HANA que você criou para que os scripts não precisem enviar senhas em tempo de execução. O comando `hdbuserstore` SAP HANA permite a criação de uma chave de usuário SAP HANA. A chave é armazenada em um ou mais nós SAP HANA. A chave de usuário permite que ele acesse o SAP HANA sem precisar gerenciar senhas de dentro do processo de script. O processo de script será abordado posteriormente neste artigo.

>[!IMPORTANT]
>Execute esses comandos de configuração com o mesmo contexto do usuário em que os comandos de snapshot são executados. Caso contrário, os comandos de instantâneo não funcionarão corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Etapa 6: Obter os scripts de instantâneo, configurar os instantâneos e testar a configuração e a conectividade

Baixe a versão mais recente dos scripts no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). A forma como os scripts são instalados mudou com a versão 4.1 dos scripts. Para obter mais informações, consulte "Habilitar comunicação com SAP HANA" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para obter a seqüência exata de comandos, consulte "Fácil instalação de ferramentas de snapshot (padrão)" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Recomendamos o uso da instalação padrão. 

Para atualizar da versão 3.x para 4.1, consulte "Atualizar uma instalação existente" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Para desinstalar o conjunto de ferramentas 4.1, consulte "Desinstalação das ferramentas de snapshot" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Não se esqueça de executar as etapas descritas em "Configuração completa de ferramentas de snapshot" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

O propósito dos diferentes scripts e arquivos que foram instalados é descrito em "Quais são essas ferramentas de snapshot?" nas [ferramentas de instantâneo da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Antes de configurar as ferramentas de snapshot, certifique-se de que você também configurou os locais de backup e configurações do HANA corretamente. Para obter mais informações, consulte "Configuração SAP HANA" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A configuração do conjunto de ferramentas de instantâneo é descrita em "Arquivo Config - HANABackupCustomerDetails.txt" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Teste a conectividade com o SAP HANA

Após colocar todos os dados de configuração no arquivo *HANABackupCustomerDetails.txt* verifique se as configurações estão corretas para os dados da instância do HANA. Use o script `testHANAConnection`, que é independente de uma configuração de expansão ou escala vertical do SAP HANA.

Para obter mais informações, consulte "Verifique a conectividade com o SAP HANA - testHANAConnection" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Conectividade de armazenamento de teste

O próximo passo do teste é verificar a conectividade ao armazenamento com base nos dados que você colocou no arquivo de configuração *HANABackupCustomerDetails.txt.* Em seguida, execute uma foto de teste. Antes de `azure_hana_backup` executar o comando, você deve executar este teste. Para obter a seqüência de comandos para este teste, consulte "Verificar conectividade com armazenamento - testeStorageSnapshotConnection"" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Após uma conexão com êxito nas interfaces da máquina virtual de armazenamento, o script continuará com a fase 2 e criará um instantâneo de teste. A saída é mostrada aqui para uma configuração de escala de três nós do SAP HANA.

Se o instantâneo de teste for executado com sucesso com o script, você pode agendar os instantâneos de armazenamento reais. Se não for bem sucedido, investigue os problemas antes de seguir em frente. O instantâneo de teste deve permanecer até que os primeiros instantâneos reais sejam concluídos.


### <a name="step-7-perform-snapshots"></a>Etapa 7: Executar instantâneos

Quando as etapas de preparação estiverem concluídas, você pode começar a configurar e agendar os instantâneos de armazenamento reais. O script a ser agendado funciona com as configurações de expansão e escalável do SAP HANA. Para execução periódica e regular do script de backup, agende o script usando o utilitário cron. 

Para obter a sintaxe e funcionalidade exatas do comando, consulte "Executar backup de instantâneos - azure_hana_backup" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Quando o `azure_hana_backup` script é executado, ele cria o instantâneo de armazenamento nas três fases seguintes:

1. Ele executa um instantâneo SAP HANA.
1. Ele executa um instantâneo de armazenamento.
1. Ele remove o instantâneo SAP HANA que foi criado antes do snapshot de armazenamento ser realizado.

Para executar o script, chame-o da pasta executável HDB para a qual foi copiado. 

O período de retenção é administrado com o número de instantâneos enviados como parâmetro quando você executa o script. A quantidade de tempo que é coberta pelos instantâneos de armazenamento é uma função do período de execução e do número de instantâneos enviados como parâmetro quando o script é executado. 

Se o número de instantâneos que são mantidos exceder o número que é nomeado como parâmetro na chamada do script, o snapshot de armazenamento mais antigo do mesmo rótulo será excluído antes que um novo snapshot seja executado. O número que você der como o último parâmetro da chamada é o número que você poderá usar para controlar o número de instantâneos mantidos. Com este número, você também pode controlar, indiretamente, o espaço em disco que é usado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência dos instantâneos para os diferentes tipos depende do fato de você usar a funcionalidade de recuperação de desastre do SAP HANA em Instâncias Grandes. Essa funcionalidade depende de instantâneos de armazenamento, os quais podem requerer recomendações especiais para os períodos de execução e frequência dos instantâneos de armazenamento. 

Nas considerações e recomendações a seguir, vamos supor que você *não* usa a funcionalidade de recuperação de desastre que o SAP HANA em Instâncias Grandes oferece. Em vez disso, você usa os instantâneos de armazenamento para ter backups e ser capaz de fornecer recuperação pontual nos últimos 30 dias. Dadas as limitações do número de instantâneos e espaço, considere os seguintes requisitos:

- O tempo de recuperação para uma restauração pontual.
- O espaço usado.
- O ponto de recuperação e os objetivos de tempo de recuperação para possível recuperação de um desastre.
- A execução eventual de backups completos de banco de dados do HANA em discos. Sempre que um backup completo do banco de dados em discos ou a interface **backint** for realizado, a execução de instantâneos de armazenamento falhará. Se você planeja executar backups de banco de dados completos em cima de instantâneos de armazenamento, certifique-se de que a execução dos instantâneos de armazenamento esteja desativada durante esse tempo.
- O número de instantâneos por volume, que é limitado a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se você não usar a funcionalidade de recuperação de desastres do HANA Large Instances, o período de snapshot será menos freqüente. Nesses casos, execute os instantâneos combinados em /hana/data e /hana/compartilhado, que inclui /usr/sap, em períodos de 12 horas ou 24 horas. Fique com as fotos por um mês. O mesmo vale para os instantâneos do volume de backup do log. A execução dos backups de log de transação SAP HANA contra o volume de backup de log ocorre em períodos de 5 minutos a 15 minutos.

Os instantâneos de armazenamento agendados executam melhor utilizando o cron. Use o mesmo script para todas as necessidades de backupe recuperação de desastres. Modifique as entradas de script para corresponder aos vários tempos de backup solicitados. Estes instantâneos são todos programados de forma diferente em cron, dependendo do tempo de execução. Pode ser de hora em hora, a cada 12 horas, diariamente ou semanalmente. 

O exemplo a seguir mostra um cronograma cron em /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, um instantâneo combinado por hora abrange os volumes que contêm os locais /hana/data e /hana/shared/SID, que inclui /usr/sap, localizações. Use esse tipo de instantâneo para uma recuperação pontual mais rápida nos últimos dois dias. Há também um instantâneo diário nesses volumes. Então, você tem dois dias de cobertura por instantâneos de hora em hora mais quatro semanas de cobertura por instantâneos diários. O volume de backup do log de transações também é feito backup diariamente. Esses backups são mantidos por quatro semanas. 

Como você pode ver na terceira linha do crontab, o backup do registro de transações HANA está programado para ser executado a cada 5 minutos. Os tempos de início dos diferentes trabalhos de cron que executam instantâneos de armazenamento são escalonados. Desta forma, os instantâneos não são executados de uma só vez em um determinado ponto no tempo. 

No exemplo a seguir, você executa um instantâneo combinado que cobre os volumes que contêm o /hana/data e /hana/shared/SID, que inclui /usr/sap, locais de hora em hora. Mantenha esses instantâneos por dois dias. Os instantâneos dos volumes de backup do log de transação são executados em uma base de 5 minutos e são mantidos por quatro horas. Como antes, o backup do arquivo de registro de transações HANA está programado para ser executado a cada 5 minutos. 

O instantâneo do volume de backup de log de transações é executado com um atraso de 2 minutos após o backup de log de transações ter sido inciado. Em circunstâncias normais, o backup do log de transação SAP HANA termina dentro desses 2 minutos. Como antes, o volume que contém o LUN de inicialização tem seu backup feito uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico a seguir ilustra as seqüências do exemplo anterior. O LUN de inicialização está excluído.

![Relação entre os backups e os instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

O SAP HANA executa gravações regulares no volume /hana/log para documentar as alterações confirmadas no banco de dados. Regularmente, o SAP HANA grava um ponto de salvamento para o volume /hana/data. Conforme especificado no crontab, um backup de log de transação SAP HANA é executado a cada 5 minutos. 

Você também vê que um instantâneo SAP HANA é executado a cada hora como resultado do acionamento de um instantâneo de armazenamento combinado sobre os volumes /hana/data e /hana/compartilhado/SID. Depois que o snapshot HANA for bem sucedido, o instantâneo de armazenamento combinado é executado. Como instruído no crontab, o instantâneo de armazenamento no volume /hana/logbackup é executado a cada 5 minutos, cerca de 2 minutos após o backup do registro de transações HANA.

> 

>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups do SAP HANA só é válido quando os instantâneos são executados junto com backups de log de transações do SAP HANA. Esses backups de log de transações precisam abordar os períodos de tempo entre os instantâneos de armazenamento. 

Se você firmou um compromisso com os usuários de uma recuperação pontual de 30 dias, será necessário:

- Acesse um instantâneo de armazenamento combinado sobre /hana/data e /hana/shared/SID que tem 30 dias, em casos extremos. 
- Tenha backups de log de transações contíguos que abrangem o tempo entre todos os instantâneos de armazenamento combinado. Portanto, o instantâneo mais antigo do volume de backup de log de transações deve ser de 30 dias. Este não é o caso se você copiar os backups do log de transação para outra ação NFS que está localizada no Azure Storage. Nesse caso, você pode efetuar pull de backups de log de transações antigos desse compartilhamento NFS.

Para se beneficiar de instantâneos de armazenamento e da eventual replicação de armazenamento de backups de log de transações, altere o local para o qual o SAP HANA grava os backups do log de transações. É possível fazer essa alteração no HANA Studio. 

Embora o SAP HANA faça backup de segmentos de log completos automaticamente, especifique um intervalo de backup de log para ser determinístico. Isso é especialmente verdadeiro quando você usa a opção de recuperação de desastres porque geralmente deseja executar backups de log com um período determinístico. No caso seguinte, 15 minutos são definidos como o intervalo de backup do log.

![Agendar logs de backup SAP HANA no Studio de SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Você também pode escolher backups mais freqüentes do que a cada 15 minutos. Uma configuração mais frequente é frequentemente usada em conjunto com a funcionalidade de recuperação de desastre do SAP HANA nas Instâncias Grandes. Alguns clientes executam backups de log de transações a cada 5 minutos.

Se o backup do banco de dados nunca foi feito, a etapa final será executar um backup de banco de dados com base no arquivo para criar uma entrada de backup única que deve existir no catálogo de backup. Caso contrário, o SAP HANA não pode iniciar seus backups de log especificados.

![Faça um backup baseado em arquivo para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Depois que seus primeiros instantâneos de armazenamento bem-sucedidos forem executados, exclua o instantâneo de teste executado na etapa 6. Para obter mais informações, consulte "Remover instantâneos de teste - removerTestStorageSnapshot" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitore o número e o tamanho dos instantâneos no volume do disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento desses instantâneos. O comando `ls` não mostra o diretório de instantâneo ou arquivos. O comando `du` do Sistema Operacional Linux mostra detalhes sobre esses instantâneos de armazenamento porque eles são armazenados nos mesmos volumes. Use o comando com as seguintes opções:

- `du –sh .snapshot`: essa opção fornece um total de todos os instantâneos no diretório de instantâneos.
- `du –sh --max-depth=1`: essa opção lista todos os instantâneos salvos na pasta **.snapshot** e o tamanho de cada instantâneo.
- `du –hc`: essa opção fornece o tamanho total usado por todos os instantâneos.

Use esses comandos para garantir que os instantâneos que são tirados e armazenados não consumam todo o armazenamento nos volumes.

>[!NOTE]
>Os instantâneos do LUN de inicialização não são visíveis com os comandos anteriores.

### <a name="get-details-of-snapshots"></a>Obtenha detalhes de instantâneos
Para obter mais detalhes sobre `azure_hana_snapshot_details`instantâneos, use o script . Você pode executar este script em qualquer local se houver um servidor ativo no local de recuperação de desastres. O script fornece a saída a seguir dividida por cada volume que contém os instantâneos: 
   * O tamanho do total de instantâneos em um volume
   * Os detalhes a seguir em cada instantâneo nesse volume: 
      - Nome do instantâneo 
      - Criar tempo 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - ID do HANA Backup associada a esse instantâneo, se relevante

Para obter sintaxe dos comandos e saídas, consulte "Listar instantâneos - azure_hana_snapshot_details" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Reduza o número de instantâneos em um servidor

Como explicado anteriormente, você pode reduzir o número de certos rótulos de snapshots que você armazena. Os dois últimos parâmetros do comando para iniciar um instantâneo são o rótulo e o número de instantâneos que você deseja manter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, o rótulo de instantâneo é **dailyhana**. O número de instantâneos com este rótulo a ser mantido é **de 28**. Ao responder ao consumo de espaço em disco, pode ser útil reduzir o número de instantâneos armazenados. Uma maneira fácil de reduzir o número de instantâneos para 15, por exemplo, é executar o script com o último parâmetro definido para **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se você executar o script com esta configuração, o número de instantâneos, que inclui o novo instantâneo de armazenamento, é de 15. Os 15 instantâneos mais recentes são mantidos e os 15 instantâneos mais antigos são excluídos.

 >[!NOTE]
 > Este script reduz o número de instantâneos somente se houver instantâneos com mais de uma hora de idade. O script não exclui instantâneos com menos de uma hora de idade. Essas restrições estão relacionadas à funcionalidade de recuperação de desastre opcional oferecida.

Se você não quiser mais manter um conjunto de instantâneos com o prefixo de backup **dailyhana** nos exemplos de sintaxe, execute o script com **0** como número de retenção. Todos os instantâneos que correspondem a esse rótulo são removidos em seguida. A remoção de todos os instantâneos pode afetar os recursos da funcionalidade de recuperação de desastres HANA Large Instances.

Uma segunda opção para excluir instantâneos específicos é usar o script `azure_hana_snapshot_delete`. Este script foi desenvolvido para excluir um instantâneo ou conjunto de instantâneos usando a ID de backup do HANA conforme encontrada no HANA Studio ou por meio do próprio nome do instantâneo. Atualmente, o ID de backup está vinculado apenas aos instantâneos criados para o tipo de snapshot **hana.** Os backups instantâneos dos **logs** de tipo e **inicialização** não executam um snapshot SAP HANA, portanto não há iD de backup para esses instantâneos. Se o nome do instantâneo for inserido, ele procurará todos os instantâneos em volumes diferentes que correspondam ao nome do instantâneo inserido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter mais informações sobre o script, consulte "Excluir um instantâneo - azure_hana_snapshot_delete" nas [ferramentas de snapshot da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Execute o script como **raiz de**usuário .

>[!IMPORTANT]
>Se houver dados que existam apenas no instantâneo que você planeja excluir, depois que o snapshot é excluído, esses dados serão perdidos para sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauração no nível do arquivo de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de instantâneos **hana** e **logs**, é possível acessar os instantâneos diretamente nos volumes no diretório **.snapshot**. Há um subdiretório para cada um dos instantâneos. Copie cada arquivo no estado em que estava no ponto do instantâneo daquele subdiretório para a estrutura real do diretório. 

Na versão atual do script, não há *script* de restauração fornecido para a restauração de instantâneos como autoatendimento. A restauração do snapshot pode ser realizada como parte dos scripts de recuperação de desastres de autoatendimento no local de recuperação de desastres durante o failover. Para restaurar um instantâneo desejado dos instantâneos disponíveis existentes, você deve entrar em contato com a equipe de operações da Microsoft abrindo uma solicitação de serviço.

>[!NOTE]
>A restauração de um único arquivo não funciona para instantâneos do LUN de inicialização independente do tipo das unidades HANA Large Instance. O **diretório .snapshot** não está exposto no LUN de inicialização. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recuperar para o instantâneo mais recente do HANA

Em um cenário de reprodução, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente do cliente com o Microsoft Azure Support. É uma questão de alta urgência se os dados foram excluídos em um sistema de produção e a única maneira de recuperá-los é restaurar o banco de dados de produção.

Em uma situação diferente, uma recuperação pontual pode ter baixa urgência e ser planejada com dias de antecedência. Você pode planejar esta recuperação com o SAP HANA no Azure em vez de levantar uma bandeira de alta prioridade. Por exemplo, você pode planejar atualizar o software SAP aplicando um novo pacote de aprimoramento. Em seguida, é necessário reverter para um instantâneo que representa o estado antes da atualização de pacote de melhoria.

Antes de enviar a solicitação, é necessário se preparar. A equipe SAP HANA no Azure pode então lidar com a solicitação e fornecer os volumes restaurados. Depois, restaure o banco de dados do HANA com base nos instantâneos.

Para obter um instantâneo restaurado com o novo conjunto de ferramentas, consulte "Como restaurar um instantâneo" no [guia de recuperação manual para SAP HANA no Azure a partir de um snapshot de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para se preparar para a solicitação, siga estas etapas.

1. Decida qual instantâneo para restaurar. Somente o volume de dados/hana é restaurado, a menos que indicado o contrário. 

1. Desligar a instância do HANA.

   ![Desligar a instância do HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó do banco de dados do HANA. Se os volumes de dados ainda estiverem montados para o sistema operacional, a restauração do instantâneo falhará.

   ![Desmonte os volumes de dados em cada nó do banco de dados HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra uma solicitação de suporte do Azure e inclua instruções sobre a restauração de um instantâneo específico:

   - Durante a restauração: SAP HANA no Azure Service pode pedir que você participe de uma teleconferência para coordenar, verificar e confirmar se o instantâneo de armazenamento correto foi restaurado. 

   - Após a restauração: SAP HANA no Azure Service notifica quando o instantâneo de armazenamento é restaurado.

1. Depois que o processo de restauração for concluído, monte novamente todos os volumes de dados.

   ![Montar novamente todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade de obter, por exemplo, arquivos de dados SAP HANA recuperados de um instantâneo de armazenamento, está documentado na etapa 7 do [guia de recuperação manual para SAP HANA no Azure a partir de um snapshot de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para restaurar a partir de um backup de instantâneo, consulte [Guia de recuperação manual para SAP HANA no Azure a partir de um snapshot de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Se o seu snapshot foi restaurado pelas operações da Microsoft, você não precisa fazer o passo 7.


### <a name="recover-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
Para restaurar até certo ponto no tempo, consulte "Recuperar o banco de dados até o seguinte ponto no tempo" no [guia de recuperação manual para SAP HANA no Azure a partir de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Próximas etapas
- Veja [os princípios e preparação de recuperação de desastres.](hana-concept-preparation.md)
