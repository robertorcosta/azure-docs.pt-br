---
title: Notas de versão de visualização do Azure Stack Edge | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para o Gateway do Azure Data Box executando a versão de disponibilidade geral.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: de0847beb92ebc95e1998d88cae93dbc19c3fb27
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180041"
---
# <a name="azure-stack-edge-with-gpu-preview-release-notes"></a>Borda de Azure Stack com notas de versão de visualização de GPU

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão de visualização de 2008 para seus dispositivos Azure Stack Edge com GPU.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o dispositivo Azure Stack Edge, Examine cuidadosamente as informações contidas nas notas de versão.

Esta versão do **Azure Stack Edge 2008** corresponde à seguinte versão de software:

- **2.1.1328.1904**



## <a name="known-issues-in-ga-release"></a>Problemas conhecidos na versão GA

A tabela a seguir fornece um resumo dos problemas conhecidos para o dispositivo Azure Stack Edge.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Borda do Azure Stack + SQL do Azure | A criação do banco de dados SQL requer acesso de administrador.   |Execute as etapas a seguir em vez das etapas de 1-2 no [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>Na interface do usuário local do seu dispositivo, habilite a interface de computação. Selecione **computação > porta n º > habilitar para computação > aplicar.**</li><li>Em uma janela do PowerShell conectada ao dispositivo, execute `Add-HcsComputeNetwork` . </li><li>Baixar `sqlcmd` no computador cliente de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Conecte-se ao seu endereço IP da interface de computação (a porta que estava habilitada), adicionando um ", 1401" ao final do endereço.</li><li>O comando final terá a seguinte aparência: sqlcmd-S {interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Depois disso, as etapas 3-4 da documentação atual devem ser idênticas. </li></ul> |
| **2.** |Atualizar| Não há suporte para alterações incrementais em BLOBs restaurados via **atualização** |Para pontos de extremidade de BLOB, atualizações parciais de BLOBs após uma atualização, podem resultar em atualizações não sendo carregadas para a nuvem. Por exemplo, sequência de ações como:<ul><li>Criar blob na nuvem. Ou exclua um blob carregado anteriormente do dispositivo.</li><li>Atualize o blob da nuvem para o dispositivo usando a funcionalidade de atualização.</li><li>Atualize apenas uma parte do blob usando as APIs REST do SDK do Azure.</li></ul>Essas ações podem fazer com que as seções atualizadas do BLOB não sejam atualizadas na nuvem. <br>**Solução alternativa**: Use ferramentas como Robocopy ou cópia de arquivo regular por meio do Explorer ou da linha de comando para substituir BLOBs inteiros.|
|**3.**|Limitação|Durante a limitação, se novas gravações não forem permitidas no dispositivo, as gravações feitas pelo cliente NFS falharão com o erro "permissão negada".| O erro será mostrado como abaixo:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar o diretório ' test ': permissão negada|
|**4.**|Ingestão de armazenamento de BLOBs|Ao usar o AzCopy versão 10 para ingestão de armazenamento de BLOBs, execute AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se esses limites não forem fornecidos para AzCopy, ele poderá enviar um grande número de solicitações para o dispositivo e resultar em problemas com o serviço.|
|**5.**|Contas de armazenamento em camadas|O seguinte se aplica ao usar contas de armazenamento em camadas:<ul><li> Somente há suporte para BLOBs de blocos. Blobs de página não têm suporte.</li><li>Não há suporte para instantâneo ou API de cópia.</li><li> Não há suporte para a ingestão de carga de trabalho do Hadoop `distcp` , pois ela usa a operação de cópia de forma intensiva.</li></ul>||
|**6.**|Conexão de compartilhamento NFS|Se vários processos estiverem sendo copiados para o mesmo compartilhamento e o `nolock` atributo não for usado, você poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando mount para copiar arquivos para o compartilhamento NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Cluster do Kubernetes|Ao aplicar uma atualização em seu dispositivo que está executando um cluster kubernetes, as máquinas virtuais kubernetes serão reiniciadas e reiniciadas. Nessa instância, somente os pods implantados com réplicas especificadas são automaticamente restaurados após uma atualização.  |Se você tiver criado pods individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, esses pods não serão restaurados automaticamente após a atualização do dispositivo. Você precisará restaurar esses pods.<br>Um conjunto de réplicas substitui os pods que são excluídos ou encerrados por qualquer motivo, como falha de nó ou atualização de nó de interrupção. Por esse motivo, recomendamos que você use um conjunto de réplicas mesmo que seu aplicativo exija apenas um único Pod.|
|**8.**|Cluster do Kubernetes|Só há suporte para kubernetes no Azure Stack Edge com o Helm V3 ou posterior. Para obter mais informações, acesse as perguntas frequentes [: remoção do gaveta](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure Arc + borda de Azure Stack|Não há suporte para implantações de arco do Azure se o proxy Web estiver configurado no dispositivo de borda Azure Stack.||
|**254.**|Kubernetes |A porta 31000 é reservada para o painel do kubernetes. Da mesma forma, na configuração padrão, os endereços IP 10.96.0.1 e 10.96.0.10, são reservados para o serviço kubernetes e o serviço DNS principal, respectivamente.|Não use IPs reservados.|
|**11.**|Kubernetes |O kubernetes atualmente não permite serviços de balanceador de vários protocolos. Por exemplo, um serviço DNS que teria que escutar em TCP e UDP. |Para solucionar essa limitação de kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de Pod. Esses serviços usam a mesma chave de compartilhamento e spec. loadBalancerIP para compartilhar o mesmo endereço IP. Os IPs também podem ser compartilhados se você tiver mais serviços do que os endereços IP disponíveis. <br> Para obter mais informações, consulte [compartilhamento de endereço IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**12.**|Cluster do Kubernetes|Os módulos existentes do Marketplace Azure IoT Edge não serão executados no cluster kubernetes como plataforma de hospedagem para IoT Edge no dispositivo Azure Stack Edge.|Os módulos precisarão ser modificados antes de serem implantados no dispositivo Azure Stack Edge. Para obter mais informações, consulte modificar módulos de Azure IoT Edge do Marketplace para executar em Azure Stack dispositivo de borda.<!-- insert link-->|
|**13.**|Kubernetes |Não há suporte para montagens de associação baseadas em arquivo com Azure IoT Edge em kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de conversão para converter `ContainerCreate` opções em construções kubernetes. Criar `Binds` mapas para o diretório hostpath ou criar e, portanto, as montagens de associação baseadas em arquivo não podem ser associadas a caminhos em contêineres de IOT Edge.|
|**140.**|Kubernetes |Se você colocar seus próprios certificados para IoT Edge e adicioná-los em seu dispositivo Azure Stack Edge, os novos certificados não serão selecionados como parte da atualização de gráficos do Helm.|Para solucionar esse problema, [Conecte-se à interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md). Reiniciar `iotedged` e `edgehub` pods.|

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o dispositivo de borda Azure Stack com GPU](azure-stack-edge-gpu-deploy-prep.md)

