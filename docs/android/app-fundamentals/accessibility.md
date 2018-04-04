---
title: Acessibilidade no Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: 2a49d15651b8c6ab7417a69d934af5d20bfc13d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility-on-android"></a>Acessibilidade no Android

Esta página descreve como usar as APIs de acessibilidade Android para compilar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte o [iOS acessibilidade](~/ios/app-fundamentals/accessibility.md) e [acessibilidade OS X](~/mac/app-fundamentals/accessibility.md) páginas para outras APIs da plataforma.


## <a name="describing-ui-elements"></a>Descrever os elementos de interface do usuário

Android fornece um `ContentDescription` propriedade que é usada por APIs de leitura de tela para fornecer uma descrição acessível da finalidade do controle.

A descrição do conteúdo pode ser definida no c# ou no arquivo de layout de AXML.

**C#**

A descrição pode ser definida no código para qualquer cadeia de caracteres (ou um recurso de cadeia de caracteres):

```csharp
saveButton.ContentDescription = "Save data";
```

**Layout AXML**

No XML layouts de usam o `android:contentDescription` atributo:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Use a dica para TextView

Para `EditText` e `TextView` controles de entrada de dados, use o `Hint` propriedade para fornecer uma descrição do que a entrada é esperada (em vez de `ContentDescription`).
Quando algum texto foi digitado, o texto será "lida" em vez da dica.

**C#**

Definir o `Hint` propriedade no código:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Layout AXML**

No XML de arquivos de layout usam o `android:hint` atributo:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor links de entrada campos com rótulos

Para associar um rótulo um controle de entrada de dados, use o `LabelFor` propriedade

**C#**

No c#, defina o `LabelFor` propriedade para a ID de recurso do controle nesse conteúdo descreve (normalmente essa propriedade é definida em um rótulo e faz referência a um outro controle de entrada):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Layout AXML**

No layout de uso do XML de `android:labelFor` propriedade para referenciar o identificador do outro controle:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Anunciar para acessibilidade

Use o `AnnounceForAccessibility` método em qualquer Exibir controle para comunicar-se uma alteração de status ou o evento aos usuários quando acessibilidade está habilitada. Esse método não é necessário para a maioria das operações em que a narração interna fornece comentários suficientes, mas deve ser usada onde informações adicionais podem ser úteis para o usuário.

O código a seguir mostra uma chamada de exemplo simples `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Alterar as configurações de foco

Navegação acessível se baseia em controles com foco para ajudar o usuário a entender as operações que estão disponíveis. Android fornece um `Focusable` propriedade que pode marcar controles como especificamente capaz de receber foco durante a navegação.

**C#**

Para impedir que um controle ganhando o foco com c#, defina o `Focusable` propriedade `false`:

```csharp
label.Focusable = false;
```

**Layout AXML**

No layout de conjunto de arquivos XML de `android:focusable` atributo:

```xml
<android:focusable="false" />
```

Você também pode controlar a ordem de foco com o `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` atributos, normalmente definidos no layout AXML. Use esses atributos para garantir que o usuário possa navegar facilmente por meio dos controles na tela.


## <a name="accessibility-and-localization"></a>Acessibilidade e localização

Nos exemplos acima são a descrição de dica e conteúdo definido diretamente para o valor de exibição. É preferível usar valores em uma **Strings.xml** arquivo, como esta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

Usando o texto de um arquivo de cadeias de caracteres é mostrado abaixo em c# e arquivos de layout AXML:

**C#**

Em vez de usar literais de cadeia de caracteres no código, pesquisar valores traduzidos de arquivos de cadeias de caracteres com `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

No layout de XML como atributos de acessibilidade `hint` e `contentDescription` pode ser definido como um identificador de cadeia de caracteres:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

A vantagem de armazenar texto em um arquivo separado é que várias traduções de idioma do arquivo podem ser fornecidas em seu aplicativo. Consulte o [guia localização Android](~/android/app-fundamentals/localization.md) para saber como adicionar arquivos de cadeia de caracteres localizada para um projeto de aplicativo.


## <a name="testing-accessibility"></a>Teste de acessibilidade

Execute [essas etapas](http://developer.android.com/training/accessibility/testing.html#how-to) para habilitar o TalkBack e explorar por toque testar a acessibilidade em dispositivos Android.

Você talvez precise instalar [o TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) no Google Play se ele não aparecer na **Configurações > acessibilidade**.


## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [APIs de acessibilidade do Android](http://developer.android.com/guide/topics/ui/accessibility/index.html)
