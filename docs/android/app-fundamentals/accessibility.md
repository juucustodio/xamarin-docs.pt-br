---
title: Acessibilidade no Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: f32f32e56ff4869c003b142f9ad67b0e54cfa353
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197684"
---
# <a name="accessibility-on-android"></a>Acessibilidade no Android

Esta página descreve como usar as APIs de acessibilidade do Android para compilar aplicativos de acordo com a [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte as páginas de acessibilidade do [Ios](~/ios/app-fundamentals/accessibility.md) e de acessibilidade do [os X](~/mac/app-fundamentals/accessibility.md) para outras APIs de plataforma.


## <a name="describing-ui-elements"></a>Descrevendo elementos da interface do usuário

O Android fornece `ContentDescription` uma propriedade usada por APIs de leitura de tela para fornecer uma descrição acessível da finalidade do controle.

A descrição do conteúdo pode ser definida em C# ou no arquivo de layout AXML.

**C#**

A descrição pode ser definida no código para qualquer cadeia de caracteres (ou um recurso de cadeia de caracteres):

```csharp
saveButton.ContentDescription = "Save data";
```

**Layout de AXML**

Em layouts XML, use `android:contentDescription` o atributo:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Usar dica para TextView

Para `EditText` controles `TextView` e para entrada de dados, use `Hint` a propriedade para fornecer uma descrição de qual entrada é esperada ( `ContentDescription`em vez de).
Quando algum texto tiver sido inserido, o texto em si será "lido" em vez da dica.

**C#**

Defina a `Hint` Propriedade no código:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Layout de AXML**

Em arquivos de layout XML, `android:hint` use o atributo:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor vincula campos de entrada com rótulos

Para associar um rótulo a um controle de entrada de dados, `LabelFor` use a propriedade para

**C#**

No C#, defina a `LabelFor` propriedade para a ID de recurso do controle que este conteúdo descreve (normalmente essa propriedade é definida em um rótulo e faz referência a algum outro controle de entrada):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Layout de AXML**

Em XML de layout, `android:labelFor` use a propriedade para fazer referência ao identificador de outro controle:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Anúncio para acessibilidade

Use o `AnnounceForAccessibility` método em qualquer controle de exibição para comunicar um evento ou uma alteração de status para os usuários quando a acessibilidade estiver habilitada. Esse método não é necessário para a maioria das operações em que a narração interna fornece comentários suficientes, mas deve ser usada onde informações adicionais seriam úteis para o usuário.

O código a seguir mostra um exemplo simples `AnnounceForAccessibility`chamando:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Alterando as configurações de foco

A navegação acessível depende de controles com foco para ajudar o usuário a entender quais operações estão disponíveis. O Android fornece `Focusable` uma propriedade que pode marcar controles como especificamente capazes de receber o foco durante a navegação.

**C#**

Para impedir que um controle fique em foco C#, defina a `Focusable` Propriedade como `false`:

```csharp
label.Focusable = false;
```

**Layout de AXML**

Em arquivos XML de layout, `android:focusable` defina o atributo:

```xml
<android:focusable="false" />
```

Você também pode controlar a ordem de foco `nextFocusDown`com `nextFocusLeft`os `nextFocusRight` `nextFocusUp` atributos,,, normalmente definidos no layout AXML. Use esses atributos para garantir que o usuário possa navegar facilmente pelos controles na tela.


## <a name="accessibility-and-localization"></a>Acessibilidade e localização

Nos exemplos acima, a dica e a descrição do conteúdo são definidas diretamente para o valor de exibição. É preferível usar valores em um arquivo **Strings. xml** , como este:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

O uso de texto de um arquivo de cadeia de C# caracteres é mostrado abaixo nos arquivos de layout do e AXML:

**C#**

Em vez de usar literais de cadeia de caracteres no código, pesquise valores traduzidos `Resources.GetText`de arquivos de cadeias de caracteres com:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

Em atributos de acessibilidade XML de `hint` layout `contentDescription` como e podem ser definidos como um identificador de cadeia de caracteres:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

O benefício de armazenar texto em um arquivo separado é que várias traduções de idioma do arquivo podem ser fornecidas em seu aplicativo. Consulte o [Guia de localização do Android](~/android/app-fundamentals/localization.md) para saber como adicionar arquivos de cadeia de caracteres localizados a um projeto de aplicativo.


## <a name="testing-accessibility"></a>Testando a acessibilidade

Siga [estas etapas](https://developer.android.com/training/accessibility/testing.html#how-to) para habilitar o Talkback e explorar por toque para testar a acessibilidade em dispositivos Android.

Talvez seja necessário instalar o [Talkback](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) de Google Play se ele não aparecer em **Configurações > acessibilidade**.


## <a name="related-links"></a>Links relacionados

- [Acessibilidade multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [APIs de acessibilidade do Android](https://developer.android.com/guide/topics/ui/accessibility/index.html)
