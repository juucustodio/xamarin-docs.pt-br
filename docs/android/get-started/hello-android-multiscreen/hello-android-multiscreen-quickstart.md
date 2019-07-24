---
title: 'Multitela Hello, Android: Guia de Início Rápido'
description: Este guia de duas partes expande o aplicativo Phoneword para manipular uma segunda tela. Ao longo do caminho, Blocos de Construção do Aplicativo Android básicos são apresentados com um aprofundamento maior de uma arquitetura Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: ED99584A-BA3B-429A-AEE5-CF3CB0116762
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: dcc60243957bb1a7720c08dc7a5ce51caf585ce8
ms.sourcegitcommit: 56b2f5cda7c37874618736d6129f19a8976826f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2019
ms.locfileid: "54418654"
---
# <a name="hello-android-multiscreen-quickstart"></a>Multitela Hello, Android: Guia de Início Rápido

_Este guia de duas partes expande o aplicativo Phoneword para manipular uma segunda tela. Ao longo do caminho, Blocos de Construção do Aplicativo Android básicos são apresentados com um aprofundamento maior de uma arquitetura Android._

Na seção de passo a passo deste guia, você adicionará uma segunda tela ao aplicativo [Phoneword](https://developer.xamarin.com/samples/monodroid/Phoneword/) para controlar o histórico de números convertidos usando o aplicativo. O [aplicativo final](https://developer.xamarin.com/samples/monodroid/PhonewordMultiscreen/) terá uma segunda tela exibindo os números que foram “convertidos”, conforme ilustrado pela captura de tela à direita:

[![Capturas de tela de aplicativo de exemplo](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

O [Aprofundamento](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md) de acompanhamento analisa o que foi criado e discute a arquitetura, navegação e outros novos conceitos do Android encontrados ao longo do caminho.

## <a name="requirements"></a>Requisitos

Como este guia continua do ponto que o [Hello, Android](~/android/get-started/hello-android/index.md) parou, ele exige a conclusão do [Início Rápido do Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Caso queira pular diretamente para o passo a passo abaixo, é possível baixar a versão completa do [Phoneword](https://developer.xamarin.com/samples/monodroid/Phoneword/) (do Início Rápido do Hello, Android) e usá-lo para iniciar o passo a passo.

## <a name="walkthrough"></a>Passo a passo

Neste passo a passo, você adicionará uma tela de **Histórico de Conversão** ao aplicativo **Phoneword**.

::: zone pivot="windows"

Comece abrindo o aplicativo **Phoneword** no Visual Studio e edite o arquivo **Main.axml** do **Gerenciador de Soluções**.

### <a name="updating-the-layout"></a>Atualizando o layout

Na **Caixa de Ferramentas**, arraste um **Botão** para a superfície de design e coloque-o abaixo do TextView **TranslatedPhoneWord**. No painel **Propriedades**, altere a **ID** do botão para `@+id/TranslationHistoryButton`

[![Arrastar um novo botão](hello-android-multiscreen-quickstart-images/vs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/vs/02-new-button.png#lightbox)

Ajuste a propriedade **Texto** do botão para `@string/translationHistory`. O Android Designer interpretará isso literalmente, porém vamos fazer algumas alterações para que o texto do botão seja exibido corretamente:

[![Definir o texto de botão do histórico de conversão](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string-sml.png)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string.png#lightbox)

Expanda o nó de **valores** na pasta **Recursos** no **Gerenciador de Soluções** e clique duas vezes no arquivo de recursos de cadeia de caracteres, **Strings.xml**:

[![Abrir Strings.xml](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file.png#lightbox)

Adicione o `translationHistory` nome e valor da cadeia de caracteres ao arquivo **Strings.xml** e salve-o:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

O texto do botão **Histórico de Conversão** deve ser atualizado para refletir o novo valor de cadeia de caracteres:

[![O botão reflete o novo valor de cadeia de caracteres](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png)](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png#lightbox)

Com o botão **Histórico de Conversão** selecionado na superfície de design, localize a configuração `enabled` no painel **Propriedades** e defina seu valor como `false` para desabilitar o botão. Isso fará com que o botão fique mais escuro na superfície de design:

[![Desabilitar o botão de histórico de conversão](hello-android-multiscreen-quickstart-images/vs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Criando a segunda atividade

Crie uma segunda Atividade para ligar a segunda tela. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Phoneword** e selecione **Adicionar > Novo Item…** :

[![Adicionar um novo arquivo](hello-android-multiscreen-quickstart-images/vs/07-add-new-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/07-add-new-file.png#lightbox)

Na caixa de diálogo **Adicionar Novo Item**, escolha **Visual C# > Atividade** e nomeie o arquivo de Atividade **TranslationHistoryActivity.cs**.

Substitua o código do modelo no **TranslationHistoryActivity.cs** pelo seguinte:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

Nesta aula, você está criando um `ListActivity` e preenchendo-o programaticamente, portanto não é necessário criar um novo arquivo de layout para essa Atividade. Isso será discutido mais detalhadamente no [Aprofundamento na Multitela do Hello, Android](~/android/get-started/hello-android/hello-android-deepdive.md).

### <a name="adding-a-list"></a>Adicionando uma lista

Este aplicativo coleta números de telefone (que o usuário tenha convertido na primeira tela) e os transfere para a segunda tela. Os números de telefone são armazenados como uma lista de cadeias de caracteres. Para permitir listas (e intenções, que serão usadas mais tarde), adicione as seguintes diretivas `using` na parte superior de **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Em seguida, crie uma lista vazia que pode ser preenchida com números de telefone.
A classe `MainActivity` terá a seguinte aparência:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

Na classe `MainActivity`, adicione o código a seguir para registrar o botão **Histórico de Conversão** (coloque esta linha após a declaração `translateButton`):

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Adicione o seguinte código ao final do método `OnCreate` para conectar o botão **Histórico de Conversão**:

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Atualize o botão **Converter** para adicionar o número de telefone à lista de `phoneNumbers`. O manipulador `Click` para o `translateButton` deve se parecer com o código a seguir:

```csharp
// Add code to translate number
string translatedNumber = string.Empty;
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

Salve e compile o aplicativo para garantir que não haja erros.

### <a name="running-the-app"></a>Executando o aplicativo

Implante o aplicativo em um dispositivo ou emulador. As capturas de tela a seguir ilustram o aplicativo **Phoneword** em execução:

[![Capturas de tela de exemplo](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end
::: zone pivot="macos"

Comece abrindo o projeto **Phoneword** no Visual Studio para Mac e edite o arquivo **Main.axml** no **Painel de Soluções**.

### <a name="updating-the-layout"></a>Atualizando o layout

Na **Caixa de Ferramentas**, arraste um **Botão** para a superfície de design e coloque-o abaixo do TextView **TranslatedPhoneWord**. No painel **Propriedades**, altere a **ID** do botão para `@+id/TranslationHistoryButton`:

[![Arrastar um novo botão](hello-android-multiscreen-quickstart-images/xs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/xs/02-new-button.png#lightbox)

Ajuste a propriedade **Texto** do botão para `@string/translationHistory`. O Android Designer interpretará isso literalmente, porém vamos fazer algumas alterações para que o texto do botão seja exibido corretamente:

[![Definir o texto de botão do histórico de conversão](hello-android-multiscreen-quickstart-images/xs/03-call-history-string-sml.png)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string.png#lightbox)

Expanda o nó de **valores** na pasta **Recursos** do **Painel de Soluções** e clique duas vezes no arquivo de recursos de cadeia de caracteres, **Strings.xml**:

[![Abrir cadeias de caracteres](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file.png#lightbox)

Adicione o `translationHistory` nome e valor da cadeia de caracteres ao arquivo **Strings.xml** e salve-o:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

O texto do botão **Histórico de Conversão** deve ser atualizado para refletir o novo valor de cadeia de caracteres:

[![O botão reflete o novo valor de cadeia de caracteres](hello-android-multiscreen-quickstart-images/xs/05-new-string-value-sml.png)](hello-android-multiscreen-quickstart-images/xs/05-new-string-value.png#lightbox)


Com o botão **Histórico de Conversão** selecionado na superfície de design, abra a guia **Comportamento** no **Painel de Propriedades** e clique duas vezes na caixa de seleção **Habilitado** para desabilitar o botão. Isso fará com que o botão fique mais escuro na superfície de design:

[![Desabilitar o botão de histórico de conversão](hello-android-multiscreen-quickstart-images/xs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Criando a segunda atividade

Crie uma segunda Atividade para ligar a segunda tela. No **Painel de Soluções**, clique no ícone de engrenagem cinza ao lado do projeto **Phoneword** e escolha **Adicionar > Novo Arquivo...** :

Na caixa de diálogo **Novo Arquivo**, escolha **Android > Atividade**, nomeie a Atividade `TranslationHistoryActivity` e clique em **Adicionar**.

Substitua o código do modelo no `TranslationHistoryActivity` pelo seguinte:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

Nesta aula, um `ListActivity` é criado e preenchido programaticamente, portanto você não tem que criar um novo arquivo de layout para essa Atividade. Isso será explicado mais detalhadamente no [Aprofundamento na Multitela do Hello, Android](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

### <a name="adding-a-list"></a>Adicionando uma lista

Este aplicativo coleta números de telefone (que o usuário tenha convertido na primeira tela) e os transfere para a segunda tela. Os números de telefone são armazenados como uma lista de cadeias de caracteres. Para permitir listas (e intenções, que serão usadas mais tarde), adicione as seguintes diretivas `using` na parte superior de **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Em seguida, crie uma lista vazia que pode ser preenchida com números de telefone. A classe `MainActivity` terá a seguinte aparência:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

Na classe `MainActivity`, adicione o código a seguir para registrar o botão **Histórico de Conversão** (coloque esta linha após a declaração `TranslationHistoryButton`):

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Adicione o seguinte código ao final do método `OnCreate` para conectar o botão **Histórico de Conversão**:

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Atualize o botão **Converter** para adicionar o número de telefone à lista de `phoneNumbers`. O manipulador `Click` para o `TranslateHistoryButton` deve se parecer com o código a seguir:

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

### <a name="running-the-app"></a>Executando o aplicativo

Implante o aplicativo em um dispositivo ou emulador. As capturas de tela a seguir ilustram o aplicativo **Phoneword** em execução:

[![Capturas de tela de exemplo](hello-android-multiscreen-quickstart-images/screenshot.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end

Parabéns por concluir seu primeiro aplicativo Xamarin.Android de multitela! Agora é hora de examinar as ferramentas e as habilidades que você acabou de aprender &ndash;, a seguir está o [Aprofundamento na Multitela do Hello, Android](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

## <a name="related-links"></a>Links relacionados

- [Ícones de aplicativo Xamarin e telas de inicialização (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (amostra)](https://developer.xamarin.com/samples/monodroid/Phoneword)
- [PhonewordMultiscreen (amostra)](https://developer.xamarin.com/samples/monodroid/PhonewordMultiscreen)
