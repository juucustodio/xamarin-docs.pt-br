---
title: Tema material
description: Tema como seu aplicativo xamarin com Material de tema
ms.topic: article
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: ba73e03d6bdeea64918e0232b2188bf8e3b65084
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="material-theme"></a>Tema material

<a name="overview" />

## <a name="overview"></a>Visão geral

*Tema material* é um estilo de interface do usuário que determina a aparência dos modos de exibição e Android 5.0 (pirulito) a partir de atividades. Tema material é incorporado ao Android 5.0, para que ela é usada pelo sistema da interface do usuário, bem como por aplicativos. Tema material não é um "tema" no sentido de uma opção de aparência de todo o sistema que um usuário pode escolher dinamicamente de um menu de configurações. Em vez disso, o tema de Material pode ser considerado como um conjunto de relacionadas estilos internos base que você pode usar para personalizar a aparência do seu aplicativo.

Android fornece três tipos de Material tema:

-  `Theme.Material` &ndash; Versão escuro do tema de Material; Este é o tipo padrão no Android 5.0.

-  `Theme.Material.Light` &ndash; Versão leve do Material de tema.

-  `Theme.Material.Light.DarkActionBar` &ndash; Versão leve do tema de Material, mas com uma barra de ação escuro.

Exemplos desses tipos de Material tema são exibidos aqui:

[![Capturas de tela de exemplo do tema escuro, o tema claro e o tema escuro barra de ação](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png)

Você pode derivar de Material de tema para criar seu próprio tema, substituindo alguns ou todos os atributos de cor. Por exemplo, você pode criar um tema que deriva de `Theme.Material.Light`, mas substitui a cor da barra de aplicativos de acordo com a cor de sua marca. Você também pode definir o estilo exibições individuais; Por exemplo, você pode criar um estilo para [CardView](~/android/user-interface/controls/card-view.md) que tem mais cantos arredondados e usa uma cor de plano de fundo mais escura.

Você pode usar um tema único para um aplicativo inteiro, ou você pode usar diferentes temas para telas diferentes (atividades) em um aplicativo. Nas capturas de tela acima, por exemplo, um único aplicativo usa um tema diferente para cada atividade para demonstrar os esquemas de cores interna. Botões de opção Alternar o aplicativo para atividades diferentes e, consequentemente, exibem diferentes temas.

Porque o tema de Material é suportado somente no Android 5.0 e versões posteriores, você não pode usar (ou um tema personalizado derivado de tema de Material) para o tema seu aplicativo para execução em versões anteriores do Android. No entanto, você pode configurar seu aplicativo para usar o tema de Material em dispositivos Android 5.0 e normalmente voltar a um tema anterior quando ele é executado em versões anteriores do Android (consulte o [compatibilidade](#compatibility) deste artigo para obter detalhes).

<a name="requirements" />

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para usar os novos recursos de tema de Material Android 5.0 em aplicativos baseados em Xamarin:

-  **Xamarin** &ndash; xamarin 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac. 

-  **SDK do Android** &ndash; Android 5.0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 pode ser usado se você é especificamente direcionar API nível 23 e versões anterior. 1.8 JDK está disponível em [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Para saber como configurar um projeto de aplicativo do Android 5.0, consulte [configuração de backup de um Android 5.0 projeto](~/android/platform/lollipop.md).

<a name="builtinthemes" />

## <a name="using-the-built-in-themes"></a>Usando os temas internos

A maneira mais fácil de usar o tema de Material é configurar seu aplicativo para usar um tema interno sem personalização. Se você não quiser configurar explicitamente um tema, seu aplicativo usará como padrão `Theme.Material` (tema escuro). Se seu aplicativo tiver apenas uma atividade, você pode configurar um tema no nível do aplicativo. Se seu aplicativo tiver várias atividades, você pode configurar um tema no nível do aplicativo para que ele usa o mesmo tema em todas as atividades, ou você pode atribuir diferentes temas para atividades diferentes. As seções a seguir explicam como configurar os temas no nível do aplicativo e no nível de atividade.

<a name="themeanapp" />

### <a name="theming-an-application"></a>Um aplicativo de temas

Para configurar um aplicativo inteiro para usar um tipo de tema de Material, defina o `android:theme` atributo do nó de aplicativo **AndroidManifest.xml** para um dos seguintes:

-  `@android:style/Theme.Material` &ndash; Tema escuro.

-  `@android:style/Theme.Material.Light` &ndash; Tema claro.

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; Tema claro com barra de ação escuro.

O exemplo a seguir configura o aplicativo *MyApp* para usar o tema claro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Como alternativa, você pode definir o aplicativo `Theme` atributo **AssemblyInfo.cs** (ou **Properties.cs**). Por exemplo:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Quando o tema de aplicativo é definido como `@android:style/Theme.Material.Light`, cada atividade no *MyApp* será exibida com `Theme.Material.Light`.

<a name="activitytheme" />

### <a name="theming-an-activity"></a>Uma atividade de temas

Para o tema de uma atividade, é possível adicionar um `Theme` definir como o `[Activity]` atributos acima sua declaração de atividade e atribuir `Theme` para o tipo de Material tema que você deseja usar. Os temas de exemplo a seguir uma atividade com `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Outras atividades neste aplicativo usará o padrão `Theme.Material` esquema de cores escuro (ou, se configurado, a configuração de tema de aplicativo).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Usando temas personalizados

Você pode aprimorar a marca criando um tema personalizado que seu aplicativo com a marca de estilos&rsquo;cores s. Para criar um tema personalizado, você define um novo estilo que deriva de um tipo de Material tema interno, substituindo os atributos de cor que você deseja alterar. Por exemplo, você pode definir um tema personalizado que é derivada de `Theme.Material.Light.DarkActionBar` e altera a cor de plano de fundo da tela para bege em vez de em branco.

Tema material expõe os seguintes atributos de layout para personalização:

-  `colorPrimary` &ndash; A cor da barra de aplicativos.

-  `colorPrimaryDark` &ndash; A cor da barra de status e barras de aplicativo contextuais; Isso normalmente é uma versão escura do `colorPrimary`.

-  `colorAccent` &ndash; A cor dos controles de interface do usuário, como caixas de seleção, botões de opção e caixas de texto de edição.

-  `windowBackground` &ndash; A cor do plano de fundo de tela.

-  `textColorPrimary` &ndash; A cor do texto de interface do usuário na barra de aplicativos.

-  `statusBarColor` &ndash; A cor da barra de status.

-  `navigationBarColor` &ndash; A cor da barra de navegação.

Essas áreas da tela são rotuladas no diagrama a seguir:

[ ![Diagrama de atributos e suas áreas da tela associada](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png)

Por padrão, `statusBarColor` é definido como o valor de `colorPrimaryDark`. Você pode definir `statusBarColor` para uma cor sólida, ou você pode defini-lo `@android:color/transparent` para tornar a barra de status transparente. Barra de navegação também pode ser feita transparente definindo `navigationBarColor` para `@android:color/transparent`.

<a name="customapptheme" />

### <a name="creating-a-custom-app-theme"></a>Criando um tema de aplicativo personalizado

Você pode criar um tema de aplicativo personalizado criando e modificando arquivos de **recursos** pasta do projeto do aplicativo. Para definir o estilo de seu aplicativo com um tema personalizado, use as seguintes etapas:

-   Criar um **Colors** arquivo **recursos/valores** &mdash; você usar esse arquivo para definir as cores de tema personalizado. Por exemplo, você pode colar o código a seguir em **Colors** para ajudá-lo a começar:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   Modificar este arquivo de exemplo para definir os nomes e os códigos de cor para recursos de cor que você usará o tema personalizado.

-   Criar um **valores/recursos-v21** pasta. Nessa pasta, crie um **Styles** arquivo:

    [ ![Local do Styles na pasta de valores/recursos-21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png)

    Observe que **valores/recursos-v21** é específico do Android 5.0 &ndash; versões mais antigas do Android não lerá arquivos nesta pasta.

-   Adicionar um `resources` nó **Styles** e definir um `style` nó com o nome do seu tema personalizado. Por exemplo, aqui está uma **Styles** arquivo que define *MyCustomTheme* (derivado interno `Theme.Material.Light` style de tema):

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

    [ ![Aparência de tema personalizado antes de personalizações](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png)

-   Adicionar personalizações de cor para **Styles** definindo as cores dos atributos de layout que você deseja alterar. Por exemplo, para alterar a cor da barra de aplicativos para `my_blue` e alterar a cor dos controles de interface do usuário para `my_purple`, adicione substituições de cor para **Styles** que se referem aos recursos de cor configurados no **Colors**:

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

Com essas alterações, um aplicativo que usa *MyCustomTheme* exibirá uma cor de barra de aplicativo em `my_blue` e controles de interface do usuário em `my_purple`, mas usar o `Theme.Material.Light` esquema de cores em todos os outros:

[ ![Aparência de tema personalizado depois de personalizações](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png)

Neste exemplo, *MyCustomTheme* usa cores de `Theme.Material.Light` do plano de fundo cor, barra de status e as cores de texto, mas ele altera a cor da barra de aplicativos para `my_blue` e define a cor do botão de opção `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Criando um estilo de exibição personalizada

Android 5.0 também possibilita que você definir o estilo de uma exibição individual. Depois de criar **Colors** e **Styles** (conforme descrito na seção anterior), você pode adicionar um estilo de exibição para **Styles**.
Para definir o estilo de um modo de exibição individual, use as seguintes etapas:

-   Editar **Resources/values-v21/styles.xml** e adicione um `style` nó com o nome do seu estilo de modo de exibição personalizado. Defina os atributos de cor personalizada para o modo de exibição dentro deste `style` nó. Por exemplo, para criar um personalizado [CardView](~/android/user-interface/controls/card-view.md) estilo que tem mais arredondado cantos e usa `my_blue` como a cor de plano de fundo de cartão, adicione um `style` nó **Styles** (dentro do `resources`nó) e configure o raio de canto e de cor em segundo plano:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   Em seu layout, defina o `style` atributo para esse modo de exibição corresponder ao nome de estilo personalizado que você escolheu na etapa anterior. Por exemplo:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

Captura de tela a seguir fornece um exemplo do padrão `CardView` (mostrado à esquerda) comparado a um `CardView` que tenha sido com o estilo personalizado `CardView.MyBlue` tema (mostrado no lado direito):

[ ![Exemplos de padrão CardView e CardView personalizado](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png)

Neste exemplo, personalizado `CardView` é exibido com a cor de plano de fundo `my_blue` e um raio de canto 18dp.

<a name="compatibility" />

## <a name="compatibility"></a>Compatibilidade

Para definir o estilo de seu aplicativo para que ele usa o Material de tema no Android 5.0, mas é revertida automaticamente para um estilo de descendente compatível em versões mais antigas de Android, use as seguintes etapas:

-   Definir um tema personalizado em **Resources/values-v21/styles.xml** que deriva de um estilo de tema do Material. Por exemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Definir um tema personalizado em **Resources/values/styles.xml** que deriva de um tema mais antigo, mas usa o mesmo nome de tema acima. Por exemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   Em **AndroidManifest.xml**, configure seu aplicativo com o nome do tema personalizado. 
    Por exemplo:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   Como alternativa, você pode definir o estilo de uma atividade específica usando seu tema personalizado:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Se o tema usa cores definidas em um **Colors** de arquivos, certifique-se de colocar esse arquivo em **recursos/valores** (em vez de **valores/recursos-v21**) para que ambas as versões do o tema personalizado pode acessar suas definições de cor.

Quando seu aplicativo é executado em um dispositivo Android 5.0, ele usará a definição de tema especificada em **Resources/values-v21/styles.xml**. Quando este aplicativo é executado em dispositivos Android mais antigos, ele automaticamente voltará para a definição de tema especificada em **Resources/values/styles.xml**.

Para obter mais informações sobre a compatibilidade de tema com versões mais antigas de Android, consulte [recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Resumo

Este artigo introduziu o novo estilo de interface de usuário de tema Material incluído no Android 5.0 (pirulito). Ele descrito os três tipos de Material tema internos que você pode usar para definir o estilo de seu aplicativo, ele explica como criar um tema personalizado para seu aplicativo de identidade visual e ele fornecido um exemplo de como tema uma exibição individual. Por fim, este artigo explicou como usar o tema de Material em seu aplicativo enquanto mantém a compatibilidade com versões anteriores do Android.



## <a name="related-links"></a>Links relacionados

- [ThemeSwitcher (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [Introdução ao pirulito](~/android/platform/lollipop.md)
- [CardView](~/android/user-interface/controls/card-view.md)
- [Recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md)
- [Visualização do desenvolvedor Android L](http://developer.android.com/preview/index.html)
- [Design de material](http://developer.android.com/preview/material/index.html)
- [Noções básicas de criação de material](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
- [Manter a compatibilidade de](http://developer.android.com/preview/material/compatibility.html)
