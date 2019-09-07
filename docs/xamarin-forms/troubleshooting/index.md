---
title: Solução de problemas
description: Condições de erro comuns e como resolvê-las
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 93cab36b21e2fe73a0e6890140b5ebaeb32f7951
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760029"
---
# <a name="troubleshooting"></a>Solução de problemas

_Condições de erro comuns e como resolvê-las_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Erro: "Não é possível localizar uma versão do Xamarin. Forms compatível com..."

Os erros a seguir podem aparecer na janela do **console do pacote** ao atualizar todos os pacotes NuGet em uma solução Xamarin. Forms ou em um projeto de aplicativo Android do Xamarin. Forms:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>O que causa esse erro?

Visual Studio para Mac (ou Visual Studio) pode indicar que as atualizações estão disponíveis para o NuGet packge do Xamarin. Forms *e todas as suas dependências*. No Xamarin Studio, o nó **pacotes** da solução pode parecer com o seguinte (os números de versão podem ser diferentes):

![](images/updates-available.png "Pasta de pacotes do projeto Android")

Esse erro pode ocorrer se você tentar atualizar _todos_ os pacotes.

Isso ocorre porque, com os projetos do Android definidos como uma versão de destino/compilação do Android 6,0 (API 23) ou inferior, o Xamarin. Forms tem uma dependência rígida em versões *específicas* dos pacotes de suporte do Android. Embora as versões atualizadas desses pacotes possam estar disponíveis, o Xamarin. Forms não é necessariamente compatível com eles.

Nesse caso, você deve atualizar _apenas_ o pacote **Xamarin. Forms** , pois isso garantirá que as dependências permaneçam em versões compatíveis. Outros pacotes que você adicionou ao seu projeto também podem ser atualizados individualmente, desde que não façam com que os pacotes de suporte do Android sejam atualizados.

> [!NOTE]
> Se você estiver usando o Xamarin. Forms 2.3.4 ou superior **e** a versão de destino/compilação do seu projeto do Android estiver definida como Android 7,0 (API 24) ou superior, as dependências rígidas mencionadas acima não serão mais aplicáveis e você poderá atualizar os pacotes de suporte independentemente do o pacote Xamarin. Forms.

### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Soluciona Remover todos os pacotes e adicionar novamente Xamarin. Forms

Se os pacotes **Xamarin. Android. support** tiverem sido atualizados para versões incompatíveis, a correção mais simples será:

1. Exclua manualmente todos os pacotes NuGet no projeto do Android e, em seguida,
2. Adicione novamente o pacote **Xamarin. Forms** .

Isso fará o download automático das versões *corretas* dos outros pacotes.

Para ver um vídeo desse processo, consulte esta [postagem de fóruns](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
