---
title: Glossário de anexação do aplicativo MSIX da área de trabalho virtual do Windows – Azure
description: Um glossário dos termos e conceitos do MSIX app Attach.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f3cc8495f673c8b428aa9e6ace2747a70c5b0847
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556206"
---
# <a name="msix-app-attach-glossary"></a>Glossário de anexação de aplicativo MSIX

Este artigo é uma lista de definições para os principais termos e conceitos relacionados à anexação do aplicativo MSIX.

## <a name="msix-container"></a>Contêiner MSIX

Um contêiner MSIX é onde os aplicativos MSIX são executados. Para saber mais, confira [contêineres MSIX](/windows/msix/msix-container).

## <a name="msix-application"></a>Aplicativo MSIX 

Um aplicativo armazenado em um arquivo MSIX.

## <a name="msix-package"></a>Pacote MSIX 

Um pacote MSIX é um arquivo ou aplicativo MSIX.

## <a name="msix-share"></a>Compartilhamento de MSIX

Um compartilhamento MSIX é um compartilhamento de rede que mantém pacotes MSIX expandidos. Compartilhamentos MSIX dão suporte a SMB 3 ou posterior. Os aplicativos são preparados deste compartilhamento de MSIX sem a necessidade de mover os arquivos de aplicativo para a unidade do sistema.

## <a name="repackage"></a>Reempacotar

O reempacotamento usa um aplicativo não MSIX e o converte em MSIX usando a ferramenta de empacotamento MSIX (MPT). Para obter mais informações, consulte [visão geral da ferramenta de empacotamento MSIX](/windows/msix/packaging-tool/tool-overview).

## <a name="expand"></a>Expanda

Expandir o pacote MSIX é um processo de várias etapas. Ele usa o arquivo MSIX e coloca seu conteúdo em um arquivo VHD (x) ou CIM. 

Para expandir um pacote MSIX:

1. Obtenha um pacote MSIX (arquivo MSIX).
2. Renomeie o arquivo MSIX para um arquivo. zip.
3. Descompacte o arquivo. zip resultante em uma pasta.
4. Crie um VHD que tenha o mesmo tamanho da pasta.
5. Monte o VHD.
6. Inicializar um disco.
7. Crie uma partição.
8. Formate a partição.
9. Copie o conteúdo descompactado para o VHD.
10. Use a ferramenta MSIXMGR para aplicar ACLs no conteúdo do VHD.
11. Desmonte o VHD (x) ou [CIM](#cim).

## <a name="upload-an-msix-package"></a>Carregar um pacote MSIX 

Carregar um pacote MSIX envolve carregar o VHD (x) ou [CIM](#cim) que contém um pacote MSIX expandido para o compartilhamento MSIX.

Na área de trabalho virtual do Windows, os carregamentos acontecem uma vez por compartilhamento MSIX. Depois de carregar um pacote, todos os pools de hosts na mesma assinatura podem fazer referência a ele.

## <a name="publish-an-msix-package"></a>Publicar um pacote MSIX

Na área de trabalho virtual do Windows, a publicação de um pacote MSIX vincula-o a um aplicativo ou área de trabalho remota.

## <a name="assign-an-msix-package"></a>Atribuir um pacote MSIX 

Na área de trabalho virtual do Windows, um pacote MSIX publicado deve ser atribuído a um usuário ou grupo de usuários do Domínio do Active Directory (AD DS) ou Azure Active Directory (Azure AD).

## <a name="staging"></a>Preparo

O preparo envolve duas coisas:

- Montar o VHD (x) ou [CIM](#cim) na VM.
- Notificar o sistema operacional de que o pacote MSIX está disponível para registro.

## <a name="registration"></a>Registro

O registro torna um pacote MSIX preparado disponível para seus usuários. O registro do está em uma base por usuário. Se você não registrou explicitamente um aplicativo para esse usuário específico, ele não poderá executar o aplicativo.

Há dois tipos de registro: regular e atrasado.

### <a name="regular-registration"></a>Registro regular

No registro regular, cada aplicativo atribuído a um usuário é totalmente registrado. O registro ocorre durante o tempo que o usuário entra na sessão, o que pode afetar o tempo necessário para que eles comecem a usar a área de trabalho virtual do Windows.

### <a name="delayed-registration"></a>Registro atrasado

No registro atrasado, cada aplicativo atribuído ao usuário é registrado parcialmente. Registro parcial significa que o bloco do menu iniciar e o clique duas vezes em associações de arquivo são registrados. O registro ocorre enquanto o usuário entra em sua sessão e, portanto, tem um impacto mínimo sobre o tempo necessário para começar a usar a área de trabalho virtual do Windows. O registro é concluído somente quando o usuário executa o aplicativo no pacote MSIX.

No momento, o registro atrasado é a configuração padrão para anexação do aplicativo MSIX.

## <a name="deregistration"></a>Cancelamento

O cancelamento de registro remove um pacote de MSIX registrado, mas que não está em execução para um usuário. O cancelamento do registro ocorre enquanto o usuário sai da sessão. Durante o cancelamento do registro, o anexação do aplicativo MSIX envia dados de aplicativo específicos ao usuário para o perfil de usuário local.

## <a name="destage"></a>Cancelar preparo

A desmontagem notifica o sistema operacional que um pacote ou aplicativo MSIX que atualmente não está em execução e não é preparado para que qualquer usuário possa ser desmontado. Isso remove todas as referências a ele no sistema operacional.

## <a name="cim"></a>CIM

. CIM é uma nova extensão de arquivo associada ao CimFS (Composite Image Files System). Montar e desmontar arquivos CIM é mais rápido que os arquivos VHD. O CIM também consome menos CPU e memória do que o VHD.

A tabela a seguir é uma comparação de desempenho entre VHD e CimFS. Esses números foram o resultado de uma execução de teste com arquivos de 800 MB em cada formato executado em um computador DSv4.

|  Especificações                          | VHD                     | CimFS   |
|---------------------------------|--------------------------|-----------|
| Tempo médio de montagem     | 356 MS                     | 255 MS      |
| Tempo médio de desmontagem   | 1615 MS                    | 36 MS       |
| Consumo da memória | 6% (de 8 GB)                      | 2% (de 8 GB)       |
| CPU (pico de contagem)          | Maximizado várias vezes | Sem impacto |

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre o anexo de aplicativo do MSIX, Confira nossa [visão geral](what-is-app-attach.md) e [perguntas frequentes](app-attach-faq.md). Caso contrário, comece a [Configurar a anexação de aplicativo](app-attach.md).

