---
title: Planejando uma implantação de Arquivos do Azure | Microsoft Docs
description: Entenda o planejamento de uma implantação de arquivos do Azure. Você pode montar diretamente um compartilhamento de arquivos do Azure ou armazenar em cache o compartilhamento de arquivos do Azure localmente com o Sincronização de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2e8a2030acd4297ab3032e8f1e3bde5b6df66659
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037160"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planejando uma implantação de Arquivos do Azure
Os [arquivos do Azure](storage-files-introduction.md) podem ser implantados de duas maneiras principais: montando diretamente os compartilhamentos de arquivos do Azure sem servidor ou armazenando em cache os compartilhamentos de arquivos do Azure no local usando sincronização de arquivos do Azure. A opção de implantação escolhida altera as coisas que você precisa considerar ao planejar sua implantação. 

- **Montagem direta de um compartilhamento de arquivo do Azure**: Como os Arquivos do Azure fornecem acesso SMB, você pode montar compartilhamentos de arquivo do Azure localmente ou na nuvem usando o cliente SMB padrão disponível no Windows, no macOS e no Linux. Como os compartilhamentos de arquivos do Azure são sem servidor, a implantação para cenários de produção não requer o gerenciamento de um servidor de arquivos nem de um dispositivo NAS. Isso significa que você não precisa aplicar patches de software nem trocar discos físicos. 

- **Armazenar em cache o compartilhamento de arquivo do Azure local com a Sincronização de Arquivos do Azure**: A Sincronização de Arquivos do Azure permite centralizar os compartilhamentos de arquivos da sua organização no serviço Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de Arquivos do Azure transforma um Windows Server local (ou em nuvem) em um cache rápido do compartilhamento de arquivo do Azure. 

Este artigo aborda principalmente as considerações de implantação para implantar um compartilhamento de arquivos do Azure para ser montado diretamente por um cliente local ou em nuvem. Para planejar uma implantação de Sincronização de Arquivos do Azure, consulte [planejando uma implantação de sincronização de arquivos do Azure](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Conceitos de gerenciamento
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Ao implantar compartilhamentos de arquivos do Azure em contas de armazenamento, recomendamos:

- Implantar somente compartilhamentos de arquivos do Azure em contas de armazenamento com outros compartilhamentos de arquivos do Azure. Embora as contas de armazenamento GPv2 permitam que você tenha contas de armazenamento de finalidade mista, como os recursos de armazenamento como compartilhamentos de arquivos do Azure e contêineres de blob compartilham os limites da conta de armazenamento, misturar recursos em conjunto pode dificultar a solução de problemas de desempenho posteriormente. 

- Prestar atenção às limitações de IOPS de uma conta de armazenamento ao implantar compartilhamentos de arquivo do Azure. O ideal é que você mapeie os compartilhamentos de arquivos em uma relação de um para um com contas de armazenamento. No entanto, isso nem sempre é possível devido a vários limites e restrições, tanto da sua organização quanto do Azure. Quando não for possível ter apenas um compartilhamento de arquivo implantado em uma conta de armazenamento, considere quais compartilhamentos serão altamente ativos e quais compartilhamentos serão menos ativos para garantir que os compartilhamentos de arquivos mais ativos não sejam colocados na mesma conta de armazenamento.

- Implante somente contas GPv2 e FileStorage e atualize GPv1 e contas de armazenamento clássico quando encontrá-las em seu ambiente. 

## <a name="identity"></a>Identidade
Para acessar um compartilhamento de arquivos do Azure, o usuário do compartilhamento de arquivos deve ser autenticado e ter autorização para acessar o compartilhamento. Isso é feito com base na identidade do usuário que está acessando o compartilhamento de arquivos. Os arquivos do Azure integram-se com três provedores de identidade principais:
- **Active Directory Domain Services local (AD DS ou AD DS local)** (visualização): as contas de armazenamento do Azure podem ser ingressadas no domínio para um Active Directory Domain Services de Propriedade do cliente, assim como um servidor de arquivos do Windows Server ou um dispositivo nas. Você pode implantar um controlador de domínio local, em uma VM do Azure ou até mesmo como uma VM em outro provedor de nuvem; Os arquivos do Azure são independentes em que o controlador de domínio está hospedado. Quando uma conta de armazenamento está ingressada no domínio, o usuário final pode montar um compartilhamento de arquivos com a conta de usuário com a qual ele entrou em seu PC. A autenticação baseada em AD usa o protocolo de autenticação Kerberos.
- **Azure Active Directory Domain Services (azure AD DS)**: o Azure AD DS fornece um controlador de domínio gerenciado pela Microsoft que pode ser usado para recursos do Azure. Domínio ingressando em sua conta de armazenamento no Azure AD DS fornece benefícios semelhantes ao ingresso no domínio para um Active Directory de Propriedade do cliente. Essa opção de implantação é mais útil para cenários de elevação e deslocamento de aplicativos que exigem permissões baseadas no AD. Como o Azure AD DS fornece autenticação baseada em AD, essa opção também usa o protocolo de autenticação Kerberos.
- **Chave de conta de armazenamento do Azure**: compartilhamentos de arquivos do Azure também podem ser montados com uma chave de conta de armazenamento do Azure. Para montar um compartilhamento de arquivos dessa forma, o nome da conta de armazenamento é usado como o nome de usuário e a chave da conta de armazenamento é usada como uma senha. Usar a chave de conta de armazenamento para montar o compartilhamento de arquivos do Azure é efetivamente uma operação de administrador, pois o compartilhamento de arquivos montado terá permissões completas para todos os arquivos e pastas no compartilhamento, mesmo que eles tenham ACLs. Ao usar a chave da conta de armazenamento para montar por SMB, o protocolo de autenticação NTLMv2 é usado.

Para os clientes que migram de servidores de arquivos locais ou para criar novos compartilhamentos de arquivos em arquivos do Azure destinados a se comportarem como servidores de arquivos do Windows ou dispositivos NAS, o domínio que ingressa em sua conta de armazenamento para **Active Directory de Propriedade do cliente** é a opção recomendada. Para saber mais sobre o ingresso no domínio com sua conta de armazenamento em um Active Directory de propriedade do cliente, confira [Visão geral sobre Active Directory no serviço Arquivos do Azure](storage-files-active-directory-overview.md).

Se você pretende usar a chave de conta de armazenamento para acessar os compartilhamentos de arquivos do Azure, recomendamos o uso de pontos de extremidade de serviço, conforme descrito na seção [rede](#networking) .

## <a name="networking"></a>Rede
Os compartilhamentos de arquivos do Azure são acessíveis de qualquer lugar pelo ponto de extremidade público da conta de armazenamento. Isso significa que as solicitações autenticadas, como solicitações autorizadas pela identidade de logon de um usuário, podem se originar de maneira segura de dentro ou fora do Azure. Em muitos ambientes de clientes, uma montagem inicial do compartilhamento de arquivo do Azure em sua estação de trabalho local falhará, mesmo que as montagens de VMs do Azure tenham tido êxito. O motivo disso é que muitas organizações e ISPs (provedores de serviços de Internet) bloqueiam a porta que o SMB usa para se comunicar, a porta 445. Para ver o resumo de ISPs que permitem ou proíbem o acesso a partir da porta 445, vá para [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para desbloquear o acesso ao compartilhamento de arquivos do Azure, você tem duas opções principais:

- Desbloqueie a porta 445 da rede local da sua organização. Os compartilhamentos de arquivos do Azure só podem ser acessados externamente por meio do ponto de extremidade público usando protocolos seguros da Internet, como SMB 3,0 e a API filerest. Essa é a maneira mais fácil de acessar o compartilhamento de arquivos do Azure do local, já que ele não requer configuração avançada de rede além de alterar as regras de porta de saída de sua organização. no entanto, recomendamos que você remova as versões herdadas e preteridas do protocolo SMB, ou seja, SMB 1,0. Para saber como fazer isso, consulte [protegendo o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e [protegendo o Linux](storage-how-to-use-files-linux.md#securing-linux).

- Acesse compartilhamentos de arquivos do Azure por meio de uma conexão de ExpressRoute ou VPN. Quando você acessa o compartilhamento de arquivos do Azure por meio de um túnel de rede, é possível montar o compartilhamento de arquivos do Azure como um compartilhamento de arquivos local, pois o tráfego SMB não atravessa o limite organizacional.   

Embora, de uma perspectiva técnica, seja consideravelmente mais fácil montar os compartilhamentos de arquivos do Azure por meio do ponto de extremidade público, esperamos que a maioria dos clientes opte por montar seus compartilhamentos de arquivos do Azure por meio de uma conexão de ExpressRoute ou VPN. Para fazer isso, será necessário configurar o seguinte para o seu ambiente:  

- O **túnel de rede usando o ExpressRoute, site a site ou VPN ponto a site**: o túnel para uma rede virtual permite acessar compartilhamentos de arquivos do Azure do local, mesmo se a porta 445 estiver bloqueada.
- **Pontos de extremidade privados**: pontos de extremidade privados dão à sua conta de armazenamento um endereço IP dedicado de dentro do espaço de endereço da rede virtual. Isso habilita o túnel de rede sem a necessidade de abrir redes locais até todos os intervalos de endereços IP pertencentes aos clusters de armazenamento do Azure. 
- **Encaminhamento de DNS**: configure seu DNS local para resolver o nome da sua conta de armazenamento (ou seja, `storageaccount.file.core.windows.net` para as regiões de nuvem pública) para resolver o endereço IP dos seus pontos de extremidade privados.

Para planejar a rede associada à implantação de um compartilhamento de arquivos do Azure, consulte [considerações de rede de arquivos do Azure](storage-files-networking-overview.md).

## <a name="encryption"></a>Criptografia
Os arquivos do Azure dão suporte a dois tipos diferentes de criptografia: criptografia em trânsito, relacionada à criptografia usada ao montar/acessar o compartilhamento de arquivos do Azure e criptografia em repouso, que se relaciona com o modo como os dados são criptografados quando armazenados em disco. 

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Por padrão, todas as contas de armazenamento do Azure têm criptografia em trânsito habilitada. Isso significa que quando você montar um compartilhamento de arquivo via SMB ou acessá-lo por meio do protocolo FileREST (por exemplo, por meio do portal do Azure, do PowerShell/CLI ou de SDKs do Azure), os Arquivos do Azure só permitirão a conexão se for feita com o SMB 3.0 e com criptografia ou HTTPS. Os clientes que não são compatíveis com SMB 3.0 ou os clientes que são compatíveis com SMB 3.0, mas não com a criptografia SMB, não poderão montar o compartilhamento de arquivo do Azure se a criptografia em trânsito estiver habilitada. Para obter mais informações sobre quais sistemas operacionais são compatíveis com o SMB 3.0 com criptografia, consulte nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) e [Linux](storage-how-to-use-files-linux.md). Todas as versões atuais do PowerShell, da CLI e dos SDKs são compatíveis com HTTPS.  

Você pode desabilitar a criptografia em trânsito para uma conta de armazenamento do Azure. Quando a criptografia estiver desabilitada, os arquivos do Azure também permitirão SMB 2,1, SMB 3,0 sem criptografia e chamadas de API filerest não criptografadas via HTTP. O principal motivo para desabilitar a criptografia em trânsito é dar suporte a um aplicativo herdado que deve ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux. Os Arquivos do Azure só permitem conexões SMB 2.1 na mesma região do Azure que o compartilhamento de arquivo do Azure. Um cliente SMB 2.1 fora da região do Azure do compartilhamento de arquivo do Azure, como local ou em uma região diferente do Azure, não será capaz de acessar o compartilhamento de arquivo.

É altamente recomendável garantir que a criptografia de dados em trânsito esteja habilitada.

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Criptografia em repouso
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Proteção de dados
Os arquivos do Azure têm uma abordagem de várias camadas para garantir que seus dados sejam copiados em backup, recuperáveis e protegidos contra ameaças à segurança.

### <a name="soft-delete"></a>Exclusão reversível
A exclusão reversível para compartilhamentos de arquivos (visualização) é uma configuração de nível de conta de armazenamento que permite recuperar o compartilhamento de arquivos quando ele é excluído acidentalmente. Quando um compartilhamento de arquivos é excluído, ele faz a transição para um estado de exclusão reversível em vez de ser apagado permanentemente. Você pode configurar a quantidade de tempo que os dados com exclusão reversível são recuperáveis antes de serem excluídos permanentemente e restaurar o compartilhamento a qualquer momento durante esse período de retenção. 

É recomendável ativar a exclusão reversível para a maioria dos compartilhamentos de arquivos. Se você tiver um fluxo de trabalho em que o compartilhamento de exclusão é comum e esperado, você pode decidir ter um período de retenção muito curto ou não ter a exclusão reversível habilitada.

Para obter mais informações sobre exclusão reversível, consulte [impedir a exclusão acidental de dados](https://docs.microsoft.com/azure/storage/files/storage-files-prevent-file-share-deletion).

### <a name="backup"></a>Backup
Você pode fazer backup do compartilhamento de arquivos do Azure por meio de [instantâneos de compartilhamento](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files), que são cópias de ponto no tempo somente leitura do seu compartilhamento. Os instantâneos são incrementais, o que significa que eles contêm tantos dados quantos foram alterados desde o instantâneo anterior. Você pode ter até 200 instantâneos por compartilhamento de arquivos e mantê-los por até 10 anos. Você pode pegar esses instantâneos manualmente no portal do Azure, por meio do PowerShell ou da CLI (interface de linha de comando), ou pode usar o [backup do Azure](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json). Os instantâneos são armazenados no compartilhamento de arquivos, o que significa que, se você excluir o compartilhamento de arquivos, os instantâneos também serão excluídos. Para proteger os backups de instantâneo contra exclusão acidental, verifique se a exclusão reversível está habilitada para seu compartilhamento.

O [backup do Azure para compartilhamentos de arquivos do Azure](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) manipula o agendamento e a retenção de instantâneos. Seus recursos de GFS (avô-pai-filho) significam que você pode usar instantâneos diários, semanais, mensais e anuais, cada um com seu próprio período de retenção distinto. O backup do Azure também orquestra a habilitação da exclusão reversível e usa um bloqueio de exclusão em uma conta de armazenamento assim que qualquer compartilhamento de arquivos dentro dele é configurado para backup. Por fim, o backup do Azure fornece determinados recursos de monitoramento e alerta importantes que permitem aos clientes ter uma exibição consolidada de seus bens de backup.

Você pode executar restaurações em nível de item e de compartilhamento no portal do Azure usando o backup do Azure. Tudo o que você precisa fazer é escolher o ponto de restauração (um instantâneo específico), o arquivo ou diretório específico, se relevante, e depois o local (original ou alternativo) no qual você deseja restaurar. O serviço de backup lida com a cópia dos dados do instantâneo e mostra o progresso da restauração no Portal.

Para obter mais informações sobre backup, consulte [sobre o backup de compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Proteção avançada contra ameaças para arquivos do Azure (versão prévia)
A ATP (proteção avançada contra ameaças) para o armazenamento do Azure fornece uma camada adicional de inteligência de segurança que fornece alertas quando detecta atividade anômala em sua conta de armazenamento, por exemplo, tentativas incomuns de acessar a conta de armazenamento. ATP também executa a análise de reputação de hash de malware e alertará sobre o malware conhecido. Você pode configurar ATP em um nível de assinatura ou de conta de armazenamento por meio da central de segurança do Azure. 

Para obter mais informações, consulte [proteção avançada contra ameaças para o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection).

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Em geral, os recursos de arquivos do Azure e a interoperabilidade com outros serviços são os mesmos entre compartilhamentos de arquivos Premium e compartilhamentos de arquivos padrão, no entanto, há algumas diferenças importantes:
- **Modelo de cobrança**
    - Os compartilhamentos de arquivos Premium são cobrados usando um modelo de cobrança provisionado, o que significa que você paga pela quantidade de armazenamento que você provisiona, em vez da quantidade de armazenamento que realmente pede. 
    - Os compartilhamentos de arquivos padrão são cobrados usando um modelo pago conforme o uso, que inclui um custo base de armazenamento para a quantidade de armazenamento que você está consumindo e, em seguida, um custo de transação adicional com base em como você usa o compartilhamento. Com compartilhamentos de arquivos padrão, sua fatura aumentará se você usar (leitura/gravação/montagem) do compartilhamento de arquivos do Azure mais.
- **Opções de redundância**
    - Os compartilhamentos de arquivos Premium só estão disponíveis para armazenamento com redundância local (LRS) e com redundância de zona (ZRS).
    - Os compartilhamentos de arquivos padrão estão disponíveis para o armazenamento localmente redundante, com redundância de zona, com redundância geográfica (GRS) e com redundância de zona geográfica (GZRS).
- **Tamanho máximo do compartilhamento de arquivos**
    - Os compartilhamentos de arquivos Premium podem ser provisionados para até 100 TiB sem nenhum trabalho adicional.
    - Por padrão, os compartilhamentos de arquivos padrão podem abranger até 5 TiB, embora o limite de compartilhamento possa ser aumentado para 100 TiB, optando pelo sinalizador de recurso de conta de armazenamento de *compartilhamento de arquivo grande* . Os compartilhamentos de arquivos padrão podem abranger até 100 TiB para contas de armazenamento com redundância local ou de zona. Para obter mais informações sobre como aumentar os tamanhos de compartilhamento de arquivos, consulte [habilitar e criar compartilhamentos de arquivos grandes](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share).
- **Disponibilidade regional**
    - Os compartilhamentos de arquivos Premium não estão disponíveis em todas as regiões, e o suporte com redundância de zona está disponível em um subconjunto menor de regiões. Para descobrir se os compartilhamentos de arquivos premium estão disponíveis atualmente em sua região, confira a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para o Azure. Para descobrir quais regiões dão suporte a ZRS, confira [suporte da zona de disponibilidade do Azure por região](../../availability-zones/az-region.md). Para nos ajudar a priorizar novas regiões e recursos da camada Premium, preencha esta [pesquisa](https://aka.ms/pfsfeedback).
    - Os compartilhamentos de arquivos padrão estão disponíveis em todas as regiões do Azure.
- O AKS (serviço kubernetes do Azure) dá suporte a compartilhamentos de arquivos Premium na versão 1,13 e posterior.

Depois que um compartilhamento de arquivos é criado como um compartilhamento de arquivos Premium ou Standard, não é possível convertê-lo automaticamente para a outra camada. Se desejar alternar para a outra camada, você deverá criar um novo compartilhamento de arquivos nessa camada e copiar manualmente os dados do compartilhamento original para o novo compartilhamento que você criou. É recomendável usar o `robocopy` para Windows ou `rsync` para MacOS e Linux para executar essa cópia.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Noções básicas sobre provisionamento para compartilhamentos de arquivos Premium
Os compartilhamentos de arquivos Premium são provisionados com base em uma taxa de GiB/IOPS/transferência fixa. Para cada GiB provisionado, o compartilhamento emitirá um IOPS e uma taxa de transferência de 0,1 MiB/s até os limites máximos por compartilhamento. O provisionamento mínimo permitido é de 100 GiB, com um IOPS e uma taxa de transferência mínimos.

Com base no melhor esforço, todos os compartilhamentos poderão acumular até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo do tamanho do compartilhamento. Os novos compartilhamentos começam com o crédito de intermitência completa com base na capacidade provisionada.

Os compartilhamentos devem ser provisionados em incrementos de 1 GiB. O tamanho mínimo é 100 GiB, o próximo tamanho é 101 GiB e assim por diante.

> [!TIP]
> IOPS de linha de base = 1 * GiB provisionado. (Até um máximo de 100.000 IOPS).
>
> Limite de intermitência = 3 * IOPS de linha de base. (Até um máximo de 100.000 IOPS).
>
> taxa de egresso = 60 MiB/s + 0, 6 * provisionamento GiB
>
> taxa de entrada = 40 MiB/s + 0, 4 * provisionamento GiB

O tamanho do compartilhamento provisionado é especificado por cota de compartilhamento. A cota de compartilhamento pode ser aumentada a qualquer momento, mas só pode ser reduzida após 24 horas desde o último aumento. Depois de aguardar 24 horas sem um aumento de cota, você pode diminuir a cota de compartilhamento quantas vezes desejar, até aumentá-la novamente. As alterações de escala de taxa de transferência/IOPS entrarão em vigor em alguns minutos após a alteração do tamanho.

É possível diminuir o tamanho do compartilhamento provisionado abaixo do GiB usado. Se você fizer isso, não perderá dados, mas ainda será cobrado pelo tamanho usado e receberá o desempenho (IOPS de linha de base, taxa de transferência e IOPS de intermitência) do compartilhamento provisionado, não o tamanho usado.

A tabela a seguir ilustra alguns exemplos dessas fórmulas para os tamanhos de compartilhamento provisionados:

|Capacidade (GiB) | IOPS de linha de base | IOPS de intermitência | Saída (MiB/s) | Entrada (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1.500   | 90   | 60   |
|1\.024       | 1\.024   | Até 3.072   | 122   | 81   |
|5.120       | 5.120   | Até 15.360  | 368   | 245   |
|10.240      | 10.240  | Até 30.720  | 675 | 450   |
|33.792      | 33.792  | Até 100.000 | 2.088 | 1.392   |
|51.200      | 51.200  | Até 100.000 | 3.132 | 2.088   |
|102.400     | 100.000 | Até 100.000 | 6.204 | 4.136   |

> [!NOTE]
> O desempenho dos compartilhamentos de arquivos está sujeito aos limites de rede da máquina, largura de banda de rede disponível, tamanhos de e/s, paralelismo, entre muitos outros fatores. Por exemplo, com base no teste interno com 8 tamanhos de e/s de leitura/gravação de KiB, uma única máquina virtual do Windows, *F16s_v2 padrão*, conectada ao compartilhamento de arquivos Premium em SMB poderia alcançar IOPS de leitura de 20 mil e IOPS de gravação de 15.000. Com tamanhos de e/s de leitura/gravação de MiB 512, a mesma VM pode atingir a saída de 1,1 GiB/s e a taxa de transferência de entrada de 370 MiB/s. Para obter a escala de desempenho máxima, distribua a carga entre várias VMs. Consulte o [Guia de solução de problemas](storage-troubleshooting-files-performance.md) para alguns problemas comuns de desempenho e soluções alternativas.

#### <a name="bursting"></a>Bursting
Os compartilhamentos de arquivos Premium podem aumentar seu IOPS até um fator de três. A intermitência é automatizada e opera com base em um sistema de crédito. A intermitência funciona em uma base de melhor esforço e o limite de intermitência não é uma garantia, os compartilhamentos de arquivos podem *aumentar até* o limite.

Os créditos se acumulam em um Bucket de intermitência sempre que o tráfego para o compartilhamento de arquivos está abaixo do IOPS de linha de base. Por exemplo, um compartilhamento de GiB 100 tem 100 IOPS de linha de base. Se o tráfego real no compartilhamento era de 40 IOPS para um intervalo específico de 1 segundo, o IOPS de 60 não utilizado será creditado em um Bucket de intermitência. Esses créditos serão então usados mais tarde, quando as operações excederem o IOPs de linha de base.

> [!TIP]
> Tamanho do Bucket de intermitência = IOPS de linha de base * 2 * 3600.

Sempre que um compartilhamento exceder o IOPS de linha de base e tiver créditos em um Bucket de intermitência, ele será rompido. Os compartilhamentos podem continuar a aumentar, contanto que os créditos permaneçam, embora os compartilhamentos menores que 50 TiB permanecerão apenas no limite de intermitência de até uma hora. Compartilhamentos maiores que 50 TiB podem, tecnicamente, exceder esse limite de hora, até duas horas, mas isso se baseia no número de créditos de intermitência acumulados. Cada e/s além do IOPS de linha de base consome um crédito e quando todos os créditos são consumidos, o compartilhamento retornaria ao IOPS de linha de base

Os créditos de compartilhamento têm três Estados:

- A acumulação, quando o compartilhamento de arquivos está usando menos do que o IOPS de linha de base.
- Recusando, quando o compartilhamento de arquivos estiver intermitente.
- Constante restante, quando não há créditos ou IOPS de linha de base em uso.

Novos compartilhamentos de arquivos começam com o número total de créditos em seu Bucket de intermitência. Os créditos de intermitência não serão acumulados se o IOPS de compartilhamento cair abaixo do IOPS de linha de base devido à limitação pelo servidor.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Habilitar compartilhamentos de arquivo padrão para abranger até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Limitações
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migração
Em muitos casos, você não estará estabelecendo um novo compartilhamento de arquivos de rede para sua organização, mas migrará um compartilhamento de arquivos existente de um servidor de arquivos local ou de um dispositivo NAS para os arquivos do Azure. A escolha da estratégia e da ferramenta de migração correta para seu cenário é importante para o sucesso da sua migração. 

O [artigo Visão geral da migração](storage-files-migration-overview.md) aborda brevemente os conceitos básicos e contém uma tabela que leva você a guias de migração que provavelmente abrangem seu cenário.

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Implantando Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantando a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
* [Confira o artigo Visão geral da migração para encontrar o guia de migração para seu cenário](storage-files-migration-overview.md)
