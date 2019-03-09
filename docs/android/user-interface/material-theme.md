---
title: Tema de material
description: Como o tema do seu aplicativo xamarin. Android com o tema de Material
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: ff94211086956e36da377445d90359789b62fc60
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668316"
---
# <a name="material-theme"></a>Tema de material

*Tema de material* é um estilo de interface do usuário que determina a aparência de modos de exibição e atividades a partir do Android 5.0 (Lollipop). Tema de material é incorporado no Android 5.0, portanto, ele é usado pelo sistema da interface do usuário, bem como por aplicativos. Tema de material não é um "tema" no sentido de uma opção de aparência de todo o sistema que um usuário pode escolher dinamicamente de um menu de configurações. Em vez disso, o tema de Material pode ser pensado como um conjunto de relacionadas estilos de base internos que você pode usar para personalizar a aparência do seu aplicativo.

O Android fornece três tipos de Material tema:

-  `Theme.Material` &ndash; Versão escuro do tema de Material; Esse é o tipo de padrão no Android 5.0.

-  `Theme.Material.Light` &ndash; Versão leve do tema de Material.

-  `Theme.Material.Light.DarkActionBar` &ndash; Versão leve do tema de Material, mas com uma barra de ação escuro.

Exemplos desses tipos de Material de tema são exibidos aqui:

[![Capturas de tela de exemplo de como o tema escuro, o tema claro e o tema escuro barra de ação](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Você pode derivar de tema de Material para criar seu próprio tema, substituindo alguns ou todos os atributos de cor. Por exemplo, você pode criar um tema que deriva de `Theme.Material.Light`, mas substitui a cor da barra de aplicativo para coincidir com a cor de sua marca. Você também pode definir o estilo exibições individuais; Por exemplo, você pode criar um estilo para [widgets CardView](~/android/user-interface/controls/card-view.md) que mais tem cantos arredondados e usa uma cor de plano de fundo mais escura.

Você pode usar um tema único para um aplicativo inteiro, ou você pode usar diferentes temas para diferentes telas (atividades) em um aplicativo. As capturas de tela acima, por exemplo, um único aplicativo usa um tema diferente para cada atividade para demonstrar os esquemas de cores interna. Botões de opção Alternar o aplicativo para diferentes atividades e, como resultado, exibem diferentes temas.

Como tema de Material é suportado somente no Android 5.0 e posterior, você não pode usá-la (ou um tema personalizado derivado de tema de Material) ao tema seu aplicativo para execução em versões anteriores do Android. No entanto, você pode configurar seu aplicativo para usar o tema de Material em dispositivos Android 5.0 e normalmente o fallback para um tema anterior quando ele é executado em versões mais antigas do Android (consulte a [compatibilidade](#compatibility) seção deste artigo para obter detalhes).


## <a name="requirements"></a>Requisitos

A seguir é necessário para usar os novos recursos de tema de Material Android 5.0 em aplicativos baseados em Xamarin:

-  **Xamarin. Android** &ndash; xamarin. Android 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac. 

-  **SDK do Android** &ndash; Android 5.0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 pode ser usado se você é especificamente direcionando API nível 23 e versões anterior. JDK 1.8 está disponível no [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Para saber como configurar um projeto de aplicativo do Android 5.0, consulte [configuração de backup de um Android 5.0 projeto](~/android/platform/lollipop.md).


## <a name="using-the-built-in-themes"></a>Usando os temas internos

A maneira mais fácil de usar tema de Material é configurar seu aplicativo para usar um tema internos sem personalização. Se você não quiser configurar explicitamente um tema, seu aplicativo usará como padrão `Theme.Material` (o tema escuro). Se seu aplicativo tem apenas uma atividade, você pode configurar um tema no nível do aplicativo. Se seu aplicativo tiver várias atividades, você pode configurar um tema no nível do aplicativo para que ele usa o mesmo tema em todas as atividades, ou você pode atribuir diferentes temas para atividades diferentes. As seções a seguir explicam como configurar os temas no nível do aplicativo e no nível de atividade.


### <a name="theming-an-application"></a>Um aplicativo de temas

Para configurar um aplicativo inteiro para usar um tipo de tema de Material, defina as `android:theme` atributo do nó no aplicativo **androidmanifest. XML** para um dos seguintes:

-  `@android:style/Theme.Material` &ndash; Tema escuro.

-  `@android:style/Theme.Material.Light` &ndash; Tema claro.

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; Tema claro com barra de ação escuro.

O exemplo a seguir configura o aplicativo *MyApp* para usar o tema claro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Como alternativa, você pode definir o aplicativo `Theme` de atributo em **AssemblyInfo.cs** (ou **Properties.cs**). Por exemplo:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Quando o tema do aplicativo é definido como `@android:style/Theme.Material.Light`, todas as atividades na *MyApp* será exibida com `Theme.Material.Light`.


### <a name="theming-an-activity"></a>Uma atividade de temas

Tema uma atividade, você adiciona uma `Theme` definir como o `[Activity]` atributo acima de sua declaração de atividade e atribuir `Theme` para o tipo de tema de Material que você deseja usar. Os temas de exemplo a seguir uma atividade com `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Outras atividades neste aplicativo usará o padrão `Theme.Material` esquema de cores escuras (ou, se configurado, a configuração de tema do aplicativo).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Usando temas personalizados

Você pode aprimorar sua marca, criando um tema personalizado que define o estilo de seu aplicativo com sua marca&rsquo;cores s. Para criar um tema personalizado, defina um novo estilo que é derivada de uma versão interna do tema de Material, sobrescrevendo os atributos de cor que você deseja alterar. Por exemplo, você pode definir um tema personalizado que deriva de `Theme.Material.Light.DarkActionBar` e altera a cor de plano de fundo da tela para bege em vez de em branco.

Tema de material expõe os seguintes atributos de layout para personalização:

-  `colorPrimary` &ndash; A cor da barra de aplicativos.

-  `colorPrimaryDark` &ndash; A cor da barra de status e barras de aplicativos contextuais; Isso normalmente é uma versão escura do `colorPrimary`.

-  `colorAccent` &ndash; A cor dos controles de interface do usuário, como caixas de seleção, botões de opção e caixas de texto de edição.

-  `windowBackground` &ndash; A cor de fundo da tela.

-  `textColorPrimary` &ndash; A cor do texto de interface do usuário na barra de aplicativos.

-  `statusBarColor` &ndash; A cor da barra de status.

-  `navigationBarColor` &ndash; A cor da barra de navegação.

Essas áreas da tela são rotuladas no diagrama a seguir:

[![Diagrama de atributos e suas áreas de tela associada](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

Por padrão, `statusBarColor` é definido como o valor de `colorPrimaryDark`. Você pode definir `statusBarColor` para uma cor sólida, ou você pode defini-lo como `@android:color/transparent` para tornar transparente a barra de status. A barra de navegação também pode ser feita transparente, definindo `navigationBarColor` para `@android:color/transparent`.


### <a name="creating-a-custom-app-theme"></a>Criando um tema do aplicativo personalizado

Você pode criar um tema do aplicativo personalizado criando e modificando os arquivos a **recursos** pasta do projeto do aplicativo. Para definir o estilo de seu aplicativo com um tema personalizado, use as seguintes etapas:

-   Criar uma **Colors** arquivo no **recursos/valores** &mdash; usar esse arquivo para definir suas cores de tema personalizado. Por exemplo, você pode colar o código a seguir em **Colors** para ajudá-lo a começar:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Modificar esse arquivo de exemplo para definir os nomes e os códigos de cor para os recursos de cor que você usará em seu tema personalizado.

-   Criar uma **valores/recursos-v21** pasta. Nessa pasta, crie uma **Styles** arquivo:

    [![Local do Styles na pasta recursos/valores-21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Observe que **valores/recursos-v21** é específico para o Android 5.0 &ndash; versões mais antigas do Android não lerá arquivos nesta pasta.

-   Adicionar um `resources` nó **Styles** e definir um `style` nó com o nome do seu tema personalizado. Por exemplo, aqui está uma **Styles** arquivo que define *MyCustomTheme* (derivado interno `Theme.Material.Light` estilo de tema):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   Neste ponto, um aplicativo que usa *MyCustomTheme* exibirá o estoque `Theme.Material.Light` tema sem personalizações:

    [![Aparência do tema personalizado antes de personalizações](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   Adicionar personalizações de cor **Styles** definindo as cores dos atributos de layout que você deseja alterar. Por exemplo, para alterar a cor da barra de aplicativo para `my_blue` e alterar a cor dos controles de interface do usuário para `my_purple`, adicione substituições de cor para **Styles** que se referem a recursos de cor configurados no **Colors**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Com essas alterações, um aplicativo que usa *MyCustomTheme* exibirá uma cor da barra de aplicativo no `my_blue` e os controles de interface do usuário do `my_purple`, mas usar o `Theme.Material.Light` todos os outros lugares do esquema de cores:

[![Aparência do tema personalizado depois de personalizações](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

Neste exemplo, *MyCustomTheme* empresta as cores da `Theme.Material.Light` da tela de fundo cor, barra de status e as cores de texto, mas ele altera a cor da barra de aplicativo para `my_blue` e define a cor do botão de opção `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Criar um estilo de exibição personalizada

Android 5.0 também torna possível para você definir o estilo de uma exibição individual. Depois de criar **Colors** e **Styles** (conforme descrito na seção anterior), você pode adicionar um estilo de exibição para **Styles**.
Para definir o estilo de uma exibição individual, use as seguintes etapas:

-   Edite **Resources/values-v21/styles.xml** e adicione um `style` nó com o nome do seu estilo de exibição personalizado. Definir os atributos de cor personalizada para o modo de exibição dentro desta `style` nó. Por exemplo, para criar um personalizado [widgets CardView](~/android/user-interface/controls/card-view.md) estilo que tem mais cantos arredondados e usos `my_blue` como a cor de plano de fundo do cartão, adicionar um `style` nó **Styles** (dentro do `resources`nó) e configurar o raio de canto e de cor em segundo plano:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   Em seu layout, defina o `style` atributo dessa exibição corresponder ao nome de estilo personalizado que você escolheu na etapa anterior. Por exemplo:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Captura de tela a seguir fornece um exemplo de como o padrão `CardView` (mostrado à esquerda) em comparação com um `CardView` que foi estilizada com personalizado `CardView.MyBlue` tema (mostrado à direita):

[![Exemplos de padrão widgets CardView e widgets CardView de personalizado](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

Neste exemplo, personalizado `CardView` é exibida com a cor do plano de fundo `my_blue` e um raio de canto 18dp.


## <a name="compatibility"></a>Compatibilidade

Para definir o estilo de seu aplicativo para que ele usa o tema de Material no Android 5.0, mas é revertida automaticamente para um estilo para baixo compatível em versões mais antigas do Android, use as seguintes etapas:

-   Definir um tema personalizado em **Resources/values-v21/styles.xml** que deriva de um estilo de tema de Material. Por exemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Definir um tema personalizado em **Resources/values/styles.xml** que deriva de um tema mais antigo, mas usa o mesmo nome do tema como acima. Por exemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Na **androidmanifest. XML**, configure seu aplicativo com o nome do tema personalizado. 
    Por exemplo:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   Como alternativa, você pode definir o estilo de uma atividade específica usando o tema personalizado:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Se o tema usa cores definidas em um **Colors** do arquivo, certifique-se de colocar esse arquivo na **recursos/valores** (em vez de **valores/recursos-v21**) para que ambas as versões do o tema personalizado pode acessar suas definições de cor.

Quando seu aplicativo é executado em um dispositivo Android 5.0, ele usará a definição de tema especificada na **Resources/values-v21/styles.xml**. Quando esse aplicativo é executado em dispositivos Android mais antigos, ele fará automaticamente o fallback para a definição de tema especificada na **Resources/values/styles.xml**.

Para obter mais informações sobre a compatibilidade de tema com versões mais antigas do Android, consulte [recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Resumo

Este artigo apresentou o novo estilo de interface de usuário de tema de Material incluído no Android 5.0 (Lollipop). Ele descreveu os três sabores internos do tema de Material que você pode usar para definir o estilo de seu aplicativo, explicou como criar um tema personalizado para seu aplicativo de identidade visual e fornecido um exemplo de como tema uma exibição individual. Por fim, este artigo explicou como usar o tema de Material em seu aplicativo enquanto mantém a compatibilidade com versões mais antigas do Android.



## <a name="related-links"></a>Links relacionados

- [ThemeSwitcher (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Introdução ao Lollipop](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Recursos alternativos](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android Lollipop](https://developer.android.com/about/versions/lollipop)
- [Pizza Android Developer](https://developer.android.com/about/versions/pie/)
- [Design de material](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Princípios de Design de material](https://material.io/design/)
- [Manter a compatibilidade](https://developer.android.com/training/backward-compatible-ui/)
