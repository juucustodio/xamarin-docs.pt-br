---
title: SoluçãoXamarin.Forms
description: Condições de erro comuns e como resolvê-las
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 857c729ac7642003f40e34afa024c6cfcbaabb39
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996559"
---
# <a name="troubleshooting-no-locxamarinforms"></a>SoluçãoXamarin.Forms

_Condições de erro comuns e como resolvê-las_

## <a name="error-unable-to-find-a-version-of-no-locxamarinforms-compatible-with"></a>Erro: "não foi possível encontrar uma versão de Xamarin.Forms compatível com..."

Os erros a seguir podem aparecer na janela do **console do pacote** ao atualizar todos os pacotes NuGet em uma Xamarin.Forms solução ou em um Xamarin.Forms projeto de aplicativo do Android:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>O que causa esse erro?

Visual Studio para Mac (ou Visual Studio) podem indicar que as atualizações estão disponíveis para o Xamarin.Forms NuGet packge *e todas as suas dependências*. No Xamarin Studio, o nó **pacotes** da solução pode parecer com o seguinte (os números de versão podem ser diferentes):

![Pasta de pacotes do projeto Android](images/updates-available.png)

Esse erro pode ocorrer se você tentar atualizar _todos_ os pacotes.

Isso ocorre porque com os projetos do Android definidos como uma versão de destino/compilação do Android 6,0 (API 23) ou abaixo, Xamarin.Forms o tem uma dependência rígida em versões *específicas* dos pacotes de suporte do Android. Embora as versões atualizadas desses pacotes possam estar disponíveis, o Xamarin.Forms não é necessariamente compatível com eles.

Nesse caso, você deve atualizar _apenas_ o **Xamarin.Forms** pacote, pois isso garantirá que as dependências permaneçam em versões compatíveis. Outros pacotes que você adicionou ao seu projeto também podem ser atualizados individualmente, desde que não façam com que os pacotes de suporte do Android sejam atualizados.

> [!NOTE]
> Se você estiver usando Xamarin.Forms o 2.3.4 ou superior **e** a versão de destino/compilação do seu projeto Android estiver definida como Android 7,0 (API 24) ou superior, as dependências rígidas mencionadas acima não serão mais aplicáveis e você poderá atualizar os pacotes de suporte independentemente do Xamarin.Forms pacote.

### <a name="fix-remove-all-packages-and-re-add-no-locxamarinforms"></a>Correção: remover todos os pacotes e adicionar novamenteXamarin.Forms

Se os pacotes **Xamarin. Android. support** tiverem sido atualizados para versões incompatíveis, a correção mais simples será:

1. Exclua manualmente todos os pacotes NuGet no projeto do Android e, em seguida,
2. Adicione novamente o **Xamarin.Forms** pacote.

Isso fará o download automático das versões *corretas* dos outros pacotes.

Para ver um vídeo desse processo, consulte esta [postagem de fóruns](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
