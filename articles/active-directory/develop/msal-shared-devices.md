---
title: Visão geral do modo de dispositivo compartilhado
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o modo de dispositivo compartilhado para habilitar o compartilhamento de dispositivos para seus primeiros trabalhadores.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550234"
---
# <a name="overview-of-shared-device-mode"></a>Visão geral do modo de dispositivo compartilhado

O modo de dispositivo compartilhado é um recurso do Azure Active Directory que permite que você crie aplicativos que dão suporte a trabalhadores de primeira e habilitam o modo de dispositivo compartilhado nos dispositivos implantados neles.

> [!NOTE]
> Esse recurso está em uma versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-are-firstline-workers"></a>O que são trabalhadores de primeira a?

Os trabalhos de primeira vez são funcionários de varejo, agentes de manutenção e campo, pessoal médico e outros usuários que não ficam na frente de um computador ou usam email corporativo para colaboração. As seções a seguir introduzem os aspectos e os desafios de dar suporte a trabalhadores de primeira vez, seguidos por uma introdução aos recursos fornecidos pela Microsoft que permitem que seu aplicativo seja usado pelos trabalhos de primeira organização.

### <a name="challenges-of-supporting-firstline-workers"></a>Desafios de dar suporte a trabalhadores de primeira a

A habilitação dos fluxos de trabalho do operador First-up inclui desafios geralmente não apresentados por operadores de informações típicos. Esses desafios podem incluir alta taxa de rotatividade e menos familiaridade com as ferramentas de produtividade principais de uma organização. Para capacitar seus primeiros trabalhadores, as organizações estão adotando estratégias diferentes. Alguns estão adotando uma estratégia BYOD (Traga seu próprio dispositivo) na qual seus funcionários usam aplicativos de negócios em seu telefone pessoal, enquanto outros fornecem aos seus funcionários dispositivos compartilhados, como tablets iPads ou Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Suporte a vários usuários em dispositivos criados para um usuário

Como os dispositivos móveis que executam o iOS ou Android foram projetados para usuários únicos, a maioria dos aplicativos otimiza sua experiência para uso por um único usuário. Parte dessa experiência otimizada significa habilitar o logon único entre aplicativos e manter os usuários conectados em seu dispositivo. Quando um usuário remove sua conta de um aplicativo, o aplicativo normalmente não considera um evento relacionado à segurança. Muitos aplicativos até mesmo mantêm as credenciais de um usuário para entrada rápida. Você pode até mesmo ter feito isso quando tiver excluído um aplicativo do seu dispositivo móvel e, em seguida, reinstalá-lo, apenas para descobrir que você ainda está conectado.

### <a name="global-sign-in-and-sign-out-sso"></a>Entrada global e saída (SSO)

Para permitir que os funcionários de uma organização usem seus aplicativos em um pool de dispositivos compartilhados por esses funcionários, os desenvolvedores precisam habilitar a experiência oposta. Os funcionários devem ser capazes de escolher um dispositivo do pool e executar um único gesto para "torná-lo" para a duração de sua mudança. No final de seu turno, eles devem ser capazes de executar outro gesto para sair globalmente no dispositivo, com todas as suas informações pessoais e da empresa removidas para que possam devolvê-las ao pool de dispositivos. Além disso, se um funcionário esquecer de sair, o dispositivo deverá ser desconectado automaticamente no final do turno e/ou após um período de inatividade.

Azure Active Directory habilita esses cenários com um recurso chamado de **modo de dispositivo compartilhado**.

## <a name="introducing-shared-device-mode"></a>Introdução ao modo de dispositivo compartilhado

Como mencionado, o modo de dispositivo compartilhado é um recurso do Azure Active Directory que permite que você:

* Criar aplicativos que dão suporte a trabalhadores de primeira geração
* Implantar dispositivos nos primeiros trabalhadores e ativar o modo de dispositivo compartilhado

### <a name="build-applications-that-support-firstline-workers"></a>Criar aplicativos que dão suporte a trabalhadores de primeira geração

Você pode dar suporte a trabalhos de primeira vez em seus aplicativos usando a MSAL (biblioteca de autenticação da Microsoft) e o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para habilitar um estado de dispositivo chamado de *modo de dispositivo compartilhado*. Quando um dispositivo estiver no modo de dispositivo compartilhado, a Microsoft fornecerá ao seu aplicativo informações para permitir que ele modifique seu comportamento com base no estado do usuário no dispositivo, protegendo os dados do usuário.

Os recursos com suporte são:

* **Conecte-se a um dispositivo de usuário em todo o** aplicativo com suporte.
* **Desconecte um dispositivo de usuário em todo o** aplicativo com suporte.
* **Consulte o estado do dispositivo** para determinar se seu aplicativo está em um dispositivo que está no modo de dispositivo compartilhado.
* **Consulte o estado do dispositivo do usuário** no dispositivo para determinar se algo foi alterado desde a última vez em que seu aplicativo foi usado.

O suporte ao modo de dispositivo compartilhado deve ser considerado um aprimoramento de segurança e uma atualização de recursos para seu aplicativo e pode ajudar a aumentar sua adoção em ambientes altamente regulamentados, como saúde e finanças.

Os usuários dependem de você para garantir que seus dados não sejam vazados para outro usuário. O modo de compartilhamento de dispositivo fornece sinais úteis para indicar ao aplicativo que uma alteração que você deve gerenciar ocorreu. Seu aplicativo é responsável por verificar o estado do usuário no dispositivo toda vez que o aplicativo é usado, limpando os dados do usuário anterior. Isso inclui se ele é recarregado do plano de fundo em várias tarefas. Em uma alteração de usuário, você deve garantir que os dados do usuário anterior sejam apagados e que todos os dados armazenados em cache que estão sendo exibidos em seu aplicativo sejam removidos. Recomendamos que você sempre execute um processo de revisão de segurança completo depois de adicionar o recurso de modo de dispositivo compartilhado ao seu aplicativo.

Para obter detalhes sobre como modificar seus aplicativos para dar suporte ao modo de dispositivo compartilhado, consulte a seção [próximas etapas](#next-steps) no final deste artigo.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Implantar dispositivos nos primeiros trabalhadores e ativar o modo de dispositivo compartilhado

Depois que os aplicativos dão suporte ao modo de dispositivo compartilhado e incluem os dados necessários e as alterações de segurança, você pode anuncia-los como sendo utilizáveis por primeiros trabalhadores.

Os administradores de dispositivos de uma organização podem implantar seus dispositivos e seus aplicativos em seus armazenamentos e locais de trabalho por meio de uma solução de MDM (gerenciamento de dispositivo móvel), como Microsoft Intune. Parte do processo de provisionamento é marcar o dispositivo como um *dispositivo compartilhado*. Os administradores configuram o modo de dispositivo compartilhado implantando o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e definindo o modo de dispositivo compartilhado por meio de parâmetros de configuração. Depois de executar essas etapas, todos os aplicativos que dão suporte ao modo de dispositivo compartilhado usarão o Microsoft Authenticator aplicativo para gerenciar seu estado de usuário e fornecer recursos de segurança para o dispositivo e a organização.

## <a name="next-steps"></a>Próximas etapas

Damos suporte a plataformas iOS e Android para o modo de dispositivo compartilhado. Examine a documentação abaixo para que sua plataforma comece a dar suporte a trabalhadores de primeira a seus aplicativos.

* [Suporte ao modo de dispositivo compartilhado para iOS](msal-ios-shared-devices.md)
* [Suporte ao modo de dispositivo compartilhado para Android](msal-android-shared-devices.md)
