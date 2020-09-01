---
title: Lista de verificação de pré-implantação para implantar Azure Stack dispositivo de GPU de borda | Microsoft Docs
description: Este artigo descreve as informações que podem ser coletadas antes da implantação do Azure Stack dispositivo de GPU de borda.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 9d7b3388b7e3a31c23b34b21017f012d40e9f849
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268135"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-gpu-device"></a>Lista de verificação de implantação para seu dispositivo de GPU do Azure Stack Edge  

Este artigo descreve as informações que podem ser coletadas à frente da implantação real do seu dispositivo de Azure Stack Edge. 

Use a seguinte lista de verificação para garantir que você tenha essas informações depois de ter colocado um pedido para um dispositivo Azure Stack Edge e antes de receber o dispositivo. 

## <a name="deployment-checklist"></a>Lista de verificação para implantação 

| Estágio                             | Parâmetro                                                                                                                                                                                                                           | Detalhes                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gerenciamento de dispositivo               | <li>Assinatura Microsoft Azure</li><li>API do Graph do Active Directory do Azure</li><li>Conta de Armazenamento do Microsoft Azure</li>|<li>Habilitado para Azure Stack borda/Gateway do Data Box, permissões de colaborador</li><li>Garantir o acesso de administrador ou usuário</li><li>Precisa de credenciais de acesso</li> |
| Instalação do dispositivo               | Cabos de alimentação no pacote. <br>Para nós, um cabo SVE 18/3 classificado para 125 V e 15 amps com um 15P de C13 do NEMA 5, com um conector de entrada para saída (Input to Output), é enviado.                                                                                                                                                                                                          | Fornecido com o dispositivo.<br>Para obter mais informações, consulte a lista de [cabos de energia com suporte por país](azure-stack-edge-technical-specifications-power-cords-regional.md)                                                                                        |
|                                   | <li>Pelo menos 1 X 1-cabo de rede RJ-45 de GbE para a porta 1  </li><li> Pelo menos 1 X 25-GbE SFP + cabo de cobre para a porta 3, porta 4, porta 5 ou porta 6</li>| O cliente precisa adquirir esses cabos.<br>Para obter uma lista completa dos cabos de rede, comutadores e transceptores com suporte para placas de rede do dispositivo, consulte [matriz de interoperabilidade da série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e [adaptadores de rede Mellanox Dual Port 25g ConnectX-4 Channel Adapter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Conexão do dispositivo pela primeira vez      | A porta 1 tem um IP fixo (192.168.100.10/24) para a conexão inicial. <li>Exigir um laptop para conexão direta com a porta 1, com um endereço IP na rede 192.168.100.0/24.</li><li> Use a interface do usuário local do dispositivo em: `https://192.168.100.10` para configuração adicional.</li><li> Uma opção mínima de 1 GbE deve ser usada para o dispositivo depois que a configuração inicial for concluída. A interface do usuário da Web local não estará acessível se o comutador conectado não for de pelo menos 1 GbE.</li>|                                                                                                                   |
| Logon do dispositivo                      | A senha de administrador do dispositivo deve ter entre 8 e 16 caracteres. <br>Deve conter três dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais.                                            | A senha padrão é *password1* que expira na primeira entrada.                                                     |
| Configurações de rede                  | O dispositivo vem com duas portas de rede de 2 x 1 GbE, 4 x 25 GbE. <li>A porta 1 é usada para definir apenas as configurações de gerenciamento. Uma ou mais portas de dados podem ser conectadas e configuradas. </li><li> Pelo menos uma interface de rede de dados entre a porta 2 – a porta 6 precisa estar conectada à Internet (com conectividade com o Azure).</li><li> As configurações de IP dão suporte à configuração de DHCP/IPv4 estático. | A configuração estática de IPv4 requer IP, servidor DNS e gateway padrão.                                                                                                                  |
| Configurações de rede de computação     | <li>Exigir 2 IPs públicos estáticos para nós kubernetes e pelo menos um IP estático para Azure Stack serviço de Hub do Edge para acessar módulos de computação.</li><li>Exija um IP para cada serviço extra ou contêiner que precise ser acessado externamente de fora do cluster kubernetes.</li>                                                                                                                       | Somente a configuração IPv4 estática tem suporte.                                                                      |
| Adicional Configurações de proxy Web     | <li>IP/FQDN do servidor proxy da Web, porta </li><li>Nome de usuário do proxy Web, senha</li>                                                                                                                                                                                                    | Atualmente não há suporte para a configuração de computação.                                                                     |
| Configurações de firewall e porta        | Use os [padrões de URLs e as portas listadas](azure-stack-edge-system-requirements.md#networking-port-requirements) a serem permitidas para IPS de dispositivo.                                                                                                                                                  |                                                                                                                   |
| Adicional Endereço MAC            | Se o endereço MAC precisar estar na lista de permissões, obtenha o endereço da porta conectada da interface do usuário local do dispositivo. |                                                                                                                   |
| Adicional Porta do comutador de rede    | O dispositivo hospeda VMs do Hyper-V para computação. Algumas configurações de porta de comutador de rede não acomodam essas instalações por padrão.                                                                                                        |                                                                                                                   |
| Aconselhável Configurações de hora       | Configure o fuso horário, o servidor NTP primário, o servidor NTP secundário.                                                                                                                                                                    | Configure o servidor NTP primário e secundário na rede local.<br>Se o servidor local não estiver disponível, os servidores NTP públicos poderão ser configurados.                                                    |
| Adicional Atualizar configurações do servidor | <li>Exigir o endereço IP do servidor de atualização na rede local, caminho para o servidor do WSUS. </li> | Por padrão, o servidor Windows Update público é usado.|
| Configurações do dispositivo                   | <li>Nome do dispositivo registrado na organização DNS </li><li>Domínio DNS</li> |                                                                                                                   |
| Adicional Certificado                      | Usar os certificados gerados pelo dispositivo <br><br> Se colocar seus próprios certificados, você precisará de: <li>Certificado de autenticação de raiz confiável no formato DER com *. cer* </li><li>Certificados de ponto de extremidade no formato *pfx*</li>|Os certificados de ponto de extremidade incluem Azure Resource Manager, armazenamento de BLOBs, interface do usuário da Web local.                                                                                                                   |
| Ativação                        | Exigir chave de ativação do Azure Stack recurso de borda/Gateway do Data Box.                                                                                                                                                       | Depois de gerada, a chave expira em 3 dias.                                                                        |


## <a name="next-steps"></a>Próximas etapas

Prepare-se para implantar o [dispositivo do Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).



