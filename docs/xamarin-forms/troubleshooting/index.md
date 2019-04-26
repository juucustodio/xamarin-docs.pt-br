---
title: Solução de problemas
description: Condições de erro comuns e como resolvê-los
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fbe4fb6fce52636b59a9637ee0150c4c19fcc9da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850428"
---
# <a name="troubleshooting"></a>Solução de problemas

_Condições de erro comuns e como resolvê-los_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Erro: "Não foi possível encontrar compatível com uma versão do xamarin. Forms..."

Os erros a seguir podem aparecer na **Console de pacotes** janela ao atualizar todos os pacotes Nuget em uma solução do xamarin. Forms ou em um projeto de aplicativo do Android do xamarin. Forms:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>O que causa esse erro?

O Visual Studio para Mac (ou o Visual Studio) pode indicar que as atualizações estão disponíveis para o Nuget xamarin. Forms packge *e todas as suas dependências*. Do Xamarin Studio, a solução **pacotes** nó teria esta aparência (os números de versão podem ser diferentes):

![](images/updates-available.png "Pasta de pacotes do projeto do Android")

Esse erro pode ocorrer se você tentar atualizar _todos os_ os pacotes.

Isso ocorre porque com Android projetos definidos para uma versão de destino/compilação do Android 6.0 (API 23) ou abaixo, o xamarin. Forms tem uma dependência forte *específico* versões do Android dão suporte a pacotes. Embora as versões atualizadas desses pacotes podem estar disponíveis, o xamarin. Forms não é necessariamente compatível com eles.

Nesse caso, você deve atualizar _só_ as **xamarin. Forms** porque isso garantirá que as dependências permanecem em versões compatíveis do pacote. Outros pacotes que você adicionou ao seu projeto também podem ser atualizados individualmente, desde que eles fazem com que os pacotes de suporte do Android atualizar.


> [!NOTE]
> Se você estiver usando o xamarin. Forms 2.3.4 ou superior **e** versão de destino/compilação do seu projeto Android é definido para o Android 7.0 (API 24) ou superior, em seguida, as dependências de disco rígidas mencionadas acima não se aplicam, e você pode atualizar o suporte pacotes independentemente do pacote xamarin. Forms.


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Correção: Remova todos os pacotes e adicione novamente o xamarin. Forms

Se o **Support** pacotes foram atualizados para versões incompatíveis, a correção mais simples é:

1. Exclua manualmente todos os pacotes do Nuget no projeto do Android, em seguida
2. Adicione novamente o **xamarin. Forms** pacote.

Isso baixará automaticamente a *correto* versões dos outros pacotes.

Para ver um vídeo desse processo, consulte este [fóruns post](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
