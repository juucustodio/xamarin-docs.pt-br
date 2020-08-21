---
title: Aplicativos de desgaste de pacotes
description: Este artigo explica como empacotar aplicativos Android desgaste.
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 83c4ba8cbdc360682a4e06a885be15dd20d0f249
ms.sourcegitcommit: f4b26c5b8cc84f79123951e80c15061eb859452d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720299"
---
# <a name="packaging-wear-apps"></a>Aplicativos de desgaste de pacotes

> [!WARNING]
> Os documentos e projetos de exemplo a seguir podem não ser mais mantidos.
> A partir do [Xamarin. Android 11,1][xa-11.1], o empacotamento automático de um aplicativo de desgaste do Android em um aplicativo portátil Android não é mais suportado. Em vez disso, é recomendável distribuir aplicativos Android desgaste como [aplicativos autônomos][standalone] .

Os aplicativos do Android desgaste 1,0 são empacotados com um aplicativo Android completo para distribuição em Google Play.

Os aplicativos do Android desgaste 2,0 podem ser enviados para Google Play como [aplicativos autônomos][standalone].

[xa-11.1]: https://docs.microsoft.com/xamarin/android/release-notes/11/11.1
[standalone]: https://developer.android.com/training/wearables/apps/standalone-apps

## <a name="automatic-packaging"></a>Empacotamento automático

A partir do Xamarin Android 5,0, seu aplicativo de desgaste é empacotado automaticamente como um recurso em seu aplicativo portátil quando você cria uma referência de projeto do projeto portátil para o projeto de desgaste. Você pode usar as seguintes etapas para criar essa associação: 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Se seu aplicativo de desgaste ainda não fizer parte de sua solução de portátil, clique com o botão direito do mouse no nó da solução e selecione **adicionar > Adicionar projeto existente...**.

2. Navegue até o arquivo **. csproj** do seu aplicativo de desgaste, selecione-o e clique em **abrir**. O projeto de aplicativo de desgaste agora deve estar visível em sua solução de portátil.

3. Clique com o botão direito do mouse no nó **referências** e selecione **Adicionar referência**.

4. Na caixa de diálogo **Gerenciador de referências** , habilite seu projeto de desgaste (clique para adicionar uma marca de seleção) e clique em **OK**.

5. Altere o nome do pacote para seu projeto de desgaste para que ele corresponda ao nome do pacote do projeto portátil (o nome do pacote pode ser alterado em **propriedades > manifesto do Android**).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Se seu aplicativo de desgaste ainda não fizer parte de sua solução de portátil, clique com o botão direito do mouse no nó da solução e selecione **adicionar > Adicionar projeto existente...**.

2. Navegue até o arquivo **. csproj** do seu aplicativo de desgaste, selecione-o e clique em **abrir**. O projeto de aplicativo de desgaste agora deve estar visível em sua solução de portátil.

3. Clique com o botão direito do mouse no nó do projeto portátil na solução e clique em **Editar referências..**..

4. Na caixa de diálogo **Editar referências** , habilite seu projeto de desgaste (clique para adicionar uma marca de seleção) e clique em **OK**.

5. Altere o nome do pacote para seu projeto de desgaste para que ele corresponda ao nome do pacote do projeto portátil (o nome do pacote pode ser alterado em **Opções do projeto > aplicativo Android**).

-----

Observe que você receberá um erro **XA5211** se o nome do pacote do aplicativo de desgaste não corresponder ao nome do pacote do aplicativo portátil. Por exemplo:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Para corrigir esse erro, altere o nome do pacote do aplicativo de desgaste para que ele corresponda ao nome do pacote do aplicativo portátil.

Quando você clica em **compilar > compilar tudo**, essa associação dispara o empacotamento automático do projeto de desgaste no projeto de telefone principal (Phone). O aplicativo de desgaste é criado e incluído automaticamente como um recurso no aplicativo portátil.

O assembly que o projeto de aplicativo de desgaste gera não é usado como uma referência de assembly no projeto de handheld (telefone). Em vez disso, o processo de compilação faz o seguinte:

- Verifica se os nomes de pacote correspondem. 

- Gera o XML e o adiciona ao projeto de portátil para associá-lo ao aplicativo de desgaste. Por exemplo: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- Adiciona o aplicativo de desgaste como um recurso **bruto** ao projeto portátil. 

## <a name="manual-packaging"></a>Empacotamento manual

Você pode gravar aplicativos de desgaste do Android no Xamarin. Android antes da versão 5,0, mas deve seguir estas instruções de empacotamento manual para distribuir o aplicativo: 

1. Verifique se o projeto portátil e os projetos de handheld (telefone) têm o mesmo número de versão e nome de pacote.

2. Crie manualmente o projeto portátil como uma compilação de **versão** .

3. Adicione a versão manualmente **. APK** da etapa (2) no diretório de **recursos/bruto** do projeto de mão (telefone).

4. Adicione manualmente um novo recurso de **recursos XML/XML/wearable_app_desc.xml** no projeto portátil que se refere a portátil **apk** da etapa (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Adicione manualmente um `<meta-data />` elemento ao elemento **AndroidManifest.xml** do projeto portátil `<application>` que se refere ao novo recurso XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Consulte também as [instruções do packging manual](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)do site para desenvolvedores do Android.
